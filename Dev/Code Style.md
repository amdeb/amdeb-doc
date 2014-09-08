#Code Style for Pythonic Developer#

One important design goal of the Python programming language is readability. 
The coding motto is: 

> Programs must be written for people to read, and only incidentally for machines to execute. 
-H. Abelson and G. Sussman (SICP).

When in doubt, always put **maintainability** as your top priority. 
The code style in this document is based on the following resources. 

1. [The Hitchhiker’s Guide to Python!] (http://docs.python-guide.org/en/latest/) 
2. [Google Python Style Guide] 
(https://google-styleguide.googlecode.com/svn/trunk/pyguide.html) 
3. [PEP-8: Style Guide for Python Code] (http://legacy.python.org/dev/peps/pep-0008/)
4. [PEP-257: Docstring Conventions] (http://legacy.python.org/dev/peps/pep-0257/)
5. [Odoo Python Style Guide] 
(https://doc.odoo.com/contribute/15_guidelines/coding_guidelines_python/)
6. [Style Guide for OpenErp Code] 
(http://www.simplerp.it/openerp/sezioni/technolab/item/305-style-guide-for-openerp-code)
7. [Code Like a Pythonista: Idiomatic Python] 
(http://python.net/~goodger/projects/pycon/2007/idiomatic/handout.html) 
A little bit outdated, but still helpful.

##Code Style
---

In the above resources, reference 1, 2, and 3 describe the basic Python 
code styles that one should follow. Reference 5 and 6 give suggestion of Odoo-specific 
code styles.  

##Code Style Check
---

In addition to code review, an automatic coding style checking tool. 
[Flake8] (http://flake8.readthedocs.org/) is recommended to be used to 
enforce standard code style. Every code file **MUST** pass Pylint check 
before it is checked in to shared repository branch. 

##Code Document
---

It is a mandatory rule that every module, function, class and 
method definition should have s docstring that provides as code document. 
[PEP-257: Docstring Conventions] (http://legacy.python.org/dev/peps/pep-0257/) 
has detail description of document format that one should follow. 

## PEP 8 Coding Style Cheat sheet
---

* Use 4 space per indentation level.
* If a function, a function call, a list, or a dictionary needs 
more than one line, there should be no argument/elements in the 
first line and there should be an indentation for continuation line.        
* The closing brace/bracket/parenthesis on multi-line constructs should
be in a line by itself. 
* Maximum code line length is 79 characters.
* Maxim docstring or comment line length is 72 characters.
* Use parentheses, brackets or braces to wrap long line implicitly. 
Backslash line continuation is useful for multiple with-statements or 
asert statements.  
* Separate top-level function and class definitions with two blank lines.
Method definitions inside a class are separated by a single blank line.
* In Python 2.x code file, put `# -*- coding: utf-8 -*-` as the 
first line.
* Each `import` (as the first word) should be on a separate line.
* Imports are put at the top of the file, just after encoding declaration 
and any module comments or docstrings.
* Imports are grouped by standard library, third party library, 
and local library. Put a blank line between each group. 
* Absolute imports are recommended. 
* It is ok to use relative imports for sibling modules. Relative
imports uses `from .path import name` syntax. 
* Do not use wildcard imports and implicit relative imports.
* Always surround these binary operators with a single space on either side.
* Don't use spaces around the = sign when used to indicate a keyword 
argument or a default parameter value.
* Don't use Compound statements (multiple statements on the same line).
* Write docstrings for all public modules, functions, classes and methods. 
* Modules should have short, all-lowercase names. 
Underscores can be used in the module name if it improves readability. 
Python packages should also have short, all-lowercase names, 
although the use of underscores is discouraged.
* Class names, including exceptions, should normally use the 
CapWords convention.  Use the suffix "Error" on your exception names.
* Function names and global variable names should be lowercase, 
with words separated by underscores as necessary to improve readability.
* Always use self for the first argument to instance methods.
* Always use cls for the first argument to class methods.
* For method names and instance variables, use one leading underscore 
only for non-public methods and instance variables.
* Constants are usually defined on a module level and written in all 
capital letters with underscores separating words. 
Examples include MAX_OVERFLOW and TOTAL.
* Always use a def statement instead of an assignment statement 
that binds a lambda expression directly to an identifier.
* When raising an exception in Python 2, use raise `ValueError('message')` 
instead of the older form `raise ValueError, 'message'`.
Context managers should be invoked through separate functions or 
methods whenever they do something other than acquire and 
release resources.
* Use string methods instead of the string module.
* Object type comparisons should always use isinstance() instead 
of comparing types directly.
* For sequences, (strings, lists, tuples), use the fact 
that empty sequences are false.



 









