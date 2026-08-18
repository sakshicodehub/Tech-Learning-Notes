
# Iterators & the Iterator Protocol

## Topics Covered

1. What is iteration?
2. Iterable vs Iterator
3. Why iterators exist
4. The Iterator Protocol (`__iter__` and `__next__`)
5. How `for` loops work internally
6. `iter()` and `next()`
7. `StopIteration`
8. Building a custom iterator
9. Memory analysis
10. Performance
11. Industry use cases
12. Best practices
13. Common mistakes
14. Interview questions
15. Coding exercises
16. Mini project

---

# 1. What is Iteration?

Iteration means **processing elements one by one**.

Example:

```python
numbers = [10, 20, 30]

for num in numbers:
    print(num)
```

Output:

```text
10
20
30
```

You already know how to use a `for` loop.

Now let's answer the important question:

> **How does Python know what the "next" element is?**

The answer is: **iterators**.

---

# 2. Why Do Iterators Exist?

Imagine a file with **100 GB** of data.

Without iterators, Python would have to load the whole file into memory.

That's impossible or extremely inefficient.

Instead:

```text
Read one line

↓

Process it

↓

Discard it

↓

Read the next line
```

This is exactly how iterators work.

They produce one item at a time.

This concept is called **lazy evaluation**.

**Iterators allow values to be produced/accessed one at a time instead of requiring all values to be available at once.**
---

# 3. Iterable vs Iterator

This is one of the most common interview questions.

## Iterable

An iterable is an object that **can produce an iterator**.
An iterable is an object that can return an iterator.

Examples:

```python
list
tuple
set
dict
str
range
```

Example:

```python
numbers = [1, 2, 3]
```

A list is **iterable**, not an iterator.

---

## Iterator

**The object returned by iter() is an iterator.**

An iterator is an object that:

* remembers its current position
* returns the next value when asked
* knows when iteration is finished

---

Think of it like this:

A **book** is iterable.

A **bookmark** is the iterator.

The book contains all the pages.

The bookmark remembers where you currently are.

---

# 4. The Iterator Protocol

An object is considered an iterator if it implements:

```python
__iter__()
```

and

```python
__next__()
```

These two methods form the **Iterator Protocol**.

---

## `__iter__()`

Returns the iterator object itself.
iter() gets an iterator from an iterable.

---

## `__next__()`

Returns the next item.

When there are no more items:

```python
raise StopIteration
```

---

# 5. `iter()`

Suppose:

```python
numbers = [10, 20, 30]
```

Create an iterator:

```python
it = iter(numbers)
```

Check its type:

```python
print(type(it))
```

Typical output:

```text
<class 'list_iterator'>
```

Notice:

The list itself is **not** the iterator.

`iter(numbers)` creates one.

---

# 6. `next()`

Now:

```python
print(next(it))
```

Output:

```text
10
```

Again:

```python
print(next(it))
```

Output:

```text
20
```

Again:

```python
print(next(it))
```

Output:

```text
30
```

Again:

```python
print(next(it))
```

Output:

```text
StopIteration
```

Python raises an exception because there are no more elements.

---

# Internal Memory

Initially:

```text
numbers

↓

[10,20,30]

iterator

↓

Current Position = 0
```

After first `next()`:

```text
Position = 1
```

After second:

```text
Position = 2
```

After third:

```text
Position = 3
```

After that:

```text
StopIteration
```

The iterator keeps track of the current position.

The list itself does not.

---

# 7. How Does a `for` Loop Work?

You write:

```python
for x in numbers:
    print(x)
```

Python roughly translates it to:

```python
it = iter(numbers)

while True:
    try:
        x = next(it)
        print(x)
    except StopIteration:
        break
```

This is one of the most important internal implementations to understand.

The `for` loop isn't magical—it repeatedly calls `next()` until it catches `StopIteration`.

---

# 8. Building Your Own Iterator

Let's create a simple iterator.

```python
class CountToThree:
    def __init__(self):
        self.current = 1

    def __iter__(self):
        return self

    def __next__(self):
        if self.current > 3:
            raise StopIteration

        value = self.current
        self.current += 1
        return value
```

Use it:

```python
counter = CountToThree()

for number in counter:
    print(number)
```

Output:

```text
1
2
3
```

Your object now behaves just like a list in a `for` loop because it follows the iterator protocol.

---

# 9. Iterator State

An important property of iterators is that they are **consumed**.

Example:

```python
numbers = [1, 2, 3]

it = iter(numbers)

print(next(it))
print(next(it))
```

Output:

```text
1
2
```

The iterator is now positioned at the third element.

If you loop over it:

```python
for x in it:
    print(x)
```

Output:

```text
3
```

Running the loop again prints nothing because the iterator has already been exhausted.

To iterate again, create a new iterator:

```python
it = iter(numbers)
```

---

# 10. Why Doesn't `range(10**12)` Use Huge Memory?

Consider:

```python
r = range(10**12)
```

`range` does **not** create one trillion integers.

It stores only enough information to calculate the next value:

* start
* stop
* step

When you ask for the next element, it computes it on demand.

This is another example of lazy evaluation.

---

# 11. Industry Examples

## Reading Large Files

```python
with open("large.log") as file:
    for line in file:
        process(line)
```

The file object is an iterator.

Only one line is in memory at a time.

---

## Database Cursors

Many database libraries return rows one by one.

```python
for row in cursor:
    process(row)
```

No need to load millions of rows into RAM.

---

## Streaming APIs

Large API responses are often processed incrementally using iterators.

---

## Machine Learning

Training datasets are often exposed as iterators to avoid loading the full dataset into memory.

---

# Memory Analysis

Suppose you have:

```python
numbers = list(range(1_000_000))
```

The list stores one million integers in memory.

An iterator over that list stores only:

* a reference to the list
* the current index

Additional memory usage is **O(1)**.

---

# Performance

Calling `next()` is generally **O(1)**.

A complete traversal of `n` elements is **O(n)**.

---

# Best Practices

* Use `for` loops unless you specifically need manual control with `next()`.
* Prefer iterators when working with large datasets.
* Don't convert iterators to lists unless necessary, as that removes their memory advantage.

---

# Common Mistakes

### Mistake 1

Confusing iterables with iterators.

```python
numbers = [1, 2, 3]

next(numbers)
```

This raises:

```text
TypeError
```

because a list is iterable, not an iterator.

Correct:

```python
it = iter(numbers)
next(it)
```

---

### Mistake 2

Forgetting that iterators are consumed.

Once exhausted, they don't automatically reset.

---

### Mistake 3

Catching all exceptions instead of specifically handling `StopIteration` when manually iterating.

---

# Interview Questions

1. What is the difference between an iterable and an iterator?
2. What methods define the iterator protocol?
3. What does `iter()` do?
4. What does `next()` do?
5. Why does `next([1, 2, 3])` fail?
6. How does a `for` loop work internally?
7. What is `StopIteration`?
8. Why is `range()` memory efficient?

---

# Coding Exercises

## Exercise 1

Create an iterator from:

```python
numbers = [5, 10, 15]
```

Use `next()` until `StopIteration` is raised.

---

## Exercise 2

Write a custom iterator that counts from 1 to 5.

---

## Exercise 3

Write a class `EvenNumbers` that iterates over even numbers from 2 to 20.

---

## Exercise 4

Without using a `for` loop, print all elements of a tuple using `iter()` and `next()`.

---

# Mini Project: Countdown Iterator

Create a class:

```python
class Countdown:
    ...
```

Requirements:

* Start from a given number.
* Each call to `next()` returns the next smaller number.
* Stop at `0` by raising `StopIteration`.

Example:

```python
countdown = Countdown(5)

for i in countdown:
    print(i)
```

Output:

```text
5
4
3
2
1
```

---

# Homework (Predict Without Running)

```python
numbers = [10, 20, 30]

it = iter(numbers)

print(type(numbers))
print(type(it))

print(next(it))
print(next(it))

for x in it:
    print(x)

try:
    print(next(it))
except StopIteration:
    print("Finished")
```

Answer these:

1. What is the complete output?
2. Why is `numbers` not an iterator?
3. Why does the `for` loop print only one value?
4. Why does the final `next(it)` raise `StopIteration`?
5. Draw the memory diagram showing the list and the iterator before and after each `next()` call.


----------------------------------------------------
### Iterable: An object from which Python can obtain an iterator by calling iter().

Examples:

list
tuple
str
dict
set
range

### Iterator: An object that implements:

__iter__()
__next__()

and maintains its current iteration state.

The important relationship is:

Iterable
   |
   | iter()
   ↓
Iterator
   |
   | next()
   ↓
Next value

> An iterator maintains the state/position necessary to determine the next value.


For a list iterator, that might effectively be an index.
For other iterators, the internal state can be completely different.


iter() returns an iterator for an iterable.

next(iterator):
Asks the iterator for its next value.
The iterator updates its internal state.
Returns that value.
If no value remains, raises StopIteration.

StopIteration is the exception raised by an iterator to signal that there are no more values to produce.




class Counter:

    def __init__(self):
        self.value = 1

    def __iter__(self):
        return self

    def __next__(self):
        if self.value > 3:
            raise StopIteration

        current = self.value
        self.value += 1
        return current


counter = Counter()

print(iter(counter) is counter)

print(next(counter))
print(next(counter))

for x in counter:
    print(x)


Tell me:

Complete output.
Why does iter(counter) is counter return True? no
What value does self.value have when the for loop starts? 1
Why does the for loop print only one number? because each time the constructore sent the value 1
What happens after the iterator reaches 3?