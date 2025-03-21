#pydll

pydll is a simple Python library designed to create and use obfuscated “DLL-like” modules within Python. It allows you to separate your code into distinct modules that can be loaded dynamically and protected with basic obfuscation to make the code harder to read and understand.

# Usage:

The pydll library provides two main functions: new() and read(). Remember to add sys.path.append() before importing pydll!

pydll.new(filepath, code_string):

Description: Creates a new “.pydll” file containing obfuscated Python code.

Arguments:

filepath (str): The path to the “.pydll” file to be created.
code_string (str): A string containing the Python code to be obfuscated and saved.
Example:

import sys
sys.path.append('./pydll') # Add this line

import pydll

pydll.new("my_module.pydll", """
def greet(name):
    return f"Hello, {name}!"

def add(a, b):
    return a + b
""")

python
pydll.read(filepath):

Description: Reads a “.pydll” file, executes the code within, and returns a dictionary containing the functions, classes, and variables defined in the code.

Arguments:

filepath (str): The path to the “.pydll” file.
Return Value:

(dict): A dictionary containing the functions, classes, and variables from the “.pydll” file. Returns None if an error occurs.
Example:

import sys
sys.path.append('./pydll') # Add this line

import pydll

# Read the .pydll file
module_data = pydll.read("my_module.pydll")

if module_data:
    greet = module_data['greet']  # Extract the greet function
    result = greet("Alice")
    print(result)  # Output: Hello, Alice!

    add = module_data['add']
    sum_result = add(5, 3)
    print(sum_result)  # Output: 8
else:
    print("Failed to load the module.")

python
Example Usage (Complete):

module.py (for creating the .pydll):

import sys
sys.path.append('./pydll') # Add this line

import pydll

pydll.new("my_module.pydll", """
def greet(name):
    return f"Hello, {name}!"

def add(a, b):
    return a + b
""")

python
main.py:

import sys
sys.path.append('./pydll') # Add this line

import pydll

# Read the .pydll file
module_data = pydll.read("my_module.pydll")

if module_data:
    greet = module_data['greet']  # Extract the greet function
    result = greet("Alice")
    print(result)

    add = module_data['add']
    sum_result = add(5, 3)
    print(sum_result)
else:
    print("Failed to load the module.")

python
Important Considerations:

Path Management: Correctly setting up the Python path (using sys.path.append()) is crucial when using the library without installation. Make sure the path points to the directory containing the pydll folder.
Security: pydll does not provide strong code protection. The obfuscation is designed to deter casual inspection, not to prevent determined reverse engineering. Do not use pydll to protect sensitive information or critical security logic.
Error Handling: Always check if pydll.read() returns None to handle potential loading errors.
Compatibility: Be aware that certain advanced Python features or complex code structures might not be handled correctly by the obfuscation process. Test your code thoroughly.
