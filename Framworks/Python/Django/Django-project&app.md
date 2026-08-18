terminology

This is an important distinction.

1. FlowForge

This is your overall application/product.

FlowForge
├── frontend
└── backend

It is not necessarily a Django concept.

> 2. Django project

### A Django project is the configuration/container for a Django application.

It contains things such as:

settings
URL configuration
ASGI/WSGI configuration

For FlowForge, we'll have one Django project inside backend.

> 3. Django app

### A Django app is a functional part of your application.

For example, FlowForge might eventually have:

accounts
tenants
projects
tasks
billing
notifications

These are Django apps.

Conceptually:

FlowForge
    │
    └── Django project
          │
          ├── accounts app
          ├── tenants app
          ├── projects app
          ├── tasks app
          └── billing app

Don't create all of these yet. We'll design them properly as we understand the requirements.


# Create the Django project

> django-admin startproject config .

**Pay attention to the . at the end.**

That dot means:

Create the Django project in the current directory rather than creating another nested config directory.


django-admin startproject config .
python manage.py migrate
python manage.py runserver
python manage.py startapp core  -> create a small app called core.

python manage.py showmigrations  //SHOW ALL MIGRATIONS

python manage.py check


python manage.py sqlmigrate accounts 0001  //MODEL NAME AND NUMBER
