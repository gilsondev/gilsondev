---
# Posts need to have the `post` layout
layout: post

# The title of your post
title: Usando o reverse URL do Django Admin

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
categories: [python]
tags: [python, django, django admin]
# If you want a category or tag to have its own page,
# check out `_featured_categories` and `_featured_tags` respectively.
---

Existem situações que queremos aproveitar uma URL de alguma página do django-admin para inserir em um link, para quando um usuário que tem permissão
de acesso, clicar nele, possa ser redirecionado para lá sem problemas, ao invés de digitar o endereço do Painel Administrativo, e etc.

Olhando a fonte do django-admin, o arquivo [sites.py](https://github.com/django/django/blob/master/django/contrib/admin/sites.py) cria as URLs das aplicações dinamicamente e com isso você pode aproveitar isso e usá-la
no seu template. Mas como assim? Vou explicar com um exemplo:

{% raw %}

```html+django
<a href="{% url admin:index %}> Acesse o Painel Administrativo</a>
```

{% endraw %}

Aquele reverse URL, ao ser renderizado pelo django, vai se transformar em um link que te leva para
a página inicial do admin. E como o Django gera as URLs das aplicações de forma dinâmica, você também
terá reverse URL das mesmas:

{% raw %}

```html+django
<a href="{% url admin:app_model_action %}>Link que acessa uma ação</a>
```

{% endraw %}

O django cria o reverse URL da seguinte forma:

    aplicação + nome do modelo + ação (create, update, delete, etc)

Seguindo esse raciocínio, vamos criar dois links: acessar lista de registros e formulário de novo registro:

{% raw %}

```html+django
<a href="{% url admin:foo_bar_changelist %}">Lista de Registros</a>
<a href="{% url admin:foo_bar_add %}">Novo registro</a>
```

{% endraw %}

Então é isso. Até mais ;)
