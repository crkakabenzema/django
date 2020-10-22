# django
django notes
1. ## Create a project:

`$ django-admin startporject projectName`

A project includes these files: 

`manage.py`: A command-line utility that lets you interact with this Django project in various ways.

`mysite/__init__.py`: An empty file that tells Python that this directory should be considered a Python package.

`mysite/settings.py`: Settings/configuration for this Django project.

`mysite/urls.py`: The URL declarations for this Django project.

`mysite/asgi.py`: An entry-point for ASGI-compatible web servers to serve your project

`mysite/wsgi.py`: An entry-point for WSGI-compatible web servers to serve your project

2. ## Run the server:

`$ python manage.py runserver`

3. ## Start an app:

`$ python manage.py startapp appName`

An app is laid out like this:

```
appName/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    views.p
```

4. ## **Write view**:

In appName/views.py:

```python
from django.http import HttpResponse
from django.http import Http404
from django.shortcuts import render

from .models import TableName

def index(request):
    # render()函数，第三个参数为context
    try:
        table_list = tableName.objects.order_by('-pub_date')[:5]
    except tableName.DoesNotExist:
        raise Http404("Question does not exist")
    return render(request,'appName/index.html',{'table_list':table_list})

def helloWorld(request):
    return HttpResponse("Hello, world. You're at the polls index.")
```

In appName/urls.py:

```python
from django.urls import path

from . import views

app_name = appName
urlpatterns = [
    # ex: /appName/
    path('', views.index, name='index'),
    # ex: /appName/id/
    path('<int:id>/', views.detail, name='detail'),
    # ex: /appName/5/results/
    path('<int:question_id>/results/', views.results, name='results'),
    # ex: /appName/5/vote/
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```

In projectName/urls.py:

```python
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('polls/', include('polls.urls')),
    path('admin/', admin.site.urls),
]
```

Installed_Apps list:

- [`django.contrib.admin`](https://docs.djangoproject.com/en/3.1/ref/contrib/admin/#module-django.contrib.admin) – The admin site. You’ll use it shortly.
- [`django.contrib.auth`](https://docs.djangoproject.com/en/3.1/topics/auth/#module-django.contrib.auth) – An authentication system.
- [`django.contrib.contenttypes`](https://docs.djangoproject.com/en/3.1/ref/contrib/contenttypes/#module-django.contrib.contenttypes) – A framework for content types.
- [`django.contrib.sessions`](https://docs.djangoproject.com/en/3.1/topics/http/sessions/#module-django.contrib.sessions) – A session framework.
- [`django.contrib.messages`](https://docs.djangoproject.com/en/3.1/ref/contrib/messages/#module-django.contrib.messages) – A messaging framework.
- [`django.contrib.staticfiles`](https://docs.djangoproject.com/en/3.1/ref/contrib/staticfiles/#module-django.contrib.staticfiles) – A framework for managing static files.

5. ## Database setup:

migrate command looks at the setting and create any necessary database tables:

`$ python manage.py migrate `

for database setting other than SQLite see the https://docs.djangoproject.com/en/3.1/ref/settings/#std:setting-DATABASES

6. ## Database layout model creation / Create models (database layout):

In appName/models.py:

```python
from django.db import models

class tableName1(models.Model):
    columnName1 = models.CharField(max_length=200)
    columnName2 = models.DateTimeField('date published')
    def __str__(self):
        return self.text

class tableName2(models.Model):
    columnName3 = models.ForeignKey(tableName1, on_delete=models.CASCADE)
    columnName4 = models.CharField(max_length=200)
    columnName5 = models.IntegerField(default=0)
    def __str__(self):
        return self.text
```

In projectName/settings.py:

```
INSTALLED_APPS = [
    'appName.apps.AppNameConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

In appName/admin.py:

```python
from django.contrib import admin

from .models import modelName

admin.site.register(modelName)
```



Database migrations 

(tell Django to make some changes to the models and store the changes as a migration):

`$ python manage.py makemigrations appName`

(run the migrations and manage database schema automatically)

`$ python manage.py sqlmigrate appName sqlName`

(create model tables in database and apply those changes to the database) 

`$ python manage.py migrate`

7. ## **Database API (Insert, Update, Delete)**

`$ python manage.py shell`

```sqlite
from polls.models import TableName

# show all objects
TableName.objects.all()

# insert
q = TableName(columnName1= content,columnName2= content)
q.save()

## insert foreign key content
q = TableName.objects.get(condition)
q.tableName_set.create(columnName1= content, columnName2= content)

# query
TableName.objects.filter(condition)
TableName.objects.get(condition)
## query example
TableName.objects.filter(id= number)
TableName.objects.filter(columnName_startswith=' ')
TableName.objects.get(pk=number)
## query foreign key table
## display any modelContent from the related object set
q = TableName.objects.get(condition)
q.tableName_set.all()
q.tableName_set.count()

# delete
q = TableName.objects.get(condition)
q.delete()
```

8. ## Create an admin user

`$ python manage.py createsuperuser`

9. ## Create template

create directory like this: appName/templates/appName

In appName/templates/appName: create templates html files 

10. ## Write a form:

request.POST is a dictionary-like object

reverse() function avoid having to hardcode a URL and return a string like: "/polls/3/results"

#example

In html:

```html
<-! form example->
<h1>{{ question.question_text }}</h1>

{% if error_message %}<p><strong>{{ error_message }}</strong></p>{% endif %}

<-! method post alter data server-side in request body. forloop.counter indicates how many times the for tag has gone through its loop->
<form action="{% url 'polls:vote' question.id %}" method="post">
{% csrf_token %}
{% for choice in question.choice_set.all %}
    <input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}">
    <label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br>
{% endfor %}
<input type="submit" value="Vote">
</form>
```

In views.py:

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

11. ## File open:

In views.py:

```python
from django.http import FileResponse
response = FileResponse(open('appName/fileName', 'rb'))
```

12. ## Static file:

Configure static files:

Make sure `django.contrib.staticfiles` is included in your [`INSTALLED_APPS`](https://docs.djangoproject.com/en/3.1/ref/settings/#std:setting-INSTALLED_APPS).

Define STATIC_URL: 

```python
STATIC_URL = '/static/'
```

A list of directories can also be defined in the settings file where Django will look for static files:

```python
STATICFILES_DIRS = [
    BASE_DIR / "static",
    '/var/www/static/',
]
```

Create a static file in the appName directory. 

In the static directory, create another directory called appName. In this directory, save the static css files like this: appName/static/appName/xxx.css

In the html, edit like this:

```html
{% load static %}

<link rel="stylesheet" type="text/css" href="{% static 'appName/xxx.css' %}">
```

In static file such as css, refer other static file like this: 

```css
body {
    background: white url("images/background.gif") no-repeat;
}
```

Deployment (gather static files in a single directory):

set the  [`STATIC_ROOT`](https://docs.djangoproject.com/en/3.1/ref/settings/#std:setting-STATIC_ROOT) setting to the directory

```python
STATIC_ROOT = "/var/www/example.com/static/"
```

Run the [`collectstatic`](https://docs.djangoproject.com/en/3.1/ref/contrib/staticfiles/#django-admin-collectstatic) management command:

```
$ python manage.py collectstatic
```

This will copy all files from your static folders into the [`STATIC_ROOT`](https://docs.djangoproject.com/en/3.1/ref/settings/#std:setting-STATIC_ROOT) directory.

13. ## Customize admin site:

In appName/admin.py

```python
from django.contrib import admin

from .models import Question, Choice

#choice is the foreign key of question model. when add class ChoiceInline in class QuestionAdmin, it will add choice to the question object.  
#admin.TabularInline display related model objects in a more compact format 
class ChoiceInline(admin.TabularInline):
    model = Choice
    extra = 3
    
# ModelAdmin edit the question admin page
class QuestionAdmin(admin.ModelAdmin):
    # the first element of each tuple in fieldsets is the title of the fieldset.
    fieldsets = [
        (None,               {'fields': ['question_text']}),
        ('Date information', {'fields': ['pub_date']}),
    ]
    inlines = [ChoiceInline]
    # display individual fields in the column list
    list_display = ('question_text', 'pub_date', 'was_published_recently')
    # add a "Filter" sidebar that filter the change list
    list_filter = ['pub_date']
    # add a search box at the top of the change list
    search_fields = ['question_text']

admin.site.register(Question, QuestionAdmin)
admin.site.register(Choice)
```

14. ## Customize the admin look and feel:

Create a `templates` directory in your project directory (the one that contains `manage.py`).

Open projectName/setting.py file and add a DIRS option in the TEMPLATES setting:

```python
# DIRS is a list of filesystem directories to check when loading Django templates.
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR + '/templates'],
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
```

Create a directory called admin inside "BASE_DIR/templates", and copy the chosen template from with the default Django admin template directory in the source code of Django itself (`django/contrib/admin/templates`) into that directory.

Where are the Django source files?

```
$ python -c "import django; print(django.__path__)"
```

15. ## Output PDFs with Django:

```
$ python -m pip install reportlab
```

Example:

```python
import io
from django.http import FileResponse
from reportlab.pdfgen import canvas

def some_view(request):
    # Create a file-like buffer to receive PDF data.
    buffer = io.BytesIO()

    # Create the PDF object, using the buffer as its "file."
    p = canvas.Canvas(buffer)

    # Draw things on the PDF. Here's where the PDF generation happens.
    # See the ReportLab documentation for the full list of functionality.
    p.drawString(100, 100, "Hello world.")

    # Close the PDF object cleanly, and we're done.
    p.showPage()
    p.save()

    # FileResponse sets the Content-Disposition header so that browsers
    # present the option to save the file.
    buffer.seek(0)
    return FileResponse(buffer, as_attachment=True, filename='hello.pdf')
```
16. ## Write reusable Apps:

pip install setuptools

Packaging your app:

Create a parent directory for app, outside of Django project, call this directory django-appName.

Move the appName directory into the django-appName directory.

Create a file django-appName/README.rst with the contents such as:

```
=====
Polls
=====

Polls is a Django app to conduct Web-based polls. For each question,
visitors can choose between a fixed number of answers.

Detailed documentation is in the "docs" directory.

Quick start
-----------

1. Add "polls" to your INSTALLED_APPS setting like this::

    INSTALLED_APPS = [
        ...
        'polls',
    ]

2. Include the polls URLconf in your project urls.py like this::

    path('polls/', include('polls.urls')),

3. Run ``python manage.py migrate`` to create the polls models.

4. Start the development server and visit http://127.0.0.1:8000/admin/
   to create a poll (you'll need the Admin app enabled).

5. Visit http://127.0.0.1:8000/polls/ to participate in the poll.
```

Create a django-appName/LICENSE file.

Create setup.cfg and setup.py files with the contents such as:

```cfg
[metadata]
name = django-polls
version = 0.1
description = A Django app to conduct Web-based polls.
long_description = file: README.rst
url = https://www.example.com/
author = Your Name
author_email = yourname@example.com
license = BSD-3-Clause  # Example license
classifiers =
    Environment :: Web Environment
    Framework :: Django
    Framework :: Django :: X.Y  # Replace "X.Y" as appropriate
    Intended Audience :: Developers
    License :: OSI Approved :: BSD License
    Operating System :: OS Independent
    Programming Language :: Python
    Programming Language :: Python :: 3
    Programming Language :: Python :: 3 :: Only
    Programming Language :: Python :: 3.6
    Programming Language :: Python :: 3.7
    Programming Language :: Python :: 3.8
    Topic :: Internet :: WWW/HTTP
    Topic :: Internet :: WWW/HTTP :: Dynamic Content

[options]
include_package_data = true
packages = find:
```

```python
from setuptools import setup

setup()
```

Create an empty directory `django-polls/docs` for future documentation.

Create a django-appName/MAINFEST.in file:

```in
include LICENSE
include README.rst
recursive-include polls/static *
recursive-include polls/templates *
recursive-include docs *
```

 Build your package with `python setup.py sdist` (run from inside `django-polls`). 


17. ## Session:
Activate SessionMiddleware:

By default, Django stores sessions in database (using the model `django.contrib.sessions.models.Session` ). 

For the use of database-backed session, add `'django.contrib.sessions'` to [`INSTALLED_APPS`] setting. 

Run `manage.py migrate` to install the single database table that stores session data.

 Each [`HttpRequest`](https://docs.djangoproject.com/en/3.1/ref/request-response/#django.http.HttpRequest) object – the first argument to any Django view function – will have a `session` attribute, which is a dictionary-like object, when middleware activated.

example:

```python
from django.shortcuts import render

# Create your views here.
from django.http import HttpResponse

from .models import Account

def registerSubmit(request):
    userName = request.POST['username']
    passWord = request.POST['password']
    confirmPassword = request.POST['password_confirm']
    try:
        q = Account.objects.get(username = request.POST['username'])
    except (KeyError, Account.DoesNotExist):
        if passWord == confirmPassword:
            # insert into table
            q = Account(username= request.POST['username'], password=request.POST['password'])
            q.save()
            return HttpResponse("Register successfully.")
        else:
            # redisplay the register form
            return render(request, 'login/register.html', {'error_message': "password and confirm_password are not the same."})
    else:
        return render(request, 'login/register.html', {'error_message': "username already exists."})

def login(request):
    m = Account.objects.get(username=request.POST['username'])
    if m.password == request.POST['password']:
       # Session data is stored in a database table named django_session . use command "django-admin clearsessions" to clean the database
        request.session['member_id'] = m.id
        return HttpResponse("You're logged in."+request.session["member_id"])
    else:
        return HttpResponse("Your username and password didn't match.")
    
def logout(request):
    try:
        del request.session['member_id']
    except KeyError:
        pass
    return HttpResponse("You're logged out.")

```

Use command "django-admin clearsessions" to clean out expired sessions in database.


















