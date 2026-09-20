---
markmap:
  colorFreezeLevel: 2
  initialExpandLevel: 2
  maxWidth: 380
---

# 📚 Revisão de Conteúdo e Questões de Prova

## 🎯 1. Resumo dos Tópicos Chave

### 🏛️ Módulo 1: Evolução dos BDs e Big Data
- **Modelo Relacional (Edgar F. Codd, 1970)**
  - Organização estrita em **tabelas e colunas**
  - Foco em **consistência e integridade** dos dados
  - Garantia de transações **ACID**
  - Escalonamento **vertical** (*Scale-Up*)
- **Ecossistema Analítico**
  - **OLTP**: Processamento transacional de rotina em tempo real
  - **OLAP / Data Warehouse**: Consultas analíticas multidimensionais para tomada de decisão
  - **ETL**: Extração, Transformação e Carga (*Extract, Transform, Load*)

### ⚡ Módulo 2: Modelos NoSQL & Teorema CAP
- **Surgimento e Pilares do NoSQL (Carlo Strozzi, 1998)**
  - Resposta às limitações do SQL na Web e Big Data
  - **Esquema Flexível**: Dados com diferentes formatos na mesma coleção
  - **Escalabilidade Horizontal**: Distribuição nativa em clusters (*Scale-Out*)
  - **Alta Disponibilidade**: Operação contínua sem ponto único de falha
- **Os 4 Modelos NoSQL**
  - **Documentos**: Dados em JSON/BSON com aninhamento (*MongoDB, CouchDB*)
  - **Chave-Valor**: Acesso ultrarrápido por par único chave/valor (*Redis, DynamoDB*)
  - **Família de Colunas**: Otimizado para agregações sobre atributos específicos (*HBase, Cassandra*)
  - **Grafos**: Estruturado em nós e arestas com propriedades (*Neo4j*)
- **Transações ACID vs. Teorema CAP (Eric Brewer, 2000)**
  - **Propriedades ACID**: **A**tomicidade, **C**onsistência, **I**solamento e **D**urabilidade
  - **Princípio CAP**: Impossível garantir simultaneamente C, A e P em redes sujeitas a partição
  - **Perfis CAP**:
    - **CA**: Consistência + Disponibilidade (*SGBDR relacionais centralizados*)
    - **CP**: Consistência + Tolerância a Partições (*MongoDB, HBase, Redis*)
    - **AP**: Disponibilidade + Tolerância a Partições (*Cassandra, DynamoDB, CouchDB*)

### 🍃 Módulo 3: Arquitetura MongoDB & BSON
- **Mapeamento de Conceitos (SQL $\rightarrow$ NoSQL)**
  - Database $\rightarrow$ **Database**
  - Tabela $\rightarrow$ **Coleção (Collection)**
  - Linha $\rightarrow$ **Documento BSON**
  - Coluna $\rightarrow$ **Campo (Field)**
  - JOIN $\rightarrow$ **Documentos Embutidos (Embedded)**
  - Chave Primária $\rightarrow$ **Campo `_id`** (gerado automaticamente)
- **Estrutura do `_id` / ObjectId (12 Bytes / 24 Hex)**
  - **4 Bytes**: *Timestamp* (data/hora em segundos Unix)
  - **5 Bytes**: Identificador exclusivo da máquina / host
  - **3 Bytes**: Identificador do processo (*PID*)
  - **4 Bytes**: Contador sequencial incremental

### 🛠️ Módulo 4: CRUD, Documentos Embutidos e Validação
- **Comandos MONGOSH Essenciais**
  - **Create**: `insertOne({})`, `insertMany([{}, {}])`
  - **Read**: `find({})`, `findOne({})`
  - **Update**: `updateOne(filtro, { $set: {...} })`, `updateMany(...)`
  - **Delete**: `deleteOne(filtro)`, `deleteMany(filtro)`
- **Operadores de Consulta**
  - **Comparação**: `$gt` (>), `$gte` ($\ge$), `$lt` (<), `$lte` ($\le$), `$eq`, `$ne` ($\ne$), `$in`, `$nin`
  - **Lógicos**: `$and`, `$or`, `$not`, `$nor`
- **Modelagem de Relacionamentos**
  - **Documentos Embutidos**: Subdocumentos aninhados para leitura rápida atômica em 1 único I/O
- **Validação de Integridade**
  - **Document Validator (`$jsonSchema`)**: Regras no banco com `bsonType`, `required`, `minimum`

---

## 📝 2. Bateria de Questões de Prova Simuladas

### ❓ Questão 1: Teorema CAP & MongoDB (Múltipla Escolha)
- **Enunciado**
  - Qual a classificação padrão do MongoDB segundo o Teorema CAP e sua justificativa?
- **Alternativas**
  - A) **CA**: Garante Consistência e Disponibilidade, descartando Tolerância a Partições.
  - B) **AP**: Prioriza Disponibilidade e Tolerância a Partições com dados desatualizados.
  - C) **CP (Correta)**: Prioriza Consistência e Tolerância a Partições, podendo suspender gravações para manter integridade.
  - D) **BASE**: Garante as três propriedades simultaneamente com controle transacional rígido.
- **Gabarito & Justificativa**
  - **Gabarito**: **C**
  - **Explicação**: O MongoDB adota o perfil **CP**. Sob partição de rede, ele prioriza a consistência dos dados frente à disponibilidade ininterrupta.

### ❓ Questão 2: Anatomia do ObjectId (Múltipla Escolha)
- **Enunciado**
  - Qual a composição correta dos 12 bytes do `ObjectId` (`_id`) no MongoDB?
- **Alternativas**
  - A) 4B IP do cliente + 4B PID + 4B Timestamp
  - B) 4B Timestamp + 5B ID da Máquina + 3B PID + 4B Contador incremental (Correta)
  - C) 6B Chave pública BSON + 6B Data/hora ISO
  - D) 2B Banco + 3B Coleção + 4B Processo + 3B Hash MD5
- **Gabarito & Justificativa**
  - **Gabarito**: **B**
  - **Explicação**: 12 bytes divididos em: 4 bytes (Timestamp) + 5 bytes (Host/Máquina) + 3 bytes (PID) + 4 bytes (Contador).

### ❓ Questão 3: Relacionamentos SQL vs. NoSQL (Dissertativa)
- **Enunciado**
  - Como o relacionamento entre entidades (`JOIN`) é abordado no MongoDB e qual a vantagem arquitetural?
- **Resposta Esperada**
  - **Técnica**: Utilizam-se **Documentos Embutidos (subdocumentos/aninhamento)** em vez de `JOINs`.
  - **Vantagem de Desempenho**: Todos os dados relacionados são lidos em uma **única operação de I/O**, eliminando o custo de junção em larga escala.

### ❓ Questão 4: Prática MONGOSH - Operadores (Código)
- **Enunciado**
  - Consulta que retorne alunos com curso `"Análise e Desenvolvimento de Sistemas"` **OU** idade menor que `31`.
- **Solução MONGOSH**
  - Sintaxe: `db.aluno.find({ $or: [ { curso: "Análise e Desenvolvimento de Sistemas" }, { idade: { $lt: 31 } } ] })`
  - **Destaque**: Uso conjunto do operador lógico `$or` com o operador de comparação `$lt`.

### ❓ Questão 5: Governança com Document Validator (Dissertativa)
- **Enunciado**
  - Por que utilizar o **Document Validator (`$jsonSchema`)** se a grande vantagem do NoSQL é a flexibilidade?
- **Resposta Esperada**
  - **Propósito**: Estabelecer um **padrão mínimo de integridade e qualidade** diretamente no banco de dados.
  - **Benefício**: Evita dados inconsistentes ou malformatados exigindo campos essenciais (`required`) e tipos (`bsonType`), preservando flexibilidade para atributos acessórios.
