 

# Memória

A convenção para se contar bytes na memória é utilizando o hexadecimal, que possui uma base 16: `0 1 2 3 4 5 6 7 8 9 A B C D E F`.

```
0 1 2 3 4 5 6 7 8 9 0 A B C D E F 10 11 12 13 14 15 16 17 18 19 1A 1B 1C 1D 1E 1F 20 21 22 23 24 25 26 27 28 29 2A 2B 2C 2D 2E 2F...
```



**Como converter um número hexadecimal para decimal:**

```
-- Considere DUAS COLUNAS para o número FF.
-- Lembrando também que F corresponderia ao número 15 no sistema decimal.

16  1
F	F
(16 x 15) + (1 x 15) = 240 + 15 = 255

16  1
3	B
(16 x 3) + (1 x 11) = 48 + 11 = 59
```

Outra coisa muito comum quando falamos de memória e hexadecimal, é a representação com `0x` na frente do número hexadecimal, por exemplo: `0x5C`. Não existe um significado matemático nisso, apenas é utilizado para a melhor identificação de um número hexadecimal.

**Operadores de memória em C:**

**&** Informa qual o endereço de memória que a variável referenciada está localizada.

***** Vai até o endereço de memória da variável.

```c
int main(void) {
    int num = 50;
    printf("%p", &num);

    return 0;
}
// Output: 0x7ffec5f2fac4

int main(void) {
    int num = 50;
    printf("%i", *&num);

    return 0;
}
// Output: 50
```

É importante ressaltar que em C o operador ***** possui três funções distintas. A primeira seria para a criação de um ponteiro, um tipo de variável que será explicado logo abaixo. A segunda para desreferenciar um endereço, como foi demonstrado no exemplo acima. E a terceira e mais simples, para a multiplicação.

**Pointers:**

Ponteiros são apenas um novo tipo de variável que armazena (ou aponta para) o endereço de um tipo de dado / variável. Eles ocupam 8 bytes em memória.

```c
int main(void) {
    int num = 50;
    int *p = &num;
    printf("%p", p);

    return 0;
}
// Output: 0x7ffec5f2fac4

int main(void) {
    int num = 50;
    int *p = &num;
    printf("%i", *p);

    return 0;
}
// Output: 50
```

É Importante ressaltar que pointers são variáveis distintas das que de fato armazenam algum valor. A única conexão entre elas, é o apontamente do ponteiro para o endereço de memória. Todavia, ainda podemos atribuir valor para um endereço de memória utilizando um ponteiro, já que ele aponta para o referenciado endereço.

Em relação as strings do C, ou melhor, os `char *string`, podemos dizer que eles nada mais são do que ponteiros. No caso do char*, ponteiros que armazenam o endereço do primeiro caractere de uma determinada string. Já que toda string termina com `\0`, armazenando o endereço do primeiro caractere, e indo até a ocorrência de um `\0`, conseguimos ter nossa string inteira.

Vamos levar em consideração o seguinte código para exemplificar:

```c
int main(void) {
    char *s = "String!";
    printf("%p\n", s); // mesma coisa que &s
    printf("%c\n", *s);
    printf("%c\n", *(s+1));
    printf("%i", *(s+7));
    // output: 0x55d158baf004
    //		   S
    //         t
    //		   0
}
```

E quanto a copiar strings em C? Como sabemos que strings ou char* nada mais são do que ponteiros apontando para o endereço em memória do primeiro caractere. Então vamos levar em consideração:

```c
char* s1 = "string";
char* s2 = s1;
```

Nesse contexto, qualquer mudança que fizermos em s2 será também aplicada para s1, por ambas estarem apontando para o mesmo endereço de memória, como acontece com arrays (visto que arrays também são ponteiros, que apontam para o endereço de memória do primeiro elemento armazenado). Então eu estaria basicamente copiando o endereço de memória, e não apenas o conteúdo da string.

A forma correta então de se copiar uma string para outra variável seria utilizando a função `strcpy(str1, str2)` de `<string.h>`. Ou, de uma forma mais manual:

```c
int main(void) {
    char *s1 = "string";
    // apesar de s1 ter apenas 6 caracteres, é ocupado na verdade 7 bytes na memória, por 		conta do \0, ou caractere nulo.
    char *s2 = malloc(7 * sizeof(char));
    
    //novamente, é utilizado n + 1 por conta do caractere nulo.
    for(int i = 0, n = strlen(s1); i < n + 1; i++) {
        s2[i] = s1[i];
    }
    
    free(s2);
}
```

Vamos agora considerar os seguintes códigos, para realmente fixar a função de pointers dentro de C:

```c
// exemplo de segfault, quando acessamos memória que não alocamos em nosso programa:
int main(void) {
    int *x;
    int *y;
    
    x = malloc(sizeof(int));
    
    *x = 42;
    *y = 13;
}

// exemplo de variaveis compartilhando o mesmo endereço de memória:
int main(void) {
    int *x;
    int *y;
    
    // não é necessário passar &x ou &y
    // pois já sabemos que essa variável corresponde a um endereço de memória.
    x = malloc(sizeof(int));
    y = x;
    
    *x = 42;
    *y = 13;
    // ambos x e y armazenarão 13.
}
```

```c
void swap(int *a, int *b);

int main(void) {
    int y = 10;
    int x = 5;

   swap(&x, &y);

    printf("X: %i, Y: %i", x, y);

    return 0;
}

void swap(int *a, int *b) {
    int tmp =  *a;
    *a = *b;
    *b = tmp;
}

// Output: X: 10, Y: 5
```



**Partições da memória (em ordem), e alguns outros termos**:

**Machine Code** - Primeira "camada" das partições existentes na memória. Assim que abrimos um app, é onde fica armazenado o binário dele.

**Globals** - Váriaveis globais que são declaradas fora das funções.

**Heap** - Grande "pedaço", ou cnunk de memória que funções como *malloc* reservam para utilizar. Toda variável armazenada no Heap precisa ser liberada depois, usando *free*.

**Stack** - Divide espaço com o Heap é onde ficam as funções chamadas no programa (inclusive a main), e todas as variáveis que estejam dentro delas. Não é necessário manualmente liberar da memória, pois ao contrário do Heap, as variáveis do Stack já são "limpadas" automaticamente.

**Heap & Stack Overflow**: Também temos dois outros termos muito importantes para serem mencionados: Heap Overflow, e Stack Overflow. Basicamente pelo Heap e o Stack compartilharem o mesmo espaço dentro da memória, caso você, por exemplo, chame muitas vezes uma função ao ponto do Stack ultrapassar e chegar até o Heap, resultará em um Stack Overflow e em seu programa simplesmente crashando. Em outras palavras: sua memória é muito curta para tentar performar a ação desejada. O mesmo pode ser aplicado para o Heap Overflow.

**Segmentation Fault / Segfault**: Esse é um dos erros mais clássicos e recorrentes da linguagem C. Ele ocorre sempre quando tentamos acessar uma parte da memória que não é pertencente ao nosso programa, ou seja, que ainda não foi alocada.

**Buffer**: Um chunk de memória que será utilizada posteriormente em uma aplicação. Um exemplo seria o streaming de vídeos, onde é carregada uma quantidade finita, para ser assistida futuramente pelo usuário. Mas caso o mesmo fique offline, o buffer não será atualizado, logo não será possível assistir o vídeo mais.
