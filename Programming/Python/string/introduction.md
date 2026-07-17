# 📘 Strings

## 📌 What is a String?

A **string** is an **immutable sequence of Unicode characters**.

Let's understand this definition one word at a time.

### What does "sequence" mean?

A sequence is simply an **ordered collection of items**.

In a string, those items are characters.

```python
name = "Python"
```

Python stores it conceptually like this:

```text
P   y   t   h   o   n
0   1   2   3   4   5
```

Since the order is maintained, we can access characters using indexing and slicing.

---

### What does "Unicode characters" mean?

A character is not limited to English letters.

A string can contain:

* English letters
* Numbers
* Symbols
* Emojis
* Characters from different languages

Example:

```python
text = "Hello 😊 नमस्ते 中文"
```

Python can store all of these because it uses **Unicode**.

---

### What does "immutable" mean?

Immutable means **cannot be changed after creation**.

Example:

```python
word = "Python"
```

This is **not allowed**:

```python
word[0] = "J"
```

Python raises an error because strings cannot be modified character by character.

If you want a different string, Python creates a **new string object** instead of modifying the old one.

---

# 📌 ASCII vs Unicode

## What is ASCII?

ASCII was one of the earliest character encoding standards.

It contains only **128 characters**.

Examples:

```text
A  → 65
B  → 66
a  → 97
0  → 48
```

ASCII works well for English text.

---

## Why was ASCII not enough?

Computers became popular worldwide.

People needed to store characters like:

```text
ह
中
€
😊
```

ASCII has no codes for these characters.

So it became impossible to represent many languages using ASCII alone.

---

## What is Unicode?

Unicode solves this problem.

Instead of supporting only English, it gives **every character in every supported language a unique number** called a **code point**.

Examples:

```text
Character      Unicode

A              U+0041

ह              U+0939

😊              U+1F60A

中              U+4E2D
```

Think of Unicode as a giant dictionary.

Every character has a unique ID.

---

## Why do we need Unicode?

Without Unicode:

* Different computers could interpret the same data differently.
* Many languages could not be represented.
* Emojis would not work.

Unicode provides one universal standard for text across different operating systems and programming languages.

---

# 📌 Unicode vs UTF-8

This is one of the most commonly misunderstood topics.

## What is Unicode?

Unicode is **not** how characters are stored.

Unicode only assigns every character a unique number.

Think of it as a dictionary.

Example:

```text
😊

↓

Unicode

↓

U+1F60A
```

Unicode tells us:

> "This emoji's unique number is U+1F60A."

It does **not** tell us how to save it in memory or in a file.

---

## What is UTF-8?

UTF-8 is an **encoding format**.

Its job is to convert Unicode characters into bytes that computers can store and transmit.

Example:

```text
Character

A

↓

Unicode

U+0041

↓

UTF-8

41
```

Another example:

```text
Character

😊

↓

Unicode

U+1F60A

↓

UTF-8

F0 9F 98 8A
```

You do **not** need to memorize these byte values.

Just remember the difference.

---

### Easy way to remember

Imagine sending a letter.

Unicode is the person's **address**.

UTF-8 is the **envelope** used to deliver the letter.

Both are necessary, but they have different jobs.

---

# 📌 Why are Strings Immutable?

This is a common interview question.

Python intentionally makes strings immutable.

Once a string is created, its contents cannot be modified.

Instead, Python creates a new string whenever a change is needed.

---

## Why? — Reason 1: Hashability

Strings are often used as dictionary keys.

Example:

```python
student = {
    "name": "Alice"
}
```

Python calculates a hash value for `"name"`.

If strings could change later, the hash value would also change.

That would break dictionaries.

Making strings immutable guarantees that their hash value never changes.

---

## Why? — Reason 2: Memory Optimization

Python avoids creating duplicate copies of identical strings whenever possible.

Example:

```python
a = "Python"

b = "Python"
```

Python may allow both variables to reference the same string object.

If one variable could modify the string, the other variable would unexpectedly change too.

Immutability makes this optimization safe.

---

## Why? — Reason 3: Thread Safety

Large applications often run multiple threads at the same time.

If strings could be modified, two threads might try to change the same string simultaneously.

This could lead to inconsistent or corrupted data.

Since strings are immutable, multiple threads can safely read the same string without synchronization.

---

## Why? — Reason 4: Predictable Programs

When a function receives a string, the function knows that nobody can modify it accidentally.

Example:

```python
def greet(name):
    print(name)
```

If another part of the program also uses `name`, it will remain unchanged.

This makes debugging much easier.

---

# 📌 How does Python "change" a string?

Consider:

```python
text = "Python"

text = text.replace("P", "J")
```

It may look like the original string changed.

It didn't.

Python actually performs these steps:

```text
Original String

↓

Create a new string

↓

"Jython"

↓

Make 'text' point to the new string
```

The original `"Python"` object is never modified.

---

# 📌 Key Takeaways

* A string is an ordered sequence of Unicode characters.
* Strings are immutable after they are created.
* ASCII supports only 128 English characters.
* Unicode gives every character in every language a unique code point.
* UTF-8 converts Unicode characters into bytes for storage and transmission.
* Unicode is the dictionary; UTF-8 is the storage format.
* Python creates a new string whenever a modification is needed.
* String immutability makes Python programs faster, safer, and easier to maintain.

