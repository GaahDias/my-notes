# Clojure: Macros

## O que é um Macro?

Macros foram introduzidos em muitos dialetos de Lisp. Ultimamente, até mesmo linguagens fora do mundo funcional utilizam macros. Mas enfim, o que é um Macro?

De uma forma resumida, Macros são funções que recebem source code como input, e retornam source code como output. Isso permite que o compilador seja estendido pelo código do usuário. Poderíamos até mesmo dizer que um macro é uma "ferramenta geradora de código".

Falando de forma mais técnica, Macros são avaliados em compile time e produzem estruturas de dados que são compiladas para JVM bytecode. Esse bytecode então é executado em run time.

É possível criar funções muito poderosas com Macros, inclusive muitas funções da biblioteca core da linguagem são feitas com isso, como por exemplo: `when`, e `->`.



## Como criar um Macro

#### Defmacro, e quote (')

Para criar um Macro, utilizamos a função `defmacro`. Essa função recebe os seguintes parâmetros:

```clojure
(defmacro name doc-string? [params*] body)
```

Quando estamos construindo um Macro, normalmente queremos retornar uma lista de comandos - `(list)`. Também utilizamos bastante as aspas `'` para a não avaliação de símbolos.

```clojure
; macro for regular math (1 + 1, insted of + 1 1)
(defmacro reg-math [arg]
    (list (second arg) (first arg) (last arg)))
(reg-math (1 + 1))
```

No exemplo acima estamos basicamente invertendo os itens do argumento passado, para realizar a operação matemática. Ou seja, é passado `1 + 1`, porém transformamos isso em `+ 1 1`.

Isso não seria possível de uma forma tão reduzida em uma função normal, visto que precisaríamos checar para cada possibilidade (adição, substração, multiplicação, divisão...). Em nosso macro, estamos simplesmente pegando o símbolo matemático passado pelo usuário, e utilizando ele para produzir o código.

```clojure
; macro that check a condition, and do some stuff if it's true
(defmacro do-something [condition & body]
    (list 'if condition (cons 'do body)))
(do-something (< 1 2) (println "Print") (+ 1 1))
```

Nesse exemplo podemos ver o grande potencial de um macro. Nele, estamos primeiramente checando se uma condição é verdadeira, e, se for, executamos **código** passado pelo usuário. Ou seja, o usuário poderia passar basicamente qualquer coisa para ser executado com nosso macro.

#### Syntax quote, unquote, unquote splicing

Normalmente em Macros utilizamos muito a aspas `'`, para indicar o que não queremos avaliar de imediato. Tendo isso em mente, em Clojure podemos trabalhar de um modo diferente com macros: ao invés de selecionar o que não queremos avaliar, podemos deixar tudo dentro do macro não avaliado (com **`**), e apenas especificar o que queremos de fato avaliar (com **~** e **~@**).

```clojure
(defmacro unless
    "Similar to if but negates the condition"
    [condition & body]
    `(if (not ~condition)
         ~@body))
(unless (= 1 2) 
        "one does not equal to two"
        "one equals two... wait, what?")
```

Aqui temos algumas novas sintaxes:

* **Syntax quote (`)**

  Serve para indicar que uma expressão inteira não será avaliada, apenas alguns pontos em específico.

* **Unquote (~)**

  Indica quais símbolos dentro do Syntax quote serão avaliados.

* **Unquote splicing (~@)**

  Mesma utilidade do unquote, porém ele também desempacota uma lista. É muito útil para `[& args]`.

#### Macroexpand

É possível visualizar um macro de forma "expandida". Em outras palavras, ver como o seu código está sendo passado por de baixo dos panos.

```clojure
(macroexpand '(unless (= 1 2) true false))
; (if (clojure.core/not (= 1 2)) true false)
(macroexpand-1 '(unless (= 1 2) true false))
; (if (clojure.core/not (= 1 2)) true false)
```

A diferença entre `macroexpand` e `macroexpand-1` é que o primeiro irá expandir tudo, até mesmo possíveis outros macros que sejam chamados dentro do macro expandido. Já o segundo, expandirá apenas o macro desejado.

#### Unhygienic macros, e geração de símbolos

Quando escrevendo um macro, existe uma possibilidade dele interagir com vars de uma inesperada. Esses macros são conhecidos como unhygienic macros. Um exemplo seria através de [shadowing](https://en.wikipedia.org/wiki/Variable_shadowing):

> In [computer programming](https://en.wikipedia.org/wiki/Computer_programming), **variable shadowing** occurs when a variable declared within a certain [scope](https://en.wikipedia.org/wiki/Scope_(computer_science)) (decision block, method, or [inner class](https://en.wikipedia.org/wiki/Inner_class)) has the same name as a variable declared in an outer scope. At the level of identifiers (names, rather than variables), this is known as [name masking](https://en.wikipedia.org/wiki/Name_resolution_(programming_languages)#Name_masking). This outer variable is said to be shadowed by the inner variable, while the inner identifier is said to *mask* the outer identifier. This can lead to confusion, as it may be unclear which variable subsequent uses of the shadowed variable name refer to, which depends on the [name resolution](https://en.wikipedia.org/wiki/Name_resolution_(programming_languages)) rules of the language.

Para prevenir esse tipo de acontecimento, em Clojure temos o que chamamos de *symbol name generation*, também conhecidos como **Gensyms**.

```clojure
(gensym)
; G__54
(gensym "param")
; param57
```

A função gensym, como o próprio nome implica, gera um símbolo. Esse símbolo possui um nome gerado automaticamente. Todavia, temos um atalho para isso: caso um símbolo termine com `#` em uma syntax quote, será expandido pelo compilador em um gensym.

```clojure
; macro that converts yes to true, and no to false
(defmacro yes-no->boolean [arg]
    `(let [b# (= ~arg "yes")]
         b#))

(macroexpand-1 '(yes-no->boolean "yes"))
; (clojure.core/let [b__148__auto__ (clojure.core/= "yes" "yes")] b__148__auto__)
```

No exemplo acima, o nome `b__148__auto__` foi gerado automaticamente.

