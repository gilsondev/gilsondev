---
# Posts need to have the `post` layout
layout: post

# The title of your post
title: Desbravando Python - Funções Built-in Parte 01

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
tags: [python, builtins, desenvolvimento]
# If you want a category or tag to have its own page,
# check out `_featured_categories` and `_featured_tags` respectively.
---

Nesse post vamos focar nas funções built-in do Python. Serão divididos em cinco partes.

## O qué esses built-ins?

Quando for programar módulos e scripts, eles vão estar sempre disponíveis, ou seja, não precisa fazer importação dos mesmos, porque estão embutidos. Então vamos para elas.

- Função abs()
- Função all()
- Função any()
- Função ascii()
- Função bin()
- Função bool()
- Função bytearray()
- [Função bytes()
- [Função callable()
- [Função chr()
- [Função classmethod()
- [Função compile()
- [Função complex()
- [Função delattr()

## abs(_x_)

Link: [https://docs.python.org/3/library/functions.html#abs](https://docs.python.org/3/library/functions.html#abs)

Essa função retorna um valor absoluto de um número. Ele aceita como argumento números inteiros ou de ponto flutuante. Mas se o valor do argumento for um número complexo, sua magnitude vai ser retornada.

```python
# Números inteiros
abs(2)
```

    2

```python
# Números do tipo ponto flutuante
abs(3.1416)
```

    3.1416

```python
# Números complexos
abs(2j)
```

    2.0

## all(_iterable_)

Link: [https://docs.python.org/3/library/functions.html#all](https://docs.python.org/3/library/functions.html#all)
Ele retorna `True` se todos os elementos de um iterável forem verdadeiros, ou se ele estiver vazio. É equivalente a isso:

```python
def all(iterable):
    for element in iterable:
        if not element:
            return False
    return True
```

Segue um exemplo de como essa função funciona:

```python
# Criando uma nova lista
lista = [1, 2, 3, 4, 5, 6]
all(lista)
```

    True

```python
# Criando uma lista vazia
lista_vazia = []
all(lista_vazia)
```

    True

Como pode ver, valores que em operações lógicas, retornam `True`. No caso de uma lista vazia, sendo vazia, não tem valores que podem remeter a algum valor falso. Agora no próximo caso é diferente:

```python
# Criando lista com valores que em operações lógicas retorna sempre falso
lista = [None, 0, '']
all(lista)
```

    False

E se tivéssemos valores que remetem a resultados verdadeiros e falsos?

```python
# Criando lista com valores que em operações lógicas retornam falso ou verdadeiro
lista = [None, 0, 1, '', 2, "teste"]
all(lista)
```

    False

Como pode ver, para retornar verdadeiro **todos** os valores precisam remetar a `True` em operações lógicas.

## any(_iterable_)

Link: [https://docs.python.org/3/library/functions.html#any](https://docs.python.org/3/library/functions.html#any)

Jà no caso dessa função, se existir algum valor que em operações lógicas retornam verdadeiro, o seu retorno será também verdadeiro. Ele funcionaria dessa forma:

```python
def any(iterable):
    for element in iterable:
        if element:
            return True
    return False
```

Segue um exemplo:

```python
# Criando uma lista
lista = [1, 2, 3]
any(lista)
```

    True

```python
# Criando uma lista vazia
lista = []
any(lista)
```

    False

Como pode ver, como não existe **qualquer** valor que retorne `True`, ele irá retornar `False`. Agora se existir pelo menos um valor?

```python
# Criando lista
lista = [0, None, False, '', 1]
any(lista)
```

    True

Temos o valor `1` que retorna True, então a função também vai retornar verdadeiro.

## ascii(_object_)

Link: [https://docs.python.org/3/library/functions.html#ascii](https://docs.python.org/3/library/functions.html#ascii)

Como a função _repr()_ que vamos explicar mais a frente, ele retorna um string que contêm uma representação impressa de um objeto. Mas pode ocorrer de retornar caracteres "não-ASCII" também.

```python
ascii(str)
```

    "<class 'str'>"

## bin(_x_)

Link: [https://docs.python.org/3/library/functions.html#bin](https://docs.python.org/3/library/functions.html#bin)

Converte um número inteiro para um string binário. Se `x` não for um objeto do tipo inteiro, ele deve implementar o método `__index__()` para retornar um inteiro.

```python
bin(2)
```

    '0b10'

```python
class NonInteger(object):
    def __index__(self):
        return 2

bin(NonInteger())
```

    '0b10'

## _class_ bool(_x_)

Link: [https://docs.python.org/3/library/functions.html#bool](https://docs.python.org/3/library/functions.html#bool)

Retorna um valor booleano, ou seja um `True` ou `False`. O valor de _x_ será convertido para o procedimento de teste padrão de veracidade, ou melhor, efetuar uma verificação lógica da mesma. Se _x_ é falso ou for omitido, ele retornará `False`, caso contrário retornará `True`.

```python
# Valores que retornam True
var1 = 10
var2 = 'ABC'
var3 = 10.123

print("bool() de var1: ", bool(var1))
print("bool() de var2: ", bool(var2))
print("bool() de var3: ", bool(var3))
```

    bool() de var1:  True
    bool() de var2:  True
    bool() de var3:  True

```python
# Valores que retornam False
var1 = None
var2 = 0
var3 = ''
var4 = []

print("bool() de var1: ", bool(var1))
print("bool() de var2: ", bool(var2))
print("bool() de var3: ", bool(var3))
print("bool() de var4: ", bool(var4))
```

    bool() de var1:  False
    bool() de var2:  False
    bool() de var3:  False
    bool() de var4:  False

O `bool()` é uma subclasse de `int`. Nós vamos explicar melhor na seção de [Tipos numéricos](), então ignore isso por enquando, mas lembre-se que ela não pode ser estendida:

```python
class NotWorkBoolSubClass(bool):
    pass
```

    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    <ipython-input-58-b7e68adf59a1> in <module>()
    ----> 1 class NotWorkBoolSubClass(bool):
          2     pass


    TypeError: type 'bool' is not an acceptable base type

## _class_ bytearray([_source_[, _encoding_[, _errors_]]])

Link: [https://docs.python.org/3/library/functions.html#bytearray](https://docs.python.org/3/library/functions.html#bytearray)

Ele retorna um novo array de _bytes_.

A classe _bytearray_ é uma sequência mutável de inteiros em um intervalo de **0 <= x < 256**.

```python
# Criando nova lista
lista = [1, 2, 3]
bytearray(lista)
```

    bytearray(b'\x01\x02\x03')

Parâmetros variados e opcionais podem ser usados para inicializar o array de diferentes maneiras:

**1 - Se for um string, você deve declarar sua codificação. `bytearray()` deve converter sua string para bytes usando `str.encode()`**

```python
bytearray("Conteudo a ser convertido")
```

    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    <ipython-input-71-697e34fada0a> in <module>()
    ----> 1 bytearray("Conteudo a ser convertido")


    TypeError: string argument without an encoding

```python
bytearray("Conteudo a ser convertido", "utf-8")
```

    bytearray(b'Conteudo a ser convertido')

**2 - Se for um inteiro, o array deve conter o tamanho do número e ser inicializado com byte nulo.**

```python
var1 = bytearray(2)
var2 = bytearray(4)

print(var1)
print(var2)
```

    bytearray(b'\x00\x00')
    bytearray(b'\x00\x00\x00\x00')

**3 - Se for um objeto em conformidade com interface _buffer_, um _buffer_ somente leitura de um objeto deve ser usado para inicializar o array de bytes**

```python
bytearray(range(10))
```

    bytearray(b'\x00\x01\x02\x03\x04\x05\x06\x07\x08\t')

**4 - Se for um iterável, ele deve ser um iterável de inteiros no intervalo e 0 <= x < 256, que são usados como conteúdos iniciais de um array**

```python
values = bytearray([3000, 4000, 5000])
```

    ---------------------------------------------------------------------------

    ValueError                                Traceback (most recent call last)

    <ipython-input-89-c18c149f2bad> in <module>()
    ----> 1 values = bytearray([3000, 4000, 5000])


    ValueError: byte must be in range(0, 256)

**5 - Sem algum argumento, o array de tamanho zero é criado.**

```python
result = bytearray()

print(result)
print(len(result))
```

    bytearray(b'')
    0

## bytes([_source_[, _encoding_[, _errors_]]])

Link: [https://docs.python.org/3/library/functions.html#bytes](https://docs.python.org/3/library/functions.html#bytes)

Retorna um novo objeto de bytes, que é uma sequência imutável de inteiros em um intervalo de **0 <= x < 256**.

```python
bytes(10)
```

    b'\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00'

O `bytes` é uma versão imutável do `bytesarray`, e tem os mesmos métodos não-mutávels e a mesma indexação e comportamento de _slicing_.

```python
foo = bytearray(5)
foo.pop()
```

    0

```python
foo = bytes(5)
foo.pop()
```

    ---------------------------------------------------------------------------

    AttributeError                            Traceback (most recent call last)

    <ipython-input-100-64564ad0bf45> in <module>()
          1 foo = bytes(5)
    ----> 2 foo.pop()


    AttributeError: 'bytes' object has no attribute 'pop'

```python
foo = bytes(5)
foo[2:]
```

    b'\x00\x00\x00'

## callable(_object_)

Link: [https://docs.python.org/3/library/functions.html#callable](https://docs.python.org/3/library/functions.html#callable)

Ele retorna `True` se o argumento aparenta ser _callable_, e é false se não for. Mas é bom lembrar que, se ele retornar verdadeiro, é possível que a chamada desse objeto falhe, mas se ele for falso, chamando o objeto nunca poderá ser bem sucedido. Note que classes que são _callable_ é quando elas possuem o método `__call__()`.

```python
callable(str)
```

    True

```python
callable(2)
```

    False

## chr(_i_)

Link: [https://docs.python.org/3/library/functions.html#chr](https://docs.python.org/3/library/functions.html#chr)

Retorna uma representação em string de um caractere a partir do código Unicode, que é o inteiro _i_. Segue um exemplo:

```python
print(chr(97))
print(chr(8364))
```

    a
    €

O inverso disso é feito pela função [ord()]().

### classmethod(_function_)

Link: [https://docs.python.org/3/library/functions.html#classmethod](https://docs.python.org/3/library/functions.html#classmethod)

Ele cria um método de classe. Esse método de classe recebe implicitamente a classe como primeiro argumento, assim como um método de instância recebe a instância pela palavra-chave _self_. Para declarar um método de classe faça da seguinte forma:

```python
class C:
    @classmethod
    def f(cls, arg1, arg2, **kwargs):
        pass
```

O _classmethod_ é um decorador, então pode ser usado para outros métodos que deseja. Ele pode ser chamado através da classe direito, ou pela instância. Segue um exemplo:

```python
# Criando a classe C
class C:
    @classmethod
    def f(cls, message):
        print("Funcao f() sendo executada: {0}".format(message))

# Executando o método de classe
C.f("Pela classe direta")
C().f("Pela instancia criada")
```

    Funcao f() sendo executada: Pela classe direta
    Funcao f() sendo executada: Pela instancia criada

**Observação:** Métodos de classe são diferentes dos métodos estáticos do C++ ou Java. Se deseja procurar esse tipo de método veja [staticmethod()]() nesse post.

## compile(_source_, _filename_, _mode_, _flags=0_, _dont_inherit=False_, _optimize=-1_)

Link: [https://docs.python.org/3/library/functions.html#compile](https://docs.python.org/3/library/functions.html#compile)

Compila o código-fonte em uma fonte ou em um objeto _AST_. Objetos de código podem ser executados pelos _exec()_ ou _eval()_. O parâmetro _source_ pode ser um string normal, um string byte ou um objeto _AST_.

```python
fonte = "print('Hello World')"

# Cria um objeto code object para ser executado pelo exec futuramente
source = compile(fonte, 'foo.py', 'exec')
```

O argumento _filename_ deve ser o arquivo que deve conter o código a ser lido. Jà o argumento _mode_ especifica que tipo de código deve ser compilado, ele pode ser _exec_ se a fonte consiste em uma sequência de declarações, _eval_ que consistem em uma expressão simples ou _single_ se consistema em uma simples declaração interativa.

Segue um exemplo:

```python
source = compile("var = 3\nprint('Valor e: {0}'.format(var))", 'modulefoo', 'exec')
exec(source)
```

    Valor e: 3

Como vimos, criamos um objeto que contém o código python, e definimos um exemplo de módulo chamado de `modulefoo`. Esse nosso módulo representa um arquivo, mas ele fica na memória para ser executado pelo _exec_, como especificamos na função _compile_. Esse tipo de função é bem vindo em situações que precisa de um script customizado, como em metaprogramação.

## complex([_real_[, _imag_]])

Link: [https://docs.python.org/3/library/functions.html#complex](https://docs.python.org/3/library/functions.html#complex)

Retorna um número complexo com o valor real mais _imag\*1j_ ou converte uma string ou um número para número complexo. Se o primeiro parâmetro for um string, ele irá ser interpretado como um número complexo e a função deve ser chamada sem um segundo parâmetro. Já o segundo parâmetro, não pode ser uma string, então fique atento a isso.

Cada argumento pode ser qualquer tipo numérico (incluindo o próprio complexo). Se o parâmetro _imag_ for omitido, o padrão é zero e o construtor serve uma conversão numérica do tipo _int_ e _float_. Se ambos os argumentos são omitidos, ele retorna `0j`.

```python
# Inserindo um número inteiro, sem imag
complex(2)
```

    (2+0j)

```python
# Inserindo uma string que tem formato de
# um número complexo
complex('1+2j')
```

    (1+2j)

```python
# Criando número complexo passando o valor
# e o imag
complex(4, 2)
```

    (4+2j)

```python
# Criando número complexo sem valor
complex()
```

    0j

## delattr(_object_, _name_)

Link: [https://docs.python.org/3/library/functions.html#delattr](https://docs.python.org/3/library/functions.html#delattr)

Remove um atributo de um objeto. Faz o contrário do [setattr()](), que insere atributos em um objeto. O primeiro parâmetro é o objeto instanciado, e o segundo é uma string que representa o nome de um dos atributos que a possui.

The string must be the name of one of the object’s attributes. The function deletes the named attribute, provided the object allows it. For example, delattr(x, 'foobar') is equivalent to del x.foobar.

```python
class ObjectFoo(object):
    def __init__(self, *args, **kwargs):
        self.attr1 = 'attr_foo'
        self.attr2 = 'attr_bar'

    def test(self):
        print("Foo bar")

foo = ObjectFoo()
print("Atributo foo.attr1: ", foo.attr1)

delattr(foo, 'attr1')
print("Atributo foo.attr1: ", foo.attr1)
```

    Atributo foo.attr1:  attr_foo



    ---------------------------------------------------------------------------

    AttributeError                            Traceback (most recent call last)

    <ipython-input-30-481db05efb93> in <module>()
         11
         12 delattr(foo, 'attr1')
    ---> 13 print("Atributo foo.attr1: ", foo.attr1)


    AttributeError: 'ObjectFoo' object has no attribute 'attr1'

A função `delattr` é equivalente a isso:

```python
class ObjectFoo(object):
    def __init__(self, *args, **kwargs):
        self.attr1 = 'attr_foo'
        self.attr2 = 'attr_bar'

    def test(self):
        print("Foo bar")

foo = ObjectFoo()
print("Atributo foo.attr1: ", foo.attr1)

del foo.attr1
print("Atributo foo.attr1: ", foo.attr1)
```

    Atributo foo.attr1:  attr_foo



    ---------------------------------------------------------------------------

    AttributeError                            Traceback (most recent call last)

    <ipython-input-32-ca6ae27f44ba> in <module>()
         11
         12 del foo.attr1
    ---> 13 print("Atributo foo.attr1: ", foo.attr1)


    AttributeError: 'ObjectFoo' object has no attribute 'attr1'

## class dict(_\*\*kwarg_)

## class dict(_mapping_, _\*\*kwarg_)

## class dict(_iterable_, _\*\*kwarg_)

Link: [https://docs.python.org/3/library/functions.html#func-dict](https://docs.python.org/3/library/functions.html#func-dict)

Cria um novo dicionário. O objeto `dict` é uma classe de dicionário, mas vamos focar nele no post de [Tipos de Mapeamento - dict]().

```python
# Criando dicionario

dict1 = dict(foo=1, bar=2)
dict1
```

    {'bar': 2, 'foo': 1}

```python
# Criando dicionario a partir de outro e estendendo-o
dict1 = dict(foo=1, bar=2)
other_dict = dict(dict1, key_1="other")
other_dict
```

    {'bar': 2, 'foo': 1, 'key_1': 'other'}

```python
# Criando um novo dicionário a partir de uma lista
lista = [("foo", 1), ("bar", 1), ("foobar", 1)]
new_dict = dict(lista, other_key="value")
new_dict
```

    {'bar': 1, 'foo': 1, 'foobar': 1, 'other_key': 'value'}

## dir([_object_])

Link: [https://docs.python.org/3/library/functions.html#dir](https://docs.python.org/3/library/functions.html#dir)

Sem argumento ele lista os nomes no escopo local.

```python
dir()
```

    ['In',
     'ObjectFoo',
     'Out',
     '_',
     '_13',
     '_14',
     '_15',
     '_16',
     '_17',
     '_18',
     '_19',
     '_20',
     '_21',
     '_22',
     '_23',
     '_24',
     '_3',
     '_34',
     '_36',
     '_37',
     '_4',
     '_44',
     '_45',
     '_46',
     '_5',
     '__',
     '___',
     '__builtin__',
     '__builtins__',
     '__doc__',
     '__loader__',
     '__name__',
     '__package__',
     '__spec__',
     '_dh',
     '_i',
     '_i1',
     '_i10',
     '_i11',
     '_i12',
     '_i13',
     '_i14',
     '_i15',
     '_i16',
     '_i17',
     '_i18',
     '_i19',
     '_i2',
     '_i20',
     '_i21',
     '_i22',
     '_i23',
     '_i24',
     '_i25',
     '_i26',
     '_i27',
     '_i28',
     '_i29',
     '_i3',
     '_i30',
     '_i31',
     '_i32',
     '_i33',
     '_i34',
     '_i35',
     '_i36',
     '_i37',
     '_i38',
     '_i39',
     '_i4',
     '_i40',
     '_i41',
     '_i42',
     '_i43',
     '_i44',
     '_i45',
     '_i46',
     '_i47',
     '_i5',
     '_i6',
     '_i7',
     '_i8',
     '_i9',
     '_ih',
     '_ii',
     '_iii',
     '_oh',
     '_sh',
     'dict1',
     'exit',
     'fonte',
     'foo',
     'get_ipython',
     'lista',
     'new_dict',
     'other_dict',
     'quit',
     'source',
     'var']

Como pode ver, alguns objetos e classes que criamos nesse post, ele ficou disponível no escopo local. Agora quando passa um objeto como argumento, ele lista os atributos disponíveis do mesmo.

```python
instance = ObjectFoo()
dir(instance)
```

    ['__class__',
     '__delattr__',
     '__dict__',
     '__dir__',
     '__doc__',
     '__eq__',
     '__format__',
     '__ge__',
     '__getattribute__',
     '__gt__',
     '__hash__',
     '__init__',
     '__le__',
     '__lt__',
     '__module__',
     '__ne__',
     '__new__',
     '__reduce__',
     '__reduce_ex__',
     '__repr__',
     '__setattr__',
     '__sizeof__',
     '__str__',
     '__subclasshook__',
     '__weakref__',
     'attr1',
     'attr2',
     'test']

Se o objeto tem um método chamado `__dir__()`, esse método deve ser chamado e deve retornar uma lista de atributos. Isto habilita os objetos de implementarem as funções customizadas de `__getattr__()` ou `__getattribute__()`, para ser a forma da função `dir()` reportar esses atributos.

Se o objeto não prover `__dir__()`, a função tenta fazer o melhor para coletar informações sobre o objeto passado pelo atributo `__dict__`, se definido, e do tipo do objeto. A lista resultando não é necessariamente completo, e pode ser impreciso quando ele tem o `__getattr__()` customizado.

O mecanismo padrão do `dir()` pode ser diferente com variados tipos de objetos, sendo que mais uma vez as tentativas de serem dados relevantes do que as mais completas. Assim:

- Se o objeto é um objeto de um módulo, a lista vai conter os nomes dos atributos do módulo;
- Se o objeto é um `type` ou objeto de classe, a lista vai conter os nomes dos seus atributos, e recursivamente os atributos das suas classes base;
- Caso contrário, as listas vão conter os nomes dos atributos do objeto, da sua classe e de forma recurso, vindo das classes base.

No próximo post, vamos abordar a segunda parte das funções built-in. Até mais!
