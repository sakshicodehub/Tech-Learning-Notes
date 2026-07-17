## What are operators?
-> An operator is a symbol that performs an operation on one or more operands.
   An operator is syntactic sugar for invoking methods defined by objects.

Expression vs Statement
Expression -> Produces a value. 10+20 = 30
Statement -> Performs an action. x = 10, x < 20

## Arithmetic Operators

Operator	Meaning
+	    Addition
-	    Subtraction
*	    Multiplication
/	    Division
//	    Floor Division
%	    Modulus
**	    Exponent

Floor Division (//) -> This is not simply "remove decimal".

It returns the mathematical floor of the division result.

Example: print(7 // 2) -> 3

# print(-7 // 2)

Predict the output. Many beginners answer: -3

Wrong.

Actual: -4

Why?

Because Python performs floor, not truncation.

Number line:

-4  -3  -2  -1   0

      -3.5

The floor of -3.5 is -4.


Exponent (**)
print(2 ** 5) -> 32

Meaning: 2 × 2 × 2 × 2 × 2

## Assignment Operators (=)

This is not "store value in variable." A more accurate description is: Bind a name to an object.

Example: a = 100

Conceptually: Name "a" -> Integer Object 100

This distinction becomes important when we discuss mutability and object references.

## Augmented Assignment
x = x + 5  ->  x += 5

Other examples:
x -= 1
x *= 2
x /= 3
x //= 2
x %= 5
x **= 2


## Is += Faster?

Generally, it's more concise and can be more efficient for some mutable types because they may modify themselves in place.

For immutable types like int and str, a new object is still created.

Example:

x = 10

print(id(x))

x += 5

print(id(x))

The IDs will typically differ because integers are immutable.

## Unary Operators

## Operator Precedence
Operator	Priority
()	        Highest
**	        High
* / // %	Medium
+ -	        Lower
Comparisons	Lower
not	        Lower
and	        Lower
or	        Lowest

Internal Working

Time Complexity
Operation	Complexity
+(small ints)	O(1)
-	            O(1)
* (small ints)	O(1)
/	            O(1)
%	            O(1)
**	Depends on exponent and operand size
String +	    O(n + m)
String *	    O(n × k)

Comparison Operators

| Operator | Meaning               |
| -------- | --------------------- |
| `==`     | Equal                 |
| `!=`     | Not Equal             |
| `>`      | Greater Than          |
| `<`      | Less Than             |
| `>=`     | Greater Than or Equal |
| `<=`     | Less Than or Equal    |


# String Comparison
print("apple" == "apple")

↓

True

print("apple" > "banana")

↓

False

Why?

Strings are compared lexicographically (dictionary order), based on the Unicode value of each character.

print("cat" > "car")

Comparison:

c == c
a == a
t > r

Unicode values:

t → 116
r → 114

Since 116 > 114,

Output:

True


List Comparison
print([1, 2] == [1, 2])

↓

True

Python compares the elements one by one.

# Numbers inside strings

These are still compared as characters, not numeric values.

print("10" < "2")  //True

Comparison:

'1' (49)
'2' (50)

Since 49 < 50, "10" is considered smaller than "2".

Logical Operators

| Operator | Meaning                             |
| -------- | ----------------------------------- |
| `and`    | Both operands must be truthy        |
| `or`     | At least one operand must be truthy |
| `not`    | Negates the truth value             |


## Truthy & Falsy Values
These evaluate to False:

False
None
0
0.0
0j
''
""
[]
()
{}
set()
range(0)

Everything else is truthy.

## Short-Circuit Evaluation (and, or)

and

Example:

False and expensive_function()

Does Python call expensive_function()? No. Why?

False and anything is always False.

So Python stops immediately. This is called short-circuit evaluation.

or  ->  True or expensive_function() //True

and and or return one of their operands.

Examples: print(5 and 10)

↓

10

Both operands are truthy, so and returns the last evaluated operand.

print("Hello" or "World")

↓

Hello

The first operand is already truthy, so or returns it.

# Identity Operators (is, is not)

| Operator | Meaning            |
| -------- | ------------------ |
| `is`     | Same object?       |
| `is not` | Different objects? |


a = [1, 2]
b = [1, 2]

print(a == b) -> True

Values are equal.

print(a is b) -> False

Different list objects.

is checks identity directly and cannot be affected by custom equality implementations.



Membership Operators (in, not in)

| Operator | Meaning  |
| -------- | -------- |
| `in`     | Present? |
| `not in` | Absent?  |

print("Py" in "Python") -> True

Dictionaries

A common interview question:

person = {
    "name": "Sakshi",
    "age": 25
}

print("name" in person) -> True

Because in checks keys, not values.
To check values:

25 in person.values()

Performance of in

| Data Type       | Average Time                                                                                   |
| --------------- | ---------------------------------------------------------------------------------------------- |
| List            | O(n)                                                                                           |
| Tuple           | O(n)                                                                                           |
| String          | O(n) (substring search has optimized algorithms internally, but linear is a good mental model) |
| Set             | O(1) average                                                                                   |
| Dictionary Keys | O(1) average                                                                                   |


## Note: None is a Singleton: In Python, None is a distinct object type (NoneType) and guarantees a single instance in memory. Every variable assigned to None points to the exact same memory address.

Why should None be compared using is? in python

None should be compared using is because None is a singleton object in Python. There is only one instance of None, so is checks whether a variable refers to that exact object. Using == checks value equality, which can be overridden by a class's __eq__() method and may produce unexpected results. Therefore, is None is both the recommended (PEP 8) and safest way to check for None.

# Why is?
is checks object identity (whether two variables refer to the exact same object).
== checks value equality (whether two objects are considered equal).

Since there is only one None object, identity is exactly what you want.

x = None
y = None

print(x is y)   # True
print(x == y)   # True

Both are True, but is expresses the intended check more directly.

# Why == can be misleading

Some classes define their own equality behavior by implementing the __eq__() method.

Example:

class Weird:
    def __eq__(self, other):
        return True

w = Weird()

print(w == None)   # True
print(w is None)   # False


I use is None because None is a singleton in Python. is checks identity, while == checks equality, which can be overridden by user-defined classes. Using is is the correct and recommended way to test for None.