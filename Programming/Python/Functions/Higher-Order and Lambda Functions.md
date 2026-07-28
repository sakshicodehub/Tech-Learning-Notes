1. # Higher-Order Functions

A higher-order function is any function that:

accepts another function, or
returns another function.


## map()

## filter()

## sorted()
***sorted(iterable, key=None, reverse=False)**

*The key parameter expects a function that takes a single element and returns a value to sort by.*

### Works on Any Iterable:
 Unlike the .sort() method (which only works on lists in-place), sorted() can accept lists, tuples, dictionaries, sets, or strings.

numbers = [42, 1, 15, 8]
print(sorted(numbers)) //[42, 1, 15, 8]

letters = ['b', 'z', 'a']
print(sorted(letters, reverse=True)) // ['z', 'b', 'a']

### Custom Sorting with key:

Python calculates this "key" value exactly once per element to ensure fast execution.Using a built-in function (e.g., sorting strings by length):
words = ["watermelon", "fig", "banana"]
print(sorted(words, key=len)) // ['fig', 'banana', 'watermelon']


Comparison:             sorted() vs .sort()
Feature             sorted(iterable)    list.sort()
Return ValueA       new sorted list   None (modifies in-place)   
Original Object     Unchanged   Mutated directly
Allowed Types       Any iterable (list, set, tuple, etc.)   Only available for lists
Memory Efficiency   Requires extra memory for the new copy  More memory-efficient

## reduce()


2. # Lambda Functions

*A lambda is an anonymous function expression designed for short-lived behavior, usually passed to another function.*


## Lambda Syntax

General form:

 **lambda parameters: expression**

square = lambda x: x*x
square(5)

*Unlike def, a lambda does not need the return keyword.*


# Lambda vs def

Regular function:

def multiply(a, b):
    return a * b

Lambda:

multiply = lambda a, b: a * b

Functionally similar.

However, lambdas have important limitations.



# Lambda with map()

Suppose:

numbers = [1, 2, 3, 4]

Using def:

def square(x):
    return x * x

result = list(map(square, numbers))

print(result)

Output:

[1, 4, 9, 16]

Using lambda:

result = list(
    map(lambda x: x * x, numbers)
)

print(result)

Exactly the same output.

The lambda is useful because the function is needed only once.


# Lambda with filter()

Keep only even numbers.

numbers = [1,2,3,4,5,6]
evens = list(
    filter(
        lambda x: x % 2 == 0,
        numbers
    )
)

print(evens)

Output: [2,4,6]

# Lambda with sorted()

Students:

students = [
    ("Alice", 90),
    ("Bob", 70),
    ("Charlie", 85)
]

Sort by marks:

sorted_students = sorted(
    students,
    key=lambda student: student[1]
)

print(sorted_students)

Output:

[
 ('Bob',70),
 ('Charlie',85),
 ('Alice',90)
]

# Lambda with max()

Find the student with the highest marks.

best = max(
    students,
    key=lambda student: student[1]
)

print(best)

Output:

('Alice', 90)

# Lambda with min()
lowest = min(
    students,
    key=lambda student: student[1]
)

print(lowest)

Output:

('Bob', 70)


# When Should You Use Lambdas?

Good uses:

map()
filter()
sorted(key=...)
max(key=...)
min(key=...)
Short callbacks
Simple transformations


**Lambdas are called anonymous functions because they are defined without an identifier or formal name. Instead of using standard naming declarations (like def in Python or function in JavaScript), they are written inline to perform short, temporary tasks.**\



## Interview Questions
What is a lambda function?
Why are lambdas called anonymous functions?
What are the limitations of lambda functions?
When should you use a lambda instead of def?
Is there a performance difference between lambda and def?
Explain how sorted(key=...) uses a lambda internally.
Why can a lambda contain only one expression?