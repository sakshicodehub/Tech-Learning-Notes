# 1. What is a template?

**A Django template is an HTML file that can contain Django Template Language (DTL).**

Example:

<h1>Hello, {{ name }}</h1>

If the view sends:

{"name": "Sakshi"}

Django renders:

<h1>Hello, Sakshi</h1>

So:

{{ name }}  is a template variable.

# 2. The flow

Now our architecture becomes:

Browser
   │
   │ GET /hello/
   ↓
URLconf
   ↓
View
   ↓
Context/Data
   ↓
Template
   ↓
Rendered HTML
   ↓
HttpResponse
   ↓
Browser

This is much closer to a real Django application



### Create template directory

Inside your core app, create:

core/
└── templates/
    └── core/
        └── dashboard.html

Why the second core?

Because as your project grows, multiple apps may have templates with the same filename:

accounts/templates/accounts/dashboard.html
projects/templates/projects/dashboard.html

The namespace helps avoid collisions.


# Template Variables, Conditions, and Loops

You currently have:

```python
def dashboard(request):
    context = {
        "name": "Sakshi"
    }

    return render(request, "core/dashboard.html", context)
```

and:

```html
<p>Welcome, {{ name }}</p>
```

The flow is:

```text
View
  ↓
context = {"name": "Sakshi"}
  ↓
Template
  ↓
{{ name }}
  ↓
Sakshi
```

---

## 1. Template variables — `{{ }}`

The syntax:

```html
{{ variable }}
```

is used to display a value.

For example:

```python
context = {
    "name": "Sakshi",
    "role": "Developer",
    "company": "FlowForge"
}
```

Template:

```html
<h1>Welcome, {{ name }}</h1>
<p>Role: {{ role }}</p>
<p>Company: {{ company }}</p>
```

Result:

```text
Welcome, Sakshi
Role: Developer
Company: FlowForge
```

---

# 2. Template conditions — `{% if %}`

Suppose your dashboard should display a different message depending on whether a user is active.

View:

```python
context = {
    "name": "Sakshi",
    "is_active": True
}
```

Template:

```html
{% if is_active %}
    <p>User is active</p>
{% else %}
    <p>User is inactive</p>
{% endif %}
```

Notice the difference:

```text
{{ }}
```

is primarily for **displaying values**.

While:

```text
{% %}
```

is used for **template logic/control structures**.

Think:

```text
{{ value }}
       ↓
Display data

{% if ... %}
       ↓
Control template rendering
```

---

# 3. Template loops — `{% for %}`

This becomes extremely useful for displaying database results.

Suppose your view has:

```python
context = {
    "projects": [
        "Project A",
        "Project B",
        "Project C"
    ]
}
```

Template:

```html
<h1>Projects</h1>

<ul>
    {% for project in projects %}
        <li>{{ project }}</li>
    {% endfor %}
</ul>
```

The rendered HTML will contain:

```html
<ul>
    <li>Project A</li>
    <li>Project B</li>
    <li>Project C</li>
</ul>
```

This is going to become important when we connect:

```text
Database
   ↓
Model
   ↓
ORM
   ↓
View
   ↓
Template
   ↓
HTML
```

---

# 🧠 Important architecture rule

Don't put your business logic into templates.

For example, avoid trying to perform complex calculations or database operations inside HTML.

Prefer:

```text
Model
   ↓
Business/data logic
   ↓
View
   ↓
Prepare context
   ↓
Template
   ↓
Presentation
```

The template should primarily be responsible for **presentation**.

---

# 🧪 Your Challenge

Let's upgrade your FlowForge dashboard.

Change your view so it sends:

```text
name = "Sakshi"
is_admin = True
projects = [
    "Project Management",
    "Task Management",
    "Team Management"
]
```

Then update `dashboard.html` to display:

```text
FlowForge Dashboard

Welcome, Sakshi

You are an administrator.

Projects:
- Project Management
- Task Management
- Team Management
```

### Requirements

Use:

```text
{{ name }}
```

for the name.

Use:

```text
{% if ... %}
```

for the administrator message.

Use:

```text
{% for ... %}
```