**Django already provides authentication functionality through:**

> django.contrib.auth

and its built-in **User model**.

Conceptually:

User
├── username

├── password

├── email

├── first_name

├── last_name

├── is_active

├── is_staff

└── is_superuser

You don't need to build password hashing, login sessions, etc. from scratch.

## 1. What is the problem with using the default User forever?

Suppose FlowForge eventually needs users like:

User
├── email
├── password
├── name
├── tenant
├── role
├── status
└── ...

The built-in Django User doesn't necessarily match your application's desired identity model.

For example, many SaaS applications want:

Login with:
email
password

rather than:

username
password

You can extend Django's user with additional models, but there is another option:

Create a custom User model.

## 2. What is a custom User model?

Instead of using Django's:

django.contrib.auth.models.User

you define your own:

> class User(AbstractUser):
    ...

or potentially use:

>class User(AbstractBaseUser):
    ...

There is an important difference between these approaches.

### AbstractUser

Gives you Django's normal user functionality and fields, while allowing you to customize it.

For a typical application, this is the easier option.

### AbstractBaseUser

Gives you a lower-level foundation.

You are responsible for more of the user-model behavior, such as defining the identity field and manager appropriately.

It's useful when you need a very customized authentication model, but it requires more work.


# Create the custom User

Open:

accounts/models.py

Add:

```python
from django.contrib.auth.models import AbstractUser


class User(AbstractUser):
    pass
```
For now, pass is intentional.

We're telling Django:

"Use Django's normal User behavior, but make this our application's User model so we can extend it later."