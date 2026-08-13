# The important ORM concept

This:

> Project.objects

**is the model's manager.**

It provides methods for interacting with the database.

For example:

Project.objects.create(...)
Project.objects.get(...)
Project.objects.filter(...)
Project.objects.all(...)
Project.objects.update(...)
Project.objects.delete(...)
Project.objects.filter(name__contains="Management")

> python manage.py shell
------shell is open--------------
13 objects imported automatically (use -v 2 for details).

Python 3.12.3 (main, Jun 19 2026, 12:46:00) [GCC 13.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
(InteractiveConsole)
>>> 
>>> 
>>> from core.models import Project     // Now Django knows which model we want to work with.
>>> 
>>> Project
<class 'core.models.Project'>

>>> Project.objects.all()
<QuerySet [<Project: Project Management>, <Project: Team Management>]>

>>>  Project.objects.get(id=1)
<Project: Project Management>
>>> 

> Project.objects.filter(name__contains="Management")
