# Project Organization, Packaging, and Deployment

This document explains how to best organize your Python projects, how to package them up for easier
installation anywhere, and some best practices for preparing your project for deployment at Wikia.

## Basic Directory Structure

A simple, well organized Python project typically has a directory structure that looks something
like this:

```
myproject/
├── myproject/
│   ├── __init__.py
│   ├── exceptions.py
│   ├── some_module.py
│   └── some_other_module.py
├── MANIFEST.in
├── README.rst
└── setup.py
```

And here's an explanation of each file and directory:

**`myproject/`**

This is the top-level directory for your **entire project**, not be confused with the directory for
your package which will likely have the same name.

**`myproject/myproject/`**

This is the directory for your actual Python package. This is where all of your code should go that
is meant to be installed as part of the package.

**`myproject/myproject/__init__.py`**

This file is what makes the directory into a Python package. A Python package is simply a directory
that contains a file with this name. This file should typically be fairly empty. Sometimes you'll
see people put all of their code in here, but that is frowned upon.

**`myproject/myproject/exceptions.py`**

A common convention in Python projects is to use a file with this name to define project-specific
exception classes.

**`myproject/myproject/some_module.py` and `myproject/myproject/some_other_module.py`**

These are just some example files that would contain the actual code for your project. These are
also Python modules. A Python module is simply an importable Python file.

**`myproject/MANIFEST.in`**

This file defines non-Python files to include in your source distribution. More on this below.

**`myproject/README.rst`**

This is your standard README. Python projects conventionally use reStructuredText as a markup
language, mainly because that's the only markup format that PyPI supports.

**`myproject/setup.py`**

This file essentially contains meta-data about your package. It is used to actually install your
package. More on this below.

## Learn by Example

Sometimes the best way to learn best practices is to look at examples of them. Below is a list of
Python projects that are widely considered be some of the most well organized and well written
Python projects out there. If you're project looks significantly different from these, there's a
good chance you're doing something wrong.

* **[Flask]** — A microframework based on Werkzeug, Jinja2 and good intentions
* **[Werkzeug]** — A flexible WSGI implementation and toolkit
* **[Click]** — Command Line Interface Creation Kit
* **[Requests]** — Python HTTP Requests for Humans™
* **[Django]** — The Web framework for perfectionists with deadlines

## Packaging

The best resource for learning about Python packaging best practices is the
**[Installation & Packaging Tutorial]** maintained by the Python Packaging Authority (PyPA).
Reading that entire guide is highly recommended. Below are some clarifications about Wikia-specific
guidelines related to packaging. These assume you have already read the guide.

* We use [virtualenv] as opposed to pyvenv.
* We use source distributions as opposed to wheels.
* Wikia has its own private PyPI server. Check out the [README in the python-cheeseshop repo] for
  details on how to use it.

### Managing Dependencies

You may have noticed that there are two different ways to list the dependencies of your project: in
the `install_requires` setting in your `setup.py` file or in a "requirements file" conventionally
called `requirements.txt`. The most important difference between the two is that the
`install_requires` setting is automatically read recursively for all dependencies. That means that
if your package depends on another package and that package depends on a third package, listing your
dependencies in your `setup.py` file will cause all of those dependencies to get installed. In
contrast, there is nothing that _automatically_ reads requirements files. You have to explicitly
install them using `pip install -r requirements.txt`.

So why would you even use requirements files? The main reasons are because they can be used to
easily save and recreate an entire environment, they can be more flexible, and some people think
they're good for listing dev only dependencies. However, requirements files are almost never needed,
and they often cause more confusion and make it harder to manage dependencies. And if you want to
list dev only dependencies, using [setuptools extras] is often a better solution. For example, you
can create an extra called "dev" and install those dependencies using `pip install -e .[dev]`. So
the recommendation is to not use requirements files unless they provide a significant benefit for
your particular situation.

Another best practice related to managing dependencies is "version pinning". This means that you
should explicitly list the versions of all your dependencies with something like
`SomePackage==1.0.4`. People are often tempted to just list the package name or something like
`SomePackage>=1.0.4`. However, this is considered bad practice. If there's a backwards incompatible
change in that other package that breaks your package, all of the sudden people downloading your
package will get a broken version of the code. So you should always "pin" the versions of your
dependencies, and test new versions when they are released. You can easily see which of your
packages are out-of-date by running `pip list -o`.

## Preparing Your Code for Deployment

At Wikia, when we deploy Python packages, we essentially build an isolated, self-contained virtual
environment containing your package, and then _only_ deploy the virtual environment. If you want to
test if your code will work when installed this way, you can do something like this:

```bash
MY_REPO="your-repo"

# Create FRESH copy of your repo
git clone git@github.com:Wikia/${MY_REPO}.git

# Create a virtualenv in a SEPARATE directory
virtualenv --no-site-packages --always-copy ${MY_REPO}-venv
source ${MY_REPO}-venv/bin/activate

# Install your package NOT in editable mode
cd ${MY_REPO}
pip install .

# Get rid of the original code
cd ..
rm -rf ${MY_REPO}

# Test that everything works with JUST the virtualenv
cd ${MY_REPO}-venv
# test stuff...
```

### Configuration

Another important note about deployment is that environment specific configuration should **NOT** be
part of your application. This means that we should be able to build a tarball out of that virtual
environment and deploy that same tarball to any environment and have it automatically work as
expected, because it should read config files put there by Chef. This is a common practice for
deployment: build once, deploy everywhere. You can include a default config in your application, but
it should just contain some reasonable defaults for your applications settings.

You should also read the [configuration section](ScenarioGuide.md#configuration) of the Scenario
Guide.


[Flask]: https://github.com/mitsuhiko/flask
[Werkzeug]: https://github.com/mitsuhiko/werkzeug
[Click]: https://github.com/mitsuhiko/click
[Requests]: https://github.com/kennethreitz/requests
[Django]: https://github.com/django/django
[Installation & Packaging Tutortial]: https://python-packaging-user-guide.readthedocs.org/en/latest/tutorial.html
[virtualenv]: https://virtualenv.pypa.io/en/latest/
[README in the python-cheeseshop repo]: https://github.com/Wikia/python-cheeseshop#readme
[setuptools extras]: https://pythonhosted.org/setuptools/setuptools.html#declaring-extras-optional-features-with-their-own-dependencies
