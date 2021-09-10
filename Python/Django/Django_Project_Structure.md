# DJANGO PROJECT STRUCTURE

```
mysite/
    manage.py
    mysite/
        __init__.py
        settings.py
        urls.py
        asgi.py
        wsgi.py
```

* Primeiro mysite/: Root directory que contém o nome do projeto.
* manage.py: Command-line utility que nos possibilita interagir com Django em várias formas.
* Segundo mysite/: Diretório do package python do projeto.
* mysite/init.py: Um arquivo vazio que autentifica o diretório como um packege.
* mysite/settings.py: Configurações para o projeto Django.
* mysite/urls.py: Declarações de urls para o projeto Django.
* mysite/asgi.py: Um ponto de entrada para ASGI-compatible web servers.
* mysite/wsgi.py: Um ponto de entrada para WSGI-compatible web servers.



## SETTINGS.PY

É basicamente um módulo python contendo as configurações do site. Por exemplo:

```python
ALLOWED_HOSTS = ['www.example.com']
DEBUG = False
DEFAULT_FROM_EMAIL = 'webmaster@example.com'
```

* Designando as configurações: No Django, você precisa dizer quais configurações você está utilizando. É possível fazer isso utilizando a environment variable `DJANGO_SETTINGS_MODULE`. O valor dessa variável deve ser na sintaxe de Python path, ex.: mysite.settings. É importante mencionar também que o módulo settings deve estar no Python import search path.

  > When using [django-admin](https://docs.djangoproject.com/en/3.2/ref/django-admin/), you can either set the environment variable once, or explicitly pass in the settings module each time you run the utility.
  >
  > Example (Unix Bash shell):
  >
  > ```
  > export DJANGO_SETTINGS_MODULE=mysite.settings
  > django-admin runserver
  > ```
  >
  > Example (Windows shell):
  >
  > ```
  > set DJANGO_SETTINGS_MODULE=mysite.settings
  > django-admin runserver
  > ```
  >
  > Use the `--settings` command-line argument to specify the settings manually:
  >
  > ```
  > django-admin runserver --settings=mysite.settings
  > ```
  >
  > Django docs - Settings

  

* No servidor (mod_wsgi): Você precisará dizer para sua aplicação WSGI qual settings file usar. Faça isso utilizando o `os.environ`:

  ```python
  import os
  
  os.environ['DJANGO_SETTINGS_MODULE'] = 'mysite.settings'
  ```

* Configurações padrão: Um arquivo settings não precisa definir nenhuma configuração se não for preciso. Cada configuração possui um valor padrão considerável. Esses padrões estão em `django/conf/global_settings.py`.

  O algorítmo utilizado pelo Django para as configurações é o seguinte:

  * Carrega as configurações padrão de `global_settings.py`.
  * Carrega as configurações do arquivo de configurações especificado, sobreescrevendo as padrões.

  Um arquivo settings jamais deve importar `global_settings`, visto que isso é redundante.

* Usando settings no código: No seu app Django você pode importar as configurações através do objeto `django.conf.settings`. Por exemplo: 

  ```python
  from django.conf import settings
  
  if settings.DEBUG:
      # Do something
  ```

  Vale lembrar que `django.conf.settings` não é um módulo, mas sim um objeto, Então importações de configurações em específico não funcionarão.

* Segurança: Por conta do conteúdo do arquivo settings ser muito sensível, como a senha de um banco de dados, você deve limitar ao máximo o acesso a ele. Mudar as permissões do arquivo para apenas você e o seu Web server conseguir utilizá-lo é uma boa ideia.



## URLS.PY

Para projetar suas URLs, você pode criar um modulo informalmente chamado de URLconf. Esse módulo será puro código Python, e seria um intermediário entre os caminhos URL para funções Python (suas views).

