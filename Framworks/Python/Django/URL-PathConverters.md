You just used:

path("products/<int:id>/", views.products, name="products")

This introduced an important Django feature: path converters.

🎯 Goal

Understand:

What <int:id> means
Why Django needs converters
Common converters
How Django passes captured values to the view
When to use each converter

# 1. What is <int:id>?

Break it into two parts:

<int:id>
  │   │
  │   └── parameter name
  └────── converter

So:

path("products/<int:id>/", views.products)

means:

> Match /products/ followed by an integer, capture that integer as id, and pass it to the view.

# 2. str

You can capture text using:

path("products/<str:name>/", views.product_by_name)

For:

/products/laptop/

Django passes:

name = "laptop"

str is actually the default converter, so:

"<str:name>"

and:

"<name>"

are equivalent for normal non-slash text.

# 🔹 3. int

You already used:

<int:id>

It matches positive integers such as:

/products/1/
/products/25/
/products/100/

It won't match:

/products/abc/

This is useful when your URL represents a numeric identifier.

# 🔹 4. slug

> A slug is commonly used for human-readable URLs.

Example:

/blog/django-url-routing/

You could define:

path("blog/<slug:slug>/", views.blog_detail)

The value could be:

django-url-routing

Slugs are commonly useful for things like:

/blog/my-first-post/
/products/iphone-17/
/courses/python-for-beginners/

# 🔹 5. uuid

For UUID-based identifiers:

path("users/<uuid:user_id>/", views.user_detail)

Example:

/users/550e8400-e29b-41d4-a716-446655440000/

**This is particularly relevant in professional systems where resources may use UUIDs instead of sequential IDs.**

For your future multi-tenant FlowForge architecture, you'll likely encounter UUIDs frequently.

# 🔹 6. path

path can capture text including /.

For example:

path("files/<path:file_path>/", views.file_view)

could match:

/files/documents/2026/report.pdf

This is different from str, because str stops at /.


# Under the hood

When Django receives:

GET /products/25/

the URL resolver compares the request against your URL patterns.

Conceptually:

/products/<int:id>/
          ↓
        "25"
          ↓
      int converter
          ↓
         25
          ↓
products(request, id=25)

An important detail:

With <int:id>, Django doesn't merely capture "25" as arbitrary text. The converter converts it to an integer before passing it to the view.

So your view receives:

id == 25

rather than:

id == "25"