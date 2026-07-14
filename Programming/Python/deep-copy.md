# `copy.deepcopy()` with Immutable vs Mutable Objects

## Key Idea

`deepcopy()` behaves differently for immutable and mutable objects.

* **Immutable objects** → returns the same object.
* **Mutable objects** → creates a completely new object.

---

# Example 1: Integer (`int`)

```python
import copy

a = 20
b = a

print(id(a), id(b), id(20))

d = copy.deepcopy(a)

print(id(a), id(d))

a = 30

print(a, b, d)
```

### Output (example)

```
11760392 11760392 11760392
11760392 11760392
30 20 20
```

### Observation

* `a`, `b`, and `d` initially point to the same integer object (`20`).
* `deepcopy(a)` does **not** create a new integer object.
* After `a = 30`, only `a` points to a different object.
* `b` and `d` still refer to `20`.

---

# Why?

`int` is **immutable**.

An immutable object cannot be modified after it is created.

When writing:

```python
a = 30
```

Python does **not** change the object `20`.

Instead:

* Creates (or reuses) the object `30`.
* Makes `a` refer to `30`.

The object `20` remains unchanged.

---

# Purpose of `deepcopy()`

`deepcopy()` is used to create independent copies of objects that contain mutable data.

For immutable objects, there is nothing to copy because they can never change.

Therefore:

```python
copy.deepcopy(20)
```

returns the same object.

---

# Why is this safe?

```python
a = 20
d = copy.deepcopy(a)

a = 50
```

The integer `20` never changes.

Only the variable `a` starts pointing to another integer (`50`).

So sharing the original object is completely safe.

---

# CPython Small Integer Caching

CPython caches integers from:

```python
-5 to 256
```

These integer objects are created once and reused.

Example:

```python
a = 20
b = 20

print(id(a))
print(id(b))
```

Output (usually):

```
Same id
```

Because both variables refer to the same cached integer object.

---

# Example 2: List (`list`)

```python
import copy

a = [1, 2, 3]

b = a
d = copy.deepcopy(a)

print(id(a))
print(id(b))
print(id(d))
```

### Output (example)

```
1000
1000
2000
```

### Observation

* `a` and `b` refer to the same list.
* `d` refers to a completely new list.

---

After:

```python
a.append(10)

print(a)
print(b)
print(d)
```

Output:

```
[1, 2, 3, 10]
[1, 2, 3, 10]
[1, 2, 3]
```

### Reason

* `b` points to the same list as `a`.
* `d` points to a separate copied list.

---

# Assignment vs Deep Copy

### Assignment

```python
a = [1, 2, 3]
b = a
```

```
a ─┐
   ▼
[1,2,3]
   ▲
b ─┘
```

Both variables share the same object.

---

### Deep Copy

```python
d = copy.deepcopy(a)
```

```
a ─► [1,2,3]

d ─► [1,2,3]
```

Now both are independent.

---

# Rule Used by `deepcopy()`

If the object is **immutable**:

* Returns the same object.

If the object is **mutable**:

* Creates a completely new object.
* Recursively copies nested mutable objects.

---

# Immutable Objects

These are immutable:

```python
int
float
bool
str
bytes
frozenset
None
tuple (only if all elements are immutable)
```

For these:

```python
copy.deepcopy(obj)
```

usually returns the same object.

---

# Mutable Objects

These are mutable:

```python
list
dict
set
bytearray
custom class objects (generally)
```

For these:

```python
copy.deepcopy(obj)
```

creates a brand-new object.

---

# Quick Comparison

| Operation            | New Object? | Nested Objects Copied? |
| -------------------- | ----------- | ---------------------- |
| Assignment (`b = a`) | ❌ No        | ❌ No                   |
| `list.copy()`        | ✅ Yes       | ❌ No (Shallow Copy)    |
| `copy.deepcopy()`    | ✅ Yes       | ✅ Yes (Deep Copy)      |

---

# Practice Example

```python
import copy

a = [10, 20]

b = a
c = a.copy()
d = copy.deepcopy(a)

print("a:", id(a))
print("b:", id(b))
print("c:", id(c))
print("d:", id(d))

a.append(30)

print(a)
print(b)
print(c)
print(d)
```

### Prediction

**IDs**

* `a` and `b` → Same
* `c` → Different
* `d` → Different

After:

```python
a.append(30)
```

Output:

```python
a = [10, 20, 30]
b = [10, 20, 30]
c = [10, 20]
d = [10, 20]
```

Reason:

* `b` shares the same list.
* `c` is a shallow copy.
* `d` is a deep copy.

Since the list contains only integers (immutable), `c` and `d` behave the same.

If the list contains nested mutable objects, their behavior becomes different.

---

# Nested List Example

```python
import copy

a = [[1, 2], [3, 4]]

c = a.copy()
d = copy.deepcopy(a)

a[0].append(100)

print(a)
print(c)
print(d)
```

Output:

```python
[[1, 2, 100], [3, 4]]
[[1, 2, 100], [3, 4]]
[[1, 2], [3, 4]]
```

### Reason

* `copy()` creates a new outer list but shares the inner lists.
* `deepcopy()` creates new outer and inner lists.
* Therefore, changes to nested lists affect the shallow copy but not the deep copy.

---

# Interview Question

**Q. Why does ****`copy.deepcopy(20)`**** return the same object?**

**Answer:**

`int` objects are immutable. Since they cannot be modified after creation, making another copy provides no benefit. Therefore, `copy.deepcopy()` returns the same object instead of creating a new one. Deep copying is mainly useful for mutable objects where independent copies are required.

---

# Summary

* `int` is immutable.
* Immutable objects cannot be modified.
* `deepcopy()` returns the same object for immutable types.
* Mutable objects can change.
* `deepcopy()` creates a completely independent copy for mutable objects.
* `list.copy()` performs a shallow copy.
* `deepcopy()` recursively copies nested mutable objects.
* For lists containing only immutable elements, `copy()` and `deepcopy()` appear to behave the same.
* The difference becomes visible when nested mutable objects (like lists or dictionaries) are present.
