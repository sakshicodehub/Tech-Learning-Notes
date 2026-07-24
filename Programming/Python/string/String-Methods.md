### What is a Method?

A method is simply a function that belongs to an object.
# Since strings are immutable, every string method returns a new string.

upper()
lower()
capitalize()
title()
swapcase()
strip()
lstrip()
rstrip()
replace()
find()
rfind()
index()

If string length is n so for upper() and lower()

O(n)

because every character must be checked.

Space Complexity
O(n)

A new string is created.


text = "python engineering"

print(text.capitalize()) // Python engineering
print(text.title())  // Python Engineering

## swapcase()

Upper becomes lower. Lower becomes upper.

text = "Python"

print(text.swapcase())

Output pYTHON

strip()

One of the most useful methods.

Suppose user enters:
    name = "   Sakshi   "

    print(name.strip())

    Output

    Sakshi

## lstrip()

Remove only left spaces.

text = "   Python"

print(text.lstrip())

Output

Python

## rstrip()

Remove only right spaces.

text = "Python   "

print(text.rstrip())

Output

Python

# why use : Professional code: username = input().strip()

replace()

Replace substring.

text = "I like Java"

print(text.replace("Java", "Python"))

Output

I like Python

## find()

Find the first index of a substring.

text = "Python"

print(text.find("t"))

Output

2

If not found:

print(text.find("z"))

Output

-1

No exception.

##rfind()

Find the last index of a substring.

text = "the Python"

print(text.rfind("t"))

Output

6

## index()

Looks similar.

text = "Python"

print(text.index("t"))

Output

2

Now:

text.index("z")

Output

ValueError

Difference Between find() and index()
Method	Not Found
find()	-1
index()	ValueError


text = "python"

print(text.upper().lower().replace("o", "0").title())

"pyth0n".title()   -> Pyth0N

The title() method capitalizes the first cased letter after a non-letter separator.

In "pyth0n":

p → first letter → becomes P
0 → not a letter, so Python treats it like a word boundary
n → first letter after 0 → becomes N



split()
rsplit()
splitlines()
join()
startswith()
endswith()
count()
isalpha()
isdigit()
isalnum()
isspace()
partition()
removeprefix()
removesuffix()
islower()
isupper()

## split()

Default Separator

If no separator is provided:

text.split()

Python splits on whitespace (spaces, tabs, newlines).

Example:

text = "Python   Java\tC++"

print(text.split())

Output:

['Python', 'Java', 'C++']

Custom Separator
data = "apple,banana,orange"

print(data.split(","))

Output:

['apple', 'banana', 'orange']

Limit the Number of Splits
text = "A-B-C-D"

print(text.split("-", 2))

Output:

['A', 'B', 'C-D']

Industry Use Cases
Reading CSV
line = "John,25,Developer"

name, age, role = line.split(",")

URL Parsing
url = "https://openai.com/docs"

parts = url.split("/")


## rsplit()

Splits from the right.

Example:

path = "folder/subfolder/file.txt"

print(path.rsplit("/", 1))

Output:

['folder/subfolder', 'file.txt']

Very useful for extracting filenames.


splitlines()

Splits text into lines.

text = "Hello\nWorld\nPython"

print(text.splitlines())

Output:

['Hello', 'World', 'Python']

Useful when reading files.

### join() — One of Python's Most Powerful Methods

This is a favorite interview topic.

Suppose:

words = ["Python", "Java", "C++"]

You want:

Python Java C++

Use:

print(" ".join(words))

Output: Python Java C++



##  Why join() is Faster than +

Bad:

result = ""

for word in words:
    result += word

Each += creates a new string because strings are immutable.

Conceptually:

"" → "Python"

↓

"PythonJava"

↓

"PythonJavaC++"

Many intermediate strings are created.

Good:

result = "".join(words)

Only one allocation.

Complexity Comparison
Method	Time Complexity
+= in loop	O(n²) (can become quadratic due to repeated copying)
join()	O(n)

Important Note: CPython has some optimizations for simple += cases, but you should still prefer join() when concatenating many strings.




## startswith()
url = "https://openai.com"

print(url.startswith("https"))

Output:

True

accept only secure URLs.

if not url.startswith("https://"):
    print("Invalid URL")


## endswith()
filename = "resume.pdf"

print(filename.endswith(".pdf"))

Output:

True
Industry Example

Validate file uploads.

Allow:

.pdf
.jpg
.png

Reject: .exe


## count()

Counts occurrences.

text = "banana"

print(text.count("a"))

Output:

3

## isalpha()

Checks whether all characters are alphabetic.

print("Python".isalpha())

↓

True
print("Python3".isalpha())

↓

False


# isdigit()
print("123".isdigit())

↓

True
print("12A".isdigit())

↓

False


# isalnum()

Letters or digits only.

print("Python3".isalnum())

↓

True
print("Python-3".isalnum())

↓

False


# isspace()
print("   ".isspace())

↓

True
print(" A ".isspace())

↓

False



# partition()
email = "user@gmail.com"

print(email.partition("@"))

Output:

('user', '@', 'gmail.com')

Returns a tuple:

(before, separator, after)

Unlike split(), it always returns exactly three elements.


# removeprefix()
url = "https://openai.com"

print(url.removeprefix("https://"))

↓

openai.com


# removesuffix()
filename = "resume.pdf"

print(filename.removesuffix(".pdf"))

↓

resume


Method	Time
split()	O(n)
join()	O(n)
find()	O(n)
replace()	O(n)
count()	O(n)
startswith()	O(m) where m is the prefix length
endswith()	O(m) where m is the suffix length