Excellent.

Today we're entering one of the most important concepts in advanced Python.

Many developers use closures without realizing it. Frameworks like Flask, Django, FastAPI, and libraries throughout the Python ecosystem rely on this concept extensively.

A closure is what allows a function to **remember values from the environment where it was created**, even after that environment would normally be gone.

This lesson builds directly on the previous one about **functions being first-class objects**.

---

# Closures (Deep Dive)

# 1. What is a Closure?

### Beginner Definition

A closure is an **inner function that remembers variables from its outer function even after the outer function has finished executing.**

---

### Professional Definition

> A closure is a function object that captures and retains references to variables from its lexical (enclosing) scope, allowing those variables to remain accessible when the function is executed later.

Notice two important words:

* **captures**
* **retains**

Those are the essence of a closure.

---

# 2. Why Do We Need Closures?

Suppose you want to create customized greeting functions.

Without closures:

```python
def greet(name):
    print(f"Hello {name}")
```

You would have to pass the name every time.

With closures, you can create specialized functions.

```python
hello_alice = create_greeter("Alice")

hello_alice()
hello_alice()
hello_alice()
```

The function remembers `"Alice"`.

---

# 3. Nested Functions

Closures start with nested functions.

Example:

```python
def outer():

    def inner():
        print("Hello")

    inner()

outer()
```

Output:

```text
Hello
```

Here, `inner()` exists only inside `outer()`.

---

# 4. Returning the Inner Function

Instead of calling it:

```python
def outer():

    def inner():
        print("Hello")

    return inner
```

Now:

```python
x = outer()

x()
```

Output:

```text
Hello
```

This should already feel familiar from the previous lesson.

But here's where closures become interesting.

---

# 5. Capturing Variables

```python
def outer():

    message = "Hello Python"

    def inner():
        print(message)

    return inner
```

Call:

```python
greet = outer()

greet()
```

Output:

```text
Hello Python
```

Wait...

`outer()` has already finished executing.

Why does `message` still exist?

That's the closure.

---

# 6. Memory Visualization

When `outer()` runs:

```text
Stack Frame (outer)

message
↓

"Hello Python"

inner
```

Normally, when `outer()` returns, its stack frame would disappear.

But Python notices:

> `inner()` still needs `message`.

Instead of destroying it completely, Python stores the captured variable in a special structure attached to the function object.

Conceptually:

```text
greet
 │
 ▼
+----------------------+
| Function Object      |
| Code                 |
| Closure              |
| message ----------+  |
+-------------------|--+
                    ▼
            "Hello Python"
```

The function object now owns a reference to that captured value.

---

# 7. Lexical Scoping

Python follows **lexical (static) scoping**.

A function looks for variables in this order:

1. Local
2. Enclosing
3. Global
4. Built-in

You already learned this as the **LEGB Rule**.

Closures depend on the **Enclosing** scope.

Example:

```python
def outer():

    x = 10

    def inner():
        print(x)

    return inner
```

`x` is not local to `inner()`.

It comes from the enclosing function.

---

# 8. Free Variables

A **free variable** is a variable used inside a function but defined in an enclosing scope.

Example:

```python
def outer():

    message = "Python"

    def inner():
        print(message)

    return inner
```

Here:

```text
message
```

is a free variable for `inner()`.

---

# 9. Closures Maintain State

Suppose we want a counter.

```python
def counter():

    count = 0

    def increment():
        return count + 1

    return increment
```

Problem:

```python
count
```

never changes.

To modify it:

```python
def counter():

    count = 0

    def increment():
        nonlocal count
        count += 1
        return count

    return increment
```

Now:

```python
c = counter()

print(c())
print(c())
print(c())
```

Output:

```text
1
2
3
```

The function remembers the updated state.

---

# 10. What is `nonlocal`?

Without `nonlocal`:

```python
count += 1
```

creates a new local variable, causing:

```text
UnboundLocalError
```

`nonlocal` tells Python:

> Use the variable from the enclosing scope, not a new local one.



> What nonlocal Means
nonlocal count

This tells Python:

**"Don't create a new local count. Use the count from the nearest enclosing function."**

Diagram:

counter()

count = 0
   ▲
   │
increment()
│
└── nonlocal count

Now when Python sees:

count += 1

it updates the enclosing variable instead of creating a new local one.

Complete Execution Diagram
c = counter()

        counter()
      ┌─────────────┐
      │ count = 0   │
      │             │
      │ increment() │
      └──────┬──────┘
             │
             ▼
             c
First call
count = 0

↓

count += 1

↓

count = 1

↓

return 1
Second call
count = 1

↓

count += 1

↓

count = 2

↓

return 2
Third call
count = 2

↓

count += 1

↓

count = 3

↓

return 3

> Why Is This Useful?

**Closures let you keep private state without using global variables or creating a class.**

For example:

def make_greeter(name):
    def greet():
        return f"Hello, {name}!"
    return greet

alice = make_greeter("Alice")
bob = make_greeter("Bob")

print(alice())  # Hello, Alice!
print(bob())    # Hello, Bob!

Each returned function remembers its own name, just as the counter() example remembers its own count.

---

# 11. Internal Working

Every function object contains metadata.

A closure stores references to captured variables.

You can inspect this:

```python
def outer():

    x = 100

    def inner():
        print(x)

    return inner

f = outer()

print(f.__closure__)
```

Typical output:

```text
(<cell at 0x...>,)
```

Those **cell objects** hold the captured variables.

You can even inspect the contents:

```python
print(f.__closure__[0].cell_contents)
```

Output:

```text
100
```

This is a great interview demonstration that closures are real runtime objects.

---

# 12. Memory Analysis

Each closure stores references to captured variables.

If a closure captures `k` variables:

Memory:

```text
O(k)
```

Calling the closure itself is still:

```text
O(1)
```

(excluding the work done inside the function).

---

# 13. Closures vs Classes

Closure:

```python
def counter():

    count = 0

    def increment():
        nonlocal count
        count += 1
        return count

    return increment
```

Class:

```python
class Counter:

    def __init__(self):
        self.count = 0

    def increment(self):
        self.count += 1
        return self.count
```

Both maintain state.

### When to prefer a closure

* Small amount of state
* One main behavior
* Lightweight factory function

### When to prefer a class

* Multiple methods
* Rich object behavior
* Complex state
* Inheritance or polymorphism

---

# 14. Industry Use Cases

## Function Factories

```python
def multiplier(n):

    def multiply(x):
        return x * n

    return multiply
```

Usage:

```python
double = multiplier(2)
triple = multiplier(3)

print(double(10))
print(triple(10))
```

Output:

```text
20
30
```

---

## Logging

```python
def logger(prefix):

    def log(message):
        print(f"[{prefix}] {message}")

    return log
```

Now create specialized loggers:

```python
error_log = logger("ERROR")
info_log = logger("INFO")
```

---

## Configuration

```python
def database(connection):

    def query(sql):
        ...

    return query
```

The returned function remembers the connection without requiring it every call.

---

## Decorators

Decorators are implemented using closures.

We'll see this in the next lesson.

---

# Performance

Closures are very efficient.

Creating one:

* Function object allocation
* Small closure metadata

Usually:

```text
O(1)
```

Memory grows only with captured variables.

---

# Best Practices

* Capture only what you need.
* Use `nonlocal` only when modifying enclosing variables.
* Prefer classes if behavior becomes large or state becomes complex.
* Give factory functions descriptive names.

---

# Interview Questions

1. What is a closure?
2. What is lexical scoping?
3. What is a free variable?
4. Why does a closure keep variables alive?
5. What is `nonlocal`?
6. How are closures different from classes?
7. How can you inspect a closure in Python?
8. Give a real-world use case for closures.

---

# Coding Exercises

## Exercise 1

Write a function:

```python
def power(exponent):
```

Return a closure that raises a number to the given exponent.

Example:

```python
square = power(2)
cube = power(3)

print(square(5))
print(cube(5))
```

Expected output:

```text
25
125
```

---

## Exercise 2

Create a greeting factory:

```python
english = greeter("Hello")
hindi = greeter("Namaste")
```

Calling each returned function should prepend its greeting.

---

## Exercise 3

Implement a counter using `nonlocal` that increments each time it is called.

---

## Exercise 4

Create a closure that remembers a tax rate and calculates the final price for different products.

---

# Mini Project: Discount Calculator Factory

Build a function:

```python
def discount_calculator(discount_percent):
```

Return a function that applies the stored discount.

Example:

```python
ten_percent = discount_calculator(10)
twenty_percent = discount_calculator(20)

print(ten_percent(1000))
print(twenty_percent(1000))
```

Expected output:

```text
900.0
800.0
```

This pattern is common in pricing engines, coupon systems, and configurable business logic.

---

# Homework (Predict Without Running)

```python
def outer():

    message = "Python"

    def inner():
        print(message)

    return inner


f = outer()

print(type(f))
print(f.__closure__)
print(f.__closure__[0].cell_contents)

f()
```

Answer these:

1. What is the complete output?
2. Why does `message` still exist after `outer()` returns?
3. What is stored in `__closure__`?
4. What is a cell object?
5. Draw the memory diagram showing the function object and captured variable.
6. What are the time and space complexities?
