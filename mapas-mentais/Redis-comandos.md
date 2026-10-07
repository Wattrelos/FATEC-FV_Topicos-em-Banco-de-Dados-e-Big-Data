---
title: Redis - Comandos Essenciais
markmap:
  colorFreezeLevel: 2
  maxWidth: 320
  initialExpandLevel: 2
---

# 🟥 Redis

## 📖 Conceitos

- **RE**mote **DI**ctionary **S**erver
- Banco **NoSQL chave-valor** em memória (RAM)
- Altíssima performance (latência < 1 ms)
- Single-threaded para comandos (operações atômicas)
- Persistência opcional
  - **RDB**: snapshots periódicos
  - **AOF**: log de cada escrita (Append Only File)
- Casos de uso
  - Cache de consultas / sessões
  - Filas e mensageria
  - Rankings e placares
  - Contadores e *rate limiting*
  - Pub/Sub em tempo real

## 🔌 Conexão e CLI

- `redis-cli`: abre o cliente interativo
- `redis-cli -h host -p 6379 -a senha`
- `PING` → `PONG`
- `SELECT n`: troca de banco (0 a 15)
- `AUTH senha`: autentica na sessão
- `QUIT`: encerra a conexão

## 🔑 Strings (Básico)

- Escrita
  - `SET chave valor`: salva um valor textual
  - `SET chave valor EX 60`: salva com expiração
  - `SET chave valor NX`: só grava se **não** existir
  - `SETNX chave valor`: equivalente ao `NX`
  - `MSET k1 v1 k2 v2`: grava várias de uma vez
  - `APPEND chave texto`: concatena ao final
- Leitura
  - `GET chave`: retorna o valor
  - `MGET k1 k2`: retorna vários valores
  - `STRLEN chave`: tamanho do valor
  - `GETRANGE chave ini fim`: substring
- Numéricos (contadores)
  - `INCR chave`: +1
  - `DECR chave`: −1
  - `INCRBY chave n`: +n
  - `DECRBY chave n`: −n
  - `INCRBYFLOAT chave 1.5`: +decimal
- Exemplo
  - ```
    SET visitas 0
    INCR visitas      # 1
    INCRBY visitas 10 # 11
    ```

## 🗝️ Gerenciamento de Chaves

- `DEL chave`: remove (bloqueante)
- `UNLINK chave`: remove em segundo plano
- `EXISTS chave`: 1 se existe, 0 se não
- `TYPE chave`: tipo (string, list, hash...)
- `RENAME antiga nova`: renomeia
- `KEYS padrão`: busca por padrão (ex.: `user:*`)
  - ⚠️ Evitar em produção (bloqueia o servidor)
- `SCAN 0 MATCH user:* COUNT 100`: busca iterativa e segura
- Boas práticas de nomes
  - Use `:` como separador
  - Ex.: `usuario:1001:perfil`

## ⏱️ Tempo de Vida (TTL)

- `EXPIRE chave segundos`: define expiração
- `PEXPIRE chave ms`: expiração em milissegundos
- `EXPIREAT chave timestamp`: expira em data Unix
- `TTL chave`: tempo restante (s)
  - `-1` → sem expiração
  - `-2` → chave não existe
- `PTTL chave`: tempo restante (ms)
- `PERSIST chave`: remove a expiração
- Exemplo: sessão de login
  - ```
    SET sessao:abc123 "user:42" EX 1800
    TTL sessao:abc123  # 1800
    ```

## 📜 Listas (Filas e Pilhas)

- Ordenadas por inserção, permitem duplicatas
- Inserção
  - `LPUSH lista valor`: no início (esquerda)
  - `RPUSH lista valor`: no final (direita)
  - `LINSERT lista BEFORE|AFTER pivo valor`
- Remoção
  - `LPOP lista`: remove o primeiro
  - `RPOP lista`: remove o último
  - `LREM lista qtd valor`: remove ocorrências
  - `LTRIM lista ini fim`: mantém só o intervalo
- Leitura
  - `LRANGE lista 0 -1`: todos os elementos
  - `LINDEX lista i`: elemento na posição
  - `LLEN lista`: tamanho
- Bloqueantes (filas de trabalho)
  - `BLPOP lista timeout`
  - `BRPOP lista timeout`
- Padrões
  - **Fila (FIFO)**: `RPUSH` + `LPOP`
  - **Pilha (LIFO)**: `LPUSH` + `LPOP`
  - **Últimos N itens**: `LPUSH` + `LTRIM 0 N-1`

## 🗂️ Hashes (Objetos / Dicionários)

- Ideal para representar entidades
- `HSET hash campo valor [campo valor ...]`
- `HGET hash campo`: valor de um campo
- `HMGET hash c1 c2`: vários campos
- `HGETALL hash`: todos os campos e valores
- `HKEYS hash` / `HVALS hash`: só campos / só valores
- `HEXISTS hash campo`: verifica campo
- `HLEN hash`: quantidade de campos
- `HINCRBY hash campo n`: incrementa campo
- `HDEL hash campo`: remove campo
- Exemplo
  - ```
    HSET usuario:1 nome "Ana" idade 22
    HINCRBY usuario:1 idade 1
    HGETALL usuario:1
    ```

## 🎭 Conjuntos (Sets)

- Sem ordem e **sem duplicatas**
- Básico
  - `SADD conjunto membro`: adiciona
  - `SREM conjunto membro`: remove
  - `SMEMBERS conjunto`: lista todos
  - `SISMEMBER conjunto membro`: pertence?
  - `SCARD conjunto`: quantidade
  - `SRANDMEMBER conjunto [n]`: aleatório
  - `SPOP conjunto`: remove aleatório
- Teoria dos conjuntos
  - `SUNION A B`: união (A ∪ B)
  - `SINTER A B`: interseção (A ∩ B)
  - `SDIFF A B`: diferença (A − B)
- Usos
  - Tags, seguidores, visitantes únicos
  - "Amigos em comum" com `SINTER`

## 🏆 Conjuntos Ordenados (Sorted Sets)

- Membros únicos com **score** numérico
- `ZADD ranking score membro`
- `ZINCRBY ranking n membro`: soma ao score
- `ZSCORE ranking membro`: score do membro
- `ZRANK` / `ZREVRANK`: posição (cresc./decresc.)
- `ZRANGE ranking 0 -1 WITHSCORES`: ordem crescente
- `ZREVRANGE ranking 0 9 WITHSCORES`: Top 10
- `ZRANGEBYSCORE ranking min max`
- `ZREM ranking membro`
- `ZCARD ranking`: quantidade
- Exemplo: placar de jogo
  - ```
    ZADD placar 150 "ana" 90 "bruno"
    ZINCRBY placar 20 "bruno"
    ZREVRANGE placar 0 2 WITHSCORES
    ```

## 🌊 Outras Estruturas

- **Streams** (log de eventos)
  - `XADD stream * campo valor`
  - `XRANGE stream - +`
  - `XREAD COUNT 10 STREAMS stream 0`
  - `XGROUP` / `XREADGROUP`: grupos de consumidores
- **Bitmaps**
  - `SETBIT chave offset 1`
  - `GETBIT chave offset`
  - `BITCOUNT chave`: ex.: usuários ativos no dia
- **HyperLogLog** (contagem aproximada)
  - `PFADD chave elemento`
  - `PFCOUNT chave`
- **Geoespacial**
  - `GEOADD locais long lat nome`
  - `GEODIST locais a b km`
  - `GEOSEARCH locais FROMMEMBER a BYRADIUS 5 km`

## 📡 Pub/Sub (Mensageria)

- `SUBSCRIBE canal`: escuta um canal
- `PSUBSCRIBE noticias:*`: escuta por padrão
- `PUBLISH canal mensagem`: envia mensagem
- `UNSUBSCRIBE canal`
- ⚠️ Mensagens não são armazenadas (fire-and-forget)

## 🔒 Transações

- `MULTI`: inicia a transação
- `EXEC`: executa os comandos enfileirados
- `DISCARD`: cancela a transação
- `WATCH chave`: bloqueio otimista
  - Se a chave mudar, o `EXEC` falha
- Exemplo
  - ```
    MULTI
    DECRBY conta:A 100
    INCRBY conta:B 100
    EXEC
    ```

## 📝 Scripts Lua

- `EVAL "script" numkeys chave [args]`
- Execução atômica no servidor
- Ex.: `EVAL "return redis.call('GET', KEYS[1])" 1 nome`

## ⚙️ Administração do Sistema

- Diagnóstico
  - `PING`: teste de conexão
  - `INFO [seção]`: estatísticas do servidor
  - `DBSIZE`: total de chaves no banco atual
  - `MONITOR`: mostra comandos em tempo real
  - `SLOWLOG GET 10`: comandos lentos
  - `CLIENT LIST`: conexões ativas
- Configuração
  - `CONFIG GET parametro`
  - `CONFIG SET maxmemory 256mb`
  - `CONFIG SET maxmemory-policy allkeys-lru`
- Persistência
  - `SAVE`: snapshot síncrono (bloqueia)
  - `BGSAVE`: snapshot em segundo plano
  - `BGREWRITEAOF`: reescreve o AOF
  - `LASTSAVE`: data do último snapshot
- ⚠️ Limpeza (cuidado!)
  - `FLUSHDB`: apaga o banco selecionado
  - `FLUSHALL`: apaga **todos** os bancos
- `SHUTDOWN`: desliga o servidor

## 🧭 Qual estrutura usar?

- Valor simples / contador → **String**
- Fila / histórico → **List**
- Objeto com campos → **Hash**
- Itens únicos / tags → **Set**
- Ranking / prioridade → **Sorted Set**
- Eventos com consumidores → **Stream**
- Notificações em tempo real → **Pub/Sub**
