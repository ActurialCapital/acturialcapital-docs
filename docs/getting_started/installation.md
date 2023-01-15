# Installation

## Prerequisites

Before you get started, you're going to need a few things:

* Your favorite IDE or text editor
* [Python 3.10](https://www.python.org/downloads/)
* [PIP](https://pip.pypa.io/en/stable/installation/)
* Install C++:
  * **MacOS**: You need to [install XCode Command Line Tools](https://osxdaily.com/2014/02/12/install-command-line-tools-mac-os-x/). They are required to compile some of Opendesk's Python dependencies during installation.
  * **Windows**: For Windows users, [download Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16), with [additional instructions](https://docs.google.com/presentation/d/0B4GsMXCRaSSIOWpYQkstajlYZ0tPVkNQSElmTWh1dXFaYkJr/edit?usp=sharing&ouid=117107708911390632479&resourcekey=0-HEezB2NFstz1GjKDkroJSQ&rtpof=true&sd=true).

!!! question "Python"
    Opendesk is built on Python. Other dependencies, such as C++, are employed to improve the speed at which the program runs.

## Set up your virtual environment

Regardless of which package management tool you're using, we recommend running the commands on this page in a virtual environment. This ensures that the dependencies pulled in for Opendesk don't impact any other Python projects you're working on.

Below are a few tools you can use for environment management:

* [pipenv](https://pipenv-fork.readthedocs.io/en/latest/)
* [poetry](https://python-poetry.org/)
* [venv](https://docs.python.org/3/library/venv.html)
* [virtualenv](https://virtualenv.pypa.io/en/latest/)
* [conda](https://www.anaconda.com/distribution/)

## Install Opendesk

!!! note "Troubleshooting"
    If any of these methods don’t work, please [raise an issue](https://github.com/ActurialCapital/xyz/issues) with the ‘packaging’ label on GitHub.

prior to using the below commands, you may need to follow the installation instructions for [cvxopt](https://cvxopt.org/install/index.html#) and [cvxpy](https://www.cvxpy.org/install/)).

### With Pip <small style="color:green">recommended</small> { #with-pip data-toc-label="With Pip" }

Installation can be done via pip:

<div class="termy">
  ```console
  $ pip install opendesk
  ---> 100%
  ```
</div>

### With Poetry <small style="color:green">recommended</small> { #with-poetry data-toc-label="with Poetry" }

[Poetry](https://python-poetry.org/) is a relatively new kid on the block but has gained traction due to its ease of use and how it resolves several issues over Conda.

Poetry will set up your virtual environment and install all required packages with one command `poetry install`. In addition, it can build installable packages with `poetry build`, which can be installed by pip. It uses the [PEP Standard pyproject.toml](https://www.python.org/dev/peps/pep-0518/) file to define dependencies and build options for other tools (e.g. pytest arguments).

It respects `.python_version` files, uses a lock file to define specific versions of packages and pip to install them - thus, you have reproducible environments installed quickly.

The only downside is the slight learning curve in changing your workflow.

<div class="termy">
  ```console
  $ poetry add opendesk
  ---> 100%
  ```
</div>

### With Conda

If you don't have Anaconda install yet, follow the steps provided on the [Anaconda installation page](https://docs.anaconda.com/anaconda/install/windows/).

<div class="termy">
  ```console
  $ conda install opendesk
  ---> 100%
  ```
</div>


### With Git 

The alternative is to clone the project:

<div class="termy">
  ```console
  $ git clone https://github.com/ActurialCapital/xyz.git
  $ cd myproject
  $ python setup.py install
  ```
</div>

## Further Reading

A pretty good guide/supporting argument to the recommended setup - [My Python Development Environment, 2020 Edition - Jacob Kaplan-Moss](https://jacobian.org/2019/nov/11/python-environment-2020/).
