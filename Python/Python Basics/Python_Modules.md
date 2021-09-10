# SCRIPTS:

Scripts são arquivos separados, geralmente também os que são inicializados para a execução de um programa.



# MODULES

São as outras "partes" do código, as quais futuramente são importadas para nosso script principal.

```python
# Fibonacci (fibo.py) numbers module:

def fib(n):
	a, b = 0, 1
    while a < n:
        	print(a, end=' ')
            a, b = b, a+b
        print()
```

```python
#(main.py) ways of using the function inside fibonacci mobule
import fibo

fibo.fib(1000)

fib = fibo.fib
fib(1000)

from fibo import fib
fib(1000)

# IT IS NOT RECOMENDED TO USE * FOR IMPORTING MODULES
from fib import *
fib(1000)
```



# EXECUTING MODULES AS SCRIPTS

Quando rodamos um modulo Python

`python fibo.py <args>` 

O código dentro do modulo será executado, como se você tivesse importado ele, mas com `__name__` sendo `"__main__"`. Ou seja, se você adicionar isso ao seu módulo:

```python
if __name__ == "__main__":
    import sys
    fib(int(sys.argv[1]))
```

É possível rodar o código com `python fibo.py 1000`.

# HOW PYTHON LOOKS FOR MODULES

Na execução do script e suas importações, o interpretador do Python irá procurar por modulos primeiramente nativos da linguagem, depois disso ele procurará em `sys.path`. É possível alterar o conteúdo de `sys.path`, fazendo com que dessa forma o interpretador do Python procure por modulos em outros diretórios. Todavia não é recomendado.

# "COMPILED" PYTHON FILES

Para acelerar o carregamento dos modulos, é armazenado uma versão compilada de cada modulo em `__pychace__`, com o nome de `module.py_version.pyc`.



# THE DIR() FUNCITON

A função dir é utilizada para descobrir quais nomes (variaveis, funções...) existem no módulo. Ele retorna uma lista de strings.

# PACKAGES

Packages são como um agrupamento de módulos para a importação, definidos com o `__init__.py` Como fica claro no exemplo:

```
Folder structure:

sound/                          Top-level package
      __init__.py               Initialize the sound package
      formats/                  Subpackage for file format conversions
              __init__.py
              wavread.py
              wavwrite.py
              aiffread.py
              aiffwrite.py
              auread.py
              auwrite.py
              ...
      effects/                  Subpackage for sound effects
              __init__.py
              echo.py
              surround.py
              reverse.py
              ...
      filters/                  Subpackage for filters
              __init__.py
              equalizer.py
              vocoder.py
              karaoke.py
```



Isso possibilita a importação `import sound.formats.wavread`

> Note that when using `from package import item`, the item can be either a submodule (or subpackage) of the package, or some other name defined in the package, like a function, class or variable. The `import` statement first tests whether the item is defined in the package; if not, it assumes it is a module and attempts to load it. If it fails to find it, an [`ImportError`](https://docs.python.org/3/library/exceptions.html#ImportError) exception is raised.
>
> Contrarily, when using syntax like `import item.subitem.subsubitem`, each item except for the last must be a package; the last item can be a module or a package but can’t be a class or function or variable defined in the previous item.
>
> - Python docs - Modules

Mas o que acontece quando o usuário resolve importar `from sounds.formats import *`? Isso pode ser referenciado dentro do arquivo `__init__.py`, usando uma variável `__all__ = ["wavread", "wavwrite"]`. Dessa forma, ao utilizar a importação com *, será importado tudo que estiver dentro de `__all__`.

