# Python `input()` and `print()` 

## `input()`

`input()` is used to take input from the user.

**Syntax:**

```python
name = input("Enter your name: ")
```

**Example:**

```text
Enter your name: Sakshi
```

```python
name = "Sakshi"
```

### Why does `input()` always return a string?

No matter what the user types, `input()` returns it as a **string (`str`)**.

Example:

```python
age = input("Age: ")

print(age)
print(type(age))
```

**Input:**

```text
25
```

**Output:**

```text
25
<class 'str'>
```

Even though `25` looks like a number, Python stores it as a string.

---

## Type Conversion (Casting)

If you need a number, convert the string.

```python
age = int(input("Age: "))
```

Other common conversions:

```python
price = float(input("Price: "))
flag = bool(input("True/False: "))   # Rarely used with input()
```

> **Remember:** `input()` → String → Convert if needed.

---

# `print()`

`print()` is used to display output on the screen.

```python
print("Hello")
```

Output:

```text
Hello
```

---

## `sep` Parameter

`sep` specifies what should appear **between** multiple values.

Default:

```python
print("A", "B", "C")
```

Output:

```text
A B C
```

Custom separator:

```python
print("A", "B", "C", sep="-")
```

Output:

```text
A-B-C
```

Another example:

```python
print(2026, 7, 17, sep="/")
```

Output:

```text
2026/7/17
```

---

## `end` Parameter

`end` controls what is printed **after** the output.

By default:

```python
print("Hello")
print("World")
```

Output:

```text
Hello
World
```

Python automatically adds a newline (`\n`).

Custom `end`:

```python
print("Hello", end=" ")
print("World")
```

Output:

```text
Hello World
```

---

## `file` Parameter

Normally, `print()` writes to the console.

```python
print("Hello")
```

You can also write to a file.

```python
with open("output.txt", "w") as f:
    print("Hello", file=f)
```

Now the text is stored in `output.txt` instead of appearing on the screen.

---

## `flush` Parameter

Python usually stores output briefly (buffering) before displaying it.

Sometimes you want the output immediately.

```python
print("Loading...", flush=True)
```

Useful in:

* Progress bars
* Live logs
* Real-time command-line applications

---

# Input Validation

Never assume the user will enter the correct input.

### Bad Example

```python
age = int(input("Age: "))
```

If the user types:

```text
abc
```

Python raises:

```text
ValueError
```

because `"abc"` cannot be converted to an integer.

---

## Better Approach

Use `try` and `except`.

```python
while True:
    try:
        age = int(input("Age: "))
        break
    except ValueError:
        print("Please enter a valid integer.")
```

This keeps asking until the user enters a valid integer.

---

# Quick Interview Points

* `input()` **always returns a string**.
* Use **type casting** (`int()`, `float()`) when numeric input is needed.
* `print()` displays output to the console.
* `sep` changes the separator between multiple values.
* `end` changes what is printed after the output.
* `file` writes output to a file instead of the console.
* `flush=True` forces immediate output.
* Always validate user input using `try`/`except` to avoid runtime errors.
