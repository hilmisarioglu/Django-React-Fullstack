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

# seralizers.py 
from rest_framework import serializers
from .models import Article 

class ArticleSerializer(serializers.Serializer):
    title = serializers.CharField(max_length=100)
    description = serializers.CharField(max_length=400)
    
def create(self,validated_data):
    return Article.objects.create(validated_data)

def update(self,instance,validated_data):
    instance.title = validated_data.get('title',instance.title)
    instance.description = validated_data.get('title',instance.description)

$ cd APIProject
$ python manage.py shell
>>> from api.models import Article
>>> from api.serializers import ArticleSerializer
>>> from rest_framework.renderers import JSONRenderer
>>> from rest_framework.parsers import JSONParser
>>> a = Article(title = 'Title3', description = 'desc3')
>>> a.save()
>>> serializer = ArticleSerializer(a)
>>> serializer.data
we rendered data in JSON 
>>> json = JSONRenderer().render(serializer.data)
>>> json
>>> import io
>>> stream = io.BytesIO(json)
>>> data = JSONParser().parse(stream)   
>>> serializer = ArticleSerializer(data=data) 
>>> serializer.is_valid()
>>> serializer.validated_data

# ModelSerializer
# serializers.py
diger yazilani yoruma al
class ArticleSerializer(serializers.ModelSerializer):
    class Meta:
        model = Article
        fields = ['id', 'title', 'description']

>>> from api.serializers import ArticleSerializer
>>> serializer=ArticleSerializer()
>>> print(repr(serializer))
ArticleSerializer():
    id = IntegerField(label='ID', read_only=True)
    title = CharField(max_length=100)
    description = CharField(style={'base_template': 'textarea.html'})

# Function Base Api Views 
# views.py
from django.shortcuts import render, HttpResponse
from .models import Article
from .serializers import ArticleSerializer
from django.http import JsonResponse
from rest_framework.parsers import JSONParser


# Create your views here.

def article_list(request):
    if request.method == 'GET':
        articles = Article.objects.all()
        serializer = ArticleSerializer(articles, many = True)
        return JsonResponse(serializer.data, safe=False)
    elif request.method == 'POST':
        data = JSONParser().parse(request)
        serializer= ArticleSerializer(data=data)
        if serializer.is_valid():
            serializer.save()
            return JsonResponse(serializer.data, status=201)
        return JsonResponse(serializer.errors, status=400)


# urls.py
from django.urls import path
from .views import article_list

urlpatterns = [
    path('articles', article_list),
]

# POSTMAN
GET http://127.0.0.1:8000/articles
[
    {
        "id": 1,
        "title": "Article 1",
        "description": "Descr 1"
    },
    {
        "id": 2,
        "title": "Article2",
        "description": "Desc2"
    },
    {
        "id": 3,
        "title": "Article3",
        "description": "Desc3"
    },
    {
        "id": 4,
        "title": "article4",
        "description": "Decs4"
    }
]

POST GET http://127.0.0.1:8000/articles
Body > row JSON
403Forbidden aldik

# views.py 
sunlari ekle 
from django.views.decorators.csrf import csrf_exempt
@csrf_exempt

Tekrardan POSTMAN a git 
POST GET http://127.0.0.1:8000/articles
Body > row JSON
{
    "title": "Article 6",
    "description": "Descr 6"
}   

201Created 65 ms 294 B döndü

# views.py
ekle

@csrf_exempt
def article_details(request,pk):
    try:
        article = Article.objects.get(pk=pk)
    except Article.DoesNotExist:
        return HttpResponse(status=404)
    
    if request.method == 'GET':
        serializer= ArticleSerializer(article)
    
    elif request.method == 'PUT':
        data = JSONParser().parse(request)
        serializer= ArticleSerializer(article,data=data)
        if serializer.is_valid():
            serializer.save()
            return JsonResponse(serializer.data)
        return JsonResponse(serializer.errors, status=400)
    
    elif request.method == 'DELETE':
        article.delete()
        return HttpResponse(status=204)

# urls.py 
from django.urls import path
from .views import article_list, article_details

urlpatterns = [
    path('articles', article_list),
    path('articles/<int:pk>/', article_details),
]

POSTMAN a git
PUT http://127.0.0.1:8000/articles/1/
Body > row JSON
{
  "title": "Updated",
  "description": "Updated"
}

200OK 83 ms 301 geldi

DELETE http://127.0.0.1:8000/articles/1/
204No Content geldi yani silindi


