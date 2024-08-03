# Python

## Making Your Code Readable
* Avoid abbreviating variable names
* Document your classes and methods
* Comment your code
* Refactor repeated lines of code into reusable functions or methods
* Keep functions and methods short. A good rule of thumb is that scrolling should not be necessary to read an entire function or method

## PEP 8
[PEP8 style guide](https://peps.python.org/pep-0008/) is mandatory to be followed in every aspects excepting the code layout part which has to follow [Black formatter](https://black.readthedocs.io/en/stable/).

[Naming conventions](https://peps.python.org/pep-0008/#naming-conventions) and consistency across the project is important in order to achieve a high-quality, self-understandable and maintainable code base.

Format code using [Black](https://black.readthedocs.io/en/stable/). It’s dead simple to install and it does auto-formatting. Apply it to all files excepting migration files, can be executed from command line or integrated as a plugin to PyCharm or VSCode:

```
$ black <filename.py>
```

## Django code style
* Follow the [Django coding style](https://docs.djangoproject.com/en/dev/internals/contributing/writing-code/coding-style/).
* Use Underscores in URL Pattern Names Rather Than Dashes
* Use Underscores in Template Block Names Rather Than Dashes
* Use [Coockicutter](https://github.com/KUWAITNET/kn-django-cookiecutter) for project layout