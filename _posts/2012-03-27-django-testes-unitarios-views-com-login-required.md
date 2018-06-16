---
# Posts need to have the `post` layout
layout: post

# The title of your post
title: Testes unitários nas views do Django usando @login_required

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
categories: [python, testes]
tags: [python, django, testes]
# If you want a category or tag to have its own page,
# check out `_featured_categories` and `_featured_tags` respectively.
---

Esses dias precisei fazer alguns testes unitários nas actions das views da minha aplicação Django, para basicamente verificar se a sua requisição foi feito com sucesso, e se está usando o template correto. Só que isso é simples quando suas views não usam o decorator [@login_required](https://docs.djangoproject.com/en/dev/topics/auth/#django.contrib.auth.decorators.login_required).

Nesse post ensina você a fazer os testes necessários quando as suas actions necessitam de uma autenticação do usuário.

## 1. Criando fixtures

Um dos procedimentos principais para que consiga executar os seus testes com sucesso, é criar fixtures que contenham dados de um usuário fictício. Caso possua usuários que fez para teste no seu banco de desenvolvimento, vai ser muito importante porque vamos fazer um dump dessas informações e jogar em um arquivo JSON. Digite o seguinte comando:

```shell
$ python manage.py dumpdata auth.User --indent 4 > users.json
```

Agora temos um arquivo chamado user.json para ser usado na classe de teste. Insira esse arquivo na pasta fixtures da sua aplicação:

```shell
$ mv users.json apps/clients/fixtures
```

Então vamos criar uma classe no arquivo* tests.py* da sua aplicação e testarmos uma action fictícia:

```python
    # -*- coding: utf8 -*-

    from django.test import TestCase
    from django.core.urlresolvers import reverse

    class ClientViewTest(TestCase):
        """Unit test for client views"""

        fixtures = ['users.json']

        def setUp(self):
            self.response = self.client.login(username='john', password='123456')

        def test_profile_success(self):
            self.response = self.client.get(reverse('clientes:profile'))
            self.assertTrue(200, self.response.status_code)
            self.assertTemplateUsed(self.response, 'clients/profile_form.html')
```

Quando for executar o teste, vai perceber que vai funcionar porque a variável _response_ já vai receber o contexto do usuário que foi logado e assim ele terá a autorização de acessar/verificar o funcionamento das actions testadas.

Então é isso. Até mais ;)
