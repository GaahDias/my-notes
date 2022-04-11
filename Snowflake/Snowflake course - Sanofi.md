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
 
 ### Data warehouses
 Uma DW é a integração e consolidação de diferentes DBs, ou seja, são diversos DBs juntos. Isso é feito para que futuramente esses DBs sejam utilizados para análise.  
 Então basicamente o DW é um amontoado de dados providos de diferentes fontes, que podemos utilzar para reportes e análises.
 
 O processo de criação de uma DW é chamado de **ETL** (Extract, Transform & Load).

Etapas para o carregamento de dados em um DW:
1. **Raw data ou Staging area:** Extração dos dados puros sem transformações, da fonte para o Data Warehouse.
2. **Data integration ou Data Transformation:** A integração dos dados é marcada pela criação de relacionamento entre as diferentes Tabelas e Bancos, bem como a remoção de dados desnecessários, e transformação dos dados existentes. No final dessa etapa o dado deve estar transformado, limpo, e devidamente integrado.
3. **Access layer:** Processo de criação de acessibilidade e disponibilidade dos dados para diferentes propósitos. Por exemplo, para a utilização de times de Data Science, Machine Learning, bem como para reportes dos dados e utilização de outras ferramentas analíticas.

Essas são as 3 etapas para o carregamento dos dados. Aqui na Sanofi, chamamos a camada, ou etapa de *Raw data* de *STG*, *Data Integration* de *PSA* e *Access Layer* de *DWH*.
 
