# Django

## Preparando ambiente

Criar venv:  
(Isolar as dependências com ambiente virtual)
``` sh
virtualenv venv
```
Ativar venv (Windows):
``` sh
.\venv\Scripts\activate
```

Ativar venv (Linux):
``` sh
source venv/bin/activate
```

Instalar Django no venv:
``` sh
pip install django
```

Gerar arquivo de dependências no venv  
(executar sempre que houver uma nova instalação)  
(retirar as versões para instalar sempre a mais recente):  
``` sh
pip freeze > requirements.txt
```

Instalar dependências a partir do requirements.txt:
``` sh
pip install -r requirements.txt
```

Visualizar comandos Django:
``` sh
django-admin help
```

Criar projeto Django  
(sem o ponto no final do comando, o Django irá criar uma subpasta 'setup'):
``` sh
django-admin startproject setup .
```

Executar o servidor:
``` sh
python manage.py runserver
```

Acessar:  
http://127.0.0.1:8000/

Parar execução:
```
Ctrl + C
```

Visualizar comandos manage.py:
``` py
python manage.py help
```

Criar App (no mesmo diretório que manage.py):
``` py
python manage.py startapp nome_do_app
```

---

## Settings (setup settings.py)

### .ENV  

SECRET_KEY
* Instalar DotEnv:
``` py
pip install python-dotenv
 ```

* Criar arquivo .env na pasta raiz
* Remover a SECRET_KEY do arquivo settings.py
* Preencher arquivo .env com a chave (sem aspas):
 ``` py
SECRET_KEY = django-insecure-v(8j@a+t7iie...
```

* Importar dotenv em settings.py:
``` py
from dotenv import load_dotenv
load_dotenv()
 ```

* Em settings.py:
 ``` py
SECRET_KEY = str(os.getenv('SECRET_KEY'))
 ```

* Criar arquivo .gitignore
* Copiar de [gitignore.io](https://www.toptal.com/developers/gitignore/)  os arquivos a serem ignorados - [gitignore.io - Django](https://www.toptal.com/developers/gitignore/api/django)  

Alterar settings.py:
``` py
LANGUAGE_CODE = 'pt-br'
TIME_ZONE = 'America/Sao_Paulo'
```

Adicionar o app ('meu_app.apps.MeuAppConfig') ao settings.py 
``` py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'meu_app.apps.MeuAppConfig',
]
```

---

## Templates

Criar pasta templates na raiz
	Criar arquivo index.html dentro da pasta templates
	
No arquivo settings.py, TEMPLATES:

``` py
from pathlib import Path, os

'DIRS': [os.path.join(BASE_DIR, 'templates')]
```
	
Configurar pasta de arquivos estáticos settings.py
``` py
STATICFILES_DIRS = [
	os.path.join(BASE_DIR, 'setup/static')
]

STATIC_ROOT = os.path.join(BASE_DIR, 'static')
```

Endereçar os arquivos estáticos:
``` py
python manage.py colletstatic
```

Indicar no arquivo .html dependências de arquivos estáticos:
``` django
{% load static %}
<html>
	<head>
		...
	</head>
	<body>
		...
	</body>
</html>
```

Carregar arquivos estáticos: 
``` django
<link rel="stylesheet" href="{% static '/styles/style.css' %}">
<img src="{% static 'img/minha_imagem.png' %}" alt="">
```

Carregar links (urls):
``` django
<a href="{% url 'index' %}">
<a href="{% url 'outra_pagina' %}">
```

DRY - Don't Repeat Yourself  
Arquivo **base.html**:
``` django
<html>
	<head>
		...
	</head>
	<body>
		{% block content %} {% endblock %}
	</body>
</html>
```

Arquivo **index.html**:
``` django
{% extends 'base.html' %}
{% load static %}
{% block content %}
	...
{% endblock %}
```

Na pasta templates criar a pasta partials
```
_nome.html
_menu.html
_footer.html
_alerts.html
```
	
No arquivo **index.html**:
``` django
<html>
	<head>
		...
	</head>
	<body>
		{% include 'partials/_menu.html' %}
		...
		{% include 'partials/_footer.html' %}
	</body>
</html>
```

Campo de busca:
``` django
<form action="{% url 'buscar' %}">
	<input type="text" name="buscar" placeholder="buscar">
	<button type="submit"></button>
</form>
```

---

## Views

Escrever a View
(responsável pela exibição das páginas, renderizar)
``` py
from django.shortcuts import render

def index(request):
	return render(request, 'index.html')

def outra_pagina(request):
	return render(request, 'outra_pagina.html')
```

---

## URLs

Escrever URLs (responsável pelas rotas):  

Criar arquivo urls.py dentro da pasta do app:
 ``` py
from django.urls import path
from meu_app.views import index, outra_pagina

urlpatterns = [
	path('', index, name='index')
	path('outra_pagina/', outra_pagina, name='outra_pagina')
]
```

No arquivo urls.py do setup:
``` py
from django.contrib import admin
from django.urls import path, include
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('meu_app.urls')),
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT) # Utilizando arquivos de mídia
```

---

## Banco de Dados

Instalar driver PostgresSQL no venv:
``` sh
pip install psycopg2
pip install psycopg2-binary
```


Em settings.py
``` py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'OPTIONS':{
            'options': '-c search_path=meu_schema'
        },
        'HOST': 'IP_DO_SERVIDOR',
        'PORT': 'PORTA_DO_SERVIDOR',
        'NAME': 'NOME_DO_BANCO',
        'USER': 'USUARIO_DO_BANCO',
        'PASSWORD': str(os.getenv('DB_PASS')),
    }
}
```

Arquivo **.env**
``` py
DB_PASS=senha_do_banco
```

Criar migrações para banco de dados:
``` py
python manage.py makemigrations
```

Executar as migrações:
``` py
python manage.py migrate
```

## Models
Layout do banco de dados, com metadados adicionais.  
Cada atributo do model representa um campo no banco de dados.

Arquivo **models.py**
``` py
class MinhaTabela(models.Model):
	campo_texto = models.CharField(max_length=200)
	campo_caixa_texto = models.TextField()
	campo_inteiro = models.IntegerFiels()
	campo_data = models.DateField()
	campo_data = models.DateTimeField(default=datetime.now, blank=True)
	campo_logico = models.BooleanField()
	campo_chave_estrangeira = models.ForeignKey('Outra_Classe', on_delete=models.SET_NULL, null=True, blank=True)
	campo_imagem = models.ImageField(upload_to='imagens/%d/%m/%Y/', blank=True) # Caminho em que será salva a imagem (neste exemplo, subdividido por dia, mês e ano)
	publicado = models.BooleanField(default=False)

	def __str__(self):
		return self.campo_texto # Altera o campo a ser exibido quando o objeto for referenciado
```

Para utilizar o campo de imagens:
* Acrescentar o arquivo **settings.py**:
``` py
# Media
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
MEDIA_URL = '/media/'
```

* Instalar Pillow
``` sh
pip install pillow
```


Após criar os models do app, criar as migrações:
``` sh
python manage.py makemigrations meu_app
```

Migrar as tabelas para o banco
``` sh
python manage.py migrate
```

### Exibir dados do banco  
Caso apresente o erro "Class 'MinhaClasse' has no 'objects' member", instalar:
```sh
pip install pylint-django
```

E em settings.json, adicionar:
```json
"python.linting.pylintArgs": [
	"--load-plugins=pylint_django"
],
```

No arquivo **views.py**
```py
from django.shortchuts import render
from .models import MinhaClasse

def index(request):
	objetos = MinhaClasse.objects.all()

	# Opção para filtar campos a serem exibidos
	# objetos = MinhaClasse.objects.filter(publicado=True)

	# Opção para ordenar a apresentação (-decrescente)
	# objetos = MinhaClasse.objects.order_by('-nome_campo').filter(publicado=True)

	dados = {
		'objetos': objetos
	}
	return render(request, 'index.html', dados)
```

No arquivo **.html**
``` django
<div class="container">
	<div class="row">
		{% if objetos %} <!-- Se o objeto não estiver vazio -->
			{% for objeto in objetos %}
				<a href="{% url 'minha_pagina' objeto.id %}">
					<h1>{{ objeto.campo_texto }}</h1>
				</a>
				<!-- Exibir imagem -->
				<img src="{{ objeto.campo_imagem.url }}">
			{% endfor %}
		{% else %}
			<p>Objeto não encontrado</p>
		{% endif %}
	</div>
</div>
```

No arquivo **urls.py**
```py
from django.urls import path
from . import views

urlpatterns = [
	path('', views.index, name='index'),
	path('int:minha_view_id>', views.minha_view, name='minha_view')
	path('busca', views.buscar, name='buscar') # Página de busca
]
```

No arquivo **views.py**:
```py
from django.shortcuts import render, get_list_or_404, get_object_or_404
from .models import MinhaClasses

def index(request):
	return render(request, 'index.html')

def minha_view(request, minha_view_id):
	minha_view = get_object_or_404(MinhaClasse, pk=minha_view_id)

	view_a_exibir = {
		'view': minha_view
	}

	return render(request, 'meu_arquivo.html', view_a_exibir)

def buscar(request):
	lista_objetos = MinhaClasse.objects.order_by('-nome_campo').filter(publicado=True)

	if 'buscar' in request.GET:
		campo_a_buscar = request.GET['buscar']
		if buscar:
			lista_objetos = lista_receitas.filter(campo_texto__icontains=campo_a_buscar)
	
	dados = {
		'objetos': lista_objetos
	}

	return render(request, 'buscar.html', dados)

```

### Integrar modelos
No arquivo **models.py**
``` py
from outra_tabela.models import MinhaClasse

class MinhaTabela(models.Model):
	campo_outra_tabela = models.ForeignKey(MinhaClasse, on_dele=models.CASCADE) # Campo que será chave estrangeira
	campo_texto = models.CharField(max_length=200)
	campo_caixa_texto = models.TextField()
	campo_inteiro = models.IntegerFiels()
	campo_data = models.DateField()
	campo_data = models.DateTimeField(default=datetime.now, blank=True)
	campo_logico = models.BooleanField()
```

Realizar ```makemigrations``` e ```migrate```

---

## Admin

Criar super usuáiro Admin:
``` sh
python manage.py createsuperuser
```

Cadastrar aplicação editável no site de administração:
No arquivo meu_app/admin.py
``` py
from django.contrib import admin
from .models import Classe_Model

admin.site.register(Classe_Model)
```

Editar display Admin (arquivo meu_app - admin.py)
``` py
from django.contrib import admin
from .models import MinhaTabela

class ListandoNomes(admin.ModelAdmin):
	list_display = ('id', 'campo_texto', 'publicado') # Campos a serem exibidos (campos do model)
	list_display_links = ('id', 'campo_texto') # Campos de links
	search_fields = ('nome_campo',) # Pesquisa
	list_filter = ('nome_campo',) # Filtro
	list_editable = ('pulicado',) # Campo editável
	list_per_page = 10 # Paginação

admin.site.register(MinhaTabela, ListandoNomes)
```

---

## Forms

Criar arquivo **forms.py** na pasta do app

``` py
from django import forms
from tempus_dominus.widgets import DatePicker # Verificar instalação tempus dominus
from meu_app.escolhas import lista_de_escolhas

class MinhaClasseForms(forms.Form):
	campo_texto_1 = forms.CharField(label='Nome a ser exibido', max_length=100)
	campo_texto_2 = forms.CharField(label='Nome a ser exibido', max_length=100)
	campo_data = form.DateField(label='Nome a ser exibido', widget=DatePicker()) # tempus dominus
	campo_desabilitado = form.CharField(label='Nome a ser exibido', max_length=100, disable=True)
	campo_escolhas = form.ChoiceField(label='Nome a ser exibido', choices=lista_de_escolhas)
	campo_email = forms.EmailField(label='Nome a ser exibido', max_lenght=150),
	campo_texto = forms.CharField( # Campo de escolhas
		label='Nome a ser exibido',
		max_lenght=200,
		widget=forms.Textarea(),
		required=False
	)

	# Validação de formulário
	def clean_campo_texto_1(self):
		campo_texto_1 = self.cleaned_data.get('campo_texto_1')
		if any(char.isdigit() for char in campo_texto_1):
			raise forms.ValidationError('Não inclua números')
		else:
			return campo_texto_1

	def clean(self):
		campo_texto_1 = self.cleaned_data.get('campo_texto_1')
		campo_texto_2 = self.cleaned_data.get('campo_texto_2')
		lista_de_erros = {}
		
		return self.cleaned_data
```

Arquivo **validation.py**:
``` py
def campo_texto_1_campo_texto_2_iguais(campo_texto_1, campo_texto_2, lista_de_erros):
	if campo_texto_1 == campo_texto_2:
		lista_de_erros['campo_texto_2'] = 'Campos não podem ser iguais'

def campo_tem_algum_numero(valor_campo, nome_campo, lista_de_erros):
	if any(char.isdigit() for char in campo_texto_1):
			raise forms.ValidationError('Não inclua números')
```

No arquivo **escolhas.py**:
``` py
lista_de_escolhas = {
	(1, 'Primeira opção'),
	(2, 'Segunda opção'),
	(3, 'Terceira opção'),
}
```

No arquivo **views.py**:

``` py
from django.shortcuts import render
from meu_app.forms import MinhaClasseForms

def index(request):
	form = PassagemForms()
	contexto = {'form':form}
	return render(request, 'index.html', contexto)

def consulta(request):
	if request.method == 'POST':
		form = MinhaClasseForms(request.POST)
		if form.is_valid():
			contexto = {'form':form}
			return render(request, 'consulta.html', contexto)
		else:
			print('Form inválido')
			contexto = {'form':form}
			return render(request, 'index.html', contexto)
```

No arquivo **urls.py**:

``` py
from django.urls import path
from . import views

urlpatterns = [
	path('', views.index, name='index'),
	path('consulta', views.consulta, name='consulta')
]
```

No arquivo **index.html**:

``` django
{% extends "base.html" %}
{% block content %}
{% load widget_tweaks %} <!-- Verificar instalação Widget Tweaks -->
	<section class="container col8">
		<form action="{% url 'consulta' %}" method="post">
			{% csrf_token %}
			{% for field in form.visible_fields %}
				<div class="form-group">
					<label for="{{ field.id_for_label }}"> {{ field.label }} </label>
					{{ field|add_class:'form-control' }} <!-- Adiciona classe aos campos -->
				</div>
				{% for error in field.errors %}
					<section class="alert alert-danger" role="alert">
						{{ field.errors }}
					</section>
				{% endfor %}
			{% endfor %}
			<input type="submit" value="Ok">
		</form>
	</section>
{% endblock %}
```

No arquivo **consulta.html**:

``` django
<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Consulta</title>
</head>
<body>
	<p>Campo 1: {{ form.campo_texto_1.value }}</p>
	<p>Campo 2: {{ form.campo_texto_2.value }}</p>
	<p>Campo 2: {{ form.campo_data.value }}</p>
</body>
</html>
```

### [Django Tempus Dominus](https://pypi.org/project/django-tempus-dominus/)

``` sh
pip install django-tempus-dominus
```

arquivo settings.py
``` py
INSTALLED_APPS = [
	...
	'tempus_dominus'
	...
]

TEMPUS_DOMINUS_LOCALIZE = True
```

``` django
<html>
  <head>
    {# Include FontAwesome; required for icon display #}
    <link rel="stylesheet" href="https://netdna.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.css">

    {# Include Bootstrap 4 and jQuery #}
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css" integrity="sha384-MCw98/SFnGE8fJT3GXwEOngsV7Zt27NXFoaoApmYm81iuXoPkFOJwJ8ERdknLPMO" crossorigin="anonymous">
    <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>

    {# Django Tempus Dominus assets are included in `{{ form.media }}` #}
    {{ form.media }}
  </head>

  <body>
    <div class="container">
      <div class="row">
        <div class="col">
          <form method="post" action=".">
            {% csrf_token %}
            {{ form.as_p }}
          </form>
        </div>
      </div>
    </div>
  </body>
</html>
```

### [Djago Widget Tweaks](https://pypi.org/project/django-widget-tweaks/)

``` sh
pip install django-widget-tweaks
```

arquivo settings.py
``` py
INSTALLED_APPS = [
	...
	'widget_tweaks',
	...
]
```

---
---

### Criar botão Excluir  
arquivo **.html**
``` django
<a href="{% url 'deleta_item' item.id %}" type="button" class="btn btn-danger"> Excluir</a>
```

arquivo **url.py**
``` py
path('deleta/<int:item_id>', views.deleta_item, name='deleta_item'),
```

arquivo **views.py**
``` py
def deleta_item(request, item_id):
	item = get_object_or_404(MinhaClasse, pk=item_id)
	item.delete()
	return redirect('index')
```

### Criar botão Editar
arquivo .html
``` django
<a href="{% url 'edita_item' item.id %}" type="button" class="btn btn-info"> Editar</a>
```

arquivo url.py
``` py
path('edita/<int:item_id>', views.edita_item, name='edita_item'),
path('atualiza_item', views.atualiza_item, name='atualiza_item'),
```

arquivo views.py
``` py
def edita_item(request, item_id):
	item = get_object_or_404(MinhaClasse, pk=item_id)
	item_a_editar = { 'item': item }
	return render(request, 'edita_item.html', item_a_editar)

def atualiza_item(request):
	if request.method == 'POST':
		item_id = request.POST['item_id']
		i = MinhaClasse.objects.get(pk=item_id)
		i.nome_item = request.POST['nome_item']
		i.campo_texto = request.POST['campo_texto']
		if imagem in request.FILES:
			i.imagem = request.FILES['imagem']
		i.save()
		return redirect('index')
```

arquivo edita_item.html
``` django
<form action="{% url 'atualiza_receita' %}">
```


---  
### Dividir views

* Criar pasta views
* Criar arquivos .py referente às views
* Arquivo urls.py
``` py
from .views import *

urlpatterns = [
	path('', index, name='index'),
	path('buscar', buscar, name='buscar'),
]
```
* Criar arquivo \_\_init__.py
* No arquivo \_\_init__.py
``` py
from .arquivo_view import *
```

### Pasta apps

* Criar pasta apps na raiz do projeto  
* Mover a pasta de cada app para pasta apps
* Alterar arquivo settings.py
``` py
import os, sys

PROJECT_ROOT = os.path.dirname(__file__)
sys.path.insert(0, os.path.join(PROJECT_ROOT, '../apps'))
```

---

### Paginação

arquivo views.py
``` py
from django.core.paginator import Paginator, EmptyPage, PageNotAnInteger

def index(request):
	objetos = MinhaClasse.objects.order_by('-campo_data').filter(pulicado=True)
	paginator = Paginator(objetos, 10)
	page = request.GET.get('page')
	objetos_por_pagina = paginator.get_page(page)

	dados = {
		'objetos': objetos_por_pagina
	}
	return render(request, 'index.html', dados)
```

arquivo index.html
``` django
<!-- ##### Pagination ##### -->
    <section class="top-catagory-area section-padding-20-0">
        <div class="container">
			{% if objetos.has_other_pages %}
            <ul class="pagination">
				{% if objetos.has_previous %}
                <li class="page-item">
                    <a href="?page={{ objetos.previous_page_number }}" class="page-link">&laquo;</a>
                </li>
				{% else %}
                <li class="page-item disabled">
                    <a class="page-link">&laquo;</a>
                </li>
				{% endif %}
				{% for pagina in objetos.paginator.page_range %}
				{% if objetos.number == pagina %}
                <li class="page-item active">
                    <a class="page-link">{{ pagina }}</a>
                </li>
				{% else %}
                <li class="page-item">
                    <a href="?page={{ pagina }}" class="page-link">{{ pagina }}</a>
                </li>
				{% endif %}
				{% endfor %}
				{% if objetos.has_next %}
                <li class="page-item">
                    <a href="?page={{ objetos.next_page_number }}" class="page-link">&raquo;</a>
                </li>
				{% else %}
                <li class="page-item disabled">
                    <a class="page-link">&raquo;</a>
                </li>
				{% endif %}
            </ul>
			{% endif %}
        </div>
    </section>
    <!-- ##### Pagination End ##### -->
```
