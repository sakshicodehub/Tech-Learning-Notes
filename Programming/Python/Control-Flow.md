What is Control Flow?

Control flow determines which statements execute and in what order.

Without control flow, a program executes line by line from top to bottom.

Syntax of if
if condition:
    # code blo

How Python Executes an if

Conceptually:

Evaluate condition
        │
        ▼
Is it truthy?
   │       │
 Yes       No
 │          │
Execute     Skip block

Python uses indentation to define blocks.

Why Did Python Choose Indentation?

Guido van Rossum (Python's creator) wanted code that is consistently readable. No braces. Less clutter. The structure is visible from indentation.

if...else

When you need two possible outcomes.

age = 16

if age >= 18:
    print("Adult")
else:
    print("Minor")

  age >= 18?
          │
      ┌───┴────┐
     Yes      No
      │         │
 Adult      Minor

### if...elif...else

Python checks conditions from top to bottom.

The first matching condition executes.

Remaining conditions are skipped.

## Nested if

An if inside another if.


### Early Return Pattern

Another professional pattern.

Instead of:

def login(user):
    if user:
        if user.is_active:
            return "Success"
    return "Failed"

Use:

def login(user):
    if not user:
        return "Failed"

    if not user.is_active:
        return "Failed"

    return "Success"

Each invalid case exits immediately.

This style is common in production code.



# Performance

if statements themselves are O(1).

The complexity depends on the condition.

Example:

if x > 10:

Comparison: O(1)

But:

if value in large_list:

Membership in a list: O(n)

Whereas:

if value in large_set:

Average: O(1)

Always think about the cost of the condition, not just the if.