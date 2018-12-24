---
# Posts need to have the `post` layout
layout: post

# The title of your post
title: Usando o Airflow no Heroku

# (Optional) Write a short (~150 characters) description of each blog post.
# This description is used to preview the page on search engines, social media, etc.
# description: >

# (Optional) Link to an image that represents your blog post.
# The aspect ratio should be ~16:9.
image: /assets/img/posts/airflow-heroku/cover.png

comments: true

# You can hide the description and/or image from the output
# (only visible to search engines) by setting:
# hide_description: true
# hide_image: true

# (Optional) Each post can have zero or more categories, and zero or more tags.
# The difference is that categories will be part of the URL, while tags will not.
# E.g. the URL of this post is <site.baseurl>/hydejack/2017/11/23/example-content/
categories: [airflow, dados]
tags: [python, airflow, workflow, datamining, etl, heroku]
# If you want a category or tag to have its own page,
# check out `_featured_categories` and `_featured_tags` respectively.
---

No post de hoje vamos mostrar como você pode usar o Airflow no famoso serviço Heroku, e como é simples fazer isso.

## Preparando o ambiente

Antes de mais nada vamos deixar tudo pronto para podemos fazer isso, tanto no ambiente local como no Heroku. Primeiro você precisa ter os requisitos abaixo para conseguir seguir tudo o que está aqui sem problemas:

- Heroku Toolbelt
- Python 3
- Virtualenv

Se está com os itens acima, vamos começar. Primeiro crie um ambiente virtual para instalarmos as dependências necessárias:

```shell
$ mkdir airflow-heroku && cd airflow-heroku
$ python3.6 -m venv .venv
```

Ative o ambiente e instale as dependências. Não esqueça de salvar no arquivo `requirements.txt` para que o Heroku saiba o que instalar:

```shell
$ source .venv/bin/activate
$ pip install "apache-airflow[postgres,password]" cryptography
$ pip freeze > requirements.txt
```

Agora precisamos usar o `airflow init` para criar os arquivos necessários para executarmos o ambiente localmente. O que vamos precisar mais na frente para o deploy é o arquivo `airflow.cfg`:

```shell
$ export AIRFLOW_HOME=$PWD  # Definindo a raiz do airflow na pasta corrente
$ airflow initdb
```

O airflow irá criar vários arquivos e pastas como o exemplo abaixo:

```shell
.
├── airflow.cfg
├── airflow.db
├── logs
└── unittests.cfg
```

Vamos aproveitar o momento e preparar o repositório do projeto, como também incluir
os arquivos que não serão versionados:

```shell
$ git init
$ cat <<EOF >> .gitignore
*.pyc
*.db
.venv/
logs/
EOF
```

**Observação**: Ao executar com Python 3.7, pode ocorrer de aparecer a seguinte saída:

```
ERROR [airflow.models.DagBag] Failed to import: /tmp/airflow-heroku/.venv/lib/python3.7/site-packages/airflow/example_dags/example_http_operator.py
Traceback (most recent call last):
  File "/tmp/airflow-heroku/.venv/lib/python3.7/site-packages/airflow/models.py", line 377, in process_file
    m = imp.load_source(mod_name, filepath)
  File "/home/gilson/.pyenv/versions/3.7.1/lib/python3.7/imp.py", line 171, in load_source
    module = _load(spec)
  File "<frozen importlib._bootstrap>", line 696, in _load
  File "<frozen importlib._bootstrap>", line 677, in _load_unlocked
  File "<frozen importlib._bootstrap_external>", line 728, in exec_module
  File "<frozen importlib._bootstrap>", line 219, in _call_with_frames_removed
  File "/tmp/airflow-heroku/.venv/lib/python3.7/site-packages/airflow/example_dags/example_http_operator.py", line 27, in <module>
    from airflow.operators.http_operator import SimpleHttpOperator
  File "/tmp/airflow-heroku/.venv/lib/python3.7/site-packages/airflow/operators/http_operator.py", line 21, in <module>
    from airflow.hooks.http_hook import HttpHook
  File "/tmp/airflow-heroku/.venv/lib/python3.7/site-packages/airflow/hooks/http_hook.py", line 23, in <module>
    import tenacity
  File "/tmp/airflow-heroku/.venv/lib/python3.7/site-packages/tenacity/__init__.py", line 352
    from tenacity.async import AsyncRetrying
                      ^
SyntaxError: invalid syntax
```

Mesmo assim os arquivos serão criados e será executado sem problemas. O erro acontece por conta de uma DAG de exemplo.

Por fim, crie o arquivo `generate_fernet_key.py` e insira na pasta `contrib`. Ela irá gerar uma chave para que o Airflow use para encriptação de dados sensíveis. Insira o conteúdo abaixo no módulo citado:

```python
#!/usr/bin/env python

from cryptography import fernet

print(fernet.Fernet.generate_key().decode("utf-8"))
```

Dessa forma que preparamos o ambiente, já podemos criar as DAGS e fazer o teste rodando localmente.

## Tornando o ambiente mais semelhante ao de produção

Mesmo o heroku oferecendo toda a facilidade de implantação, é uma boa ter localmente o ambiente minimamente parecido com o que as suas DAGs serão executados. Vamos fazer algumas mudanças no arquivo `airflow.cfg`

O arquivo `airflow.cfg` apesar de versionarmos, será usado mais para uso local, então aproveite para deixar o ambiente mais próximo da produção como:

- Definir um banco relacional para o Airflow como PostgreSQL
- Registrar o tipo de executor adequado para rodar as DAGS
- Gerar uma chave com Fernet por meio do `generate_fernet_key.py`

Faça a alteração no arquivo de configuração para inserir informações acerca do banco de dados e o tipo de [executor desejado](https://airflow.incubator.apache.org/code.html?highlight=executors#airflow.executors.local_executor.LocalExecutor) que o Airflow irá usar para executar o agendamento dos workflows:

```config
# ...
sql_alchemy_conn = postgresql+psycopg2://postgres:postgres@localhost:5432/airflow

# ...
executor = LocalExecutor

# ...
fernet_key = $(python "contrib/generage_fernet_key.py")
```

Dessa forma o Airflow irá usar o banco PostgreSQL para armazenar os metadados das DAGs e suas execuções como o também o seu scheduler.

Remova o arquivo `airflow.db` e rode novamente o `airflow initdb` para que as migrations do airflow seja aplicados no banco.

## Configurando projeto para o Heroku

Agora vamos fazer algumas mudanças para que o Airflow seja executado corretamente no Heroku, juntamente com um usuário para acessar o painel de administração da ferramenta.

No caso do Heroku vamos aproveitar o uso de [variávels de ambiente](https://airflow.apache.org/howto/set-config.html?highlight=variable%20environment#setting-configuration-options) para sobrescrever propriedades do Airflow. Faça uso do `config:set`. Lembre-se de criar o app e os addons necessários:

```shell
$ heroku apps:create heroku-airflow
$ heroku addons:create heroku-postgresql:hobby-dev
```

```shell
$ heroku config:set SLUGIFY_USES_TEXT_UNIDECODE=yes
$ heroku config:set AIRFLOW_HOME="/app"
$ heroku config:set AIRFLOW__CORE__DAGS_FOLDER="/app/dags"
$ heroku config:set AIRFLOW__CORE__FERNET_KEY=`python contrib/generate_fernet_key.py`
$ heroku config:set AIRFLOW__CORE__LOAD_EXAMPLES=False
$ heroku config:set AIRFLOW__CORE__SQL_ALCHEMY_CONN=`heroku config:get DATABASE_URL`
$ heroku config:set AIRFLOW__CORE__EXECUTOR="LocalExecutor"
```

Crie o arquivo `Procfile` para o Heroku inicializar a aplicação corretamente:

```shell
release: airflow initdb
web: airflow webserver -p $PORT --daemon && airflow scheduler
```

Agora vamos versionar e enviar os arquivos para o Heroku:

```shell
$ git add .
$ git commit -m "Arquivos para o ambiente Airflow"
$ git push heroku master
```

Com isso conseguimos fazer com que o Airflow execute no ambiente:

![Página inicial do Airflow no Heroku](/assets/img/posts/airflow-heroku/airflow_heroku_worked_1.png)

Mas como pode perceber, deixar aberto o painel dessa forma não é seguro. Vamos tornar mais seguro.

### Inserindo autenticação no Airflow

Vamos inserir uma nova variável de ambiente para que o Airflow invoque a autenticação:

```shell
heroku config:set AIRFLOW__WEBSERVER__AUTHENTICATE=True
heroku config:set AIRFLOW__WEBSERVER__AUTH_BACKEND="airflow.contrib.auth.backends.password_auth"
```

Reinicie o seu dyno, e então vai encontrar uma página parecida com a imagem abaixo, se seguiu os procedimentos corretamente:

![Página inicial do Airflow no Heroku](/assets/img/posts/airflow-heroku/airflow_heroku_worked_2.png)

Agora que está preparado para autenticar, vamos criar um novo usuário. Acesse o shell do Python no Heroku com `heroku run python` e execute os comandos abaixo:

```shell
import airflow
from airflow import models, settings
from airflow.contrib.auth.backends.password_auth import PasswordUser

user = PasswordUser(models.User())
user.username = 'admin'
user.email = 'me@gilsondev.in'
user.password = 'password'

session = settings.Session()
session.add(user)
session.commit()
session.close()
exit()
```

Com usuário criado, se autentique com o seu username e senha e então irá acessar o painel administrativo novamente.

![Página inicial do Airflow no Heroku](/assets/img/posts/airflow-heroku/airflow_heroku_worked_1.png)

E pronto! Temos um airflow preparado no Heroku para receber suas DAGs.

## Bônus: Inserindo sua primeira DAG

Para fechar bem o post, vamos inserir uma DAG de exemplo para entenderem como usar no Airflow. Crie uma pasta `dags` na raiz do projeto e crie dentro dele um módulo chamado `tutorial_dag.py`:

```shell
"""
Code that goes along with the Airflow tutorial located at:
https://github.com/apache/incubator-airflow/blob/master/airflow/example_dags/tutorial.py
"""
from airflow import DAG
from airflow.operators.bash_operator import BashOperator
from datetime import datetime, timedelta


default_args = {
    'owner': 'airflow',
    'depends_on_past': False,
    'start_date': datetime(2015, 6, 1),
    'email': ['airflow@example.com'],
    'email_on_failure': False,
    'email_on_retry': False,
    'retries': 1,
    'retry_delay': timedelta(minutes=5),
    # 'queue': 'bash_queue',
    # 'pool': 'backfill',
    # 'priority_weight': 10,
    # 'end_date': datetime(2016, 1, 1),
}

dag = DAG('tutorial', default_args=default_args, schedule_interval=timedelta(days=1))

# t1, t2 and t3 are examples of tasks created by instantiating operators
t1 = BashOperator(
    task_id='print_date',
    bash_command='date',
    dag=dag)

t2 = BashOperator(
    task_id='sleep',
    bash_command='sleep 5',
    retries=3,
    dag=dag)

templated_command = """
    {% for i in range(5) %}
        echo "{{ ds }}"
        echo "{{ macros.ds_add(ds, 7)}}"
        echo "{{ params.my_param }}"
    {% endfor %}
"""

t3 = BashOperator(
    task_id='templated',
    bash_command=templated_command,
    params={'my_param': 'Parameter I passed in'},
    dag=dag)

t2.set_upstream(t1)
t3.set_upstream(t1)
```

Vamos aproveitar o código que está no [tutorial](https://airflow.apache.org/tutorial.html) da documentação do Airflow para focarmos no funcionamento. Faça o versionamento dessa nova DAG e envie para o heroku:

```shell
$ git add .
$ git commit -m "Inserção da primeira DAG"
$ git push heroku master
```

Agora acesse novamente o seu Airflow que você verá a DAG que acabamos de criar:

![Airflow com a DAG criada](/assets/img/posts/airflow-heroku/airflow_heroku_worked_3.png)

E agora ativando o schedule da DAG, já começou sua execução:

![Executando a DAG](/assets/img/posts/airflow-heroku/airflow_heroku_worked_4.png)

Depois de vários passos, agora temos um Airflow preparado para usar no Heroku.

Até mais pessoal!
