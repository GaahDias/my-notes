# The Reader :book:

Clojure é uma [linguagem homoicônica](https://en.wikipedia.org/wiki/Homoiconicity), que é uma forma mais sofisticada de descrever o fato que as aplicações Clojure são representadas por estruturas de dados.

> A language is **homoiconic** if a program written in it can be manipulated as data using the language, and thus the program's internal representation can be inferred just by reading the program itself. This property is often summarized by saying that the language treats "code as data".

Essa é uma diferença importante entre Clojure (e Common Lisp e suas variantes) e outras linguagens de programação. Clojure é definido em termos de avaliação de estruturas de dados, e não em termos de sintaxe de fluxo / arquivos de caracteres. É muito comum e fácil para programas Clojure manipularem, transfomarem e produzirem outros programas Clojure. 

Tendo dito isso, a maior parte dos programas Clojure começam apenas como arquivos de texto, e é a tarefa do **reader** parsear o texto, e produzir uma estrutura de dados para o compilador. 

É possível afirmar que o reader tem sua sintaxe baseada em caracteres, já a linguagem Clojure tem sua sintaxe definida em símbolos, listas, vetores, maps, etc.



## Reader forms

#### Symbols

* Símbolos começam com um caractere não numérico, e podem conter caracteres alfanuméricos e  `*, +, !, -, _, ', ?, <, >, =`.
* `/` tem um significado especial. Pode ser usado uma vez no meio do símbolo para separar o namespace do nome. Ex.: `my-namespace/foo`.
* `.` tem um significado especial. Pode ser usado uma ou mais vezes no meio de um símbolo para designar um nome de classe qualificado. Ex.: `java.util.Bitset`, ou em nomes de namespaces. Símbolos que começam ou terminam com `.` são reservados pelo Clojure.
* Símbolos que começam ou terminam com `:` são revervados pelo Clojure. Um símbolo pode conter um ou mais `:`.

#### Literals

* **Strings** - São rodeadas de "aspas duplas". Podem ocupar múltiplas linhas.
* **Numbers** - Geralmente representados como em Java:
  * Inteiros serão representados como Longs quando dentro do limite, caso contrário, `clojure.lang.BigInts`.
  * Números de pontuação flutuantes são lidos como Doubles - com o sufixo M serão lidos como BigDecimals.
  * Frações são suportadas. Ex.: `22/7`.
* **Characters** - São precedidos de uma barra invertida: `\c, \newline, \space, \tab, \formfeed, \backspace, \return`. Caracteres Unicode também são suportados com `\uNNNN`, como em Java. Octais são representados com `\oNNN`.
* **Nil** - Significa "nada / sem valor" - representa o java Null, e em testes lógicos false.
* **Booleans** - true e false.
* **Symbolic values** - `##Inf`, `##-Inf`, e `##NaN`.
* **Keywords** - Keywords são como símbolos, exceto que:
  * Elas devem começar com dois pontos. Ex.: `:fred`.
  * Elas não podem conter `.`.
  * Como símbolos, podem conter namespace. Ex.: `:person/name`.
  * Uma keyword que comece com dois dois pontos `:` é vinculada aquele namespace.

#### Lists

Listas são zero ou mais formas rodeadas de parenteses. Ex.: `(a b c)`.

#### Vectors

Vetores são zero ou mais formas rodeadas de colchetes. Ex.: `[1 2 3]`.

#### Maps

* Maps são zero ou mais key/value pairs (pares de chave / valor) rodeados de chaves. Ex.: `{:a 1 :b 2}`.
* Vírgulas são consideradas espaços, e podem ser usadas para organizar os pares. Ex.: `{:a 1, :b 2}`.
* Valores e chaves podem ser de qualquer formato.

##### Map namespace syntax

Maps podem opcionalmente especificar um namespace para as keys usadas no map com o prefixo `#:ns`, com *ns* sendo o nome do namespace.

Por exemplo:

```clojure
#:person{:first "Han"
         :last "Solo"
         :ship #:ship{:name "Millennium Falcon"
                      :model "YT-1300f light freighter"}}
```

É lido como:

```clojure
{:person/first "Han"
 :person/last "Solo"
 :person/ship {:ship/name "Millenium Falcon"
               :ship/model "YT-1300f light freighter"}}
```

#### Sets

Sets são zero ou mais formas entre colchetes precedidos por `#`. Ex.: `#{:a :b :c}`.



## Macro characters

O comportamento do reader é ditado por uma combinação de construtores imbutidos, e uma extensão chamada de read table. As entradas na read table fornecem mapeamentos de certos caracteres, chamados caracteres de macro, para um comportamento de leitura específico, chamado macros de leitura. A menos que indicado de outra forma, os caracteres do macro não podem ser usados em símbolos do usuário.

#### Quote (')

`'form => (quote form)`

#### Character (\\)

Produz um literal de caractere: `\a \b \c`.

#### Comment (;)

Comentário de linha única. Faz com que o leitor ignore tudo do `;` até o final da linha.

#### Deref (@)

`@form => (deref form)`

#### Metadata (^)

Metadata são os dados de alguns objetos, como: Simbolos, Listas, Vetores, Sets, Maps, entre outros. O macro de metadata `^` lê as informações passadas, e insere elas para a próxima forma:

```clojure
(def ^{:doc "Greets someone" :type (type greeting)} greeting "Hello user!")

(meta #'greeting) ; #' is used for refering to a var
; {:doc "Greets someone", :type java.lang.String, :line 1, :column 1, :file "NO_SOURCE_PATH", :name greeting, :ns #object[clojure.lang.Namespace 0x2bffa76d "user"]}
```

#### Dispatch (#)

O macro de dispatch faz com que o leitor interprete o caractere seguinte baseado em outra tabela.

* **Sets** - `#{}`.
* **Regex patterns** - `#"pattern"`. Um padrão regex é lido e compilado em real time. 
* **Var quote** - `#'x`. Também podemos referenciar vars com `(var x)`.
* **Anonymous function** - `#()`. 
* **Ignore next form** - `#_`. A forma seguida por `#_` é completamente ignorada pelo leitor.

#### Syntax-quote (`), Unquote (~) and Unquote-splicing (~@)

Para todas as formas com exceção de símbolos, listas, vetores, sets e maps, **`x** é a mesma coisa que **'x**.

Para símbolos, syntax-quote resolve o símbolo no contexto atual, retornando um símbolo completamente qualificado (ex.: namespace/name ou fully.qualified.Classname).

```clojure
(def x 10)
`(x)
; (user/x)
```

Símbolos com um namespace não qualificado, ou que terminam com `#`, serão resolvidos para um símbolo gerado automaticamente. Esse símbolo gerado será acompanhado de `_` e um id.

```clojure
(def x# 10)
`(x)
; (x__10__auto__)
```

Para Listas / Vetores / Sets / Maps, syntax-quote estabelece um template da estrutura de dados. Dentro desse template, as formas serão aplicadas recursivamente com syntax-quote. Porém, é possível remover algumas formas desse quote, utilizando unquote (~), e unquote-splicing (~@). O segundo serve para sequências de valores.

```clojure
(def x 5)
(def lst '(a b c))
`(fred x ~x lst ~@lst 7 8 :nine)
; (user/fred user/x 5 user/lst a b c 7 8 :nine)
```



## Reader Conditionals

Em casos onde não for viável separar o código em várias partes, ou quando o código tem sua maior parte portátil, com apenas algumas poucas partes relacionadas a plataformas específicas, podemos utilizar **reader conditionals**. Reader conditionals são suportados em arquivos `.cljc`, e no REPL padrão. Elas devem ser utilizadas com moderação, e apenas quando necessário.

Reader conditionals são uma nova form de dispatch do leitor, que podem começar com `#?` ou `#?@`. Ambos correspondem a uma série de recursos alternativo e expressões, similar a `cond`. Toda plataforma Clojure possui seu identificador - `:clj :cljs :cljr`. Cada condição em um reader conditional é lida, até que o identificador seja reconhecido, e depois é retornado uma expressão. Um identificador `:default` pode ser usado para fornecer uma opção padrão.

O seguinte exemplo será lido como Double/NaN em Clojure, js/NaN em ClojureScript, e nil em qualquer outra plataforma:

```clojure
#?(:clj		Double/NaN
   :cljs	js/NaN
   :default nil)
```

A sintaxe `#?@` é exatamente a mesma, porém é esperado o retorno de uma coleção que possa ser usada no contexto, de forma similar ao unquote-splicing `~@` do syntax quote.

```clojure
[1 2 #?@(:clj [3 4] :cljs [5 6])]
;; in clj =>		[1 2 3 4]
;; in cljs =>		[1 2 5 6]
;; anywhere else =>	[1 2]
```

As funções `read` e `read-string` levam opcionalmente um map de opções como primeiro argumento. 

```clojure
;; :read-cond	:allow - to process reader conditionals
;;				:preserve - to keep all branches
;; :features - persistent set of feature keywords that are active
```

Um exemplo em um arquivo Clojurescript:

```clojure
(read-string
    {:read-cond :allow
     :features #{:cljs}}
    "#?(cljs :works! :default :boo)")
;; :works!
```

```clojure
(read-string
  {:read-cond :preserve}
  "[1 2 #?@(:clj [3 4] :cljs [5 6])]")
;; [1 2 #?@(:clj [3 4] :cljs [5 6])]
```

