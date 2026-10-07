---
title: Cassandra - Conceitos e Comandos Essenciais
markmap:
  colorFreezeLevel: 2
  maxWidth: 320
  initialExpandLevel: 2
---

# 👁️ Apache Cassandra

## 📖 Conceitos

- Banco **NoSQL wide-column** (orientado a colunas largas)
- Criado no **Facebook** (2008), hoje projeto da **Apache**
- Inspirado no **Dynamo** (Amazon) + **Bigtable** (Google)
- **Distribuído** e **sem mestre** (masterless / peer-to-peer)
- **Sem ponto único de falha**
- Escalabilidade **horizontal linear** (mais nós = mais capacidade)
- Otimizado para **escritas massivas**
- Linguagem: **CQL** (Cassandra Query Language), parecida com SQL
- Teorema CAP → **AP** (Disponibilidade + Tolerância a Partições)
  - Consistência **eventual** e **ajustável**
- Casos de uso
  - Séries temporais / IoT
  - Histórico de mensagens (chat)
  - Feeds e timelines
  - Logs, eventos e auditoria
  - Perfis e personalização
  - Detecção de fraudes
- Quem usa: Netflix, Apple, Uber, Instagram, Spotify

## 🏗️ Arquitetura

- **Nó (node)**: uma instância do Cassandra
- **Anel (ring)**: nós organizados em círculo de tokens
- **Data Center**: grupo lógico de nós (ex.: uma região)
- **Cluster**: conjunto de data centers
- **Particionador** (Murmur3)
  - Hash da partition key → **token** → nó responsável
- **Vnodes**: cada nó possui vários intervalos de tokens
- **Gossip**: nós trocam estado entre si a cada segundo
- **Snitch**: informa a topologia (rack / data center)
- **Coordenador**: nó que recebe a requisição e a repassa às réplicas
- Reparo de dados
  - **Hinted Handoff**: guarda escritas para nó que estava fora
  - **Read Repair**: corrige réplicas divergentes na leitura
  - `nodetool repair`: reparo manual completo (anti-entropia)

## ✍️ Caminho da Escrita e Leitura

- Escrita
  - 1. **Commit Log** (disco, sequencial → durabilidade)
  - 2. **Memtable** (memória RAM)
  - 3. Flush → **SSTable** (arquivo imutável em disco)
  - 4. **Compaction**: mescla SSTables e remove dados obsoletos
- Leitura
  - Memtable + SSTables
  - **Bloom Filter**: descarta SSTables que não têm a chave
  - Cache de chaves / linhas
- Estratégias de compactação
  - **STCS** (Size-Tiered): padrão, foco em escrita
  - **LCS** (Leveled): foco em leitura
  - **TWCS** (Time-Window): séries temporais com TTL
  - **UCS** (Unified): adaptativa (Cassandra 5)
- **Tombstone**: marcador de exclusão
  - ⚠️ Muitas exclusões degradam as leituras
  - Removidos após `gc_grace_seconds` (padrão 10 dias)

## 🔁 Replicação e Consistência

- **Replication Factor (RF)**: nº de cópias de cada dado (comum: 3)
- Estratégias
  - `SimpleStrategy`: um data center (testes)
  - `NetworkTopologyStrategy`: vários DCs (produção)
- Níveis de consistência
  - `ONE` / `TWO` / `THREE`
  - `QUORUM`: maioria das réplicas do cluster
  - `LOCAL_QUORUM`: maioria no DC local
  - `EACH_QUORUM`: maioria em cada DC
  - `ALL`: todas as réplicas
  - `ANY`: qualquer nó (só escrita)
- Regra de ouro
  - `R + W > RF` → consistência forte
  - Ex.: QUORUM + QUORUM com RF 3 → 2 + 2 > 3 ✅

## 🔌 cqlsh (Shell)

- `cqlsh host 9042 -u usuario -p senha`
- `DESCRIBE KEYSPACES`: lista keyspaces
- `DESCRIBE KEYSPACE nome`: estrutura completa
- `DESCRIBE TABLES` / `DESCRIBE TABLE t`
- `USE keyspace;`: seleciona o keyspace
- `CONSISTENCY QUORUM;`: nível da sessão
- `TRACING ON;`: mostra o caminho da consulta
- `PAGING 50;`: paginação dos resultados
- `EXPAND ON;`: exibição vertical
- `SOURCE 'script.cql';`: executa arquivo
- `COPY t TO 'dados.csv' WITH HEADER = true;`
- `COPY t FROM 'dados.csv' WITH HEADER = true;`
- `EXIT`

## 🗄️ Keyspaces

- Equivalente a um **schema / database**
- Define a **replicação**
- Criar
  - ```
    CREATE KEYSPACE loja
      WITH replication = {
        'class': 'NetworkTopologyStrategy',
        'dc1': 3
      };
    ```
- `ALTER KEYSPACE loja WITH replication = {...};`
- `DROP KEYSPACE loja;`

## 🔑 Chave Primária (Modelagem)

- `PRIMARY KEY ((partition key), clustering columns)`
- **Partition Key**
  - Define **em qual nó** o dado fica
  - Obrigatória em toda consulta (com `=` ou `IN`)
  - Pode ser composta: `((sensor_id, dia))`
- **Clustering Columns**
  - Definem a **ordem** dentro da partição
  - Permitem `>`, `<`, `>=`, `<=` e `ORDER BY`
  - Filtrar na ordem declarada (sem pular colunas)
- Exemplo
  - ```
    CREATE TABLE leituras (
      sensor_id text,
      dia date,
      ts timestamp,
      valor double,
      PRIMARY KEY ((sensor_id, dia), ts)
    ) WITH CLUSTERING ORDER BY (ts DESC);
    ```
- Regras de ouro
  - **Uma tabela por consulta** (query-driven)
  - **Desnormalizar** é normal (sem JOINs)
  - Distribuir dados por igual entre os nós
  - Partições pequenas (< ~100 MB) → usar **buckets**
  - Evitar **hot partitions**

## 🧱 Tipos de Dados

- Texto: `text` / `varchar`, `ascii`
- Números: `int`, `bigint`, `smallint`, `varint`, `float`, `double`, `decimal`
- Lógico: `boolean`
- Tempo: `timestamp`, `date`, `time`, `duration`
- Identificadores
  - `uuid`: `uuid()`
  - `timeuuid`: `now()` (único + ordenável por tempo)
- Rede: `inet`
- Binário: `blob`
- Especiais
  - `counter`: contador distribuído
  - `frozen<...>`: valor serializado como um bloco
  - `vector<float, n>`: busca vetorial (Cassandra 5)
- **UDT** (tipo definido pelo usuário)
  - ```
    CREATE TYPE endereco (
      rua text, cidade text, cep text
    );
    ```

## 📋 Tabelas

- `CREATE TABLE [IF NOT EXISTS] t (...);`
- `ALTER TABLE t ADD coluna tipo;`
- `ALTER TABLE t DROP coluna;`
- `ALTER TABLE t WITH default_time_to_live = 86400;`
- `TRUNCATE t;`: apaga todos os dados
- `DROP TABLE t;`
- ⚠️ Não é possível alterar a chave primária

## ✏️ CRUD (DML)

- Inserir
  - `INSERT INTO t (a, b) VALUES (1, 'x');`
  - ⚠️ É um **upsert**: sobrescreve se já existir
  - `INSERT ... IF NOT EXISTS;` (LWT)
  - `INSERT INTO t JSON '{"a": 1, "b": "x"}';`
- Ler
  - `SELECT * FROM t WHERE pk = 1;`
  - `SELECT ... WHERE pk = 1 AND ck > 10 LIMIT 20;`
  - `SELECT ... PER PARTITION LIMIT 1;`
  - `SELECT JSON * FROM t WHERE pk = 1;`
  - `SELECT COUNT(*) ...` ⚠️ caro sem partition key
- Atualizar
  - `UPDATE t SET b = 'y' WHERE pk = 1;`
  - Também é **upsert**
  - `UPDATE ... IF b = 'x';` (LWT)
- Excluir
  - `DELETE FROM t WHERE pk = 1;`
  - `DELETE b FROM t WHERE pk = 1;`: só a coluna
  - ⚠️ Gera **tombstones**
- `ALLOW FILTERING`
  - ⚠️ Varre partições inteiras → evitar em produção

## ⏱️ TTL e Timestamps

- `INSERT ... USING TTL 3600;`: expira em 1 h
- `UPDATE t USING TTL 60 SET ...`
- `default_time_to_live` na tabela
- `SELECT TTL(coluna) FROM t WHERE ...;`
- `SELECT WRITETIME(coluna) FROM t WHERE ...;`
- `USING TIMESTAMP 1700000000000000`
- Conflitos: **Last Write Wins** (maior timestamp vence)

## 🧺 Coleções

- `set<text>`: sem duplicatas, ordenado
  - `UPDATE t SET tags = tags + {'nosql'} WHERE ...;`
- `list<text>`: ordem de inserção, aceita duplicatas
  - `UPDATE t SET passos = passos + ['fim'] WHERE ...;`
- `map<text, int>`: chave → valor
  - `UPDATE t SET notas['bd'] = 10 WHERE ...;`
- ⚠️ Para dados pequenos (não crescer sem limite)

## 🔢 Contadores

- ```
  CREATE TABLE stats (
    video_id uuid PRIMARY KEY,
    views counter
  );
  UPDATE stats SET views = views + 1
  WHERE video_id = ...;
  ```
- Só `UPDATE` (sem `INSERT`)
- Todas as colunas não-chave devem ser counter
- Sem TTL e não idempotente

## 🔒 LWT e Batches

- **Lightweight Transactions** (Paxos)
  - `IF NOT EXISTS`, `IF EXISTS`, `IF coluna = valor`
  - Retorna `[applied] True/False`
  - Garante unicidade (ex.: username)
  - ⚠️ ~4x mais lento
- **BATCH**
  - ```
    BEGIN BATCH
      INSERT INTO usuarios_por_id ...;
      INSERT INTO usuarios_por_email ...;
    APPLY BATCH;
    ```
  - Atomicidade entre tabelas desnormalizadas
  - ⚠️ Não serve para ganhar desempenho
  - `UNLOGGED BATCH`: mesma partição, sem garantia

## 🔍 Índices e Views

- **Índice secundário**
  - `CREATE INDEX ON t (coluna);`
  - Bom para baixa cardinalidade dentro da partição
- **SAI** (Storage-Attached Index, Cassandra 5)
  - `CREATE INDEX ON t (coluna) USING 'sai';`
  - Mais eficiente, permite intervalos e vetores
- **Materialized View**
  - `CREATE MATERIALIZED VIEW ... AS SELECT ...`
  - ⚠️ Experimental, preferir tabelas manuais

## ⚙️ Administração (nodetool)

- Diagnóstico
  - `nodetool status`: estado dos nós (UN = Up/Normal)
  - `nodetool info`: detalhes do nó
  - `nodetool ring`: distribuição de tokens
  - `nodetool describecluster`: nome e versão do schema
  - `nodetool tablestats ks.t`: estatísticas da tabela
  - `nodetool tablehistograms ks t`: latências e tamanhos
  - `nodetool compactionstats`: compactações em andamento
- Manutenção
  - `nodetool repair`: sincroniza réplicas
  - `nodetool flush`: memtable → SSTable
  - `nodetool compact`: força compactação
  - `nodetool cleanup`: remove dados que não pertencem ao nó
  - `nodetool snapshot` / `clearsnapshot`: backup
- Ciclo de vida
  - `nodetool drain`: prepara para desligar
  - `nodetool decommission`: remove o nó do cluster
  - `nodetool removenode <id>`: remove nó morto

## 🧭 Qual recurso usar?

- Dados por intervalo de tempo → **Clustering column + DESC**
- Partição crescendo demais → **Bucket na partition key**
- Dados que expiram → **TTL + TWCS**
- ID único e ordenado → **timeuuid**
- Atributos multivalorados pequenos → **Coleções**
- Contagens distribuídas → **counter**
- Unicidade / compare-and-set → **LWT**
- Mesma info por outra consulta → **Nova tabela desnormalizada**
- Vários data centers → **NetworkTopologyStrategy + LOCAL_QUORUM**
