# PySpark

## Prerequisitos

1. Hadoop 3.3.0
2. OpenJDK e JRE 8 ou superior

## Instalação manual dos pacotes...

- Instalando o Scala

```

wget https://downloads.lightbend.com/scala/2.13.3/scala-2.13.3.tgz

tar xvf scala-2.13.3.tgz

```

>adicionar Scala e um alias para o jupyter notebook no bashrc
>```
>nano ~/.bashrc
>```
>adicione esse comandos no final do arquivo
>alias jupyter-notebook=”~/.local/bin/jupyter-notebook — no-browser”
>export SCALA_HOME=/home/`nome do root user`/scala-2.13.3
>export PATH=$PATH:$SCALA_HOME/bin
>ctrl + o para salvar e ctrl + x para fechar
>execute o arquivo
>```
>source ~/.bashrc
>```

- Instalando Spark com Hadoop

```
wget https://archive.apache.org/dist/spark/spark-3.1.1/spark-3.1.1-bin-hadoop3.2.tgz
```
```
tar xvf spark-3.1.1-bin-hadoop3.2.tgz
```



## Iniciando projeto com virtualenv e jupyter notebook

1. Instalando JDK e JRE

```
!sudo apt update
!sudo apt install default-jre
!sudo apt install default-jdk
!java -version
!javac -version
```
>É necessário ter Java 8 ou posterior instalado como JAVA_HOME 
>Caso estéja usando o JDK 11, 
>defina '-dio.netty.tryReflectionSetAccessible=true' para recursos relacionados ao Arrow

2. checar atualizações do python3 e pip

```
!sudo apt install python3 python3-pip ipython3
```

3. Crie uma virtal env

```
!python3 -m venv <nome_do_diretório>
```

4. Entre no diretório e execute

```
!cd <nome_do_diretório>
!source bin/activate
```

5. Intale jupyter py4j, pyspark e jupyter-notebook

```
!pip install notebook py4j pyspark 
```
>caso necessite de alguma biblioteca extra do pyspark use a variação
>
>```
>!pip install pyspark[nome_do_pacote] 
>```
>
>para plotar seus dados, você pode usar a lib plotly...
>
>```
>pip install pyspark[pandas_on_spark] plotly  
>```

>Caso necessite de outras versões do Hadoop
>Por padrão são usadas as libs Hadoop 3.3 e Hive 2.3
>```
>!PYSPARK_HADOOP_VERSION=2 pip install pyspark
>```
>```
>PYSPARK_HIVE_VERSION=2 pip install pyspark -v
>```
>tag -v para exibir o status do download
>Valores suportados em 'PYSPARK_HADOOP_VERSION'
>2: Apache Hadoop 2.7
>3: Apache Hadoop 3.3 (default)

6. Execute o notebook

```
!jupyter notebook 
```
>por padrão a porta de acesso para o recurso é 8888
>caso esteje usando wsl, copie e cole o link com o token de acesso no navegador de sua preferência
>acesse http://localhost:8888/ no navegador...

 ## Dependências PySpark
 
 |Dependências usadas | Versão mínima suportada | Observações|
 |---|---|---|
 | `pandas` | 1.0.5 | Opcional para o **Spark SQL** e obrigatória para o **pandas API Spark** |
 | `pyarrow` | 1.0.0 | Opcional para o **spark SQL** e obrigatória para o **pandas API Spark** |
 | `numpy` | 1.15 | Obrigatória para **API pandas** e **DataFrame-basedAPI** do **MLLib** |
 | `py4j` | 0.10.9.5 | Obrigatória |

## Links úteis

- [Guia Spark SQL, DataFrames e DataSets](https://spark.apache.org/docs/latest/sql-programming-guide.html)
- [Guia RDD Programming Guide](https://spark.apache.org/docs/latest/rdd-programming-guide.html#overview)
- [Guia Programação de Streaming estruturado](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)
- [Guia Programação em Streaming](https://spark.apache.org/docs/latest/streaming-programming-guide.html)
- [Guia da lib de ML (MLlib)](https://spark.apache.org/docs/latest/ml-guide.html)
- [Entendendo o funcionamento do PySpark](https://medium.com/data-hackers/entendo-funcionamento-do-pyspark-2b5ab4321ab9)
- [Spark with Python (PySpark) Tutorial For Beginners](https://sparkbyexamples.com/pyspark-tutorial/)

## Iniciando sessão Pyspark

Para ter acesso aos recursos do PySpark usamos a classe `SparkSession` para iniciar o cluter e a sessão.


```python
#!export SPARK_HOME='/MLOps/ETL_PySpark/ETL/lib/python3.8/site-packages/pyspark'
!export PYSPARK_PYTHON='/MLOps/ETL_PySpark/ETL/bin/python3.8'
#!export PYSPARK_DRIVER_PYTHON='/MLOps/ETL_PySpark/ETL/bin/jupyter'
#!export PYSPARK_DRIVER_PYTHON_OPTS='notebook'
#!export SPARK_LOCAL_IP=192.168.1.110
```


```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.getOrCreate()
```

    23/01/12 15:24:40 WARN Utils: Your hostname, DESKTOP-LI0TJ46 resolves to a loopback address: 127.0.1.1; using 192.168.1.110 instead (on interface eth0)
    23/01/12 15:24:40 WARN Utils: Set SPARK_LOCAL_IP if you need to bind to another address


    Setting default log level to "WARN".
    To adjust logging level use sc.setLogLevel(newLevel). For SparkR, use setLogLevel(newLevel).


    23/01/12 15:24:41 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable



## Resumo rápido sobre DataFrame

Para usar aplicações PySpark precisamos inicializar o ponto de entrada da lib usando o `SparkSession`

>Caso deseje execute via shell por meio do executável do pyspark, o shel cria automaticamente a sessão na variável spark para os usuários.

### Criando DataFrame

Use a função `pyspark.sql.SparkSession.createDataFrame` passando uma lista de listas, tuplas, difionários e `pyspark.sql.Row`, um `Pandas DataFrame` e um RDD que consiste em tal lista. essa função usa o `schema` para especificar o esquema do DataFrame. Quando omisso, o PySpark se encarrega de criar um esquema correspondente obtendo uma amostra dos dados.

Exemplo de criação de DataFrame usando a partir de uma lista de linhas:


```python
from datetime import datetime, date
import pandas as pd
from pyspark.sql import Row

df = spark.createDataFrame([
    Row(a=1, b=2., c='string1', d=date(2000, 1, 1), e=datetime(2000, 1, 1, 12, 0)),
    Row(a=2, b=3., c='string2', d=date(2000, 2, 1), e=datetime(2000, 1, 2, 12, 0)),
    Row(a=4, b=5., c='string3', d=date(2000, 3, 1), e=datetime(2000, 1, 3, 12, 0))
])
df.show()
```

                                                                                    

    +---+---+-------+----------+-------------------+
    |  a|  b|      c|         d|                  e|
    +---+---+-------+----------+-------------------+
    |  1|2.0|string1|2000-01-01|2000-01-01 12:00:00|
    |  2|3.0|string2|2000-02-01|2000-01-02 12:00:00|
    |  4|5.0|string3|2000-03-01|2000-01-03 12:00:00|
    +---+---+-------+----------+-------------------+
    


Criando DataFrame passando um `schema`


```python
df = spark.createDataFrame([
    (1, 2., 'string1', date(2000, 1, 1), datetime(2000, 1, 1, 12, 0)),
    (2, 3., 'string2', date(2000, 2, 1), datetime(2000, 1, 2, 12, 0)),
    (3, 4., 'string3', date(2000, 3, 1), datetime(2000, 1, 3, 12, 0))
], schema='a long, b double, c string, d date, e timestamp')
df.show()
```

    +---+---+-------+----------+-------------------+
    |  a|  b|      c|         d|                  e|
    +---+---+-------+----------+-------------------+
    |  1|2.0|string1|2000-01-01|2000-01-01 12:00:00|
    |  2|3.0|string2|2000-02-01|2000-01-02 12:00:00|
    |  3|4.0|string3|2000-03-01|2000-01-03 12:00:00|
    +---+---+-------+----------+-------------------+
    


Criando um DataFrame a partir de um DataFrame pandas


```python
import pandas as pd
import os
from pyspark.sql.functions import pandas_udf
from pyspark.sql import SparkSession

os.environ['PYSPARK_PYTHON'] = "./environment/bin/python"
os.environ["PYARROW_IGNORE_TIMEZONE"] = "1"
spark = SparkSession.builder.getOrCreate()

# Url de acesso ao dataset CSV
url = "https://query1.finance.yahoo.com/v7/finance/download/GOOG?period1=1582781719&period2=1614404119&interval=1d&events=history&includeAdjustedClose=true"
df = pd.read_csv(url)

pdf = pd.DataFrame(df)

pdf
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Adj Close</th>
      <th>Volume</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2020-02-27</td>
      <td>68.102997</td>
      <td>68.585197</td>
      <td>65.858498</td>
      <td>65.904503</td>
      <td>65.904503</td>
      <td>59566000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2020-02-28</td>
      <td>63.875000</td>
      <td>67.056999</td>
      <td>63.549999</td>
      <td>66.966499</td>
      <td>66.966499</td>
      <td>75782000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2020-03-02</td>
      <td>67.580498</td>
      <td>69.543503</td>
      <td>66.340752</td>
      <td>69.455498</td>
      <td>69.455498</td>
      <td>48630000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2020-03-03</td>
      <td>69.971001</td>
      <td>70.507500</td>
      <td>66.599998</td>
      <td>67.069504</td>
      <td>67.069504</td>
      <td>48046000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2020-03-04</td>
      <td>67.961502</td>
      <td>69.404503</td>
      <td>67.155502</td>
      <td>69.325996</td>
      <td>69.325996</td>
      <td>38266000</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>248</th>
      <td>2021-02-22</td>
      <td>103.349998</td>
      <td>104.570999</td>
      <td>103.107002</td>
      <td>103.244003</td>
      <td>103.244003</td>
      <td>27350000</td>
    </tr>
    <tr>
      <th>249</th>
      <td>2021-02-23</td>
      <td>101.250504</td>
      <td>104.100502</td>
      <td>100.100998</td>
      <td>103.542999</td>
      <td>103.542999</td>
      <td>33348000</td>
    </tr>
    <tr>
      <th>250</th>
      <td>2021-02-24</td>
      <td>102.091499</td>
      <td>105.039001</td>
      <td>101.906502</td>
      <td>104.758499</td>
      <td>104.758499</td>
      <td>24966000</td>
    </tr>
    <tr>
      <th>251</th>
      <td>2021-02-25</td>
      <td>103.372498</td>
      <td>104.744003</td>
      <td>101.064499</td>
      <td>101.568001</td>
      <td>101.568001</td>
      <td>36568000</td>
    </tr>
    <tr>
      <th>252</th>
      <td>2021-02-26</td>
      <td>102.526001</td>
      <td>103.550499</td>
      <td>100.803001</td>
      <td>101.843002</td>
      <td>101.843002</td>
      <td>41670000</td>
    </tr>
  </tbody>
</table>
<p>253 rows × 7 columns</p>
</div>




```python
# Criando um DataFrame do Spark a partir um arquivo CSV online
# Ativando cabeçalho (header=True) e adicionando inferencia de schema automática(inferSchema=True)
#spark_df = spark.read.csv(url, header=True, inferSchema=True)
import pyspark.pandas as ps

psdf = ps.DataFrame(
    {'a': [1, 2, 3, 4, 5, 6],
     'b': [100, 200, 300, 400, 500, 600],
     'c': ["one", "two", "three", "four", "five", "six"]},
    index=[10, 20, 30, 40, 50, 60])
psdf
```

    /home/luizpibo/MLOps/ETL_PySpark/ETL/lib/python3.8/site-packages/pyspark/pandas/internal.py:1573: FutureWarning: iteritems is deprecated and will be removed in a future version. Use .items instead.
      fields = [
    /home/luizpibo/MLOps/ETL_PySpark/ETL/lib/python3.8/site-packages/pyspark/sql/pandas/conversion.py:604: FutureWarning: iteritems is deprecated and will be removed in a future version. Use .items instead.
      [(c, t) for (_, c), t in zip(pdf_slice.iteritems(), arrow_types)]
                                                                                    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10</th>
      <td>1</td>
      <td>100</td>
      <td>one</td>
    </tr>
    <tr>
      <th>20</th>
      <td>2</td>
      <td>200</td>
      <td>two</td>
    </tr>
    <tr>
      <th>30</th>
      <td>3</td>
      <td>300</td>
      <td>three</td>
    </tr>
    <tr>
      <th>40</th>
      <td>4</td>
      <td>400</td>
      <td>four</td>
    </tr>
    <tr>
      <th>50</th>
      <td>5</td>
      <td>500</td>
      <td>five</td>
    </tr>
    <tr>
      <th>60</th>
      <td>6</td>
      <td>600</td>
      <td>six</td>
    </tr>
  </tbody>
</table>
</div>




```python
import pyspark.pandas as ps
# Url de acesso ao dataset CSV
url = "https://query1.finance.yahoo.com/v7/finance/download/GOOG?period1=1582781719&period2=1614404119&interval=1d&events=history&includeAdjustedClose=true"

df = pd.read_csv(url)
pdf = pd.DataFrame(df)

#pyspark_dataframe = ps.read_csv(url)

pdf
#pyspark_dataframe
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Adj Close</th>
      <th>Volume</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2020-02-27</td>
      <td>68.102997</td>
      <td>68.585197</td>
      <td>65.858498</td>
      <td>65.904503</td>
      <td>65.904503</td>
      <td>59566000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2020-02-28</td>
      <td>63.875000</td>
      <td>67.056999</td>
      <td>63.549999</td>
      <td>66.966499</td>
      <td>66.966499</td>
      <td>75782000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2020-03-02</td>
      <td>67.580498</td>
      <td>69.543503</td>
      <td>66.340752</td>
      <td>69.455498</td>
      <td>69.455498</td>
      <td>48630000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2020-03-03</td>
      <td>69.971001</td>
      <td>70.507500</td>
      <td>66.599998</td>
      <td>67.069504</td>
      <td>67.069504</td>
      <td>48046000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2020-03-04</td>
      <td>67.961502</td>
      <td>69.404503</td>
      <td>67.155502</td>
      <td>69.325996</td>
      <td>69.325996</td>
      <td>38266000</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>248</th>
      <td>2021-02-22</td>
      <td>103.349998</td>
      <td>104.570999</td>
      <td>103.107002</td>
      <td>103.244003</td>
      <td>103.244003</td>
      <td>27350000</td>
    </tr>
    <tr>
      <th>249</th>
      <td>2021-02-23</td>
      <td>101.250504</td>
      <td>104.100502</td>
      <td>100.100998</td>
      <td>103.542999</td>
      <td>103.542999</td>
      <td>33348000</td>
    </tr>
    <tr>
      <th>250</th>
      <td>2021-02-24</td>
      <td>102.091499</td>
      <td>105.039001</td>
      <td>101.906502</td>
      <td>104.758499</td>
      <td>104.758499</td>
      <td>24966000</td>
    </tr>
    <tr>
      <th>251</th>
      <td>2021-02-25</td>
      <td>103.372498</td>
      <td>104.744003</td>
      <td>101.064499</td>
      <td>101.568001</td>
      <td>101.568001</td>
      <td>36568000</td>
    </tr>
    <tr>
      <th>252</th>
      <td>2021-02-26</td>
      <td>102.526001</td>
      <td>103.550499</td>
      <td>100.803001</td>
      <td>101.843002</td>
      <td>101.843002</td>
      <td>41670000</td>
    </tr>
  </tbody>
</table>
<p>253 rows × 7 columns</p>
</div>



    23/01/12 16:39:37 WARN HeartbeatReceiver: Removing executor driver with no recent heartbeats: 1370006 ms exceeds timeout 120000 ms
    23/01/12 16:39:37 WARN SparkContext: Killing executors is not supported by current scheduler.

