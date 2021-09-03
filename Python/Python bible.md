# Python bible :snake:

## 4. More Control Flow Tools

### 4.1 If statements

Python trabalha de uma forma parecida com outras linguagens em relação a if statements:

* **if** = if
* **elif** = else if
* **else**  = else

É possível ter zero elifs, e o else é completamente opcional. Ou seja, você pode utilizar apenas um if. Uma sequencia de if... elif... elif... seria o substituto para um switch case em Python.

### 4.2 For statements

O for certamente é a característica mais singular da línguagem. Ao invés de sempre interagir com uma progressão aritmética de números, em Python temos outras possibilidades mais elegantes, como a interação através de todos os itens de uma lista:

```python
words = ['cat', 'window', 'snake']
for word in words:
    print(word)

# OUTPUT: cat
#		  window
#		  snake
```

Interação em um dicionário:

```python
# Interagindo em uma cópia
for user, status in users.copy().items():
    if status == 'inactive':
        del users[user]
# Interagindo em uma nova coleção (dict)
active_users = {}
for user, status in users.items():
    if status == 'active':
        active_users[user] = status
# Ambas as soluções tem o mesmo resultado final.
```

### 4.3 The range() function

Como mencionado, em Python podemos interagir diretamente sobre uma estrutura de dados, como listas ou dicionários. Mas também temos a possibilidade de utilizar um for loop da forma mais comum entre outras linguagens, com uma sequência de números:

```python
for i in range(3)
	print(f'INDEX: {i}')
# OUTPUT: 0
#		  1
#         2
```

Também podemos manipular a partir de qual número começará, bem como o tamanho dos "passos" para cada interação:

```python
>>> list(range(5, 10))
[5, 6, 7, 8, 9]

>>> list(range(0, 10, 3))
[0, 3, 6, 9]

>>> list(range(-10, -100, -30))
[-10, -40, -70]
```

Para interagir pelos índices de uma lista, poderíamos utilizar `range()` combinado de `len()`. Em alguns casos todavia, talvez seja mais eficiente o uso de `enumerate()`:

```python
seasons = ['Spring', 'Summer', 'Fall', 'Winter']

list(enumerate(seasons))
[(0, 'Spring'), (1, 'Summer'), (2, 'Fall'), (3, 'Winter')]

list(enumerate(seasons, start=1))
[(1, 'Spring'), (2, 'Summer'), (3, 'Fall'), (4, 'Winter')]
```

```python
chars = ['a', 'b', 'c']
# Sem enumerate:
for i in range(len(chars))
	print(i, chars[i])
# Com enumerate:
for index, value in enumerate(chars):
    print(index, value)
```

Vale lembrar que `range()` pode ser confundido com uma lista, mas não é. Ele na verdade seria um objeto que quando interagido retorna os valores de uma desejada sequencia. Todavia, ele não faz uma lista, dessa forma nos salvando espaço em memória. Chamamos tais objetos de iterables, que são ótimos como alvos de funções e construtores que esperam algo do qual possam obter itens sucessivos.

```python
# EXEMPLOS DE RANGE
range(10)
# OUTPUT: range(0, 10)
list(range(10))
# OUTPUT: [0, 1, 2, 3, ...]
sum(range(10))
# OUTPUT: 45
for _ in range(10)
```

### 4.4 Break and continue statements, and else clauses on loops

Break, como na maior parte das linguagens de programação, simplesmente quebra ou para um loop. Já o continue, ao invés de parar um loop, ele dá para a execução daquela interação, e segue para a próxima. Break e continue são muito utilizados em conjunto com estrutura condicionais.

Podemos utilizar elses dentro de loops em Python. É executado quando:

* **For**: Foi rodado todas as interações de um iterable.
* **While**: Quando a condição se torna falsa.

```python
# for loop
for n in range(2, 10):
    for x in range(2, n)
    	if n % x == 0:
            print(n, 'equals', x, '*', n//x)
        	break
     else:
        # já foi rodado todo o for loop de cima
        print(n, 'is a prime number')
        
# while loop
n = 0
while n < 5:
    n += 1
    print(n)
else:
    print("Loop finished.")
```

### 4.5 Pass statement

O pass é a mais simples de todas. Ele simplesmente não faz nada. Já que em python não podemos deixar algo como uma função, ou uma classe vazia por conta de identação, utilizamos o pass.

```python
def super_useful_function(*args, **kwargs):
    pass # lembrar de implementar essa funçaõ!
```

### 4.6 Defining functions

A primeira declaração em uma função Python pode ser uma string literal. Essa string é a documentação da função, diz sobre o propósito dela, sobre os argumentos, etc. 

```python
def fib(n):
    """Printa uma sequência fibonacci até n."""
    a, b = 0, 1
    while a < n:
        print(a, end=' ')
        a, b = b, a+b
    print()
```

Em funçõoes também não podemos diretamente atribuir valores diretamente para variáveis globais e de outras funções dentro da principal. Para isso, devemos usar as keywords **global**, ou **nonlocal**. Todavia, elas ainda podem ser referênciadas sem a necessidade de keywords adicionais.

```python
x = 'x'
def myfun1():
    global x
    x = 'w'
	y = 'y'
    def myfun2():
        nonlocal y
        y = 'z'
```

Atribuir uma função para uma variável, associa a função para aquela variável.

```python
f = fib
f(50)
# OUTPUT: 0 1 1 2 3 4 8 13 21 34
```

### 4.7.1 Default argument values

Uma funcionalidade muito útil na declaração de argumentos para função é passar valores padrão para algumas delas, para caso o usuário não informe. 

```python
def ask_ok(prompt, retries=4, reminder='Please try again!'):
    while True:
        ok = input(prompt)
        if ok in ('y', 'ye', 'yes'):
            return True
        if ok in ('n', 'no', 'nop', 'nope'):
            return False
        retries = retries - 1
        if retries < 0:
            raise ValueError('invalid user response')
        print(reminder)
```

### 4.7.2 Keyword arguments

Podemos passar também argumentos do tipo `*args`, e `**kwargs`. O primeiro te dá a possibilidade de passar quantos argumentos por posição você quiser na função, já o segundo quantos argumentos com nome (ou keywords) você quiser.

```python
def fun(*args, **kwargs):
    for arg in args:
        print(arg)
    for kw in kwargs:
        print(kw, kwarg[kw])
fun('O', 'l', 'á', num=10)
# OUTPUT: O l á
#		  num 10
```



### 4.7.3 Special parameters

Por questões de leitura e performance, em Python podemos restringir as formas que os argumentos serão passados em nossas funções. Fazemos isso utilizando os simbolos **/** e *****.

```
def f(pos1, pos2, /, pos_or_kwd, *, kwd1, kwd2):
      -----------    ----------     ----------
        |             |                  |
        |        Positional or keyword   |
        |                                - Keyword only
         -- Positional only
```

```python
def func(name, /, age, *, color):
	print(f'Your name is {name}. Your age is {age}. Your color is {color}.')
func('Gabriel', age=19, color='purple')

# APENAS POR POSICAO
def func(name, /)
	print(name)
func('Gabriel')

# POR PORSICAO OU KEYWORD
def func(age)
	print(age)
func(age=19) # OU func(19)

# APENAS POR KEYWORD
def func(*, color):
    print(color)
func(color='purple')
```



### 4.7.5 Unpacking argument lists

Parecido com o spread operator do Javascript, podemos utilizar ***** em uma lista para "desempacotar" os items dela. 

```python
nums = [1, 2, 3]

print(nums)
# OUTPUT: [1, 2, 3]
print(*nums)
# OUTPUT: 1 2 3
```



### 4.7.6 Lambda expressions

Como em outras linguagens, Lambda são funções curtas e anonimas. Aqui podemos declarar uma Lambda utilizando a **lambda** keyword.

```python
y = lambda x: x*2
y(10)
# OUTPUT: 20

sum_nums = lambda x, y: x+y
sum_nums(10, 20)
# OUTPUT: 30
```



### 4.7.7 Documentation strings

Existem algumas convenções de como strings para documentação de funções devem ser feitas.  A primeira linha deve ser sempre uma descrição simples e concisa da função daquela função.  Caso haja a necessidade de mais linhas na documentação, a segunda deverá sempre estar em branco (sendo assim um pulo de linha), e o restante deve ser composto de um ou mais paragrafos para detalhar melhor a função.

Também podemos acessar a documentação de uma função através do atributo `__doc__`.

```python
def fun():
    """Não faz nada.
    
    Realmente não faz nada.
    """
    pass
print(fun.__doc__)
```



### 4.7.8 Function annotations

Anotações de funções são metadados opcionais. Com isso, você pode definir quais serão os tipos dos argumentos da função, e qual o tipo de retorno.

Para a primeira, devemos passar com `:` o tipo após a declaração do argumento. Já o tipo de retorno pode ser especificado usando `->`.

```python
def fun(name: str, movie: str='Star Wars') -> str:
    return f'Hello {name}, I think {movie} is a great movie too!'
fun('Gabriel', 'Harry Potter')
```





## 5. Data Structures

### 5.1 More on lists

A estutura de dados lista em Python possui alguns métodos:

* **append(x)**: Adiciona um item no final da lista. Equivalente a `a[len(a):] = [x]`.
* **extend(iterable)**: Extende a lista adicionando items de um iterable. Equivalente a `a[len(a):] = iterable`.
* **insert(i, x)**: Insire um item em uma posição específica. O primeiro argumento seria o index de um elemento, com isso, o valor desejado (segundo argumento) seria inserido antes do index informado.  Logo, `a.insert(0, x)` inseri um item na frente do primeiro elemento da lista. Equivalente a `a.append(x)`.
* **remove(x)**: Remove um item da lista cujo o valor é igual ao argumento passado. Levanta um *ValueError* caso não existe o item procurado para a deleção.
* **pop([i])**: Remove um item de uma posição em específico. Caso nenhuma posição seja informada, pop removerá o último item da lista.  Vale lembrar que os [] no parametro da função indica que ele é opcional.

* **clear()**: Remove todos os itens da lista. Equivalente a `del a[:]`.

* **index(x[, start[, end]])**: Retorna o index de um valor procurado na lista. Levanta o erro *ValueError* caso não exista nenhum item.

  Os argumentos opcionais *start* e *end* são interpretados como um slice notation. Basicamente são utilizados para reduzir o tamanho da busca na lista. Isso é muito útil para algoritmos mais complexos reduzirem o tempo de procura, por exemplo.

* **count(x)**: Retorna o número que x aparece na lista.

* **sort(*, key=None, reverse=False)**: Ordena os itens de uma lista. É possível passar uma função lambda em *key*, para uma ordenação personalizada da lista. Já o reverse serve para o que o nome indica - inverter a lista.

* **reverse()**: Inverte os elementos de uma lista.

* **copy()**: Retorna uma cópia superficial (shallow copy) da lista. Equivalente a `a[:]`.



### 5.1.1 Using Lists as Stacks

Com os métodos de lista que aprendemos, podemos facilmente utilizar a estrutura de dados Pilha (Stack). Como pilhas utilizam o principio **LIFO (Last-in, First-out)**, adicionaremos um item no topo da pilha utilizando `append()`, e para retirar `pop()`.

```python
stack = [7, 5, 3]
stack.append(1)
print(stack)
# OUTPUT: [7, 5, 3, 1]

stack.pop()
# OUTPUT: [7, 5, 3]
```



### 5.1.2 Using Lists as Queues

Também podemos utilizar listas como Filas (Queues), com o princípio **FIFO (First-in, First-out)**. Todavia, listas não são tão eficazes para esse propósito, visto que para fazermos inserções ou remoções do começo é muito lento, já que todos os elementos da lista precisam ser movidos (coisa que não acontece quando removemos e inserimos do final).

Então, para implementar uma fila, podemos utilizar o **colections.deque**, que foi feito para ter inserções e remoções mais rápidas em ambas as formas:

```python
from collections import deque
queue = deque(['Gabriel', 'Carlos', 'Mariana'])
queue.append('Jorge')
queue.popleft()
print(qeue)
# OUTPUT: deque(['Carlos', 'Mariana', 'Jorge'])
```



### 5.1.3 List Comprehensions

List comprehensions nos dá uma forma mais concisa de criar listas. Com ela, podemos interagir com cada item da lista:

```python
squares = []
for x in range(10):
    squares.append(x**2)
print(squares)
[0, 1, 4, 9, 16, 25, ...]
```

A forma apresentada a cima é a mais simples de todas. Porém, temos outros caminhos mais concisos e elegantes para chegar no mesmo resultado:

```python
# Usando map
squares = list(map(lambda x: x**2, range(10)))

# Usando for in
squares = [x**2 for x in range(10)]
```

Mais um exemplo de todo o potencial de list comprehensions:

```python
[(x, y) for x in [1,2,3] for y in [3,1,4] if x != y]

# É igual a

for x in [1,2,3]:
    for y in [3,1,4]:
        if x != y:
            list.append((x, y))
```



### 5.1.4 Nested List Comprehensions

Também podemos utilizar esse conceito para trabalhar com nested lists:

```python
matrix = [
    [1, 2, 3, 4],
    [5, 6, 7, 8],
    [9, 10, 11, 12]
]
[[row[i] for row in matrix] for i in range(4)]
# OUTPUT: [[1, 5, 9], [2, 6, 10], [3, 7, 11], [4, 8, 12]]
```

Isso é possível por que a expressão inicial de uma list comprehension pode ser qualquer expressão arbitrária, incluindo outra list comprehension.

Porém, temos funções feitas especialmente para isso no Python, como o `zip()`: 

```python
list(zip(*matrix))
# OUTPUT: [(1, 5, 9), (2, 6, 10), (3, 7, 11), (4, 8, 12)]
# O asterisco antes de matrix é para desempacotar a lista
```

A função `zip()` retorna um objeto zip, que é um iterator de tuplas onde o primeiro item de cada iterator passado é pareado junto. Após isso, o segundo item de cada um é pareado, e assim por diante.

```python
a = ('John', 'Charles')
b = ('Jenny', 'Christy', 'Vicky')

x = zip(a, b)
print(x)
# OUTPUT: (('John', 'Jenny'), ('Charles', 'Christy'))
```



### 5.2 The del statement

Existe uma forma de remover um item de uma lista passando o index ao invés do valor: utilizando **del**. Ele é diferente de `pop()` que retorna um valor. Com del podemos também remover pedaços ou slices de uma lista, ou até mesmo limpar a lista por completo.

```python
a = [-1, 1, 66.25, 33, 33, 123.5]
del a[0]
# OUTPUT: [1, 66.25, 33, 33, 123.5]
del a[2:4]
# OUTPUT: [1, 66.25, 123.5]
del a[:]
# OUTPUT: []
```

Del também pode ser usado para remover variáveis inteiras, ou até mesmo classes:

```python
del var
del My_class
```



### 5.3 Tuples and Sequences

Uma tupla consiste de valores que, uma vez definidos, não poderão ser alterados. Ou seja, uma tupla é imutável: não podemos deletar um item, adicionar novos, ou reescrever os que já foram definidos. Existem múltiplas formas de se definir uma tupla:

```python
t = (1, 2)
t = tuple(1, 2)
t = 1, 2
```

Uma funcionalidade muito utilizada com tuplas é o empacotamento e desempacotamento de itens:

```python
t = 10, 'ten', 20, 'twenty'
a, b, c, d = t
# a = 10
# b = 'ten'
# c = 20
# d = 'twenty'
```

Algo problemático com tuplas é a declaração de um único item. Para isso, temos uma semântica um tanto quanto estranha:

```python
t = 1,
# Precisamos colocar uma virgula após o valor para definir ele como uma tupla.
```



### 5.4 Sets

Um set é uma estrutura de dados que não contém dados repetidos Sets são muito utilizados quando se quer remover itens duplicados de uma lista.  Eles também aceitam operações matemáticas como união, interseção, diferença, etc.

Na declaração de um set podemos utilizar a função `set()` ou simplesmente passar os valores entre {}. Porém, para declarar um set vazio, devemos usar a função, e não {}.

```python
basket = {'apple', 'orange', 'apple', 'pear'}
print(basket)
# OUTPUT: {'apple', 'orange', 'pear'}
```

```python
a = set('abracadabra')
b = set('alacazam')

print(a)
# OUTPUT: {'a', 'r', 'b', 'c', 'd'}

# Letras de a, porém tirando as letras contidas em b
print(a - b)
# OUTPUT: {'r', 'd', 'b'}

# Letras em a ou b: todas as letras
print(a | b)
# OUTPUT: {'a', 'c', 'r', 'd', 'b', 'm', 'z', 'l'}

# Letras em a e b
print(a & b)
# OUTPUT: {'a', 'c'}

# Letras em a ou b, mas não em ambos
print(a ^ b)
# OUTPUT: {'r', 'd', 'b', 'm', 'z', 'l'}
```

Similar a list comprehensions, set comprehensions também são suportadas:

```python
a = {x for x in 'abracadabra' if x not in 'abc'}
print(a)
# OUTPUT:{'r', 'd'}
```



### 5.5 Dictionaries

Uma das estruturas de dados mais útil de Python é o dicionário (ou, para os íntimos, dict). Dicts são muitas vezes achados em outras linguagens como "memórias associativas", ou "array associativas". Ao contrário de sequencias, que são indexadas por uma gama de números, dictionaries são indexados por *keys*, que podem ser de qualquer tipo imutável: normalmente números ou strings. Tuplas também podem ser utilizadas caso elas contenham apenas strings, números, ou outras tuplas.

Podemos remover itens de um dicionário usando del. E, caso passarmos um novo valor para uma key já existente, o valor anteriormente armazenado por ela será reescrito. Também vale lembrar que caso formos acessar uma key que ainda não existe no dicionário, será levantado um erro de *KeyError*.

Já na declaração de dicts podemos utilizar tanto {}, quanto o método `dict()`:

```python
d = {'one': 1, 'two': 2}

l = [('one', 1), ('two', 2)]
d = dict(l)
```

Podemos fazer dictionaries comprehensions também:

```python
{x: x**2 for x in (2, 4, 6)}
# OUTPUT: {2: 4, 4: 16, 6: 36}
```

Utilizar `list(d)` em um dicionário retornará uma lista de todas as keys usadas nele. Caso queira essa lista ordenada, utilize `sorted(d)`. Por fim, para saber se uma key existe no dicionário use in.

```python
tel = {
    'Gabriel': 10283,
    'Joao': 84729,
    'Camila': 73719
}

list(tel)
# OUTPUT: ['Gabriel', 'Joao', 'Camila']

sorted(tel)
# OUTPUT: ['Camila', 'Gabriel', 'Joao']

'Camila' in tel
# OUTPUT: True

'Joao' not in tel
# OUTPUT: False
```



### 5.6 Looping Techniques

Vamos falar um pouco sobre loops nas estruturas de dados que foram apresentadas. Quando loopando em dicionários, a key e o value podem ser recuperados simultaneamente usando o método `items()`.

```python
knights = {'gallahad': 'the pure', 'robin': 'the brave'}
for k, v in knights.items():
    	print(k, v)
# OUTPUT: gallahad the pure
#		  robin the brave
```

Quando loopando em uma sequência, o index e o valor correspondente podem ser retirados utilizando a função `enumerate()`.

```python
fruits = ['apple', 'pear', 'watermelon']
for i, n in enumerate(fruits):
    print(i, v)
# OUTPUT: 0 apple
#		  1 pear
#		  2 watermelon	
```

Para loopar em duas sequências ou mais ao mesmo tempo, as entradas podem ser pareadas usando a função `zip()`.

```python
question = ['name', 'quest', 'favorite color']
answers = ['lancelot', 'the holy grail', 'pink']
for q, a in zip(questions, answers):
    print(f'What is your {q}? It is {a}.')
# OUTPUT: What is your name? It is lancelot.
#		  What is your quest? It is the holy grail.
#  		  What is your favorite color? It is pink.
```

Para realizar um loop de forma reversa nos elementos, utilizamos a função `reversed()`.

```python
for i in reversed(range(1, 10, 2))
	print(i)
# OUTPUT: 9
#		  7
#		  5 ...
```

Algo muito interessante também é o combinamento de algumas dessas funções. Por exemplo, em uma lista com frutas repetidas, podemos primeiramente transformá-las em um set para remover as duplicatas, e depois ainda aplicar a função `sorted` para organizar os itens na lista.

```python
basket = ['apple', 'orange', 'apple', 'pear', 'orange']
for f in sorted(set(basket)):
    print(f)
# OUTPUT: apple
#		  orange
#		  pear
```



### 5.7 More on Conditions

Ok, já sabemos como as condições mais básicas funcionam: **and**, **or**, e **not**. Porém, ainda existem algumas coisas a serem ditas quanto a condições.

Por exemplo, é possível passar o resultado de uma comparação ou outra expressão booleana para uma variável:

```python
string1, string2, string3 = '', 'Trondheim', 'Hammer Dance'
non_null = string1 or string2 or string3
# OUTPUT: 'Trondheim'
```

Também é importante ressaltar que diferentemente de C, passar valores dentro de uma expressão deve ser feito explicitamente com o wairus operator **:=**.



## 6. Modules

Caso você saia do interpretador do Python, as definições e todo o trabalho feito lá será perdido. Portanto, se você quiser escrever programas mais longos e complexos, será necessário um editor de códigos para a criação de um *script*. Conforme seu programa for ficando maior, você provavelmente precisará separá-lo em várias pequenas partes, para ter uma manutenção sem dor de cabeça.

Para suportar esse tipo de conexão entre arquivos, em Python temos algo chamado de módulos, onde cada arquivo do programa é considerável um módulo. Com isso, podemos importar diferentes módulos em outros, fazendo essa conexão entre arquivos.

O nome de um módulo é essencialmente aquele que é nomeado nele, sem a extensão `.py`. Podemos checar o nome de um módulo através da variável global `__name__`.



### 6.1 More on Modules

Um módulo pode conter declarações executáveis e definições de funções. As declarações tem por objetivo inicializar o módulo. Elas são executadas somente na primeira vez que o nome do módulo é encontrado em um import (elas também são inicializadas caso o arquivo seja rodado como um script).

Cada módulo tem usa própria tabela de simbolos privada, que são utilizadas nos nomes das funções, e variáveis. Logo, o autor de um módulo pode usar variáveis globais sem se preocupar com confrontos acidentais entre os nomes das variáveis. Por outro lado, se você souber o que está fazendo, pode acessar as variáveis globais de um módulo da mesma forma que acessaria uma função: `module.var`.

Existe uma variante do import em Python, que importa diretamente os itens desejados de um módulo:

```python
from fibo import fun1, fun2
fun1(500)
```

Dessa forma o nome do módulo não é inserido em nosso código, apenas os itens passados no import. Ou seja, podemos utilizar eles como se fossem do nosso próprio módulo, sem precisar colocar o nome do módulo original.

Também temos outra variante para importar todos os nomes contidos naquele módulo:

```python
from fibo import *
fun1(500)
fun2(100)
```

Outro uso interessante é a utilização de alias dentro das importações:

```python
import fibo as fib
fib.fun1(500)

from fibo import fun1 as fibonacci
fibonacci(500)
```



### 6.1.1 Executing modules as scripts

Quando rodamos um programa python com `python fibo.py <arguments>`, o código no módulo será executado, mas a variável global `__name__` terá o valor de `"__main__"`. Com isso, podemos inicializar certas partes de código apenas se nosso módulo for executado como script:

```python
if __name__ == "__main__":
    import sys
    fun1(int(sys.argv[1]))
```



### 6.1.2 The Module Search Path

O sistema de pesquisa por módulos funciona da seguinte forma: Quando um módulo chamado *control* é importado, primeiramente o interpretador procurará por um módulo chamado Control dentro dos built-in modules. Caso não encontre, ele então procurará por um arquivo com nome *control.py*, em uma lista de diretórios fornecido pela variável `sys.path`. Essa variável é inicializada das localizações:

* Diretório contendo o script executado.
* PYTHONPATH (uma lista de diretórios, com a mesma sintaxe que a variável PATH do shell).
* O padrão dependente da instalação.

Após a inicialização, um programa Python pode modificar o caminho de `sys.path`:

```python
import sys
sys.path.append('/ufs/guido/lib/python')
```





### 6.1.3 "Compiled" Python files

Para acelerar os módulos, Python tem um sistema de cache para armazenar a versão compilada de cada módulo em uma pasta chamada `__pycache__`, com o nome de `module.version.pyc`. Por exemplo: `__pycache__/control.cpython-33.pyc`.

É checado a data de modificação da fonte, contra a da versão compilada para saber se é necessário recompilar o módulo. Isso é um processo completamente automático.

Algumas outras coisas para se atentar:

* É possível utilizar as opções `-0` e `-00` para reduzir o tamanho dos módulos compilados. A opção `-0` remove algumas declarações, e o `-00` remove algumas declarações e as strings `__doc__`. Entretetando, é recomendado utilizar isso somente se você tiver certeza do que está fazendo.
* Um programa não roda mais rápido por ser `.pyc`. A única diferença dele para um `.py` é que a versão compilada é carregada mais rapidamente (o programa executa mais rapidamente, porém não tem um ganho de performance).
* O módulo **compileall** pode criar arquivos `.pyc` para todos os módulos no diretório.



### 6.2 Standard Modules

Em Python temos alguns módulos padrões que já vem com a própria linguagem. Esses possibilitam operações que não são partes fundamentais da linguagem, mas extremamente úteis. 

```python
# Módulo padrão Python sys (system)
import sys

sys.ps1
# OUTPUT: '>>> '
sys.ps2
# OUTPUT: '... '

sys.path
# OUTPUT: ['', '/usr/lib/python39.zip', '/usr/lib/python3.9', ...]
```



### 6.3 The dir() Function

A função `dir()` é usada para descobrir quais nomes um módulo define. Ele retorna uma lista ordenada de strings:

```python
import fibo sys
dir(fibo)
# OUTPUT: ['__name__', 'fun1', 'fun2']
dir(sys)
# OUTPUT: ['__breakpointhook__', '__displayhook__', '__doc__', '__excepthook__', '__interactivehook__', '__loader__', '__name__',...]
```

Sem argumentos, a função retorna os nomes definidos dentro módulo:

```python
a = [1, 2, 3]
b = [4, 5, 6]
dir()
# OUTPUT: ['__builtins__', '__name__', 'a', 'b']
```

Todavia, `dir()` não inclui o nome de variável e funções padrão (builtins). Se você quiser listar elas, precisará procurar pelo módulo **builtins**:

```python
import builtins
dir(builtins)
# OUTPUT: ['ArithmeticError', 'AssertionError', 'AttributeError', 'BaseException', 'BlockingIOError', ...]
```



### 6.4 Packages

Packages é uma forma de separar seus módulos mais organizadamente, utilizando **.** para navegar entre módulos. Por exemplo: em `import A.B` eu estaria importando um submódulo chamado B do package A. 

Para exemplificar o uso disso, vamos imaginar a seguinte situação: você está desenvolvendo uma biblioteca de áudio em Python, e para manter ela organizada, precisará tratar dos packages. 

```
Organização das pastas da biblioteca de áudio:

sound/                          Top-level package
      __init__.py               Initialize the sound package
      formats/                  Subpackage for file format conversions
              __init__.py
              wavread.py
              wavwrite.py
              aiffread.py
              aiffwrite.py
              auread.py
              auwrite.py
              ...
      effects/                  Subpackage for sound effects
              __init__.py
              echo.py
              surround.py
              reverse.py
              ...
      filters/                  Subpackage for filters
              __init__.py
              equalizer.py
              vocoder.py
              karaoke.py
              ...
```

No exemplo, usuários podem utilizar a biblioteca da seguinte forma:

```python
import sound.effects.echo
sound.effects.echo.echofilter(input, output, delay=0.7, atten=4)

from sound.effects import echo
echo.echofilter(input, output, delay=0.7, atten=4)

from sound.effects.echo import echofilter
echofilter(input, output, delay=0.7, atten=4)
```

Alguns pontos a se atentar: 

* Quando é preciso importar um package, o Python procurará por diretórios em `sys.path`, como já vimos anteriormente.

* O arquivo `__init__.py` é necessário para indicar que aquele diretório é de fato um package. No caso mais simples, o arquivo init pode ser vazio, sem conteúdo algum. Por outro lado, é possível executar um código de execução para o pacote, ou definir a variável `__all__`.

  

### 6.4.1 Importing * From a Package

O que acontece quando um usuário tenta importar tudo dentro do package com `from sound.effects import *`? Idealmente, ele esperará que isso retorne tudo contido em sound.effects. Todavia, isso pode levar um tempo considerável, e resultar em resultados inesperados.

A única solução é o autor da biblioteca tratar disso manualmente. Por padrão, se o arquivo `__init__.py` tiver definido a variável `__all__` tudo nela será levado como "padrão" para a importação do pacote. Em outras palavras, é o que será importado caso o usuário use `from sound.effects import *`.

```python
__all__ = ["echo", "surround", "reverse"]
```

Com isso, a importação geral, utilizando *****, resultará na importação de echo, surround e reverse. 

É importante lembrar que caso `__all__` não esteja definido, a importação geral não resultará na importação total do package. Apenas será executado qualquer código incluso em `__init__.py`, e a importação de nomes definidos lá.



### 6.4.2 Intra-package References

Para realizar a importação de packages inclusos no mesmo local de trabalho do código, utilizamos o **.** para referenciar os diretórios. Por exemplo, considere o módulo surround.py:

```python
from . import echo
from .. import formats
from ..filters import equalizer
```



### 6.4.3 Packages in Multiple Directories

Packages também suportam mais um atributo especial: `__path__`. Por padrão, essa variável é inicializada com o diretório contendo o arquivo `__init__.py`. Entretetanto podemos modificar isso, e passar um novo caminho. Fazer isso afetará futuras pesquisas por módulos e subpackages contidos no package.

Enquanto esse recurso não é usado com muita frequenta, ele pode ser utilizado para estender o conjunto de módulos em um pacote.



## 7. Input and Output

