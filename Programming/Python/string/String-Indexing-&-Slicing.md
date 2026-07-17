# 📘 String Indexing & Slicing

## 💡 Big Picture

A **string** in Python is simply an ordered collection (sequence) of characters.

```python
name = "PYTHON"
```

Conceptually, Python stores it like this:

```text
Character :   P   Y   T   H   O   N

Positive :    0   1   2   3   4   5

Negative :   -6  -5  -4  -3  -2  -1
```

### 📝 Remember

Every character has **two addresses**:

* Positive index → Count from the left.
* Negative index → Count from the right.

I usually use **negative indexing** when I need characters from the end because it's cleaner than writing `len(string)-1`.

Example:

```python
word = "Python"

word[0]      # P
word[3]      # h
word[-1]     # n
word[-2]     # o
```

---

# 📌 Why is indexing O(1)?

When Python accesses

```python
word[3]
```

it doesn't check every character one by one.

Conceptually, it calculates something like:

```
Memory Address + (Index × Character Size)
```

(Internally it's more complex because Python stores Unicode characters, but this is the basic idea.)

That's why indexing is **constant time (O(1))**.

---

# 📌 What is Slicing?

Slicing means taking a **portion of a string**.

Syntax:

```python
string[start:stop]
```

### Rule to remember

✅ Start index is included.

❌ Stop index is excluded.

Example:

```python
word = "Python"

word[0:2]
```

Output:

```text
Py
```

Python includes:

```
0
1
```

but **stops before index 2**.

---

# 📌 Why is the stop index excluded?

This is one of Python's best design decisions.

Suppose I want the first four characters.

Instead of thinking:

```
0
1
2
3
```

I can simply write:

```python
word[:4]
```

The stop index also makes it easy to calculate lengths.

```
Length = stop - start
```

Example:

```python
word[2:5]
```

```
5 - 2 = 3 characters
```

Simple and predictable.

---

# 📌 Omitting start and stop

Python automatically fills in missing values.

```python
word[:]
```

means

```
Take everything.
```

```python
word[:4]
```

means

```
Start from index 0.
```

```python
word[2:]
```

means

```
Go until the end.
```

I don't need to write `len(word)` every time.

---

# 📌 Step Parameter

Syntax:

```python
string[start:stop:step]
```

Meaning:

* **start** → Where to begin.
* **stop** → Where to stop (exclusive).
* **step** → How many positions to move each time.

Example:

```python
word = "Python"

word[::2]
```

Python visits:

```
0 → 2 → 4
```

Output:

```text
Pto
```

Another example:

```python
word[1::2]
```

Python visits:

```
1 → 3 → 5
```

Output:

```text
yhn
```

---

# 📌 Reversing a String

One of the most common Python tricks:

```python
word = "Python"

word[::-1]
```

Output:

```text
nohtyP
```

### How does this work?

```python
word[start:stop:-1]
```

Since start and stop are omitted:

Python assumes

```
Start = last character

Stop = before the first character

Step = -1
```

A negative step simply means:

> Move backwards.

So Python reads:

```
n
o
h
t
y
P
```

---

# 📌 Common slicing examples

```python
text = "Python"

text[:]        # Entire string

text[::1]      # Entire string (forward)

text[::-1]     # Reverse

text[::2]      # Every second character

text[::-2]     # Every second character backwards

text[2:]       # From index 2 to the end

text[:4]       # First four characters

text[1:5:2]    # Every second character between 1 and 4
```

---

# 📌 Understanding Negative Indexing in Slicing

Example:

```python
text = "Hello"

print(text[-3:5])
```

Python first converts the negative index.

Length of `"Hello"` = 5

```
-3

↓

5 + (-3)

↓

2
```

So Python actually sees:

```python
text[2:5]
```

Output:

```text
llo
```

A useful formula:

```
Positive Index = Length + Negative Index
```

Example:

```
Length = 5

-1 = 4

-2 = 3

-3 = 2
```

---

# 📌 Why does `"*" * 12` work?

Example:

```python
masked = "*" * 12
```

Output:

```text
************
```

Many beginners think Python converts `12` into a string.

It doesn't.

No implicit conversion happens here.

Instead, Python calls a special method behind the scenes.

Conceptually:

```python
"*".__mul__(12)
```

The `str` class defines `__mul__()` to mean:

> Repeat this string **n** times.

So Python creates twelve `"*"` characters and joins them together.

---

# 📌 Operator Overloading

One thing I found interesting is that operators don't always mean the same thing.

Their behavior depends on the object.

Examples:

```python
5 * 3
```

means

```
Multiply numbers.
```

But

```python
"Hi" * 3
```

means

```
Repeat the string.
```

Python allows this through **Operator Overloading**.

Each class can define what operators should do.

---

# 📌 Common Magic (Dunder) Methods

Python internally translates many operators and built-in functions into special methods.

| Expression  | Internal Call         |
| ----------- | --------------------- |
| `a + b`     | `a.__add__(b)`        |
| `a * b`     | `a.__mul__(b)`        |
| `len(text)` | `text.__len__()`      |
| `text[2]`   | `text.__getitem__(2)` |

Example:

```python
len("Hello")
```

Conceptually becomes

```python
"Hello".__len__()
```

Similarly,

```python
text[2]
```

becomes

```python
text.__getitem__(2)
```

This is why different Python objects can customize how operators behave.

---

# 📌 Key Takeaways

* A string is an ordered sequence of characters.
* Every character has both a positive and a negative index.
* Indexing is **O(1)** because Python can directly access the requested position.
* Slicing always creates a **new string** because strings are immutable.
* Start index is included, stop index is excluded.
* Omitting start or stop lets Python use sensible defaults.
* A negative step means traverse the string backwards.
* `[::-1]` is the easiest way to reverse a string.
* Negative indexes are converted using:

```
Positive Index = Length + Negative Index
```

* `"*" * 12` works because the string class implements the `__mul__()` method.
* Python uses **operator overloading**, not implicit type conversion, for operations like string repetition.
