# NOTES FROM FIRST DJANGO APP: POLLS APP

## Runserver cmd:

Por padrão, no comando `python manage.py` o servidor é rodado na porta 8080, mas é possível alterar isso:

```bash
python manage.py runserver 5000
```

O servidor que é incluído com o django é apenas destinado para o **desenvolvimento** e não deve ser utilizado como um web server fixo.

## Projects vs. apps:

> *What’s the difference between a project and an app? An app is a Web application that does something – e.g., a Weblog system, a database of public records or a small poll app. A project is a collection of configuration and apps for a particular website. A project can contain multiple apps. An app can be in multiple projects.*
>
> Django Docs - Tutorial 01: Poll app

* Comando para iniciar um PROJETO: `django-admin startproject name`.
* Comando para iniciar um APP: `python manage.py startapp name`.

**==========================================================================================================================**

## URLS FIles

Todo app Django terá o seu arquivo URLS.py, para o tratamento da URL fornecida pelo cliente. É possível realizar redirecionamentos de um arquivo URLS para outro, utilizando o include:

```python
urlpatterns = [
    path('admin/', admin.site.urls),
    path('polls/', include('polls.urls'))
]
```

No caso acima, caso a URL fornecida contenha *polls*, será redirecionado para o URLconfs do app polls.

**Path() function arguments:**

* Route: Do tipo string, que contém um url pattern. Patterns não procuram por GET e POST, ou o nome do domínio. Por exemplo, em  `https://www.example.com/myapp/` o URLconf receberá apenas `myapp/`. E em `https://www.example.com/myapp/?page=3` terá o mesmo resultado, apenas `myapp/`será passado.
* View: Serve para chamar a view que corresponde a função referenciada. Pode ser passado com argumentos como o objeto HttpRequest e qualquer outro valor capturado como keyword arguments.
* Kwargs: Keyword arguments podem ser passados como um dictionary para a view.
* Name: Nomeando sua URL permite que você referencie ela em qualquer lugar do seu projeto Django, especialmente dentro de templates.

**==========================================================================================================================**

## SETTINGS

Um arquivo Python com variáveis que representam as configurações do Django.

**Databases**

Por padrão, o framework utiliza o banco SQLite, porém é completamente viável a utilização de outros bancos. Para isso, será necessário mudar a config `DATABASES`:

```python
DATABASES = {
    'default': {
        # Django suporta oficialmente as dbs: PostgreSQL, MariaDB,
        # MySQL, Oracle e SQLite.
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}
```

> - [`ENGINE`](https://docs.djangoproject.com/en/3.2/ref/settings/#std:setting-DATABASE-ENGINE) – Either `'django.db.backends.sqlite3'`, `'django.db.backends.postgresql'`, `'django.db.backends.mysql'`, or `'django.db.backends.oracle'`. Other backends are [also available](https://docs.djangoproject.com/en/3.2/ref/databases/#third-party-notes).
> - [`NAME`](https://docs.djangoproject.com/en/3.2/ref/settings/#std:setting-NAME) – The name of your database. If you’re using SQLite, the database will be a file on your computer; in that case, [`NAME`](https://docs.djangoproject.com/en/3.2/ref/settings/#std:setting-NAME) should be the full absolute path, including filename, of that file. The default value, `BASE_DIR / 'db.sqlite3'`, will store the file in your project directory.
>
> If you are not using SQLite as your database, additional settings such as [`USER`](https://docs.djangoproject.com/en/3.2/ref/settings/#std:setting-USER), [`PASSWORD`](https://docs.djangoproject.com/en/3.2/ref/settings/#std:setting-PASSWORD), and [`HOST`](https://docs.djangoproject.com/en/3.2/ref/settings/#std:setting-HOST) must be added.
>
> Django Docs - Tutorial 02: Poll app

**Time_Zone**

Simples time_zone. Por padrão o valor é 'UTC', mas para colocar o fuso horário do Brazil: 'America/Sao_Paulo'. A lista com todos os fuso horários se encontra [aqui](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones).

Além de `TIME_ZONE`, também temos o `USE_TZ`, que basicamente leva o valor de um boolean para afirmar se o time_zone informado será utilizado no Database, ou não.

**Language_Code**

A linguagem de código da instalação. Por padrão o valor é 'en-us', mas é possível alterar para português com 'pt-br'. A lista com todas as identificações de linguagens está [aqui](http://www.i18nguy.com/unicode/language-identifiers.html).

**Use_I18N e Use_L10N**

`USE_I18N`: Booleano para verificar se o sistema de tradução deve ser ativado, ou não.

`USE_L10N`: Booleano para verificar se as informações do site devem se adequar a região do usuário, ou não. Por ex.: Se setado como True, será apresentado números e datas conforme a localização do usuário.

**Installed_Apps**

Contém os apps ativos no projeto. Por padrão, os apps permitidos são:

* `django.contrib.admin`: Página de administração.
* `django.contrib.auth`: Sistema de autentificação.
* `django.contribcontenttypes`: Um framework para tipos de conteudos.
* `django.contrib.sessions`: Um framework para sessions.
* `django.contrib.messages`: Um framework para mensagens.
* `django.contrib.staticfiles`: Um framework para gerenciar arquivos estáticos.

**==========================================================================================================================**

## MODELS

Essencialmente são o layout do nosso banco, com alguns metadados (dados de dados) adicionais. Cada tabela do nosso banco será representado por uma classe:

```python
from django.db import models

class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```

Cada classe herda de django.db.models.Model, que possui suas variáveis que representam um campo dentro de uma tabela. E cada campo é representado por um *Field* class, ex.: `CharField`, `DateTimeField`... que possui parametros para sua "configuração", ex.: `max_length=200`, `default=0`...

Com o model acima, damos informações para o django poder de:

* Criar um database schema (CREATE TABLE statements) para o app.
* Criar um Python database-access API para acesso a Question e Choice.

Agora para incluir o app no nosso projeto, precisamos ir até `INSTALLED_APPS`:

```python
INSTALLED_APPS = [
    'polls.apps.PollsConfig',
    'django.contrib.admin',
    # other apps...
]
```

**Migrations**

Para criar o arquivo de migração (migration) `python manage.py makemigrations polls` O resultado para essa migração foi: 

```bash
$ python manage.py sqlmigrate polls 0001
BEGIN;
--
-- Create model Question
--
CREATE TABLE "polls_question" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "question_text" varchar(200) NOT NULL, "pub_date" datetime NOT NULL);
--
-- Create model Choice
--
CREATE TABLE "polls_choice" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "choice_text" varchar(200) NOT NULL, "votes" integer NOT NULL, "question_id" bigint NOT NULL REFERENCES "polls_question" ("id") DEFERRABLE INITIALLY DEFERRED);
CREATE INDEX "polls_choice_question_id_c5b4b260" ON "polls_choice" ("question_id");
```

Agora, para aplicar a migração acima no banco de dados, rode: `python manage.py migrate`. Algumas informações adicionais sobre banco de dados e migrations em django:

> - The exact output will vary depending on the database you are using. The example above is generated for PostgreSQL.
> - Table names are automatically generated by combining the name of the app (`polls`) and the lowercase name of the model – `question` and `choice`. (You can override this behavior.)
> - Primary keys (IDs) are added automatically. (You can override this, too.)
> - By convention, Django appends `"_id"` to the foreign key field name. (Yes, you can override this, as well.)
> - The foreign key relationship is made explicit by a `FOREIGN KEY` constraint. Don’t worry about the `DEFERRABLE` parts; it’s telling PostgreSQL to not enforce the foreign key until the end of the transaction.
> - It’s tailored to the database you’re using, so database-specific field types such as `auto_increment` (MySQL), `serial` (PostgreSQL), or `integer primary key autoincrement` (SQLite) are handled for you automatically. Same goes for the quoting of field names – e.g., using double quotes or single quotes.
>
> Django Docs - Tutorial 02: Poll app

**==========================================================================================================================**

## ADMIN

Admin é a área de administração do seu projeto. Para definir seu login/senha, use: `python manage.py createsuperuser`. 

Para importar um model para a parte de administração:

```python
from django.contrib import admin
from .models import Question

admin.site.register(Question)
```

Os fields da model já são formatados automaticamente para a página, de acordo com sua tipo definido. Para alterar os campos da nossa área admin, podemos fazer:

```python
from django.contrib import admin
from .models import Question

class QuestionAdmin(admin.ModelAdmin):
    fields = ['pub_date', 'question_text']

admin.site.register(Question, QuestionAdmin)
```

Você deve seguir esses passos - criar uma classe da model admin, e depois passar ela como segundo argumento em `admin.site.register()`. Também é possível customizar os campos:

```python
from django.contrib import admin
from .models import Question

class QuestionAdmin(admin.ModelAdmin):
    fieldsets = [
        (None,               {'fields': ['question_text']}),
        ('Date information', {'fields': ['pub_date']}),
    ]

admin.site.register(Question, QuestionAdmin)
```

O primeiro elemento de cada túpla do fieldsets é reconhecido como um título, ou cabeçalho para o campo.

Agora, para adicionarmos uma escolha juntamente com o registro da função podemos usar o `admin.StackedInline`. Dessa forma, as escolhas aparecerão logo abaixo da questão.

```python
from django.contrib import admin
from .models import Choice, Question

class ChoiceInline(admin.StackedInline):
    model = Choice
    extra = 3

class QuestionAdmin(admin.ModelAdmin):
    fieldsets = [
        (None,               {'fields': ['question_text']}),
        ('Date information', {'fields': ['pub_date'], 'classes': ['collapse']}),
    ]
    inlines = [ChoiceInline]

admin.site.register(Question, QuestionAdmin)
```

Na nova classe passamos dois atributos: Model (para informar qual model será utilizado) e Extra (corresponde ao número de linhas, ou escolhas nesse caso, que serão apresentadas no site).

Também podemos fazer isso utilizando TabularInline, dessa forma as escolhas ficam mais "compactas" na página:

```python
class ChoiceInline(admin.TabularInline):
    #...
```

E quanto a estilização do próprio site admin? Para isso, podemos criar uma pasta de templates no próprio projeto: `project/templates/admin`. Dentro disso, devemos copiar o código de `admin/base_site.html` do próprio diretório do Django. Temos um comando para achar o mencionado diretório: `python -c "import django; print(django.__path__)"`.

Após isso precisamos substituir `{{ site_header|default:_('Django administration') }}`, com o nome de nosso site.

Para mais informações sobre customizações da página Admin vá até o Tutorial 07 do polls app, ou clique [aqui](https://docs.djangoproject.com/en/3.2/intro/tutorial07/).

**==========================================================================================================================**

## VIEW

Em Django os conteúdos das páginas são manuseados através das views. Cada view é representada por uma função dentro de `views.py`:

```python
def detail(request, question_id):
    return HttpResponse("You're looking at question %s." % question_id)

def results(request, question_id):
    response = "You're looking at the results of question %s."
    return HttpResponse(response % question_id)

def vote(request, question_id):
    return HttpResponse("You're voting on question %s." % question_id)
```



Quanto a urls: provavelmente você já se deparou com um site com: `ME2/Sites/dirmod.htm?sid=&type=gen&mod=Core+Pages&gid=A6CD4967199A42D9B65B1B`, certamente não podemos considerar isso um url amigável. Por outro lado em Django, URLs tem padrões muito mais elegantes, como: `/newsarchive/<year>/<month>/`. Para partir de uma URL para a adequada view, utilizamos algo chamado de URLconfs, que você pode configurar no arquivo `urls.py`:

```python
from django.urls import path
from . import views

urlpatterns = [
    # ex: /polls/
    path('', views.index, name='index'),
    # ex: /polls/5/
    path('<int:question_id>/', views.detail, name='detail'),
    # ex: /polls/5/results/
    path('<int:question_id>/results/', views.results, name='results'),
    # ex: /polls/5/vote/
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```

Dessa forma, podemos passar parâmetros nas nossas URLs de uma forma muito mais user friendly, e elegante, sem ter que utilizar query strings como `?question=`. Apenas precisamos passar entre `<>` o tipo de dado requerido, e seguido por `:` com o nome do parâmetro da função da View.

Cada view tem a responsabilidade de fazer uma de duas coisas: ou retornar um objeto HttpResponse, contendo a página solicitada, ou levantar uma exceção (erro), como Http404. O resto depende de você:

> *Your view can read records from a database, or not. It can use a template system such as Django’s – or a third-party Python template system – or not. It can generate a PDF file, output XML, create a ZIP file on the fly, anything you want, using whatever Python libraries you want.* *All Django wants is that HttpResponse. Or an exception.*
>
> Django Docs - Tutorial 02: Poll app

**==========================================================================================================================**

## TEMPLATES

Os templates descrevem como a página será carregada - são os html. O arquivo de configurações do Django deixa por padrão um *DjangoTemplates*, onde a opção `APP_DIRS` tem como valor True. Naturalmente *DjangoTemplates* procurará por uma pasta de templates em cada `INSTALLED_APPS` do `settings.py`. Todavia podemos colocar templates onde for mais favorável, até mesmo fora da pasta do projeto, basta passar o diretório em `settings.py` `TEMPLATES = []`.

As pastas de templates devem ser criadas dentro de cada app, por exemplo:

```
project/
	app/
		templates/
			polls(ou outro nome)/
				index.html
```

```django
{% if latest_question_list %}
    <ul>
    {% for question in latest_question_list %}
        <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
    {% endfor %}
    </ul>
{% else %}
    <p>No polls are available.</p>
{% endif %}
```

Como é possível observar todo código Python fica dentro de `{% %}`. E para inserir algo no html utilizamos `{{}}`.

**Rendereziando o template e retornando um HttpResponse:**

Para trabalhar com esse código do template, poderíamos fazer o seguinte na `views.py`:

```python
from django.http import HttpResponse
from django.template import loader
from .models import Question

def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    template = loader.get_template('polls/index.html')
    context = {
        'latest_question_list': latest_question_list,
    }
    return HttpResponse(template.render(context, request))
```

Explicando o código:

* `latest_question_list`: Basicamente faz um split na lista para obter os 5 primeiros elementos.
* `loader.get_template`: Pega o template da nossa pasta de Templates. É importante observar que não é necessário especificar `templates/` no path.
* `context`: No nosso index.html nós passamos a variável latest_question_list, tendo isso em mente o contexto apenas "contextualizou" nosso template dessa variável, passando em forma de um dicionário. Com isso podemos passar qualquer valor que seja utilizado em um template.
* `return`: Como anteriormente já haviamos definido `template = loader.get_template()`, apenas utilizamos o `render` para renderizar a página, passando `context, request`.

Uma forma mais curta e eficiênte de se performar o mesmo código seria:

```python
from django.shortcuts import render
from .models import Question

def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context)
```

Render tem como parametro: o objeto request, o caminho para o template, e opcionalmente um dicionário como terceiro argumento (normalmente utilizado para contexts).

**Retornando uma Exception (Error 404):**

```python
from django.http import Http404
from django.shortcuts import render
from .models import Question
# ...
def detail(request, question_id):
    try:
        question = Question.objects.get(pk=question_id)
    except Question.DoesNotExist:
        raise Http404("Question does not exist")
    return render(request, 'polls/detail.html', {'question': question})
```

Podemos observar um novo conceito aqui: A view executa um Http404 caso a questão requisitada não exista, ou seja, esteja fora do range de IDs do banco.

Uma forma mais curta e eficiênte de se performar o mesmo código seria:

```python
from django.shortcuts import get_object_or_404, render
from .models import Question
# ...
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html', {'question': question})
```

A função `get_object_or_404` leva como primeiro argumento um Django Model, e depois disso `**kwargs`, que posteriormente é passado para um `get()` do Model manager. E claro, levanta uma exceção 404 caso o objeto não exista.

Também temos o `get_list_or_404`, que funciona de forma similar, exceto a utilização de `filter()` ao invés de `get()`. A diferença entre os dois pode ser resumida a:

* **get()**: Retorna UM objeto com base na query.
* **filter()**: Pode filtrar por vários itens.

Levando em consideração a View Detail, aqui está um exemplo de como ficaria o template:

```django
<h1>{{ question.question_text }}</h1>
<ul>
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }}</li>
{% endfor %}
</ul>
```



**Removendo URLs "hardcoded"**: Tendo em vista que já definimos o name do `path()` em `urls.py`, podemos utilizar o `{% url %}` no nosso template para passar url de forma mais dinâmica.

```django
<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
```

```python
...
# the 'name' value as called by the {% url %} template tag
path('<int:question_id>/', views.detail, name='detail'),
...
```

Dessa forma, caso futuramente seja necessário a mudança da URL, basta alterá-la em `urls.py`:

```python
...
# added the word 'specifics'
path('specifics/<int:question_id>/', views.detail, name='detail'),
...
```



**Adicionando namespaces nas URLs**:

> *For example, the `polls` app has a `detail` view, and so might an app on the same project that is for a blog. How does one make it so that Django knows which app view to create for a url when using the `{% url %}` template tag?*
>
> Django Docs - Tutorial 02: Poll app 

Em um projeto Django muitas vezes teremos diversos apps, cada um com suas respectivas URLs. Para o framework conseguir diferenciar qual URL é de cada app, devemos adicionar um namespace da seguinte forma:

```python
from django.urls import path
from . import views

app_name = 'polls'
urlpatterns = [
    path('', views.index, name='index'),
    path('<int:question_id>/', views.detail, name='detail'),
    # outros paths
]
```

Fazendo isso, precisaremos alterar nosso template:

```django
<li><a href="{% url 'polls:detail' question.id %}">{{ question.question_text }}</a></li>
```

Outra funcionalidade muito interessante nos templates é o `plurize`:

```django
<li> {{ choice.choice_text }} -- {{ choice.votes }} vote{{ choice.votes|pluralize }}</li>
```

Permitindo que o resultado fique como:

* Not much -- **2** **votes**
* The sky -- **1** **vote**

**==========================================================================================================================**

## FORMS

Como tudo no Django, forms também são tratados de forma bem dinâmica:

```django
<form action="{% url 'polls:vote' question.id %}" method="post">
{% csrf_token %}
<fieldset>
    <legend><h1>{{ question.question_text }}</h1></legend>
    {% if error_message %}<p><strong>{{ error_message }}</strong></p>{% endif %}
    {% for choice in question.choice_set.all %}
        <input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}">
        <label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br>
    {% endfor %}
</fieldset>
<input type="submit" value="Vote">
</form>
```

Explicando:

* **csrf_token**: Já que estamos lidando com POST, precisamos ser cuidadosos. Pensando nisso, no Django temos o sistema `csrf_token`, que nos protege de certos ataques.
* **forloop.count**: Basicamente um incrementador de 1 em 1, para modificar o id de cada opção disponível.
* **error_menssage**: Iremos implementar isso na `views.py`.

Agora, vamos implementar algumas mudanças na função da View também:

```python
from django.http import HttpResponse, HttpResponseRedirect
from django.shortcuts import get_object_or_404, render
from django.urls import reverse
from .models import Choice, Question
# ...
def vote(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    try:
        selected_choice = question.choice_set.get(pk=request.POST['choice'])
    except (KeyError, Choice.DoesNotExist):
        # Redisplay the question voting form.
        return render(request, 'polls/detail.html', {
            'question': question,
            'error_message': "You didn't select a choice.",
        })
    else:
        selected_choice.votes += 1
        selected_choice.save()
        # Always return an HttpResponseRedirect after successfully dealing
        # with POST data. This prevents data from being posted twice if a
        # user hits the Back button.
        return HttpResponseRedirect(reverse('polls:results', args=(question.id,)))
```

Explicando:

* **request.POST**: É um objeto do tipo dicionário, que permite o acesso a dados enviados na forma POST pelo *key name*. Nesse caso, estamos passando *choice* por que é como está o nome do radio da template.
* **except (KeyError, Choice.DoesNotExist)**: caso o usuário não selecione uma escolha, e tente enviar o formulário para processamento, retornaremos um HttpResponse para a mesma página, mas agora com uma menssagem de erro.
* **HttpResponseRedirect**: Caso o método POST seja bem sucedido, vai redirecionar o usuário para outra página.
* **reverse**: Serve para não ser necessário passar a URL de forma *hardcoded*. Sem isso, teríamos que passar algo como: `f'polls/{question_id}/results'`.

**==========================================================================================================================**

## GENERIC VIEWS

Muitas vezes no desenvolvimento web iremos fazer views semelhantes, como aconteceu no nosso simples Poll App, com as views detail(), results() e index(). Nesses exemplos estamos fazendo algo muito comum e rotineiro, pegando dados de um banco de dados de acordo com um parametro passado no URL, e por fim carregando um html.

É pensando nisso que no Django temos as chamadas Gerenic Views. Com elas, muitas vezes nem precisamos escrever código python para fazer uma aplicação.

Para implementar as View Genéricas, vamos primeiramente até o URLconf:

```python
from django.urls import path
from . import views

app_name = 'polls'
urlpatterns = [
    path('', views.IndexView.as_view(), name='index'),
    path('<int:pk>/', views.DetailView.as_view(), name='detail'),
    path('<int:pk>/results/', views.ResultsView.as_view(), name='results'),
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```

Note que o parâmetro da URL mudou de `int:question_id` para simplesmente `int:pk`.

Agora vamos até as views:

```python
from django.http import HttpResponseRedirect
from django.shortcuts import get_object_or_404, render
from django.urls import reverse
from django.views import generic
from .models import Choice, Question

class IndexView(generic.ListView):
    template_name = 'polls/index.html'
    context_object_name = 'latest_question_list'

    def get_queryset(self):
        """Return the last five published questions."""
        return Question.objects.order_by('-pub_date')[:5]

class DetailView(generic.DetailView):
    model = Question
    template_name = 'polls/detail.html'

class ResultsView(generic.DetailView):
    model = Question
    template_name = 'polls/results.html'

def vote(request, question_id):
    ... # same as above, no changes needed.
```

Estamos utilizando duas Views Genéricas aqui: ListView e DetailView. Respectivamente, elas server para: "mostrar uma lista de objetos", e "mostrar uma pagina de detalhes para um tipo de objeto em especifico":

* Cada Generic View precisa saber qual Model usar. Isso é provido através do atributo `model`.
* A DetailView espera a primary key capturada da URL ser chamada **pk**, por isso foi alterado no URLconf.

Por padrão a DetailView usa um template `<app name> /<model name>_detail.html`. Para sobreescrever isso, basta declarar o atributo `template_name`, como fizemos no exemplo acima.  De forma similar a ListView também usa dessa funcionalidade, no caso sendo `<app name>/<model name>_list.html`.

Nos nossos templates, estamos passando variaveis como `question` e `latest_question_list`. Para DetailView, essa variável é provida automaticamente, já que estamos usando a Django model (Question), o próprio framework consegue determinar o nome apropriado para a variável. Todavia, em ListView, a variável de contexto gerada automaticamente é `question_list`. Para sobreescrever isso nós fornecemos o atributo `context_object_name`.

**==========================================================================================================================**

## AUTOMATED TESTS

**O que é?**

Servem basicamente para testarmos a funcionalidade da nossa aplicação de maneira automática. Por exemplo, em um teste automatizado você poderia passar inputs padrões para servir sua aplicação, e verificar o retorno.

**Por que usar?**

Por eles basicamente serem muito úteis em salvar tempo do programador durante o desenvolvimento. Vale ressaltar também que essa técnica não é exclusiva do Django.

**Testes podem prevenir erros**

Muitas vezes por meio de um teste você pode prevenir erros que ainda nem chegaram a acontecer no uso cotidiano da sua aplicação.

**São ótimos para se trabalhar em equipe**

Um teste automatizado pode prevenir que você quebre o código de seu colega de trabalho, ou que ele causa algum dano ao seu também. Resumindo: **chega a ser quase uma necessidade saber não só realizar, mas criar testes**.

Muitas vezes programadores mais experientes irão escrever primeiramente o teste, para depois começar a desenvolver a aplicação. Isso é conhecimento como **test-driven development**. Mas, mesmo que você não tenha feito seu teste logo no início de um projeto, nunca é tarde de mais.

***Fazendo de fato um teste:***

Primeiramente devemos identificar um bug dentro de nossa aplicação. A função `was_published_recently` da classe Question em nossa models aceita não só questões que foram publicadas de fato recentemente, mas também questões que ainda nem foram publicadas - de uma data futura a atual:

```python
def was_published_recently(self):
        return self.pub_date >= timezone.now() - datetime.timedelta(days=1)
```

```shell
>>> import datetime
>>> from django.utils import timezone
>>> from polls.models import Question
>>> # create a Question instance with pub_date 30 days in the future
>>> future_question = Question(pub_date=timezone.now() + datetime.timedelta(days=30))
>>> # was it published recently?
>>> future_question.was_published_recently()
True
```

Então vamos criar um teste para apontar esse erro. Um lugar convencional para se realizar os testes é em `tests.py`. O sistema de teste irá identificar automaticamente testes caso o nome do arquivo comece com *test*.

```python
import datetime
from django.test import TestCase
from django.utils import timezone
from .models import Question

class QuestionModelTests(TestCase):
    def test_was_published_recently_with_future_question(self):
        """
        was_published_recently() returns False for questions whose pub_date
        is in the future.
        """
        time = timezone.now() + datetime.timedelta(days=30)
        future_question = Question(pub_date=time)
        self.assertIs(future_question.was_published_recently(), False, "Should return false!")
```

Para rodar o teste, basta digitar:

```bash
$ python manage.py test polls
```

```
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
F
======================================================================
FAIL: test_was_published_recently_with_future_question (polls.tests.QuestionModelTests)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/gabriel/Code/Python/learning-django/src/polls/tests.py", line 11, in test_was_published_recently_with_future_question
    self.assertIs(future_question.was_published_recently(), False, "Should return false!")
AssertionError: True is not False : Should return false!

----------------------------------------------------------------------
Ran 1 test in 0.001s

FAILED (failures=1)
Destroying test database for alias 'default'...
```

Vamos revisar o que aconteceu:

* `manage.py test polls` procurou por um teste na aplicação polls.
* Achou uma subclasse de `django.test.TestCase`.
* Criou um banco de dados especial apenas para o teste.
* Procurou por metodos de testes - que começam com `test`.
* Em `test_was_published_recently_with_future_question` criou uma instância de Question com `pub_date` colocado em 30 dias no futuro.
* E utilizando `assertIs()` foi descoberto que `was_published_recently()` retorna True, e não False. Sendo assim, ele nos retorna a mensagem personalizada que passamos: *"Should return false!"*.

***Consertando o bug***:

Para consertar o bug, devemos limitar o retorno da função:

```python
def was_published_recently(self):
    now = timezone.now()
    return now - datetime.timedelta(days=1) <= self.pub_date <= now
```

Rodando o teste novamente agora, vemos que tudo ocorreu bem.

***Tornando o teste mais real***:

Em um cenário real, não testaríamos apenas se o retorno da função corresponde a uma data não futura. Então vamos introduzir novos metódos de teste no nosso `tests.py`:

```python
def test_was_published_recently_with_old_question(self):
    """
    was_published_recently() returns False for questions whose pub_date
    is older than 1 day.
    """
    time = timezone.now() - datetime.timedelta(days=1, seconds=1)
    old_question = Question(pub_date=time)
    self.assertIs(old_question.was_published_recently(), False)

def test_was_published_recently_with_recent_question(self):
    """
    was_published_recently() returns True for questions whose pub_date
    is within the last day.
    """
    time = timezone.now() - datetime.timedelta(hours=23, minutes=59, seconds=59)
    recent_question = Question(pub_date=time)
    self.assertIs(recent_question.was_published_recently(), True)
```

Agora sim podemos ter certeza que tudo está ok com nossa função `was_published_recently()`.

**==========================================================================================================================**

## CUSTOMIZAÇÃO E STATIC FILES

Nossas aplicações web também precisam de outros tipos de arquivos, como scripts JS, estilizações CSS, e imagens em geral. Em Django tratamos esses tipos de arquivos como **static files**. Para isso, usaremos o `django.contrib.staticfiles`.

Primeiramente precisamos criar um diretório chamado *static* na pasta de nossa aplicação, como fizemos com os templates. Nas configurações de `STATICFILES_FINDERS` contém uma lista de "procuradores" para arquivos estáticos, um dos padrão é o `AppDirectoriesFinder`, que procura por um diretório chamado static em cada `INSTALLED_APPS`.

**Adicionando arquivos css:**

Dentro do diretório recém criado, vamos criar outro chamado polls com um style.css, ficando então: `project/app/static/polls/style.css`. Para referenciar esse arquivo no Django, podemos passar como `polls/style.css`:

```django
{% load static %}

<link rel="stylesheet" type="text/css" href="{% static 'polls/style.css' %}">
```

A tag template `{% static %}` gera um URL absoluto de static files.

**Adicionando imagens de background:**

Para isso precisamos criar um diretório para as imagens: `app/static/polls/images/background.gif`. Após isso, em nosso css:

```css
body {
    background: white url("images/background.gif") no-repeat;
}
```

