
def products(request,id):
    return HttpResponse(f"Product ID: {id}")

def blog_post(request, slug):
    return HttpResponse(f"Blog Post Slug: {slug}")

def dashboard(request):
    return render(request, 'core/dashboard.html')

# 1. Why request?

When someone visits:

/dashboard/

Django creates an HttpRequest object containing information about that request.

For example, it can contain:

request.method     → GET / POST
request.user       → who is logged in
request.GET        → ?search=django
request.POST       → submitted form data
request.headers    → browser/request information

So Django gives your view that object:

def dashboard(request):

> You need request because the view is handling a web request.

Why does render() need both?

Because Django needs two pieces of information:

render(request,  'core/dashboard.html')
       ↑                  ↑
       │                  │
   Who made the      What page should
     request?           I render?



## One thing to remember

This is the key difference from your previous exercise:

# Previous
return HttpResponse("Hello, FlowForge!")

versus:

# Template-based
return render(request, "core/dashboard.html", context)

HttpResponse is useful when you want to construct the response directly.

render() is useful when your response is based on an HTML template + dynamic data.