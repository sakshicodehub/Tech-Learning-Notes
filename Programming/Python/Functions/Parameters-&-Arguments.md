# 📘 Python Function Arguments — Human-Written Notes

---

# 1. Parameters vs Arguments

This is one of the most common Python interview questions.

Although people often use these terms interchangeably, they mean different things.

## Parameter

A **parameter** is a variable listed in the function definition (also called the function signature). It acts as a placeholder that will receive a value when the function is called.

```python
def greet(name):
    print(f"Hello {name}")
```

Here:

* `name` is a **parameter**.

Think of a parameter as an **empty box** waiting to receive a value.

---

## Argument

An **argument** is the actual value you pass to a function when calling it.

```python
greet("Sakshi")
```

Here:

* `"Sakshi"` is the **argument**.

The argument fills the parameter with a value.

Conceptually:

```text
Function Definition
-------------------
Parameter
   │
   ▼
def greet(name):

Function Call
-------------
Argument
   │
   ▼
greet("Sakshi")
```

Inside the function, Python performs:

```python
name = "Sakshi"
```

---

## Easy Way to Remember

| Parameter                         | Argument                        |
| --------------------------------- | ------------------------------- |
| Defined in the function           | Passed during the function call |
| Placeholder                       | Actual value                    |
| Exists in the function definition | Exists in the function call     |

---

# 2. Positional Arguments

By default, Python matches arguments to parameters **by their position**, from left to right.

Example:

```python
def introduce(name, age):
    print(f"My name is {name}")
    print(f"My age is {age}")

introduce("Sakshi", 24)
```

Matching:

```text
Parameter        Argument

name      ←      "Sakshi"

age       ←      24
```

Output:

```text
My name is Sakshi
My age is 24
```

---

## How Python Matches Them

Python simply pairs the first argument with the first parameter, the second argument with the second parameter, and so on.

It does **not** understand the meaning of the values.

---

## Wrong Order

```python
introduce(24, "Sakshi")
```

Output:

```text
My name is 24
My age is Sakshi
```

No error occurs because Python only checks the **position**, not whether the values make logical sense.

---

## Rule

For positional arguments:

```text
1st argument  → 1st parameter
2nd argument  → 2nd parameter
3rd argument  → 3rd parameter
...
```

---

# 3. Keyword Arguments

Instead of relying on position, you can specify the parameter name while calling the function.

Example:

```python
def introduce(name, age):
    print(f"My name is {name}")
    print(f"My age is {age}")

introduce(age=24, name="Sakshi")
```

Output:

```text
My name is Sakshi
My age is 24
```

Notice that the order no longer matters because each value is attached to a parameter by name.

---

## Why Use Keyword Arguments?

Imagine a function with many parameters.

```python
create_user("Rahul", 23, "Delhi", True, "Developer")
```

Looking at the function call alone, it is difficult to know what each value represents.

Using keyword arguments makes the code much clearer.

```python
create_user(
    name="Rahul",
    age=23,
    city="Delhi",
    is_active=True,
    profession="Developer"
)
```

Now every value is self-explanatory.

---

## Benefits of Keyword Arguments

* Improves readability.
* Avoids mistakes caused by incorrect ordering.
* Makes long function calls easier to understand.
* Allows arguments in any order.

---

# 4. Default Arguments

Sometimes a parameter usually has the same value.

Instead of forcing the caller to provide it every time, you can assign a **default value**.

Example:

```python
def greet(name, country="India"):
    print(f"{name} from {country}")
```

Here:

* `country` has the default value `"India"`.

---

## Using the Default Value

```python
greet("Sakshi")
```

Output:

```text
Sakshi from India
```

Since no value was given for `country`, Python automatically used the default.

---

## Overriding the Default

```python
greet("Alice", "USA")
```

Output:

```text
Alice from USA
```

Whenever you supply an argument, it replaces the default value.

---

## Rules for Default Arguments

Default parameters must come **after** non-default parameters.

✅ Correct

```python
def greet(name, country="India"):
    pass
```

❌ Incorrect

```python
def greet(country="India", name):
    pass
```

This causes a `SyntaxError` because Python cannot determine which values belong to which parameters.

---

# 5. Mixing Positional and Keyword Arguments

Python allows positional and keyword arguments in the same function call.

Example:

```python
def student(name, age, city):
    print(name, age, city)

student("Sakshi", age=24, city="Indore")
```

Output:

```text
Sakshi 24 Indore
```

Python processes:

```text
"Sakshi"  → name
age=24    → age
city="Indore" → city
```

---

## Rule

All positional arguments must come **before** keyword arguments.

✅ Correct

```python
student("Sakshi", age=24, city="Indore")
```

❌ Incorrect

```python
student(name="Sakshi", 24, "Indore")
```

This raises:

```text
SyntaxError
```

because positional arguments cannot appear after keyword arguments.

---

# 6. Mutable Default Argument Pitfall

This is one of Python's most famous interview questions.

Consider this function:

```python
def add_item(item, items=[]):
    items.append(item)
    return items
```

Now call it twice:

```python
print(add_item("A"))
print(add_item("B"))
```

Many beginners expect:

```text
['A']
['B']
```

Actual output:

```text
['A']
['A', 'B']
```

---

## Why Does This Happen?

Most people think the empty list `[]` is created every time the function is called.

It is **not**.

Python creates the default list **once**, when the function is defined.

Every future call that doesn't provide `items` uses that **same list object**.

Conceptually:

```text
Function Definition Time

items
 │
 ▼
[]

Call 1
append("A")

↓

["A"]

Call 2
append("B")

↓

["A", "B"]
```

The list keeps growing because each call shares the same object.

---

## Important Rule to Remember

✅ Function scope (stack frame) is created every time the function is called.

✅ Local variables are created fresh for every function call.

⚠️ Default argument values are created **only once**, when the function is defined.

---

# Correct Approach

Use `None` as the default value.

```python
def add_item(item, items=None):

    if items is None:
        items = []

    items.append(item)
    return items
```

Now:

```python
print(add_item("A"))
print(add_item("B"))
```

Output:

```text
['A']
['B']
```

Each function call creates a brand-new list.

---

## Why `None`?

`None` is immutable and serves as a safe placeholder. Inside the function, you decide when to create a new mutable object such as a list or dictionary.

This pattern is considered the Pythonic way to handle mutable defaults.

---

# 7. How Python Stores Default Arguments Internally

When Python executes:

```python
def greet(name, country="India"):
    ...
```

it creates a **function object**.

Conceptually, that object stores:

```text
Function Object
│
├── Name: greet
├── Parameters
│      ├── name
│      └── country
├── Default Values
│      └── "India"
├── Code Object
└── Metadata
```

The important point is that the default value (`"India"`) is evaluated and stored **once**, at the time the function is defined—not each time it is called.

---

# 8. Best Practices

* Use clear, descriptive parameter names.
* Keep functions focused on a single task.
* Prefer `return` over `print()` when the result will be reused.
* Use keyword arguments when a function has many parameters.
* Place default parameters after required parameters.
* Never use mutable objects (like `[]`, `{}`, or `set()`) as default argument values.
* Use `None` as the default for mutable data and create a new object inside the function.
* Write small, reusable functions instead of long, complex ones.

---

# Common Interview Questions

1. What is the difference between a parameter and an argument?
2. What are positional arguments?
3. What are keyword arguments, and why are they useful?
4. What are default arguments?
5. Why can't positional arguments come after keyword arguments?
6. Why are mutable default arguments dangerous?
7. Why does `def func(items=[]):` behave unexpectedly?
8. Why is `None` used as the default value for lists and dictionaries?
9. When are default argument values evaluated in Python?
10. Can you mix positional and keyword arguments in the same function call?

---

# Practice Exercises

1. Write a function `multiply(a, b)` that returns the product of two numbers.
2. Create a function `introduce(name, age, city)` and call it using both positional and keyword arguments.
3. Write a function `greet(name, country="India")` and test it with and without passing the country.
4. Create a function `calculate(price, tax=18)` that returns the final price including tax.
5. Write a function that accepts a list of numbers and returns the largest number.
6. Fix a function that incorrectly uses a mutable default argument.

---

# Mini Project Idea

## Student Information System

Create functions to:

* Add a student.
* Display student details.
* Update a student's city.
* Calculate the average marks.
* Search for a student by name.
* Use default arguments where appropriate (for example, a default city or default grade).
* Avoid mutable default arguments when storing student data.

This project reinforces positional arguments, keyword arguments, default values, function returns, and writing reusable code.

---

# Key Takeaways

* **Parameters** are placeholders in the function definition; **arguments** are the actual values passed during a function call.
* **Positional arguments** are matched by order, while **keyword arguments** are matched by parameter name.
* **Default arguments** provide fallback values when an argument is omitted.
* Always place **positional arguments before keyword arguments** in a function call.
* **Default parameter values are evaluated once**, when the function is defined.
* Avoid using **mutable objects** as default values; use `None` and create a new object inside the function instead.
* Understanding argument types and default values is essential for writing clean, reliable, and interview-ready Python code.
