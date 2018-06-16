---
# Posts need to have the `post` layout
layout: post

# The title of your post
title: Crie seu mockups com Pencil

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
categories: [ui, ux]
tags: [ux, mockups, ui]
# If you want a category or tag to have its own page,
# check out `_featured_categories` and `_featured_tags` respectively.
---

Quando desenvolvemos, antes de iniciar algum projeto (seja web ou mobile), sempre queremos rascunhar as telas e como será a disposição dos componentes, fluxo de navegação e etc. Existem inúmeras ferramentas, com seus diferenciais de recursos e variados tipos de preços. Hoje quero apresentar para vocês uma alternativa opensource para esse tipo de aplicação, o [Pencil Project](http://pencil.evolus.vn).

![]({{ "assets/img/posts/pencil_mockups/pencil_site.png" | absolute_url }})

Hoje ele foi totalmente revitalizado, porque para quem já conhecia sabe como ele estava largado no tempo. Se for verificar o [repositório](https://github.com/evolus/pencil) do projeto, pode ver que eles migraram sua base que era com o Mozilla XULRunner para o Atom Eletron. Isso é bom porque o projeto vai continuar e vai ser evoluido cada vez mais ;)

## Instalação

O Pencil suporta os seguintes SOs e distribuições:

- Windows
- OS X
- Fedora
- Debian
- Arch Linux (por meio do [AUR](https://aur.archlinux.org/packages/evolus-pencil-bin/))

Além disso, ele disponibiliza uma extensão [firefox](http://evoluspencil.googlecode.com/files/Pencil-2.0-5-fx.xpi), mas ele é da versão antiga, então use por sua conta e risco. Com isso, é só escolhar a forma de instalação desejada e abrir a aplicação.

## Dando uma olhada no Pencil

Vamos dar uma olhada na aplicação:

![]({{ "assets/img/posts/pencil_mockups/pencil_window.png" | absolute_url }})

Nessa janela temos:

1.  No lado esquerdo temos as opções de kits padrão no aplicativo, para serem usados. Eles possuem um conjunto de componentes, chamado de shapes.
2.  Oferece duas opções:

    - _Open a Document..._: Para abrir um arquivo já criado pelo Pencil
    - _Create a New Document_: Cria um novo documento no Pencil

3.  Aba de propriedade de algum shape selecionado no documento.

## Criando o primeiro mockup

Vamos criar um novo documento. Feito isso ele exibe uma página em branco para inserirmos os nossos shapes. Olhando a barra inferior, irá perceber que tem um _Untitled Page_. Em um documento, podemos criar vários mockups, e em cada um **será um page**.

![]({{ "assets/img/posts/pencil_mockups/pencil_new_document.png" | absolute_url }})

Agora é escolher o shape desejado, na barra lateral e arrastar na página:

![]({{ "assets/img/posts/pencil_mockups/pencil_shape_page.gif" | absolute_url }})

## Customizando a página

Na barra inferior você possui outras opções relacionados a página selecionada:

![]({{ "assets/img/posts/pencil_mockups/pencil_page_bar.gif" | absolute_url }})

## Opções da página

Clicando com o botão direito do mouse, você poderá acessar algumas opções como:

- Criar uma página filha: Uma nova página dependente da que foi criada antes
- Duplicar página
- Remover página
- Ir para uma página específica
- Propriedades da página
- Exportar página como PNG
- Inserir notas na página

![]({{ "assets/img/posts/pencil_mockups/pencil_options_page.png" | absolute_url }})

Abaixo segue os exemplos de cada opção.

### Criando Pàgina filha

![]({{ "assets/img/posts/pencil_mockups/pencil_child_page.gif" | absolute_url }})

### Duplicar a página

![]({{ "assets/img/posts/pencil_mockups/pencil_duplicate_page.gif" | absolute_url }})

### Remover a página

![]({{ "assets/img/posts/pencil_mockups/pencil_remove_page.gif" | absolute_url }})

### Ir para a página

![]({{ "assets/img/posts/pencil_mockups/pencil_goto_page.gif" | absolute_url }})

### Exportar página como PNG

![]({{ "assets/img/posts/pencil_mockups/pencil_export_page.gif" | absolute_url }})

Segue a página exportada:

![]({{ "assets/img/posts/pencil_mockups/exported_page.png" | absolute_url }})

## Propriedades da página

Dentre as opções, você possui as propriedades da página, em que pode modificar a página selecionada.

- Definir página atual como filha de outra página
- Renomear página
- Alterar tamanho da página
- Mudar o background da página

### Tornar página filha

![]({{ "assets/img/posts/pencil_mockups/pencil_properties_child_page.gif" | absolute_url }})

### Renomeando a página

![]({{ "assets/img/posts/pencil_mockups/pencil_rename_page.gif" | absolute_url }})

### Alterar tamanho da página

![]({{ "assets/img/posts/pencil_mockups/pencil_properties_size_page.gif" | absolute_url }})

### Alterar background da página

![]({{ "assets/img/posts/pencil_mockups/pencil_properties_bg_page.gif" | absolute_url }})

## Shapes no Pencil

Na versão antiga do Pencil, vocẽ precisa importar coleções de shapes para ter mais opções. Um exemplo de collection de terceiros é o [Nataniel Watson](https://nathanielw.github.io/pencil-stencils/). Hoje, o pencil já disponibiliza no próprio programa uma forma de instalar por meio de um gerenciador de coleções. Vá no ícone no canto superior esquerdo, e vai em **Tools -&gt; Manage Collections...**.

![]({{ "assets/img/posts/pencil_mockups/pencil_shape_collections.png" | absolute_url }})

Aqui você possui o catálogo de coleções que vem por padrão e instalados no seu programa. Para instalar um novo, clique em **Install From Repository...**.

![]({{ "assets/img/posts/pencil_mockups/pencil_collection_repository.png" | absolute_url }})

Instalado uma nova coleção, ele irá no seu catálogo, e então é só usar nos seus mockups.

Espero que tenha gostado desse post. O intuito é trazer uma visão geral do programa, e o seu uso básico. Agora comece a criar os seus protótipos ;)

Até mais!
