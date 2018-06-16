---
# Posts need to have the `post` layout
layout: post

# The title of your post
title: Usando Django MPTT em APIs REST

# (Optional) Write a short (~150 characters) description of each blog post.
# This description is used to preview the page on search engines, social media, etc.
description: >
  Saiba como organizar os dados em uma hierarquia usando django-mptt em um ambiente
  que usa API REST.

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
tags: [django, rest]
# If you want a category or tag to have its own page,
# check out `_featured_categories` and `_featured_tags` respectively.
---

Essa semana precisei fazer com que criasse uma simples feature para categorias e sub-categorias, mas para uma API REST. De forma convencional com Django usei o [Django MPTT](https://github.com/django-mptt/django-mptt), e ele trás algumas boas features para serem persistidas e renderizadas com os templates do framework, mas aproveitei para fazer uns testes com o Django REST Framework. Assim, vamos aprender a preparar a serialização desse tipo de estrutura de dados, tanto para consulta como para persistência.

## Criando o projeto

Vamos criar uma simples aplicação de cadastro de categorias e subcategorias, nada tão complexo. Assim vamos criar o projeto Django, e instalar as dependências necessárias:

```shell
(categories)$ pip install djangorestframework django-mptt
```

Configure para que sua aplicação use as bibliotecas:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    'mptt',
    'rest_framework',
]
```

Feito isso, vamos criar a nossa aplicação.

```shell
(categories)$ python manage.py startapp core
(categories)$ mv core categories/
```

Registre essa nova aplicação, para iniciarmos o nosso desenvolvimento:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    'mptt',
    'rest_framework',

	'categories.core',
]
```

## Preparando o modelo

Vamos criar um modelo chamado **Category** que irá representar o que precisamos:

## Preparando o modelo

Vamos criar um modelo chamado **Category** que irá representar o que precisamos. Ele precisa herdar da classe _MPTTModel_. Veja a [documentação](http://django-mptt.github.io/django-mptt) para mais informações:

```python
# -*- coding: utf-8 -*-

from django.db import models

from mptt.models import MPTTModel, TreeForeignKey


class Category(MPTTModel):
	"""
	Representa as categorias e
	subcategorias do sistema
	"""
    name = models.CharField(max_length=80, unique=True)
    parent = TreeForeignKey('self', null=True, blank=True,
                            related_name='children', db_index=True)

    class MPTTMeta:
        db_table = "categories"
        order_insertion_by = ['name']

    def __str__(self):
        return self.name
```

O que fizemos é nada mais que criar um novo modelo. Ele possui o atributo _name_ que representa o nome da categoria e o atributo _parent_ que vai ser uma chave estrangeira do mesmo tipo de modelo, para associar as sub-categorias que são do mesmo tipo.

Para aplicar ao nosso banco, gere a migração e a execute:

```shell
(categories)$ python manage.py makemigrations
Migrations for 'core':
  0001_initial.py:
    - Create model Category

(categories)$ python manage.py migrate
Operations to perform:
  Apply all migrations: contenttypes, auth, sessions, core, admin
Running migrations:
  Rendering model states... DONE
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length...p OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null...y OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying core.0001_initial... OK
  Applying sessions.0001_initial... OK
```

## Criando serializer e views

Feito a nossa classe, vamos criar a classe serializer que vai tratar os dados vindos do usuário como provindas do servidor.

```python
# -*- coding: utf-8 -*-

from rest_framework import serializers

from categories.core.models import Category


class CategorySerializer(serializers.ModelSerializer):
    class Meta:
        model = Category
        fields = ("id", "name",)
```

Vamos criar a view que vai listar e persistir as categorias e subcategorias da nossa API. Vamos usar os generics do Django REST.

```python
# -*- coding: utf-8 -*-

from rest_framework import generics

from categories.core.serializers import CategorySerializer
from categories.core.models import Category


class CategoryView(generics.ListCreateAPIView):
    queryset = Category.objects.all()
    serializer_class = CategorySerializer


class CategoryDetailView(generics.RetrieveUpdateAPIView):
    queryset = Category.objects.all()
    serializer_class = CategorySerializer
```

E depois crie uma endpoint para a nossa view em `categories/urls.py`:

```python
# -*- coding: utf-8 -*-
from django.conf.urls import url
from django.contrib import admin

from categories.core.views import CategoryView, CategoryDetailView

urlpatterns = [
    url(r'^admin/', admin.site.urls),

    url(r'^categories/(?P<pk>[0-9]+)$', CategoryDetailView.as_view()),
    url(r'^categories/', CategoryView.as_view()),
]
```

Agora com tudo implementado e configurado, vamos rodar o servidor.

```shell
(categories)$ python manage.py runserver
```

## Criando categoria e subcategoria

Vamos criar uma categoria. Vamos usar o [httpie](https://github.com/jkbrzt/httpie) que é um belo CLI HTTP para fazer esses testes.

```shell
http POST http://localhost:8000/categories/ name="Category Bar"

HTTP/1.0 201 Created
Allow: GET, POST, HEAD, OPTIONS
Content-Type: application/json
Date: Thu, 09 Jun 2016 15:29:38 GMT
Server: WSGIServer/0.2 CPython/3.5.1
Vary: Accept, Cookie
X-Frame-Options: SAMEORIGIN

{
    "id": 1,
    "name": "Category Bar"
}
```

Agora vamos atualizar o nosso serializer para que ele possa aceitar subcategorias, mas de forma opcional, porque se criarmos outra categorias e tornar o children por padrão obrigatório conforme o Django REST Framework faz, vai gerar erro de validação.

```python
# -*- coding: utf-8 -*-

from rest_framework import serializers

from categories.core.models import Category


class CategorySerializer(serializers.ModelSerializer):
    children = serializers.PrimaryKeyRelatedField(
        queryset=Category.objects.all(),
        many=True,
        required=False
    )

    class Meta:
        model = Category
        fields = ("id", "name", "children",)
```

Vamos criar outra categoria que vai definir nosso registro anterior como subcategoria:

```shell
(categories) $ http POST http://localhost:8000/categories/ name="Category Foo" children:=[1]
HTTP/1.0 201 Created
Allow: GET, POST, HEAD, OPTIONS
Content-Type: application/json
Date: Thu, 09 Jun 2016 15:39:09 GMT
Server: WSGIServer/0.2 CPython/3.5.1
Vary: Accept, Cookie
X-Frame-Options: SAMEORIGIN

{
    "children": [
		1
	],
    "id": 2,
    "name": "Category Foo"
}
```

Vendo na interface do Django REST temos isso:

![Listando Categorias]({{ "/assets/img/posts/django-mptt-api-rest/category_list.png" | absolute_url }})

# Detalhando subcategorias

Vemos os filhos das categorias retornam somente a identificação do registro, e não é isso que queremos. Vamos fazer com que retorna além do _id_ retorne também o seu nome.

```python
# -*- coding: utf-8 -*-

from rest_framework import serializers

from categories.core.models import Category


class SubCategorySerializer(serializers.ModelSerializer):
    class Meta:
        model = Category
        fields = ("id", "name", "children",)


class CategorySerializer(serializers.ModelSerializer):
    children = SubCategorySerializer(many=True, required=False)

    class Meta:
        model = Category
        fields = ("id", "name", "children",)
```

Vendo na interface do Django REST temos isso:

![Listando Categorias]({{ "/assets/img/posts/django-mptt-api-rest/category_list_with_nested_serializer.png" | absolute_url }})

# Customizando serialização

Vamos supor que queremos mudar o nome _children_ para _subcategories_. Podemos fazer dessa forma:

```python
# -*- coding: utf-8 -*-

from rest_framework import serializers

from categories.core.models import Category


class SubCategorySerializer(serializers.ModelSerializer):
    class Meta:
        model = Category
        fields = ("id", "name", "children",)


class CategorySerializer(serializers.ModelSerializer):
    subcategories = SubCategorySerializer(source="children", many=True, required=False)

    class Meta:
        model = Category
        fields = ("id", "name", "subcategories",)
```

Vendo na interface do Django REST temos isso:

![Listando Categorias]({{ "/assets/img/posts/django-mptt-api-rest/category_list_with_custom_field.png" | absolute_url }})

O problema que na nossa subcategoria, vemos que ele continua _children_ já que não alteramos no serializer `SubCategorySerializer`. Você até poderia mudar, mas fica limitado a profundidade da estrutura, já que tiver mais de 20 níveis, teria que ficar mudando toda hora, e isso não é bom.

## Criando um novo field

O que podemos fazer é criar um field chamado de `RecursiveField` em que ele vai serializar a partir do serializer do parente. Assim pode ter quandos níveis quiser, que ele vai fazer isso e fora que podemos eliminar o `SubCategorySerializer`, e mudar o nome do campo sem repetições.

```python
# -*- coding: utf-8 -*-

from django.core.exceptions import ObjectDoesNotExist

from rest_framework import serializers

from categories.core.models import Category


class RecursiveField(serializers.BaseSerializer):
    """
    Cria instancia do serializer parente e retorna os dados
    serializados.
    """
    def to_representation(self, value):
        ParentSerializer = self.parent.parent.__class__
        serializer = ParentSerializer(value, context=self.context)
        return serializer.data

    def to_internal_value(self, data):
        ParentSerializer = self.parent.parent.__class__
        Model = ParentSerializer.Meta.model
        try:
            instance = Model.objects.get(pk=data)
        except ObjectDoesNotExist:
            raise serializers.ValidationError(
                "Objeto {0} não encontrado".format(
                    Model().__class__.__name__
                )
            )
        return instance


class CategorySerializer(serializers.ModelSerializer):
    subcategories = RecursiveField(source="children",
                                   many=True, required=False)

    class Meta:
        model = Category
        fields = ("id", "name", "subcategories",)
```

Vendo na interface do Django REST temos isso:

![Listando Categorias]({{ "/assets/img/posts/django-mptt-api-rest/category_list_with_recursive_field.png" | absolute_url }})

## Inserindo subcategorias

Vamos criar uma nova categoria, em que ela vai estar associada a nossa categoria _Category Bar_.

```shell
(categories)$ http POST http://localhost:8000/categories/ name="Category Foobar"
HTTP/1.0 201 Created
Allow: GET, POST, HEAD, OPTIONS
Content-Type: application/json
Date: Thu, 09 Jun 2016 16:13:16 GMT
Server: WSGIServer/0.2 CPython/3.5.1
Vary: Accept, Cookie
X-Frame-Options: SAMEORIGIN

{
    "id": 3,
    "name": "Category Foobar",
    "subcategories": []
}
```

```shell
(categories)$ http PUT http://localhost:5000/categories/1 subcategories:=[5]
HTTP/1.0 200 OK
Allow: GET, PUT, PATCH, HEAD, OPTIONS
Content-Type: application/json
Date: Thu, 09 Jun 2016 18:02:20 GMT
Server: WSGIServer/0.2 CPython/3.5.1
Vary: Accept, Cookie
X-Frame-Options: SAMEORIGIN

{
    "id": 1,
    "name": "Category Bar",
    "subcategories": [
        {
            "id": 5,
            "name": "Category Foobar",
            "subcategories": []
        }
    ]
}
```

Vendo na interface do Django REST temos isso:

![Listando Categorias]({{ "/assets/img/posts/django-mptt-api-rest/category_list_after_updated.png" | absolute_url }})

Vendo agora que temos três nívels na nossa hierarquia, o campo que criamos para o serializer evita repetição de código.

## Refatorando nossa consulta

Agora que já implementamos nossos endpoints, vamos refatorar a consulta das categorias, já que ele deve retornar da forma correta, que é a partir das categorias-pai, e encadeado pelos filhos.

```python
# -*- coding: utf-8 -*-

from rest_framework import generics

from categories.core.serializers import CategorySerializer
from categories.core.models import Category


class CategoryView(generics.ListCreateAPIView):
    queryset = Category.objects.root_nodes()
    serializer_class = CategorySerializer


class CategoryDetailView(generics.RetrieveUpdateAPIView):
    queryset = Category.objects.all()
    serializer_class = CategorySerializer
```

O django-mptt oferece um manager próprio, que possui os tipos de consutas que precisamos. Aqui usamos o `root_nodes()` para listar as categorias retornando somente as categorias principais, que são base para as subcategorias.

Vendo na interface do Django REST temos isso:

![Listando Categorias]({{ "/assets/img/posts/django-mptt-api-rest/category_with_queryset_custom.png" | absolute_url }})

## Solução Alternativa

Acima é uma solução em que pode fazer as customização que necessitar, mas se não vai passar disso, recomendo que use o [django-rest-framework-recursive](https://github.com/heywbj/django-rest-framework-recursive) que oferece uma serialização recursiva. Além da serialização no estilo de árvore, oferece alguns outros.

Assim vamos refatorar a solução que fizemos, deixando com menos código. Primeiro vamos instalar e configurar.

```shell
(categories)$ pip install djangorestframework-recursive
```

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    'mptt',
    'rest_framework',
    'rest_framework_recursive',

    'categories.core',
]
```

Vamos refatorar o nosso `serializers.py` para usar o campo recursivo.

```python
# -*- coding: utf-8 -*-

from rest_framework import serializers

from rest_framework_recursive.fields import RecursiveField

from categories.core.models import Category


class CategorySerializer(serializers.ModelSerializer):
    subcategories = serializers.ListSerializer(source="children",
                                               child=RecursiveField())

    class Meta:
        model = Category
        fields = ("id", "name", "subcategories",)
```

Vendo na interface do Django REST temos isso:

![Listando Categorias]({{ "/assets/img/posts/django-mptt-api-rest/category_list_with_app_recursive.png" | absolute_url }})

## Conclusão

Assim, vemos que não é tão complicado tratar esse tipo de estrutura de dados com o Django REST e o Django MPTT. Com o app que citei anteriormente, ele lista perfeitamente, mas no caso de persistência encontrei alguns problemas na validação, assim quando encontrar a solução irei atualizar esse post.

Repositório do projeto do post: [https://github.com/gilsondevlabs/categories-api-rest](https://github.com/gilsondevlabs/categories-api-rest/)

Espero que tenha gostado. Até mais!
