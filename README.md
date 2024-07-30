# Part 1: Getting Started with Django Tweet Project

## Prerequisites

Before we begin, ensure you have the following installed on your system:

1. **Python**: Python 3.6 or later.
2. **Django**: The web framework we will use to build our project.
3. **Virtual Environment**: To manage dependencies for your project.

## Introduction

Hello! I'm Dhruv, and in this blog series, I'll be documenting the process of creating a Tweet Project using Django. This project aims to simulate a simplified version of a Twitter-like application where users can post tweets, view them, and interact with them. In Part 1, we will set up our development environment, create our Django project, and set up a basic structure.

## Step-by-Step Guide

### 1. Create Project Directory

First, let's create a directory for our project. Open your terminal and navigate to the path where you want to create your project folder, then execute:

```sh
mkdir tweet-demo-project
cd tweet-demo-project
```

### 2. Set Up Virtual Environment

Next, we'll set up a virtual environment to manage our project's dependencies. Run the following commands:

```sh
python -m venv .venv
.venv\Scripts\activate  # On Windows
source .venv/bin/activate  # On macOS/Linux
```

### 3. Install Django

With the virtual environment activated, install Django and upgrade `pip`:

```sh
pip install django
python.exe -m pip install --upgrade pip
```

To keep track of our project dependencies, create a `requirements.txt` file:

```sh
pip freeze > requirements.txt
```

### 4. Create Django Project

Now, we'll create our Django project. Run:

```sh
django-admin startproject tweetdemo
cd tweetdemo
python manage.py runserver
```

You should see an output indicating the server is running. Open your browser and navigate to `http://127.0.0.1:8000/` to see the default Django welcome page.

### 5. Make Migrations and Apply Migrations

Before applying the initial migrations, we need to create them. This sets up the database schema according to the models defined in Django's default apps. Run:

```sh
python manage.py makemigrations
```

Now, apply the migrations to set up the database:

```sh
python manage.py migrate
```

### 6. Create Superuser

Create a superuser to access the Django admin interface:

```sh
python manage.py createsuperuser
```

Enter the required information (username, email, password) when prompted.

### 7. Configure Static and Media Files

Open `settings.py` and add the following configurations to handle static and media files:

```python
import os

STATIC_URL = '/static/'
STATICFILES_DIRS = [os.path.join(BASE_DIR, 'static')]

MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

Here’s a breakdown:
- **STATIC_URL**: This is the URL prefix for serving static files (CSS, JavaScript, images).
- **STATICFILES_DIRS**: This tells Django where to look for additional static files.
- **MEDIA_URL**: This is the URL prefix for serving user-uploaded files.
- **MEDIA_ROOT**: This is the file system path to the directory that will hold user-uploaded files.

Create the necessary directories:

```sh
mkdir static
mkdir media
```

### 8. Update URLs for Media Files

In `urls.py`, add the following imports and configurations to serve media files during development:

```python
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path('admin/', admin.site.urls),
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

Here’s a breakdown:
- **settings**: Importing settings allows us to access the `MEDIA_URL` and `MEDIA_ROOT` configurations.
- **static**: The `static` function is used to serve static and media files during development.

### 9. Create Tweet App

Create a new Django app named `tweet`:

```sh
python manage.py startapp tweet
```

### 10. Register Tweet App

Tell our `tweetdemo` project about the `tweet` app. In `tweetdemo/settings.py`, add `'tweet',` to the `INSTALLED_APPS` list:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'tweet',
]
```

The `INSTALLED_APPS` list contains all Django applications that are activated in this Django instance. Adding `'tweet'` allows Django to include the configurations and models of the `tweet` app.

Also, update the `TEMPLATES` setting to include a `templates` directory:

```python
'DIRS': [os.path.join(BASE_DIR, 'templates')],
```

Here’s a breakdown:
- **DIRS**: This is a list of directories where the `DjangoTemplates` backend will search for templates.

### 11. Link Tweet App URLs

In `tweetdemo/urls.py`, include the `tweet` app URLs:

```python
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('tweet/', include('tweet.urls')),
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

Here’s a breakdown:
- **include**: The `include` function allows referencing other URL configurations. This is used to include the `tweet` app's URL patterns in the main URL configuration.

### 12. Create a View in Tweet App

In `tweet/views.py`, create a simple index view:

```python
from django.shortcuts import render

def index(request):
    return render(request, 'index.html')
```

### 13. Set Up Tweet App URLs

Create a `urls.py` file in the `tweet` app and link the index view:

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```

### 14. Create Template for Tweet App

Create a `templates` folder within the `tweet` app and add an `index.html` file:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Tweet Project</title>
    <style>
        body {
            background-color: #000;
            color: #FFF;
        }
    </style>
</head>
<body>
    <h1>TWEET INDEX TEMPLATE</h1>
</body>
</html>
```

### 15. Test the Setup

Run the development server:

```sh
python manage.py runserver
```

Visit `http://127.0.0.1:8000/tweet/` in your browser to see the index template.

---

This concludes Part 1 of our Django Tweet Project. We have set up the project, created a basic structure, and ensured everything is running smoothly. Stay tuned for Part 2, where we will dive deeper into building out the Tweet functionality.




# Part 2: Building the Tweet Functionality with Django

In Part 1, we set up the Django project, created a basic structure, and ensured everything was running smoothly. In this part, we'll enhance the project by integrating Bootstrap for a responsive layout, creating a Tweet model, and implementing basic CRUD (Create, Read, Update, Delete) functionality.

## Step-by-Step Guide

### 1. Integrate Bootstrap

First, let's integrate Bootstrap to style our application. We will create a `layout.html` file which will serve as the base template for our app.

#### Create `layout.html`

Create a new file named `layout.html` in the `templates` directory of the `tweet` app and add the following code:

```html
{% load static %}

<!DOCTYPE html>
<html lang="en" data-bs-theme="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>
        {% block title %}
        TWEET APP LAYOUT
        {% endblock %}
    </title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">
</head>
<body>
    <nav class="navbar navbar-expand-lg bg-body-tertiary">
        <div class="container-fluid">
          <a class="navbar-brand" href="#">Tweet Pro</a>
          <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
          </button>
          <div class="collapse navbar-collapse" id="navbarSupportedContent">
            <ul class="navbar-nav me-auto mb-2 mb-lg-0">
              <li class="nav-item">
                <a class="nav-link active" aria-current="page" href="#">Home</a>
              </li>
              <li class="nav-item">
                <a class="nav-link" href="#">Link</a>
              </li>
              <li class="nav-item dropdown">
                <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                  Dropdown
                </a>
                <ul class="dropdown-menu">
                  <li><a class="dropdown-item" href="#">Action</a></li>
                  <li><a class="dropdown-item" href="#">Another action</a></li>
                  <li><hr class="dropdown-divider"></li>
                  <li><a class="dropdown-item" href="#">Something else here</a></li>
                </ul>
              </li>
              <li class="nav-item">
                <a class="nav-link disabled" aria-disabled="true">Disabled</a>
              </li>
            </ul>
            <form class="d-flex" role="search">
              <input class="form-control me-2" type="search" placeholder="Search" aria-label="Search">
              <button class="btn btn-outline-success" type="submit">Search</button>
            </form>
          </div>
        </div>
      </nav>
    <div class="container">
        {% block content %}
        <!-- Your page content goes here -->
        {% endblock %}
    </div>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-9ndCyUaJ6BsTAPGe7GqA4G5kBRFOfJ5in9EQOVecIRBbgP7d1Ns9H3cnPTzpgI7q" crossorigin="anonymous"></script>
</body>
</html>
```

### 2. Update Index Template

Modify the `index.html` file in the `templates/tweet` directory to extend `layout.html`:

```html
{% extends "layout.html" %}

{% block title %}
TWEET APP INDEX
{% endblock %}

{% block content %}
<h1 class="text-center mt-4">Welcome to the Tweet App!</h1>
{% endblock %}
```

### 3. Create Tweet Model

Next, we will create a `Tweet` model to store the tweets. Open `models.py` in the `tweet` app and add the following code:

```python
from django.db import models
from django.contrib.auth.models import User

class Tweet(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    text = models.TextField(max_length=280)
    photo = models.ImageField(upload_to='photos/', blank=True, null=True)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    def __str__(self):
        return f'{self.user.username} - {self.text[:20]}...'
```

### 4. Register Tweet Model

Register the `Tweet` model in the admin interface. Open `admin.py` in the `tweet` app and add:

```python
from django.contrib import admin
from .models import Tweet

admin.site.register(Tweet)
```

### 5. Install Pillow

Django uses the Pillow library to handle image files. Install Pillow by running:

```sh
pip install Pillow
pip freeze > requirements.txt
```

### 6. Make and Apply Migrations

Create and apply the necessary migrations for the `Tweet` model:

```sh
python manage.py makemigrations tweet
python manage.py migrate
```

### 7. Create Forms

Create a `forms.py` file in the `tweet` app to handle the tweet form:

```python
from django import forms
from .models import Tweet

class TweetForm(forms.ModelForm):
    class Meta:
        model = Tweet
        fields = ['text', 'photo']
```

### 8. Update Views

In `views.py` of the `tweet` app, update the views to handle tweet creation, editing, and deletion:

```python
from django.shortcuts import render, get_object_or_404, redirect
from .models import Tweet
from .forms import TweetForm
from django.contrib.auth.decorators import login_required

def index(request):
    return render(request, 'index.html')

def tweet_list(request):
    tweets = Tweet.objects.all().order_by('-created_at')
    return render(request, 'tweet_list.html', {'tweets': tweets})

@login_required
def tweet_create(request):
    if request.method == "POST":
        form = TweetForm(request.POST, request.FILES)
        if form.is_valid():
            tweet = form.save(commit=False)
            tweet.user = request.user
            tweet.save()
            return redirect('tweet_list')
    else:
        form = TweetForm()
    return render(request, 'tweet_form.html', {'form': form})

@login_required
def tweet_edit(request, tweet_id):
    tweet = get_object_or_404(Tweet, pk=tweet_id, user=request.user)
    if request.method == "POST":
        form = TweetForm(request.POST, request.FILES, instance=tweet)
        if form.is_valid():
            tweet = form.save(commit=False)
            tweet.user = request.user
            tweet.save()
            return redirect('tweet_list')
    else:
        form = TweetForm(instance=tweet)
    return render(request, 'tweet_form.html', {'form': form})

@login_required
def tweet_delete(request, tweet_id):
    tweet = get_object_or_404(Tweet, pk=tweet_id, user=request.user)
    if request.method == "POST":
        tweet.delete()
        return redirect('tweet_list')
    return render(request, 'tweet_confirm_delete.html', {'tweet': tweet})
```

### 9. Update URL Configurations

In the `urls.py` of the `tweet` app, define the URL patterns for the views:

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='index'),
    path('list/', views.tweet_list, name='tweet_list'),
    path('create/', views.tweet_create, name='tweet_create'),
    path('edit/<int:tweet_id>/', views.tweet_edit, name='tweet_edit'),
    path('delete/<int:tweet_id>/', views.tweet_delete, name='tweet_delete'),
]
```

Also, ensure that the `tweetdemo/urls.py` includes these paths:

```python
from django.contrib import admin
from django.urls import path, include
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path('admin/', admin.site.urls),
    path('tweet/', include('tweet.urls')),
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

### 10. Create Templates for CRUD Operations

Create `tweet_list.html`, `tweet_form.html`, and `tweet_confirm_delete.html` templates in the `templates/tweet` directory.

#### `tweet_list.html`

```html
{% extends "layout.html" %}

{% block title %}
Tweet List
{% endblock %}

{% block content %}
<div class="container mt-4">
    <h1>Tweet List</h1>
    <a class="btn btn-primary" href="{% url 'tweet_create' %}">Create Tweet</a>
    <ul class="list-group mt-3">
        {% for tweet in tweets %}
        <li class="list-group-item">
            <h5>{{ tweet.user.username

 }}</h5>
            <p>{{ tweet.text }}</p>
            {% if tweet.photo %}
            <img src="{{ tweet.photo.url }}" alt="Tweet photo" class="img-fluid">
            {% endif %}
            <p class="text-muted">{{ tweet.created_at }}</p>
            <a class="btn btn-secondary" href="{% url 'tweet_edit' tweet.id %}">Edit</a>
            <a class="btn btn-danger" href="{% url 'tweet_delete' tweet.id %}">Delete</a>
        </li>
        {% endfor %}
    </ul>
</div>
{% endblock %}
```

#### `tweet_form.html`

```html
{% extends "layout.html" %}

{% block title %}
Create/Edit Tweet
{% endblock %}

{% block content %}
<div class="container mt-4">
    <h1>{% if form.instance.pk %}Edit{% else %}Create{% endif %} Tweet</h1>
    <form method="post" enctype="multipart/form-data">
        {% csrf_token %}
        {{ form.as_p }}
        <button class="btn btn-primary" type="submit">{% if form.instance.pk %}Update{% else %}Create{% endif %}</button>
    </form>
</div>
{% endblock %}
```

#### `tweet_confirm_delete.html`

```html
{% extends "layout.html" %}

{% block title %}
Confirm Delete
{% endblock %}

{% block content %}
<div class="container mt-4">
    <h1>Confirm Delete</h1>
    <p>Are you sure you want to delete this tweet?</p>
    <p><strong>{{ tweet.text }}</strong></p>
    <form method="post">
        {% csrf_token %}
        <button class="btn btn-danger" type="submit">Delete</button>
        <a class="btn btn-secondary" href="{% url 'tweet_list' %}">Cancel</a>
    </form>
</div>
{% endblock %}
```

### 11. Run the Server

Run the Django development server:

```sh
python manage.py runserver
```

Navigate to `http://127.0.0.1:8000/admin/tweet/` to see the Tweet model in the admin panel. You can also create, edit, and delete tweets from the front end using the paths defined in the URL configurations.

By following these steps, you have now integrated Bootstrap for a responsive layout, created a Tweet model, and implemented CRUD functionality in your Django application. Stay tuned for Part 3, where we will further enhance our Tweet app with more features and functionalities.


### Part 3: Adding URLs, Templates, and User Registration

In this part, we will integrate URLs for our views, create the necessary templates for our Tweet app, and set up user registration and authentication.

#### 1. Configuring URLs

First, we need to add URL patterns for our views. This will map each view to a specific URL.

In the `urls.py` file of your `tweet` app, add the following code:

```python
from django.contrib import admin
from django.urls import path
from django.conf import settings
from django.conf.urls.static import static
from . import views

urlpatterns = [
    path('index/', views.index, name='index'),
    path('', views.tweet_list, name='tweet_list'),
    path('create/', views.tweet_create, name='tweet_create'),
    path('<int:tweet_id>/edit/', views.tweet_edit, name='tweet_edit'),
    path('<int:tweet_id>/delete/', views.tweet_delete, name='tweet_delete'),
    path('register/', views.register, name='register'),
]

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

- `index/`: Maps to the `index` view.
- `/`: Maps to the `tweet_list` view, which displays the list of tweets.
- `create/`: Maps to the `tweet_create` view, for creating new tweets.
- `<int:tweet_id>/edit/`: Maps to the `tweet_edit` view, for editing existing tweets.
- `<int:tweet_id>/delete/`: Maps to the `tweet_delete` view, for deleting tweets.
- `register/`: Maps to the `register` view, for user registration.

#### 2. Creating Templates

Next, we need to create the HTML templates for displaying and managing tweets.

**`tweet_list.html`**

This template displays the list of tweets.

```html
{% extends "layout.html" %}

{% block title %}
Tweet App - Tweet List
{% endblock %}

{% block content %}
<h1 class="text-center mt-4">Welcome to the Tweet App!</h1>

<a class="btn btn-primary" href="{% url 'tweet_create' %}">Create Tweet</a>
<div class="container row gap-3">
    {% for tweet in tweets %}
    <div class="card" style="width: 18rem;">
        {% if tweet.photo %}
        <img src="{{ tweet.photo.url }}" class="card-img-top" alt="Tweet photo">
        {% endif %}
        <div class="card-body">
            <h5 class="card-title">{{ tweet.user.username }}</h5>
            <p class="card-text">{{ tweet.text }}</p>
            <a href="{% url 'tweet_edit' tweet.id %}" class="btn btn-primary">Edit</a>
            <a href="{% url 'tweet_delete' tweet.id %}" class="btn btn-danger">Delete</a>
        </div>
    </div>
    {% endfor %}
</div>
{% endblock %}
```

**`tweet_form.html`**

This template provides the form for creating and editing tweets.

```html
{% extends "layout.html" %}

{% block title %}
Tweet App - {% if form.instance.pk %}Edit{% else %}Create{% endif %} Tweet
{% endblock %}

{% block content %}
<h1 class="text-center mt-4">{% if form.instance.pk %}Edit{% else %}Create{% endif %} Tweet</h1>

<form method="post" enctype="multipart/form-data" class="form">
    {% csrf_token %}
    {{ form.as_p }}
    <button class="btn btn-warning" type="submit">Submit</button>
</form>
<a href="{% url 'tweet_list' %}">Back to Tweets</a>
{% endblock %}
```

**`tweet_confirm_delete.html`**

This template provides a confirmation form for deleting tweets.

```html
{% extends "layout.html" %}

{% block title %}
Tweet App - Confirm Delete
{% endblock %}

{% block content %}
<h1 class="text-center mt-4">Confirm Delete</h1>
<h3>Are you sure you want to delete this tweet?</h3>

<form method="post">
    {% csrf_token %}
    <button class="btn btn-danger">Delete</button>
    <a class="btn btn-success" href="{% url 'tweet_list' %}">Cancel</a>
</form>
{% endblock %}
```

#### 3. Updating Forms

We need to create and update forms for our Tweet model and user registration.

**`forms.py`**

```python
from django import forms
from .models import Tweet
from django.contrib.auth.forms import UserCreationForm
from django.contrib.auth.models import User

class TweetForm(forms.ModelForm):
    class Meta:
        model = Tweet
        fields = ['text', 'photo']

class UserRegistrationForm(UserCreationForm):
    email = forms.EmailField()
    class Meta:
        model = User
        fields = ('username', 'email', 'password1', 'password2')
```

#### 4. Creating User Registration Templates

Create a `registration` folder within your `templates` directory. Inside the `registration` folder, create `login.html`, `logged_out.html`, and `register.html`.

**`register.html`**

This template provides a form for user registration.

```html
{% extends "layout.html" %}

{% block title %}
Register
{% endblock %}

{% block content %}
<div class="container mt-4">
    <h1>Register</h1>
    <form method="post">
        {% csrf_token %}
        {{ form.as_p }}
        <button class="btn btn-primary" type="submit">Register</button>
    </form>
    <a href="{% url 'login' %}">Already have an account? Login here</a>
</div>
{% endblock %}
```

**`login.html`**

```html
{% extends "layout.html" %}

{% block title %}
Login
{% endblock %}

{% block content %}
<div class="container mt-4">
    <h1>Login</h1>
    <form method="post">
        {% csrf_token %}
        {{ form.as_p }}
        <button class="btn btn-primary" type="submit">Login</button>
    </form>
    <a href="{% url 'register' %}">Don't have an account? Register here</a>
</div>
{% endblock %}
```

**`logged_out.html`**

```html
{% extends "layout.html" %}

{% block title %}
Logged Out
{% endblock %}

{% block content %}
<div class="container mt-4">
    <h1>You have been logged out</h1>
    <a href="{% url 'login' %}">Login again</a>
</div>
{% endblock %}
```

#### 5. Adding User Registration View

In `views.py`, add the following code for user registration:

```python
from .forms import TweetForm, UserRegistrationForm
from django.contrib.auth import login
from django.shortcuts import render, redirect

def register(request):
    if request.method == "POST":
        form = UserRegistrationForm(request.POST)
        if form.is_valid():
            user = form.save(commit=False)
            user.set_password(form.cleaned_data['password1'])
            user.save()
            login(request, user)
            return redirect('tweet_list')
    else:
        form = UserRegistrationForm()
    
    return render(request, 'registration/register.html', {'form': form})
```

#### 6. Configuring Authentication URLs

In the `urls.py` file of the `tweetdemo` project folder, add the following:

```python
from django.urls import include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('tweet/', include('tweet.urls')),
    path('accounts/', include('django.contrib.auth.urls')),
]
```

This will include the default authentication views provided by Django.

#### 7. Setting Redirect URLs

In `settings.py`, add the following lines to configure login and logout redirects:

```python
LOGIN_URL = '/accounts/login'
LOGIN_REDIRECT_URL = '/tweet/'
LOGOUT_REDIRECT_URL = '/tweet/'
```

#### 8. Updating the Base Layout

Update `layout.html` to include navigation links for login, logout, and registration.

```html
{% load static %}

<!DOCTYPE html>
<html lang="en" data-bs-theme="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>
        {% block title %}
        Tweet App
        {% endblock %}
    </title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">
</head>
<body>
    <nav class="navbar navbar-expand-lg bg-body-tertiary">
        <div class="container-fluid">
            <a class="navbar-brand" href="{% url 'tweet_list' %}">Tweet Pro</a>
            <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarSupportedContent">
                <ul class="navbar-nav me-auto mb-2

 mb-lg-0">
                    <li class="nav-item">
                        <a class="nav-link active" aria-current="page" href="{% url 'tweet_list' %}">Home</a>
                    </li>
                </ul>
                <form class="d-flex me-3" role="search">
                    <input class="form-control me-3" type="search" placeholder="Search" aria-label="Search">
                    <button class="btn btn-outline-success" type="submit">Search</button>
                </form>
                {% if user.is_authenticated %}
                <form method="post" action="{% url 'logout' %}">
                    {% csrf_token %}
                    <button type="submit" class="btn btn-outline-danger">Logout</button>
                </form>
                {% else %}
                <a href="{% url 'register' %}" class="btn btn-primary mx-2">Register</a>
                <a href="{% url 'login' %}" class="btn btn-success mx-2">Login</a>
                {% endif %}
            </div>
        </div>
    </nav>
    <div class="container">
        {% block content %}
        {% endblock %}
    </div>
</body>
</html>
```

By following these steps, we've successfully set up URL routing, created the necessary templates, and configured user registration and authentication for our Tweet app. This completes Part 3 of our project.


### Part 4: Adding Authentication and Search Functionality to TweetProject

In this part, we'll focus on creating the authentication templates and enabling search functionality within the `layout.html`. This will allow users to log in, register, and search tweets or members.

#### Step 1: Create Authentication Templates

1. **login.html**

```html
{% extends 'layout.html' %}

{% block content %}
<div class="container mt-5">
    <h2>Login</h2>
    <form method="post" class="form-group">
        {% csrf_token %}
        {{ form.as_p }}
        <button class="btn btn-primary mt-3">Login</button>
    </form>
    <p class="mt-3">Don't have an account? <a href="{% url 'register' %}">Register now</a></p>
</div>
{% endblock %}
```

2. **register.html**

```html
{% extends 'layout.html' %}

{% block content %}
<div class="container mt-5">
    <h2>Register</h2>
    <form method="post" class="form-group">
        {% csrf_token %}
        {{ form.as_p }}
        <button class="btn btn-primary mt-3">Register</button>
    </form>
</div>
{% endblock %}
```

3. **logged_out.html**

```html
{% extends 'layout.html' %}

{% block content %}
<div class="container mt-5">
    <h2>Logged Out</h2>
    <p>You have been logged out successfully. <a href="{% url 'login' %}">Login again</a></p>
</div>
{% endblock %}
```

#### Step 2: Enable Search Functionality in `layout.html`

To add search functionality, we will update the `views.py` to handle search queries and modify the `layout.html` to include the search form.

1. **views.py**

Add a new view function to handle the search.

```python
from django.db.models import Q

def search(request):
    query = request.GET.get('q')
    if query:
        tweets = Tweet.objects.filter(Q(text__icontains=query) | Q(user__username__icontains=query))
    else:
        tweets = Tweet.objects.all()
    return render(request, 'search_results.html', {'tweets': tweets})
```

2. **urls.py**

Update the `urls.py` to include the search view.

```python
from django.urls import path
from . import views

urlpatterns = [
    path('index/', views.index, name='index'),
    path('', views.tweet_list, name='tweet_list'),
    path('create/', views.tweet_create, name='tweet_create'),
    path('<int:tweet_id>/edit/', views.tweet_edit, name='tweet_edit'),
    path('<int:tweet_id>/delete/', views.tweet_delete, name='tweet_delete'),
    path('register/', views.register, name='register'),
    path('search/', views.search, name='search'),
]
```

3. **layout.html**

Modify the search form to direct to the new search view.

```html
{% load static %}

<!DOCTYPE html>
<html lang="en" data-bs-theme="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}TWEET APP LAYOUT{% endblock %}</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">
</head>
<body>
    <nav class="navbar navbar-expand-lg bg-body-tertiary">
        <div class="container-fluid">
          <a class="navbar-brand" href="{% url 'tweet_list' %}">Tweet Pro</a>
          <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
          </button>
          <div class="collapse navbar-collapse" id="navbarSupportedContent">
            <ul class="navbar-nav me-auto mb-2 mb-lg-0">
              <li class="nav-item">
                <a class="nav-link active" aria-current="page" href="{% url 'tweet_list' %}">Home</a>
              </li>
            </ul>
            <form class="d-flex me-3" role="search" method="get" action="{% url 'search' %}">
              <input class="form-control me-3" type="search" placeholder="Search tweets or users" aria-label="Search" name="q">
              <button class="btn btn-outline-success" type="submit">Search</button>
            </form>
            {% if user.is_authenticated %}
            <form method="post" action="{% url 'logout' %}">
              {% csrf_token %}
              <button type="submit" class="btn btn-outline-danger">Logout</button>
            </form>
            {% else %}
            <a href="{% url 'register' %}" class="btn btn-primary mx-2">Register</a>
            <a href="{% url 'login' %}" class="btn btn-success mx-2">Login</a>
            {% endif %}
          </div>
        </div>
      </nav>
    <div class="container">
        {% block content %}{% endblock %}
    </div>
</body>
</html>
```

4. **search_results.html**

Create a new template to display search results.

```html
{% extends "layout.html" %}

{% block title %}Search Results{% endblock %}

{% block content %}
<div class="container mt-5">
    <h2>Search Results</h2>
    {% if tweets %}
        <div class="row">
            {% for tweet in tweets %}
            <div class="col-md-4">
                <div class="card mb-4">
                    {% if tweet.photo %}
                    <img src="{{ tweet.photo.url }}" class="card-img-top" alt="...">
                    {% endif %}
                    <div class="card-body">
                        <h5 class="card-title">{{ tweet.user.username }}</h5>
                        <p class="card-text">{{ tweet.text }}</p>
                        <a href="{% url 'tweet_edit' tweet.id %}" class="btn btn-primary">Edit</a>
                        <a href="{% url 'tweet_delete' tweet.id %}" class="btn btn-danger">Delete</a>
                    </div>
                </div>
            </div>
            {% endfor %}
        </div>
    {% else %}
        <p>No results found</p>
    {% endif %}
</div>
{% endblock %}
```

With these steps completed, your Django project now has fully functional user authentication, allowing users to register, log in, log out, and perform searches within the app. This will enhance user experience and make the TweetProject more interactive and user-friendly.
