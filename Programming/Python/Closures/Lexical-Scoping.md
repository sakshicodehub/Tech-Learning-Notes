This is one of the most important concepts in programming. The idea is called **Lexical Scoping (Static Scoping)**,
 and most modern languages (Python, JavaScript, Java, C#, Swift, Go, Rust, Kotlin, etc.) use it.

---

# What is Lexical Scoping?

**Lexical** means **the place where the code is written**.

A function can access variables based on **where it was defined**, **not where it is called**.

Think of variables as being searched outward from the function.

```
Current Function
      │
      ▼
Local variables
      │
      ▼
Parent Function (Enclosing)
      │
      ▼
Global variables
      │
      ▼
Built-in variables/functions
```

Python calls this the **LEGB Rule**:

```
L → Local
E → Enclosing
G → Global
B → Built-in
```

---

# Example 1 (Python)

```python
x = 100

def outer():
    x = 10

    def inner():
        print(x)

    inner()

outer()
```

Output

```
10
```

### Memory Diagram

```
Global Scope
──────────────
x = 100

outer()
│
│
├── x = 10
│
└── inner()
      │
      └── print(x)
             ↑
             finds x = 10
```

Search order:

```
inner()

Local?
❌ No x

↓

Enclosing?
✅ x = 10

↓

Done
```

It never reaches the global variable.

---

# Example 2

```python
x = 100

def outer():

    def inner():
        print(x)

    inner()

outer()
```

Output

```
100
```

Diagram

```
Global
────────────
x = 100

outer()

    inner()

        print(x)
             │
             ▼

Local?
❌

Enclosing?
❌

Global?
✅ 100
```

---

# Example 3

```python
def outer():
    x = 10

    def inner():
        x = 20
        print(x)

    inner()
    print(x)

outer()
```

Output

```
20
10
```

Diagram

```
outer()

x = 10

    inner()

    x = 20
    print(x)

After inner()

print(x)
```

Search

```
Inside inner()

Local?
✅ x = 20

Stop.
```

Outside:

```
outer()

x = 10
```

The two variables are different because they are in different scopes.

---

# Closures

Now look carefully.

```python
def outer():

    x = 10

    def inner():
        print(x)

    return inner

f = outer()

f()
```

Output

```
10
```

People often ask:

> `outer()` finished. Shouldn't `x` be destroyed?

Normally yes.

But because `inner()` still needs it, Python **keeps the enclosing scope alive**.

Diagram

```
outer()

x = 10

inner()
  │
  └───────┐
          │
return inner
          │
          ▼

f -----> inner

        x = 10   (still stored)
```

This is called a **closure**.

A closure is:

> A function together with the variables from its enclosing scope that it still uses.

---

# JavaScript works exactly the same

```javascript
function outer() {
    let x = 10;

    function inner() {
        console.log(x);
    }

    return inner;
}

const f = outer();

f();
```

Output

```
10
```

Diagram

```
Global

outer()

x = 10

return inner

↓

f()

↓

inner()

↓

x = 10
```

JavaScript also creates a **closure**.

---

# Another JavaScript Example

```javascript
let x = 100;

function outer() {

    let x = 10;

    function inner() {
        console.log(x);
    }

    inner();
}

outer();
```

Output

```
10
```

Search

```
inner()

Local?
❌

Parent?
✅ x = 10

Stop.
```

---

# Why is it called "Lexical"?

Consider:

```python
def outer():
    x = 10

    def inner():
        print(x)

    return inner
```

Where is `inner()` **written**?

```
outer

│
├── x = 10
│
└── inner
```

Since `inner` is **written inside `outer`**, Python permanently records:

> "My parent scope is `outer`."

It doesn't matter where `inner()` is called later.

Even if you do:

```python
f = outer()

# many lines later...

f()
```

`inner` still knows:

```
My parent = outer
```

That is **lexical (static) scoping**.

---

# Dynamic Scoping (How it would work)

Imagine a language with **dynamic scoping** (Python and JavaScript do **not** use this).

```text
x = 100

outer()
    x = 10

another()
    inner()
```

With **lexical scoping**:

```
inner()

↓

Looks where it was defined

↓

Uses x = 10
```

With **dynamic scoping**:

```
inner()

↓

Looks at who called me

↓

Maybe x = 100
Maybe another x

Depends on runtime
```

So:

* **Lexical scoping:** "Where was I **defined**?" ✅ (Python, JavaScript, Java, C#, Go, Rust, Swift, Kotlin)
* **Dynamic scoping:** "Who **called** me?" ❌ (rare today; seen in some older or specialized languages)

---

# Visual Summary

```
                LEGB Variable Lookup

                inner()

                   │
                   ▼
        +-------------------+
        | Local             |
        +-------------------+
                 │
          not found
                 ▼
        +-------------------+
        | Enclosing         |
        | (parent function) |
        +-------------------+
                 │
          not found
                 ▼
        +-------------------+
        | Global            |
        +-------------------+
                 │
          not found
                 ▼
        +-------------------+
        | Built-in          |
        | print(), len()... |
        +-------------------+
```

A **closure** is simply a function that remembers variables from its **enclosing (parent) scope**, even after that parent function has finished executing. This behavior is a direct result of lexical scoping.
