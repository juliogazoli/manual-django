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
``` html
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
``` html
<link rel="stylesheet" href="{% static '/styles/style.css' %}">
<img src="{% static 'img/minha_imagem.png' %}" alt="">
```

Carregar links (urls):
``` html
<a href="{% url 'index' %}">
<a href="{% url 'outra_pagina' %}">
```

DRY - Don't Repeat Yourself  
Arquivo **base.html**:
``` html
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
``` html
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
``` html
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
``` html
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
Caso apresente o erro "Class 'Minha_Classe' has no 'objects' member", instalar:
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
from .models import Minha_Classe

def index(request):
	objetos = Minha_Classe.objects.all()

	# Opção para filtar campos a serem exibidos
	# objetos = Minha_Classe.objects.filter(publicado=True)

	# Opção para ordenar a apresentação (-decrescente)
	# objetos = Minha_Classe.objects.order_by('-nome_campo').filter(publicado=True)

	dados = {
		'objetos': objetos
	}
	return render(request, 'index.html', dados)
```

No arquivo **.html**
``` html
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
from .models import Minha_Classes

def index(request):
	return render(request, 'index.html')

def minha_view(request, minha_view_id):
	minha_view = get_object_or_404(Minha_Classe, pk=minha_view_id)

	view_a_exibir = {
		'view': minha_view
	}

	return render(request, 'meu_arquivo.html', view_a_exibir)

def buscar(request):
	lista_objetos = Minha_Classe.objects.order_by('-nome_campo').filter(publicado=True)

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
from outra_tabela.models import Minha_Classe

class MinhaTabela(models.Model):
	campo_outra_tabela = models.ForeignKey(Minha_Classe, on_dele=models.CASCADE) # Campo que será chave estrangeira
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
