1. Why variable-length arguments exist

Why Do We Need Variable-Length Arguments?

Imagine writing a function to add numbers.

Without *args:

def add(a, b):
    return a + b

Works only for two numbers.

add(10, 20)

But what if tomorrow you need:

add(10, 20, 30)

Or:

add(10, 20, 30, 40, 50)

You would have to keep changing the function signature.

That's not scalable.

Python solves this using *args.

2. *args

*args allows a function to accept any number of positional arguments.

Example:

def add(*args):
    print(args)

Call:

add(10, 20, 30)

Output:

(10, 20, 30)

Notice:

# args is a tuple, not a list.

# Why a Tuple?

Because tuples are:

Immutable
Slightly more memory-efficient
Faster for read-only data

Python assumes function arguments shouldn't be modified accidentally.


# Iterating Over *args
def add(*args):
    total = 0

    for num in args:
        total += num

    return total

Usage:

print(add(1, 2, 3))  //6
print(add(10, 20, 30, 40)) //100

3. **kwargs

**kwargs allows a function to accept any number of keyword arguments.

Example:

def profile(**kwargs):
    print(kwargs)

Call:

profile(
    name="Sakshi",
    age=24,
    city="Indore"
)

Output:

{
'name': 'Sakshi',
'age': 24,
'city': 'Indore'
}

Notice:

kwargs is a dictionary.

Why a Dictionary?

Keyword arguments naturally map:

Parameter Name

↓

Value

This is exactly what dictionaries represent.

# Iterating Over kwargs
def profile(**kwargs):

    for key, value in kwargs.items():
        print(key, value)

Output:

name Sakshi
age 24
city Indore

4. # Packing vs Unpacking

# Packing

Multiple values become one object.

Example:

def demo(*args):
    print(args)

Call:

demo(1, 2, 3)

Python packs them:

args = (1, 2, 3)

# Unpacking

One object becomes multiple values.

Example:

numbers = (10, 20)

print(*numbers)

Output:

10 20

Python conceptually does:

print(10, 20)


# Dictionary Unpacking

Function:

def student(name, age):
    print(name, age)

Dictionary:

data = {
    "name": "Alice",
    "age": 25
}

Call:

# student(**data)

Python expands:

student(name="Alice", age=25)

Using * and ** in function calls


# Parameter Order Rules

Python requires parameters to appear in this order:

def func(
    positional,
    default=0,
    *args,
    keyword_only=None,
    **kwargs
):
    ...

A simplified order to remember is:

Regular positional parameters
Default parameters
*args
Keyword-only parameters (we'll study these in the next lesson)
**kwargs


# 📘 Python Variable-Length Arguments (`*args` and `**kwargs`)

---

# 1. Why Do Variable-Length Arguments Exist?

One of the biggest goals in programming is to write **flexible and reusable** code.

Imagine you write a function to add two numbers.

```python
def add(a, b):
    return a + b
```

Usage:

```python
print(add(10, 20))
```

Output:

```text
30
```

This works perfectly—for **two numbers**.

But what if tomorrow you need to add three numbers?

```python
add(10, 20, 30)
```

Python raises an error because the function expects only two arguments.

If later you need four or five numbers, you would have to keep changing the function definition.

```python
def add(a, b, c):
    ...

def add(a, b, c, d):
    ...

def add(a, b, c, d, e):
    ...
```

This is repetitive, difficult to maintain, and not scalable.

Python solves this problem with **variable-length arguments**.

These allow a function to accept **any number of arguments** without changing its definition.

---

# 2. `*args` — Variable-Length Positional Arguments

`*args` allows a function to accept **any number of positional arguments**.

Example:

```python
def add(*args):
    print(args)
```

Call the function:

```python
add(10, 20, 30)
```

Output:

```text
(10, 20, 30)
```

Instead of expecting a fixed number of values, Python collects all positional arguments into a single object.

---

## What Exactly Is `args`?

`args` is a **tuple**.

```python
def demo(*args):
    print(type(args))
```

Output:

```text
<class 'tuple'>
```

So conceptually:

```python
add(10, 20, 30)
```

becomes

```python
args = (10, 20, 30)
```

The `*` tells Python:

> "Collect all extra positional arguments into a tuple."

---

## Why Does Python Use a Tuple?

Python intentionally stores `*args` in a tuple because tuples are:

* **Immutable** (cannot be modified accidentally)
* More memory-efficient than lists
* Slightly faster for read-only data
* A good fit for function inputs, which are usually meant to be read, not changed

This helps prevent accidental modification of the passed arguments.

---

## Iterating Over `*args`

Since `args` is a tuple, you can loop through it just like any other iterable.

Example:

```python
def add(*args):
    total = 0

    for num in args:
        total += num

    return total
```

Usage:

```python
print(add(1, 2, 3))
print(add(10, 20, 30, 40))
```

Output:

```text
6
100
```

No matter how many numbers are passed, the same function works.

---

## `*args` Can Be Empty

If no positional arguments are provided:

```python
def demo(*args):
    print(args)

demo()
```

Output:

```text
()
```

An empty tuple is created.

---

# 3. `**kwargs` — Variable-Length Keyword Arguments

While `*args` collects positional arguments, `**kwargs` collects **keyword arguments**.

Example:

```python
def profile(**kwargs):
    print(kwargs)
```

Call:

```python
profile(
    name="Sakshi",
    age=24,
    city="Indore"
)
```

Output:

```python
{
    'name': 'Sakshi',
    'age': 24,
    'city': 'Indore'
}
```

Python collects all keyword arguments into a **dictionary**.

---

## What Exactly Is `kwargs`?

`kwargs` is simply a dictionary.

```python
def demo(**kwargs):
    print(type(kwargs))
```

Output:

```text
<class 'dict'>
```

Conceptually:

```python
profile(
    name="Sakshi",
    age=24
)
```

becomes

```python
kwargs = {
    "name": "Sakshi",
    "age": 24
}
```

The `**` tells Python:

> "Collect all keyword arguments into a dictionary."

---

## Why a Dictionary?

Keyword arguments naturally represent **key-value pairs**.

For example:

```python
name="Sakshi"
age=24
```

is equivalent to:

```python
{
    "name": "Sakshi",
    "age": 24
}
```

A dictionary is therefore the perfect data structure to store keyword arguments.

---

## Iterating Over `**kwargs`

Since `kwargs` is a dictionary, you can loop through it using `.items()`.

Example:

```python
def profile(**kwargs):

    for key, value in kwargs.items():
        print(key, value)
```

Call:

```python
profile(
    name="Sakshi",
    age=24,
    city="Indore"
)
```

Output:

```text
name Sakshi
age 24
city Indore
```

---

# 4. Packing vs Unpacking

One of the most important ideas behind `*` and `**` is **packing** and **unpacking**.

---

## Packing

Packing means:

> Multiple values are collected into a single object.

Example:

```python
def demo(*args):
    print(args)

demo(1, 2, 3)
```

Python internally performs something similar to:

```python
args = (1, 2, 3)
```

Many values become one tuple.

Similarly:

```python
def profile(**kwargs):
    print(kwargs)
```

Call:

```python
profile(name="Alice", age=25)
```

Conceptually:

```python
kwargs = {
    "name": "Alice",
    "age": 25
}
```

Many keyword arguments become one dictionary.

---

## Unpacking

Unpacking is the opposite of packing.

It means:

> One iterable or mapping is expanded into multiple individual values.

---

### Tuple Unpacking

```python
numbers = (10, 20)

print(*numbers)
```

Output:

```text
10 20
```

Conceptually, Python changes:

```python
print(*numbers)
```

into

```python
print(10, 20)
```

Each element of the tuple becomes a separate argument.

---

### List Unpacking

Unpacking also works with lists.

```python
numbers = [1, 2, 3]

print(*numbers)
```

Output:

```text
1 2 3
```

---

### Dictionary Unpacking

Suppose a function expects two keyword arguments.

```python
def student(name, age):
    print(name, age)
```

Dictionary:

```python
data = {
    "name": "Alice",
    "age": 25
}
```

Call:

```python
student(**data)
```

Python conceptually converts it to:

```python
student(
    name="Alice",
    age=25
)
```

Each key becomes a parameter name, and each value becomes its argument.

---

# Using `*` and `**` in Function Calls

The symbols `*` and `**` are not only used in function definitions—they can also be used while calling functions.

Example using a tuple:

```python
def add(a, b):
    return a + b

numbers = (10, 20)

print(add(*numbers))
```

Python expands it to:

```python
add(10, 20)
```

Example using a dictionary:

```python
def student(name, age):
    print(name, age)

data = {
    "name": "Alice",
    "age": 25
}

student(**data)
```

Python expands it to:

```python
student(name="Alice", age=25)
```

---

# 5. Parameter Order Rules

Python follows strict rules about the order of parameters in a function definition.

The complete order is:

```python
def func(
    positional,
    default=0,
    *args,
    keyword_only=None,
    **kwargs
):
    ...
```

---

## Simplified Order to Remember

```text
1. Regular positional parameters
2. Default parameters
3. *args
4. Keyword-only parameters
5. **kwargs
```

If you violate this order, Python raises a `SyntaxError`.

---

## Example

```python
def student(name, age=18, *subjects, city=None, **details):
    print(name)
    print(age)
    print(subjects)
    print(city)
    print(details)
```

Call:

```python
student(
    "Sakshi",
    24,
    "Math",
    "Science",
    city="Indore",
    hobby="Reading"
)
```

Output:

```text
Sakshi
24
('Math', 'Science')
Indore
{'hobby': 'Reading'}
```

Notice how each type of argument is stored separately.

---

# Best Practices

* Use `*args` when the number of positional arguments is unknown.
* Use `**kwargs` when the number of keyword arguments is unknown.
* Choose meaningful names when appropriate (for example, `*numbers` or `**options`), although `args` and `kwargs` are the common convention.
* Don't overuse `*args` and `**kwargs` if a fixed function signature is clearer.
* Document expected keyword arguments when using `**kwargs` so others know what the function accepts.

---

# Common Interview Questions

1. What is the purpose of `*args`?
2. Why is `args` stored as a tuple instead of a list?
3. What is the difference between `*args` and `**kwargs`?
4. Why is `kwargs` stored as a dictionary?
5. What is the difference between packing and unpacking?
6. How does `student(**data)` work internally?
7. Can a function have both `*args` and `**kwargs`?
8. What is the correct parameter order in Python?
9. What happens if no values are passed to `*args` or `**kwargs`?
10. Can lists, tuples, and dictionaries all be unpacked?

---

# Practice Exercises

1. Write a function `sum_all(*numbers)` that returns the sum of all numbers.
2. Create a function `find_max(*numbers)` that returns the largest value.
3. Write a function `print_profile(**details)` that prints each key-value pair.
4. Pass a tuple to a function using `*`.
5. Pass a dictionary to a function using `**`.
6. Write a function that accepts a name, any number of marks using `*args`, and extra information like city or grade using `**kwargs`.

---

# Key Takeaways

* Variable-length arguments make functions **flexible** and **scalable**.
* `*args` collects **positional arguments** into a **tuple**.
* `**kwargs` collects **keyword arguments** into a **dictionary**.
* **Packing** combines many arguments into one tuple or dictionary.
* **Unpacking** expands a tuple, list, or dictionary into individual arguments.
* The same `*` and `**` syntax is used both in **function definitions** (to collect arguments) and in **function calls** (to expand arguments).
* Python enforces a specific **parameter order**: regular parameters → default parameters → `*args` → keyword-only parameters → `**kwargs`.
* Understanding `*args` and `**kwargs` is essential for writing reusable functions and is a frequent topic in Python interviews.
