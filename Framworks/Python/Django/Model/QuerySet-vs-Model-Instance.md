# QuerySet

1. Project.objects.all() → QuerySet

returns a QuerySet.

Think of a QuerySet as:

A collection of database records that Django lets you query and manipulate.

Conceptually:

QuerySet
   │
   ├── Project Management
   └── Team Management

You can iterate over it:

for project in Project.objects.all():
    print(project.name)

Result:

Project Management
Team Management

# Model instance

2. Project.objects.get(id=1) → Model instance

returns one Project object:

<Project: Project Management>

Now:

project.name

gives:

Project Management

# ⚠️ Important behavior of .get()

This is something you should remember for interviews and production code.

If:

> Project.objects.get(id=1)

finds exactly one record → ✅ returns it.

If no record exists:

> Project.objects.get(id=999)

Django raises:

> Project.DoesNotExist

If your query can match multiple records, .get() can raise:

> Project.MultipleObjectsReturned

Therefore:

.get() should be used when you expect exactly one object.

> Project.objects.filter(name__contains="Management")

# This is Django's field lookup syntax:

> field__lookup=value

Here:

name__contains="Management"
│     │
│     └── lookup
└────── field

Django essentially asks the database:

Find projects where name contains "Management".

# Update

Database
   ↓
get()
   ↓
Python object
   ↓
change attribute
   ↓
save()
   ↓
Database updated

# Delete

project = Project.objects.get(id=2)

Then:

project.delete()

Verify:

Project.objects.all()

# ⭐ Important distinction to remember

There are two styles of ORM operations.

## Instance-based

When you already have an object:

project = Project.objects.get(id=1)

project.name = "New Name"
project.save()

project.delete()

## QuerySet/Manager-based

When you're operating through the model:

Project.objects.create(...)

Project.objects.filter(...)

Project.objects.get(...)