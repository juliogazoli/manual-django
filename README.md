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
``` py
{% load static %}
```
Exemplo: 
``` html
href="{% static '/styles/style.css' %}"
```

Indicar links (urls) no arquivo .html:
Exemplos:
``` html
<a href="{% url 'outra_pagina' %}">
<a href="{% url 'index' %}">
```

DRY - Don't Repeat Yourself
Arquivo base.html:
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

Arquivo index.html:
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
_menu
_footer
```
	
No arquivo base.html:
``` html
<html>
	<head>
		...
	</head>
	<body>
		{% block content %} {% endblock %}
		{% include 'partials/_footer.html' %}
	</body>
</html>
```

---

## Views

Escrever a View
(responsável pela exibição das páginas, renderizar)
``` py
from django.shortcuts import render

def index(request):
	return render(request, 'index.html')
```

---

## URLs

Escrever URLs (responsável pelas rotas):  

Criar arquivo urls.py dentro da pasta do app:
 ``` py
from django.urls import path
from meu_app.views import index

urlpatterns = [
	path('', index, name='index')
	path('outra_pagina/', outra_pagina, name='outra_pagina')
]
```

No arquivo urls.py do setup:
``` py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('meu_app.urls')),
]
```

---

## Banco de Dados

Criar migrações para banco de dados:
``` py
python manage.py makemigrations
```

Executar as migrações:
``` py
python manage.py migrate
```

Models - layout do banco de dados, com metadados adicionais.

Após criar os models do app, criar as migrações:
``` py
python manage.py makemigrations polls
```

---

## Admin

Criar super usuáiro Admin:
``` py
python manage.py createsuperuser
```

Cadastrar aplicação editável no site de administração:
No arquivo meu_app/admin.py
``` py
from django.contrib import admin

from .models import Classe_Model

admin.site.register(Classe_Model)
```
