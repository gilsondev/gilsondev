---
# Posts need to have the `post` layout
layout: post

# The title of your post
title: "Mensageria de Alta Performance com Apache Kafka #2"

# (Optional) Write a short (~150 characters) description of each blog post.
# This description is used to preview the page on search engines, social media, etc.
# description: >
# Nesse primeiro post da série, vamos trazer uma abordagem geral do
# Apache Kafka, sua instalação e configuração, e entender os conceitos básicos de como ele funciona.

# (Optional) Link to an image that represents your blog post.
# The aspect ratio should be ~16:9.
image: /assets/img/posts/serie_kafka_2/cover.jpg

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

Olá pessoal, nesse segundo post da série vamos abordar mais sobre os tópicos no Kafka e como é sua estrutura, além de mostrar as operações a serem feitos para mantê-las e as ações de um producer e consumer.

## Tópicos e Partições

O Kafka oferece binários para podemos efetuar operações nele, e uma delas é relacionado aos tópicos. Um tópico é nada mais que um fluxo de dados nomeado, e esse tópico armazena logs e são distribuídos e particionados, ou seja, divididos em pequenas partes. Pense no tópico como uma categoria ou um feed em que os registros são publicados.

![](/assets/img/posts/serie_kafka_2/image1.png)

Cada partição possui uma sequência ordenada e imutável, que é continuamente anexado (_appended_) para uma estrutura de log. Como pode ver na imagem acima, cada registro na partição tem um número sequencial atribuído, chamado de _offset_, que identifica unicamente cada registro da partição.

O cluster Kafka persiste todos os registros publicados, mesmo sendo consumidos ou não, usando uma configuração de retenção. Por exemplo, se na configuração foi setada que a política de retenção é de dois dias, então dentro desse período os registro serão inseridos nos tópicos e mantidos. Caso passe dos dois dias, eles serão descartados.

Uma característica interessante do Kafka é também armazenar metadados por consumer, que são o deslocamento ou a posição dele no log. Veja a imagem abaixo:

![](/assets/img/posts/serie_kafka_2/image2.png)

Esse _offset_ é controlado pelo consumer, então ele avança linearmente para consumir os registros, mas se ele quiser resetar esse metadado para reprocessar os dados ele pode, ou pular para o registro mais recente e começar a consumir a partir de "agora", isso é possível.

## Operações nos Tópicos

Por meio do `kafka-topics` podemos criar, listar, alterar e remover tópicos quando necessário. Abaixo vamos mostrar como efetuar cada operação, e os detalhes de cada parâmetro podemos explicar mais a frente.

**Criando um tópico**

```shell
$ kafka-topics --zookeeper 127.0.0.1:2181 --create --topic first_topic --partition 3 --replication-factor 1
```

A sáida deve ser parecida com essa:

```
Created topic "first_topic"
```

Se tentar criar um tópico com o mesmo nome, o Kafka não vai autorizar porque cada nome de tópico é único. Será preciso removê-la para criar outro com o mesmo nome.

**Listando tópicos**

```shell
$ kafka-topics --zookeeper 127.0.0.1:2181 --list
```

A saída deve ser parecida com essa:

```
__consumer_offsets
logs-broker
first_topic
```

**Removendo tópicos**

```shell
$ kafka-topics --zookeeper 127.0.0.1:2181 --topic first_topic --delete
```

A saída deve ser parecida com essa:

```
Topic first_topic is marked for deletion
Note: This will have no impact if delete.topic.enable is not set to True
```

O tópico não é removido se:

- Possuir producers vinculados
- Possuir consumers vinculados
- Tiver registros salvos

Uma das [formas de remover definitivamente](https://stackoverflow.com/questions/33537950/how-to-delete-a-topic-in-apache-kafka/33538299) é reiniciando e removendo fisicamente o diretório do tópico.

**Detalhando tópico**

```shell
$ kafka-topics --zookeeper 127.0.0.1:2181 --topic first_topic --describe
```

A saída deve ser parecida com essa:

```
Topic:first_topic       PartitionCount:3        ReplicationFactor:1     Configs:
        Topic: first_topic      Partition: 0    Leader: 0       Replicas: 0     Isr: 0
        Topic: first_topic      Partition: 1    Leader: 0       Replicas: 0     Isr: 0
        Topic: first_topic      Partition: 2    Leader: 0       Replicas: 0     Isr: 0
```

---

## Enviando mensagens com Producers

Vamos fazer o primeiro envio de mensagens ao tópico que acabamos de criar na seção anterior. O Kafka possui um script chamado de [`kafka-console-producer.sh`](http://kafka-console-producer.sh/) em que faz o papel de um producer. Abaixo está o comando que vamos executar para enviar um texto de exemplo:

```shell
$ kafka-console-producer.sh --broker-list localhost:9092 --topic first_topic
This is a message
This is another message
```

Quando damos enter no comando, ele abre a comunicação no terminal, para receber o conteúdo e assim enviar ao tópico. a cada texto inserido seguindo de <ENTER> será uma mensagem enviada, então no exemplo acima foi enviado duas informações.

## Consumindo mensagens com Consumers

Vamos seguir a mesma idéia, só que para o consumo das mensagens enviadas. No mesmo diretório de binários do Kafka, tem o script [`kafka-console-consumer.sh`](http://kafka-console-consumer.sh/) em que atua como um consumidor de um determinado tópico. O comando abaixo exemplifica o acesso as mensagens do tópico _first_topic_:

```
$ kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic first_topic --from-beginning
This is a message
This is another message
```

Ele abre também a comunicação no terminal, só que para a saída padrão. O comando pediu para o Kafka consumir as mensagens do tópico **desde o início**. Isso é importante porque o consumer pode escolher acessar as informações da seguinte forma:

- **Início do tópico (earliest)**: Consome as mensagens partindo do offset 0 de todas as partições, ou de uma específica
- **Fim do tópico (latest)**: Consome as mensagens partindo do último offset de todas as partições ou de uma específica
- **Offset específico**: Consome mensagem de um offset ID específico que foi definido pelo consumidor, então nesse caso precisa saber o seu índice.

Vamos mostrar alguns exemplos:

- **Consumindo a partir do offset inicial, de todas as partições**

```shell
$ kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic first_topic --offset earliest
This is a message
This is another message
```

- **Consumindo a partir do offset inicial, de uma partição específica**

```shell
$ kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic first_topic --partition 0 --offset earliest
This is a message
```

- **Consumindo a partir do offset final, de todas as partições**

```shell
$ kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic first_topic --offset latest
Message sended after the two first
```

- **Consumindo a partir do offset final, de uma partição específica**

```shell
$ kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic first_topic --partition 1 --offset latest
This is another message
Message sended after the two first
```

- **Consumindo a partir de um offset específico (índice 1)**

```shell
$ kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic first_topic --offset 1
This is another message
```

Com as informações abordadas aqui, já podemos fazer uso da ferramenta para atuar como broker de mensagens. No terceiro post vamos aprofundar sobre os Consumer Groups, e como isso é importante no consumo de grande volume de dados.
