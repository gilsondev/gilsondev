---
# Posts need to have the `post` layout
layout: post

# The title of your post
title: Automatizando seu fluxo de trabalho com Airflow

# (Optional) Write a short (~150 characters) description of each blog post.
# This description is used to preview the page on search engines, social media, etc.
# description: >

# (Optional) Link to an image that represents your blog post.
# The aspect ratio should be ~16:9.
image: /assets/img/default.jpg

comments: true

# You can hide the description and/or image from the output
# (only visible to search engines) by setting:
# hide_description: true
# hide_image: true

# (Optional) Each post can have zero or more categories, and zero or more tags.
# The difference is that categories will be part of the URL, while tags will not.
# E.g. the URL of this post is <site.baseurl>/hydejack/2017/11/23/example-content/
categories: [airflow, dados]
tags: [python, airflow, workflow, datamining, etl]
# If you want a category or tag to have its own page,
# check out `_featured_categories` and `_featured_tags` respectively.
---

É comum surgir situações em que precisamos fazer alguns procedimentos repetitivos em diversos projetos, principalmente quando envolve dados.

Um exemplo disso:

- Migrar dados de uma tabela para outra
- Importar dados a partir de várias fontes e juntar em uma única base
- Seguir o passo acima e trabalhar com tratamento e filtragem das
  informações

Entre várias outras necessidade, mas no final acabamos tendo algo em comum que é definir um workflow, em que será composto por várias etapas para alcançarmos um objetivo, seja unificar, seja tratar, etc. Nesse post vamos falar do Apache Airflow, que é uma ferramenta opensource e que nos auxilia nesses tipos de situações.

## O que é o Airflow?

Ela foi criada pelo time de desenvolvedores do AirBnB, com o intuito de definir fluxos de trabalho que envolve consulta de dados de inúmeras fontes, tratamento e mineração de dados, de forma que possa ser feito de forma periódica ou não, ou seja, uma pipeline de dados. Ela se tornou open source em meados de 2015, sendo divulgado por um [post](https://medium.com/airbnb-engineering/airflow-a-workflow-management-platform-46318b977fd8) no blog aonde os engenheiros e cientistas de dados da empresa compartilham suas experiências. Depois de algum tempo, ela foi cedida para o Apache em que se tornou um dos inúmeros projetos que ela mantém, hoje chamado de [apache-airflow](https://github.com/apache/incubator-airflow).

## Instalação e configuração minima

Vamos preparar o ambiente e criar o primeiro workflow para entender como funciona. Primeiramente, vamos criar um ambiente virtual para instalarmos a ferramenta, então sugiro que use Python 3:

```shell
$ mkdir airflow-tutorial
$ cd airflow-tutorial
$ python3 -m venv .venv
$ source .venv/bin/activate
```

Instale o airflow usando _pip_:

```shell
(.venv)$ pip install apache-airflow
```

Conforme a [documentação](https://airflow.apache.org/), após a instalação é necessário definirmos a raiz para que possa ser gerado toda a base do airflow em que será usado.

```shell
# Definindo o diretório airflow-tutorial como raiz do Airflow
$ export AIRFLOW_HOME=$PWD
```

Agora vamos gerar o ambiente com o comando abaixo.

```shell
(.venv)$ airflow initdb
```

Esse comando irá criar os arquivos necessários para o Airflow, e rodar as migrações em um banco SQLite, em que ele armazena os metadados dos workflows. Para uso local e didático, com esse tipo de banco é o suficiente, mas tem outros procedimentos para preparar o seu uso em produção. Outra observação: **não se preocupe com o pedido do módulo cryptography.** Isso não vai afetar na experiência, isso é importante quando precisa encriptar algumas informações que não é o caso levantar agora, mas o seu uso real é necessário.

Vamos criar uma pasta chamada `dags`, em que vamos armazenar os fluxos implementados. Mais na frente irei explicar melhor:

```shell
(.venv)$ mkdir dags
```

Após preparar tudo, vamos iniciar o servidor local para vermos o painel administrativo da ferramenta.

```shell
(.venv)$ airflow webserver
```

Agora é acessar o [http://localhost:8080.](http://localhost:8080) Feito isso você vai ver algo parecido com isso:

![Página inicial do Painel Administrativo do Airflow]({{ "assets/img/posts/introducao-airflow/airflow_home.png" | absolute_url }})

Nessa página ele lista todos os workflows instalados, e que nesse caso são os exemplos que vem com a ferramenta, em que mostra várias formas de construir a sua pipeline. O bacana de usar pela interface é que pode ter um controle das execuções e o histórico de cada um deles, como qual etapa do fluxo falhou acompanhado de um log de execução, mas essa não é a única forma de usar, temos também a opção de linha de comando que veremos em breve nesse post.

## Criando nosso primeiro workflow

Agora com tudo pronto, vamos criar nosso exemplo. Vamos supor que precisamos fazer o fluxo abaixo:

1.  Imprimir a data na saída padrão
2.  Definir um sleep de 5 segundos
3.  Salvar a data em um arquivo texto

![Um pequeno diagrama exemplificando o fluxo]({{ "assets/img/posts/introducao-airflow/fluxo-tutorial.png" | absolute_url }})

A idéia é que ele seja executado diariamente, e que não seja cíclico. Outro ponto importante, é que a próxima etapa será executada se a anterior teve êxito no que foi criada a fazer após o seu processamento.

## Explicando a estrutura de uma DAG

Essa estrutura que preparamos, para o Airflow é chamada de [DAG](https://airflow.apache.org/concepts.html#dags):

> DAG = Directed Acyclic Graph

É um grafo acíclico, em que todas as tarefas que são executadas e organizadas de uma forma que reflete os seus relacionamentos e dependências. Você pode definir que um determinado nó deve ser executado se a anterior for um sucesso ou não, ou que o nó A terá um timeout de 5 minutos, e o nó B pode ser reiniciado até 5 vezes caso não tenha sucesso.

Dessa forma cada arquivo python armazena uma DAG, e ela é reconhecida pelo Airflow para sua execução na pasta `dags` que criamos anteriormente. Você [pode mudar o caminho](https://airflow.apache.org/configuration.html#setting-configuration-options) dessa pasta, alterando as configurações da ferramenta.

## Vamos ao código

Explicado como é um workflow para o Airflor, vamos criar a DAG do nosso exemplo.

### Preparando a DAG

Crie um novo arquivo chamado de `fluxo_simples.py` na pasta `dags` e implemente o código abaixo:

```python
import airflow

from airflow import DAG
from airflow.operators.bash_operator import BashOperator
from datetime import, timedelta

args = {
    'owner': 'airflow',
    'start_date': airflow.utils.dates.days_ago(2)
}

dag = DAG(
    dag_id='fluxo_simples',
    default_args=args,
    schedule_interval=timedelta(days=1),
    dagrun_timeout=timedelta(minutes=60)
)
```

O que fizemos aqui é preparar a criação da DAG em que ela possui algumas definições iniciais:

- Possui um ID único chamado de _fluxo_simples_: `dag_id`
- Recebe argumentos padrão e que reflete nos nós do grafo: `defaul_args`
- Define a periodicidade de execução. Nesse caso é diária: `schedule_interval`
- Define o tempo em que a DAG terá antes de ser interrompida: `dagrun_timeout`

A variável `dag` será necessária para vincular os nós que vamos criar.

### Criando os nós do fluxo

Continuando, no mesmo arquivo vamos criar os nós, baseado na estrutura que definirmos no exemplo:

```python
# (...)
# Depois da preparação da DAG

# 1. Imprime a data na saída padrão
task1 = BashOperator(
    task_id='print_date',
    bash_command='date',
    dag=dag)

# 2. Faz uma sleep de 5 segundos.
# Dando errado tente em no máximo 3 vezes
task2 = BashOperator(
    task_id='sleep',
    bash_command='sleep 5',
    retries=3,
    dag=dag)

# 3. Salve a data em um arquivo texto
task3 = BashOperator(
    task_id='save_date',
    bash_command='date > /tmp/date_output.txt',
    retries=3,
    dag=dag)
```

Com isso temos a DAG pronta. Criamos três tarefas em que cada uma delas é representado por um nó do nosso grafo e são criadas a partir de `Operators`, que são classes Python que oferece variados tipos de processamento. Aqui estamos usando do tipo `BashOperator` em que iremos executar comandos bash no console, passados no argumento `bash_command`. Cada tarefa criada também tem um ID único, então caso crie outro com o mesmo identificador, o Airflow vai gerar um erro antes de executá-la. Além disso, vinculamos as tarefas a dag criada através do argumento `dag`.

Por fim, precisamos definir a interligação dos nós e sua ordem de execução:

```python
# Interligando task1 ao task2
task1.set_downstream(task2)

# Interligando task2 ao task3
task2.set_downstream(task3)
```

Cada tarefa instanciada possui métodos para fazer ligação entre eles. No exemplo acima, usamos o `set_downstream` para definir que será a próxima tarefa a interligar no nó em questão. Temos também o `set_upstream` que define quem será o nó anterior. Segue a imagem para ficar mais claro a ligação:

![Um pequeno diagrama exemplificando o fluxo]({{ "assets/img/posts/introducao-airflow/fluxo-tutorial-2.png" | absolute_url }})

Os operators são importantes para a definição das DAGS. Abaixo segue alguns que a ferramenta disponibiliza:

- BashOperator: executa comandos shellscript
- DummyOperator: cria um nó que não executa nada, somente para representação
- EmailOperator: envia e-mail
- HdfsOperator: aguarda por um arquivo ou pasta no HDFS
- HiveOperator: executa código _hql_ em um banco de dados Hive específico
- SimpleHttpOperator: faz uma requisição de um endpoint HTTP
- MySqlOperator: executa códigos SQL em um banco MySQL
- PostgresOperator: executa códigos SQL em um banco PostgreSQL
- PythonOperator: executa um código callable em Python

Isso são alguns dos [vários operators](https://airflow.apache.org/code.html#operators) disponíveis, como existem outros [operators](https://github.com/airflow-plugins) criados e mantidos pela comunidade.

### Agora é executar

Com a nossa DAG totalmente finalizada, vamos testar executando na linha de comando:

```shell
(.venv)$ airflow backfill fluxo_simples -s 2018-06-09
```

Com isso, através do identificador que passamos via parâmetro ele irá localizar a DAG e executar todo o nosso fluxo. A data é importante para saber a data inicial da execução. Você vai ver várias saídas no console, como qual a tarefa que está alocada no momento para executar, o seu processamento e saída gerada, entre outras informações que consegue acessar também pelo Painel Administrativo. Abaixo vou colocar alguns trechos que faz sentido para o nosso tutorial:

```shell
[2018-06-09 12:14:36,996] {base_task_runner.py:98} INFO - Subtask: [2018-06-09 12:14:36,996] {bash_operator.py:97} INFO - Output:
[2018-06-09 12:14:36,997] {base_task_runner.py:98} INFO - Subtask: [2018-06-09 12:14:36,997] {bash_operator.py:101} INFO - Sáb Jun 09 12:14:36 -03 2018
```

Aqui ele executa o comando `date` e traz o resultado para a saída padrão.

```shell
[2018-06-09 12:14:53,475] {base_task_runner.py:98} INFO - Subtask: [2018-06-09 12:14:53,475] {bash_operator.py:88} INFO - Running command: date > /tmp/date_output.txt
[2018-06-09 12:14:53,477] {base_task_runner.py:98} INFO - Subtask: [2018-06-09 12:14:53,477] {bash_operator.py:97} INFO - Output:
[2018-06-09 12:14:53,479] {base_task_runner.py:98} INFO - Subtask: [2018-06-09 12:14:53,479] {bash_operator.py:105} INFO - Command exited with return code 0
```

E por aqui mostra que a execução do `date` e o resultado salvo em um arquivo foi feito com sucesso. Se for fazer um `cat` no arquivo gerado vai encontrar algo parecido:

```shell
(.venv)$ cat /tmp/date_output.txt
Sáb Jun 09 12:14:53 -03 2018
```

## Alguns comandos interessantes

Outros comandos que pode acabar usando na linha de comando:

Disparar a execução de uma DAG para que possa [preparar o agendamento](https://airflow.apache.org/scheduler.html), caso isso esteja habilitado:

```shell
$ airflow trigger_dag fluxo_simples -e 2018-06-10
```

Testar uma tarefa específica, ao invés de toda a DAG:

```shell
$ airflow test fluxo_simples print_date 2018-06-09
```

Nesse caso ele se basea em alguma configuração específica para testes no arquivo `unittest.cfg` e que não precisa usar alguma configuração no `airflow.cfg`.

Caso queira saber o status da execução de uma DAG:

```shell
$ airflow dag_state fluxo_simples 2018-06-09
```

Executar uma tarefa específica, ao invés de toda a DAG:

```shell
$ airflow run fluxo_simples print_date 2018-06-09
```

Listar todas as tarefas de uma DAG:

```shell
$ airflow list_tasks fluxo_simples
```

Listar todas as DAGS disponíveis:

```shell
$ airflow list_dags
```

Iniciar uma instância de scheduler. Isso vamos aprofundar quando abordarmos sobre os `Executors` do airflow. Aqui fica a título de curiosidade, ele é importante para verificar quais as próximas DAGS que estão perto de serem iniciadas a partir do que configurou na sua periodicidade:

```shell
$ airflow scheduler
```

Limpar o conjunto de instâncias de tarefas de uma DAG, como se nunca tivesse sido executado:

```shell
$ airflow clear fluxo_simples
```

## Visualizando no Painel

Agora vamos verificar como conseguimos acessar as informações da nossa DAG no Painel Administrativo. Com o `airflow webserver` executado, você deverá visualizar algo parecido das imagens abaixo:

1.  Página inicial do Painel, contendo a lista das dags e a nossa no final. Ela mostra a quantidade de vezes que teve sucesso e falha na DAG inteira, como na quantidade de tarefas.

![Página inicial com informações da nossa DAG]({{ "assets/img/posts/introducao-airflow/painel1.png" | absolute_url }})

2.  Podemos ver abaixo a linha com a nossa DAG. Quando executei estava com o nome `tutorial`, mas não é nada diferente do que fizemos nesse post:

![Linha com as informações da nossa DAG]({{ "assets/img/posts/introducao-airflow/painel2.png" | absolute_url }})

![Linha com as informações da nossa DAG, com tamanho maior]({{ "assets/img/posts/introducao-airflow/painel3.png" | absolute_url }})

Explicando cada tipo de estado e a quantidade em que cada DAG e tarefa o Airflow registrou:

![Explicando cada estado]({{ "assets/img/posts/introducao-airflow/detalhes-painel1.png" | absolute_url }})

3.  Clicando no nome da DAG, temos mais detalhes dela, como diferentes formas de visualizar todo os metadados gerados durante seu processamento.

**Visão em árvore**
![Visão hierárquica]({{ "assets/img/posts/introducao-airflow/painel-treeview.png" | absolute_url }})

**Visão em grafo**
![Visão em grafo]({{ "assets/img/posts/introducao-airflow/painel-graphview.png" | absolute_url }})

**Visão por tarefa e sua duração**
![Visão da duração da tarefa]({{ "assets/img/posts/introducao-airflow/painel-taskduration.png" | absolute_url }})

**Visão em Gantt da execução das tarefas**
![Visão em Gantt da duração da tarefa]({{ "assets/img/posts/introducao-airflow/painel-gantt.png" | absolute_url }})

**Visão do código da DAG**
![Visão do código]({{ "assets/img/posts/introducao-airflow/painel-codeview.png" | absolute_url }})

## E agora?

Esse post procurou trazer uma visão geral do Airflow, e o potencial que ele tem para auxiliar em automatização de fluxos de trabalho em geral. Segue alguns projetos que desenvolvi e que pode ser bons exemplo de como usar a ferramenta. Então façam o fork e testem a vontade para entender mais.

<div class="github-card" data-github="gilsondev/dag_dominios_govbr" data-width="800" data-height="130" data-theme="default"></div>
<div class="github-card" data-github="meucandidato/airflow-dags" data-width="800" data-height="130" data-theme="default"></div>

Espero que tenham gostado. Quaisquer dúvidas e sugestões comentem embaixo e vamos trocar idéias.

Até mais!
