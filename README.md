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

18. ## Authentication Views

The easiest way is to include the provided URLconf in `django.contrib.auth.urls` in your own URLconf, for example:

```
urlpatterns = [
    path('accounts/', include('django.contrib.auth.urls')),
]
```

This will include the following URL patterns:

```
accounts/login/ [name='login']
accounts/logout/ [name='logout']
accounts/password_change/ [name='password_change']
accounts/password_change/done/ [name='password_change_done']
accounts/password_reset/ [name='password_reset']
accounts/password_reset/done/ [name='password_reset_done']
accounts/reset/<uidb64>/<token>/ [name='password_reset_confirm']
accounts/reset/done/ [name='password_reset_complete']
```

If you want more control over your URLs, you can reference a specific view :

```python
from django.contrib.auth import views as auth_views

urlpatterns = [
    path('change-password/', auth_views.PasswordChangeView.as_view()),
]
```

The views have optional arguments to alter the behavior of the view.

```python
# for example
auth_views.PasswordChangeView.as_view(template_name='change-password.html'),
```

If you want to change Login_URL, login_redirect_url, logout_redirect_url, in settings.py

```python
# for example
LOGIN_REDIRECT_URL = '/appname/'
```

19. ## **User Model for authentication**：

User, Permission and Group are the authentication model.

If you **just want to define some methods**, use proxy:

```python
from django.contrib.auth.models import User
class ProxyUser(User):
    class Meta:
        proxy = True # define proxy model
    def get_data(self):
        return self.objects.filter('filter condition')
```

If you **want to extend User model**:

In appname/models.py: create a User model

```python
from django.contrib.auth.models import User
from django.db import models

class UserProfile(models.Model):
    
    # UserProfile and User are OneToOne
    user = models.OneToOneField(User, on_delete=models.CASCADE, related_name='profile')
    
    org = models.CharField('Organization', max_length=128, blank=True)
    
    tele = models.CharField('Telephone', max_lenght=50, blank=True)
    
    mod_date = models.DateTimeField('Last modified', auto_now=True)
    
    class Meta:
        verbose_name = 'User Profile'
        
    def __str__(self):
        return self.user.__str__()
```

**If you want to override User model and rewrite it**,  in models.py:

```python
from django.contrib.auth.models import BaseUserManager, AbstractUser
from shortuuidfield import ShortUUIDField

class UserManager(BaseUserManager): #自定义Manager管理器
    def _create_user(self,username,password,email,**kwargs):
        if not username:
            raise ValueError("请传入用户名！")
        if not password:
            raise ValueError("请传入密码！")
        if not email:
            raise ValueError("请传入邮箱地址！")
        user = self.model(username=username,email=email,**kwargs)
        user.set_password(password)
        user.save()
        return user

    def create_user(self,username,password,email,**kwargs): # 创建普通用户
        kwargs['is_superuser'] = False
        return self._create_user(username,password,email,**kwargs)

    def create_superuser(self,username,password,email,**kwargs): # 创建超级用户
        kwargs['is_superuser'] = True
        kwargs['is_staff'] = True
        return self._create_user(username,password,email,**kwargs)

class User(AbstractUser): # 自定义User
    GENDER_TYPE = (
        ("1","男"),
        ("2","女")
    )
    uid = ShortUUIDField(primary_key=True)
    username = models.CharField(max_length=15,verbose_name="用户名",unique=True)
    nickname = models.CharField(max_length=13,verbose_name="昵称",null=True,blank=True)
    age = models.IntegerField(verbose_name="年龄",null=True,blank=True)
    gender = models.CharField(max_length=2,choices=GENDER_TYPE,verbose_name="性别",null=True,blank=True)
    phone = models.CharField(max_length=11,null=True,blank=True,verbose_name="手机号码")
    email = models.EmailField(verbose_name="邮箱")
    picture = models.ImageField(upload_to="Store/user_picture",verbose_name="用户头像",null=True,blank=True)
    home_address = models.CharField(max_length=100,null=True,blank=True,verbose_name="地址")
    card_id = models.CharField(max_length=30,verbose_name="身份证",null=True,blank=True)
    is_active = models.BooleanField(default=True,verbose_name="激活状态")
    is_staff = models.BooleanField(default=True,verbose_name="是否是员工")
    date_joined = models.DateTimeField(auto_now_add=True)

    USERNAME_FIELD = 'username' # 使用authenticate验证时使用的验证字段，可以换成其他字段，但验证字段必须是唯一的，即设置了unique=True
    REQUIRED_FIELDS = ['email'] # 创建用户时必须填写的字段，除了该列表里的字段还包括password字段以及USERNAME_FIELD中的字段
    EMAIL_FIELD = 'email' # 发送邮件时使用的字段

    objects = UserManager()

    def get_full_name(self):
        return self.username

    def get_short_name(self):
        return self.username

    class Meta:
        verbose_name = "用户"
        verbose_name_plural = verbose_name
```

In settings.py, 

```python
AUTH_USER_MODEL = "appname.User"
```

Run the following command, create UserProfile database:

```
python manage.py makemigrations` 
python manage.py migrate
```

In appname/forms.py: create RegistrationForm and LoginForm:

```python
from django import forms
from django.contrib.auth.models import User
import re

def email_check(email):
    pattern = re.compile(r"\"?([-a-zA-Z0-9.`?{}]+@\w+\.\w+)\"?")
    return re.match(pattern, email)

class RegistrationForm(forms.Form):
    
    username = forms.CharField(label='Username', max_length=50)
    email = forms.EmailField(label='Email')
    passowrd1 = forms.CharField(label='Password', widget=forms.PasswordInput)
    password2 = forms.CharField(label='Password Confirmation', widget=forms.PassowrdInput)
    
    # Use clean methods to define custom validation rules
    
    def clean_username(self):
        username = self.cleaned_data.get('username')
        
        if len(username) < 6:
            raise forms.ValidationError("Your username must be at least 6 characters long.")
        elif len(username) > 50:
            raise forms.ValidationError("Your username is too long.")
        else:
            filter_result = User.objects.filter(username__exact=username)
            if len(filter_result) > 0:
                raise forms.ValidationError("Your username already exists.")
 
        return username
     
    def clean_email(self):
        email = self.cleaned_data.get('email')
 
        if email_check(email):
            filter_result = User.objects.filter(email__exact=email)
            if len(filter_result) > 0:
                raise forms.ValidationError("Your email already exists.")
        else:
            raise forms.ValidationError("Please enter a valid email.")
 
        return email
 
    # form has the cleaned_data method
    def clean_password1(self):
        password1 = self.cleaned_data.get('password1')
        
        if len(password1) < 6:
            raise forms.ValidationError("Your password is too short.")
        elif len(password1) > 20:
            raise forms.ValidationError("Your password is too long.")
 
        return password1
 
    def clean_password2(self):
        password1 = self.cleaned_data.get('password1')
        password2 = self.cleaned_data.get('password2')
 
        if password1 and password2 and password1 != password2:
            raise forms.ValidationError("Password mismatch. Please enter again.")
 
        return password2

class LoginForm(forms.Form):
    
    username = forms.CharField(label='Username', max_length=50)
    password = forms.CharField(label='Password', widget=forms.PasswordInput)
    
    # Use clean methods to define custom validation rules
    
    def clean_username(self):
        username = self.cleaned_data.get('username')
 
        if email_check(username):
            filter_result = User.objects.filter(email__exact=username)
            if not filter_result:
                raise forms.ValidationError("This email does not exist.")
        else:
            filter_result = User.objects.filter(username__exact=username)
            if not filter_result:
                        raise forms.ValidationError("This username does not exist. Please register first.")
 
        return username
```

In appname/views.py:

@login_required(login_url='/accounts/login/')

```python
from django.shortcuts import render, get_object_or_404
from django.contrib.auth.models import User
from .models import UserProfile
from django.contrib import auth
from .forms import RegistrationForm, LoginForm, ProfileForm, PwdChangeForm
from django.http import HttpResponseRedirect
from django.urls import reverse
from django.contrib.auth.decorators import login_required
 
 
def register(request):
    if request.method == 'POST':
 
        form = RegistrationForm(request.POST)
        if form.is_valid():
            username = form.cleaned_data['username']
            email = form.cleaned_data['email']
            password = form.cleaned_data['password2']
 
            # 使用内置User自带create_user方法创建用户，不需要使用save()
              user = User.objects.create_user(username=username, password=password, email=email)
 
            # 如果直接使用objects.create()方法后不需要使用save()
              user_profile = UserProfile(user=user)
              user_profile.save()
 
            return HttpResponseRedirect("/accounts/login/")
# return JsonResponse({"code": 200,"message":"验证通过", "data":{"username": "","password1":"","password2":"", "email": ""}})
# return JsonResponse({"code":400,"message":"验证失败","data":{"username":form.errors.get("username"),"password1":form.errors.get("password1"),"password2":form.errors.get("password2"),"email":form.errors.get("email")}})

    else:
        # 返回空表
        form = RegistrationForm()
 
    return render(request, 'users/registration.html', {'form': form})
 
 
def login(request):
    if request.method == 'POST':
        form = LoginForm(request.POST)
        if form.is_valid():
           username = form.cleaned_data['username']
           password = form.cleaned_data['password']
 
           user = auth.authenticate(username=username, password=password)
 
           if user is not None and user.is_active:
           #使用自带的login函数进行登录，会自动添加session信息
              auth.login(request, user)
              request.session["username"] = username # 自定义session，login函数添加的session不满足时可以增加自定义的session信息。
                if remember:
                    request.session.set_expiry(None) # 设置session过期时间，None表示使用系统默认的过期时间 
                else:
                    request.session.set_expiry(0) # 0代表关闭浏览器session失效  
              return HttpResponseRedirect(reverse('users:profile', args=[user.id]))
           else:
                # 登陆失败
                return render(request, 'users/login.html', {'form': form,
                               'message': 'Wrong password. Please try again.'})
    else:
        form = LoginForm()
 
    return render(request, 'users/login.html', {'form': form})

def logoutView(request):
    logout(request) # 调用django自带退出功能，会帮助我们删除相关session
    return redirect(request.META["HTTP_REFERER"]) 

@login_required
def my_view(request):
    
```

In /templates/appname/ directory, create registration.html and login.html:

(registration.html):

```html
{% block content %}
<div class="form-wrapper">
   <form method="post" action="" enctype="multipart/form-data">
      {% csrf_token %}
      {% for field in form %}
           <div class="fieldWrapper">
        {{ field.errors }}
        {{ field.label_tag }} {{ field }}
        {% if field.help_text %}
             <p class="help">{{ field.help_text|safe }}</p>
        {% endif %}
           </div>
        {% endfor %}
      <div class="button-wrapper submit">
         <input type="submit" value="Submit" />
      </div>
   </form>
</div>
{% endblock %}
```

(login.html):

```
{% block content %}
<h2>Login</h2>
{% if message %}
 {{ message }}
{% endif %}
<div class="form-wrapper">
   <form method="post" action="" enctype="multipart/form-data">
      {% csrf_token %}
      {% for field in form %}
           <div class="fieldWrapper">
        {{ field.errors }}
        {{ field.label_tag }} {{ field }}
        {% if field.help_text %}
             <p class="help">{{ field.help_text|safe }}</p>
        {% endif %}
           </div>
        {% endfor %}
      <div class="button-wrapper submit">
         <input type="submit" value="Login" />
      </div>
   </form>
    <a href="/accounts/register">Register</a>
</div>
{% endblock %}
```

20. ## **Permissions:**

permission model has id, name, content_type_id, codename field.

When `django.contrib.auth` is listed in your [`INSTALLED_APPS`](https://docs.djangoproject.com/en/3.1/ref/settings/#std:setting-INSTALLED_APPS) setting, it will ensure that four default permissions – add, change, delete, and view – are created for each Django model defined in one of your installed applications.

name presents the function of the permission,

content_type_id presents the id of the model which has the permission to **add, change, delete, view** it,

codename presents the name of the permission. **add_model, change_model, delete_model, view_model.**

The method of adding permissions:

In class Meta of model, add the permission; 

```python
from django.db import models
#通过定义模型来添加权限：
class Address(models.Model):
    """
    收货地址
    """
    recv_address = models.TextField(verbose_name = "收货地址")
    receiver =  models.CharField(max_length=32,verbose_name="接收人")
    recv_phone = models.CharField(max_length=32,verbose_name="收件人电话")
    post_number = models.CharField(max_length=32,verbose_name="邮编")
    buyer_id = models.ForeignKey(to=User,on_delete = models.CASCADE,verbose_name = "用户id")
    class Meta:
        permissions = [("view_addresses", "查看地址")]        
# 通过代码添加权限
from django.contrib.auth.models import Permission,ContentType
from .models import Address
#
content_type = ContentType.objects.get_for_model(Address)
permission = Permission.objects.create(name = "查看地址",codename = "view_addresses",content_type = content_type)
permission1 = Permission.objects.crate(name = '改变地址',codename = 
"change_addresses",content_type = content_type)
```

You can also use the following statements to add permissions in User model:

```python
user.user_permissions.set(permission_list) #直接给定一个权限的列表
user.user_permissions.add(permission,permission,...) #一个个添加权限
user.user_permission.remover(permission,permission) #一个个删除权限
user.user_permission.clear() #清除权限
user.has_perm('<app_name>.<codename>') #判断是否拥有某个权限
user.get_all_permission() #获得所有权限
user.get_group_permission #获得所在组权限
```

For authentication in views, you can use user.has_perm('<app_name>.<codename>') method to validate the permission.

You can also use the @permission_required(*perm*ission, *login_url=None*, *raise_exception=False*) decorator.

```python
from django.contrib.auth.decorators import permission_required
 
@permission_required('appname.codename')
def my_view(request):
    ...
```

To apply permission checks to [class-based views](https://docs.djangoproject.com/en/3.1/ref/class-based-views/), you can use the `PermissionRequiredMixin`:

This mixin, just like the `permission_required` decorator, checks whether the user accessing a view has all given permissions.

```python
from django.contrib.auth.mixins import PermissionRequiredMixin

class MyView(PermissionRequiredMixin, View):
    permission_required = 'polls.add_choice'
    # Or multiple of permissions:
    permission_required = ('polls.view_choice', 'polls.change_choice')
```

In **templates**, to check if the logged-in user has any permissions in the foo app:

```html
{% if perms.foo %}
```

```html
{% if perms.foo %}
    <p>You have permission to do something in the foo app.</p>
    {% if perms.foo.add_vote %}
        <p>You can vote!</p>
    {% endif %}
    {% if perms.foo.add_driving %}
        <p>You can drive!</p>
    {% endif %}
{% else %}
    <p>You don't have permission to do anything in the foo app.</p>
{% endif %}
```

Group can classify the User by permissions:

```
Group.objects.create(group_name) # 创建分组
group.permission.add #添加权限
group.permission.remove #移除权限
group.permisiion.clear #清除权限
user.get_group_permission() #获取用户所属组的权限
```

21. ## Group:

see 19. **User Model for authentication**

22. ## Manager:

A Manager is the **interface through which database query operations are provided to Django models.**

```python
# First, define the Manager subclass.
class DahlBookManager(models.Manager):
    def get_queryset(self):
        return super().get_queryset().filter(author='Roald Dahl')

# Then hook it into the Book model explicitly.
class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.CharField(max_length=50)

    objects = models.Manager() # The default manager.
    dahl_objects = DahlBookManager() # The Dahl-specific manager.
```

The statement Book.objects.all() will return all books in the database.

Because `get_queryset()` returns a `QuerySet` object, you can use `filter()`, `exclude()` and all the other `QuerySet` methods on it

```
Book.dahl_objects.all()
Book.dahl_objects.filter(title='Matilda')
Book.dahl_objects.count()
```

23. ## Templates

Loading templates in this order:

base_dir/projectName/templates/x.html

base_dir/projectName/appName1/templates/x.html

base_dit/projectName/appName2/templates/x.html

{{ variable }}:  It evaluates that variable and replaces it with the result.

".": it tries the following lookups, in this order: dictionary lookup, attribute or method lookup, numeric index lookup.

{{ name|lower}}: This displays the value of the {{ name }} variable after being filtered through the lower filter, which converts text to lowercase. Use a pipe (|) to apply a filter. Filter can be chained.

{{% tag %}}: 

for:

```html
<ul>
{% for athlete in athlete_list %}
    <li>{{ athlete.name }}</li>
{% endfor %}
</ul>
```

if, elif and else:

```html
{% if athlete_list %}
    Number of athletes: {{ athlete_list|length }}
{% elif athlete_in_locker_room_list %}
    Athletes should be out of the locker room soon!
{% else %}
    No athletes.
{% endif %}
```

{% block %}: defines blocks that child templates can override.

{% block block_name %} content {% endblock %}

```html
<!DOCTYPE html base.html>
<html lang="en">
<head>
    <link rel="stylesheet" href="style.css">
    <title>{% block title %}My amazing site{% endblock %}</title>
</head>

<body>
    <div id="sidebar">
        {% block sidebar %}
        <ul>
            <li><a href="/">Home</a></li>
            <li><a href="/blog/">Blog</a></li>
        </ul>
        {% endblock %}
    </div>

    <div id="content">
        {% block content %}{% endblock %}
    </div>
</body>
</html>
```

```html
{% extends "base.html" %}

{% block title %}My amazing blog{% endblock %}

{% block content %}
{% for entry in blog_entries %}
    <h2>{{ entry.title }}</h2>
    <p>{{ entry.body }}</p>
{% endfor %}
{% endblock %}
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <link rel="stylesheet" href="style.css">
    <title>My amazing blog</title>
</head>

<body>
    <div id="sidebar">
        <ul>
            <li><a href="/">Home</a></li>
            <li><a href="/blog/">Blog</a></li>
        </ul>
    </div>

    <div id="content">
        <h2>Entry one</h2>
        <p>This is my first entry.</p>

        <h2>Entry two</h2>
        <p>This is my second entry.</p>
    </div>
</body>
</html>
```










