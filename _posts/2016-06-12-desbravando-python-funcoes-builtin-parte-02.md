---
# Posts need to have the `post` layout
layout: post

# The title of your post
title: Desbravando Python - Funções Built-in Parte 02

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

Nesse segundo post de funções built-in, vamos trazer funções em alguns tipos de dados, execução de expressões Python e muito mais.

- Função dict()
- Função dir()
- Função divmod()
- Função enumerate()
- Função eval()
- Função exec()
- Função filter()
- Função float()
- Função format()
- Função frozenset()
- Função getattr()
- Função globals()
- Função hasattr()
- Função hash()

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

## divmod(_a_, _b_)

Link: [https://docs.python.org/3/library/functions.html#divmod](https://docs.python.org/3/library/functions.html#divmod)

Pega dois argumentos que são números não complexos, e retorna um par de números que consistem no quociente e o resto de uma divisão inteira.

Com tipos de operandos misturados, as regras para operadores aritméticos binários são aplicados. Para inteiros, o resultado é o mesmo que `(a // b, a % b)`. Para números de ponto flutuante o resultado é `(q, a % b)`, onde _q_ é usualmente `math.floor(a / b)`, mas pode ser menos que um. Em qualquer caso, `q * b + a % b` está muito perto de _a_, se `a % b` for diferente de zero e que tem o mesmo sinal como _b_, e `0 <= abs(a % b) < abs(b)`.

```python
divmod(2, 2)
```

    (1, 0)

```python
divmod (2, 2.222)
```

    (0.0, 2.0)

```python
divmod(0, 2)
```

    (0, 0)

## enumerate(_iterable_, _start=0_)

Link: [https://docs.python.org/3/library/functions.html#enumerate](https://docs.python.org/3/library/functions.html#enumerate)

Retorna um objeto _enumerate_. O argumento _iterable_ deve ser uma sequência, um _iterator_, ou outro objeto que suporte iteração. O método `__next__()` do iterador retornado pelo `enumerate()` retorna uma tupla, contendo um contador (do parâmetro _start_ é do padrão _0_) e os valores obtidos por ir percorrendo sobre o iterador.

```python
seasons = ['Spring', 'Summer', 'Fall', 'Winter']
list(enumerate(seasons))
```

    [(0, 'Spring'), (1, 'Summer'), (2, 'Fall'), (3, 'Winter')]

```python
list(enumerate(seasons, start=1))
```

    [(1, 'Spring'), (2, 'Summer'), (3, 'Fall'), (4, 'Winter')]

Essa função é o equivalente a isso:

```python
def enumerate(sequence, start=0):
    n = start
    for elem in sequence:
        yield n, elem
        n += 1
```

## eval(_expression_, _globals=None_, _locals=None_)

Link: [https://docs.python.org/3/library/functions.html#eval](https://docs.python.org/3/library/functions.html#eval)

Essa função é usado para executar expressões python que são passados via string para o argumento _expression_. Caso queria passar variaveis globais e locais é só passar um dicionário para os argumentos _globals_ e _locals_ respectivamente.

```python
x = 1
eval('x+1')
```

    2

Se o dicionário local for omitido, o padrão será o dicionário global. Se ambos os argumentos forem omitidos, a expressão será executada no ambiente aonde o `eval()` foi chamado. O valor de retorno é o resultado da expressão avaliada. Erros de sintaxe são reportados como exceções.

Essa função pode ser usado para executar objetos _code_ (como criamos pela função [compile()](https://docs.python.org/3/library/functions.html#compile)), assim, passando esse tipo de objeto vai ser substituido pela string que usamos antes. Se o objeto _code_ foi compilado usando o argumento _'exec'_, o valor de retorno da função `eval()` será `None`.

## exec(_object_[, _globals_[, _locals_]])

Link: [https://docs.python.org/3/library/functions.html#exec](https://docs.python.org/3/library/functions.html#exec)

Essa função suporta execução dinâmica de código Python. O parâmetro _object_ deve ser um string ou um objeto _code_. Se for um string, ele é parseado como uma declação python em que é executado (a não ser que ocorra um erro de sintaxe). Se for um objeto _code_, ele é simplesmente executado. Em todos os casos, o código executado é esperado que seja válido para uma entrada de arquivo. Tenha cuidado com as declaração `return` e `yield`, que não pode ser usados fora das definições da função, mesmo no contexto do código passado para o `exec()`. O valor retornado é `None`.

Em todos os casos, se as partes opcionais foram omitidos, o código é executado no escopo atual. Se somente for passado valores globais, ele deve ser um ducionário, que deve ser usado tanto para variáveis globais e locais. Se for passado globais e locals, eles serão usados para o que foi definidos. Lembre-se que globais e locais a nível de módulo é o mesmo dicionário. Se o exec retorna dois objetos separados como globais e locais, o código deve ser executado como se tivesse sido incorporado em uma definição de classe.

Se o dicionário de globais não tiver um valor para a chave `__builtins__`, uma referência para o dicionário do módulo built-in é inserido. Dessa forma, você pode controlar os builtins que são disponíveis para serem executados no código, inserido seu próprio dicionário `__builtins__` dentro de _globals_ antes de passar para o `exec()`.

```python
exec('x+1')
print(x)
```

    1

```python
code = """
def ola_mundo():
    print('Ola Mundo!')
"""
exec(code)

ola_mundo()
```

    Ola Mundo!

## filter(_function_, _iterable_)

Link: [https://docs.python.org/3/library/functions.html#filter](https://docs.python.org/3/library/functions.html#filter)

Constrói um iterador em cima dos elementos do parâmetro _iterable_, para que retorne `True`. Esse iterável deve ser uma sequência, um container que suporta iteração ou um iterador simplesmente. Se o parâmetro _function_ for `None`, a função de indentidade é assumida, ou seja, todos os elementos que são iteráveis irá retornar `False`.

```python
def eh_par(value):
    if value % 2 == 0:
        return True
    return False

numeros = [0, 1, 2, 3, 4]
resultado = filter(eh_par, numeros)
```

Note que o `filter(function, iterable)` é equivalente uma expressão geradora `item for item in iterable if function(item))` se a função não for `None`. Caso contrário equivale a `(item for item in iterable if item)`, em caso de que a função for `None`.

```python
numeros = [0, 1, 2, 3, 4]
resultado = filter(lambda num: True if num % 2 == 0 else False, numeros)

numeros_pares = [numero for numero in resultado]
print(numeros_pares)
```

    [0, 2, 4]

## class float(_[x]_)

Link: [https://docs.python.org/3/library/functions.html#float](https://docs.python.org/3/library/functions.html#float)

Retorna um numéro de ponto flutuante construído por um número ou string enviado ao parâmetro _x_.

Se o argumento é um string, ele deve conter um número decimal, opcionalmente precedido por um sinal, e opcionalmente embarcado com espaços. O sinal opcional deve ser **+** ou **-**; um sinal **+** não tem efeito no valor produzido. O argumento pode ter um string representando _NaN_ (not-a-number), ou um positivo e negativo infinito. Mais precisamente, a entrada deve estar em conformidado com a gramática abaixo:

```
sign           ::=  "+" | "-"
infinity       ::=  "Infinity" | "inf"
nan            ::=  "nan"
numeric_value  ::=  floatnumber | infinity | nan
numeric_string ::=  [sign] numeric_value
```

Aqui o `floatnumber` é uma forma literal do python para pontos flufuantes, descritos em [Pontos flutuantes literais](https://docs.python.org/3/reference/lexical_analysis.html#floating). Caso não seja significante, então poe exemplo, _"inf"_, _"Inf"_ e _"iNfINity"_ são todos grafias aceitáveis para infinito positivo.

Por outro lado, se o argumento é um inteiro ou um número de ponto flutuantes, esse número do tipo _float_ com o mesmo valor (com ponto de precisão do Python) é retornado. Se o argumento é fora do intervalo do tipo float da linguagem, um `OverflowError` deve ser disparado.

Para objetos comuns Python, o nosso objeto `x` por exemplo, ao ser usado em `float(x`) vai ser delegado para a função especial `__float__()`.

Se não tiver nenhum argumento para a função, é retornado o valor _0.0_.

Segue alguns exemplos:

```python
print("float('+1.23'): ", float('+1.23'))

print("float('   -12345\\n'): ", float('   -12345\n'))

print("float('1e-003'): ", float('1e-003'))

print("float('+1E6'): ", float('+1E6'))

print("float('+Infinity'): ", float('+Infinity'))

print("float('-Infinity'): ", float('-Infinity'))
```

    float('+1.23'):  1.23
    float('   -12345\n'):  -12345.0
    float('1e-003'):  0.001
    float('+1E6'):  1000000.0
    float('+Infinity'):  inf
    float('-Infinity'):  -inf

## format(_value_ _[, format__spec]_)

Link: [https://docs.python.org/3/library/functions.html#format](https://docs.python.org/3/library/functions.html#format)

Converte o parâmetro _value_ para um formato representável, controlado parâmetro `format_spec`. A intepretação desse formato vai depender do tipo de valor passado para o primeiro parâmetro. No entanto possui uma sintaxe de formato padrão que é usado pelas funções built-in e que deve ser levado em consideração. Veja mais sobre eles na seção [Mini-linguagem de especificação de formatos](https://docs.python.org/3/library/string.html#formatspec).

Segue alguns exemplos:

```python
print("Número inteiro: ", format(2))

print("Número de ponto flutuante: ", format(2, 'f'))

print("Número binário: ", format(4, 'b'))

print("Número octal: ", format(9, 'o'))

print("Número hexadecimal: ", format(12, 'X'))
```

    Número inteiro:  2
    Número de ponto flutuante:  2.000000
    Número binário:  100
    Número octal:  11
    Número hexadecimal:  C

## class frozenset([_iterable_])

Link: [https://docs.python.org/3/library/functions.html#func-frozenset](https://docs.python.org/3/library/functions.html#func-frozenset)

Retorna um novo objeto `frozenset`, opcionalmente com elementos retirados do parâmetro _iterable_. O `frozenset` é uma classe built-in. Para mais informações é explicado veja [frozenset]() e [Tipos Set - set, frozen](), que terá o link em breve.

## getattr(_object_, _name_[, _default_])

Link: [https://docs.python.org/3/library/functions.html#getattr](https://docs.python.org/3/library/functions.html#getattr)

Retorna o valor de um atributo nomedo em um objeto que foi passado no parâmetro _object_. O atributo _name_ é um string que representa o nome desse atributo. Caso esse atributo não exista, é retornado o valor padrão definido no parâmetro _default_, caso contrário um `AttributeError` é disparado. O `getattr(foo, 'x')` é equivalente a `foo.x`. Veja o exemplo abaixo:

```python
class Foo(object):
    def __init__(self):
        self.attrA = 'Foo'
        self.attrB = 'Bar'

foo = Foo()
getattr(foo, 'attrA')
```

    'Foo'

```python
# Passando o valor default em caso de não encontrar
# o atributo

getattr(foo, 'attrC', 'Nenhum atributo encontrado')
```

    'Nenhum atributo encontrado'

```python
# Se o atributo não for encontrado, como
# não foi passado um valor default, a exceção
# AttributeError será disparado.

getattr(foo, 'attrX')
```

    ---------------------------------------------------------------------------

    AttributeError                            Traceback (most recent call last)

    <ipython-input-57-2a88621c904c> in <module>()
          3 # AttributeError será disparado.
          4
    ----> 5 getattr(foo, 'attrX')


    AttributeError: 'Foo' object has no attribute 'attrX'

## globals()

Link: [https://docs.python.org/3/library/functions.html#globals](https://docs.python.org/3/library/functions.html#globals)

Retorna um dicionário representando a tabela de símbolos globais que existem. Será sempre um dicionário de símbolos do módulo atual (dentro de uma função ou método, esse é o módulo onde ele é definido, não o módulo que é chamado).

## hasattr(_object_, _name_)

Link: [https://docs.python.org/3/library/functions.html#hasattr](https://docs.python.org/3/library/functions.html#hasattr)

Verifica se determinado atributo _name_ existe no objeto _object_. Os argumentos devem ser um objeto e uma string, respectivamente. Como falado, se encontrado o retorno da função é `True`, e caso contrário é retornado `False`.

```python
hasattr(foo, 'attrA')
```

    True

```python
hasattr(foo, 'otherAttr')
```

    False

## hash(_object_)

Link: [https://docs.python.org/3/library/functions.html#hash](https://docs.python.org/3/library/functions.html#hash)

Retorna o valor _hash_ do objeto passado no parâmetro _object_. O valor _hash_ são inteiros e eles são usados para facilmente comparar chaves de dicionários durante uma análise do dicionário. Valores numéricos que comparam sua igualdade são do mesmo valor _hash_ (mesmo se eles são de tipos diferentes, como o caso de 1 e 1.0).

```python
print("hash(1): ", hash(1))

print("hash(1.000): ", hash(1.000))

print("hash(foo)", hash(foo))
```

```
hash(1):  1
hash(1.000):  1
hash(foo) -9223363277595646492
```

No próximo post, vamos abordar a terceira parte das funções built-in. Até mais!
