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
class Article(model.Model):
    title = models.CharField(max_length=100)
    description = models.TextField()

$ python manage.py makemigrations


