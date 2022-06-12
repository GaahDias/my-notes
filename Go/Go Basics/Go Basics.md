# Golang Basics 🦫

Notas feitas ao longo do curso **Learn Go Programming - Golang Tutorial for Beginners (freeCodeCamp.org)**.

## Variables

#### Declaração e atribuição de valores para variáveis

 Diferente de outras linguagens, em Go temos diversas formas de se declarar variáveis:

```go
// Declarando variável
var num int

// Declarando várias variáveis de uma vez
var (
    num1 int
    num2 int
    num3 int
)

// Declarando e atribuindo um valor para variável
var num int
num = 10
var num int = 10
num := 10

// Modificando o valor da variável
num = 20
```

Porém, devemos nos atentar para alguns pontos importantes:

* O operador `:=` serve para inicializar ou declarar uma variável nova variável, e na mesma linha, atribuir um valor a mesma. É o equivalente a:
  
  ```go
  var num int
  num = 10
  ```

* `var num int = 10` vs `num := 10`: Por mais que a segunda opção seja mais simples de ser declarada, o tipo dela é sempre inferido pelo dado atribuído a variável. Ou seja, nesse caso, um `int`. Caso você deseje que a variável seja de um tipo específico, como um `int8`, `uint8`, `int16`, entre outros, você precisará declarar explicitamente: `var num uint16 = 10`.

* Para declarar uma variável sem atribuir um valor, é **obrigatório** o uso de `var num int`, e não apenas `var num` ou `num`.

#### Zero values

Variáveis declaradas sem um valor explícito terão o valor padrão de:

* `0` para tipos numéricos

* `false` para tipos booleanos

* `""` para strings

```go
var i int // 0
var b bool // false
var s string // ""
```

#### Nomenclatura das variáveis

Outro aspecto singular da linguagem é o naming convention utilizado nas variáveis.

Como em várias outras linguagens, declaramos variáveis utilizando o padrão *camelCase*. Porém o que torna a linguagem diferente das de mais é o escopo das variáveis: 

* Caso uma variável comece com letra maiúscula, ela será visível para todos os outros pacotes e módulos.

* Caso ela comece com letra minúscula, será uma variável local e privada do atual módulo ou pacote.

## Data types

#### Booleans

Booleans em Go são utilizados como em qualquer outra linguagem:

```go
var isGoAnAwesomeLang bool = true
myBool := 1 == "1"
```

#### Números

Em Go possuímos uma grande variedade de tipos de números:

```
int8        = 8-bit signed integer
int16       = 16-bit signed integer
int32       = 32-bit signed integer
int64       = 64-bit signed integer
uint8       = 8-bit unsigned integer
uint16      = 16-bit unsigned integer
uint32      = 32-bit unsigned integer
uint64      = 64-bit unsigned integer
float32     = 32-bit floating point number
float64     = 64-bit floating point number
complex64   = 64-bit complex number
complex128  = 128-bit complex number
int         = Both int and uint contain same size, either 32 or 64 bit.
uint        = Both int and uint contain same size, either 32 or 64 bit.
float       = Either 32 or 64 bit float.
rune        = It is a synonym of int32 and also represent Unicode code points.
byte        = It is a synonym of uint8.
uintptr     = It is an unsigned integer type. Its width is not defined, but its can hold all the bits of a pointer value.
```

Vale lembrar que o valor atribuído para `int` e `float`dependerá do seu sistema. Para sistemas 32-bit, será um `int32` e `float32`, já para sistemas 64-bit, `int64` e `float64`.

###### Conversões para números

Para converter algum tipo de dado para um número, basta utilizarmos as funções do tipo de dado específico. Ou seja:

```go
var num float32 = 3.5
newNum := int16(num) // 3

var num uint64 = 300
newNum := float32(num) + 0.75 // 300.75
```

###### Bytes e Runes

Bytes e Runes são alias para `uint8` e `int32`, respectivamente. Como em Go nós não possuímos um tipo de dado char, utilizamos `byte` e `rune` para representar caracteres do tipo ASCII (byte), e Unicode UTF-8 (rune). A declaração de um caractere é feito através de aspas simples `'c'`.

```go
var myChar byte = 'c'
fmt.Printf("%v", myChar) // 99 - actual ASCII value for the letter 'c'
fmt.Printf("%v", string(myChar)) // c - ASCII converted to string
```

Por padrão, um caractere é inferido ao tipo `rune`. Caso você deseje ter um caractere do tipo `byte`, deverá declarar isso manualmente:

```go
myChar = 'c' // tipo rune
var myChar byte = 'c'
```

#### Strings

Strings são um agrupamento de caracteres, ou um texto. Diferentemente de bytes e runes, elas são declaradas com aspas `"string"`.

Em Go, elas são basicamente um aliases para bytes. Ou seja, cada caractere de uma string é, na verdade, um byte.

```go
s := "Minha string"
fmt.Printf("%v, %T", s[10], s[10]) // 110, uint8 (byte)
fmt.Printf("%v", string(s[10])) // n
```

A concatenação de strings é feita através do operador `+`:

```go
s1 := "Minha "
s2 := "string"
s3 := s1 + s2
```

Também são imutáveis, então operações como a seguinte são inválidas:

```go
s := "Minha string"
s[2] = "x"
```

###### Conversões para strings

Como já foi visto anteriormente, para converter um `byte` ou `rune` para string, basta utilizar a função `string()`. Agora, para a conversão de digamos, o número 100, para a string "100", precisamos utilizar a função `Itoa()` do pacote `strconv`:

```go
n := 100
s := strconv.Itoa(n)
fmt.Printf("%v, %T", s, s) //100, string
```

Isso é necessário pois se tentássemos converter o número 100 para string usando a função `string()`, obteríamos o ASCII correspondente ao número 100. No caso, `'d'`. Para ver todos os caracteres ASCII, acesse a [tabela ascii](https://theasciicode.com.ar/).

## Arrays and Slices

Em Go nós possuímos duas estruturas de dados que são muito parecidas: Arrays e Slices. Ambas são utilizadas para armazenar múltiplos valores de um tipo de dado em específico. O que difere as duas é justamente como elas armazenam esses valores.

* **Arrays:** Consegue armazenar um número limitado e pré estabelecido de valores. Por ex.: `array := [3]int{1, 2, 3}`
  
  Também podemos declarar o número de armazenamento em um array utilizando `...`. Dessa forma, será detectado automaticamente quantos valores foram inseridos na array, e esse será o tamanho dela. Por ex.: `array := [...]int{1, 2, 3}`.

* **Slices:** Consegue armazenar um número dinâmico de valores, que não é necessário ser estabelecido na sua criação. Por conta disso, é assumido um valor para a underlying array do nosso slice, que pode variar conforme formos adicionando novos elementos. Essa variação acontece com a cópia dos elementos existentes para um novo underlying array maior, em outro endereço de memória. Por ex.: `slice := []int{1, 2, 3}`. 

#### Cópias de arrays e slices

Vamos usar como base o seguinte código:

```go
myArray := [...]int{1, 2, 3}
mySlice := []int{1, 2, 3}
myNewArray := myArray
myNewSlice := mySlice

fmt.Printf("myArray: %v\n", &myArray[0]) // myArray: 0xc000126000
fmt.Printf("mySlice: %v\n", &mySlice[0]) // mySlice: 0xc000126018
fmt.Printf("myNewArray: %v\n", &myNewArray[0]) // myNewArray: 0xc000126030
fmt.Printf("myNewSlice: %v\n", &myNewSlice[0]) // myNewSlice: 0xc000126018
```

Como foi demonstrado acima, cópias de arrays em Go são alocadas para um novo endereço de memória, ou seja, no final existem dois setores de memória sendo utilizados para armazenar o mesmo dado. Já com cópias de slices acontece o contrário: Na cópia, o novo slice é simplesmente apontada para o mesmo endereço de memória do slice original.

Isso é muito importante de se saber, sendo que uma mudança feita na cópia de um slice, repercurtirá no slice original. 

```go
mySlice := []int{1, 2, 3}
myNewSlice := mySlice

myNewSlice[0] = 10
fmt.Println("mySlice: " + strconv.Itoa(mySlice[0]) + " myNewSlice: " + strconv.Itoa(myNewSlice[0]))
// mySlice: 10 myNewSlice: 10
```

E claro, nós podemos ter o mesmo comportamento em um array se explicitamente indicarmos para a cópia dela ser igual ao endereço de memória da original.

```go
myArray := [...]int{1, 2, 3}
myNewArray := &myArray


fmt.Printf("myArray: %v, myNewArray: %v", &myArray[0], &myNewArray[0])
// myArray: 0xc0000be000, myNewArray: 0xc0000be000
```

#### Acessando elementos

Para acessar um único elemento, basta informar o seu índice:

```go
arr := []int{1,2,3}
a[0] // 1
```

Para acessar múltiplos elementos em uma array/slice, utilizamos uma semântica parecida com a de Python:

```go
arr := [...]int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
arr[:] // slice de todos os elementos
arr[3:] // slice do 4o elemento até o fim
arr[:6] // slice dos primeiros 6 elementos
a[3:6] // slice o 4o, 5o e 6o elementos
```

Também temos a possibilidade de utilizar o que chamamos em outras linguagens de spread operator em arrays ou slices:

```go
sl1 := []int{1, 2, 3}
sl2 := []int{4, 5}
sl1 = append(sl1, sl2...)
fmt.Printf("%v", sl1) // [1, 2, 3, 4, 5]
```

```go
func (ip IPAddr) String() string {
    tmp := make([]interface{}, len(ip))
    for i, val := range ip {
        tmp[i] = val
    }
    return fmt.Sprintf("%d.%d.%d.%d", tmp...)
}
```

#### Funções

Temos algumas funções muito úteis para se utilizar quando trabalhando com arrays e slices:

* **len()** - Retorna o tamanho (length), ou número de elementos contidos na array/slice.

* **cap()** - Retorna a capacidade total da array/slice.
  
  ```go
  s := make([]int, 0, 3)
  for i := 0; i < 5; i++ {
      s = append(s, i)
      fmt.Printf("cap %v, len %v, %p\n", cap(s), len(s), s)
  }
  //cap 3, len 1, 0x1040e130
  //...
  //cap 6, len 4, 0x10432220
  //...
  ```

* **make()** - Função utilizada para criar um slice. Ela leva três argumentos:
  
  * Tipo de dado que será armazenado no slice. Por ex.: `[]int`.
  
  * Length ou tamanho do slice. Por ex.: `3`.
  
  * Capacidade inicial total do slice. Por ex.: `100`.
  
  Essa função é útil quando queremos estabelecer um valor inicial para capacidade do slice (underlying array). Por padrão o próprio Go faz isso, mas podemos modificar para termos no começo da declaração do mesmo um slice com um tamanho maior. Isso é útil para não ser necessário o resize constante do slice, e consequentemente de criações constantes de novos underlying arrays maiores.

* **append()** - Retorna um novo slice com o item indicado inserido no final do mesmo. Essa função apenas funciona com slices.
  
  ```go
  arr := [...]int{1, 2, 3}
  arr = append(arr, 4) // 1, 2, 3, 4
  arr := append(arr, 5, 6) // 1, 2, 3, 4, 5, 6
  arr := append(arr, []int{7, 8, 9}...) // 1, 2, 3, 4, 5, 6, 7, 8, 9 
  ```

## Maps

Maps em Go possuem o mesmo conceito que maps e dictionaries em outras linguagens, ou seja, são feito de key/value pairs. Um exemplo de map seria:

```go
tvShowsYear := map[string][]int{
    "The Office":            []int{2005, 2013},
    "How I Met Your Mother": []int{2005, 2014},
    "Rick and Morty":        []int{2013},
}
```

#### Cópias de maps

Da mesma forma como slices, as cópias de maps meramente apontam para o endereço de memória do original.

```go
myCopy := tvShowsYear
fmt.Println(&myCopy["The Office"][0]) // 0xc0000be000
fmt.Println(&tvShowsYear["The Office"][0]) // 0xc0000be000
```

É importante ter isso em mente quando deletando ou adicionando elementos em maps e cópias de maps.

#### Acessando e adicionando elementos

Para acessar elementos de nossos maps, basta passarmos a key corresponde para tal elemento:

```go
fmt.Println(tvShowsYear["The Office"]) //[2005, 2013]
```

Já para adicionar elementos, faremos o seguinte:

```go
tvShowsYear["The Haunting of Hill House"] = []int{2018, 2018}
fmt.Println(tvShowsYear["The Haunting of Hill House"]) // [2018, 2018]
```

Algo bem particular da linguagem é a forma como checamos a existência de key/value pairs em nossos maps. Vamos pegar como exemplo o seguinte:

```go
fmt.Println(tvShowsYear["Breaking Bad"]) // []
```

O comportamento mais esperado seria o retorno de um erro, ou até mesmo o boolean "False". Porém, o que acontece é o retorno do valor padrão para o tipo do valor do map. Ou seja, se fosse um tipo número, o retorno seria 0, já se fosse uma string, "".

Para realmente termos uma confirmação se o key/value pair existe em nosso map, precisamos fazer o seguinte:

```go
val, ok := tvShowsYear["Breaking Bad"]
fmt.Println(val, ok) // [], false
```

Caso você não possua interesse no valor, e apenas no boolean, podemos passar um `_` como valor. Dessa forma, o valor será descartado na compilação, e consequentemente desconsiderado de nossa aplicação.

```go
_, ok := tvShowsYear["Breaking Bad"]
```

#### Funções

Temos algumas funções muito úteis para se utilizar quando trabalhando com maps:

* **make()** - Da mesma forma como com slices, também serve para criar um map. Por ex.: `myMap := make(map[string]int)`.

* **delete()** - Função para deletar key/value pairs de nossos maps. Por ex.: `delete(myMap, "Key01")`.

## Structs

Structs em Go tem uma lógica muito similar a de structs em C. Aqui vai um exemplo de declaração de struct:

```go
type Dog struct {
    name string
    age int
    gender byte
}

nina := Dog{
    name: "Nina",
    age: 8,
    gender: 'F',
}
```

Também podemos declarar structs de forma anônima em uma só linha:

```go
nina := struct{name string}{name: "Nina"}
```

Para acessar campos de uma struct, precisamos simplesmente referenciá-lo com `.field`:

```go
fmt.Println(nina.name) // Nina
```

Da mesma forma como Arrays, quando copiamos o valor de uma variável struct, para outra variável, esse valor é de fato copiado para um novo endereço de memória:

```go
nina := Dog{
    name: "Nina",
    age: 8,
    gender: 'F',
}

otherNina := nina
otherNina.age = 16
fmt.Println(nina.age) // 8
fmt.Println(otherNina.age) // 16

otherOtherNina := &nina
otherOtherNina.age = 16
fmt.Println(nina.age) // 16
fmt.Println(otherOtherNina.age) // 16
```

#### Embedding

Embedding funciona de uma forma semelhante a herança do tradicional modelo OOP. Com isso, podemos reutilizar structs, dentro de outras structs. Por exemplo:

```go
type Animal struct {
    name   string
    age    int
    gender byte
}
type Dog struct {
    Animal
    color      string
    isFriendly bool
}

nina := Dog{
    Animal: Animal{
        name:   "Nina",
        age:    8,
        gender: 'F'},
    color:      "Brown",
    isFriendly: true,
}

fmt.Println(nina) // {{Nina 8 70} Brown true}
```

Dessa forma nós exportamos os campos da estrutura Animal para Dog.

#### Tags

Tags são outro recurso disponível para a utilização quando se trabalhando com structs. Através delas, podemos passar informações mais específicas para outras libs, por exemplo. Para utilizar tags, precisaremos importar o pacote reflect.

```go
import (
    "fmt"
    "reflect"
)

type Dog {
    name string `required`
    age int `max:30`
    gender byte `F/M`
}


func main() {
    t := reflect.TypeOf(Animal{})
    field, _ := t.FieldByName("name")
    fmt.Println(field.Tag) // required
}
```

Quanto ao pacote reflect, segundo a própria documentação:

*Package reflect implements run-time reflection, allowing a program to
manipulate objects with arbitrary types. The typical use is to take a value
with static type interface{} and extract its dynamic type information by
calling TypeOf, which returns a Type.*

## If Else

If Elses em Go são declarados da seguinte forma:

```go
guess := 50

if guess >= 50 && < 100 {
    fmt.Println("Maior ou igual a 50, e menor que 100")
} else if guess >= 100 {
    fmt.Println("Maior ou igual a 100")
} else {
    fmt.Println("Menor que 50")
}
```

#### Inicialização de variáveis

Também podemos inicializar variáveis e testá-las em nosso if statement. Vale lembrar que variáveis inicializadas dessa forma apenas existem dentro do escopo do if statement:

```go
tvShowsYear := map[string][]int{
        "The Office":            []int{2005, 2013},
        "How I Met Your Mother": []int{2005, 2014},
        "Rick and Morty":        []int{2013},
    }

    if year, ok := tvShowsYear["Stranger Things"]; ok {
        fmt.Println(year)
    } else {
        fmt.Println("It doesn't exists")
    }
```

## Switch

Em Go, switch stements possuem algumas características bem peculiares e singulares.

#### Testes para múltiplos valores

Testes para múltiplos valores em um só case é permitido em Go, diferentemente de muitas outras linguagens:

```go
i := 2
switch i {
    case 1:
        fmt.Println("1")
    case 2, 3:
        fmt.Println("2 ou 3")
    default:
        fmt.Println("Não é 1, 2 nem 3")
}
```

#### Break e Fallthrough

No exemplo acima, também podemos observar a omissão da keyword `break`. Sim, nós temos a keyword `break` em Go, porém, por padrão os cases de um switch statement tem implicitamente um break imbutido. Caso seja necessário substituir esse comportamento, podemos utilizar a keyword `fallthrough`:

```go
i := 10
switch {
    case i <= 10:
        fmt.Println("Menor que 10")
        fallthrough
    case i <= 20:
        fmt.Println("Menor que 20")
    default:
        fmt.Println("Maior que 20")
}
// Menor que 10
// Menor que 20
```

#### Testes booleanos

Outro uso interessante demonstrado no exemplo acima é o uso de switch statements como if elses. Caso não seja especificado uma variável na declaração do switch, podemos passar qualquer expressão booleana nos cases. Isso pode ser muito útil como uma alternativa para if statements.

#### Testes de tipos

Outro uso peculiar de swtich statements em Go é para checar o tipo de uma interface. Como em variáveis de interfaces podemos atribuir qualquer valor, isso pode ser muito útil em diversos casos:

```go
var i interface{} = 1

switch i.(type) {
    case int:
        fmt.Println("Integer")
    case float64:
        fmt.Println("Floar64")
    case string:
        fmt.Println("String")
    default:
        fmt.Println("Another type")
}
// Integer
```

#### Inicialização de variáveis

E claro, como em if statements, também podemos inicializar variáveis no escopo do nosso switch statement:

```go
switch i := 5; i {
    case 5:
        fmt.Println("5")
    default:
        fmt.Println("Not 5")
}
```

## For

O único tipo disponível de loop em Go é o conhecido For. Aqui vão alguns exemplos do uso de For em Go:

```go
for i := 0; i < 5; i++ {
    fmt.Println(i)
}
```

```go
i := 0
for ; i < 5; i++ {
    fmt.Println(i)
}
```

#### Declaração de múltiplas variáveis

Para declarar mais de uma variável para interagir com nosso For loop, podemos fazer da seguinte forma:

```go
for i, j := 0, 0; i < 5; i, j = i+1, j+1 {
    fmt.Println(i, j)
} 
```

Algo para se atentar aqui é o incremento dessas variáveis. Algo como `i, j - i++, j++` não é permitido. Isso se dá por conta de `++` em Go não ser uma expressão como em outras linguagens, mas sim um statement.

#### Usando For como While

Uma forma de contornar a omissão de while que os desenvolvedores da linguagem encontraram, foi simplificar o uso de for para se assemelhar um while loop:

```go
i := 0
for i < 5 {
    fmt.Println(i)
    i++
}
```

Dessa forma não precisamos lidar com incrementadores ou declarações de variáveis no escopo do for loop, mas apenas inserir a expressão booleana para parar o loop. Basicamente um while loop.

#### Loops infinitos

Para loops infinitos, basta apenas utilizarmos a keyword `for`. E para sair do nosso loop, podemos utilizar o `break`:

```go
i := 0
for {
    fmt.Println(i)
    i++
    if i == 5 { break }
}
```

Outra keyword para se ter em mente enquanto trabalhando com loops é o `continue`:

```go
i := 0
for {
    i++
    if i%2 == 0 { continue }
    else if i == 5 { break }
    fmt.Println(i)
}

// 1
// 3
```

#### Labels identificadoras

Em nested loops, como forma de identificação podemos utilizar as labels:

```go
Loop:
    for i := 0; i < 3; i++ {
        for j := 0; j < 3; j++ {
            if i * j >= 3 {
                break Loop
        }
    } 
}
```

Neste exemplo, utilizamos a label Loop, que foi atribuída ao nosso primeir for loop, para quebrar, ou sair do mesmo. Caso excluirmos essa lógica do nested loop, ao declarar o `break`, estaríamos saindo apeanas do segundo loop, e não do primeiro. Dessa forma, conseguimos finalizar nosso loop de uma maneira eficaz.

#### Key Value pairs

Para executar um loop em Key Value pairs, podemos utilizar a keyword `range`:

```go
myMap := map[int]string {
    1: "One",
    2: "Two",
    3: "Three",
}

for k, v := range myMap {
    fmt.Println(k, v)
}
// 1 One
// 2 Two
// 3 Three
```

```go
s := "hello"
for k, v := range s {
    fmt.Println(k, v)
}
// 0 104
// 1 101
// 2 108
// 3 108
// 4 111
```

```go
a := []int{10, 20, 30}
for k, v := range a {
    fmt.Println(k, v)
}
// 0 10
// 1 20
// 2 30
```



## Error Handling

A forma de se lidar com erros em Go é certamente uma das partes mais singulares da linguagem. Para fazer um bom uso das técnicas de error handling em Go, precisamos ver sobre **Defer**, **Panic** e **Recover**.

#### Defer

A forma como defer funciona é basicamente colocando uma instrução para ser executada por último em uma função:

```go
func main() {
    fmt.Println("Start")
    defer fmt.Println("Middle")
    fmt.Println("End")
}
// Start
// End
// Middle
```

Go lê o pedaço de código acima da seguinte forma:

1. Entra na função main, e executa o primeiro Println - Start.

2. Localiza um defer, e armazena essa informação.

3. Executa o último Println - End.

4. Assim que tudo na função foi executado, entram os defers. Como nesse exemplo temos apenas um, o mesmo é executado, Println - Middle.

Também é importante ressaltar que defer armazena o valor das variáveis e argumentos no código no momento que ele é chamado:

```go
a := "Start"
defer fmt.Println(a)
a = "End"
// Start
```

A ordem de execução de defer é um LIFO (last in, first out). O que significa que o seu último defer será na verdade o primeiro a ser executado, e o primeiro defer será o último a entrar em execução.

```go
func main() {
    defer fmt.Println("Start")
    defer fmt.Println("Middle")
    defer fmt.Println("End")
}
// End
// Middle
// Start
```

Um dos usos muito comuns de defers que vale ser mencionado é o fechamento de recursos logo após sua inicialização (abrir e fechar). Por exemplo:

```go
import (
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
)

func main() {
    res, err := http.Get("https://www.google.com/robots.txt")
    if err != nil {
        log.Fatal(err)
    }
    defer res.Body.Close()
    robots, err := ioutil.ReadAll(res.Body)
    if err != nil {
        log.Fatal(err)
    }
    fmt.Printf("%s", robots)
}
```

Neste caso, ao invés de fecharmos nosso `res` no final do código, utilizamos defer para fechá-lo logo após sua inicialização.

#### Panic

Diferente de outras linguagens, Go raramente ditará quando algo é de fato considerado um erro fatal, ou seja, um erro do qual sua aplicação não consegue continuar rodando. Isso na verdade, fica a encargo do desenvolvedor para decidir, e é nisso que o panic entra em ação. Panic seria o equivalente a exceptions de outras linguagens de programação:

```go
// Exemplo de panic da própria linguagem na função Println
a, b := 1, 0
c := a / b
fmt.Println(c)
// panic: runtime error: integer divide by zero


// Exemplo de panic implementado pelo usuário
func sum(a int, b int) int {
    if a < 0 || b < 0 {
        panic("number lower than 0")
    }
    return a + b
}
```

Como já foi visto anteriormente, normalmente nós temos dois retornos para funções em Go, o valor esperado, e um erro. É nisso que entra a beleza do error handling de Go: ele te dá a possibilidade de tratar aquele erro como uma exceção ou um panic, ou não:

```go
res, err := http.Get("https://google.com")
if err != nil {
    panic("can't access google.com")
}
defer res.Body.Close()
```

Outro ponto importante que é valido ser destacado é mais uma vez a ordem de execução de panics em uma função:

```go
func main() {
    fmt.Println("start")
    defer fmt.Println("this was deferred")
    panic("something bad happened")
    fmt.Println("end")
}
// start
// this was deferred
// panic: something bad happened
```

Com isso podemos concluir que após o fim da função primeiramente é iniciado os defers, e segundamente os panics. Ou seja, na função acima o código foi executado da seguinte forma:

1. É executado o Println - start.

2. Localiza um defer e armazena essa informação

3. Localiza um panic, e para a execução da função

4. Executa todos os defers

5. Executa todos os panics

#### Recover

Recover é utilizado para se recuperar de um panic na aplicação. Ele normalmente é utilizado em conjunto com defer:

```go
import (
    "fmt"
    "log"
)

func main() {
    fmt.Println("start")
    defer func() {
        if err := recover(); err != nil {
            log.Println("Error:", err)
        }
    }()
    panic("something bad happened")
    fmt.Println("end")   
}
// start
// 2009/11/10 23:00:00 Error: something bad happened
```

Com isso nossa aplicação pode continuar normalmente mesmo com o `panic("something bad happened")`, todavia a função main não será mais executada após o panic, o que significa que o nosso `fmt.Println("end")` não é considerado.



## Pointers
