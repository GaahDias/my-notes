 

# What kind of data does Pandas handle?

```python
In [1]: import pandas as pd
```

Um exemplo de armazenamento de dados com Pandas utilizando `DataFrame`:

```python
In [2]: df = pd.DataFrame(
   ...:     {
   ...:         "Name": [
   ...:             "Braund, Mr. Owen Harris",
   ...:             "Allen, Mr. William Henry",
   ...:             "Bonnell, Miss. Elizabeth",
   ...:         ],
   ...:         "Age": [22, 35, 58],
   ...:         "Sex": ["male", "male", "female"],
   ...:     }
   ...: )
```

> Para armazenar dados manualmente em uma tabela, crie um DataFrame. Quando usando um dicionário de listas, as keys serão usadas como headers das colunas, e os valores de cada lista como colunas.
>
> Pandas docs - What kind of data does pandas handle?

Um DataFrame é uma estrutura de dados bidimensional, que armazena diferentes tipos de dados (chars, ints, floats, etc) em colunas. É muito semelhante a uma planilha de excel, ou uma tabela SQL.

**Cada coluna de um DataFrame é uma Série (Series)**:

Quando selecionamos uma única coluna de um DataFrame, o resultado é uma Series. Para selecionar a coluna, basta passar o nome dela em `[]`:

```python
In [4]: df["Age"]
Out[4]: 
0    22
1    35
2    58
Name: Age, dtype: int64
```

Também é possível criar uma Series do zero:

```python
In [5]: ages = pd.Series([22, 35, 58], name="Age")

In [6]: ages
Out[6]: 
0    22
1    35
2    58
Name: Age, dtype: int64
```

Vale lembrar que uma Series não possui titulo na coluna, visto que é apenas uma coluna. Uma serie tem titulos para as linhas.

**Como fazer algo com um DataFrame ou Series:**

Series e Dataframes possuem métodos. Por exemplo, caso seja necessário descobrir o valor máximo e mínimo de uma coluna em um df/series:

```python
# Para Dataframes:
In [7]: df["Age"].max()
Out[7]: 58
# Para Series:
In [8]: ages.min()
Out[8]: 22
```

Agora para obter uma descrição mais estatística dos dados no dataframe:

```python
In [9]: df.describe()
Out[9]: 
             Age
count   3.000000
mean   38.333333
std    18.230012
min    22.000000
25%    28.500000
50%    35.000000
75%    46.500000
max    58.000000
```

> O método describe() retorna uma visão geral dos dados numéricos em um DataFrame. Já que Name e Sex são do tipo string, ou texto, não são incluídos por padrão no retorno do método.
>
> Pandas docs - What kind of data does pandas handle?



# How do I read and write tabular data?

Pandas disponibiliza funções do tipo read para ler diferentes arquivos de dados nativamente. Temos: `read_csv`, `read_sql`, `read_json`, `read_excel`, `read_html`...

Outra utilidade muito poderosa do Pandas é a posibilidade de ler uma quantidade de dados especifica, partindo do começo dos dados **(head)**, ou do final **(tail)**.

Também conseguimos obter informações sobre cada coluna de um DataFrame passando o atributo `dtypes`:

```python
In [5]: titanic.dtypes
Out[5]: 
PassengerId      int64
Survived         int64
Pclass           int64
Name            object
Sex             object
Age            float64
SibSp            int64
Parch            int64
Ticket          object
Fare           float64
Cabin           object
Embarked        object
dtype: object
    
# Integers (int64), Floats (Float64), Strings (Object)
```

Para converter dados em diferentes tipos é muito simples utilizando Pandas também:

```python
In [6]: titanic.to_excel("titanic.xlsx", sheet_name="passengers", index=False)
```

> No exemplo acima, o parametro `sheet_name` corresponde ao nome da planilha, que no caso será atribuído "passangers", ao invés do padrão "Sheet1". Já o `index` é responsável por atribuir os indices das linhas do DataFrame, no caso como está False, os indices serão ocultos na versão Excel.
>
> Pandas docs - How do I read and write tabular data?

Podemos também obter um resumo técnico do nosso DataFrame, usando a função `info()`:

```python
In [9]: titanic.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 891 entries, 0 to 890
Data columns (total 12 columns):
 #   Column       Non-Null Count  Dtype  
---  ------       --------------  -----  
 0   PassengerId  891 non-null    int64  
 1   Survived     891 non-null    int64  
 2   Pclass       891 non-null    int64  
 3   Name         891 non-null    object 
 4   Sex          891 non-null    object 
 5   Age          714 non-null    float64
 6   SibSp        891 non-null    int64  
 7   Parch        891 non-null    int64  
 8   Ticket       891 non-null    object 
 9   Fare         891 non-null    float64
 10  Cabin        204 non-null    object 
 11  Embarked     889 non-null    object 
dtypes: float64(2), int64(5), object(5)
memory usage: 83.7+ KB
```

> O método info() nos retorna informações técnicas quanto ao nosso DataFrame. Vamos explicar o resultado em detalhes:
>
> * É de fato um DataFrame.
> * Tem 891 entradas (ou linhas).
> * Cada linha possui uma label (index) com valores entre 0 e 890.
> * A tabela  tem 12 colunas. A maioria delas possui um valor para cada linha (todos que estão como 891 valores `non-null`). Algumas colunas possuem linhas sem valores, por terem menos de 819 `non-null`.
> * As colunas `Name, Sex, Cabin, Embarked` consistem de dados textuais (strings, ou objetos). As outras colunas possuem dados numéricos.
> * Os tipos de dados de cada coluna são sumarizados com o dtype.
> * O valor aproximado de RAM Usado para o DataFrame também é apresentado.
>
> Pandas docs - How do I read and write tabular data?



# How do I select a subset of a `DataFrame`?

**Como selecionar colunas em específico de um DataFrame:**

Como já foi mencionado anteriormente, para se obter os dados de *uma* coluna em específico de um DataFrame basta passar o nome da coluna em `[]`:

```python
In [4]: ages = titanic["Age"]

In [5]: ages.head()
Out[5]: 
0    22.0
1    38.0
2    26.0
3    35.0
4    35.0
Name: Age, dtype: float64
```

É válido mencionar também que o retorno disso é uma Series, e não um DataFrame. Agora, e para juntar os dados de uma coluna com a outra? É simples, basta passar ambos dentro de uma lista:

```python
In [8]: age_sex = titanic[["Age", "Sex"]]

In [9]: age_sex.head()
Out[9]: 
    Age     Sex
0  22.0    male
1  38.0  female
2  26.0  female
3  35.0  female
4  35.0    male
```

Vamos também dar uma olhada no atributo shape de uma Series/DataFrame, que retorna o número de colunas e de linhas do mesmo:

```python
In [7]: titanic["Age"].shape
Out[7]: (891,)

In [8]: titanic.shape
Out[8]: (891, 12)
    
# NOTA: O retorno é uma túpla (tuple).
```

**Como filtrar linhas específicas de um DataFrame:**

Como no Excel, também podemos filtrar por valores usando condicionais como **>, <, ==**, **!=**, bem como acessando um elemento em específico:

```python
In [12]: above_35 = titanic[titanic["Age"] > 35]
    	 equal_to_35 = titanic[titanic["Age"] == 35]
         bellow_35 = titanic[titanic["Age"] < 35]
In [13]: titanic["Age"][30]
Out[13]: 40.0
```

Uma função que muito utilizada no Pandas é a `isin()` (is in). Ela é do tipo booleana, e retorna um valor (True ou False) para cada item da tabela. É muito utilizada também para filtrar itens:

```python
# Apenas passageiros que estavam na cabine classe 2 e 3 utilizando isin:
In [16]: class_23 = titanic[titanic["Pclass"].isin([2, 3])]
# É a mesma coisa que:
In[16]: class_23 = titanic[(titanic["Pclass"] == 2) | (titanic["Pclass"] == 3)]
```

> Nota: Em python quando combinando multiplos condicionais, cada condição precisa estar cercada por `()`. Também não é permitido a utilização de `or / and`, precisamos utilizar os operadores `| e &`.
>
> Pandas docs - How do I select a subset of a DataFrame?



# How to create plots in Pandas?

```python
import pandas as pd
import matplotlib.pyplot as plt
```

Plots são diferentes formas de se visualizar os dados, normalmente em forma de gráficos, e funcionam tanto com Dataframes, quanto com Series (Obs.: **Todo plot criado com Pandas é um objeto matplotlib**). É muito simples de se criar um plot utilizando Pandas (por padrão é criado uma linha para cada coluna com dados numéricos):

```python
In [5]: air_quality.plot()
Out[5]: <AxesSubplot:xlabel='datetime'>
```

Caso queira um plot com apenas dados de uma coluna em específico:

```python
In [6]: air_quality["station_paris"].plot()
Out[6]: <AxesSubplot:xlabel='datetime'>
```

O plot padrão que nos é apresentado é o `line`. Porém, existem outras formas de se visualizar os dados utilizando Plot no Pandas. Para saber de todas, podemos usar do autocomplete do ambiente que estivermos trabalhando, ou utilizar o seguinte comando:

```python
In [8]: [
   ...:     method_name
   ...:     for method_name in dir(air_quality.plot)
   ...:     if not method_name.startswith("_")
   ...: ]
Out[8]: 
['area',
 'bar',
 'barh',
 'box',
 'density',
 'hexbin',
 'hist',
 'kde',
 'line',
 'pie',
 'scatter']
```

Cada um desses tipos nos retornará uma nova forma de visualizar os dados de nossa Dataframe ou Series. É importante lembrar também que cada um possui diferentes parametros, então é necessário [checar a documentação](https://pandas.pydata.org/pandas-docs/stable/user_guide/visualization.html#visualization-other) antes de sair utilizando algum.

Outra funcionalidade interessante dos plots que devemos ressaltar é a possibilidade de apresentar diferentes gráficos em conjunto. Cada um separado em um subplot diferente:

```python
In [10]: axs = air_quality.plot.area(figsize=(12, 4), subplots=True)
```

Podemos também customizar ainda mais nossos gráficos, bem como salvá-los:

```python
In [11]: fig, axs = plt.subplots(figsize=(12, 4))

In [12]: air_quality.plot.area(ax=axs)
Out[12]: <AxesSubplot:xlabel='datetime'>

In [13]: axs.set_ylabel("NO$_2$ concentration")
Out[13]: Text(0, 0.5, 'NO$_2$ concentration')

In [14]: fig.savefig("no2_concentrations.png")
    
# EXPLICAÇÃO:

fig, axs = plt.subplots(figsize=(12, 4))        # Create an empty matplotlib Figure and Axes
air_quality.plot.area(ax=axs)                   # Use pandas to put the area plot on the prepared Figure/Axes
axs.set_ylabel("NO$_2$ concentration")          # Do any matplotlib customization you like, in this case changing the label
fig.savefig("no2_concentrations.png")           # Save the Figure/Axes using the existing matplotlib method.
```

No exemplo acima estamos utilizando a lib matplot em conjunto com o Pandas. Dessa forma, temos opções quase intermináveis de customizações. É extremamente comum se deparar com matplotlib e pandas sendo usados juntos.



# How to create new columns derived from existing columns?

Criar novas colunas em um Dataframe é extremamente fácil: basta passar o nome entre [], como fazemos com dictionaries. Feito isso, a nova coluna deve aparecer ao lado direito do Dataframe.

```python
In [4]: air_quality["london_mg_per_cubic"] = air_quality["station_london"] * 1.882

In [5]: air_quality.head()
Out[5]: 
                     station_antwerp  station_paris  station_london  london_mg_per_cubic
datetime                                                                                
2019-05-07 02:00:00              NaN            NaN            23.0               43.286
2019-05-07 03:00:00             50.5           25.0            19.0               35.758
2019-05-07 04:00:00             45.0           27.7            19.0               35.758
2019-05-07 05:00:00              NaN           50.4            16.0               30.112
2019-05-07 06:00:00              NaN           61.9             NaN                  NaN
```

```python
In [6]: air_quality["ratio_paris_antwerp"] = (
   ...:     air_quality["station_paris"] / air_quality["station_antwerp"]
   ...: )
   ...: 

In [7]: air_quality.head()
Out[7]: 
                     station_antwerp  station_paris  station_london  london_mg_per_cubic  ratio_paris_antwerp
datetime                                                                                                     
2019-05-07 02:00:00              NaN            NaN            23.0               43.286                  NaN
2019-05-07 03:00:00             50.5           25.0            19.0               35.758             0.495050
2019-05-07 04:00:00             45.0           27.7            19.0               35.758             0.615556
2019-05-07 05:00:00              NaN           50.4            16.0               30.112                  NaN
2019-05-07 06:00:00              NaN           61.9             NaN                  NaN                  NaN

```

Podemos também renomear o nome de colunas já existentes, utilizando a função `rename`, e passando em forma de dicionário as colunas, e seus respectivos novos nomes:

```python
In [8]: air_quality_renamed = air_quality.rename(
   ...:     columns={
   ...:         "station_antwerp": "BETR801",
   ...:         "station_paris": "FR04014",
   ...:         "station_london": "London Westminster",
   ...:     }
   ...: ) 
In [9]: air_quality_renamed.head()
Out[9]: 
                     BETR801  FR04014  London Westminster  london_mg_per_cubic  ratio_paris_antwerp
datetime                                                                                           
2019-05-07 02:00:00      NaN      NaN                23.0               43.286                  NaN
2019-05-07 03:00:00     50.5     25.0                19.0               35.758             0.495050
2019-05-07 04:00:00     45.0     27.7                19.0               35.758             0.615556
2019-05-07 05:00:00      NaN     50.4                16.0               30.112                  NaN
2019-05-07 06:00:00      NaN     61.9                 NaN                  NaN                  NaN
```

Porém o mapeamento dessa função não precisa ficar preso em nomes fixos. Podemos por exemplo, converter os nomes das colunas para lowercase usando uma função:

```python
In [10]: air_quality_renamed = air_quality_renamed.rename(columns=str.lower)

In [11]: air_quality_renamed.head()
Out[11]: 
                     betr801  fr04014  london westminster  london_mg_per_cubic  ratio_paris_antwerp
datetime                                                                                           
2019-05-07 02:00:00      NaN      NaN                23.0               43.286                  NaN
2019-05-07 03:00:00     50.5     25.0                19.0               35.758             0.495050
2019-05-07 04:00:00     45.0     27.7                19.0               35.758             0.615556
2019-05-07 05:00:00      NaN     50.4                16.0               30.112                  NaN
2019-05-07 06:00:00      NaN     61.9                 NaN                  NaN                  NaN
```



# How to calculate summary statistics?

**Mean**:

Para calcular médias estatiscias podemos usar a função `mean`:

```python
In [4]: titanic["Age"].mean()
Out[4]: 29.69911764705882
```

**Median**:

Para calcular a mediana utilizamos a função `median`:

```python
In [5]: titanic[["Age", "Fare"]].median()
Out[5]: 
Age     28.0000
Fare    14.4542
dtype: float64
```

**Agg**:

Já vimos anteriormente a função `describe` para visualizar um resumo geral de informações sobre as colunas. Mas temos outra função para filtrar quais informações aparecerão, o `agg`:

```python
In [7]: titanic.agg(
   ...:     {
   ...:         "Age": ["min", "max", "median", "skew"],
   ...:         "Fare": ["min", "max", "median", "mean"],
   ...:     }
   ...: )
Out[7]: 
              Age        Fare
min      0.420000    0.000000
max     80.000000  512.329200
median  28.000000   14.454200
skew     0.389108         NaN
mean          NaN   32.204208
```

**Groupby**:

Para agruparmos por uma coluna em específico utilizamos o `groupby`. Veja este exemplo de `groupby` combinado com `mean` para saber a média de idades entre passageiros do sexo masculino, e feminino:

```python
In [8]: titanic[["Sex", "Age"]].groupby("Sex").mean()
Out[8]: 
              Age
Sex              
female  27.915709
male    30.726645

# OUTROS EXEMPLOS DE GROUPBY:

In [10]: titanic.groupby("Sex")["Age"].mean() # terá o mesmo resultado que o de cima
Out[10]: 
Sex
female    27.915709
male      30.726645
Name: Age, dtype: float64
        

In [9]: titanic.groupby("Sex").mean()
Out[9]: 
        PassengerId  Survived    Pclass        Age     SibSp     Parch       Fare
Sex                                                                              
female   431.028662  0.742038  2.159236  27.915709  0.694268  0.649682  44.479818
male     454.147314  0.188908  2.389948  30.726645  0.429809  0.235702  25.523893

```

> Calcular a estatísica de cada categoria em uma coluna (ex.: masculino/feminino da coluna Sex) é uma tarefa comum. O método groupby foi feito para lidar com esse tipo de operações, seguindo o padrão *split-apply-combine* (dividir-aplicar-combinar):
>
> * **Divide** os dados em grupos (no caso do exemplo, em masculino e feminino).
> * **Aplica** uma função para cada grupo independentemente (no exemplo a média com mean).
> * **Combina** os resultados em uma estrutura só.
>
> Pandas Docs - How to calculate summary statistics?

Também podemos passar mais de uma coluna no `groupby`:

```python
In [11]: titanic.groupby(["Sex", "Pclass"])["Fare"].mean()
Out[11]: 
Sex     Pclass
female  1         106.125798
        2          21.970121
        3          16.118810
male    1          67.226127
        2          19.741782
        3          12.661633
Name: Fare, dtype: float64
```

**Value_counts & Count**:

Podemos utilizar o método `value_counts` para calcular a quantidade de vezes que se repete um valor em uma coluna:

```python
In [12]: titanic["Pclass"].value_counts()
Out[12]: 
3    491
1    216
2    184
Name: Pclass, dtype: int64
```

Value_counts nada mais é do que um atalho para a operação de `groupby + count`:

```python
In [13]: titanic.groupby("Pclass")["Pclass"].count()
Out[13]: 
Pclass
1    216
2    184
3    491
Name: Pclass, dtype: int64
```

> Nota: Ambos size e count podem ser combinados com groupby, onde size inclui valores NaN e informa o número de linhas total, já count exclui todos valores faltantes. Em value_count podemos passar o argumento `dropna` para incluir valores NaN.
>
> Pandas Docs - How to calculate summary statistics?



# How to reshape the layout of tables?

**Sort table rows**:

Digamos que você queira organizar o Dataframe com os passageiros do Titanic pela idade, do menor para o maior:

```python
In [6]: titanic.sort_values(by="Age").head()
Out[6]: 
     PassengerId  Survived  Pclass                             Name     Sex   Age  SibSp  Parch  Ticket     Fare Cabin Embarked
803          804         1       3  Thomas, Master. Assad Alexander    male  0.42      0      1    2625   8.5167   NaN        C
755          756         1       2        Hamalainen, Master. Viljo    male  0.67      1      1  250649  14.5000   NaN        S
644          645         1       3           Baclini, Miss. Eugenie  female  0.75      2      1    2666  19.2583   NaN        C
469          470         1       3    Baclini, Miss. Helene Barbara  female  0.75      2      1    2666  19.2583   NaN        C
78            79         1       2    Caldwell, Master. Alden Gates    male  0.83      0      2  248738  29.0000   NaN        S
```

Agora, vamos ordenar pela classe de cabine e idade, em ordem decrescente:

```python
In [7]: titanic.sort_values(by=['Pclass', 'Age'], ascending=False).head()
Out[7]: 
     PassengerId  Survived  Pclass                       Name     Sex   Age  SibSp  Parch  Ticket    Fare Cabin Embarked
851          852         0       3        Svensson, Mr. Johan    male  74.0      0      0  347060  7.7750   NaN        S
116          117         0       3       Connors, Mr. Patrick    male  70.5      0      0  370369  7.7500   NaN        Q
280          281         0       3           Duane, Mr. Frank    male  65.0      0      0  336439  7.7500   NaN        Q
483          484         1       3     Turkula, Mrs. (Hedwig)  female  63.0      0      0    4134  9.5875   NaN        S
326          327         0       3  Nysveen, Mr. Johan Hansen    male  61.0      0      0  345364  6.2375   NaN        S

```

**Long to wide table format**:

Primeiramente vamos começar separando o Dataframe de air quality em um pequeno subset. Vamos nos focar apenas nos parametros iguais a NO², e armazenar somente as duas primeiras medições de cada localização:

```python
# filter for no2 data only
In [8]: no2 = air_quality[air_quality["parameter"] == "no2"]
    
# use 2 measurements (head) for each location (groupby)
In [9]: no2_subset = no2.sort_index().groupby(["location"]).head(2)

In [10]: no2_subset
Out[10]: 
                                city country            location parameter  value   unit
date.utc                                                                                
2019-04-09 01:00:00+00:00  Antwerpen      BE             BETR801       no2   22.5  µg/m³
2019-04-09 01:00:00+00:00      Paris      FR             FR04014       no2   24.4  µg/m³
2019-04-09 02:00:00+00:00     London      GB  London Westminster       no2   67.0  µg/m³
2019-04-09 02:00:00+00:00  Antwerpen      BE             BETR801       no2   53.5  µg/m³
2019-04-09 02:00:00+00:00      Paris      FR             FR04014       no2   27.4  µg/m³
2019-04-09 03:00:00+00:00     London      GB  London Westminster       no2   67.0  µg/m³

```

Agora vamos utilizar a função `pivot` para separar nosso subset em apenas três colunas, das três respectivas estações. Vale lembrar que essa função meramente reestrutura nosso Dataframe. Para isso, passaremos como parâmetros:

* `columns="location"`: Para informar qual coluna deve ser levada em consideração para os dados.
* `values="value"`: Para informar qual coluna deve ser utilizada para os resultados de cada coluna resultante.

```python
In [11]: no2_subset.pivot(columns="location", values="value")
Out[11]: 
location                   BETR801  FR04014  London Westminster
date.utc                                                       
2019-04-09 01:00:00+00:00     22.5     24.4                 NaN
2019-04-09 02:00:00+00:00     53.5     27.4                67.0
2019-04-09 03:00:00+00:00      NaN      NaN                67.0
```

Também podemos transformar nosso pivot diretamente em um gráfico com `plot`:

```python
In [13]: no2.pivot(columns="location", values="value").plot()
Out[13]: <AxesSubplot:xlabel='date.utc'>
```

**Wide to long format**:

```python
In [16]: no2_pivoted = no2.pivot(columns="location", values="value").reset_index()

In [17]: no2_pivoted.head()
Out[17]: 
location                  date.utc  BETR801  FR04014  London Westminster
0        2019-04-09 01:00:00+00:00     22.5     24.4                 NaN
1        2019-04-09 02:00:00+00:00     53.5     27.4                67.0
2        2019-04-09 03:00:00+00:00     54.5     34.2                67.0
3        2019-04-09 04:00:00+00:00     34.5     48.5                41.0
4        2019-04-09 05:00:00+00:00     46.5     59.5                41.0
```

Digamos agora que você queira coletar todas as medições de NO² em uma única coluna. Para isso, podemos utilizar outra função cujo o objetivo principal é reestruturar nosso Dataframe, o `melt`:

```python
In [20]: no_2 = no2_pivoted.melt(
   ....:     id_vars="date.utc",
   ....:     value_vars=["BETR801", "FR04014", "London Westminster"],
   ....:     value_name="NO_2",
   ....:     var_name="id_location",
   ....: )

In [21]: no_2.head()
Out[21]: 
                   date.utc id_location  NO_2
0 2019-04-09 01:00:00+00:00     BETR801  22.5
1 2019-04-09 02:00:00+00:00     BETR801  53.5
2 2019-04-09 03:00:00+00:00     BETR801  54.5
3 2019-04-09 04:00:00+00:00     BETR801  34.5
4 2019-04-09 05:00:00+00:00     BETR801  46.5
```

* `id_vars`: Informa qual coluna será utilizada como identificadora, as outras que não foram passadas aqui serão "derretidas".
* `value_vars`: Define explicitamente qual colunas serão "derretidas" juntas.
* `value_name`: Providencia um nome personalizado para a coluna resultante.
* `var_name`:  Providencia um nome personalizado para a coluna responsável por agrupar o nome dos valores "derretidos".



# How to combine data from multiple tables?

Para unir dois Dataframes podemos utilizar o método `concat`:

```python
In [8]: air_quality = pd.concat([air_quality_pm25, air_quality_no2], axis=0)

In [9]: air_quality.head()
Out[9]: 
                    date.utc location parameter  value
0  2019-06-18 06:00:00+00:00  BETR801      pm25   18.0
1  2019-06-17 08:00:00+00:00  BETR801      pm25    6.5
2  2019-06-17 07:00:00+00:00  BETR801      pm25   18.5
3  2019-06-17 06:00:00+00:00  BETR801      pm25   16.0
4  2019-06-17 05:00:00+00:00  BETR801      pm25    7.5
```

Por padrão no método `concat` caso existam duas colunas com o mesmo nome, elas serão simplesmente unificadas.
