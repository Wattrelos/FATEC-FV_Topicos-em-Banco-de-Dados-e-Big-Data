# Redis Applications

> O Redis é extremamente versátil por trabalhar na memória RAM, o que o torna ideal para cenários que exigem velocidade de milissegundos e alta taxa de leitura/escrita.

**Por que o Redis é rápido?**
* Dados em **memória RAM** (acesso em microssegundos, contra milissegundos do disco).
* Execução de comandos em **thread única** → cada comando é **atômico**, sem condições de corrida entre comandos individuais.
* Estruturas de dados nativas (Strings, Hashes, Lists, Sets, Sorted Sets, Streams…), que evitam ter que "montar" a lógica na aplicação.
* **TTL (Time To Live)** nativo: chaves podem expirar sozinhas (`EXPIRE`, `SET ... EX`).

> ⚠️ Como os dados ficam em RAM, a memória é limitada e cara. Para não perder dados em uma queda, o Redis oferece persistência via **RDB** (snapshots periódicos) e/ou **AOF** (log de cada escrita).

---

## 1. Sistema de Cache (O mais comum)
Para evitar consultas repetidas e lentas ao banco de dados principal (como PostgreSQL ou MongoDB), o Redis armazena os dados mais acessados.

* Exemplo: Guardar o catálogo de produtos de um e-commerce ou o perfil de um usuário. Na próxima requisição, a aplicação busca direto no Redis em vez de fazer um SELECT pesado no banco relacional.

**Padrão Cache-Aside (Lazy Loading):**
1. A aplicação procura a chave no Redis.
2. **Cache hit** → devolve o dado direto da memória.
3. **Cache miss** → consulta o banco, grava o resultado no Redis com TTL e devolve.

```redis
GET produto:42                                   # (nil) → cache miss
# ... aplicação faz o SELECT no PostgreSQL ...
SET produto:42 '{"nome":"Notebook","preco":3500}' EX 300   # guarda por 5 min
GET produto:42                                   # cache hit nas próximas requisições
DEL produto:42                                   # invalida quando o produto for alterado
```

**Cuidados:**
* **Invalidação**: ao atualizar o dado no banco, apague (`DEL`) ou atualize a chave no cache, senão o usuário verá dados desatualizados.
* **Política de despejo (eviction)**: quando a memória enche, o Redis remove chaves conforme `maxmemory-policy` (ex.: `allkeys-lru` remove as menos usadas recentemente).
* **Cache stampede**: se uma chave muito acessada expira, milhares de requisições podem bater no banco ao mesmo tempo. Solução: lock com `SET NX` ou TTLs com pequena variação aleatória.


## 2. Limitador de Taxa (Rate Limiting)
Protege APIs e servidores contra abusos, ataques DDoS ou sobrecarga, limitando quantas requisições um usuário (ou IP) pode fazer em um intervalo de tempo.


* Exemplo: Garantir que um usuário só possa tentar fazer login 5 vezes por minuto. O Redis usa o comando INCR combinado com EXPIRE para controlar esse contador rapidamente.

**Janela fixa (Fixed Window):**
```redis
INCR login:tentativas:192.168.0.10     # → 1 (cria a chave se não existir)
EXPIRE login:tentativas:192.168.0.10 60 NX   # define TTL só na primeira vez
# ... se o valor retornado pelo INCR for > 5 → responder HTTP 429 (Too Many Requests)
TTL login:tentativas:192.168.0.10      # quantos segundos faltam para liberar
```

**Janela deslizante (Sliding Window) com Sorted Set** mais precisa, pois não "zera" de uma vez:
```redis
ZADD rl:user:7 1700000060123 "req-uuid"          # score = timestamp em ms
ZREMRANGEBYSCORE rl:user:7 0 1700000000123       # remove requisições com mais de 60s
ZCARD rl:user:7                                  # conta requisições na janela
```

> 💡 Como o `INCR` é atômico, mesmo com vários servidores da API acessando o mesmo Redis o contador nunca "perde" incrementos.


## 3. Filas de Mensageria e Background Jobs (Message Brokers)
Gerenciamento de tarefas em segundo plano para que o usuário não precise esperar a execução terminar para ver a resposta na tela.


* Exemplo: Quando você se cadastra em um site, o disparo do e-mail de boas-vindas vai para uma fila do Redis (usando estruturas como Lists ou Streams). Um serviço em segundo plano (worker) consome essa fila e envia o e-mail, mantendo o site rápido para o usuário.

**Fila simples com Lists (FIFO):**
```redis
LPUSH fila:emails '{"para":"ana@email.com","tipo":"boas-vindas"}'   # produtor (API)
BRPOP fila:emails 0      # worker: bloqueia até chegar uma tarefa e a retira da fila
```

**Fila robusta com Streams (grupos de consumidores + confirmação):**
```redis
XADD eventos:cadastro * usuario_id 101 email ana@email.com    # produtor
XGROUP CREATE eventos:cadastro grupo-email $ MKSTREAM          # cria grupo de workers
XREADGROUP GROUP grupo-email worker-1 COUNT 1 BLOCK 0 STREAMS eventos:cadastro >
XACK eventos:cadastro grupo-email 1700000000000-0             # confirma o processamento
XPENDING eventos:cadastro grupo-email                          # mensagens não confirmadas
```

| | Lists | Streams |
|---|---|---|
| Complexidade | Simples | Mais completa |
| Se o worker cair no meio | Mensagem é perdida | Fica pendente e pode ser reprocessada |
| Vários consumidores | Competem pela mensagem | Grupos de consumo + histórico |
| Uso típico | Jobs simples | Eventos, logs, pipelines |

> Bibliotecas populares que usam o Redis como fila: **Sidekiq** (Ruby), **Celery/RQ** (Python), **BullMQ** (Node.js).


## 4. Placar de Líderes em Tempo Real (Leaderboards)
Sistemas de pontuação que precisam ser atualizados instantaneamente para milhões de usuários simultâneos.


* Exemplo: Classificação de jogadores em um game online ou tópicos mais comentados (Trending Topics). O Redis possui uma estrutura nativa perfeita para isso chamada Sorted Sets (ZADD/ZRANK), que mantém os dados ordenados automaticamente conforme novos pontos são inseridos.

```redis
ZADD ranking:jogo 1500 "ana" 1200 "bruno" 1800 "carla"
ZINCRBY ranking:jogo 400 "bruno"            # bruno ganhou 400 pontos → 1600
ZREVRANGE ranking:jogo 0 2 WITHSCORES       # Top 3 (maior pontuação primeiro)
# 1) "carla" 1800   2) "bruno" 1600   3) "ana" 1500
ZREVRANK ranking:jogo "ana"                 # posição da ana (0 = primeiro lugar) → 2
ZSCORE ranking:jogo "ana"                   # pontuação da ana → 1500
```

> 💡 As operações em Sorted Sets têm complexidade **O(log N)** em um banco relacional, recalcular a posição de um jogador exigiria um `ORDER BY` + `COUNT` sobre a tabela inteira a cada consulta.


## 5. Aplicações de Chat e Notificações (Pub/Sub)
Sistemas que exigem comunicação em tempo real entre servidores e clientes.


* Exemplo: O Redis possui o mecanismo de Publish/Subscribe. Quando o Usuário A envia uma mensagem no chat, o servidor publica essa mensagem em um "canal" do Redis, e todos os servidores conectados que cuidam dos outros usuários recebem o alerta instantaneamente para renderizar a mensagem na tela.

```redis
# Terminal 1 (servidor de WebSocket B)
SUBSCRIBE chat:sala:42
PSUBSCRIBE chat:sala:*          # assina vários canais por padrão

# Terminal 2 (servidor de WebSocket A)
PUBLISH chat:sala:42 '{"de":"UsuarioA","msg":"Olá!"}'   # retorna nº de assinantes que receberam
```

> ⚠️ O Pub/Sub é **"fire and forget"**: se nenhum assinante estiver conectado no momento, a mensagem se perde. Para histórico de mensagens (ex.: "carregar mensagens antigas"), combine com **Streams** ou salve no banco principal.

---

## 6. Idempotência
**Idempotência** é a garantia de que executar a mesma operação várias vezes produz o **mesmo resultado** que executá-la uma única vez.

* Exemplo: O usuário clica duas vezes no botão "Pagar", ou a rede falha e o app reenvia a requisição. Sem proteção, o cliente seria **cobrado duas vezes**.

**Como funciona:** o cliente envia um identificador único (`Idempotency-Key`) em cada operação. O servidor tenta "reservar" essa chave no Redis de forma atômica:

```redis
SET idem:pagamento:7f3a-uuid "PROCESSANDO" NX EX 86400
# OK    → primeira vez: processa o pagamento
# (nil) → chave já existe: requisição duplicada, NÃO processa de novo

# Após concluir, guarda a resposta para devolver a requisições repetidas:
SET idem:pagamento:7f3a-uuid '{"status":"aprovado","id":"pg_991"}' XX KEEPTTL
GET idem:pagamento:7f3a-uuid
```

* `NX` → só grava se a chave **não existir** (equivalente ao antigo `SETNX`).
* `EX` → a chave expira (ex.: 24h), evitando acúmulo infinito de chaves.
* Usado também para evitar **processamento duplicado de mensagens** em filas e webhooks (ex.: o gateway de pagamento reenvia o mesmo evento).

> 💡 A mesma técnica (`SET NX EX`) é a base dos **locks distribuídos**, usados para garantir que apenas um servidor execute uma tarefa por vez (ex.: um cron job em um cluster).


## 7. Tokens de Sessão
Após o login, o servidor gera um **token aleatório** (opaco) e o entrega ao cliente (cookie ou header `Authorization`). O Redis mapeia esse token para o usuário.

```redis
SET sessao:token:a9f8e7d6c5 "usuario:101" EX 1800   # token válido por 30 min
GET sessao:token:a9f8e7d6c5                         # a cada requisição: quem é o usuário?
DEL sessao:token:a9f8e7d6c5                         # logout → token invalidado na hora
```

**Blacklist de JWT:** um JWT é válido até expirar e não pode ser "desligado". Para revogá-lo no logout, guarda-se seu ID (`jti`) no Redis até a data de expiração original:
```redis
SET jwt:revogado:jti-123 1 EXAT 1700003600   # expira junto com o próprio JWT
EXISTS jwt:revogado:jti-123                  # 1 → token revogado, negar acesso
```

**Outros tokens temporários:** códigos de verificação 2FA, links de recuperação de senha e confirmação de e-mail todos se beneficiam do TTL automático.


## 8. Controle de Sessão
Regras de **segurança e políticas** sobre as sessões de cada usuário.

* **Limitar sessões simultâneas** (ex.: streaming que permite no máximo 3 telas):
```redis
SADD usuario:101:sessoes "a9f8e7d6c5"
SCARD usuario:101:sessoes          # se > 3 → bloquear novo login ou derrubar a mais antiga
```
* **"Sair de todos os dispositivos":**
```redis
SMEMBERS usuario:101:sessoes       # lista todos os tokens do usuário
# → DEL em cada sessao:token:<token>, e depois:
DEL usuario:101:sessoes
```
* **Expiração por inatividade (sliding expiration):** a cada requisição, renova-se o tempo de vida da sessão.
```redis
EXPIRE sessao:token:a9f8e7d6c5 1800     # renova mais 30 min a cada acesso
```


## 9. Gerenciamento de Sessão
Armazenamento dos **dados da sessão** (quem é o usuário, permissões, carrinho, preferências) de forma centralizada.

* Exemplo: Uma aplicação roda em **vários servidores atrás de um load balancer**. Se a sessão ficasse na memória de cada servidor, o usuário seria "deslogado" ao cair em outro servidor. Com o Redis, todos compartilham a mesma sessão → servidores ficam **stateless** e podem escalar horizontalmente.

```redis
HSET sessao:a9f8e7d6c5 usuario_id 101 nome "Ana" perfil "admin" carrinho_itens 3
EXPIRE sessao:a9f8e7d6c5 1800
HGET sessao:a9f8e7d6c5 perfil              # lê apenas um campo
HINCRBY sessao:a9f8e7d6c5 carrinho_itens 1 # atualiza um campo sem reescrever tudo
HGETALL sessao:a9f8e7d6c5                  # lê a sessão inteira
```

> 💡 Hashes são preferíveis a uma String com JSON porque permitem ler/alterar **campos individuais**. Frameworks como Spring Session, Express (`connect-redis`) e Django já oferecem integração pronta.

### Sessões: resumo das diferenças
| Conceito | Pergunta que responde | Estrutura típica |
|---|---|---|
| Token de sessão | "Este token é válido? De quem é?" | String + TTL |
| Controle de sessão | "Quantas sessões? Pode continuar logado?" | Set + EXPIRE |
| Gerenciamento de sessão | "Quais dados pertencem a esta sessão?" | Hash + TTL |

---

## 10. Outras aplicações (bônus)
| Aplicação | Estrutura | Comandos |
|---|---|---|
| Contagem de visitantes únicos (aproximada, ~12 KB para milhões) | HyperLogLog | `PFADD`, `PFCOUNT` |
| "Lojas perto de mim" / entregadores próximos | Geospatial | `GEOADD`, `GEOSEARCH` |
| Presença diária / usuários ativos por dia | Bitmaps | `SETBIT`, `BITCOUNT` |
| Tags, seguidores em comum, likes únicos | Sets | `SADD`, `SINTER`, `SISMEMBER` |
| Contadores de visualização / curtidas | Strings | `INCR`, `INCRBY` |


------------------------------
## 🛠️ Resumo de Como as Estruturas se Encaixam

### Cache
* **Strings (JSON) ou Hashes + TTL**: armazenam o resultado de consultas caras com expiração automática.

### Rate Limiting
* **Strings com INCR + EXPIRE**: contador atômico por janela de tempo.
* **Sorted Sets**: janela deslizante mais precisa.

### Idempotência e Sessões
Para garantir que uma operação não seja executada duas vezes (mesmo que o cliente tente enviar o mesmo comando várias vezes), usamos:

* **SETNX (Set if Not eXists)**: Garante que uma ação seja executada apenas uma vez. Hoje recomenda-se `SET chave valor NX EX segundos`, que define valor e expiração em um único comando atômico.
* **Hashes com Expire**: Essencial para o gerenciamento de sessões de usuário, armazenando dados temporários (como tokens de autenticação) que expiram automaticamente.
* **Sets**: controle de sessões ativas por usuário.

### Filas e Mensageria
Para processamento assíncrono de tarefas, utilizamos:

* **Lists**: Para filas simples (FIFO) ou pilhas (LIFO).
* **Streams**: Para sistemas mais robustos de mensagens e logs de eventos.

### Placar de Líderes
Para ranqueamentos em tempo real:

* **Sorted Sets (ZSet)**: Permite adicionar membros com pontuações e recuperar o ranking instantaneamente.

### Chat e Notificações
Para comunicação em tempo real:

* **Pub/Sub**: Sistema de mensagens Publish/Subscribe.
![Redis](/img/Redis-uses-example.png)
