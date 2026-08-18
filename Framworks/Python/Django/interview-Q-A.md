# what is Django?
Django is a web framework build in python.It solve the problem of writing a repeated connection code for developer. It provide the build in different libraries for development.
Django gives the authentication,admin panel, data connection and management, error handling and Rest api handling by it self, developer not need to implement this basic application feature. Developer can focus on feature of the application.


Django is a Python web framework used to build web applications. It reduces the need to write repetitive code for common web-development tasks. Django provides many built-in features such as authentication, an admin panel, database interaction, URL routing, security, and more. It can also be used to build APIs, and Django REST Framework provides additional tools for building REST APIs. Because Django provides these common features, developers don't have to implement everything from scratch and can focus more on the application's business requirements.

# Q. why use Django?

Django is used because it supports rapid development and provides many built-in features such as authentication, URL routing, ORM, admin interface, and security mechanisms. It follows the “batteries-included” approach, meaning many common web-development features are available out of the box.

**Batteries-included**, meaning the framework provides many commonly needed features without requiring developers to build everything from scratch.

You could also mention:

Rapid development
Built-in security features
ORM
Authentication
Admin panel
URL routing
Scalability
Large ecosystem/community

# Q. What is MVT in Django?

MVT stands for Model-View-Template.It is a software design patterns used to organize code in web applications. The Model is responsible for data and database interaction through the ORM. 
The View handles HTTP requests, contains the application logic, and returns an HTTP response. 
The Template is responsible for the presentation layer, meaning how the data is displayed to the user.

MVT vs MVC

Django's MVT is conceptually similar to MVC, Model-View-Controller (an architectural pattern that separates data, presentation, and application control).


# How is Django MVT different from MVC?

In Django, the View works somewhat like the Controller in MVC because it handles the request and application logic, while the Django Template works like the View in MVC because it handles presentation.

# Django project file structure

Django Project

A project is the overall configuration of your Django website/application.

Django App

An app is a reusable component that provides a particular feature, such as users, products, payments, or orders.

Then explain each:

.venv/
A virtual environment, meaning an isolated Python environment used to manage project-specific dependencies.

manage.py
A command-line utility used to perform administrative tasks such as running the development server, creating migrations, applying migrations, and creating apps.

config/
Usually contains the main project configuration.

settings.py
Contains project configuration such as installed apps, middleware, database settings, templates, static files, and other settings.

urls.py
Defines URL patterns and maps URLs to views.

models.py
Defines database models.

views.py
Contains views that process requests and return responses.

admin.py
Used to register models with Django's admin interface.

migrations/
Contains migration files, which describe changes to the database schema, meaning the structure of database tables and relationships.