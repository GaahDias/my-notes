# Sistema dos computadores (memória e binários):

Nós humanos, contamos de 0 até 9, ou seja, possuimos 10 digitos em nosso vocabulário, chamado de sistema decimal. Já os computadores utilizam o sistema binário.

**Bits:** Abreviação de **bi**nary digi**ts**.

**Byte:** O equivalente a 8 bits.

**Por que computadores usam o sistema binário?** Por um simples motivo: o input dos computadores é algo simples, a eletricidade. Não é possível representar mais de duas formas com a eletricidade, ligado ou desligado, on ou off, zero ou um.

Hoje em dia nossos computadores possuem inúmeros transistores, que podem representar zeros e uns, dando assim uma gama gigantesca de possibilidades.

### Como contar em binários? (de uma forma fácil):

Digamos que o número fornecido seja 110010. Como poderíamos saber facilmente que número esse binário representa no sistema decimal? Simples:

```
1  1  0  0  1  0
32 16 8  4  2  1

32 + 16 + 2 = 50
```

**E como representar letras do alfabeto em binário?** Para isso, precisamos fazer da mesma forma que utilizamos com os números: através do sistema binário. Por exemplo, vamos considerar a letra A (maiuscula):

```
A = 01000001, ou 65 em decimal.
```

Para isso que existem os diferentes tipos de variáveis. Para um integer, 01000001 representará 65, já para um char, será A. 

### ASCII:

Sistema inventado por nós (humanos) para decidir quais valores binários (normalmente veremos convertidos para decimal, por praticidade) devem representar cada letra do alfabeto.

```
A  B  C  D  E  F  G
65 66 67 68 69 70 71
```

É por conta disso que em um código de C como este, o resultado é `A` ao invés de um erro:

```c
#include <stdio.h>

int main() {
	char my_char = 65;
	printf("%c", my_char);
	return 0;
}
```

> Por padrão na tabela ASCII as letras são compostas por 8 binários, ou 8 bits. Convertendo isso para bytes, temos que um char ocupa um byte.

**ASCII vs Unicode:** A diferença entre ASCII e Unicode é que em ASCII estamos limitados apenas aos caracteres da lingua inglesa. Já em Unicode, onde utilizamos uma quantidade mais considerável de bits. Ou seja, caso tente armazenar um caractere da Unicode em um char no C, por exemplo, resultará em um erro, pois a quantidade de memória a ser alocada para um char da Unicode é maior do que o padrão do ASCII.

### Colors:

Para cores temos sistemas como o RGB (Red, Green, Blue), onde conseguimos representar todas as cores do arco-íris. No caso de RGB, os valores entre R, G, e B podem variar entre 0 e 255, ou seja, 265 combinações, o que resulta em oito bits ou um byte. Então podemos dizer que um pixel de cor em RGB ocupa 3 bytes de memória.

### Extensões de arquivos:

O motivo para a existência de tantos arquivos diferentes como `.png` ou `.mkv`, é para o computador compreender como ler determinados binários, bits, ou bytes.

# Algoritmos:

> Para um algoritmo não importa somente se ele funciona corretamente, mas também sua **eficiência**.