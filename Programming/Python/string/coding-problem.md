# Exercise 1. Create a string made of the first, middle, and last character
Practice Problem: 
Write a program to create a new string made of an input string’s first, middle, and last characters.

Exercise Purpose: This exercise teaches the fundamentals of String Indexing. Accessing specific positions, especially the middle that requires calculating length, is a foundational skill for data parsing and text manipulation.

Given Input: str1 = "James"

Expected Output: Jms

# Exercise 2. Create a string made of the middle three characters
Practice Problem: Write a program to create a new string made of the middle three characters of an input string of odd length.
Exercise Purpose: This builds on indexing by introducing String Slicing. Slicing is a powerful Python feature that lets you extract entire “chunks” of data efficiently.

Given Input: str1 = "JhonDipPeta"

Expected Output: Dip

# Exercise 3. Append new string in the middle of a given string
Practice Problem: Given two strings, s1 and s2, create a new string by appending s2 in the middle of s1.

Exercise Purpose: This exercise introduces String Partitioning and Concatenation. In programming, you often need to “inject” data into a template or modify strings at specific locations.

Given Input: s1 = "Ault" s2 = "Kelly"

Expected Output: AuKellylt

# Exercise 4. Create a new string made of the first, middle, and last characters of each input string
Practice Problem: Given two strings, s1 and s2, create a new string from the first, middle, and last characters of each input string.
Exercise Purpose: This exercise practices Complex Concatenation. It requires multiple independent extractions and organizing them into a single result, common when generating IDs or codes from user data.

Given Input: s1 = "America" s2 = "Japan"

Expected Output: AJrpan

# Exercise 5. Reverse a given string
Practice Problem: Write a program to reverse a given string.

Exercise Purpose: Reversing a string is a classic logic-building exercise. In Python, this is most efficiently done via Slicing, demonstrating the language’s ability to manipulate sequences using “steps.” It is a prerequisite for solving problems like palindrome detection.

Given Input: str1 = "PYnative"

Expected Output: evitanYP

# Exercise 6. Find the last position of a given substring
Practice Problem: Write a program to find the last index of the substring “Emma” in a given string.

Exercise Purpose: While the .find() method searches from the beginning of a string, the .rfind() method (Reverse Find) locates the most recent occurrence of a specified pattern. This functionality is essential when parsing file paths or URLs that require identification of the final delimiter.

Given Input: str1 = "Emma is a data scientist who knows Python. Emma works at google."

Expected Output: Last occurrence of Emma starts at index 43

# Exercise 7. Split a string on hyphens
Practice Problem: Write a program to split a given string on hyphens and display each substring.

Exercise Purpose: This exercise introduces the concept of tokenization. Dividing strings into smaller components based on delimiters, such as commas, spaces, or hyphens, is a common technique for processing CSV files, logs, and user-entered lists.

Given Input: str1 = "Emma-is-a-data-scientist"

Expected Output:

Displaying each substring: 
Emma
is
a
data
scientist

# Exercise 8. Find all occurrences of a substring in a given string by ignoring the case

Practice Problem: Write a program to find the total count of the substring “USA” in a given string, ignoring the case (i.e., both “usa” and “USA” should be counted).

Exercise Purpose: This exercise addresses case normalization. In practical data science and web scraping applications, text data is frequently inconsistent. Converting all text to lowercase prior to processing is considered a standard best practice.

Given Input: str1 = "Welcome to USA. usa awesome, isn't it?"

Expected Output: The USA count is: 2


# Exercise 9. String characters balance test
Practice Problem: Write a program to check if two strings are balanced. For example, strings s1 and s2 are balanced if all the characters in s1 are present in s2. The character’s position doesn’t matter.

Exercise Purpose: This exercise focuses on membership testing. This fundamental concept is utilized in data validation, such as verifying whether a password contains required characters or determining if a search query matches a database entry.

Given Input:

Case 1: s1 = "yn", s2 = "PyNative"
Case 2: s1 = "ynf", s2 = "PyNative"

Expected Output:

Case 1: True
Case 2: False


# Exercise 11. Prefix/Suffix Check
Practice Problem: Check if a given URL starts with “https” and ends with “.com”.

Exercise Purpose: This exercise teaches Boolean Validation. Methods like .startswith() and .endswith() are cleaner and less error-prone than manual slicing for verifying file formats, protocols, or naming conventions.

Given Input: str1 = "https://google.com"

Expected Output: Is valid URL: True

# Exercise 12. Swap Case
Practice Problem: Write a program to toggle the case of all characters in a string (uppercase becomes lowercase and vice versa).

Exercise Purpose: This demonstrates Case Transformation. Though simple, it is often used in search algorithms to normalize data or in text editors to provide “Toggle Case” functionality.

Given Input: str1 = "PyThOn"

Expected Output: pYtHoN


# Exercise 13. Remove Whitespace
Practice Problem: Remove every single space from a given string, including spaces between words.

Exercise Purpose: This highlights the difference between Trimming and Filtering. While .strip() only removes leading/trailing spaces, .replace() can reach inside a string to remove characters globally.

Given Input: str1 = " P y t h o n "

Expected Output: Python


# Exercise 14. N-th Character Removal
Practice Problem: Write a program to remove the character at index i from a string.
Exercise Purpose: Since Python strings are Immutable (you can’t just delete a character at an index), this exercise teaches you how to “reconstruct” a string by skipping over a specific part.

Given Input: str1 = "Python", i = 2

Expected Output:

Pyhon (The character 't' at index 2 was removed)


# Exercise 15. String Partitioning
Practice Problem: Use the .partition() method to split a string into three parts: the part before a separator, the separator itself, and the part after it.

Exercise Purpose: .split() is great for breaking a string into many pieces. .partition() is a specialized tool that always returns a 3-tuple. It is especially useful for parsing data like email addresses or key-value pairs where the separator needs to be preserved or accounted for.

Given Input: str1 = "username@company.com", sep = "@"

Expected Output: ('username', '@', 'company.com')


# Exercise 17. Lowercase First
Practice Problem: Write a program to arrange string characters such that all lowercase letters come first, followed by all uppercase letters.

Exercise Purpose: This exercise introduces Conditional Filtering and Reassembly. It’s a common pattern in data sorting: you need to group items by a specific property (in this case, “casing”) while preserving their relative order.

Given Input: str1 = "PyNaTive"

Expected Output: yaivePNT


# Exercise 18. Count all letters, digits, and special symbols from a given string
Practice Problem: Write a program to count all letters, digits, and special symbols from a given string.

Exercise Purpose: This introduces Character Classification. Using built-in string methods like .isalpha() and .isdigit() is the standard way to validate user input and perform data cleaning.

Given Input: str1 = "P@#yn26at^&i5ve"

Expected Output:

Total counts of chars, digits, and symbols: Chars = 8 Digits = 3 Symbol = 4


# Exercise 21. Count occurrences of all characters within a string
Practice Problem: Count the frequency of every character in a string and store the results in a dictionary.

Exercise Purpose: This exercise introduces Frequency Mapping using Dictionaries. Dictionaries are essential for counting and organizing data because they store unique “keys” (the characters) and associated “values” (their counts).

Given Input: str1 = "apple"

Expected Output: {'a': 1, 'p': 2, 'l': 1, 'e': 1}


# Exercise 22. Remove empty strings from a list of strings
Practice Problem: Given a list of strings, remove any empty strings or None values from it.

Exercise Purpose: This introduces Data Cleaning. Real-world datasets often have missing or empty values. Learning to filter lists is essential to prevent your program from crashing when processing empty data points.

Given Input: str_list = ["Emma", "Jon", "", "Kelly", None, "Eric", ""]

Expected Output: ['Emma', 'Jon', 'Kelly', 'Eric']





Document link: [https://pynative.com/python-string-exercise/]



