# Python styleguide

Reccomended reading:

* [PEP8 - Style Guide for Python Code](https://www.python.org/dev/peps/pep-0008/)
* [The Elements of Python Style](https://github.com/amontalenti/elements-of-python-style)

## Formatting

Python has a strong and widely followed styleguide called [PEP8], you should follow [PEP8].

If you want to add checking and enforcement to your project the reccomended tool is [flake8]. You may want to 
[adjust the 79-character line length limit][flake8-config] with the `max-line-length` config option.

[PEP8]: https://www.python.org/dev/peps/pep-0008/
[flake8]: https://flake8.readthedocs.org
[flake8-config]: https://flake8.readthedocs.org/en/latest/config.html

## Style

[The Elements of Python Style][TEPS] is a good starting point for going beyond what's covered by [PEP8]. There are a
couple of places where we would like to make our own interpretation clearer.

[TEPS]: https://github.com/amontalenti/elements-of-python-style

### Functions vs classes

Python is multi-paradigm, make use of functions and modules where they suffice, use classes when you wish to 
encapsulate state and behaviour. Avoid using classes to group functions.

### Exceptions

Create your own exceptions where doing so better expresses intent or provides clearer failure messages, but try to keep
the number low.