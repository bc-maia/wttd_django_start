# Django

---

## Activating Python venv & Install Django

Virtual Environments [Docs](https://docs.python.org/3/library/venv.html)

```console
python -m venv .env_folder

source .env_folder/bin/activate

pip install django
```

## Creating new Django Project

[Django](https://docs.djangoproject.com/) Docs

```console
django-admin startproject mysite .  
```

**Caution**: the . above isn't required, but if already inside the root dir, do not skip it, or a nested folder will be created.

The [**django manage**](https://docs.djangoproject.com/en/2.2/ref/django-admin/) script located in the project root can be added as an alias to be reused anytime

```console
source .env_folder/bin/activate

# the line bellow shows the venv path
echo $VIRTUAL_ENV

# to create a venv manager alias
alias manage="python $VIRTUAL_ENV/../manage.py"

```

## First Start

Run the [Dev Server](https://docs.djangoproject.com/en/2.2/intro/tutorial01/#the-development-server)

```console
# ip and port not required if access happens from the same working machine
manage runserver 0.0.0.0:8000
```

---

## CREATING FIRST DJANGO APP

Projects and Applications [Docs](https://docs.djangoproject.com/en/2.2/ref/applications/#projects-and-applications)

### Creating first App

```console
# inside project folder
manage startapp core
```

As result:

```console
> tree ..
..
├── db.sqlite3
├── hellosite
│   ├── core
│   │   ├── admin.py
│   │   ├── apps.py
│   │   ├── __init__.py
│   │   ├── migrations
│   │   │   └── __init__.py
│   │   ├── models.py
│   │   ├── tests.py
│   │   └── views.py
│   ├── __init__.py
│   ├── __pycache__
│   │   ├── __init__.cpython-37.pyc
│   │   ├── settings.cpython-37.pyc
│   │   ├── urls.cpython-37.pyc
│   │   └── wsgi.cpython-37.pyc
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── manage.py
```

## Add App into main project

Append into 'INSTALLED_APPS' in settings.py

```python
# ...
INSTALLED_APPS = [
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
    "hellosite.core",  # <core app here>
]
# ...
```

## Create a route

Add into 'urls.py' a view route pattern

```python
# ...
import hellosite.core.views as views
# ...
urlpatterns = [
    path("", views.home),
    path("admin/", admin.site.urls)]
]
# ...
```

## Create a view render

Also, include the view call inside the app views
Template renderer [docs](https://docs.djangoproject.com/en/2.2/ref/forms/renderers/#built-in-template-form-renderers)

```python
# ...
def home(request):
    return render(request, "index.html")
# ...
```

## Make the view file

Create an index.html file inside a templates folder
with some _Lorem Ipsum_ content.

Structure will be like this:

```console
❯ tree ..
..
├── db.sqlite3
├── hellosite
│   ├── core
│   │   ├── admin.py
│   │   ├── apps.py
│   │   ├── __init__.py
│   │   ├── migrations
│   │   │   ├── __init__.py
│   │   │   └── __pycache__
│   │   │       └── __init__.cpython-37.pyc
│   │   ├── models.py
│   │   ├── __pycache__
│   │   │   ├── admin.cpython-37.pyc
│   │   │   ├── __init__.cpython-37.pyc
│   │   │   ├── models.cpython-37.pyc
│   │   │   └── views.cpython-37.pyc
│   │   ├── templates
│   │   │   └── index.html
│   │   ├── tests.py
│   │   └── views.py
│   ├── __init__.py
│   ├── __pycache__
│   │   ├── __init__.cpython-37.pyc
│   │   ├── settings.cpython-37.pyc
│   │   ├── urls.cpython-37.pyc
│   │   └── wsgi.cpython-37.pyc
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── manage.py
```

## Update using custom made template

If using other people custom templates, you can add it to your project apps

* Create a custom **static** folder into your app directory and add the files

```console
> tree hellosite/core

hellosite/core
├── admin.py
├── ...
├── static
│   ├── css
│   │   ├── ...
│   ├── fonts
│   │   ├── ...
│   ├── img
│   │   ├── ...
│   └── js
│       ├── ...
├── templates
│   └── index.html
└── ...
```

## [Managing static files](https://docs.djangoproject.com/en/2.2/howto/static-files/)

* Load the template tag

```jinja
{% load static %}
<!DOCTYPE html>
<html>
...
```

* use the static template tag to build the URL. E.g.:

```html
...
<link rel="stylesheet" href="{% static 'css/main.css' %}">
...
 <script src="{% static 'js/main.js' %}"></script>
...
<img class="img-responsive" src="{% static 'img/sponsor-silver-01.png' %}" alt="logo">
...
```

HINT = Using an editor, [ReGex](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions) can be usefull to replace all old plain html to [jinja](https://palletsprojects.com/p/jinja/)-like template

```py
(src|href)="((img|js|css).*?)"
```

into:

```jinja
$1="{% static '$2' %}"
```

---

## _Release the Kraken_ [using [Heroku](https://dashboard.heroku.com/apps)]

Install Heroku CLI following the [instructions](https://devcenter.heroku.com/articles/heroku-cli#download-and-install).

..and add [Python-Decouple](https://github.com/henriquebastos/python-decouple/) dependency.

```console
> pip install python-decouple
```

## Creating the .env variables document and extract them from current code

edit __settings__ file and update environment variables like example:

```py
...
# SECURITY WARNING: keep the secret key used in production secret!
SECRET_KEY="0ld_$ecr3t_K3y_V@lu3"  
...
DEBUG = True  # <- remove this line
...
```

```py
...
SECRET_KEY = config('SECRET_KEY')
...
DEBUG = config("DEBUG", default=False, cast=bool)
...
```

and add these informations into a **.env** file (remember to remove quotes):

```content
SECRET_KEY=0ld_$ecr3t_K3y_V@lu3
DEBUG=True
```

Also, add [dj-database-url](https://github.com/jacobian/dj-database-url) dependency, so _Database_ can be decoupled from code either.

```console
> pip install dj-database-url
```

...then, edit from:

```py
DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.sqlite3",
        "NAME": os.path.join(BASE_DIR, "db.sqlite3"),
    }
}
```

Into:

```py
default_dburl = f"sqlite:///{os.path.join(BASE_DIR, 'db.sqlite3')}"

DATABASES = {
    "default": config("DATABASE_URL", default=default_dburl, cast=dburl),
}
```

To Django respond requests inside Heroku, we need to allow into host:

```py
ALLOWED_HOSTS = ["*"]
```

Finally, configuration for serving python static content in heroku:

* Add the [dj-static](https://github.com/heroku-python/dj-static) dependency

```console
> pip install dj-static
```

* Update the settings file:

```py
...
STATIC_URL = "/static/"
STATIC_ROOT = os.path.join(BASE_DIR, "staticfiles")
```

* update the wsgi file

```py
...
from dj_static import Cling
...
application = Cling(get_wsgi_application())
...
```

* Make a file _requirements.txt_ and append python packages.

```console
> pip freeze > requirements.txt
```

Also, include two production only packages:

[gunicorn](http://gunicorn.org/) and [psycopg2](http://initd.org/psycopg/)

---

## Deploying Site into Heroku

[App Creating](https://devcenter.heroku.com/articles/creating-apps#creating-a-named-app) and [Build packs](https://devcenter.heroku.com/articles/buildpacks#setting-a-buildpack-on-an-application) Documentations

* Check for a folder (**.git/**). It means already repository was configured, otherwise, init it.

```console
> git init
> git add .
> git commit -m 'first commit'
```

Then:

```console
> heroku apps:create hello-events-app --buildpack heroku/python
```

* check if app is running

```console
> heroku open -a hello-events-app
```

* set config variables into heroku workspace

```console
> heroku config:set -a hello-events-app SECRET_KEY='0ld_$ecr3t_K3y_V@lu3'
> heroku config:set -a hello-events-app DEBUG=True
```

* Add heroku to your git remote

```console
> heroku git:remote -a hello-events-app
```

* Concluding, Upload the content to heroku

```console
> git push heroku master --force
```
