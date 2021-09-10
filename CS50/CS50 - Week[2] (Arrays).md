 

# CLI Arguments:

**Clang:** Dentro desse compiladdor possuímos alguns argumentos, como:

* `-o name`: Define o nome do arquivo compilado. Por padrão ele será nomeado de a.out (assembly out).
* -`lname`: Funciona como um link entre as bibliotecas (header files) e o arquivo principal.

Também é possível tratar argumentos na linha de comando em nosso próprio programa, através de `int argc, char* argv[]`, onde argc (argument count) contaria a quantidade de argumentos passados, e argv (argument value) seria o valor de cada um desses argumentos.

Para saber qual o retorno da aplicação realizada, podemos utilizar `echo $?`, que nos retornará `0`, caso tenha sido bem sucedidada, ou `1` caso o contrário.

# How compiling works:

* Preprocessing: No caso de C, cuida dos `#define, #include`. Caso seja um include por exemplo, o preprocessamento colocará as funções (apenas protótipos) do header file no meu programa.
* Compiling: Conversão de C para Assembly.
* Assembling: Conversão de Assembly para machine code (binários).
* Linking: Junta o machine code dos `#includes` com o seu programa principal.

Os arquivos headers (ao menos os principais como `stdio.h` `stdlib.h`...) ficam juntamente com os compiladores (gcc, clang...), dessa forma é possível integrá-los em nosso código sem precisar baixar nada adicionalmente. É importante mencionar também que não é salvo os arquivos `.c` dessas bibliotecas, pelo simples motivo que não seria eficiente fazer o processo de compilamento toda vez que fossemos rodar um programa. Pensando nisso, são salvas as versões já compiladas (binários, zeros e uns).

# Strings in C:

Quando armazenamos uma string em C, nada mais é do que um array de chars. Porém, ao final de cada string, é adicionado um caractere `NUL / (null)`, equivalente a 0 na tabela ASCII.

```c
char string[] = "ola";
// string[3] seria uma posição após o final de nossa string
printf("%i", string[3]);
//output: 0
```

Com isso, podemos fazer alguns calculos:

```c
char a = 'a';
printf("%c", a - 32);
//output: A (maiusculo)

char A = 'A';
printf("%c", A + 32);
//output: a (minusculo)

char sete = '7';
printf("%i,", sete - 48);
printf("%i", sete - '0');
//output: 7, 7
```

Outro exemplo de uma função muito útil feita pensando nessas funcionalidades:

```c
#include <stdio.h>

int sum_from_string(char* str) {
    int res = 0;
    for(int i = 0; str[i] != '\0'; i++) {
        if(str[i] > 47 && str[i] < 58) {
            res += str[i] - '0';
        }
    }
    return res;
}

int main(int argc, char *argv[]) {
   char str[7] = "ola12a1";
   printf("%i", sum_from_string(str));
}

//output: 4
```

