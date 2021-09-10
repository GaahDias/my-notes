# Algoritmos

**Runtime** Tempo que leva para um algoritmo ser concluído. A medida de tempo aqui pode ser múltiplas coisas, como segundos, passos (steps), etc.

**O (Big O)** Utilizado frequentemente para descrever o runtime máximo de um algoritmo.

**Determinando a qualidade do algorítmo com O:**

* **O(n)**: Pior cenário possível, onde houve o maior runtime.

* **O(n/2)**: Cenário um pouco mais favorável, metade do runtime anterior.

* **O(log²n)**: Ótimo cenário, com um runtime baixo.

* Outros meios de se determinar (pior para o melhor):

  ```
  O(n²)
  O(n log n)
  O(n)
  O(log n)
  O(1)
  ```

**Ω**: Utilizando para descrever o runtime mínimo de um algoritmo.

```
Ω(n²)
Ω(n log n)
Ω(n)
Ω(log n)
Ω(1)
```



## C functions:

**strcmp (string compare)**: Compara caractere por caractere das strings até o `\0`, e converte para valores ASCII. Caso a soma dos valores ASCII da primeira seja MAIOR que o da segunda, será retornado um número maior que zero. Caso seja menor, um número negativo. Caso os dois valores ASCII sejam iguais, ou seja, ambas strings são identicas, é retornado ZERO.



## Linear search VS Binary search:

**LS**: Procura um item por vez, sem pular nenhum.

> A linear search scans one item at a time, without jumping to any item .
>
> 1. The worst case complexity is  O(n), sometimes known an O(n) search
> 2. Time taken to search elements keep increasing as the number of elements are increased.
>
> geekforgeeks.org

**BS**: Continuamente corta ao meio sua lista de dados, para agilizar o processo de busca.

> A binary search however, cut down your search to half as soon as you find middle of a sorted list.
>
> 1. The middle element is looked to check if it is greater than or less than the value to be searched.
> 2. Accordingly, search is done to either half of the given list
>
> geekforgeeks.org



## Tipos de Algoritmos:

**Linear Search**: Procura item por item de uma array.

**Binary Search**: Divide a array no meio, utilizando o principio de "dividir e conquistar".

**Bubble Sort**: Coloca no lugar dois elementos da array por vez. Por exemplo: `array[2] = {3, 2}`, como o primeiro indíce é três, o segundo dois, a array será rearranjada para: `array[2] = {2, 3}`. Dessa forma todos os valores maiories vão "borbulhar" para o fim da array, enquanto no começo ficarão apenas os menores.

**Selection Sort**: Procura pelo menor elemento da array, e troca ele de lugar com o menor índice. Por exemplo: `array[4] = {3, 2, 5, 1}`. Aplicando o algoritmo, na primeira interação a array ficaria: `array[4] = {1, 2, 5, 3}`, onde 1 é trocado de lugar com 3.



