<div align="center">

# Mapas Mentais - Tópicos em Banco de Dados e Big Data

[![Deploy Markmaps](https://github.com/Wattrelos/FATEC-FV_Topicos-em-Banco-de-Dados-e-Big-Data/actions/workflows/deploy-mindmaps.yml/badge.svg?branch=mindmaps)](https://github.com/Wattrelos/FATEC-FV_Topicos-em-Banco-de-Dados-e-Big-Data/actions/workflows/deploy-mindmaps.yml)
[![GitHub Pages](https://img.shields.io/badge/GitHub%20Pages-online-222222?logo=githubpages&logoColor=white)](https://wattrelos.github.io/FATEC-FV_Topicos-em-Banco-de-Dados-e-Big-Data/)
[![Último commit](https://img.shields.io/github/last-commit/Wattrelos/FATEC-FV_Topicos-em-Banco-de-Dados-e-Big-Data?label=%C3%BAltimo%20commit)](https://github.com/Wattrelos/FATEC-FV_Topicos-em-Banco-de-Dados-e-Big-Data/commits)
[![Tamanho do repositório](https://img.shields.io/github/repo-size/Wattrelos/FATEC-FV_Topicos-em-Banco-de-Dados-e-Big-Data?label=tamanho)](https://github.com/Wattrelos/FATEC-FV_Topicos-em-Banco-de-Dados-e-Big-Data)
[![Stars](https://img.shields.io/github/stars/Wattrelos/FATEC-FV_Topicos-em-Banco-de-Dados-e-Big-Data?style=flat&logo=github)](https://github.com/Wattrelos/FATEC-FV_Topicos-em-Banco-de-Dados-e-Big-Data/stargazers)

![FATEC](https://img.shields.io/badge/FATEC-Ferraz%20de%20Vasconcelos-B20000)
![Markdown](https://img.shields.io/badge/Markdown-000000?logo=markdown&logoColor=white)
![Markmap](https://img.shields.io/badge/Markmap-mapas%20mentais-4CAF50)
![Mermaid](https://img.shields.io/badge/Mermaid-diagramas-FF3670?logo=mermaid&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-CI%2FCD-2088FF?logo=githubactions&logoColor=white)

![MongoDB](https://img.shields.io/badge/MongoDB-47A248?logo=mongodb&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-DC382D?logo=redis&logoColor=white)
![Apache Cassandra](https://img.shields.io/badge/Apache%20Cassandra-1287B1?logo=apachecassandra&logoColor=white)
![NoSQL](https://img.shields.io/badge/NoSQL-Big%20Data-6A1B9A)

</div>

> O mapa mental foi elaborado, estruturado e consolidado no arquivo [Mapas mentais](https://wattrelos.github.io/FATEC-FV_Topicos-em-Banco-de-Dados-e-Big-Data/) no formato **Markmap**.
> 📖 Confira também o tutorial completo: [Como gerar e publicar Mapas Mentais com IA e GitHub Actions](/gerando-mapas-mentais.md).

---

## 🗺️ Mapas Mentais Disponíveis

| Mapa | Conteúdo | Versão online |
|---|---|---|
| 🧭 [topicos.md](/mapas-mentais/topicos.md) | Visão geral da disciplina (módulos 1 a 4) | [Abrir](https://wattrelos.github.io/FATEC-FV_Topicos-em-Banco-de-Dados-e-Big-Data/topicos.html) |
| 🟥 [Redis-comandos.md](/mapas-mentais/Redis-comandos.md) | Conceitos, estruturas de dados e comandos do Redis | [Abrir](https://wattrelos.github.io/FATEC-FV_Topicos-em-Banco-de-Dados-e-Big-Data/Redis-comandos.html) |
| 👁️ [cassandra.md](/mapas-mentais/cassandra.md) | Arquitetura, modelagem, CQL e `nodetool` do Cassandra | [Abrir](https://wattrelos.github.io/FATEC-FV_Topicos-em-Banco-de-Dados-e-Big-Data/cassandra.html) |
| 📚 [revisao.001.md](/mapas-mentais/revisao.001.md) | Revisão de conteúdo e questões de prova | [Abrir](https://wattrelos.github.io/FATEC-FV_Topicos-em-Banco-de-Dados-e-Big-Data/revisao.001.html) |

## 📄 Documentos de Aplicações Práticas

| Documento | Conteúdo |
|---|---|
| 🟥 [Redis-applications.md](/Redis-applications.md) | Cache, rate limiting, filas, rankings, Pub/Sub, idempotência e sessões, com comandos de exemplo |
| 👁️ [Cassandra-applications.md](/Cassandra-applications.md) | Séries temporais, chats, feeds, logs, fraude, contadores, LWT e multi-região, com exemplos em CQL |

---

### 🌟 O que foi organizado e integrado:

1. **Configuração Markmap Otimizada (Frontmatter)**:
   - `initialExpandLevel: 2`: O mapa mental abre inicialmente focado nos módulos principais, permitindo uma navegação interativa sem poluição visual.
   - `colorFreezeLevel: 2`: Mantém uma paleta de cores consistente por módulo.
   - `maxWidth: 380`: Limita a largura dos nós para manter o diagrama compacto e legível.

2. **Módulo 1: Evolução Histórica e Big Data**:
   - **Armazenamento Físico e Pré-Computacional**: Epopeia de Gilgamesh, Papiro de Ani, Biblioteca de Alexandria, Pergaminho, Gutenberg e problemas do meio físico (perda, durabilidade, espaço).
   - **Primórdios da Computação (1900–1960)**: Cartões perfurados (Hollerith/IBM), fitas magnéticas (UNIVAC I) e o primeiro HD (IBM RAMAC 305 por Alan Shugart, com acesso aleatório).
   - **Primeiros Modelos (1960)**: Modelo Hierárquico (IBM IMS) e Modelo em Rede (CODASYL).
   - **Revolução Relacional (1970)**: Edgar F. Codd, esquema predefinido, ACID, *scale-up*, IBM System R, INGRES, Oracle v2 e dBASE.
   - **Consolidação e SQL (1980–1990)**: SGBDR corporativo, padronização ANSI/ISO do SQL, e motores relacionais (SQL Server, Db2, MySQL, PostgreSQL).
   - **Ecossistema Analítico (1990)**: Data Warehouse, distinção OLTP vs. OLAP, pipeline ETL, BI e Data Mining.
   - **Transição para a Nuvem e NoSQL**: Carlo Strozzi (1998), DBaaS (AWS RDS, DynamoDB, GCP Datastore, Azure) e Apache Hadoop (Big Data).

3. **Módulo 2: Bancos de Dados Não Relacionais (NoSQL) e Teoremas**:
   - **3 Pilares NoSQL**: Esquema Flexível/Dinâmico, Escalabilidade Horizontal (*Scale-Out*) e Alta Disponibilidade.
   - **4 Modelos NoSQL com Casos de Uso e Exemplos**:
     - *Documentos*: MongoDB, CouchDB, Azure Cosmos DB, Google Cloud Firestore.
     - *Chave-Valor*: Redis, Amazon DynamoDB, Couchbase, Oracle NoSQL.
     - *Família de Colunas*: Apache HBase, Apache Cassandra, BigTable.
     - *Grafos*: Neo4j, OrientDB, ArangoDB.
   - **Teoremas e Propriedades**: Propriedades ACID vs. Modelo BASE (consistência eventual) e Teorema CAP (Eric Brewer, 2000) com os perfis **CA**, **CP** (MongoDB, HBase, Redis) e **AP** (Cassandra, CouchDB, DynamoDB).
   - **Persistência Poliglota**: Combinação em microserviços e bancos multi-modelo.

4. **Módulo 3: Arquitetura MongoDB e Formato BSON**:
   - **Mapeamento Conceitual SQL vs. MongoDB**: Database, Coleção, Documento BSON, Campo, `_id` e subdocumentos embutidos.
   - **JSON vs. BSON**: Comparativo de formato textual vs. binário serializado e tipos de dados estendidos (`ObjectId`, `Date`, `BinData`, `Int32`, `Int64`, `Decimal128`).
   - **Anatomia do ObjectId (`_id`) de 12 Bytes (24 hex)**: 4B Timestamp + 5B Machine ID + 3B PID + 4B Contador incremental.
   - **Terminal MONGOSH**: Comandos essenciais de contexto (`show dbs`, `use <db>`, `show collections`, `db`).

5. **Módulo 4: Operações CRUD, Modelagem e Validação**:
   - **CRUD no MONGOSH**: `insertOne`, `insertMany`, `find`, `findOne`, `updateOne`, `updateMany`, `deleteOne`, `deleteMany` e operadores de modificação (`$set`, `$unset`, `$inc`, `$push`, `$pull`).
   - **Operadores de Consulta**: Comparação (`$gt`, `$gte`, `$lt`, `$lte`, `$eq`, `$ne`, `$in`, `$nin`) e Lógicos (`$and`, `$or`, `$not`, `$nor`).
   - **Modelagem de Dados**: Documentos Embutidos (desnormalização, limite de 16MB) vs. Referências (normalização com `$lookup`).
   - **Validação de Esquema e Ferramentas**: Document Validator (`$jsonSchema`, `required`, `bsonType`), MongoDB Compass e MongoDB Atlas.

6. **Módulo 5: Redis (Chave-Valor em Memória)**:
   - **Conceitos**: armazenamento em RAM, execução single-threaded (comandos atômicos), TTL e persistência **RDB** / **AOF**.
   - **Estruturas de Dados**: Strings, Hashes, Lists, Sets, Sorted Sets, Streams, Bitmaps, HyperLogLog e Geoespacial.
   - **Recursos Avançados**: Pub/Sub, transações (`MULTI`/`EXEC`/`WATCH`), scripts Lua e administração (`INFO`, `CONFIG`, `maxmemory-policy`).
   - **Aplicações**: cache-aside, rate limiting, filas de background jobs, leaderboards, chat, idempotência (`SET NX EX`) e gerenciamento de sessões.

7. **Módulo 6: Apache Cassandra (Wide-Column Distribuído)**:
   - **Arquitetura**: anel *masterless*, particionador Murmur3, vnodes, Gossip, Snitch, Hinted Handoff e Read Repair.
   - **Caminho da Escrita**: Commit Log, Memtable, SSTables, compactação (STCS, LCS, TWCS, UCS) e tombstones.
   - **Replicação e Consistência**: Replication Factor, `NetworkTopologyStrategy` e níveis ajustáveis (`ONE`, `QUORUM`, `LOCAL_QUORUM`, `ALL`) com a regra `R + W > RF`.
   - **Modelagem e CQL**: partition key, clustering columns, buckets, desnormalização, TTL, coleções, counters, LWT, batches, índices SAI e `nodetool`.
   - **Aplicações**: séries temporais/IoT, histórico de chat, feeds sociais, auditoria, antifraude, rastreamento e alta disponibilidade multi-região.

---

## 📁 Estrutura do Repositório

```
.
├── .github/workflows/
│   └── deploy-mindmaps.yml      # Compila os mapas e publica no GitHub Pages
├── mapas-mentais/               # Mapas mentais no formato Markmap
│   ├── topicos.md
│   ├── Redis-comandos.md
│   ├── cassandra.md
│   └── revisao.001.md
├── diagrams/                    # Fontes dos diagramas Mermaid (.mmd)
├── img/                         # Diagramas renderizados (PNG)
├── Redis-applications.md        # Aplicações práticas do Redis
├── Cassandra-applications.md    # Aplicações práticas do Cassandra
└── gerando-mapas-mentais.md     # Tutorial: NotebookLM + Markmap + GitHub Pages
```

## 🛠️ Como Usar Localmente

```bash
# Visualizar um mapa mental no navegador
npx -y markmap-cli mapas-mentais/cassandra.md

# Renderizar um diagrama Mermaid em PNG
npx -y @mermaid-js/mermaid-cli -i diagrams/cassandra-uses.mmd -o img/Cassandra-uses-example.png -b white -s 2
```

---
💡 *Você pode visualizar o mapa interativo abrindo o arquivo [topicos.md](/mapas-mentais/topicos.md) e utilizando a extensão Markmap do seu editor.*