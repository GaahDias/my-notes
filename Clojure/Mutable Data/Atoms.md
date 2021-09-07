# Atoms :green_apple:

## What is an Atom?

Atoms são uma espécie de dado mutável, que nos possibilitam o controle de estados assíncronos, independentes e compartilhados. Eles são um tipo de referencia como refs e vars. Você pode criar um atom com `atom`, e pode acessar seu estado com `deref/@`. Como refs e agents, atoms suportam validadores. Para mudar o valor de um atom, você pode utilizar `swap!`. Uma função mais "lower-level" também é fornecida com `compare-and-set!`. Mudanças em Atoms são livres de race conditions.

Atoms são muito utilizados em aplicações multithreading, e como dito anteriormente, a mudança de um valor em um Atom pode ser feita através da função `swap!`. Internamente, essa função lê o valor atual do Atom, aplica uma função nele, e tenta usar `compare-and-set!`. Como outra thread pode ter alterado o valor nesse meio tempo, `swap!` continuará tentando modificar o valor, e fará isso em um "spin loop". Portanto, funções passadas em `swap!` devem ser livres de Side Effects.

Em Java, Atoms requerem a criação de um objeto chamado Atomic Reference. Agora, falando em termos mais lower-level, em uma linguagem como C ou Assembly, Atoms não passam de um simples ponteiro para um dado.



### Creating an Atom

Para criar um Atom, utilizamos a função `atom`. E para obter o valor de um Atom, utilizamos `deref` ou `@`.

```clojure
(def a (atom 1))
; #'user/a
a
; #object[clojure.lang.Atom 0x5cbf9e9f {:status :ready, :val 1}]
(deref a)
1
@a
1
```



### Atom Functions

* **reset!**: Substitui o valor do Atom sem conferir o valor anterior. `(reset! atom-name newvalue)`.
* **CAS - compare-and-set!**: Substitui o valor do Atom, conferindo o valor anterior. Ele somente atribuirá um novo valor ao Atom, caso o valor antigo seja passado corretamente. `(compare-and-set atom-name old-value new-value)`.
* **swap!**: Substitui o valor do Atom baseado em uma função, conferindo o valor anterior. `(swap! atom-name fun args)`. Detalhe: a função passada em `swap!` pode ser chamada várias vezes (principalmente em contexto de threads), até finalmente o valor ser alterado.
