# Apache Spark

## Visão geral do Apache Spark

O Spark é um mecanismo de análise de processamento paralelo unificado para processamento de dados em larga escla. Ele oferece suporte ao processamento na memória para aumentar o desempenho de aplicativos que analisam big data. Forenece APIs de alto nível para Java, Scala, Python e um mecanismo otimizado que oferece suporte a gráficos de execução geral. Ele também oferece um conjunto de ferramentas auxiliares incluindo processamento de gráficos(GraphX), Processametno de dados estruturados (Spark SQL), compatibilidade com Pandas, para aprendizado de máquina como MLib e streaming estruturado para computação. Ela foi originalmente desenvolvida na Universidade de Berkeley em 2009. O framework Spark é 100% open source, hospedado no Apache Software Foundation independente de fornecedor. Ele usa o modelo de programação `MapReduce` usado pelo Hadoop e o modelo de programação estendido. Fornecendo uma perfomance muito superior ao Hadoop.

### Arquitetura do Spark

Para executar o conjunto de bibliotecas e ferramentas que acompanham o Spark ele útiliza três partes principais:

1. **Drive Program**: É quem faz a tradução, configuração e gestão dos comando passados para a API.
2. **Cluster Manager**: é um componentte opcional que só é necessário se o Spark for executado de forma distribuída.
3. **Workers**: São as máquinas que executarão as tarefas que são enviadas pelo Drive Program, caso o Spark seja executado localmente, a máquina desempenhará os papéis de Driver Program e Worker.
    
![Arquitetura Spark](https://sparkbyexamples.com/wp-content/uploads/2020/02/spark-cluster-overview.png) 
 
### Tipos de gerenciamento de Cluester
 
- **Autônomo**: um gerenciador de cluster simples incluído no Spark que facilita a configuração de um Cluster.
- **Mesos**: gerenciador de cluster que pode executar aplicativos Hadoop MapReduce e PySpark.
- **Hadoop YARN**: gerenciador de recursos no Hadoop 2. É o mais usado.
- **Kubernetes**: gerenciador de aplicativos em contêineres.

### Principais vantagens

- **Integração**: Ela possui recursos complexos, revelantes e a possibilidade de integração com ferramentas conhecidas com Apache Cassandra e Hadoop.
- **Quantidade de clusters**: Muitas organizações executam o Spark em clusters de milhares de nós. O maior aglomerado que conhecemos tem 8.000 deles. Em termos de tamanho de dados, o Spark demonstrou funcionar bem até petabytes.
- **API para linguagens de programação**: Suporta linguagens como Java, Python, R e Scal e SQL. 
- **Eficiência**: Ele foi planejato para tirar um consumo eficiente de recursos da máquina, ele é capaz de processar os dados de forma a minimizar a quantidade de dados que precisam ser lidos.
- **Ampla biblioteca de ferramentas**: Similar a outras bibliotecas para análise e processamento de dados, o Spark possui algumas bibliotecas para facilitar do desenvolvedores. processamento de gráficos(GraphX), Processametno de dados estruturados (Spark SQL), compatibilidade com Pandas, para aprendizado de máquina como MLib, streaming estruturado para computação(Spark Streaming).
- **Framework de código aberto**: O biblioteca inicialmente comeu a ser desenvolvida no AMPLab e posteriormente é mantido pela Apache Software.
- **Velocidade**: Ela é capaz de processar grandes quantidades de dados em paralelo e processamento na memória.

### Algumas Desvantagens

- **Overhead de memória**: Como ele faz processamento em memória, a quantidade de memória ram que seus jobs utilizam pode ser um problema gerenciar o consumo no cluster, mas caso o volume de dados ultrapassar a quantidade de memória a plataforma começa a usar o armazenamento em disco por padrão, causando mais **Latência de E/S**.
- **Latência de E/S**: o Apache Spark pode sofrer com latência de E/S (entrada/saída) em alguns casos, especialmente quando os dados precisam ser lidos de armazenamento secundário.

## Ecossistema

O Apache Spark se integra com diversas bibliotecas que podem te auxiliar na integração com outros serviços.

- **Data science e Machine learning**: Scikit learn, PyTorch, pandas, TTensorFlow, mlFlow, R.
- **SQL analytics and BI**: Apache Superset, Power BI, Loocker, Re dash, Tableau, dbt, 
- **Infra e armazenamento**: Elasticsearch, MongoDB, Apache kafka, Delta Lake, Kubernetes, Apache Airflow, Parquet, SQL Server, cassandra, Apache ORC.

## Instalação

### Prerequisitos

1. Hadoop 3.3.0
2. OpenJDK e JRE 8 ou superior

### Instalação manual dos pacotes...

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



### Iniciando projeto com virtualenv e jupyter notebook

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

    23/01/16 16:04:48 WARN Utils: Your hostname, DESKTOP-LI0TJ46 resolves to a loopback address: 127.0.1.1; using 192.168.1.110 instead (on interface eth0)
    23/01/16 16:04:48 WARN Utils: Set SPARK_LOCAL_IP if you need to bind to another address


    Setting default log level to "WARN".
    To adjust logging level use sc.setLogLevel(newLevel). For SparkR, use setLogLevel(newLevel).


    23/01/16 16:04:51 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable


>Após iniciarmos a sessão, é possível monitorar os recursos do spark.
>Executando o Spark localmente a url de acesso a esse recurso é `localhost:4040`.
>Caso necessário é possível mudar o ip e porta de acesso.


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
    


### Criando DataFrame passando um `schema`


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
    


### Usando o pyspark.pandas para criar um DataFrame


```python
import pyspark.pandas as ps

psdf = ps.DataFrame(
    {'a': [1, 2, 3, 4, 5, 6],
     'b': [100, 200, 300, 400, 500, 600],
     'c': ["one", "two", "three", "four", "five", "six"]},
    index=[10, 20, 30, 40, 50, 60])
psdf
```

    WARNING:root:'PYARROW_IGNORE_TIMEZONE' environment variable was not set. It is required to set this environment variable to '1' in both driver and executor sides if you use pyarrow>=2.0.0. pandas-on-Spark will set it for you but it does not work if there is a Spark context already launched.
    /home/luizpibo/MLOps/ETL_PySpark/ETL/lib/python3.8/site-packages/pyspark/pandas/internal.py:1573: FutureWarning: iteritems is deprecated and will be removed in a future version. Use .items instead.
      fields = [
    /home/luizpibo/MLOps/ETL_PySpark/ETL/lib/python3.8/site-packages/pyspark/sql/pandas/conversion.py:486: FutureWarning: iteritems is deprecated and will be removed in a future version. Use .items instead.
      for column, series in pdf.iteritems():





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



## Referências

- [APACHE. Apache Spark, 2022. Visão geral do Spark](https://spark.apache.org/docs/latest/)
- [Microsoft, O que é o Apache Spark?](https://learn.microsoft.com/pt-br/dotnet/spark/what-is-spark)
- [Cetax, Spark: Saiba mais sobre esse poderoso framework](https://cetax.com.br/conheca-mais-sobre-o-framework-apache-spark/)
- [Spark: entenda sua função e saiba mais sobre essa ferramenta.](https://blog.xpeducacao.com.br/apache-spark/)
- [Guia Spark SQL, DataFrames e DataSets](https://spark.apache.org/docs/latest/sql-programming-guide.html)
- [Guia RDD Programming Guide](https://spark.apache.org/docs/latest/rdd-programming-guide.html)
- [Guia Programação de Streaming estruturado](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)
- [Guia Programação em Streaming](https://spark.apache.org/docs/latest/streaming-programming-guide.html)
- [Guia da lib de ML (MLlib)](https://spark.apache.org/docs/latest/ml-guide.html)
- [Entendendo o funcionamento do PySpark](https://medium.com/data-hackers/entendo-funcionamento-do-pyspark-2b5ab4321ab9)
- [Spark with Python (PySpark) Tutorial For Beginners](https://sparkbyexamples.com/pyspark-tutorial/)
- [Perguntas frequentes Do Apache Spark ™](https://spark.apache.org/faq.html)
- [Uma breve introdução do Hadoop HDFS — Hadoop Distributed File System [1|2]](https://medium.com/@cm.oeiras01/uma-breve-introdu%C3%A7%C3%A3o-do-hadoop-hdfs-hadoop-distributed-file-system-1-2-6883710ea64f)
