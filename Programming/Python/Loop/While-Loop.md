1. What is a while Loop?

A while loop repeatedly executes a block of code as long as a condition remains truthy.

General syntax:

while condition:
    # code

Example:

count = 1

while count <= 5:
    print(count)
    count += 1

Output:

1
2
3
4
5


How It Works Internally

Python repeatedly performs this cycle:

Evaluate condition
        │
        ▼
     Is it True?
      │      │
     Yes     No
      │       │
Execute body  Exit loop
      │
      └──────────────► Repeat



2. while vs for

This is a very common interview question.

Use for when:
You already know what to iterate over.
You're processing a list, tuple, string, dictionary, file, or range.

Example:

for student in students:
    print(student)

Use while when:
The number of iterations is unknown.
The loop depends on a condition changing.

Example:

password = ""

while password != "admin123":
    password = input("Enter password: ")

The loop continues until the correct password is entered.


3. Infinite Loops

Example:

while True:
    print("Running...")

This loop never stops on its own.

Why use an infinite loop?

Many real systems are designed to run continuously:

Web servers
Chat servers
Background workers
Event listeners
IoT devices

They rely on an infinite loop and exit only when explicitly instructed.

Example:

while True:
    command = input("> ")

    if command == "exit":
        break

4. break

break immediately exits the nearest enclosing loop.

Example:

number = 1

while True:
    if number == 5:
        break

    print(number)
    number += 1

Output:

1
2
3
4

When number becomes 5, the loop terminates immediately.

5. continue

continue skips the rest of the current iteration and proceeds to the next one.

Example:

for i in range(1, 6):
    if i == 3:
        continue

    print(i)

Output:

1
2
4
5

Notice that only the current iteration (3) is skipped.

Difference Between break and continue

break:

1
2
STOP

continue:

1
2
(skip)
4
5

6. pass

pass does absolutely nothing.

It is simply a placeholder.

Example:

if True:
    pass

Without pass, Python would raise an IndentationError because the block cannot be empty.


7. Loop else

One of Python's unique features.

Syntax:

while condition:
    ...
else:
    ...


8. Nested Loops

A loop inside another loop.

Example:

for row in range(3):
    for col in range(3):
        print(row, col)

