# Python Iteration Notes

## 1. What is Iteration?

Iteration means **accessing elements one by one from a collection until all elements are completed**.

Example:

```python
for item in collection:
    print(item)
```

A `for` loop does not know the size of the collection in advance. It simply keeps asking for the **next element** until there are no more elements.

---

## Why does Python use this style?

In most cases, we need the **actual value**, not the position (index).

### Less Pythonic way:

```python
names = ["Alice", "Bob", "Charlie"]

for i in range(len(names)):
    print(names[i])
```

Here we are manually managing indexes.

### Pythonic way:

```python
for name in names:
    print(name)
```

This code is:

* Easier to read
* Less error-prone
* Works with many types of collections

---

## Strings are Iterable

A string is also a collection of characters, so we can loop through it.

Example:

```python
word = "Python"

for ch in word:
    print(ch)
```

Output:

```
P
y
t
h
o
n
```

---

# 2. What is an Iterable?

An **iterable** is an object that can give its elements one by one.

Common examples:

* List
* Tuple
* String
* Dictionary
* Set
* Range
* File objects

Example:

```python
numbers = [10, 20, 30]

for num in numbers:
    print(num)
```

The list is iterable because Python can get each element from it.

An iterable object provides the method:

```python
__iter__()
```

which creates an iterator.

---

# 3. What is an Iterator?

An **iterator** is an object that remembers its current position while iterating.

Example:

```python
numbers = [10, 20, 30]

iterator = iter(numbers)

print(next(iterator))
print(next(iterator))
print(next(iterator))
```

Output:

```
10
20
30
```

The iterator remembers where it is:

```
10  →  20  →  30  →  Stop
```

When there are no more values, Python raises:

```
StopIteration
```

Example:

```python
numbers = [10, 20, 30]

iterator = iter(numbers)

while True:
    try:
        item = next(iterator)
        print(item)
    except StopIteration:
        break
```

---

# 4. How Does a for Loop Work Internally?

When we write:

```python
for item in numbers:
    print(item)
```

Python internally does something similar:

```python
iterator = iter(numbers)

while True:
    try:
        item = next(iterator)
        print(item)
    except StopIteration:
        break
```

A `for` loop uses three main things:

1. `iter()`

   * Creates an iterator from an iterable.

2. `next()`

   * Gets the next element from the iterator.

3. `StopIteration`

   * Tells Python that iteration is finished.

---

# 5. range()

`range()` creates a range object.

Example:

```python
print(range(5))
```

Output:

```
range(0, 5)
```

It does **not** create:

```
[0, 1, 2, 3, 4]
```

---

## Types of range()

### Only stop value:

```python
range(5)
```

Gives:

```
0,1,2,3,4
```

### Start and stop:

```python
range(2, 6)
```

Gives:

```
2,3,4,5
```

### Start, stop, step:

```python
range(1, 10, 2)
```

Gives:

```
1,3,5,7,9
```

---

## Why is range() memory efficient?

If we write:

```python
range(1000000)
```

Python does not store one million numbers.

It only stores:

```
start
stop
step
```

Example:

```
start = 0
stop = 1000000
step = 1
```

When Python needs the next number, it calculates it.

Memory usage:

```
O(1)
```

That is why `range()` works efficiently even with large values.

---

# 6. enumerate()

Sometimes we need both:

* Index
* Value

Example:

```python
names = ["Alice", "Bob"]

for index, name in enumerate(names):
    print(index, name)
```

Output:

```
0 Alice
1 Bob
```

Without enumerate:

```python
for i in range(len(names)):
    print(i, names[i])
```

`enumerate()` is cleaner.

---

## Starting index with enumerate()

By default indexing starts from 0.

We can change it:

```python
for index, name in enumerate(names, start=1):
    print(index, name)
```

Output:

```
1 Alice
2 Bob
```

---

# 7. zip()

`zip()` combines multiple iterables together.

Example:

```python
names = ["Alice", "Bob"]
ages = [25, 30]

for name, age in zip(names, ages):
    print(name, age)
```

Output:

```
Alice 25
Bob 30
```

Behind the scenes:

```
Alice → 25
Bob   → 30
```

`zip()` stops when the shortest iterable ends.

Example:

```python
names = ["Alice", "Bob", "Charlie"]
ages = [25, 30]

zip(names, ages)
```

Result:

```
Alice 25
Bob 30
```

Charlie is ignored because there is no matching age.

---

# Time Complexity

| Operation         | Time Complexity |
| ----------------- | --------------- |
| Loop through list | O(n)            |
| range() iteration | O(n)            |
| enumerate()       | O(n)            |
| zip()             | O(min(n, m))    |

---

# Interview Summary

* **Iterable:** An object that can return elements one by one using `__iter__()`.
* **Iterator:** An object that remembers its current position and provides values using `__next__()`.
* A `for` loop internally uses `iter()`, `next()`, and handles `StopIteration`.
* `range()` is memory efficient because it stores only start, stop, and step values.
* `enumerate()` gives both index and value.
* `zip()` combines multiple iterables and stops at the shortest one.

**Python's iteration system is based on the Iterator Protocol (`__iter__()` and `__next__()`).**
