For example:

> /products/<id>/

Then:

/products/10/

gives:

id = 10

and:

/products/25/

gives:

id = 25


# 1. Dynamic parameter syntax 

You wrote:

'products/int:id/'

Django's syntax is:

> 'products/<int:id>/'

Notice the < >.


The structure is:

<converter:name>

So:

<int:id>

means:

### Capture an integer from this part of the URL and pass it to the view as id.

