# Project vs app

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


# Q. what is project and app why we use apps in Django application?

In Django, a **project** is the entire website or web application, while an **app** is a small, self-contained module that performs a single specific function.

Think of a Django project as a large house, and the individual apps as the specialized rooms (kitchen, bathroom, bedroom) inside it.

------------------------------
## What is a Django Project?
A project is the top-level configuration container for your entire web application. It ties everything together and dictates how the site runs. 
A project contains:

* Global configurations:
     Settings for databases, security keys, installed extensions, and time zones inside settings.py.

* The master routing system: 
    The main URL routing map inside urls.py that directs incoming web requests to the appropriate sections.
    
* Management tools: 
    The manage.py script used to run servers, migrate databases, and execute custom terminal commands.

## What is a Django App?
An app is a modular Python package designed to do one job and do it well. An app contains its own dedicated data layouts (models.py), logic processing (views.py), and presentation layers (templates/).

For example, if you build an e-commerce website (the Project), you will break it down into several sub-features (Apps):

* accounts app: Manages user login, registration, and profiles.
* products app: Displays items, inventories, and categories.
* cart app: Manages item counts and price processing.
* reviews app: Handles user comments and ratings. [1, 10, 11, 12, 13] 

------------------------------
## Why We Use Apps in a Django Application

Django uses this app-based architecture because it promotes best practices in software engineering:

* Code Reusability (DRY Principle): Django apps are designed to be "pluggable". If you build a highly functional blog or payments app for one project, you can literally copy-paste it into a completely different project without rewriting code.

* Easier Maintenance: If something breaks on your checkout page, you don't have to sift through thousands of lines of unrelated code. You simply navigate straight to your checkout app directory. 

* Seamless Team Collaboration: Large software teams can divide and conquer. One group of developers can build the forum system inside a forum app, while another works independently on the user onboarding system in an auth app without merging conflicts. 

* Logical Organization: Instead of creating massive, messy files with thousands of variables, your code stays clean, readable, and structured into hyper-focused micro-directories.
