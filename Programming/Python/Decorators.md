# 1. Why Do Decorators Exist?

Imagine you have three functions.

```python
def login():
    print("Login logic")

def logout():
    print("Logout logic")

def register():
    print("Register logic")
```

Now your manager says:

> "Log the execution time of every function."

A beginner often writes:

```python
import time

def login():
    start = time.time()

    print("Login logic")

    end = time.time()

    print(end - start)
```

And repeats the same timing code in every function.

This violates the **DRY (Don't Repeat Yourself)** principle.

---

# 2. What Is a Decorator?

### Beginner Definition

A decorator is a function that takes another function, adds extra behavior, and returns a new function.

---

### Professional Definition

> A decorator is a higher-order function that wraps another callable, extending or modifying its behavior without changing its original source code.

The key idea is:

> **Extend behavior without modifying the original function.**

---

# 3. Building a Decorator Manually

Suppose:

```python
def greet():
    print("Hello")
```

Create a decorator:

```python
def decorator(func):

    def wrapper():
        print("Before function")

        func()

        print("After function")

    return wrapper
```

Apply it manually:

```python
greet = decorator(greet)
```

Now:

```python
greet()
```

Output:

```text
Before function
Hello
After function
```

Notice:

We never changed `greet()` itself.

---

# Memory Diagram

Before decorating:

```text
greet
   │
   ▼
Function Object (greet)
```

After:

```text
greet
   │
   ▼
Wrapper Function
      │
      ▼
Original greet Function
```

The name `greet` now points to the wrapper.

The wrapper still holds a reference to the original function.

---

# 4. The `@` Syntax

Instead of writing:

```python
greet = decorator(greet)
```

Python provides syntactic sugar:

```python
@decorator
def greet():
    print("Hello")
```

This is **exactly equivalent** to:

```python
def greet():
    print("Hello")

greet = decorator(greet)
```

There is **no magic**.

The `@` syntax is only shorthand.

---

# 5. Why Do We Need `*args` and `**kwargs`?

Our current wrapper works only for functions without arguments.

Example:

```python
def add(a, b):
    return a + b
```

This fails:

```python
@decorator
def add(a, b):
    ...
```

because `wrapper()` accepts no parameters.

The solution:

```python
def decorator(func):

    def wrapper(*args, **kwargs):

        print("Before")

        result = func(*args, **kwargs)

        print("After")

        return result

    return wrapper
```

Now the decorator works with:

* any number of positional arguments
* any number of keyword arguments

This is the industry-standard pattern.

---

# 6. Example

```python
@decorator
def add(a, b):
    return a + b

print(add(10, 20))
```

Execution:

```text
Before

↓

Original add()

↓

After

↓

30
```

---

# 7. Returning Values

Many beginners forget this.

Wrong:

```python
def wrapper(*args, **kwargs):

    func(*args, **kwargs)
```

This discards the return value.

Correct:

```python
def wrapper(*args, **kwargs):

    result = func(*args, **kwargs)

    return result
```

Always return the original result unless your decorator intentionally changes it.

---

# 8. Internal Working

Suppose:

```python
@decorator
def hello():
    print("Hi")
```

Python executes:

```python
def hello():
    print("Hi")

hello = decorator(hello)
```

Inside `decorator()`:

```python
func
```

points to the original function.

The returned `wrapper` forms a **closure**, capturing `func`.

When you call:

```python
hello()
```

you're actually calling:

```python
wrapper()
```

which then calls:

```python
func()
```

---

# 9. Decorators and Closures

Remember closures?

```python
def decorator(func):

    def wrapper():
        func()

    return wrapper
```

`wrapper()` uses `func`, which belongs to the enclosing scope.

Therefore:

> **Every typical decorator uses a closure.**

Without closures, decorators wouldn't be possible in this form.

---

# 10. Preserving Function Metadata

Consider:

```python
@decorator
def greet():
    """Greeting function"""
```

Now inspect:

```python
print(greet.__name__)
```

Output:

```text
wrapper
```

Oops!

The original function name is lost.

Similarly:

```python
print(greet.__doc__)
```

returns the wrapper's docstring (or `None`).

This is a problem for debugging, documentation, introspection, and frameworks.

---

# 11. `functools.wraps`

Python provides the solution.

```python
from functools import wraps

def decorator(func):

    @wraps(func)
    def wrapper(*args, **kwargs):

        return func(*args, **kwargs)

    return wrapper
```

Now:

```python
print(greet.__name__)
```

Output:

```text
greet
```

and

```python
print(greet.__doc__)
```

returns the original docstring.

**Best Practice:** Always use `@wraps` when writing decorators.

---

# 12. Decorators with Arguments

Sometimes you want configurable behavior.

Example:

```python
@repeat(3)
def hello():
    print("Hello")
```

How does this work?

You need three nested functions:

```python
def repeat(times):

    def decorator(func):

        def wrapper(*args, **kwargs):

            for _ in range(times):
                func(*args, **kwargs)

        return wrapper

    return decorator
```

Execution flow:

```text
repeat(3)
      ↓
decorator(func)
      ↓
wrapper()
```

The outer function captures `times`, while the wrapper captures `func`.

---

# 13. Stacking Decorators

Example:

```python
@decorator1
@decorator2
def greet():
    ...
```

Equivalent to:

```python
greet = decorator1(
            decorator2(greet)
        )
```

Execution order:

1. `decorator2` wraps `greet`.
2. `decorator1` wraps the result.

Call order:

```text
decorator1

↓

decorator2

↓

greet
```

Understanding this order is a common interview topic.

---

# 14. Industry Examples

## Flask

```python
@app.route("/users")
def users():
    ...
```

The route decorator registers the function with Flask's routing table.

---

## FastAPI

```python
@app.get("/products")
def products():
    ...
```

The decorator stores metadata about the endpoint.

---

## Logging

```python
@log_execution
def process():
    ...
```

Logs before and after execution.

---

## Timing

```python
@measure_time
def search():
    ...
```

Measures execution time.

---

## Authentication

```python
@login_required
def dashboard():
    ...
```

Checks permissions before executing the function.

---

## Caching

```python
@lru_cache
def fibonacci(n):
    ...
```

Stores previous results to avoid repeated computation.

---

# Performance

Decorators add:

* one extra function call
* one closure lookup

Overhead is usually very small compared to database queries, network requests, or file I/O.

However, stacking many decorators increases call depth slightly.

---

# Memory Analysis

A typical decorator stores:

* the wrapper function object
* a reference to the original function (closure)
* any captured configuration values

Memory usage is proportional to the number of captured references.

---

# Best Practices

* Always use `functools.wraps`.
* Preserve return values.
* Keep decorators focused on one responsibility.
* Use meaningful decorator names (`@authenticate`, `@retry`, `@measure_time`).
* Avoid decorators with hidden side effects.

---

# Common Mistakes

### Mistake 1

Forgetting to return the wrapper.

```python
def decorator(func):
    def wrapper():
        ...
```

Missing:

```python
return wrapper
```

---

### Mistake 2

Forgetting to return the original function's result.

```python
func(*args, **kwargs)
```

instead of

```python
return func(*args, **kwargs)
```

---

### Mistake 3

Not using `*args` and `**kwargs`, making the decorator work only for a specific function signature.

---

### Mistake 4

Not using `@wraps`, causing loss of metadata.

---

# Time Complexity

Assume the original function takes **O(f(n))**.

Decorator overhead:

* Wrapper call: **O(1)**
* Argument forwarding: **O(k)** where `k` is the number of arguments

Overall complexity remains dominated by the original function.

---

# Interview Questions

1. What is a decorator?
2. Why are decorators useful?
3. Explain how decorators use closures.
4. What does the `@` syntax do internally?
5. Why do decorators usually use `*args` and `**kwargs`?
6. Why should you use `functools.wraps`?
7. How do decorators with arguments work?
8. In what order are stacked decorators applied?

---

# Coding Exercises

## Exercise 1

Write a decorator that prints:

```text
Starting...
```

before a function executes and:

```text
Finished.
```

after it completes.

---

## Exercise 2

Write a timing decorator using the `time` module to measure how long a function takes to execute.

---

## Exercise 3

Write a decorator that counts how many times a function has been called.

Hint: You'll need a closure and `nonlocal`.

---

## Exercise 4

Create a decorator named `@uppercase` that converts the returned string from a function to uppercase.

Example:

```python
@uppercase
def greet():
    return "hello"
```

Output:

```text
HELLO
```

---

# Mini Project: Simple Authorization Decorator

Create a decorator:

```python
def login_required(func):
```

Assume a global variable:

```python
is_logged_in = True
```

If the user is logged in:

* execute the original function

Otherwise:

* print `"Access Denied"`

Example:

```python
@login_required
def profile():
    print("Welcome to your profile")
```

Test it with both `True` and `False`.

This models how web frameworks protect routes.

---

# Homework (Predict Without Running)

```python
from functools import wraps

def decorator(func):

    @wraps(func)
    def wrapper(*args, **kwargs):
        print("Before")
        result = func(*args, **kwargs)
        print("After")
        return result

    return wrapper


@decorator
def add(a, b):
    """Adds two numbers"""
    return a + b


print(add(5, 3))
print(add.__name__)
print(add.__doc__)
```

Answer these:

1. What is the complete output?
2. Which function object does `add` reference after decoration?
3. Where is the original `add` function stored?
4. Why does `add.__name__` still return `"add"`?
5. What would happen if `@wraps` were removed?
6. Draw the memory diagram showing the wrapper, closure, and original function.

