---
markmap:
  colorFreezeLevel: 2
  initialExpandLevel: 2
  maxWidth: 380
---

# 🧭 Tópicos em Banco de Dados e Big Data

## 🏛️ Módulo 1: Evolução Histórica e Big Data

### 📜 Armazenamento Físico e Pré-Computação
- **Marcos Históricos Antigos**
  - **Epopeia de Gilgamesh** (3.500 a.C.): Primeiro livro registrado em tabuletas de argila
  - **Papiro de Ani** (2.200 a.C.): Registro em rolo vegetal no Egito Antigo
  - **Biblioteca de Alexandria** (283 a.C.): Primeira tentativa de centralização do conhecimento
  - **Pergaminho** e **Bíblia de Gutenberg** (1455 d.C.): Impressão em massa com tipos móveis
- **Problemas do Armazenamento Físico**
  - **Perda e Deterioração**: Vulnerabilidade a fogo, água, fungos e rasgos
  - **Dificuldade de Acesso e Localização**: Documentos dispersos e distantes
  - **Espaço e Custo Elevado**: Demanda por prateleiras, climatização e mão de obra dedicada
  - **Indexação Manual**: Dependência exclusiva de catalogação humana

### 💾 Primórdios da Computação (1900 - 1960)
- **Cartões Perfurados (1900 - 1950)**
  - Criação por Basile Bouchon / Herman Hollerith
  - Padrão dominante de entrada e processamento corporativo liderado pela **IBM**
- **Fitas Magnéticas (1949 - UNIVAC I)**
  - Armazenamento sequencial sobre fita com óxido magnético
  - Precursor das fitas cassete e revolução no processamento comercial
- **Primeiro Disco Rígido - IBM RAMAC 305 (1953)**
  - **Capacidade**: 5 MB a um custo de US$ 10.000 por megabyte
  - **Estrutura**: 50 pratos de 24 polegadas a 1200 rpm (tamanho de dois refrigeradores)
  - **Inovação (*Alan Shugart*)**: Introdução do **Acesso Aleatório** (*Random Access*)

### 🌲 Primeiros Modelos de Dados (Década de 1960)
- **Modelo Hierárquico (IBM IMS - 1960)**
  - Estrutura em árvore rígida com raiz (relacionamento pai-filho 1:N)
  - Dificuldade para representar relacionamentos muitos-para-muitos (N:M)
- **Modelo em Rede (CODASYL - Charles Bachman, 1964)**
  - Representação em formato de grafo com caixas e links
  - Permite registros com múltiplos nós-pais

### 🗄️ A Revolução do Modelo Relacional (Década de 1970)
- **Edgar Frank Codd (1970)**
  - Artigo clássico: *"A Relational Model of Data for Large Shared Data Banks"*
  - Primeiro modelo fundamentado formalmente na teoria matemática de conjuntos
- **Pilares do Modelo Relacional**
  - **Tabelas (Relações)**: Organizadas estritamente em linhas (tuplas) e colunas (atributos)
  - **Esquema Predefinido**: Estrutura rígida de dados definida antes da inserção
  - **Integridade Transacional (ACID)**: Garantia de consistência em sistema centralizado
  - **Escalonamento Vertical (*Scale-Up*)**: Aumento de poder computacional em servidor único
- **Pioneiros e Primeiros SGBDs**
  - **IBM System R (1974)**: Primeiro a implementar a linguagem SEQUEL (origem do SQL)
  - **INGRES (1974 - UC Berkeley)**: Criado por Michael Stonebraker (linguagem QUEL)
  - **Oracle v2 (1978)**: Primeiro SGBDR comercial de grande porte
  - **dBASE I (1979)**: Primeiro banco de dados embutido para computadores pessoais

### 📐 Consolidação dos SGBDR e Padrão SQL (Décadas de 1980 e 1990)
- **SGBDR / RDBMS Corporativo (1980)**
  - Gestão de concorrência, controle de falhas e garantia ACID
- **Padronização da Linguagem SQL**
  - **ANSI** (1986 - SQL-86) e **ISO** (1987 - SQL-87)
  - Padrão universal para definição (DDL) e manipulação (DML) de dados
- **Evolução dos Motores Relacionais**
  - **Microsoft SQL Server** (1989/1990)
  - **IBM Db2** (1983/1993)
  - **MySQL** (1995 - Open Source e pilar da Web)
  - **PostgreSQL** (1996 - Foco em robustez e extensibilidade)

### 📊 Ecossistema Analítico e Decisório (Década de 1990)
- **Data Warehouse (DW)**
  - Repositório central de dados consolidados e históricos para apoio à tomada de decisão
- **Diferenciação: OLTP vs. OLAP**
  - **OLTP (*Online Transaction Processing*)**: Sistemas transacionais rotineiros em tempo real (ERP, CRM)
  - **OLAP (*Online Analytical Processing*)**: Consultas analíticas multidimensionais complexas
- **Pipeline de Dados e Decisão**
  - **ETL (*Extract, Transform, Load*)**: Extração de múltiplas fontes, limpeza/transformação e carga no DW
  - **Data Mining**: Algoritmos para descoberta de padrões ocultos
  - **BI (*Business Intelligence*)**: Relatórios executivos e painéis de indicadores

### 🌐 Transição para a Nuvem e Raízes do NoSQL
- **Nascimento do Termo NoSQL (1998)**
  - Cunhado academicamente por **Carlo Strozzi** para bancos relacionais sem sintaxe SQL
- **Surgimento do DBaaS (*Database as a Service*)**
  - Infraestrutura de banco gerenciada em nuvem pública
  - **AWS**: Amazon RDS (2006) e DynamoDB (2012)
  - **Google Cloud**: Datastore (2008), BigTable (2014), Firestore (2017)
  - **Microsoft Azure**: SQL Database (2010), Table Storage (2012), Cosmos DB (2015/2017)
- **A Era do Big Data**
  - **Apache Hadoop (2006)**: Processamento massivo distribuído em clusters

---

## ⚡ Módulo 2: Bancos de Dados Não Relacionais (NoSQL) e Teoremas

### 🏛️ Três Pilares Fundamentais do NoSQL
- **1. Esquema Flexível (Dinâmico)**
  - **Ausência de Rigidez**: Documentos na mesma coleção podem ter campos distintos
  - **Evolução Fluida**: Adiciona novas propriedades sem executar `ALTER TABLE`
  - **Desnormalização**: Uso de subdocumentos embutidos, eliminando operações de `JOIN`
  - **Equilíbrio com Validação**: Uso de *Document Validator* (`$jsonSchema`) para regras de qualidade
- **2. Escalabilidade Horizontal (*Scale-Out*)**
  - **Arquitetura Distribuída**: Adiciona novas máquinas comuns a um cluster em rede
  - **Sem Teto Físico**: Supera as limitações físicas e os altos custos do *Scale-Up* (SQL)
  - **Processamento Paralelo**: Divisão da carga de leitura e gravação entre múltiplos nós
- **3. Alta Disponibilidade**
  - **Operação Contínua**: Resiliência contra quedas de nós e falhas de infraestrutura
  - **Sem Ponto Único de Falha**: Dados replicados entre servidores do cluster
  - **Atendimento a Altas Demandas**: Suporte a milhões de requisições web simultâneas

### 🧩 Os 4 Principais Modelos de Dados NoSQL
- **📄 Orientado a Documentos**
  - **Estrutura**: Armazenamento em documentos auto-descritivos (JSON / BSON) com arrays e aninhamentos
  - **Casos de Uso**: E-commerce, blogs/CMS, catálogos e sistemas que exigem leitura rápida desnormalizada
  - **Exemplos**: **MongoDB**, **Apache CouchDB**, **Azure Cosmos DB**, **Google Cloud Firestore**, **Amazon DocumentDB**
- **🔑 Orientado a Chave-Valor (*Key-Value*)**
  - **Estrutura**: Pares simples de chave única e valor associado (menor sobrecarga estrutural)
  - **Casos de Uso**: Caching em memória, gerenciamento de sessões web e filas rápidas
  - **Exemplos**: **Redis** (Salvatore Sanfilippo, 2010), **Amazon DynamoDB** (2012), **Couchbase**, **Oracle NoSQL**
- **📊 Orientado a Família de Colunas (*Column-Family*)**
  - **Estrutura**: Dados agrupados em famílias de colunas dinâmicas em vez de linhas fixas
  - **Casos de Uso**: Consultas analíticas pesadas sobre atributos específicos, IoT, sensores e séries temporais
  - **Exemplos**: **Apache HBase** (Hadoop), **Apache Cassandra**, **Google Cloud BigTable**
- **🕸️ Orientado a Grafos**
  - **Estrutura**: Modelagem em Nós (entidades) e Arestas (relacionamentos diretos com propriedades)
  - **Casos de Uso**: Redes sociais, análise de fraudes, rotas logísticas e motores de recomendação
  - **Exemplos**: **Neo4j**, OrientDB, ArangoDB

### ⚖️ Teorema CAP e Modelos Transacionais
- **Propriedades ACID (Modelo Relacional)**
  - **Atomicidade**: A transação é executada por completo ou totalmente abortada ("tudo ou nada")
  - **Consistência**: O banco transita exclusivamente entre estados válidos e íntegros
  - **Isolamento**: Transações concorrentes são executadas sem interferir umas nas outras
  - **Durabilidade**: Uma vez confirmados (*commit*), os dados persistem mesmo após falhas
- **Teorema CAP (Eric Brewer - 2000)**
  - **Princípio**: Em um sistema distribuído, é impossível garantir simultaneamente C, A e P
  - **Consistência (C)**: Todas as leituras recebem o dado mais recente ou geram erro
  - **Disponibilidade / Availability (A)**: Toda requisição recebe uma resposta válida (mesmo desatualizada)
  - **Tolerância a Partições (P)**: O sistema continua operando mesmo com quebra de comunicação na rede
- **Perfis no Teorema CAP**
  - **CA (Consistência + Disponibilidade)**: Bancos relacionais centralizados (sem particionamento)
  - **CP (Consistência + Partição)**: Prioriza dado íntegro em falhas de rede (Ex: **MongoDB**, **HBase**, **Redis**)
  - **AP (Disponibilidade + Partição)**: Prioriza manter o sistema online a qualquer custo (Ex: **Cassandra**, **CouchDB**, **DynamoDB**)
- **Modelo BASE (Filosofia NoSQL)**
  - **Basically Available**: O sistema prioriza responder mesmo diante de falhas
  - **Soft-state**: O estado dos dados pode oscilar sem garantia estrita imediata
  - **Eventual consistency**: A consistência entre todos os nós é convergida com o tempo

### 🔄 Integração Moderna: Persistência Poliglota
- **Arquitetura de Microserviços**
  - Adoção combinada de diferentes bancos de acordo com a função do serviço:
  - **SQL**: Módulos financeiros, cadastros rígidos e pagamentos (ACID)
  - **NoSQL Chave-Valor (Redis)**: Cache de baixa latência e controle de sessão
  - **NoSQL Documentos (MongoDB)**: Catálogo de produtos, perfis e feeds flexíveis
  - **NoSQL Grafos (Neo4j)**: Mecanismo de conexões e recomendações personalizadas
- **Bancos Multi-Modelo**
  - Mecanismos capazes de armazenar documentos, grafos e chave-valor em um único motor (Ex: Azure Cosmos DB)

---

## 🍃 Módulo 3: Arquitetura MongoDB e Formato BSON

### 🔄 Mapeamento Conceitual: SQL vs. MongoDB
- `Banco de Dados (Database)` $\rightarrow$ **Database**
- `Tabela (Table)` $\rightarrow$ **Coleção (Collection)**
- `Linha (Row / Tuple)` $\rightarrow$ **Documento (Document BSON)**
- `Coluna (Column)` $\rightarrow$ **Campo (Field)**
- `Chave Primária (Primary Key)` $\rightarrow$ **Campo `_id` (ObjectId)**
- `JOIN entre Tabelas` $\rightarrow$ **Documentos Embutidos (Embedded) ou `$lookup`**

### 📦 Formatos de Armazenamento: JSON vs. BSON
- **JSON (*JavaScript Object Notation*)**
  - Formato textual e legível para humanos
  - Utilizado primariamente na troca de dados entre cliente e servidor (Web APIs)
  - Suporta apenas tipos básicos: String, Number, Boolean, Array, Object e Null
- **BSON (*Binary JSON*)**
  - Formato binário serializado para armazenamento interno do MongoDB
  - Otimizado para compactação de espaço e alta velocidade de travessia (*scanning*)
  - Adiciona tipos avançados: `ObjectId`, `Date`, `Int32`, `Int64`, `Decimal128`, `BinData`, `Regex`

### 🆔 Anatomia do `ObjectId` (`_id`) de 12 Bytes
- **Tamanho Total**: 12 Bytes (representado por string de **24 caracteres hexadecimais**)
- **Composição dos Bytes**:
  - **4 Bytes**: *Timestamp* (data/hora de criação do documento em segundos Unix)
  - **5 Bytes**: Identificador exclusivo da máquina / host (*Machine ID*)
  - **3 Bytes**: Identificador do processo gerador (*Process ID / PID*)
  - **4 Bytes**: Contador sequencial incremental com valor inicial aleatório
- **Vantagens Arquiteturais**:
  - Geração descentralizada e única em clusters distribuídos sem bloqueios de rede
  - Ordenação cronológica natural por padrão através do campo `_id`

### 💻 Ambiente Prático: Terminal MONGOSH
- **Comandos de Contexto e Navegação**
  - `show dbs`: Exibe todos os bancos de dados existentes
  - `use <nome_do_banco>`: Alterna para o banco (criação sob demanda ao inserir dado)
  - `db`: Mostra o banco de dados ativo no momento
  - `show collections`: Lista todas as coleções do banco atual
  - `cls` ou `console.clear()`: Limpa a tela do terminal

---

## 🛠️ Módulo 4: Operações CRUD, Modelagem e Validação

### 📝 Operações CRUD no MONGOSH
- **Create (Inserção)**
  - `db.colecao.insertOne({ campo: "valor" })`: Insere um único documento
  - `db.colecao.insertMany([ { ... }, { ... } ])`: Insere múltiplos documentos em lote
- **Read (Consulta)**
  - `db.colecao.find(filtro, projecao)`: Busca documentos correspondentes
  - `db.colecao.findOne(filtro)`: Retorna o primeiro documento que atende à condição
  - **Modificadores**:
    - `.sort({ campo: 1 })`: Ordenação ascendente (1) ou descendente (-1)
    - `.limit(n)`: Limita o número de registros retornados
    - `.skip(n)`: Salta um número definido de registros (paginação)
    - `.countDocuments(filtro)`: Totaliza registros correspondentes
- **Update (Atualização)**
  - `db.colecao.updateOne(filtro, { $set: { campo: "novo" } })`: Atualiza o primeiro encontrado
  - `db.colecao.updateMany(filtro, { $set: { campo: "novo" } })`: Atualiza todos os documentos encontrados
  - **Operadores de Atualização**:
    - `$set`: Modifica ou cria novos campos
    - `$unset`: Remove campos existentes do documento
    - `$inc`: Incrementa ou decrementa valores numéricos
    - `$push`: Adiciona um elemento a um array
    - `$pull`: Remove elementos específicos de um array
- **Delete (Exclusão)**
  - `db.colecao.deleteOne(filtro)`: Remove o primeiro documento que atender à condição
  - `db.colecao.deleteMany(filtro)`: Remove todos os documentos que atenderem à condição

### 🔍 Operadores de Consulta
- **Operadores de Comparação**
  - `$gt` (>): Maior que (*Greater Than*)
  - `$gte` ($\ge$): Maior ou igual (*Greater Than or Equal*)
  - `$lt` (<): Menor que (*Less Than*)
  - `$lte` ($\le$): Menor ou igual (*Less Than or Equal*)
  - `$eq` (=): Igual a (*Equal*)
  - `$ne` ($\ne$): Diferente de (*Not Equal*)
  - `$in`: Valor contido em uma lista de valores especificada
  - `$nin`: Valor não contido na lista de valores
- **Operadores Lógicos**
  - `$and`: Conjunção lógica (todas as expressões verdadeiras)
  - `$or`: Disjunção lógica (pelo menos uma expressão verdadeira)
  - `$not`: Inverte o resultado da avaliação da expressão
  - `$nor`: Negação conjunta (nenhuma expressão pode ser verdadeira)

### 📐 Modelagem de Dados NoSQL
- **Documentos Embutidos (Subdocumentos / Desnormalização)**
  - Aninhamento de objetos JSON/BSON dentro de um documento pai
  - **Indicação**: Relacionamentos 1:1 e 1:N contidos (dados acessados juntos frequentemente)
  - **Vantagens**: Leitura ultrarrápida em uma única operação de disco (I/O)
  - **Limitação**: Limite máximo de **16 MB** por documento BSON
- **Referências (Normalização)**
  - Armazenamento do identificador `_id` como chave estrangeira
  - **Indicação**: Relacionamentos 1:N grandes ou N:M complexos
  - **Consulta**: Requer consultas adicionais ou o estágio de agregação `$lookup`

### 🛡️ Validação de Esquema e Ferramentas
- **Document Validator (`$jsonSchema`)**
  - Combina a flexibilidade do NoSQL com a governança e integridade de dados
  - **Propriedades Principais**:
    - `validator: { $jsonSchema: { ... } }`: Bloco definidor das regras
    - `required: [ "nome", "email" ]`: Campos obrigatórios no documento
    - `bsonType`: Tipos de dados exigidos (ex: `"string"`, `"int"`, `"date"`, `"array"`)
    - `minimum` / `maximum`: Limites para valores numéricos
- **Ferramentas do Ecossistema**
  - **MongoDB Compass**: Interface gráfica (GUI) oficial para análise visual de esquemas, CRUD interativo, construção de queries e importação/exportação (JSON, CSV)
  - **MongoDB Atlas**: Plataforma de nuvem DBaaS totalmente gerenciada com particionamento automático (*sharding*) e replicação (*replica sets*)
