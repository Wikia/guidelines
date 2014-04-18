# Wikia Python Coding Conventions

This document defines the best practices for contributing to Python projects at Wikia. The purpose
of this document is to keep our code stable and consistent. We firmly believe that well written code
looks like it was written by one person. Please read this whole document before writing Python code
for a Wikia project. Let's start off with an excerpt from the [Google Python Style Guide] that does
a good job of articulating our desire for consistency (emphasis added):

> BE CONSISTENT.
>
> If you're editing code, take a few minutes to look at the code around you and determine its style.
> If they use spaces around all their arithmetic operators, you should too. If their comments have
> little boxes of hash marks around them, make your comments have little boxes of hash marks around
> them too.
>
> **The point of having style guidelines is to have a common vocabulary of coding so people can
> concentrate on what you're saying rather than on how you're saying it.** We present global style
> rules here so people know the vocabulary, but local style is also important. If code you add to a
> file looks drastically different from the existing code around it, it throws readers out of their
> rhythm when they go to read it. Avoid this.

## Documents With Which You Should Be Intimately Familiar

* [PEP 20] a.k.a. The Zen of Python: These are the oft-quoted guiding principles of Python's
  design. Memorize them, seriously.
* [PEP 8]: This is the de-facto code style guide for Python. It was adapted from
  [Guido van Rossum]'s original Python Style Guide essay. Pretty much everyone in the Python
  community uses it, and so should you.
* [PEP 257]: Documentation on the semantics and conventions associated with Python docstrings.
* [Google Python Style Guide]: This is the main style guide that Wikia's Platform Team decided to
  adopt. It's mainly just a superset of PEP 8 with some useful additions.

## General Guidelines

_(This list contains differences, additions, and clarifications to the above documents.)_

* Most Python style guides suggest a line length limit of around 80 characters. Let's be practical.
  No one wants to do that. Our line length limit is 120 characters.
* Use "CamelCase" for class names.
* Use "lowercase" or "lowercase_with_underscores" for function, method, and variable names. For
  short names, maximum two words, joined lowercase may be used (e.g. `filename`). For long names
  with three or more words, or where it’s hard to parse the split between two words, use
  lowercase_with_underscores (e.g., `note_explicit_target`, `explicit_target`). If in doubt, use
  underscores.
* Single letter variable names are allowed under the "we're all consenting adults" philosophy.
  **Don't overuse them.** Single letter names are great for things like iteration, comprehensions,
  lambdas, etc. There are some situations where you might be tempted to use a single letter name
  when a two letter name would be significantly more readable. For example:
    * For exceptions, `ex` is better than `e`.
    * For file objects (pointers), `fp` is better than `f`.
    * For function objects (like inside a decorator), `fn` is better than `f`.
* Use `'single quotes'` for string literals, and `"""triple double quotes"""` for docstrings. Double
  quotes are OK for something like `"don't"`.
* If you want to document your function parameters in their docstrings, we recommend using
  [Sphinx style docstrings] instead of the Google style docstrings described in the Google Python
  Style Guide.

## How to Check Your Code With `pep8` and `pylint`

_NOTE: In the future, we will likely improve the process for checking your code with `pep8` and
`pylint` to make it easier. For now, the instructions below are the recommended procedure._

The [pep8] command-line tool is a code style checker that you can use to check your code against
the [PEP 8] document. Here's how to use it:

1. Install it using `pip`:

   ```
   pip install pep8
   ```

1. Put the following settings into the `setup.cfg` file for your project (create this file if you
   don't have one already):

   ```
   [pep8]
   max-line-length=120
   ```

1. Run it with the following command (replace `your_module` with the actual name of your module):

   ```
   cd /path/to/your_project
   pep8 your_module
   ```

The [pylint] command-line tool is a code analyzer that tries to find potential problems in your
code. It also does a little bit of style checking. Here's how to use it:

1. Install it using `pip`:

   ```
   pip install pylint
   ```

1. Copy our [base pylintrc file] into root directory of your project. This file contains all our
   recommended pylint settings for Wikia Python projects.
1. Run it with the following command (replace `your_module` with the actual name of your module):

   ```
   cd /path/to/your_project
   pylint your_module
   ```

Some notes about Pylint:

* Pylint doesn't always know best. However, you should always have a good, defendable reason for
  ignoring a Pylint message. It might be a good idea to add a `# TODO: ...` comment in the code with
  your plans to fix it in the future.
* There should generally be **ZERO** Pylint messages in the _fatal_, _error_, and _convention_
  categories. There will almost always be no good reason for these. Don't ignore these.
* Don't disable Pylint messages by using `# pylint: disable=...` comments. It's best to be
  constantly aware of the messages than to risk forgetting about them.

## Recommended Code Quality Metrics

* Pylint global evaluation rating: Use the `--reports=y` option to `pylint` to get this value
  from the report it generates. _(Recommended threshold: > 9.50)_
* Percentage of comments: Divide the number of lines of Python comments by the number of lines
  of Python code (as measured by the [cloc] command-line tool) to get a percentage.
  _(Recommended threshold: > 20%)_
* Test code coverage: If your code has unit tests, run the tests through the [coverage] module. Look
  at the documentation for the coverage module for more details. _(Recommended threshold: > 80%)_.

## Further Reading

* [The Hitchhiker's Guide to Python]: This is a really great website created by [Kenneth Reitz],
  the guy who wrote [requests]. He's considered by some to be an expert on Python best practices.
* [The Flask Mega-Tutorial]: If you want to use [Flask], look at this. You'll learn _SOOOOO_ much.


[Google Python Style Guide]: http://google-styleguide.googlecode.com/svn/trunk/pyguide.html
[PEP 8]: http://www.python.org/dev/peps/pep-0008/
[PEP 20]: http://www.python.org/dev/peps/pep-0020/
[PEP 257]: http://www.python.org/dev/peps/pep-0257/
[Guido van Rossum]: http://en.wikipedia.org/wiki/Guido_van_Rossum
[Sphinx style docstrings]: https://pythonhosted.org/an_example_pypi_project/sphinx.html#full-code-example
[pep8]: https://github.com/jcrocholl/pep8
[pylint]: http://www.pylint.org/
[base pylintrc file]: https://github.com/Wikia/python-wikiautils/blob/master/pylintrc
[cloc]: http://cloc.sourceforge.net/
[coverage]: http://nedbatchelder.com/code/coverage/
[The Hitchhiker's Guide to Python]: http://docs.python-guide.org/en/latest/
[Kenneth Reitz]: http://kennethreitz.org/software/
[requests]: http://docs.python-requests.org/en/latest/
[The Flask Mega-Tutorial]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world
[Flask]: http://flask.pocoo.org/
