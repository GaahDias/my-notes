# Golang Basics 🐿

## Getting started

Para o getting started estarei usando como base a documentação [Tutorial: Get started with Go](https://go.dev/doc/tutorial/getting-started).

#### Dependency tracking

Quando seu código importa pacotes contidos em outros módulos, você gerencia essas dependências através do módulo do seu código. Esse módulo em questão é definido através de um arquivo `go.mod`. 

Para habilitar dependency tracking para seu código e criar um arquivo `go.mod`, use o comando `go mod init` passando o nome do módulo - o nome do módulo é também o caminho (path) para o módulo. 

Em desenvolvimento, o path do módulo seria a localização onde está o source code. Por exemplo: `github.com/mymodule`. Se você planeja publicar seu módulo para outros utilizarem, o módulo path **deve** ser a localização de qual o Go tools consiga fazer o download do módulo.

Para própositos do tutorial, utilizaremos apenas `example/hello`:

```bash
go mod init example/hello
```

Agora, para realmente começarmos a escrever nosso código, precisamos criar um arquivo `.go`, por exemplo `hello.go`.\

#### Primeiro código em Go, "Hello, world!"

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, world!")
}
```

Nesse código nós:

* Declaramos o pacote `main` - Um pacote é uma forma de agrupar funções, como em outras linguagens de programação.

* Importamos o pacote `fmt`, que contém funções para formatação de texto. Esse pacote está incluso na biblioteca padrão do Go.

* Implementamos uma função `main` para printar a mensagem "Hello, world!" para o console.

Por fim, para rodar os códigos em Go podemos utilizar o comando `go run file.go`.

#### Importando pacotes externos

Para procurarmos por pacotes Go, podemos ir até [pkg.go.dev](https://pkg.go.dev/). Para o tutorial, utilizaremos o pacote [quote](https://pkg.go.dev/rsc.io/quote/v4).

```go
package main

import "fmt"
import "rsc.io/quote"


func main() {
    fmt.Println(quote.Go())
}
```

Após importado o pacote, adicionaremos o novo pacote para o módulo como um requerimento, bem como geraremos um arquivos `go.sum` para ser usado na autentificação do módulo. Para isso, utilizaremos o comando `go mod tidy`:

```bash
$ go mod tidy
go: finding module for package rsc.io/quote
go: found rsc.io/quote in rsc.io/quote v1.5.2
```

Quando rodamos o comando `go mod tidy`, ele localizou e baixou o módulo `rsc.io/quote`que contém o pacote importado.

## Modules

Para essa sessão de **Modules** estarei usando como base a documentação [Totiral: Create a Go module](https://go.dev/doc/tutorial/create-module) e [Tutorial: Call module code]([Call your code from another module - The Go Programming Language](https://go.dev/doc/tutorial/call-module-code)).

#### Criando o módulo

Como já foi visto anteriormente, basta seguir os seguintes comandos para a criação de um módulo:

```shell
mkdir greetings && cd greetings
go init example.com/greetings
```

Então, faremos uma simples função de saudação:

```go
package greetings

import "fmt"


func Hello(name string) string {
    message := fmt.Sprintf("Hi, %v. Welcome!", name)
    return message
}
```

Dentro desse código fizemos algumas coisas:

* Criamos uma função chama `Hello`:
  
  * Em Go, funções que começam com letra maiúscula podem ser chamadas fora do seu próprio pacote.

* Inicializamos e atribuímos um valor para uma variável em `message := ...`:
  
  * O operador := serve para inicializar a varíavel, e na mesma linha atribuir um valor para ela. É o equivalente a:
    
    ```go
    var message string
    message = fmt.Sptrinf("Hi, %v. Welcome!", name)
    ```

* Utilizamos a função `Sprintf()`:
  
  * Printa o resultado formatado, e retorna a string desse print. 

#### Chamando o módulo

Para chamar o módulo criado logo acima, precisamos primeiramente criar uma nova pasta, e inicializar um segundo módulo nela:

```shell
cd ..
mkdir hello && cd hello
go mod init example.com/hello
```

Agora o código em `hello.go`:

```go
package main

import (
    "fmt"

    "example.com/greetings"
)


func main() {
    message := greetings.Hello("Gladys")
    fmt.Println(message)
}
```

Agora, para podermos utilizar o nosso módulos `greetings`, ainda precisamos executar alguns comandos. Em produção, publicaríamos o nosso módulo em algum repositório onde a Go tools conseguisse encontrar e baixar com facilidade, porém como não fizemos isso, precisamos adaptar o módulo `example.com/hello` para que consiga encontrar de forma local o módulo `example.com/greetings`.

Para isso, utilizaremos o seguinte comando:

```shell
go mod edit -replace example.com/greetings=../greetings
```

Aqui estamos modificando a localização do módulo `greetings` para ser em `../greetings`. Para confirmar isso, podemos verificar nosso arquivo `go.mod`:

```go
module example.com/hello

go 1.18

replace example.com/greetings => ../greetings
```

Após isso, devemos sincronizar as dependencias do módulo `example.com/hello`. Para isso, rode o seguinte comando:

```shell
go mod tidy
```

Depois disso, o nosso arquivo `go.mod` deve estar assim:

```go
module example.com/hello

go 1.16

replace example.com/greetings => ../greetings

require example.com/greetings v0.0.0-00010101000000-000000000000
```

O número no final do `require` seria uma pseudo-versão do nosso módulo. Agora, podemos finalmente rodar nosso código em `hello.go`:

```shell
go run .
Hi, Gladys. Welcome!
```
