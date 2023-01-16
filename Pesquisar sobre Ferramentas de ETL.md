# Pesquisar sobre Ferramentas de ETL

## Listagem de algumas ferramentas de código livre com integração com python para ETL.

1. Apache Beam
2. Bonobo
3. ETLUtils
4. Luigi
5. Petl
6. Airflow
7. DBT
8. Transmute
9. Pygrametl
10. Dataform
11. Spoon
12. Keboola Connection
13. Apache Nifi
14. GeoKettle
15. Apache Flink
16. DataJoint
17. Spark

- ## **Apache Beam**
    
    ### Visão geral do Apache Beam

    Apache Beam é uma biblioteca de código aberto criada para definir pipelines de processamento de dados vindo de lotes de dados ou streamings. Ela oferece uma API de alto nível para definir o pipeline que será executado no back-end. A biblioteca foi criada visando o processamento de dados em larga escala podendo ser usada em diferentes plataformas de computação como o Apache Flink, Apache Spark e Google Cloud Dataflow. Isso significa que o código que você escreve para o processamento de dados pode ser facilmente migrado para alguma dessas plataformas, sem a necessidade de modificar o código.
    O Beam é estremamente útil para tarefas de processamento de dados [Embarrassingly parallel](https://en.wikipedia.org/wiki/Embarrassingly_parallel), assim podendo dividir um grande pacote de dados em pacotes menores que podem ser processados de forma independente ou em paralelo, O Beam também é muito usado para a tarefa de ETL e pure data integration.

    ![Infográfico simplificado demonstrando como o beam deve funcionar integrando as fontes de dados, o executor da tarefa, os recursos e linguagens de programação](https://beam.apache.org/images/learner_graph.png)

    ### Principais vantagens

    - Portabilidade: Sua API de alto nível oferece a opção de migração do código para diversas plataformas de computação sem a necessidade de alteração do código.
    - Facilidade de uso: A API é fácil de usar e permite que você crie pipelines  de processamento de dados de forma rápida e simples. Ela fornece suporte a várias linguagens de programação, exemplo: Java, Pythom, Go, Sacala e SQL.
    - Escalabilidade: Apache Beam foi projetada para lidar com grandes volumes de dados, assim, ela oferece a possibilidade de aumentar ou diminuir a capacidade de processamento conforme as necessidades do projeto.
    - Integração com diferentes fontes de dados: Ela oferece a integração com uma ampla variedade de fontes de dados, como bancos relacionais, arquivos de sistemas de arquivos distribuídos, Streams, entre outras diversas possibilidades.
    - Suporte a diferentes modelos de processamento: Ela fornece integração com uma ampla variedade de sistemas de armazenamento, podendo ser bancos de dados, sistemas de arquivos distribuídos e plataformas de nuvem. 
    - Integração com outras ferramentas de processamento de dados: Ela oferece integração com outras ferramentas de processamento de dados, como o *Apache Hive* e o *Apache Pig*, o que permite que você use a Apache Beam como parte de uma solução de processamento de dados mais ampla.

    ### Algumas Desvantagens

    - Complexidade: A lib conta com uma sere de ferramentas poderosas para diversas opções e recursos avançado de transformação e coleta de dados, isso pode toná-la complexa para usuários iniciantes e pode não ser a melhor opção para projetos que precisão ser entregues em curto prazo ou projetos simples.
    - Documentação limitada: Segundo relatos de alguns usuários da ferramenta, a biblioteca conta com uma documentação completa mas não tão detalhada quanto outras bibliotecas de processamento de dados. isso pode tornar um pouco mais dessfioador para os usuários iniciantes entender todos os recursos e opções disponíveis.
    - Suporte a linguagens: mesmo oferecendo suporte a várias linguagens, ela não oferece suporte nativo a linguagens muito utilizadas no processo de análise de dados como c++ ou R.
    - Dificuldade de depuração: ao trabalhar com fluxos de trabalho de processamento de dados complexos, podemos ser difícil depurar erros ou problemas que surgirem. Isso pode dificultar o entendimento do processo de processamento de dados mais complexos para usuários iniciantes.
    - Desempenho: Dependendo do tamanho do conjunto de dados pode ser que a lib tenha um desempenho inferior comparada à outras bibliotecas, principalmente no processamento de streams muito grande. Isso pode ser uma consideração ao escolher a Apache Beam para um projeto de processamento de dados específico.

    ### Referências 

    - APACHE. Apache Beam, 2022. Apache Beam Overview. Disponível em: https://beam.apache.org/get-started/beam-overview. Acesso em: 6 jan. 2022

    - APACHE. Apache Beam, 2021. Apache Beam documentation. Disponível em: https://beam.apache.org/documentation/. Acesso em: 6 jan. 2022

    - Google. Google Cloud, 2022. Modelo de programação para o Apache Beam. Disponível em: https://cloud.google.com/dataflow/docs/concepts/beam-programming-model?hl=pt-br. Acesso em: 6 jan. 2022

    - ChatGPT. 2022. 

- ## **Apache Spark**

    ### Visão geral do Apache Spark

    O Spark é um mecanismo de análise de processamento paralelo unificado para processamento de dados em larga escla. Ele oferece suporte ao processamento na memória para aumentar o desempenho de aplicativos que analisam big data. Forenece APIs de alto nível para Java, Scala, Python e um mecanismo otimizado que oferece suporte a gráficos de execução geral. Ele também oferece um conjunto de ferramentas auxiliares incluindo processamento de gráficos(GraphX), Processametno de dados estruturados (Spark SQL), compatibilidade com Pandas, para aprendizado de máquina como MLib e streaming estruturado para computação. Ela foi originalmente desenvolvida na Universidade de Berkeley em 2009. O framework Spark é 100% open source, hospedado no Apache Software Foundation independente de fornecedor.

    ### Principais vantagens

    - Integração: ela possui recursos complexos, revelantes e a possibilidade de integração com ferramentas conhecidas com Apache Cassandra e Hadoop.
    - API para linguagens de programação: Suporta linguagens como Java, Python, R e Scal e SQL. 
    - Eficiência: ele foi planejato para tirar um consumo eficiente de recursos da máquina, ele é capaz de processar os dados de forma a minimizar a quantidade de dados que precisam ser lidos.
    - Ampla biblioteca de ferramentas: similar a outras bibliotecas para análise e processamento de dados, o Spark possui algumas bibliotecas para facilitar do desenvolvedores. processamento de gráficos(GraphX), Processametno de dados estruturados (Spark SQL), compatibilidade com Pandas, para aprendizado de máquina como MLib, streaming estruturado para computação(Spark Streaming).
    - Framework de código aberto: O biblioteca inicialmente comeu a ser desenvolvida no AMPLab e posteriormente é mantido pela Apache Software.
    - Velocidade: Ela é capaz de processar grandes quantidades de dados em paralelo e processamento na memória.


    ### Algumas Desvantagens

    - Complexidade: Por possuir um número grande de funcionabilidades e configurações, as pessoas possuem uma certa dificuldade em aprender a utilizar a plataforma.
    - Overhead de memória: Como ele faz processamento em memória, a quantidade de memória ram que seus jobs utilizam pode ser um problema gerenciar o consumo no cluster.
    - Latência de E/S: o Apache Spark pode sofrer com latência de E/S (entrada/saída) em alguns casos, especialmente quando os dados precisam ser lidos de armazenamento secundário.
    - Custo de licenciamento: o Apache Spark pode ser caro de se licenciar em alguns casos, especialmente se você estiver usando uma versão comercial do software.
    - Falta de pessoas profissionais qualificadas na área: Por ser uma ferramenta nova e complexa de ser utilizda e o mercado ser relativamente novo e possuir diversas qualificações adicionais para ser compriendido e utilizado, encontrar profissionais que possuem essas habilidades e conhecem a plataforma é uma tarefa difícil.

    ## Referências

    - APACHE. Apache Spark, 2022. Visão geral do Spark. Disponível em: https://spark.apache.org/docs/latest/. Acesso em: 6 jan. 2022
    - Microsoft, O que é o Apache Spark?. 2022. Disponível em: https://learn.microsoft.com/pt-br/dotnet/spark/what-is-spark. Acesso em: 6 jan. 2022
    - Cetax, Spark: Saiba mais sobre esse poderoso framework. 2022. https://cetax.com.br/conheca-mais-sobre-o-framework-apache-spark/ Acesso em: 6 jan. 2022
    - xpeducacao, Spark: entenda sua função e saiba mais sobre essa ferramenta. 2022. https://blog.xpeducacao.com.br/apache-spark/. Acesso em: 6 jan. 2022
