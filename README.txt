## https://www.youtube.com/watch?v=GHWnCH3caUc&list=PLwDQt7s1o9J5XM4xhwQJb4EQaPyFmwd6S&index=4
## https://youtu.be/4dVbN35WujQ?list=PLwDQt7s1o9J5XM4xhwQJb4EQaPyFmwd6S&t=382
## https://orcahmlee.github.io/devops/nginx-uwsgi-django-root/ ###



######## INSTALL Django ##########
apt install curl net-tools vim
apt install tzdata
apt install libpcre3 libpcre3-dev
apt install python3 python3-pip
apt install libpq-dev python3-dev

pip install django
pip install djangorestframework
pip install django-cors-headers
pip install requests psycopg2 ldap3
pip install uwsgi --no-cache-dir
######## END INSTALL Django ##########


######## nginx/nginx.conf ##########
server {
    listen 80;
    # server_name 0.0.0.0; 
    location / {
        # include uwsgi_params;
        proxy_pass http://api:8003/;
 
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header  X-Forwarded-Proto $scheme;
    }   
}


######## web/uwsgi.ini ##########
[uwsgi]
http = :8003
# socket = :8003
master=true
module=dj3.wsgi:application
processes=4
threads=2
vacuum=true
#pidfile = /tmp/dj3-master.pid


######## docker-compose.yml ##########
version: '3'
services:
    python:
        container_name: api
        image: python:latest
        working_dir: /web
        # expose:
        # - "8003"
        ports:
            - "8003:8003"
        volumes:
            - ./web:/web
        command: uwsgi --ini uwsgi.ini

    nginx:
        container_name: nginx
        # build: ./nginx
        image: nginx:latest
        ports:
            - "80:80"
        volumes:
            - ./web:/web
            - ./nginx/log:/var/log/nginx
            - ./nginx/nginx.conf:/etc/nginx/conf.d/default.conf
            # - ./my_htpasswd:/my_htpasswd








django-admin startproject HelloDjango
python3 ./manage.py startapp app

#最後一步將我們的表提交或發送到資料庫伺服器 
python3 ./manage.py migrate

#更新資料庫模型（每次 models.py 發生變更時，我們都必須執行此命令，例如新增資料表、變更欄位名稱等
python3 ./manage.py makemigrations

python3 ./manage.py runserver

#### vi setting.py #########################################
import os

ALLOWED_HOSTS = ["*"]
LANGUAGE_CODE = 'zh-hant'
TIME_ZONE = 'Asia/Taipei'

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app',  ### << 加入 project 名稱 'app'
]

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')],  ## <<< dir 加入 templates
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]




#############################################
vi urls.py
from django.contrib import admin
from django.urls import path

from app import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('hello/',views.hello),
]

#############################################
vi views.py

from django.http import HttpResponse
from django.shortcuts import render

# Create your views here.

def hello(request):
    return HttpResponse("okok")



#### for templates #########################################
vi settings.py
import os

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')],  ## <<< dir 加入 templates
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]


#### for templates #########################################
vi views.py

def hello(request):
    context          = {}
    context['hello'] = 'Hello World!'
    return render(request, 'runoob.html', context)    


#### for templates #########################################
vi  /app/HelloDjango/templates/runoob.html
<h1>{{ hello }}</h1>







#### for postgres #########################################
vi settings.py

INSTALLED_APPS = [
    'app',   ## <<< 加入 app 名稱
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]


DATABASES = {
    'default': {
        #'ENGINE': 'django.db.backends.postgresql',
        'ENGINE': 'django.db.backends.postgresql_psycopg2',        
        'NAME': 'postgres',  #資料庫名稱
        'USER': 'postgres',  #資料庫帳號
        'PASSWORD': 'postgres123456',  #資料庫密碼
        'HOST': 'localhost',  #Server(伺服器)位址
        'PORT': '5432'  #PostgreSQL Port號
    }
}


#### for postgres #########################################
vi /app/HelloDjango/app/models.py

from django.db import models

# Create your models here.

class Teacher(models.Model):
    name = models.CharField(max_length=80)
    age = models.IntegerField()

class abcd(models.Model):
    name1 = models.CharField(max_length=80)
    name2 = models.CharField(max_length=80)
    name3 = models.CharField(max_length=80)
    age = models.IntegerField()

#### for postgres #########################################
python3 ./manage.py migrate
python3 ./manage.py makemigrations

#### for postgres insert #########################################
vi /app/HelloDjango/app/views.py

from django.http import HttpResponse
from django.shortcuts import render
from app.models import Teacher

# Create your views here.
def hello(request):
    Teacher.objects.create(name="bbbb",age=1)
    return HttpResponse("okok")



