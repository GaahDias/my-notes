# Cursos sobre Linux da Digital Innovation One :penguin:

## Atalhos do Terminal

* **Ctrl+C**: Cancela o comando atual em funcionamento.
* **Ctrl+Z**: Pausa o comando atual, em primeiro ou segundo plano.
* **Ctrl+D**: Logout da sessão atual (sai do terminal).
* **Ctrl+W**: Apaga uma palavra na linha de comando.
* **Ctrl+U**: Apaga a linha inteira.
* **Ctrl+R**: Busca um comando recente.



## Operadores

* **|** : Serve para enviar a saída de um comando, para a entrada de outro.
* **>** : Redireciona a saída de um comando para outro comando ou arquivo.
* **>>** : Similar ao anterior, além de redirecionar a saída, ele também concatena ela com o conteúdo existente em um arquivo.
* **<** : Direciona a entrada de um arquivo para a saída de um comando.
* **&** : Esse operador permite o uso de dois comandos. Ele também separa o output deles no terminal.
* **&&** : Parecido com o *and* da programação, ele somente executa dois comandos caso o primeiro tenha sido executado com sucesso.



## Manipulação de Arquivos de Texto

Dentro do terminal podemos editar um arquivo de texto com diversas ferramentas, como: **nano**, **vim**, **neovim**, etc.

Outros comandos úteis para visualização de arquivos no terminal:

* **cat**: Para mostrar o conteúdo do arquivo.
* **tac**: Para mostrar o conteúdo do arquivo, mas de forma invertida.
* **head**: Mostra as 10 primeiras linhas de um arquivo.
* **tail**: Mostra as 10 últimas linhas de um arquivo.

Podemos pegar o output desses comandos para visualização, e inserir em um novo arquivo de texto. Por exemplo:

```shell
head file.txt > another_file.txt
```

Com isso, as 10 primeiras linhas do arquivo *file.txt* serão inseridas no arquivo *another_file.txt*.

Também podemos inserir um conteúdo em um arquivo de texto, através do comando **echo**:

```shell
echo 'Texto para inserção.' >> file.txt
```

Alguns pontos para se atentar das duas operações exemplificadas:

* Utilizamos primeiramente como exemplo o comando **head**, porém qualquer comando que gere um output em formato de texto é valido para a inserção. Alguns exemplos: *cal*, *date*, *neofetch*, etc.
* A diferença entre **>**, e **>>** é:
  * **>** serve para inserir um texto no arquivo limpo (sem conteúdo). Caso exista algum conteúdo no arquivo, ele simplesmente será deletado e substituido.
  * **>>** serve para adicionar um texto no arquivo, ou concatenar o texto.



## Pesquisa de arquivos

Temos dois principais comandos relacionados a pesquisa de arquivos:

* **file**: Nos especifica qual o tipo do arquivo. Se é um arquivo txt, uma pasta, etc. Além de especificar algumas outras características do arquivo em questão.
* **find**: Serve para pesquisar o caminho até um arquivo. Por exemplo `find /home/gabriel/ -name text*` procurará na pasta home por um arquivo cujo o nome comece com text.

Temos alguns comandos que normalmente são utilizados em conjunto com outros (como cat, head, etc) para a pesquisa e visualização dentro de arquivos. Um deles é o **more**, usado para uma visualização mais compreensiva de arquivos grandes. Por exemplo: `cat ~/Code/Clojure/super-project/core.clj | more`.

Outro muito utilizado no terminal para a pesquisa em arquivos é o **grep**, que tem a função de procurar por conteúdos (strings) dentro dos arquivos. Por exemplo: `head /etc/apt/sources.list | grep non-free`.

É importante mencionar que o **pipe (|)** nesses exemplos tem a função de direcionar o output de um comando, para o outro.

