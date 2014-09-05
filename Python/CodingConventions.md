# Wikia Python Coding Conventions

This document defines the best practices for contributing to Python projects at Wikia. The purpose
of this document is to keep our code readable, stable, and consistent. We firmly believe that well
written code looks like it was written by one person. Please read this whole document before writing
Python code for a Wikia project. Let's start off with an excerpt from [PEP 8], the de facto Python
style guide:

> **A Foolish Consistency is the Hobgoblin of Little Minds**
>
> One of Guido's key insights is that code is read much more often than it is written. The
> guidelines provided here are intended to improve the readability of code and make it consistent
> across the wide spectrum of Python code. As PEP 20 says, "Readability counts".
>
> A style guide is about consistency. Consistency with this style guide is important. Consistency
> within a project is more important. Consistency within one module or function is most important.
>
> But most importantly: know when to be inconsistent -- sometimes the style guide just doesn't
> apply. When in doubt, use your best judgment. Look at other examples and decide what looks best.
> And don't hesitate to ask!

Here's another good quote about consistency from the [Google Python Style Guide]:

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

## Documents You Should DEFINITELY Read

The documents listed below are the basis of this style guide. These three documents are the
essentially the holy scriptures of Python development. Every Pythonista should be extremely familiar
with these documents.

* **[PEP 20] \(The Zen of Python\)** – These are the oft-quoted guiding principles of Python's
  design. Memorize them, seriously. These are not only good rules for Python development, but also
  software development in general.
* **[PEP 8]** – This is the de facto code style guide for Python. It was adapted from
  [Guido van Rossum]'s original Python Style Guide essay. Pretty much everyone in the Python
  community uses it, and so should you.
* **[PEP 257]** – Documentation on the semantics and conventions associated with Python docstrings.
  These are just the basics, if you want to get more fancy, see the section on
  [documenting your code's API](#documenting-your-codes-api).

## General Guidelines

This list contains differences, additions, and clarifications to the above documents.

* [PEP 8] suggests a line length limit of 80 characters. However, it also says that if you "strongly
  prefer a longer line length", then you should use 100 characters. This is the convention we follow
  at Wikia. See the ["max line length" section of PEP 8] for more details.
* Use `UpperCamelCase` for class names.
* Use `lowercase` or `lowercase_with_underscores` for function, method, and variable names. For
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

## How to Check Your Code With `pep8` and `pylint`

**NOTE:** In the future, we will likely improve the process for checking your code with `pep8` and
`pylint` to make it easier. For now, the instructions below are the recommended procedure.

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
   max-line-length=100
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

1. Copy our **[base pylintrc file]** into the root directory of your project. This file contains all
   our recommended pylint settings for Wikia Python projects.
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
* If you disable Pylint messages by using `# pylint: disable=...` comment, it should always have a
  corresponding comment explaining why it was disabled.

## Recommended Code Quality Metrics

* Pylint global evaluation rating: Use the `--reports=y` option to `pylint` to get this value
  from the report it generates. _(Recommended threshold: > 9.50)_
* Percentage of comments: Divide the number of lines of Python comments by the number of lines
  of Python code (as measured by the [cloc] command-line tool) to get a percentage.
  _(Recommended threshold: > 20%)_
* Test code coverage: If your code has unit tests, run the tests through the [coverage] module. Look
  at the documentation for the coverage module for more details. _(Recommended threshold: > 80%)_.

## Documenting Your Code's API

If you want to document the functionality of your code beyond simple one-line docstrings, use
[Sphinx style docstrings]. This will allow you to document your code inline and then generate nice,
browsable documentation from that. Here's an example of a function definition with its arguments
documented:

```python
def send_message(sender, recipient, message_body, priority=1):
    """Send a message to a recipient

    :param str sender: The person sending the message
    :param str recipient: The recipient of the message
    :param str message_body: The body of the message
    :param priority: The priority of the message, can be a number 1-5
    :type priority: integer or None

    :return: the message id
    :rtype: int

    :raises ValueError: if the message_body exceeds 160 characters
    :raises TypeError: if the message_body is not a basestring
    """
    ...
```

**NOTE:** If the data type of the parameter is a single word, you can combine them on one line.
Otherwise, you need to define it on a separate line. Both examples are given above.


[Google Python Style Guide]: http://google-styleguide.googlecode.com/svn/trunk/pyguide.html
[PEP 8]: http://www.python.org/dev/peps/pep-0008/
[PEP 20]: http://www.python.org/dev/peps/pep-0020/
[PEP 257]: http://www.python.org/dev/peps/pep-0257/
[Guido van Rossum]: http://en.wikipedia.org/wiki/Guido_van_Rossum
["max line length" section of PEP 8]: http://legacy.python.org/dev/peps/pep-0008/#maximum-line-length
[Sphinx style docstrings]: http://sphinx-doc.org/domains.html#signatures
[pep8]: https://github.com/jcrocholl/pep8
[pylint]: http://www.pylint.org/
[base pylintrc file]: https://github.com/Wikia/guidelines/blob/master/Python/pylintrc
[cloc]: http://cloc.sourceforge.net/
[coverage]: http://nedbatchelder.com/code/coverage/
