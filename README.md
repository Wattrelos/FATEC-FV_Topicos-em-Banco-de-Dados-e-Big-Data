

# Mapas Mentais - Tópicos em Banco de Dados e Big Data

> O mapa mental foi elaborado, estruturado e consolidado no arquivo [Mapas mentais](https://wattrelos.github.io/FATEC-FV_Topicos-em-Banco-de-Dados-e-Big-Data/) no formato **Markmap**.
> 📖 Confira também o tutorial completo: [Como gerar e publicar Mapas Mentais com IA e GitHub Actions](/gerando-mapas-mentais.md).

### 🌟 O que foi organizado e integrado:

1. **Configuração Markmap Otimizada (Frontmatter)**:
   - `initialExpandLevel: 2`: O mapa mental abre inicialmente focado nos 4 módulos principais, permitindo uma navegação interativa sem poluição visual.
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

---
💡 *Você pode visualizar o mapa interativo abrindo o arquivo [topicos.md](/mapas-mentais/topicos.md) e utilizando a extensão Markmap do seu editor.*