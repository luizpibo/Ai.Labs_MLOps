# PySpark

## Caso queira iniciar um projeto localmente usando o jupyter notebook e pyspark...

1. Crie uma virtal env

    ```
        python3 -m venv <nome_do_diretório>
    ```

2. Entre no diretório e execute

    ```
        cd <nome_do_diretório>
        source bin/activate
    ```

3. dentro do bash da venv, instale o jupyter

    ```
         pip install jupyter notebook
    ```

4. instale o pyspark
    
    ```
        pip install pyspark 
    ```
>caso necessite de alguma biblioteca extra do pyspark use a variação
>```
>pip install pyspark[nome_do_pacote] 
>```
>```
>para plotar seus dados, você pode usar a lib plotly...
>pip install pyspark[pandas_on_spark] plotly  

>Caso necessite de outras versões do Hadoop
>Por padrão são usadas as libs Hadoop 3.3 e Hive 2.3
>```
>PYSPARK_HADOOP_VERSION=2 pip install pyspark
>```
>```
>PYSPARK_HIVE_VERSION=2 pip install pyspark -v
>```
>tag -v para exibir o status do download
>Valores suportados em 'PYSPARK_HADOOP_VERSION'
>2: Apache Hadoop 2.7
>3: Apache Hadoop 3.3 (default)
    
5. execute o jupyter

    ```
         jupyter notebook
    ```
    
>por padrão a porta de acesso para o recurso é 8888
>caso esteje usando wsl, copie e cole o link com o token de acesso no navegador de sua preferência
>acesse http://localhost:8888/ no navegador...
>
>É necessário ter Java 8 ou posterior instalado como JAVA_HOME 
>Caso estéja usando o JDK 11, 
>defina '-dio.netty.tryReflectionSetAccessible=true' para recursos relacionados ao Arrow

## Dependências PySpark

 |Dependências usadas | Versão mínima suportada | Observações|
 |---|---|---|
 | `pandas` | 1.0.5 | Opcional para o **Spark SQL** e obrigatória para o **pandas API Spark** |
 | `pyarrow` | 1.0.0 | Opcional para o **spark SQL** e obrigatória para o **pandas API Spark** |
 | `numpy` | 1.15 | Obrigatória para **API pandas** e **DataFrame-basedAPI** do **MLLib** |
 | `py4j` | 0.10.9.5 | Obrigatória |


```python
import pandas as pd
import numpy as np
import pyspark.pandas as ps
from pyspark.sql import SparkSession
```
