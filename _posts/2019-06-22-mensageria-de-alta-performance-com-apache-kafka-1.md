---
# Posts need to have the `post` layout
layout: post

# The title of your post
title: "Mensageria de Alta Performance com Apache Kafka #1"

# (Optional) Write a short (~150 characters) description of each blog post.
# This description is used to preview the page on search engines, social media, etc.
# description: >
# Nesse primeiro post da série, vamos trazer uma abordagem geral do
# Apache Kafka, sua instalação e configuração, e entender os conceitos básicos de como ele funciona.

# (Optional) Link to an image that represents your blog post.
# The aspect ratio should be ~16:9.
image: /assets/img/posts/serie_kafka_1/cover.jpg

comments: true

# You can hide the description and/or image from the output
# (only visible to search engines) by setting:
# hide_description: true
# hide_image: true

# (Optional) Each post can have zero or more categories, and zero or more tags.
# The difference is that categories will be part of the URL, while tags will not.
# E.g. the URL of this post is <site.baseurl>/hydejack/2017/11/23/example-content/
categories: [engenharia-de-dados]
tags:
  [
    dados,
    engenharia de dados,
    desenvolvimento,
    data science,
    mensageria,
    kafka,
    pubsub,
  ]
# If you want a category or tag to have its own page,
# check out `_featured_categories` and `_featured_tags` respectively.
---

Olá pessoal, atualmente tenho entrado na área de Ciência de Dados diante do projeto que estou trabalhando no Banco do Brasil, e a partir dessa série pretendo trazer os conhecimentos adquiridos em uma ferramenta que atua como barramento de um grande volume de mensagens, e hoje iremos falar sobre o Apache Kafka.

### O que é o Apache Kafka?

O Apache Kafka é uma plataforma de streaming de eventos distribuída, ou seja, ele possui três capacidades chave:

- Faz _publish_ e _subscribe_ de streaming de dados, simular a uma fila de mensagens ou sistema de mensageria enterprise.
- Armazena streaming de dados em um modo _fault-tolerant_ (tolerante a falhas)
- Processa stream de dados

Ele é geral usado para dois tipos de aplicações:

- Construção de pipeline de streaming de dados em tempo real que precisa de dados confiáveis entre sistemas ou aplicações
- Construção de aplicações em tempo real que transforma ou reage com o fluxo de dados
- Arquiteturas de microserviços que faz uso de um sistema de mensageria para a comunicação entre os módulos de uma aplicação dessa modalidade, e que precisa de uma alta velocidade.

![](/assets/img/posts/serie_kafka_1/tipos_uso_kafka.png)

Fonte: [https://www.confluent.io/what-is-apache-kafka/](https://www.confluent.io/what-is-apache-kafka/)

### Arquitetura Pub/Sub

O Kafka segue a arquitetura de mensageria no estilo Publisher/Consumer, em que um componente **publica** uma mensagem em uma fila chamado de **tópico** a um intermediário e um outro componente **consome** essa mensagem a partir do intermediário, no **tópico** que essa informação está. Essas mensagens não são direcionadas a determinados consumidores, então para que eles possam acessar os dados eles se **inscrevem** nessa fila do intemediário para aguardarem novas informacões.

- **Publisher**: responsável por publicar as mensagens a um broker
- **Broker**: intermediário entre um Publisher e Consumer, que no nosso caso é o **Kafka**
- **Tópico**: Recurso nomeado para qual as mensagens são enviadas
- **Consumer**: responsável por consumir as mensagens de um determinado tópico

![](/assets/img/posts/serie_kafka_1/kafka_producer_consumer.png)

Fonte: [https://www.cloudkarafka.com/blog/2016-11-30-part1-kafka-for-beginners-what-is-apache-kafka.html](https://www.cloudkarafka.com/blog/2016-11-30-part1-kafka-for-beginners-what-is-apache-kafka.html)

### Instalação e Configuração

O Kafka precisa do [Apache Zookeeper](https://zookeeper.apache.org/) para coordenar os brokers do Kafka, principalmente quando envolve [múltiplos brokers](https://kafka.apache.org/documentation/#quickstart_multibroker) em um ambiente clusterizado. Então siga as [instruções de instalação do Zookeeper](https://zookeeper.apache.org/doc/r3.5.5/zookeeperStarted.html) antes de seguir nos procedimentos abaixo.

- Faça o [download](https://kafka.apache.org/downloads) do Apache Kafka e descompacte no diretório desejado. No nosso caso tanto o Zookeeper como o Kafka estará no _/opt_:

```shell
$ cd /opt
$ wget https://www-eu.apache.org/dist/kafka/2.2.1/kafka_2.11-2.2.1.tgz
$ tar -zxvf kafka_2.11-2.2.1.tgz
$ mv kafka_2.11-2.2.1 kafka
```

- Inicie o servidor Zookeeper caso não tenha feito, e após isso inicie o servidor do Kafka:

```shell
# Iniciando Zookeeper

$ cd /opt/zookeeper
$ ./bin/zkServer.sh start

# Iniciando Kafka

$ cd /opt/kafka
$ ./bin/kafka-server-start.sh config/server.properties
```

Para confirmar se estão de pé, verifique se na sua máquina tem as seguintes portas abaixo:

```shell
$ netstat -tlpn | grep -E "2181|9092"
tcp6       0      0 :::9092                 :::*                    OUÇA       6531/java
tcp6       0      0 :::2181                 :::*                    OUÇA       6498/java
```

A porta 2181 é do Apache Zookeeper e o 9092 é a porta padrão do Apache Kafka.

### Conceitos do Kafka

Ele possui alguns conceitos que são relevantes saber:

- Kafka é executado como **um cluster de um ou mais servidores** que pode abranger para múltiplos **datacenters**
- Esse cluster armazena fluxos de dados em categorias chamados de **_topics_**.
- Cada registro consistem de uma **chave**, um **valor** e um **timestamp**.

Ele é composto por quatro APIs:

- O [**Producer API**](https://kafka.apache.org/documentation.html#producerapi) faz com que uma aplicação possa publicar um fluxo de registros para um ou mais tópicos Kafka
- O [**Consumer API**](https://kafka.apache.org/documentation.html#consumerapi) faz com que a aplicação possa assinar um ou mais tópicos e processar o fluxo de registros produzidos por alguém.
- O [**Streams API**](https://kafka.apache.org/documentation/streams) faz com que a aplicação possa atuar como um _stream processor_, consumindo um fluxo de entrada (_input stream_) de um ou mais tópicos e produzir um fluxo de saída (_output stream_) para um ou mais tópicos, transformando de forma eficiente.
- O [**Connector API**](https://kafka.apache.org/documentation.html#connect) permite construir e executar _producers_ ou _consumers_ reutilizáveis que conecta tópicos a aplicativos ou sistemas de dados existentes. Por exemplo, um conector para um banco relacional captura cada mudança de uma tabela.

A comunicação entre os clientes e servidores é feito de forma simples e com alta performance, usando o protocolo TCP. Além disso, o Kafka provêm um cliente Java, mas tem disponíveis [outros clientes para várias linguagens](https://cwiki.apache.org/confluence/display/KAFKA/Clients).

![](/assets/img/posts/serie_kafka_1/kafka-apis.png)

Fonte: [https://kafka.apache.org/intro](https://kafka.apache.org/intro)

No próximo post, vamos falar mais sobre os tópicos e qual é a idéia das partições no Kafka. Além disso vou mostrar como podemos criar, alterar e remover tótpicos, como também atuar na ação de publicar e consumir mensagens de um tópico, e como fazemos com que mais de um consumer possa acessar o tópico sem buscar dados duplicados, por meio de _consumer group_.

Até mais! ;)
