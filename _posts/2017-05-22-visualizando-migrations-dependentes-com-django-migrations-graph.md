---
# Posts need to have the `post` layout
layout: post

# The title of your post
title: Visualizando migrations dependentes com django-migrations-graph

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
tags: [django, migrations, python]
# If you want a category or tag to have its own page,
# check out `_featured_categories` and `_featured_tags` respectively.
---

Quando trabalhos em projetos Django, a partir do momento em que ele sofre inúmeras mudanças, acabamos usando as migrations para que o banco possa refletir as alterações. Com o tempo, a quantidade delas aumenta, e não sabemos exatamente qual módulo de migração depende de outra. Para resolver esse problema, foi criado o [django-migrations-graph](https://github.com/dizballanze/django-migrations-graph).

O objetivo ele é dar uma opção via management command, para que ele acesse as migrações e defina no terminal as dependências entre elas. Então vamos começar a usar.

## Instalação

Antes de mais nada, vamos preparar um ambiente virtualenv e criar um projeto de exemplo:

```shell
$ python3 -m venv .venv
$ pip install django
$ django-admin startproject graph_example .
```

Depois, vamos instalar o pacote e inserir o aplicativo no INSTALLED_APPS do projeto:

```shell
$ pip install django-migrations-graph
```

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    # Third Apps
    'migraph',
]
```

Instalado e configurado, vamos aproveitar os apps contrib para verificar a dependência entre _contrib.auth_ e _contrib.admin_:

```shell
$ python manage.py migration_dependencies auth admin
[auth]
auth/0001_initial
        Depending:
                admin/0001_initial
        Depends on:
                contenttypes/__first__
auth/0002_alter_permission_name_max_length
auth/0003_alter_user_email_max_length
auth/0004_alter_user_username_opts
auth/0005_alter_user_last_login_null
auth/0006_require_contenttypes_0002
        Depends on:
                contenttypes/0002_remove_content_type_name
auth/0007_alter_validators_add_error_messages
auth/0008_alter_user_username_max_length

[admin]
admin/0001_initial
        Depends on:
                auth/__first__
                contenttypes/__first__
admin/0002_logentry_remove_auto_add
```

É muito interessante! Vendo a saída, ao passarmos os aplicativos para efetuar a introspecção das migrações, ele mostra acima que o auth/0001*initial depente do 0001_initial do \_contrib.admin*, e que este depende de vários outros do _contrib.contenttypes_. Vemos também que em cada app ele separa por _[nome do app]_.

Esse app vai ajudar muito quando precisa trabalhar em projetos que ainda não conhece como um todo, aprender mais das migrações, como foram feitas e suas dependências.

Espero que tenham gostado. Até mais!
