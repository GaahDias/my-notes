# Curso de snowflake

### Novo e antigo console
No webapp do Snowflake é possível transitar entre a nova interface, com sistema de autocomplete, e o antigo console (clássico).
* Para ir para o console antigo:
	 ![Antigo console](https://i.imgur.com/qhBlviF.png)
* Para a nova interface: 
![Nova interface](https://i.imgur.com/grZMjU6.png)

### Carregamento de dados
Podemos carregar dados de arquivos internos no computador, como por exemplo um csv, e também de através de plataformas como AWS.
* Carregamento de dados internos:
![Carregamento de dados](https://i.imgur.com/XEOno3X.png)

* Carregamento de dados com bucket AWS:
```sql
// S3 bucket from lesson 12:
// s3://bucketsnowflakes3/Loan_payments_data.csv

CREATE TABLE LOAN_PAYMENT (
    "Loan_ID" STRING,
    "loan_status" STRING,
    "Principal" INTEGER,
    "terms" INTEGER,
    "effective_date" DATE,
    "due_date" DATE,
    "paid_off_time" STRING,
    "past_due_days" STRING,
    "age" INTEGER,
    "education" STRING,
    "Gender" STRING);
    
COPY INTO LOAN_PAYMENT
    FROM s3://bucketsnowflakes3/Loan_payments_data.csv
    file_format = (type = csv
                  field_delimiter = ','
                  skip_header = 1);
 ```
 
Também é válido mencionar dois métodos principais utilizados no carregamento de dados: o **Bulk loading** (mais utilizado, bom para altas quantidades de dados, e possui o comando COPY), e o **Continuous loading** (menos utilizado, bom para pequenas quantidades de dados, e possui a feature serverless snowpipe). Posteriormente esses métodos serão aprofundado em mais detalhes.
 
 ### Data Warehouses
 Uma DW é a integração e consolidação de diferentes DBs, ou seja, são diversos DBs juntos. Isso é feito para que futuramente esses DBs sejam utilizados para análise.  
 Então basicamente o DW é um amontoado de dados providos de diferentes fontes, que podemos utilzar para reportes e análises.
 
 O processo de criação de uma DW é chamado de **ETL** (Extract, Transform & Load).

Etapas para o carregamento de dados em um DW:
1. **Raw data ou Staging area:** Extração dos dados puros sem transformações, da fonte para o Data Warehouse.
2. **Data integration ou Data Transformation:** A integração dos dados é marcada pela criação de relacionamento entre as diferentes Tabelas e Bancos, bem como a remoção de dados desnecessários, e transformação dos dados existentes. No final dessa etapa o dado deve estar transformado, limpo, e devidamente integrado.
3. **Access layer:** Processo de criação de acessibilidade e disponibilidade dos dados para diferentes propósitos. Por exemplo, para a utilização de times de Data Science, Machine Learning, bem como para reportes dos dados e utilização de outras ferramentas analíticas.

Essas são as 3 etapas para o carregamento dos dados. Aqui na Sanofi, chamamos a camada, ou etapa de *Raw data* de *STG*, *Data Integration* de *PSA* e *Access Layer* de *DWH*.
 
 ### Cloud Computing
 A computação em nuvem surgiu por conta do custo e da dificuldade de se manter um servidor, o que não chega a ser viável dependendo da empresa. Com servidores de terceiros, as empresas podem focar no seu software e aplicações, sem ter que se preocupar com manutenção de servidores próprios.
 Algo válido de se mencionar também são os Cloud Providers: são eles que cuidam dos servidores físicos, máquinas virtuais, bem como do hardware dos equipamentos. Alguns dos Cloud Providers mais utilizados são AWS, Azure e GCP. 

### Software as a Service (SaaS)
O modelo SaaS tem ganhado muita popularidade ultimamente. Nesse modelo, o sistema não é comercializado como um produto, mas sim como serviço, como o próprio nome sugere. Assim, não é feita a instalação de nenhum programa nos equipamentos e as aplicações são usadas pela internet. O Snowflake, por exemplo, é um SaaS.
Nesse modelo o cliente paga pela assinatura do serviço oferecido (o acesso) e não por um produto, o que elimina o custo com a aquisição de licenças.

![Cloud & SaaS](https://i.imgur.com/KZU9yPA.png)

### Snowflake roles
No Snowflake temos diferentes roles, que dão diferentes permissões para os usuários.

![Roles](https://i.imgur.com/B6NbfvU.png)

### Stages para carregamento de dados
Os stages que utilizamos no carregamento de dados nada mais são do que a localização dos dados que serão carregados. Eles podem ser internos (armazenamento local do próprio Snowflake) ou externos (S3 buckets, Google Cloud Platform, Microsoft Azure).

Para criar um stage externo, utilizamos os seguintes comandos:
```sql
// Db to manage stage objects, file formats, etc.
CREATE OR REPLACE DATABASE MANAGE_DB;
CREATE OR REPLACE SCHEMA external_stages;

// External stage
CREATE OR REPLACE STAGE MANAGE_DB.external_stages.aws_stage
    url = 's3://bucketsnowflakes3'
    credentials = (aws_key_id = 'ABCD_DUMMY_ID' aws_secret_key = '1234abcd_key');
    
```

Alguns outros comandos úteis para manipulação do stage:
```sql
// Description of external stage
DESC STAGE MANAGE_DB.external_stages.aws_stage;
    
// Alter external stage
ALTER STAGE MANAGE_DB.external_stages.aws_stage
    SET credentials = (aws_key_id = 'XYZ_DUMMY_ID' aws_secret_key = '987xyz')
    
// Public accessible staging area (no credentials)
CREATE OR REPLACE STAGE MANAGE_DB.external_stages.aws_stage
    url = 's3://bucketsnowflakes3';
    
// List files in stage
LIST @MANAGE_DB.external_stages.aws_stage
```

### Copy command
Para de fato carregarmos os dados que estão em um stage externo, podemos utilizar o comando **Copy**:
```sql
// Creating table to receive the data
CREATE TABLE FIRST_DB.PUBLIC.ORDERS (
    ORDER_ID VARCHAR(30),
    AMOUNT INT,
    PROFIT INT,
    QUANTITY INT,
    CATEGORY VARCHAR(30),
    SUBCATEGORY VARCHAR(30)
);

// Copy command
COPY INTO FIRST_DB.PUBLIC.ORDERS
    FROM @MANAGE_DB.external_stages.aws_stage
    file_format = (type = csv field_delimiter = ',' skip_header = 1)
    files = ('OrderDetails.csv');
    // Or we could use pattern matching for looking for our files:
    // pattern = ('*.csv')
```
É válido mencionar também que o Snowflake armazena metadados dos nossos carregamentos de dados. Com isso, por uma medida de segurança. não é permitido o carregamento duplicado de dados. Ou seja, caso o comando copy seja rodado novamente para o mesmo arquivo, os dados não serão carregados uma segunda vez.
