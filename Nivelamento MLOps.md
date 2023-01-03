# Principais módulos MLOps

## ETL

### O que é ETL?

ETL é um acrônimo que significa Extração(extract), Transformação(transform) e Carga(load). É um processo comumente usado em sistemas de Business Intelligence para integrar dados de várias fontes e prepará-los para análise.

Em Machine Learning, o ETL também pode ser usado para integrar dados de várias fontes e prepará-los para serem usados em modelos de aprendizado de máquina. Isso inclui tarefas como remover dados duplicados, tratar valores ausentes, normalizar os dados e selecionar apenas as colunas relevantes. Essas tarefas são importantes porque os modelos de aprendizado de máquina geralmente exigem que os dados estejam em um formato específico e limpo antes de poderem ser usados.

### Quais são as principais libs em python usadas para ETL?

Algumas das mais populares são:

- Pandas: Uma biblioteca popular para manipulação e análise de dados. Possui funções úteis para ler e escrever dados em vários formatos, incluindo CSV, Excel e SQL. Também possui funções para selecionar e filtrar linhas, calcular agregados e tratar valores ausentes.

- Numpy: Uma biblioteca de cálculo científico para Python. Possui funções úteis para realizar operações em conjuntos de dados, como calcular estatísticas básicas e realizar operações em matrizes.

- PySpark: Uma biblioteca que permite acessar o Apache Spark, um sistema de processamento de big data distribuído, a partir de Python. É útil para realizar tarefas de ETL em grandes conjuntos de dados.

- Luigi: Uma biblioteca de gerenciamento de trabalhos ETL que permite a criação de pipelines de ETL complexos.

- Apache Beam: Uma biblioteca de processamento de dados em larga escala que permite a criação de pipelines de ETL para diversas plataformas, incluindo o Apache Flink e o Google Cloud Dataflow.

### Exemplo de ETL usando Pandas

~~~python
# Importe o Pandas
import pandas as pd

# Extraindo dados de um arquivo CSV
df = pd.read_csv('data.csv')

# Selecionando apenas as colunas relevantes
df = df[['column_1', 'column_2', 'column_3']]

# Removendo linhas com valores ausentes
df = df.dropna()

~~~

### Exemplo de ETL usando Numpy

~~~python
import numpy as np

# Extraindo dados de um arquivo CSV
# Usamos a função loadtxt do Numpy para ler os dados de um arquivo CSV. 
# Definimos o parâmetro delimiter como ',' para indicar que os valores são separados por vírgulas no arquivo CSV. 
# Também definimos o parâmetro skiprows como 1 para pular a primeira linha, que geralmente contém os nomes das colunas.
raw_data = np.loadtxt('dados.csv', delimiter=',', skiprows=1)

# Selecionando apenas as colunas relevantes
# Aqui, estamos usando indexação por colunas para selecionar apenas as colunas 0, 1 e 2 do array raw_data.
data = raw_data[:, [0, 1, 2]]

# Removendo linhas com valores ausentes
# Usamos a função isnan do Numpy para criar uma máscara booleana que indica quais elementos são valores ausentes (NaN).
# Usamos a função any com o parâmetro axis=1 para verificar se qualquer elemento da linha é um NaN. 
# Usamos o operador de negação (~) para inverter a máscara e selecionar apenas as linhas que não possuem valores ausentes.
mask = ~np.isnan(data).any(axis=1)
data = data[mask]

# Normalizando os dados
# Estamos normalizando os dados da coluna 1
# Essa linha de código é responsável por normalizar os dados da coluna 1 do array data usando a fórmula de Z-score
data[:, 1] = (data[:, 1] - data[:, 1].mean()) / data[:, 1].std()

# Agregando os dados
# Calculando alguns agregados dos dados, como médias e desvios padrão, e armazenando os resultados em um array agregados.
agregados = np.array([
    data[:, 0].mean(),
    data[:, 1].mean(),
    data[:, 2].mean(),
    data[:, 0].std(),
    data[:, 1].std(),
    data[:, 2].std()
])

# Salvando os dados transformados em um novo arquivo CSV
np.savetxt('dados_limpos.csv', agregados, delimiter=',', header='média_1,média_2,média_3,desvio_1,desvio_2,desvio_3')
~~~

### Exemplo de ETL sendo armazenado em um feature store

~~~python
import pandas as pd
import featuretools as ft

# Extraindo dados de um arquivo CSV
df = pd.read_csv('dados.csv')

# Criando o objeto entityset
es = ft.EntitySet(id='dados')

# Adicionando a entidade principal
es = es.entity_from_dataframe(entity_id='principal', dataframe=df, index='id')

# Criando features usando featuretools
features, feature_names = ft.dfs(entityset=es, target_entity='principal')

# Salvando as features em um feature store
feature_store = ft.FeatureStore(es, 'feature_store')
feature_store.save_features(feature_names, features)

# Carregando as features do feature store
loaded_features = feature_store.load_features()
~~~

>Neste exemplo, os dados são lidos de um arquivo CSV e usados para criar um objeto **``EntitySet``** com o *Featuretools*. Em seguida, criamos features adicionais usando o *Featuretools* e salvamos essas features em um feature store. Por fim, carregamos as features do feature store para usá-las em um modelo de aprendizado de máquina ou para outros fins.

## Data version

### O que é Data version?

O termo "Data version" é um termo geral que pode ser usado para se referir a qualquer conjunto de dados que está sendo usado em um projeto de aprendizado de máquina. Ele pode ser usado para descrever o conjunto de dados completo ou um subconjunto dos dados que está sendo usado para treinar um modelo.

A "Data version" é importante em aprendizado de máquina, pois os conjuntos de dados podem ter uma grande influência nos resultados do modelo. Por exemplo, se você estiver treinando um modelo para prever o preço de uma casa com base em características como tamanho, localização e idade, a qualidade e a precisão dos dados de treinamento afetarão diretamente a precisão das previsões do modelo. Se os dados de treinamento forem incompletos, desatualizados ou incorretos, isso pode levar a previsões imprecisas.

Além disso, a "Data version" é importante porque pode ser útil manter o histórico de diferentes versões dos dados usados ​​em um projeto. Isso pode ser útil, por exemplo, se você estiver trabalhando em um projeto de longo prazo e precisar comparar os resultados de diferentes versões do modelo. Mantendo uma "Data version" pode também ajudar a garantir a reproducibilidade do seu trabalho, pois outros pesquisadores ou colaboradores saberão exatamente quais dados foram usados ​​em cada etapa do projeto.

### Quais são as principais libs em python usadas para data version?

As principais bibliotecas em Python que são usadas para gerenciar e controlar versões de dados são:

- DVC (Data Version Control): uma ferramenta de linha de comando que permite gerenciar e controlar conjuntos de dados e pipelines de treinamento de modelos de aprendizado de máquina.

- Quilt: uma ferramenta de gerenciamento de pacotes de dados que permite a colaboração e o compartilhamento de conjuntos de dados em toda a equipe.

- DataJoint: uma biblioteca Python para o gerenciamento de pipelines de dados científicos, que permite a colaboração e o controle de versão dos dados em projetos de longo prazo.

Essas são apenas algumas das principais bibliotecas disponíveis em Python para gerenciar e controlar versões de dados. Existem muitas outras ferramentas e bibliotecas disponíveis, dependendo das suas necessidades específicas e do escopo do seu projeto.

### Exemplo de Data Version usando DVC

~~~python
# Instale o DVC usando o pip
!pip install dvc

# Inicialize o DVC no seu repositório
!dvc init

# Adicione o conjunto de dados ao DVC
!dvc add data/dataset.csv

# Faça commit das alterações no DVC
!dvc commit -m "Adicionando conjunto de dados inicial"

# Atualize o conjunto de dados com novas observações
!dvc add data/new_observations.csv

# Faça commit das alterações no DVC
!dvc commit -m "Adicionando novas observações ao conjunto de dados"

# Veja o histórico de versões do conjunto de dados
!dvc history data/dataset.csv
~~~

>Esse código mostra como usar o DVC para controlar a versão de um conjunto de dados chamado **``dataset.csv``**. Primeiro, instalamos o DVC usando o **``pip``**, inicializamos o DVC no nosso repositório e adicionamos o conjunto de dados ao DVC. Em seguida, fazemos commit das alterações no DVC usando o comando **``dvc commit``**. Depois, atualizamos o conjunto de dados com novas observações e fazemos commit dessas alterações também. Por fim, podemos ver o histórico de versões do conjunto de dados usando o comando **``dvc history``**.

### Exemplo de Data Version usando Quilt

~~~python
# Instale o Quilt usando o pip
!pip install quilt

# Crie uma conta no Quilt Cloud
!quilt login

# Adicione o conjunto de dados ao Quilt
!quilt push username/package_name data/dataset.csv

# Faça commit das alterações no Quilt
!quilt build username/package_name
!quilt push username/package_name

# Atualize o conjunto de dados com novas observações
!quilt push username/package_name data/new_observations.csv

# Faça commit das alterações no Quilt
!quilt build username/package_name
!quilt push username/package_name

# Veja o histórico de versões do conjunto de dados
!quilt logs username/package_name
~~~

>Esse código mostra como usar o Quilt para controlar a versão de um conjunto de dados chamado **``dataset.csv``**. Primeiro, instalamos o *Quilt* usando o **``pip``** e criamos uma conta no Quilt Cloud. Em seguida, adicionamos o conjunto de dados ao Quilt usando o comando **``quilt push``** e fazemos commit das alterações usando os comandos **``quilt build``** e **``quilt push``**. Depois, atualizamos o conjunto de dados com novas observações e fazemos commit dessas alterações também. Por fim, podemos ver o histórico de versões do conjunto de dados usando o comando **``quilt logs``**.

## Feature Store

### O que é Feature Store?

 O Feature Store é uma plataforma para armazenar, gerenciar e compartilhar recursos (features) que são usados em modelos de machine learning. Os recursos são geralmente dados processados e transformados que são usados como entrada para os modelos de machine learning. O Feature Store permite que você armazene e gerencie esses recursos de maneira centralizada, facilitando o treinamento e o uso de modelos de machine learning em produção. Ele também pode fornecer ferramentas para monitorar e gerenciar os recursos, como ajustar os valores de recursos ou adicionar novos recursos. Além disso, o Feature Store pode ser usado para compartilhar recursos entre diferentes equipes e projetos, o que pode economizar tempo e esforço ao evitar a necessidade de calcular os recursos de forma independente em cada projeto.

### Quais são as principais libs em python usadas para Feature Store?

As principais bibliotecas em Python que são usadas para Feature Store são:

- TensorFlow Transform: Esta é uma biblioteca da Google que fornece uma série de ferramentas para processar e transformar dados para uso em modelos de aprendizado de máquina. Ela inclui suporte para o armazenamento de recursos em um Feature Store e pode ser usada para ler, escrever e transformar recursos no Feature Store.

- Feast: Esta é uma biblioteca de código aberto desenvolvida pela Google que fornece um Feature Store para armazenar, gerenciar e compartilhar recursos usados em modelos de machine learning. Ela é projetada para ser flexível e escalável e pode ser usada com diferentes linguagens de programação, incluindo Python.

- Great Expectations: Esta é uma biblioteca de Python para validação e teste de dados que também pode ser usada para armazenar e gerenciar recursos em um Feature Store. Ela fornece ferramentas para monitorar e garantir a qualidade dos recursos no Feature Store e pode ser integrada com outras ferramentas de machine learning, como o TensorFlow.

- DVC: O DVC (Data Version Control) é uma ferramenta de controle de versão de dados que pode ser usada para armazenar e gerenciar recursos em um Feature Store. Ele fornece uma maneira de rastrear as alterações nos recursos e facilita o compartilhamento de recursos entre diferentes projetos e equipes.

### Exemplo de Feature Store usando tensorflow transform

~~~python

# Importando libs
import tensorflow as tf
import tensorflow_transform as tft
import pandas as pd

# Carregue os dados de treinamento e de teste a partir de um arquivo CSV
train_data = pd.read_csv('train_data.csv')
test_data = pd.read_csv('test_data.csv')

# Defina as transformações de recursos que deseja aplicar aos dados
# Cada chave no dicionário é o nome de um recurso e o valor é um objeto de feature do TensorFlow
# que descreve o tipo de dado para esse recurso
feature_spec = {
    'feature1': tf.io.FixedLenFeature([], tf.float32),
    'feature2': tf.io.FixedLenFeature([], tf.float32),
    'feature3': tf.io.FixedLenFeature([], tf.string),
}

# Crie um transformador de recursos para aplicar as transformações aos dados de treinamento
# O transformador irá armazenar os parâmetros de transformação para aplicar às novas entradas no futuro
transformer = tft.TFTransform(feature_spec)

# Aplique as transformações aos dados de treinamento e de teste
transformed_train_data = transformer.transform(train_data)
transformed_test_data = transformer.transform(test_data)

# Crie um Feature Store para armazenar os recursos transformados
feature_store = tft.TFRecordFeatureStore('./feature_store', transformed_train_data)

# Crie um Feature Store para armazenar os recursos transformados
# O Feature Store armazenará os dados em um formato de arquivo que pode ser facilmente lido pelo TensorFlow
feature_store.add(transformed_train_data)
feature_store.add(transformed_test_data)

# Você também pode ler os recursos do Feature Store usando o método `load`
transformed_data = feature_store.load()

# Você pode acessar os recursos usando as chaves do dicionário feature_spec
features = transformed_data['features']
feature1 = features['feature1']
feature2 = features['feature2']
feature3 = features['feature3']

# Você também pode acessar o rótulo (se houver um) usando a chave 'label'
labels = transformed_data['label']

~~~

>Neste exemplo, carregamos os dados de treinamento e de teste e definimos as transformações de recursos que desejamos aplicar aos dados. Em seguida, criamos um transformador de recursos usando o **``TFTransform``** e aplicamos as transformações aos dados de treinamento e de teste. Em seguida, criamos um Feature Store usando o **``TFRecordFeatureStore``** e adicionamos os recursos transformados ao Feature Store usando o método **``add``**. Por fim, podemos ler os recursos do Feature Store usando o método **``load``** e acessar os recursos e os rótulos (se houver) usando as chaves do dicionário **``feature_spec``**.

### Exemplo de Feature Store usando DVC

~~~python
import dvc.api as dvc

# Carregue os dados de treinamento
train_data = ...

# Defina as transformações de recursos que deseja aplicar aos dados
# Estas podem ser funções de pré-processamento, como normalização ou remoção de outliers
def normalize(data):
  # Calcule a média e o desvio padrão dos dados
  mean = data.mean()
  std = data.std()
  # Normalize os dados subtraindo a média e dividindo pelo desvio padrão
  return (data - mean) / std

def remove_outliers(data):
  # Remova os outliers dos dados, considerando apenas os valores abaixo da quantile 90
  return data[data < data.quantile(0.9)]

# Aplique as transformações aos dados de treinamento
# Primeiro, remova os outliers dos dados
transformed_train_data = remove_outliers(train_data)
# Em seguida, normalize os dados restantes
transformed_train_data = normalize(transformed_train_data)

# Crie um Feature Store para armazenar os recursos transformados
feature_store = dvc.DVCFeatureStore('feature_store')

# Adicione os recursos transformados ao Feature Store
feature_store.add(transformed_train_data)

# Você também pode ler os recursos do Feature Store usando o método `load`
transformed_data = feature_store.load()
~~~

>Neste exemplo, primeiro carregamos os dados de treinamento e definimos as transformações de recursos que desejamos aplicar aos dados. Em seguida, aplicamos as transformações aos dados de treinamento e criamos um Feature Store usando o **``DVCFeatureStore``**. Adicionamos os recursos transformados ao Feature Store usando o método **``add``**. Por fim, podemos ler os recursos do Feature Store usando o método **``load``**.

### Exemplo de Feature Store sendo usado para treinar um modelo de ML

~~~python
# Importe a classe de modelo
from sklearn.ensemble import RandomForestClassifier

# Carregue os dados do Feature Store
X_train = feature_store.load_feature(name='train_features')
y_train = feature_store.load_feature(name='train_labels')
X_test = feature_store.load_feature(name='test_features')
y_test = feature_store.load_feature(name='test_labels')

# Crie o modelo de Random Forest
model = RandomForestClassifier()

# Treine o modelo usando os dados do Feature Store
model.fit(X_train, y_train)

# Faça previsões no conjunto de teste
predictions = model.predict(X_test)

# Avalie o desempenho do modelo usando o conjunto de teste
accuracy = model.score(X_test, y_test)
print('Accuracy:', accuracy)

# Armazene o modelo treinado no Feature Store
feature_store.save_model(name='random_forest_model', model=model)
~~~

>Importamos a classe **``RandomForestClassifier``** do scikit-learn, que será usada para criar o modelo de Random Forest.<br/>
Carregamos os dados de treinamento e teste do Feature Store usando os métodos *load_feature*. Esses dados são armazenados nas variáveis **``X_train``**, **``y_train``**, **``X_test``** e **``y_test``**, respectivamente.<br/>
Criamos o modelo de Random Forest usando a classe **``RandomForestClassifier``**.<br/>
Treinamos o modelo usando os dados de treinamento **``X_train``** e **``y_train``**.<br/>
Fazemos previsões no conjunto de teste **``X_test``** usando o método **``predict``** do modelo.<br/>
Avaliamos o desempenho do modelo comparando as previsões com os rótulos reais do conjunto de teste **``y_test``** usando o método **``score``**.<br/>
Armazenamos o modelo treinado no Feature Store usando o método **``save_model``**.

## Data Governance

### O que é Data Governance?

A Data Governance em ML envolve muitos aspectos diferentes do gerenciamento de dados para modelos de aprendizado de máquina. Algumas das coisas que podem ser incluídas na Data Governance em ML incluem:

Garantir que os dados usados para treinar modelos são de qualidade e adequados para o propósito. Isso pode incluir coisas como verificar a integridade dos dados, remover valores ausentes ou incorretos e garantir que os dados estejam em um formato apropriado.

Assegurar que as políticas de privacidade são cumpridas. Isso pode incluir coisas como garantir que os dados pessoais sensíveis sejam anonimizados ou agrupados de acordo com as leis e regulamentos aplicáveis.

Proteger os dados de acesso não autorizado. Isso pode incluir medidas de segurança como criptografia de dados e controle de acesso baseado em papéis.

Definir responsabilidades e papéis para diferentes pessoas na organização que lidam com os dados e os modelos ML. Isso pode incluir coisas como designar um responsável pela governança de dados e estabelecer processos para aprovação de modelos antes do uso em produção.

A Data Governance em ML é importante porque garante que os modelos de aprendizado de máquina sejam precisos e confiáveis, e protege a privacidade e segurança dos dados. Isso é crucial para garantir a confiança dos usuários nos modelos e na organização como um todo.

### Como esse processo é feito?

Existem muitas maneiras de implementar a Data Governance em ML, e o processo pode variar bastante de acordo com as necessidades e recursos da organização. No entanto, aqui estão alguns passos gerais que podem ser seguidos para implementar a Data Governance em ML em uma organização:

1. Defina o objetivo da governança de dados em ML. É importante ter uma visão clara do que se deseja alcançar com a governança de dados em ML. Isso pode incluir coisas como garantir a precisão dos modelos, proteger a privacidade dos dados ou cumprir regulamentos aplicáveis. Definir objetivos claros ajudará a orientar as decisões e as ações relacionadas à governança de dados.

>As empresas podem ter vários objetivos ao implementar a governança de dados, mas alguns dos principais incluem:
>
>1. Melhorar a qualidade dos dados: A governança de dados pode ajudar a garantir que os dados usados pela empresa sejam precisos, consistentes e de qualidade. Isso é importante porque os dados de qualidade são cruciais para tomar decisões informadas e para o sucesso de muitas atividades empresariais.
>
>2. Aumentar a eficiência: A governança de dados pode ajudar a tornar os processos de gerenciamento de dados mais eficientes e a reduzir o tempo gasto procurando e limpiando dados. Isso pode permitir que as equipes se concentrem em atividades de maior valor agregado.
>
>3. Garantir a conformidade: A governança de dados pode ajudar a garantir que a empresa cumpra com regulamentos e leis aplicáveis, como as leis de privacidade de dados. Isso é importante para evitar multas e outras penalidades, bem como para proteger a reputação da empresa.
>
>4. Melhorar a confiança dos usuários: A governança de dados pode ajudar a garantir que os dados usados pela empresa sejam precisos e confiáveis, o que pode aumentar a confiança dos usuários nos produtos e serviços da empresa.
>
>5. Melhorar a tomada de decisão: A governança de dados pode ajudar a garantir que os dados usados pela empresa sejam precisos e completos, o que pode melhorar a qualidade das decisões tomadas pela empresa.

2. Estabeleça um conjunto de políticas e práticas para a governança de dados em ML. Depois de definir os objetivos da governança de dados, é preciso estabelecer as políticas e práticas que serão usadas para alcançá-los. Isso pode incluir coisas como diretrizes para a qualidade dos dados, processos de aprovação de modelos e medidas de segurança para proteger os dados. As políticas e práticas devem ser documentadas de forma clara e devem ser compartilhadas com todas as pessoas envolvidas na governança de dados.

>As políticas e práticas de governança de dados são estabelecidas de diferentes maneiras, dependendo da empresa e do contexto específico. Algumas coisas que podem ser consideradas ao estabelecer políticas e práticas de governança de dados incluem:
>
>1. Objetivos da governança de dados: As políticas e práticas devem ser estabelecidas de acordo com os objetivos da governança de dados da empresa. Por exemplo, se um dos objetivos for garantir a conformidade com as leis de privacidade de dados, as políticas e práticas devem incluir medidas para proteger os dados pessoais sensíveis.
>
>2. Requisitos legais e regulatórios: As políticas e práticas devem ser estabelecidas de acordo com os requisitos legais e regulatórios aplicáveis. Por exemplo, se a empresa estiver sujeita a regulamentos específicos sobre o uso de dados pessoais, as políticas e práticas devem incluir medidas para garantir a conformidade com esses regulamentos.
>
>3. adrões e melhores práticas da indústria: As políticas e práticas devem ser estabelecidas de acordo com os padrões e as melhores práticas da indústria. Por exemplo, se a empresa estiver seguindo as diretrizes da ISO 27001 para segurança da informação, as políticas e práticas de governança de dados devem incluir medidas de segurança compatíveis com essas diretrizes.
>
>4. Considerações de negócio: As políticas e práticas devem ser estabelecidas de acordo com as necessidades e os objetivos do negócio da empresa. Por exemplo, se a empresa precisar de acesso rápido aos dados para tomar decisões de negócios em tempo real, as políticas e práticas de governança de dados devem incluir medidas para garantir que os dados estejam disponíveis de forma rápida e fácil.
>
>5. Feedback dos usuários: As políticas e práticas devem ser estabelecidas com base no feedback dos usuários, incluindo funcionários, clientes e outros stakeholders. Por exemplo, se os funcionários tiverem dificuldades em acessar os dados de que precisam para realizar suas tarefas, as políticas e práticas de governança de dados devem incluir medidas para facilitar o acesso aos dados.

3. Designe uma pessoa ou equipe responsável pela governança de dados em ML. Alguém precisa ser responsável por garantir que as políticas e práticas de governança de dados sejam seguidas e por gerenciar quaisquer questões relacionadas à governança de dados. Essa pessoa ou equipe também pode ser responsável por fornecer orientação e treinamento sobre governança de dados a outras pessoas na organização.

4. Crie processos para garantir a qualidade dos dados usados para treinar modelos. Os dados de qualidade são cruciais para garantir que os modelos de aprendizado de máquina sejam precisos e confiáveis. Portanto, é preciso criar processos para verificar a integridade dos dados, remover valores ausentes ou incorretos e garantir que os dados estejam em um formato apropriado. Isso pode incluir coisas como rotinas de limpeza de dados e verificação de qualidade dos dados.

5. Estabeleça processos para aprovar modelos antes do uso em produção (continuação). Além de testes de precisão e avaliação de riscos, os processos de aprovação de modelos também podem incluir coisas como revisão por pares e aprovação por uma equipe de liderança. É importante ter um processo de aprovação bem definido e documentado para garantir que os modelos sejam avaliados de maneira consistente antes do uso em produção.

6. Monitorar e avaliar continuamente o processo de governança de dados em ML. A governança de dados é um processo contínuo e é preciso dedicar tempo e recursos para garantir que esteja sendo feita de maneira eficiente e eficaz. Isso pode incluir coisas como verificar se as políticas e práticas estão sendo seguidas, avaliar se os modelos estão sendo usados de forma apropriada e fazer alterações no processo de governança de dados se necessário. É importante ter uma abordagem de "melhoria contínua" para garantir que a governança de dados esteja sempre atualizada e eficaz.

É importante notar que a governança de dados em ML é um processo contínuo e é preciso dedicar tempo e recursos para garantir que esteja sendo feita de maneira eficiente e eficaz.