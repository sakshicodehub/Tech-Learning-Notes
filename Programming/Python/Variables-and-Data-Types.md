variable:
    A variable in Python is a name that refers (or is bound) to a Python object. It does not store the object itself; it stores a reference to that object.

    Variable
    │
    ▼
    Reference
    │
    ▼
    Object

id() returns the unique identity of an object. In CPython, this identity is the object's memory address.
type() returns the class (type) of an object.

Dynamic Typing?
    In Python, variable types are determined at runtime. A variable can reference objects of different types during execution.

    x = 10
    x = "Hello"
    x = [1,2,3]

Strong Typing
    Python is strongly typed because it doesn't automatically convert incompatible types. "10" + 10 -> TypeError, need int("10") + 10

Assignment vs Copying
    assignment: a = [1,2], b = a,  
                a ─> [1,2] <─ b  same object.
    Copying: import copy
            b = copy.deepcopy(a)
            a ─► [1,2]
            b ─► [1,2]
            Different objects.

copy.deepcopy()
    deepcopy() behaves differently for immutable objects like integers than it does for mutable objects like lists or dictionaries.

how str is immutable?
    A string (str) is immutable because once a string object is created, its contents cannot be changed. Any operation that looks like it modifies a string actually creates a new string object.

    s = "hello"

    print(id(s))  //140217318437680

    s = s.upper()

    print(s)   //HELLO
    print(id(s))  //140217318439984


# Python Data Types

## What is a Data Type?

A data type defines:

* What kind of value an object stores.
* What operations can be performed on it.
* How Python stores it in memory.

### Built-in Primitive Types

```python
int
float
bool
complex
str
NoneType
```

---

# 1. Integer (`int`)

### Definition

* Stores **whole numbers** (no decimal point).

Examples:

```python
10
0
-25
999999999999999999
```

### Key Points

* Immutable.
* Supports **arbitrary precision** (no fixed size like C).
* Limited mainly by available memory.
* Larger integers use more memory.

Example:

```python
import sys

print(sys.getsizeof(10))
print(sys.getsizeof(10**100))
```

### Integer Caching (CPython)

* CPython caches integers from **-5 to 256**.
* These objects are created once and reused.

Example:

```python
a = 100
b = 100

print(a is b)   # Usually True
```

> **Note:** This does **not** mean Python only stores numbers between -5 and 256.

---

# 2. Float (`float`)

### Definition

* Stores numbers with a decimal point.

Examples:

```python
3.14
0.5
-8.2
```

### Key Points

* Immutable.
* Uses **IEEE 754 double-precision** format.
* Stored as a **binary approximation**, not an exact decimal value.

### Precision Issue

```python
print(0.1 + 0.2)
```

Output:

```text
0.30000000000000004
```

Reason:

* `0.1` and `0.2` cannot be represented exactly in binary.
* Python stores the closest possible value.

For financial calculations, use:

```python
from decimal import Decimal
```

---

# 3. Boolean (`bool`)

### Values

```python
True
False
```

### Key Points

* Immutable.
* `bool` is a **subclass of `int`**. because this design supports arithmetic and compatibility with numeric operations.

```python
True == 1
False == 0
```

Examples:

```python
print(True + True)      # 2
print(True * 10)        # 10
print(False + 100)      # 100
```

Conversion:

```python
int(True)    # 1
int(False)   # 0
```

---

# 4. Complex (`complex`)

### Definition

Stores complex numbers.

Example:

```python
z = 3 + 4j
```

* Real part → `3`
* Imaginary part → `4`

Access values:

```python
print(z.real)
print(z.imag)
```

Used mainly in:

* Scientific computing
* Signal processing
* Engineering

---

# 5. `None`

### Definition

Represents:

* No value
* Absence of a value
* Nothing to return

Example:

```python
x = None
```

### Common Uses

Default function argument:

```python
def greet(name=None):
    pass
```

Placeholder:

```python
result = None
```

### Type

```python
print(type(None))
```

Output:

```python
<class 'NoneType'>
```

### Comparing with `None`

Preferred:

```python
if value is None:
    ...
```

Not recommended:

```python
if value == None:
    ...
```

Reason:

* `None` is a **singleton** (only one `None` object exists).
* `is` checks object identity.

---

# `is` vs `==`

`==` → Checks if values are equal.

```python
5 == 5
```

`is` → Checks if both references point to the same object.

```python
a = None

print(a is None)
```

---

# Summary Table

| Type       | Mutable? | Example | Important Point        |
| ---------- | -------- | ------- | ---------------------- |
| `int`      | ❌        | `10`    | Arbitrary precision    |
| `float`    | ❌        | `3.14`  | Binary approximation   |
| `bool`     | ❌        | `True`  | Subclass of `int`      |
| `complex`  | ❌        | `3+4j`  | Real + Imaginary parts |
| `NoneType` | ❌        | `None`  | Represents no value    |

---

# Important Interview Points

* `int` in Python does **not** overflow like C's `int`.
* CPython caches integers from **-5 to 256**.
* `0.1 + 0.2` is not exactly `0.3` because floats are stored in binary.
* `bool` is a subclass of `int`.
* `True == 1` and `False == 0`.
* `None` means "no value."
* Use `is None`, not `== None`.

---

# Remember

* **int** → Whole numbers
* **float** → Decimal numbers (approximate)
* **bool** → `True` / `False`
* **complex** → `a + bj`
* **None** → No value

These notes are written in a concise, notebook-style format—easy to revise before interviews or while practicing.
