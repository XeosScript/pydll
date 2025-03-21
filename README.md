"""
pydll - A Simple Python Library for "DLL-like" Modules

This guide explains how to use the pydll library without installing it.
Make sure you have the `pydll` directory containing `__init__.py` and `core.py`
in your project.
"""

# --- 1. Project Setup ---
# Create a directory structure like this:
# my_project/
# ├── pydll/
# │   ├── __init__.py
# │   └── core.py
# ├── main.py  # Your main script
# ├── module.py # Module Creation Script (optional)
# --- 2. Importing the Library ---
# You need to tell Python where to find the pydll library.
# The easiest way (and recommended) is by adding the pydll
# directory to sys.path.  Put this at the *very top* of your scripts.

import sys
sys.path.append('./pydll')  # Adjust path if needed. Relative to the current script's location

# Now you can import pydll
import pydll

# --- 3. Creating a .pydll File (Using module.py, optional) ---

# (Create a file named 'module.py', for example)
# This script *creates* the .pydll file containing your code.
#
# Example 'module.py' content:
"""
import sys
sys.path.append('./pydll')  # Make sure pydll is importable, if this file is not at the same level

import pydll

pydll.new("my_module.pydll", """
def greet(name):
    return f"Hello, {name}!"

def add(a, b):
    return a + b
""")
"""

# To create the my_module.pydll, run module.py

# --- 4. Loading and Using the .pydll File (main.py) ---

# (Create a file named 'main.py')
# This script *uses* the .pydll file you created.

# Example 'main.py' content:
"""
import sys
sys.path.append('./pydll')  # Add this line

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
"""

# --- Important Notes and Considerations ---

# - **sys.path:** The sys.path.append('./pydll') line is CRUCIAL.
#   Adjust the path if your pydll folder is located somewhere else.
# - **Security:** The obfuscation is minimal and not secure. Do not rely on it
#   for protecting sensitive code.
# - **Error Handling:** Always check the return value of pydll.read() for None.
# - **Code Structure:** The obfuscation can break if your code has very unusual
#   structures. Keep it relatively simple.
# - **Obfuscation Level:** The core.py file contains `OBFUSCATION_LEVEL`.
#   You can adjust it for more or less obfuscation (0 for no obfuscation).

# --- Example pydll/core.py (for reference - you'll have your own version) ---
"""
import base64
import random
import re

OBFUSCATION_LEVEL = 2
RANDOM_SEED = 42
random.seed(RANDOM_SEED)

# (Rest of core.py code from previous examples) - Replace this with the content of your core.py!
# I will not copy core.py here to prevent this from becoming a huge code block

def generate_random_string(length=10):
    letters = 'abcdefghijklmnopqrstuvwxyz'
    return ''.join(random.choice(letters) for i in range(length))

def insert_junk_comments(code, level):
    if level > 0:
        lines = code.splitlines()
        for i in range(len(lines)):
            if random.random() < 0.3 * level:
                lines[i] += f"  # {generate_random_string()}"
        code = "\n".join(lines)
    return code

def split_and_shuffle_code(code, level):
    if level > 0:
        definitions = re.findall(r"(def\s+\w+\(.*\):\n(?:    .*\n)*)|(class\s+\w+\(.*\):\n(?:    .*\n)*)", code, re.MULTILINE)
        definitions = [item[0] or item[1] for item in definitions]
        if not definitions:
            return code
        random.shuffle(definitions)
        code = "\n\n".join(definitions)
    return code

def encode_strings(code, level):
    if level > 0:
        def replace_string(match):
            string = match.group(1)
            encoded_string = base64.b64encode(string.encode('utf-8')).decode('utf-8')
            parts = [encoded_string[i:i + 8] for i in range(0, len(encoded_string), 8)]
            random.shuffle(parts)
            combined_parts = '+'.join(f"'{part}'" for part in parts)
            return f"_decode_string({combined_parts})"
        code = re.sub(r"'(.*?)'", replace_string, code)
        code = re.sub(r'"(.*?)"', replace_string, code)
    return code

def insert_dummy_variables(code, level):
    if level > 0:
        lines = code.splitlines()
        for i in range(len(lines)):
            if random.random() < 0.2 * level:
                variable_name = generate_random_string(5)
                lines[i] = f"{variable_name} = {random.randint(0, 100)}\n" + lines[i]
        code = "\n".join(lines)
    return code

def shuffle_function_arguments(code, level):
    if level > 0:
        def replace_function(match):
            function_name = match.group(1)
            arguments = match.group(2).split(',')
            arguments = [arg.strip() for arg in arguments]
            random.shuffle(arguments)
            shuffled_arguments = ', '.join(arguments)
            return f"def {function_name}({shuffled_arguments}):"
        code = re.sub(r"def (\w+)\((.*?)\):", replace_function, code)
    return code

def obfuscate(code, level):
    code = insert_junk_comments(code, level)
    code = insert_dummy_variables(code, level)
    code = split_and_shuffle_code(code, level)
    code = encode_strings(code, level)
    code = shuffle_function_arguments(code, level)
    return code

def new(filepath, code_string):
    try:
        obfuscated_code = obfuscate(code_string, OBFUSCATION_LEVEL)
        print("Obfuscated code:")
        print(obfuscated_code)
        encoded_code = base64.b64encode(obfuscated_code.encode('utf-8')).decode('utf-8')
        with open(filepath, "w") as f:
            f.write(encoded_code)
    except Exception as e:
        print(f"Error creating .pydll file: {e}")

def read(filepath):
    try:
        with open(filepath, "r") as f:
            encoded_code = f.read()
        obfuscated_code = base64.b64decode(encoded_code.encode('utf-8')).decode('utf-8')
        def _decode_string(*encoded_strings):
            decoded_string = ""
            for encoded_string in encoded_strings:
                decoded_string += base64.b64decode(encoded_string.encode('utf-8')).decode('utf-8')
            return decoded_string
        module_dict = {'_decode_string': _decode_string}
        exec(obfuscated_code, module_dict)
        return module_dict
    except FileNotFoundError:
        print(f"Error: File '{filepath}' not found.")
        return None
    except Exception as e:
        print(f"Error reading .pydll file: {e}")
        return None

"""
# --- Example pydll/__init__.py (for reference)---
from .core import new, read
"""
