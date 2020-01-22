# Minimal Python project

![Build status](https://img.shields.io/travis/com/energizah/minimal-python-project/master)


## Introduction

There are two ways to structure a Python project: "src" layout (pronounced "source") and "classic" layout. The "src" layout has an extra directory layer. That is the layout I prefer and describe here. Two other good guides are http://bit.ly/pypackages (classic layout) and http://bit.ly/pypackaging (src layout). My favorite way to make a Python package in 2020 is with [Poetry](https://poetry.eustace.io/) (`poetry new --src myproject`). But if you want to use the older `setuptools` way, this guide shows how. The tree structure will be the same with Poetry except it replaces `setup.py` with `pyproject.toml`.

## How to use this

1. Create a file tree structure matching the one described below.

2. Create a local packages directory ("virtual environment" or "venv"). On Debian/Ubuntu you might need to first run `sudo apt install python3-dev python3-pip python3-venv` to get the `venv` and `pip` modules.

```
python3 -m venv myenv
```

3. Install your project into the venv in editable mode. Editable mode means you can make changes to your code without reinstalling. There are a couple of exceptions: if you change `setup.py` or create a new subpackage (subdirectory), you'll have to install again.

```
myenv/bin/pip install -e .
```

4. Run the console script `greet` in the venv:

```
myenv/bin/greet
```

or run `__main__.py` as a module:

```
myenv/bin/python -m greetings
```

Don't try to run the app without `-m`, it won't work reliably.

## Tree structure


The `python-greeter/` directory is called the "project root". That should be your shell's working directory whenever you're working on this project.
```
python-greeter/setup.py
python-greeter/src/greetings/app.py
python-greeter/src/greetings/cli.py
python-greeter/src/greetings/__init__.py
python-greeter/src/greetings/__main__.py
```
### `setup.py`
```python
# setup.py

import pathlib

import setuptools

PROJECT_DIR = pathlib.Path(__file__).parent

DISTRIBUTION_NAME = "hello" # The "pip install" name.
VERSION = "0.1.0"
DESCRIPTION = "Greetings."
CONSOLE_SCRIPTS = ["greet = greetings.cli:cli"]
INSTALL_REQUIRES = ["attrs", "click"]

setuptools.setup(
    name=DISTRIBUTION_NAME,
    version=VERSION,
    description=DESCRIPTION,
    packages=setuptools.find_packages("src"),
    package_dir={"": "src"},
    include_package_data=True,
    install_requires=INSTALL_REQUIRES,
    entry_points={"console_scripts": CONSOLE_SCRIPTS},
)
```

### `app.py`
```python
# app.py

def morning():
    print('hello')


def evening():
    print('gooodbye')
```

### `cli.py`
```python
# cli.py
import click

from greetings import app


commands = [
    click.Command("morning", callback=app.morning),
    click.Command("evening", callback=app.evening),
]
cli = click.Group(commands={c.name: c for c in commands})
```

### `__init__.py`
```python
# __init__.py
# Yes, it's blank.
```

### `__main__.py`
```python
# __main__.py
from greetings import cli

cli.cli()
```
