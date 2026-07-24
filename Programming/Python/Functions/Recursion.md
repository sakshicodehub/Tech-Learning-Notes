1. # What is Recursion?
Beginner Definition

A function calling itself.

Example:

def hello():
    hello()

Yes, this is recursion—but it's broken recursion because it never stops.

## Professional Definition

Recursion is a technique where a problem is solved by reducing it into smaller instances of the same problem until a terminating condition (base case) is reached.

The important idea is problem reduction, not just "calling itself."



2. # Why Do We Need Recursion?

Imagine climbing a staircase.

Instead of thinking:

"How do I climb 100 stairs?"

Think:

"How do I climb one stair, then solve the remaining 99 stairs the same way?"

This is recursive thinking:

Problem(100)

↓

Problem(99)

↓

Problem(98)

↓

...

↓

Problem(1)

↓

Done

Large problem → Smaller version of the same problem.



3. # Two Essential Parts of Every Recursive Function

Every correct recursive function has:

## Base Case

The stopping condition.

## Recursive Case

The step that reduces the problem.

Without either one, recursion fails.

example:
def countdown(number):
    if number == 0 :
        print("Done!")
        return;
    
    print(number);
    countdown(number - 1)

countdown(5)

5. # What Happens Internally?

This is where interviews become interesting.

Let's trace:

factorial(4)

# Python(all language) creates a new stack frame for every function call.

Step 1
factorial(4)

↓

needs factorial(3)
Step 2
factorial(4)

↓

factorial(3)

↓

needs factorial(2)
Step 3
factorial(4)

↓

factorial(3)

↓

factorial(2)

↓

needs factorial(1)
Step 4
factorial(1)

↓

needs factorial(0)
Base Case
factorial(0)

↓

returns 1

Now the stack starts unwinding.

factorial(1)

↓

1 × 1 = 1
factorial(2)

↓

2 × 1 = 2
factorial(3)

↓

3 × 2 = 6
factorial(4)

↓

4 × 6 = 24

Final answer:

24


# Visualizing the Call Stack

During execution:

+----------------------+
| factorial(4)         |
+----------------------+
| factorial(3)         |
+----------------------+
| factorial(2)         |
+----------------------+
| factorial(1)         |
+----------------------+
| factorial(0)         |
+----------------------+

After the base case, frames are popped in reverse order.

This is Last In, First Out (LIFO)—the behavior of a stack.


6. # Stack Frames

Each function call gets its own stack frame.

A frame stores:

Local variables
Parameters
Return address
Execution state

Example:

def square(x):
    y = x * x
    return y

Each call has a separate x and y.

Calling:

square(5)
square(10)

creates two different stack frames.


7. # Memory Analysis

Each recursive call creates a new stack frame.

If recursion depth is n:

# Space Complexity:

# O(n)

Example:

factorial(1000)

creates about 1000 stack frames.
    


# Python Recursion Limit

Python intentionally limits recursion depth.

import sys

print(sys.getrecursionlimit())

Typical output:

1000

If you exceed it:

RecursionError:
maximum recursion depth exceeded

Python does this to prevent stack overflow and crashes.

8. # Time Complexity

Factorial:

factorial(n)

One recursive call per level.

Time:

O(n)

Space:

O(n)



9. # Recursion vs Iteration

| Feature     | Recursion                       | Iteration        |
| ----------- | ------------------------------- | ---------------- |
| Readability | Often cleaner                   | Sometimes longer |
| Memory      | O(n)                            | O(1)             |
| Call Stack  | Yes                             | No               |
| Performance | Slower (function call overhead) | Usually faster   |
| Risk        | RecursionError                  | None             |



10. # Tail Recursion

Tail recursion means the recursive call is the last operation.

Example:

def countdown(n):

    if n == 0:
        return

    return countdown(n - 1)

Some languages optimize this automatically (Tail Call Optimization).

Important

Python does NOT perform Tail Call Optimization (TCO).

So even tail-recursive functions consume one stack frame per call.


# Algorithms
DFS (Depth-First Search)
Tree Traversal
Merge Sort
Quick Sort
Binary Search (recursive version)
Backtracking (N-Queens, Sudoku)
Dynamic Programming (recursive + memoization)


# Interview Questions
What is recursion?
What are the base case and recursive case?
What happens internally during recursion?
Why is recursion memory-intensive?
Why doesn't Python optimize tail recursion?
Difference between recursion and iteration?
What is a stack frame?
What is the recursion limit in Python?