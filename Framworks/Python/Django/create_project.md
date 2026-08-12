Create the virtual environment

You are currently here:

~/sakshi-dev/pythonProjects/SaaS-project/FlowForge/backend

**Run:**

> python3 -m venv .venv

It creates an isolated Python environment inside

### Why do we need this?

Suppose later you have:

Project A → Django 5.x
Project B → Django 6.x

You don't want their dependencies interfering with each other.

# **Then activate it:**

> source .venv/bin/activate

After activation, your prompt should look approximately like:

> (.venv) developer@developer:~/sakshi-dev/pythonProjects/SaaS-project/FlowForge/backend$

### Why .venv belongs here

We want the dependency environment to belong to this backend project:

backend/
├── .venv/
└── ...

Later, we'll have something like:

FlowForge/
├── backend/
│   ├── .venv/
│   ├── manage.py
│   ├── config/
│   └── apps/
│
└── frontend/

But don't create the Django project structure manually. Django will generate it for us.


# What exactly does this command do?

> python3 -m venv .venv

**python3**

This tells your computer:

"Use Python 3."

**-m venv**

This tells Python:

> "Run Python's built-in venv module."

venv is the tool that creates virtual environments.

**.venv**

This is the name of the directory where the environment will be created.

So:

python3 -m venv .venv

basically means:

> **"Python, create a virtual environment called .venv in this directory."**


then 
# Install Django

Run:

> python -m pip install django

Notice I'm using:

python -m pip

rather than simply: pip

**This makes it explicit that we're using the pip associated with the currently active Python environment.**

After installation, verify it:

> python -m django --version


After running it:

ls

You should see something approximately like:

backend/
├── .venv/
├── manage.py
└── config/
    ├── __init__.py
    ├── asgi.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py

## Why are we calling it config?

Because this directory will primarily contain Django's project configuration.

Later:

config/
├── settings.py
├── urls.py
├── asgi.py
└── wsgi.py

Your actual business functionality will live in Django apps.


# 🧠 Understand what Django created

Before running anything, let's understand these two things:

manage.py
config/

# 1. manage.py

manage.py is a command-line utility for interacting with your Django project.

You'll use it constantly.

For example:

> python manage.py runserver

starts the development server.

Later:

python manage.py startapp accounts
python manage.py makemigrations
python manage.py migrate
python manage.py createsuperuser
python manage.py shell

So think of it as:

manage.py
    ↓
Your interface for running Django management commands
    ↓
Django project

It is basically a convenient project-specific wrapper around Django's management system.

# 2. config/

This is your Django project configuration package.

Let's inspect it.

Run:

ls config

You should see something similar to:

__init__.py
asgi.py
settings.py
urls.py
wsgi.py

Each file has a different responsibility.

## settings.py

Contains project configuration:

Database
Installed apps
Middleware
Templates
Static files
Security settings
Internationalization
etc.

We'll spend a lot of time here later.

urls.py

Contains your project's URL configuration.

Conceptually:

/products/  → product view
/users/     → user view
/orders/    → order view

We'll learn URL routing soon.

asgi.py

Entry point for running Django using ASGI.

This becomes important when we discuss:

asynchronous Django
WebSockets
production deployment
ASGI servers

Don't worry about it yet.

wsgi.py

Entry point for running Django using WSGI.

You'll encounter it when learning traditional Django deployment.

Again, don't worry about it yet.

## __init__.py

This tells Python that config is a Python package.

Because you already know Python, this part should be familiar.

1. manage.py
    it sets the django project config settings from config.settings and interacts with os and sys. it execute the Django application from the command line

> Think of it as a project-specific command-line entry point.

manage.py is a command-line utility that sets up the Django project environment and passes commands to Django's management system.

2. settings.py — 

You correctly identified several important categories:

Database
Middleware
Applications
Authentication
Password validation
URLs

3. INSTALLED_APPS contains the Django applications that are enabled for your project.

Some of them are built into Django:

INSTALLED_APPS = [
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
]

But your own apps also go here.

For example, later FlowForge might have:

INSTALLED_APPS = [
    # Django apps
    "django.contrib.admin",
    "django.contrib.auth",
    ...

    # FlowForge apps
    "accounts",
    "tenants",
    "projects",
    "tasks",
]

# 🧩 Why does Django need INSTALLED_APPS?

This is a very important question.

Suppose we create:

accounts
projects
tasks

> Django needs to know: "These applications are part of this Django project."

That's one purpose of INSTALLED_APPS.


source .venv/bin/activate
django-admin startproject config .
python manage.py migrate
python manage.py runserver
python manage.py startapp core