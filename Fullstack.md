# Django-React-Fullstack

# Django Intro & Installation
$ python -m venv venv
$ source ./venv/Scripts/activate
$ pip install Django
https://www.django-rest-framework.org/
$ pip install djangorestframework

# First Project in Django
$ django-admin startproject APIProject
$ cd APIProject
$ python manage.py runserver
$ python manage.py migrate

# First App
$ python manage.py startapp api

# Django admin site
One of the most powerful parts of Django is the automatic admin interface. It reads metadata from your models to provide a quick, model-centric interface where trusted users can manage content on your site. The admin’s recommended use is limited to an organization’s internal management tool. It’s not intended for building your entire front end around.
$ python manage.py createsuperuser

# APIProject>urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('api.urls')),
]

# api>urls.py
from django.urls import path
from .views import Index

urlpatterns = [
    path('', Index),
]

# Models
A model is the single, definitive source of information about your data. It contains the essential fields and behaviors of the data you’re storing. Generally, each model maps to a single database table.

# settings.py
INSTALLED_APPS = ... 'api',

# models.py
class Article(models.Model):
    title = models.CharField(max_length=100)
    description = models.TextField()
    def __str__(self):
    return self.title

$ python manage.py makemigrations
$ python manage.py migrate

# admin.py 
from django.contrib import admin
from .models import Article

admin.site.register(Article) 

or

@admin.register(Article)
class ArticleModel(admin.ModelAdmin):
    list_filter = ('title','description')
    list_display = ('title','description')

# Serialization
https://www.django-rest-framework.org/api-guide/serializers/

Serializers allow complex data such as querysets and model instances to be converted to native Python datatypes that can then be easily rendered into JSON, XML or other content types.Serializers also provide deserialization, allowing parsed data to be converted back into complex types, after first validating the incoming data.

INSTALLED_APP , 'rest_framework',

