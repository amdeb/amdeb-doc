#Code Style for Pythonic Developer#

One important design goal of the Python programming language is readability. 
The coding motto is: 

> Anyone can write code a computer can understand,
but professional developers write code humans can understand.

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

##Code Style##
---

In the above resources, reference 1, 2, and 3 describe the basic Python 
code styles that one should follow. Reference 5 and 6 give suggestion of Odoo-specific 
code styles.  

##Code Style Check##
---

In addition to code review, an automatic code style checking tool. 
[Pylint] (http://www.pylint.org/) is recommended to be used to 
enforce standard code style. Every code file **MUST** pass Pylint check 
before it is checked in. 

##Code Document##
---

It is a mandatory rule that every module, function, class and method definition should 
have s docstring that provides as code document. 
[PEP-257: Docstring Conventions] (http://legacy.python.org/dev/peps/pep-0257/) 
has detail description of document format that one should follow. 





