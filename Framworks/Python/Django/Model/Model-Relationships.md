# different relationships in Django

1. OneToOneField
2. ForeignKey
3. ManyToManyField

# 1.What is a OneToOneField

User 1 ───── Profile 1
User 2 ───── Profile 2
User 3 ───── Profile 3

user = models.OneToOneField(
    User,
    on_delete=models.CASCADE
)

# 1. What is a ForeignKey?

Suppose we have:

class Project(models.Model):
    name = models.CharField(max_length=200)

We want every project to belong to a tenant.

Conceptually:

Tenant
----------------
id | name
----------------
1  | Company A
2  | Company B


Project
--------------------------------
id | name              | tenant_id
--------------------------------
1  | Project A         | 1
2  | Project B         | 1
3  | Project C         | 2

Notice:

Project.tenant_id
       ↓
Tenant.id

That's the relationship.

This is called a:

> One-to-many relationship
> In Django, we represent it using ForeignKey.

# 3. Connect Project → Tenant

Change your Project model to:
```python
class Project(models.Model):
    name = models.CharField(max_length=200)
    tenant = models.ForeignKey(
        Tenant,
        on_delete=models.CASCADE
    )

    def __str__(self):
        return self.name

The important part is:

tenant = models.ForeignKey(
    Tenant,
    on_delete=models.CASCADE
)
```

This says:

Each Project belongs to one Tenant.

But a Tenant can have many Projects.

So:

Tenant
   │
   ├──── Project
   ├──── Project
   └──── Project

> on_delete=models.CASCADE -> Django says: Delete the related Projects as well.

So:

Delete Tenant A
      ↓
Project A deleted
Project B deleted
Project C deleted

This is called cascade deletion.

**There are other strategies:**

CASCADE
PROTECT
SET_NULL
SET_DEFAULT
DO_NOTHING



Project.objects.filter(tenant__isnull=True).count()


# Querying Related Objects

## 1. Forward relationship

```python
project = Project.objects.get(id=1)

project.tenant

returns:
```

< Tenant: FlowForge >

And:

> project.tenant.name

returns:

FlowForge

## 2. Reverse relationship

Now let's go the other direction.

We have:

Tenant
   ↓
Projects

> Django automatically gives the Tenant a reverse relationship.

Try in the shell:

> tenant = Tenant.objects.first()

Then:

> tenant.project_set.all()

# 3. Why is it called **project_set**?

Because Django needs a name for the reverse relationship.

You didn't specify one, so Django creates the default:

> project_set

But in production code, it's usually better to give the relationship an explicit name.

For example:

```python
tenant = models.ForeignKey(
    Tenant,
    on_delete=models.CASCADE,
    related_name="projects"
)
```

Then instead of:

> tenant.project_set.all()

you can write:

> tenant.projects.all()

Much clearer.

> Project.objects.filter(tenant__name="FlowFroge")


### Questions
1. What is the difference between:

project.tenant

and

tenant.project_set.all()

2. Why would we use related_name="projects"?.


# 1.What is a ManyToManyField

Multiple records on one side can be associated with multiple records on the other side.

Student ↔ Course

because:

Student A → Python
Student A → Django

Student B → Python
Student B → React

Both sides can have multiple relationships.

> courses = models.ManyToManyField(Course)

ForeignKey
1 ────────< many

OneToOne
1 ───────── 1

ManyToMany
many >────< many