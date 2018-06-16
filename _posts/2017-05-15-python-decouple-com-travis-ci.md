---
# Posts need to have the `post` layout
layout: post

# The title of your post
title: Python decouple e Travis CI

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
categories: [python, travis]
tags: [python, django, ci, travis]
# If you want a category or tag to have its own page,
# check out `_featured_categories` and `_featured_tags` respectively.
---

Para quem trabalha com projetos em Python e Django, talvez deva conhecer a iniciativa do Henrique Bastos que é o projeto [python-decouple](https://github.com/henriquebastos/python-decouple), que tem o intuito de facilitar a separação de dados de configuração com o código da sua aplicação. Antes era estrito para o Django, mas com o tempo ele tornou flexível para quem usa outros frameworks Python que forem usar. Esse procedimento de separação é uma das dicas recomendadas pelo [Twelve-Factors App](https://12factor.net/pt_br/), que é armazenar as configurações no ambiente em que o projeto será rodado.

Nesse post vamos supor que temos um pequeno projeto em Django, e precisamos definir um valor de configuração para o arquivo settings.py. Para isso vamos importar a função config do pacote para procurar o valor em uma variável nos arquivos que ele busca.

Ele basicamente passa a ler informações de configuração em arquivos .env ou com a extensão .ini. Caso a variável definida não encontrar, ele usa um valor padrão, se definido. Abaixo segue um pequeno exemplo pego no README do projeto:

```python
from decouple import config
```

Usamos a função para recuperar os valores:

```python
SECRET_KEY = config('SECRET_KEY')
DEBUG = config('DEBUG', default=False, cast=bool)
EMAIL_HOST = config('EMAIL_HOST', default='localhost')
EMAIL_PORT = config('EMAIL_PORT', default=25, cast=int)
```

Assim, se no arquivo .env ou um .ini tenha uma variável **SECRET_KEY**, então ele irá atribuir o valor ao existente no arquivo de configuração da aplicação. Para mais detalhes da aplicação, veja o README do projeto.

## Usando integração contínua

Para quem usa serviços de integração contínua, é necessário colocar configurações da aplicação para que possa executar o trabalho desejado, que normalmente é executar os testes automatizados para caso todos passarem, efetuar uma segunda ação (enviar cobertura, efetuar deploy em um PaaS, etc). Aqui, com o Travis CI, caso queiramos definir valores de configuração, a boa prática é não colocar no código, mas em variáveis de ambiente. No caso do Travis CI, podemos fazer da seguinte forma:

```python
language: python
 python:
   - "3.5"
 global:
   -DEBUG=False
   -SECRET_KEY="vg$ita%40wm=q1h4sr2jf+zjzyi*dt!*j-@8(b0b(4#d(0#a67"
 install:
   # (...)
```

Assim, no arquivo de configuração do seu projeto, você usa o os.environ.get do módulo os do Python para capturar o valor, como o exemplo abaixo:

```python
import os

# (...)

SECRET_KEY = os.environ.get('SECRET_KEY', '')
```

Dessa forma, ao executar o job, as variáveis serão inseridas no ambiente do Travis, e então quando a aplicação ler seu arquivo de configuração, o SECRET_KEY irá possuir o valor desejado, porque o módulo pegou da variável de ambiente. Mas no caso do python-decouple, ele não lê dessa forma, mas como a documentação orienta, nos arquivos ditos anteriormente. Como fazer com essa situação?

## Adaptando o job

A solução que encontrei, foi usar a opção `before_script` do .travis.yml para fazer uma escrita de um arquivo .env usando o comando `echo`:

```python
language: python
 python:
   - "3.6"
 install:
   # (...)
 before_script:
   - echo "DEBUG=False" >> .env
   - echo "SECRET_KEY=$SECRET_KEY" >> .env
```

Como pode ver, eu usei o comando echo para inserir a saída que coloquei em aspas duplas no arquivo .env. No caso do SECRET_KEY, eu fui nas configurações do job no Travis CI e coloquei o SECRET_KEY, para justamnete não exibir no log, já que fiz isso em um projeto aberto no Github. Dessa forma, o decouple irá ler o arquivo recém-criado e efetuar o que desejar no seu projeto (executar os testes, etc).

Espero que isso tenha ajudado, e com isso recomendo o uso desse pacote.

Até mais!
