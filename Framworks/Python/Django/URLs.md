URLs naming:

Right now we have:

path("hello/", views.hello)

Let's understand an important URL concept: named URLs.

Instead of:


> path("hello/", views.hello)

we can write:

> path("hello/", views.hello, name="hello")

The name gives this URL a stable identifier.

Why is that useful?

Imagine later your URL changes:

/hello/

to:

/greeting/

If your templates and Python code are referring directly to /hello/, you'd potentially need to change many places.

With a named URL:

name = "hello"

your code can ask Django:

**"Give me the URL associated with the name hello."**

> **This is called URL reversing.**

We'll use it heavily with templates, redirects, forms, and APIs.


The real benefit

Suppose you have this URL:

path('hello/', views.hello, name="hello")

And somewhere in your HTML you want a link to it.

You could write:

<a href="/hello/">Say Hello</a>

This works.

But now imagine you change your URL:

path('greeting/', views.hello, name="hello")

Your URL is now:

/greeting/

If you hard-coded /hello/ everywhere, you'd have to find and change all those places.

But with a named URL, you can do:

<a href="{% url 'hello' %}">Say Hello</a>

Django says:

"Find the URL whose name is hello."

Django finds:

path('greeting/', views.hello, name="hello")

and generates:

/greeting/

So you don't care what the actual URL is.

Think of it like a variable

You can think of:

path('greeting/', views.hello, name="hello")

as:

"hello" → "/greeting/"

Then your template says:

{% url 'hello' %}

Django looks up:

hello
  ↓
/greeting/

and produces the correct URL.


Don't think:

URL = name

Think:

URL pattern                 NAME
     ↓                        ↓
'hello/'       ←──────→    'hello'
'greeting/'    ←──────→    'greeting'

**The URL is what the user/browser sees.**

**The name is what your Django code uses to refer to that URL.**


Questions:
1. What code did you change?

2. Why do you think giving a URL a name is useful?


-> A URL name provides a stable identifier for a URL pattern. Instead of hardcoding the URL in templates or Python code, we can refer to the URL by its name. If the actual URL path changes later, we only need to update the URL pattern, while the code that references the URL name can remain unchanged.