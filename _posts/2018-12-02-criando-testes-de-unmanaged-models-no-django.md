---
# Posts need to have the `post` layout
layout: post

# The title of your post
title: Criando testes de unmanaged models no Django

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
categories: [django]
tags: [python, django, testes]
# If you want a category or tag to have its own page,
# check out `_featured_categories` and `_featured_tags` respectively.
---

No projeto que estou trabalhando no momento, precisei lidar com uma situação que era
criar modelos não-gerenciados para refletirem em tabelas já criadas no banco de dados. Até aí tudo bem,
mas como poderia criar testes unitários para validar não só o modelo mas funções de negócio que iria
precisar ficar atrelado a classe?

Depois de pesquisar um pouco sobre isso na [documentação](https://docs.djangoproject.com/en/2.1/ref/models/options/#managed), encontrei uma forma de lidar com isso
sem afetar o funcionamento real da aplicação. Mas como assim?

## Como os unmanaged models funciona?

Todos os modelos que você cria no seu projeto Django, por padrão eles são **managed**, ou seja,
o django irá criar a tabela no banco de dados apropriadamente, baseando no que o modelo representa. Segue um exemplo:

```python
class Question(models.Model):
    text = models.TextField()
```

Segue um modelo bem simples, mas que ele diz muita coisa para a ORM poder criar uma tabela como definida abaixo:

```sql
CREATE TABLE app_question(
    id int not null,
    text text not null,
    PRIMARY KEY(id)
);
```

Caso for executar esse SQL em um banco que respeite o ANSI SQL, ela será criada. O django faz essa conversão por debaixo dos panos e preparar para a criação por meio das [migrations](https://docs.djangoproject.com/en/2.1/topics/migrations/). Você que conhece o framework sabe como funciona.

Agora pode ocorrer de a gestão desses objetos do banco de dados, como as tabelas, já existam ou são controladas fora do framework, por
inúmeros motivos, e por isso não faz sentido o Django criá-las senão o encontrará problemas, sendo que não tem como ter tabelas duplicadas. Dessa forma o framework oferece no _Meta_ do seu modelo a propriedade _managed_. Se você definir como _False_, ela não será
responsável pela criação, alteração e remoção da tabela que o modelo se espelha.

O caso comum de uso de modelos não-gerenciáveis é quando precisa usar um banco de dados legado em que sua aplicação Django precisará
acessar. O comando _inspectdb_ do _manage.py_ é um ótimo auxílio para fazer o parser das tabelas desse banco para classes de modelo. Se nunca usou, recomendo que faça um teste e acredito que vai ser bem útil um dia.

## Classes não-gerenciáveis e os testes

Por não serem gerenciáveis pelo Django, não quer dizer que não vai poder:

- Inserir/alterar/remover registros
- Efetuar consultas nos registros

Mas como disse antes, não poderá criar/alterar/remover a tabela. Quando você faz testes automatizados no Django e isso envolver modelos, ao rodar os testes o comportamento do framework é:

- Criar tabelas no [banco de testes](https://docs.djangoproject.com/en/2.1/topics/testing/overview/#the-test-database) usando as migrations (default em SQLite: test_nome_do_banco.sqlite)
- Prepara o suite de testes
- Faz o uso do método _setUp_
- Executa os testes automatizados
- Se for usado os modelos para criar dados fake, insere na tabela recém criada
- Faz o uso do método _tearDown_
- Remove os registros criados nos testes
- Elimina o suite de testes
- Remove as tabelas do banco de dados de teste

Esse passo a passo foi descrito de forma bem grosseira, mas no geral é como funciona. O problema é que no caso de modelos não-gerenciados não irá funcionar porque se for criar dados fake com o modelo, vai dar erro dizendo que a tabela não existe e que o modelo não autoriza o Django a criar a tabela. Como lidar com isso?

## Uma abordagem simples

Buscando alternativas de lidar com isso encontrei uma solução que é [alterar o valor do managed no teste](https://kdazzle.svbtle.com/testing-with-unmanaged-databases-in-django) ou [criar um TestRunner para isso](https://dev.to/patrnk/testing-against-unmanaged-models-in-django). Se um o outro vai ser melhor, vai depender do problema que quer resolver, mas a primeira é a mais prática.

Como assim? Digamos que temos um modelo não gerenciado que queremos testar:

```python
class LegacyTable(models.Model):
    id = models.PositiveIntegerField(primary_key=True, db_column="cod_legacy")
    name = models.CharField(max_length=255, db_column="txt_name")

    class Meta:
        db_table = "legacy_table"
        managed = False
```

Criando ele e rodar o `makemigrations` para o app que o modelo está, irá criar uma migração parecido com essa:

```python
# -*- coding: utf-8 -*-
from __future__ import unicode_literals

from django.db import migrations, models


class Migration(migrations.Migration):

    dependencies = []

    operations = [
        migrations.CreateModel(
            name='LegacyTable',
            fields=[
                ('id', models.PositiveIntegerField(db_column='cod_legacy', primary_key=True, serialize=False)),
                ('name', models.CharField(db_column='txt_name', max_length=255)),
            ],
            options={
                'db_table': 'legacy_table',
                'managed': False,
            },
        ),
    ]
```

O que podemos fazer é definir uma variável no nosso _settings.py_ para que caso formos rodar testes automatizados ele esteja como _True_:

```python
# settings.py

# ...
DEBUG = True
TESTING = True
```

E com isso alteramos a migração para ficar assim:

```python
# -*- coding: utf-8 -*-
from __future__ import unicode_literals

from django.db import migrations, models
from django.conf import settings


class Migration(migrations.Migration):

    dependencies = []

    operations = [
        migrations.CreateModel(
            name='LegacyTable',
            fields=[
                ('id', models.PositiveIntegerField(db_column='cod_legacy', primary_key=True, serialize=False)),
                ('name', models.CharField(db_column='txt_name', max_length=255)),
            ],
            options={
                'db_table': 'legacy_table',
                'managed': settings.TESTING,
            },
        ),
    ]
```

Dessa forma, quando rodarmos `manage.py test app` a suíte de testes irá considerar como um modelo convencional, criará a tabela
no banco que será usado nos testes, e assim poderemos criar os dados fake perfeitamente. Claro que ao rodar em ambiente que não irá precisar desse tipo de situção, a propriedade `TESTING` precisa estar `False`.

Com isso conseguimos criar testes perfeitamente sem precisar fazer customizações malucas. Quem tiver outras soluções, coloque aqui nos comentários!

Até mais!
