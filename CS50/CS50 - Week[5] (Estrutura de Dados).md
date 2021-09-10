 

# Data structures

## Linked Lists:

Quando trabalhamos com arrays em C não conseguimos alocar mais elementos dentro dele do que o que foi estabelecido na implementação da variável. Mas, e se nós quisessemos inserir, digamos, 5 elementos em uma array de tamanho 4? Bem, não seria possível simplesmente alocar o quinto elemento logo ao lado dela, como se é de esperar, pois é possível que o byte de memória ao lado já esteja em uso por outra variável do nosso programa. Então, como resolver isso? Temos dois caminhos:

* **Reescrever nossa array em outro lugar na memória**: Essa forma não é tão performática por dois motivos. Primeiramente, pode levar um tempo considerável para reescrever uma array inteira em outro pedaço de memória (notação big O(n)). E também, dependendo do tamanho da array que estamos falando, nem pode ser possível. Por exemplo, não será tão fácil de achar espaço em memória para realocar uma array com 10000 elementos. Mas, sem dúvidas, é uma forma de se encarar o problema.

* **Trabalhar com apontamento de memória para outros elementos na array**: Isso, como é descrito no livro *grooking algorithms* é conhecido como List, e mencionado no *CS50* como Linked List. Nada mais é que uma array espalhada na memória, ao invés de agrupada em um gigante pedaço. É possível que um elemento esteja na posição `0x10A`, e outro na posição `0x10D`, por exemplo. Dessa forma, cada elemento da array apontará para o próximo elemento existente na lista, ou seja, será armazenado um ponteiro em cada elemento que apontará para o endereço em memória da próxima posição. Esse struct de elemento + ponteiro é popularmente chamado de **Node**.

  Mas é claro que isso também tem suas desvantagens. Para leitura, por exemplo, uma array de tamanho definido é considerávelmente mais veloz, visto que é possível determinar onde está um elemento pelo tamanho. Então digamos que eu esteja procurando pelo elemento 3 de uma array. Se eu souber onde está o primeiro elemento, saberei onde está o terceiro:

  ```
  array[0] = 0x123
  array[2] = 0x125
  ```

  Já em uma Lista, eu precisaria passar por cada elemento para descobrir onde está o terceiro, já que cada elemento aponta para o próximo. Ou seja, Listas são mais lentas para a leitura.

  Outra desvantagem das Listas é o tamanho em memória se comparado a um simples array, visto que cada posição de uma Lista não armazena apenas o valor indicado pelo programa (seja uma string, um int, etc), mas também um endereço de memória para o próximo elemento.

**Criando estruturas de dados em C**:

Para isso, utilizaremos três conceitos muito importantes de C: o `struct`, bem como os operadores `. e *`. Por `. e *` serem comumente utilizados juntos, temos uma "abreviação" para eles em C, que seria o *arrow operator*: `->`.

Vamos então criar um struct para uma Lista, ou Node:

```c
typedef struct node {
    int number;
    struct node *next;
} node;

int main(void) {
    node *n = malloc(sizeof(node));
    
    if(n != NULL) {
        n->number = 1; // mesma coisa que (*n).number = 1
        n->next = NULL;
    }
}
```

Mas e para acessar de forma dinamica os valores da minha lista? E indo mais além, e para limpar a minha lista, já que está sendo alocado dinamicamente a memória através de malloc? 

```c
typedef struct node {
    int number;
    struct node *next;
} node;

int main(void) {
    node *list = NULL;

    node *n = malloc(sizeof(node));
    if(n == NULL) {
        return 1;
    }
    n->number = 1;
    n->next = NULL;
    list = n;

    n = malloc(sizeof(node));
    if(n == NULL) {
        free(list);
        return 1;
    }
    n->number = 2;
    n->next = NULL;
    list->next = n;

    //printando valores da lista
    for(node *tmp = list; tmp != NULL; tmp = tmp->next) {
        printf("%i\n", tmp->number);
    }

    //limpando a lista
    while(list != NULL) {
        node *tmp = list->next;
        free(list);
        list = tmp;
    }
}
```

* **Printando os valores**: No for loop do código é passado primeiramente uma variável chamada tmp do tipo node, e é apontada para list. List representa *apenas o primeiro elemento da lista, não ela inteira*. O loop continua até tmp (ou seja, nossa lista) ser null, em outras palavras não ter mais elementos. E em cada interação do loop é atribuído o valor de next para a temp, dessa forma acessando o próximo elemento da lista, como estávamos fazendo anteriormente através do `list->next->next`. Por fim, é printado o number do node atual.

* **Limpando**: O while loop começa com a condição *enquanto list for diferente de NULL, execute...* Após isso, é declarado novamente um node temporário, e nele atribuido o próximo elemento da nossa lista, para salvar o endereço de memória dele. Depois, limpamos a list, que como já foi mencionado, seria apenas o primeiro elemento da lista inteira, ou o primeiro node. Por fim, é atribuido list para ser igual a tmp, ou seja, o próximo elemento, e repetido o loop.

## Binary Search Tree:

Linked Lists definitivamente são ótimas. Mas, novamente, elas pecam um pouco na performance de pesquisa. Mas, e se nós conseguíssemos implementar a pesquisa binária em uma Linked List? Isso é possível utilizando a *Binary Search Tree*.

Até agora temos pensado em uma lista apenas como um elemento unidimensional, apenas para frente, ou para trás. Porém, se pensarmos nessa mesma estrutura de uma forma bidimensional, ou seja, frente trás cima e baixo, começamos a ter a ideia de uma árvore, ou melhor dizendo, de galhos de uma ávore. Vamos levar em consideração o seguinte então:

```
    v------4------v
 v--2--v	   v--6--v
 1	   3	   5     7
```

A estrutua acima é bem semelhanta a uma ávore de fato. Temos um certo padrão nela também: sempre ao lado **esquerdo estão os elementos menores**, e do lado **direto os elementos maiores**. E claro, ao meio o elemento do meio. Dessa forma, conseguimos utilizar o conceito de linked lists (se considerarmos que cada apontação para um número é um ponteiro), e pesquisa binária ao mesmo tempo! *Obs.: isso somente será efetivo para listar ordenadas.

Traduzindo a pesquisa binária em uma árvore para código, utilizando recursão:

```c
typedef struct node {
    int number;
    struct node *left;
    struct node *right;
} node;

bool search(node *tree, int num) {
    if(tree == NULL) {
        return false;
    }
    else if(num < tree->number) {
        return search(tree->left, num);
    }
    else if(num > tree->number) {
        return search(tree->right, num);
    }
    else {
        return true;
    }
}
```

O único lado negativo da Binary Search Tree é a inserção de elementos. Para pesquisa ela é excelente, mas quando o assunto é inserção, temos um pequeno problema, visto que pode ser necessário reestruturar a nossa árvore para inserir um novo elemento na mesma. Para isso precisaríamos de um algoritmo um tanto quanto complexo.

## Hash Tables:

Hash Tables são basicamente arrays de lists, e sua proposta é ter um tempo algorítmo mais favorável, como big O(1) por exemplo. Podemos pensar nisso mais amplamente na forma de uma agenda de contatos de telefone:

```
A
B
C -> Credic
D -> Draco
E 
F
G -> Ginny, George
H -> Hermione, Hagrid, Harry
...
```

Quanto a **Hash Functions**: São funções que recebem um input(string) e retorna um output(integer) de onde o input está dentro da Hash Table. Então, novamente voltando ao exemplo de agenda de contatos, uma função hash que receba "Harry" como parâmetro, provavelmente retornará 7.

Hash Tables como temos visto são muito boas para data sets pequenos, mas e quando falamos de uma larga quantidade de dados? É possível observar que ainda não chegamos no tão sonhado big O(1), mas podemos alcançar isso dessa forma:

```
H
Ha
Hb
Hc
...
Haa
Hab
Hac
Hae
Haf
Hag -> Hagrid
...
Haq
Har -> Harry
Has
...
Heq
Her -> Hermione
Hes
```

Agora sim podemos dizer que a pesquisa nessa hash table é de big O(1). E podemos expandir isso ainda mais, de acordo com a necessidade e com o número de dados que temos. Mas, como tudo que já vimos ate agora, isso também tem o seu preço, e no caso das Hash Tables é a memória. Uma tabela como essa, de AAA até ZZZ irá ocupar um espaço considerável na memória, basta fazer as contas:

* Para A até Z: 26 espaços.
* Para AA até ZZ: 26x26 = 676
* Para AAA até ZZZ: 26x26x26 = 17576

Porém novamente, isso é apenas um pequeno custo se comparado ao ganho que big O(1) trás. Caso estamos falando sobre uma empresa como Google, ou Facebook, isso certamente não é nada, basta se investir em mais hardware.

## Outros Data Structures:

* **Queues (ou fila)** : Tem o prinício de FIVO: First In, First Out, se assemelha com uma fila na vida real, como o próprio nome implica. Existem dois principais métodos em uma Queue: enqueue (para entrar na fila), e dequeue (para sair da fila).
* **Stack (ou pilha)**: Tem o príncipio de LIFO: Last In, First Out, se assemelha com a ação de pegar objetos de uma pilha na vida real, como o próprio nome implica. Como Queues, também possuem dois principais métodos: push (para inserir itens), e pop (para remover itens).
* **Dictionaries**: Permite que se associe Keys com Values. Esse tipo de dado é fortemente utilizado em muitas linguagens de programação, especialmente em Python.

