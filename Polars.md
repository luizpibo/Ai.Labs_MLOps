# Polars

![Logo Polars](https://warehouse-camo.ingress.cmh1.psfhosted.org/6ee1fcdd8ef06e75b8a1d0bedceaa9faad6c093a/68747470733a2f2f7261772e67697468756275736572636f6e74656e742e636f6d2f706f6c612d72732f706f6c6172732d7374617469632f6d61737465722f6c6f676f732f706f6c6172735f6769746875625f6c6f676f5f726563745f6461726b5f6e616d652e737667)

## Visão geral

Polars é uma biblioteca para criação e tratamento de DataFrames que é implementada em Rust e usa [Apache Arrow](https://arrow.apache.org/docs/format/Columnar.html) como modelo de memória, ela possui APIs escritas em Python e Rust, essas APIs servem apenas como uma camada superior que é interpretada e executada na bilbioteca principal.

Essa lib foi criada pensando em resolver alguns problemas que outras libs famosas em python possuem:
- Tempo de E/S.
- Processamento multithreading.
- Armazenamento orientado a colunas.
- Algoritmos eficientes.
- Otimizações de consultas.

Alguns Benchmarks mostram que essa ferramenta consegue ser mais rápida que as libs `Pandas` e `Dask`.
Esse melhor desempenho é relacionado ao melhor gerenciamento de recursos do computador por lado da lib, possuir um otimizador de código que valida e faz a troca da sequência que as instruções são executadas quando necessário, por usar um modelo de dado colunar criado e gerenciado pelo Apache Arrow e ter sido escrita em Rust.

O criador da lib [Ritchie Vink](https://www.ritchievink.com/) diz ter começado a escrever-la durante o começo da pandemia e ela foi lançada em agosto de 2021, para saber mais sobre a história acesse o post ['I wrote one of the fastest DataFrame libraries'](https://www.ritchievink.com/blog/2021/02/28/i-wrote-one-of-the-fastest-dataframe-libraries/).

![Benchmarks](https://www.ritchievink.com/img/post-35-polars-0.15/db-benchmark.png)

[link para o Benchmark](https://www.pola.rs/benchmarks.html)

## Vantagens

- Ela possui uma sintaxe um pouco complicada de início mas após de algum tempo de prática fica fácil de entender as transformações e fluxos. 
- Ela foi planejada para rodar seus scripts em um nível de abstração inferior que outras ferramentas escritas em python, isso ocasiona uma maior rapidez nas transformações e carregamento dos dados na memória e processamento. 
- Creio que a escolha em utilizar Apache Arrow no core foi essencial para a performance.
- Dois diferentes tipos de execução do código (Lazy|eager).
- Processamento multi-threaded.
- Otimizador de Querys.
- Modo híbrido para carregamento de dados na memória.

## Desvantagens

Essa lib aparenta ter um grande potencial e se mostra promissora quando precisamos fazer processamentos de batches em memória, mas dependendo do volume de dados precisamos ter uma melhor administração do código e recusos para que ela funciona corretamente, outro ponto negativo é a idade da ferramenta, ela começou a ser escrita por uma pessoa e ainda não completou 5 anos.

## Instalando o Py-polars

Vamos usar a API em Python para esse artigo... Primeiro precisamos criar uma virtualenv, iniciar a venv e depois execute o comando a seguir para instalar a versão 0.15.15 do Polars.


```python
$pip install polars==0.15.15
```

## Exemplo de utilização

### Resumo sobre o teste...

Esse exemplo foi retirado do blog oficial do Polars, explicando resumidamente ele usa a base de dados [conjunto de dados de avatar do World of Warcraft](https://www.kaggle.com/mylesoneill/warcraft-avatar-history), o problema que vamos resolver persiste em elaborar uma análise onde se um personagem for visto jogando por 36 horas sem interrupção, pode-se argumentar que pode haver um bot. O objetivo final é remover bots do conjunto de dados como uma etapa de pré-processamento para outras análises. [Para entender mais sobre a estratégia que será usada](https://www.pola.rs/posts/the-expressions-api-in-polars-is-amazing/).

|char|level|race|charclass|zone|timestamp|
|---|---|---|---|---|---|
|2|12|Orc|Shaman|The Barrens|03/12/2008 10:41:47|
|7|55|Orc|Hunter|O Templo de Atal'Hakkar|15/01/2008 23:37:25|
|7|55|Orc|Hunter|O Templo de Atal'Hakkar|2008-01-15 23:47:09|
|7|55|Orc|Hunter|Orgrimmar|2008-01-15 23:56:52|
|7|55|Orc|Hunter|Orgrimmar|2008-01-16 00:07:28|

>É um conjunto de dados que contém logs de um servidor de World of Warcraft de 2008. A cada 10 minutos, o sistema registraria todos os jogadores da facção Horde se eles estivessem jogando.

Antes de implementar o código é bom ter um algoritmo que exemplifica quais as colunas novas precisamos criar e qual seram as etapas para essa análise.

1. Precisamos criar uma coluna session_id que conterá o id que representa a sessão atual do jogador, caso ele esteja em outra sessão, adicionaremos um novo session_id para esse jogador.
2. Dada a coluna session_id, podemos calcular quanto tempo durou a sessão.
3. Dado o tempo de jogo de cada sessão, podemos olhar a sessão mais longa para cada jogador, se exceder um limite como 24hrs, removeremos todas as atividades desse usuário da tabela.


```python
#Importando o Polars
import polars as pl

#Lendo CSV
df = pl.read_csv("wowah_data.csv", parse_dates=False)
#Removendo espaços em branco antes do título de cada coluna
df.columns = [c.replace(" ", "") for c in df.columns]
#Determinando modo lazy para o carregamento dos dados
df = df.lazy()
```


```python
#Função responsável por mudar o tipo de dado das colunas timestamp e guild
#Timestamp para o formato de datetime do polars
#Guild para boolean
def set_types(dataf):
    return (dataf
            .with_columns(
                [pl.col("timestamp")
                 .str.strptime(
                     pl.Datetime,
                     fmt="%m/%d/%y %H:%M:%S"
                 ),
                 pl.col("guild") != -1,]
            )
           )
```


```python
def sessionize(dataf, threshold=20 * 60 * 1_000):
    return (dataf
            .with_columns([
                (pl.col("timestamp").diff().cast(pl.Int64) > threshold).fill_null(True).alias("ts_diff"), #cria coluna que determina se houve um salto de valores do times stamp que justifique uma nova sessão.  
                (pl.col("char").diff() != 0).fill_null(True).alias("char_diff"), #cria coluna para determinar se há outras aparições do mesmo usuário.
            ])
            .with_columns([
                (pl.col("ts_diff") | pl.col("char_diff")).alias("new_session_mark") #cria coluna que faz uma opreção or entre as colunas criadas anteriormente para validar qual dos logs é uma nova sessão.
            ])
            .with_columns([
                pl.col("new_session_mark").cumsum().alias("session") #Detemina novas sessões
            ])
            .drop(['char_diff', 'ts_diff', 'new_session_mark'])) #Exclui colunas desnecessárias.
```

># Poque tantas with_columns?
>Isso ocorre poque no momento que post foi escrito, as colunas precisam existir antes de usar a expressão dentro de uma >`.with_columns` 
>Normalmente são agrupadas tantas expressões quanto possível em uma única `with_colums`, porque cada expressão é executada em >uma thread separada. Se usar `.with_column` sequencialmente, ocorre o risco de o otimizador não reconhecer que o comando pode >ser executado em paralelo.


```python
def add_features(dataf):
    return (dataf
        .with_columns([
            pl.col("char").count().over("session").alias("session_length"), #Número de seções
            pl.col("session").n_unique().over("char").alias("n_sessions") #Criando idencificador para cada nova sessão de um usuário
        ]))
```


```python
def remove_bots(dataf, max_session_hours=24):
    n_rows = max_session_hours * 6
    return (dataf
            .filter(pl.col("session_length").max().over("char") < n_rows)) #Checando se o número de sessões ultrapassa o limite
```


```python
#Determinando fluxo de execução das funções
#20 minutos
threshold = 20 * 60 * 1000

df_intermediate = (df
                   .sort(["char","timestamp"])
                   .pipe(set_types)
                   .pipe(sessionize, threshold=20 * 60 * 1000)
                   .pipe(add_features)
                  )

df_intermediate.collect()
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

    .dataframe td {
        white-space: pre;
    }

    .dataframe td {
        padding-top: 0;
    }

    .dataframe td {
        padding-bottom: 0;
    }

    .dataframe td {
        line-height: 95%;
    }
</style>
<table border="1" class="dataframe">
<small>shape: (10826734, 10)</small>
<thead>
<tr>
<th>
char
</th>
<th>
level
</th>
<th>
race
</th>
<th>
charclass
</th>
<th>
zone
</th>
<th>
guild
</th>
<th>
timestamp
</th>
<th>
session
</th>
<th>
session_length
</th>
<th>
n_sessions
</th>
</tr>
<tr>
<td>
i64
</td>
<td>
i64
</td>
<td>
str
</td>
<td>
str
</td>
<td>
str
</td>
<td>
bool
</td>
<td>
datetime[μs]
</td>
<td>
u32
</td>
<td>
u32
</td>
<td>
u32
</td>
</tr>
</thead>
<tbody>
<tr>
<td>
2
</td>
<td>
18
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Shaman&quot;
</td>
<td>
&quot;The Barrens&quot;
</td>
<td>
true
</td>
<td>
2008-12-03 10:41:47
</td>
<td>
1
</td>
<td>
1
</td>
<td>
1
</td>
</tr>
<tr>
<td>
7
</td>
<td>
54
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Hunter&quot;
</td>
<td>
&quot;Feralas&quot;
</td>
<td>
false
</td>
<td>
2008-01-15 21:47:09
</td>
<td>
2
</td>
<td>
1
</td>
<td>
655
</td>
</tr>
<tr>
<td>
7
</td>
<td>
54
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Hunter&quot;
</td>
<td>
&quot;Un&#x27;Goro Crater...
</td>
<td>
false
</td>
<td>
2008-01-15 21:56:54
</td>
<td>
3
</td>
<td>
1
</td>
<td>
655
</td>
</tr>
<tr>
<td>
7
</td>
<td>
54
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Hunter&quot;
</td>
<td>
&quot;The Barrens&quot;
</td>
<td>
false
</td>
<td>
2008-01-15 22:07:23
</td>
<td>
4
</td>
<td>
1
</td>
<td>
655
</td>
</tr>
<tr>
<td>
7
</td>
<td>
54
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Hunter&quot;
</td>
<td>
&quot;Badlands&quot;
</td>
<td>
false
</td>
<td>
2008-01-15 22:17:08
</td>
<td>
5
</td>
<td>
1
</td>
<td>
655
</td>
</tr>
<tr>
<td>
7
</td>
<td>
54
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Hunter&quot;
</td>
<td>
&quot;Badlands&quot;
</td>
<td>
false
</td>
<td>
2008-01-15 22:26:52
</td>
<td>
6
</td>
<td>
1
</td>
<td>
655
</td>
</tr>
<tr>
<td>
7
</td>
<td>
54
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Hunter&quot;
</td>
<td>
&quot;Badlands&quot;
</td>
<td>
false
</td>
<td>
2008-01-15 22:37:25
</td>
<td>
7
</td>
<td>
1
</td>
<td>
655
</td>
</tr>
<tr>
<td>
7
</td>
<td>
54
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Hunter&quot;
</td>
<td>
&quot;Swamp of Sorro...
</td>
<td>
true
</td>
<td>
2008-01-15 22:47:10
</td>
<td>
8
</td>
<td>
1
</td>
<td>
655
</td>
</tr>
<tr>
<td>
7
</td>
<td>
54
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Hunter&quot;
</td>
<td>
&quot;The Temple of ...
</td>
<td>
true
</td>
<td>
2008-01-15 22:56:53
</td>
<td>
9
</td>
<td>
1
</td>
<td>
655
</td>
</tr>
<tr>
<td>
7
</td>
<td>
54
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Hunter&quot;
</td>
<td>
&quot;The Temple of ...
</td>
<td>
true
</td>
<td>
2008-01-15 23:07:25
</td>
<td>
10
</td>
<td>
1
</td>
<td>
655
</td>
</tr>
<tr>
<td>
7
</td>
<td>
54
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Hunter&quot;
</td>
<td>
&quot;The Temple of ...
</td>
<td>
true
</td>
<td>
2008-01-15 23:17:09
</td>
<td>
11
</td>
<td>
1
</td>
<td>
655
</td>
</tr>
<tr>
<td>
7
</td>
<td>
55
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Hunter&quot;
</td>
<td>
&quot;The Temple of ...
</td>
<td>
true
</td>
<td>
2008-01-15 23:26:53
</td>
<td>
12
</td>
<td>
1
</td>
<td>
655
</td>
</tr>
<tr>
<td>
...
</td>
<td>
...
</td>
<td>
...
</td>
<td>
...
</td>
<td>
...
</td>
<td>
...
</td>
<td>
...
</td>
<td>
...
</td>
<td>
...
</td>
<td>
...
</td>
</tr>
<tr>
<td>
90575
</td>
<td>
2
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Hunter&quot;
</td>
<td>
&quot;Orgrimmar&quot;
</td>
<td>
false
</td>
<td>
2008-12-31 21:14:51
</td>
<td>
10823166
</td>
<td>
1
</td>
<td>
2
</td>
</tr>
<tr>
<td>
90576
</td>
<td>
2
</td>
<td>
&quot;Blood Elf&quot;
</td>
<td>
&quot;Warlock&quot;
</td>
<td>
&quot;Eversong Woods...
</td>
<td>
false
</td>
<td>
2008-12-31 22:06:58
</td>
<td>
10823167
</td>
<td>
1
</td>
<td>
5
</td>
</tr>
<tr>
<td>
90576
</td>
<td>
3
</td>
<td>
&quot;Blood Elf&quot;
</td>
<td>
&quot;Warlock&quot;
</td>
<td>
&quot;Eversong Woods...
</td>
<td>
false
</td>
<td>
2008-12-31 22:17:35
</td>
<td>
10823168
</td>
<td>
1
</td>
<td>
5
</td>
</tr>
<tr>
<td>
90576
</td>
<td>
3
</td>
<td>
&quot;Blood Elf&quot;
</td>
<td>
&quot;Warlock&quot;
</td>
<td>
&quot;Eversong Woods...
</td>
<td>
false
</td>
<td>
2008-12-31 22:32:52
</td>
<td>
10823169
</td>
<td>
1
</td>
<td>
5
</td>
</tr>
<tr>
<td>
90576
</td>
<td>
4
</td>
<td>
&quot;Blood Elf&quot;
</td>
<td>
&quot;Warlock&quot;
</td>
<td>
&quot;Eversong Woods...
</td>
<td>
false
</td>
<td>
2008-12-31 22:47:54
</td>
<td>
10823170
</td>
<td>
1
</td>
<td>
5
</td>
</tr>
<tr>
<td>
90576
</td>
<td>
5
</td>
<td>
&quot;Blood Elf&quot;
</td>
<td>
&quot;Warlock&quot;
</td>
<td>
&quot;Eversong Woods...
</td>
<td>
false
</td>
<td>
2008-12-31 23:07:13
</td>
<td>
10823171
</td>
<td>
1
</td>
<td>
5
</td>
</tr>
<tr>
<td>
90577
</td>
<td>
1
</td>
<td>
&quot;Blood Elf&quot;
</td>
<td>
&quot;Warlock&quot;
</td>
<td>
&quot;Eversong Woods...
</td>
<td>
false
</td>
<td>
2008-12-31 22:17:35
</td>
<td>
10823172
</td>
<td>
1
</td>
<td>
3
</td>
</tr>
<tr>
<td>
90577
</td>
<td>
2
</td>
<td>
&quot;Blood Elf&quot;
</td>
<td>
&quot;Warlock&quot;
</td>
<td>
&quot;Eversong Woods...
</td>
<td>
false
</td>
<td>
2008-12-31 22:32:52
</td>
<td>
10823173
</td>
<td>
1
</td>
<td>
3
</td>
</tr>
<tr>
<td>
90577
</td>
<td>
3
</td>
<td>
&quot;Blood Elf&quot;
</td>
<td>
&quot;Warlock&quot;
</td>
<td>
&quot;Eversong Woods...
</td>
<td>
false
</td>
<td>
2008-12-31 22:47:54
</td>
<td>
10823174
</td>
<td>
1
</td>
<td>
3
</td>
</tr>
<tr>
<td>
90578
</td>
<td>
1
</td>
<td>
&quot;Blood Elf&quot;
</td>
<td>
&quot;Paladin&quot;
</td>
<td>
&quot;Eversong Woods...
</td>
<td>
false
</td>
<td>
2008-12-31 22:32:52
</td>
<td>
10823175
</td>
<td>
1
</td>
<td>
1
</td>
</tr>
<tr>
<td>
90579
</td>
<td>
1
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Warrior&quot;
</td>
<td>
&quot;Durotar&quot;
</td>
<td>
false
</td>
<td>
2008-12-31 22:44:45
</td>
<td>
10823176
</td>
<td>
1
</td>
<td>
1
</td>
</tr>
<tr>
<td>
90580
</td>
<td>
1
</td>
<td>
&quot;Tauren&quot;
</td>
<td>
&quot;Warrior&quot;
</td>
<td>
&quot;Mulgore&quot;
</td>
<td>
false
</td>
<td>
2008-12-31 23:15:20
</td>
<td>
10823177
</td>
<td>
1
</td>
<td>
1
</td>
</tr>
</tbody>
</table>
</div>




```python
#Removendo os bots apos a execução do pipeline anterior
df_intermediate.pipe(remove_bots).collect()
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

    .dataframe td {
        white-space: pre;
    }

    .dataframe td {
        padding-top: 0;
    }

    .dataframe td {
        padding-bottom: 0;
    }

    .dataframe td {
        line-height: 95%;
    }
</style>
<table border="1" class="dataframe">
<small>shape: (10826734, 10)</small>
<thead>
<tr>
<th>
char
</th>
<th>
level
</th>
<th>
race
</th>
<th>
charclass
</th>
<th>
zone
</th>
<th>
guild
</th>
<th>
timestamp
</th>
<th>
session
</th>
<th>
session_length
</th>
<th>
n_sessions
</th>
</tr>
<tr>
<td>
i64
</td>
<td>
i64
</td>
<td>
str
</td>
<td>
str
</td>
<td>
str
</td>
<td>
bool
</td>
<td>
datetime[μs]
</td>
<td>
u32
</td>
<td>
u32
</td>
<td>
u32
</td>
</tr>
</thead>
<tbody>
<tr>
<td>
2
</td>
<td>
18
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Shaman&quot;
</td>
<td>
&quot;The Barrens&quot;
</td>
<td>
true
</td>
<td>
2008-12-03 10:41:47
</td>
<td>
1
</td>
<td>
1
</td>
<td>
1
</td>
</tr>
<tr>
<td>
7
</td>
<td>
54
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Hunter&quot;
</td>
<td>
&quot;Feralas&quot;
</td>
<td>
false
</td>
<td>
2008-01-15 21:47:09
</td>
<td>
2
</td>
<td>
1
</td>
<td>
655
</td>
</tr>
<tr>
<td>
7
</td>
<td>
54
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Hunter&quot;
</td>
<td>
&quot;Un&#x27;Goro Crater...
</td>
<td>
false
</td>
<td>
2008-01-15 21:56:54
</td>
<td>
3
</td>
<td>
1
</td>
<td>
655
</td>
</tr>
<tr>
<td>
7
</td>
<td>
54
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Hunter&quot;
</td>
<td>
&quot;The Barrens&quot;
</td>
<td>
false
</td>
<td>
2008-01-15 22:07:23
</td>
<td>
4
</td>
<td>
1
</td>
<td>
655
</td>
</tr>
<tr>
<td>
7
</td>
<td>
54
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Hunter&quot;
</td>
<td>
&quot;Badlands&quot;
</td>
<td>
false
</td>
<td>
2008-01-15 22:17:08
</td>
<td>
5
</td>
<td>
1
</td>
<td>
655
</td>
</tr>
<tr>
<td>
7
</td>
<td>
54
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Hunter&quot;
</td>
<td>
&quot;Badlands&quot;
</td>
<td>
false
</td>
<td>
2008-01-15 22:26:52
</td>
<td>
6
</td>
<td>
1
</td>
<td>
655
</td>
</tr>
<tr>
<td>
7
</td>
<td>
54
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Hunter&quot;
</td>
<td>
&quot;Badlands&quot;
</td>
<td>
false
</td>
<td>
2008-01-15 22:37:25
</td>
<td>
7
</td>
<td>
1
</td>
<td>
655
</td>
</tr>
<tr>
<td>
7
</td>
<td>
54
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Hunter&quot;
</td>
<td>
&quot;Swamp of Sorro...
</td>
<td>
true
</td>
<td>
2008-01-15 22:47:10
</td>
<td>
8
</td>
<td>
1
</td>
<td>
655
</td>
</tr>
<tr>
<td>
7
</td>
<td>
54
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Hunter&quot;
</td>
<td>
&quot;The Temple of ...
</td>
<td>
true
</td>
<td>
2008-01-15 22:56:53
</td>
<td>
9
</td>
<td>
1
</td>
<td>
655
</td>
</tr>
<tr>
<td>
7
</td>
<td>
54
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Hunter&quot;
</td>
<td>
&quot;The Temple of ...
</td>
<td>
true
</td>
<td>
2008-01-15 23:07:25
</td>
<td>
10
</td>
<td>
1
</td>
<td>
655
</td>
</tr>
<tr>
<td>
7
</td>
<td>
54
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Hunter&quot;
</td>
<td>
&quot;The Temple of ...
</td>
<td>
true
</td>
<td>
2008-01-15 23:17:09
</td>
<td>
11
</td>
<td>
1
</td>
<td>
655
</td>
</tr>
<tr>
<td>
7
</td>
<td>
55
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Hunter&quot;
</td>
<td>
&quot;The Temple of ...
</td>
<td>
true
</td>
<td>
2008-01-15 23:26:53
</td>
<td>
12
</td>
<td>
1
</td>
<td>
655
</td>
</tr>
<tr>
<td>
...
</td>
<td>
...
</td>
<td>
...
</td>
<td>
...
</td>
<td>
...
</td>
<td>
...
</td>
<td>
...
</td>
<td>
...
</td>
<td>
...
</td>
<td>
...
</td>
</tr>
<tr>
<td>
90575
</td>
<td>
2
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Hunter&quot;
</td>
<td>
&quot;Orgrimmar&quot;
</td>
<td>
false
</td>
<td>
2008-12-31 21:14:51
</td>
<td>
10823166
</td>
<td>
1
</td>
<td>
2
</td>
</tr>
<tr>
<td>
90576
</td>
<td>
2
</td>
<td>
&quot;Blood Elf&quot;
</td>
<td>
&quot;Warlock&quot;
</td>
<td>
&quot;Eversong Woods...
</td>
<td>
false
</td>
<td>
2008-12-31 22:06:58
</td>
<td>
10823167
</td>
<td>
1
</td>
<td>
5
</td>
</tr>
<tr>
<td>
90576
</td>
<td>
3
</td>
<td>
&quot;Blood Elf&quot;
</td>
<td>
&quot;Warlock&quot;
</td>
<td>
&quot;Eversong Woods...
</td>
<td>
false
</td>
<td>
2008-12-31 22:17:35
</td>
<td>
10823168
</td>
<td>
1
</td>
<td>
5
</td>
</tr>
<tr>
<td>
90576
</td>
<td>
3
</td>
<td>
&quot;Blood Elf&quot;
</td>
<td>
&quot;Warlock&quot;
</td>
<td>
&quot;Eversong Woods...
</td>
<td>
false
</td>
<td>
2008-12-31 22:32:52
</td>
<td>
10823169
</td>
<td>
1
</td>
<td>
5
</td>
</tr>
<tr>
<td>
90576
</td>
<td>
4
</td>
<td>
&quot;Blood Elf&quot;
</td>
<td>
&quot;Warlock&quot;
</td>
<td>
&quot;Eversong Woods...
</td>
<td>
false
</td>
<td>
2008-12-31 22:47:54
</td>
<td>
10823170
</td>
<td>
1
</td>
<td>
5
</td>
</tr>
<tr>
<td>
90576
</td>
<td>
5
</td>
<td>
&quot;Blood Elf&quot;
</td>
<td>
&quot;Warlock&quot;
</td>
<td>
&quot;Eversong Woods...
</td>
<td>
false
</td>
<td>
2008-12-31 23:07:13
</td>
<td>
10823171
</td>
<td>
1
</td>
<td>
5
</td>
</tr>
<tr>
<td>
90577
</td>
<td>
1
</td>
<td>
&quot;Blood Elf&quot;
</td>
<td>
&quot;Warlock&quot;
</td>
<td>
&quot;Eversong Woods...
</td>
<td>
false
</td>
<td>
2008-12-31 22:17:35
</td>
<td>
10823172
</td>
<td>
1
</td>
<td>
3
</td>
</tr>
<tr>
<td>
90577
</td>
<td>
2
</td>
<td>
&quot;Blood Elf&quot;
</td>
<td>
&quot;Warlock&quot;
</td>
<td>
&quot;Eversong Woods...
</td>
<td>
false
</td>
<td>
2008-12-31 22:32:52
</td>
<td>
10823173
</td>
<td>
1
</td>
<td>
3
</td>
</tr>
<tr>
<td>
90577
</td>
<td>
3
</td>
<td>
&quot;Blood Elf&quot;
</td>
<td>
&quot;Warlock&quot;
</td>
<td>
&quot;Eversong Woods...
</td>
<td>
false
</td>
<td>
2008-12-31 22:47:54
</td>
<td>
10823174
</td>
<td>
1
</td>
<td>
3
</td>
</tr>
<tr>
<td>
90578
</td>
<td>
1
</td>
<td>
&quot;Blood Elf&quot;
</td>
<td>
&quot;Paladin&quot;
</td>
<td>
&quot;Eversong Woods...
</td>
<td>
false
</td>
<td>
2008-12-31 22:32:52
</td>
<td>
10823175
</td>
<td>
1
</td>
<td>
1
</td>
</tr>
<tr>
<td>
90579
</td>
<td>
1
</td>
<td>
&quot;Orc&quot;
</td>
<td>
&quot;Warrior&quot;
</td>
<td>
&quot;Durotar&quot;
</td>
<td>
false
</td>
<td>
2008-12-31 22:44:45
</td>
<td>
10823176
</td>
<td>
1
</td>
<td>
1
</td>
</tr>
<tr>
<td>
90580
</td>
<td>
1
</td>
<td>
&quot;Tauren&quot;
</td>
<td>
&quot;Warrior&quot;
</td>
<td>
&quot;Mulgore&quot;
</td>
<td>
false
</td>
<td>
2008-12-31 23:15:20
</td>
<td>
10823177
</td>
<td>
1
</td>
<td>
1
</td>
</tr>
</tbody>
</table>
</div>


