---
toc: true
layout: post
description: A minimal project structure for a data science portfolio.
comments: true
sticky_rank: 1
categories: [markdown, cookiecutter, data-science, portfolio, template, python]
title: Automate Creation and Use of a Structured Data Science Portfolio Project
---
# Automate Creation and Use of a Structured Data Science Portfolio Project

## Background and Motivation

### The Use-Case

Starting a new data analytics project can be stressful, repetitive or annoying. Actually, it can be all of these combined. We are thinking about the business use-case (user, client, etc.) for the project we're about to begin working on. We want it to be our implementation of a real-world use-case and we're trying to correctly identify the business user (client) and their requirements. Additionally, we're dealing with constraints imposed by data, such as when the data will be available to use, if there is enough of it and what supplementary datasets are needed.

### The Problem

If we've got a previous project structure, it becomes tempting to copy-paste that structure into a new folder and begin changing files. This becomes annoying because we want to focus on the use-case for the project, and the related issues mentioned above, and not the drudgery of 56 mouse-clicks to *set up one project*. We know that this process has to be repeated for the next project. This process is susceptible to errors, missing files and missing content.

### Build on Existing Proven Solutions

The recommended approach to creation of a new data science project is to spend the time **once** to build a project template that we can use across multiple projects and automate this project set-up process. The [`cookiecutter-data-science` project template](https://github.com/drivendata/cookiecutter-data-science/tree/v2) does precisely this. It can be used to effortlessly and reliably start up a new project.

### The Need for Customizing and Extending An Available Solution

But what happens if we want to go beyond this and enhance the functionality of such a template. For example, if we want to
- create a project directory structure, pre-populate analysis (sample) code to use this structure and then run this code to see the output
- specify logical code-formatting requirements and run checks on any pre-populated code in our project
- set up all the Python virtual environments we need
- set up a [continuous-integration](https://www.atlassian.com/continuous-delivery/continuous-integration) workflow to
  - verify that our end-to-end workflow (including code-formatting checks) runs after we have made any changes to our code base
  - ensure our analytical tools (Python libraries) stay up to date

then we need to expand on the `cookiecutter-data-science` template. That project has provided us with a solid base, but it is up to us to customize it to our preference. This is why the `cookiecutter-portfolio` template ([link](https://github.com/elsdes3/cookiecutter-portfolio)) was created.

### A Candidate Solution

`cookiecutter-portfolio` goes a little further than `cookiecutter-data-science`. It implements the four enhancements discussed above (and a few others too), and makes opinionated modifications to the solid base provided by `cookiecutter-data-science` in order to accomplish this. Its underlying goals are to
- make several decisions for the user in preparing minimal pre-populated content in files
  - two sample notebooks for data analysis
  - baseline implementation of code-formatting checks
- configure programmatic execution of analysis during [CI using Github Actions](https://docs.github.com/en/actions/automating-builds-and-tests/about-continuous-integration)
- run the probided minimal (sample) analysis code from start to finish, and perform code-formatting checks

This template can be used to automate the set-up and continuous development of a Python-based data analytics project that a user wants to host on [Github](https://github.com/).

### About this blog post

This blog post is intended to explain the following about the `cookiecutter-portfolio` template
- how to use it to create a new data analytics project
- what are the files and folders produced by the template, and what is their purpose
- how does it differ from `cookiecutter-data-science`

## Using the `cookiecutter-portfolio` template

### Step-by-Step Instructions to create a project using `cookiecutter-portfolio`

Install the `git` system package from [here](https://git-scm.com/downloads).

Next, install the necessary Python packages
- `tox` ([link](https://tox.wiki/en/latest/))
  - this will be used to manage virtual environments
- `cookiecutter` ([link](https://cookiecutter.readthedocs.io/en/latest/))
  - this is required since the `cookiecutter-portfolio` template was created using the `cookiecutter` Python library

using
```shell
pip3 install tox cookiecutter
```

A templated portfolio project can be created from this template by cloning the source code from github
```shell
git clone https://github.com/elsdes3/cookiecutter-portfolio
```

Next, change into the root directory of the project
```shell
cd cookiecuter-portfolio
```

and run the [`make`](https://www.gnu.org/software/make/) [target](https://www.gnu.org/software/make/manual/html_node/Standard-Targets.html) `build` using
```shell
make build
```

These instructions have only been verified on Linux and MacOS systems. Currently, verification is not available on Windows-based systems.

### Walkthrough of the portfolio project that is created

#### **Document our analysis in Jupyter notebooks**

Initial development of data science analysis is commonly done using [Jupyter notebooks](https://jupyter-notebook-beginner-guide.readthedocs.io/en/latest/what_is_jupyter.html), which are [**documents** consisting of code (including multiple programming languages), rich text, figures, images, links, etc](https://jupyter-notebook-beginner-guide.readthedocs.io/en/latest/what_is_jupyter.html#notebook-document). In this template, notebooks are named using a numbering system. A two-digit number is specified in the prefix of the notebook filename. Notebooks should be run in chronological order of their filename. One approach to using multiple notebooks is to limit each notebook to performing a single step in our overall workflow.

A logical sequence of workflow steps and their corresponding notebooks might be the following
- retrieve raw data
  - `01_get_raw_data.ipynb`
- process raw data
  - `02_process_data.ipynb`
- explore processed data
  - `03_explore_processed_data.ipynb`
- analyze processed data
  - `04_analyze_processed_data.ipynb`

This template provides sample code for the first two of these steps (notebooks, [1](https://nbviewer.org/github/elsdes3/cookiecutter-portfolio/blob/main/cookiecutter-project/%7B%7Bcookiecutter.repo_name%7D%7D/01_get_data.ipynb) and [2](https://nbviewer.org/github/elsdes3/cookiecutter-portfolio/blob/main/cookiecutter-project/%7B%7Bcookiecutter.repo_name%7D%7D/02_process_data.ipynb)).

#### **Programmatically execute our notebooks**

How can we be sure that our code runs from start to finish? When we're sharing code in our portfolio project, we want to be sure that our analysis is free of errors. In a data science portfolio project, this amounts to having several notebooks that run from start to finish. If each notebook captures a single step in our analysis workflow, then we would want to run all notebooks in succession and verify that we don't get any errors. If we define notebook inputs using `papermill_runner.py`, then this file will allow us to programmatically execute a notebook using Python. The benefit of this is that we can automate the execution of an entire workflow (consisting of several notebooks). If we've taken care to ensure our analysis does indeed run as we expect it to, from start to finish, then this should make us confident in having others also run our code from themselves and follow the logic we have implemented in our code. Using `papermill` is a convenient approach to be sure that our portfolio project contains Python code that is free of errors.

Papermill requires a single cell with Python variables that can be programmatically changed. This cell should be marked with a `parameters` tag. See [these instructions](https://jupyterbook.org/content/metadata.html#adding-tags-using-notebook-interfaces) for adding a tag to a cell. In the sample notebook `01_get_data.ipynb`, [these parameters are](https://nbviewer.org/github/elsdes3/cookiecutter-portfolio/blob/main/cookiecutter-project/%7B%7Bcookiecutter.repo_name%7D%7D/01_get_data.ipynb#user-inputs)
```python
num_data_files = 5
num_rows = 10
column_names = ["A", "B", "C", "D", "E"]
```

and in `02_process_data.ipynb`, [these parameters are](https://nbviewer.org/github/elsdes3/cookiecutter-portfolio/blob/main/cookiecutter-project/%7B%7Bcookiecutter.repo_name%7D%7D/02_process_data.ipynb#user-inputs)
```python
processed_data_file_name = "processed"
```

The `papermill_runner.py` script contains these python parameters in dictionaries for the two sample notebooks provided with this template (`one_dict` and `two_dict` for `01_get_data.ipynb` and `02_process_data.ipynb` respectively). In this script, these two notebook names are assigned to the variables `one_dict_nb_name` and `two_dict_nb_name` respectively.

To add more notebooks (eg. `03_explore_processed_data.ipynb`) to this template, the following changes must be made
- add a new dictionary of input variables (eg. `three_dict`) to `nb_dict_list`
- add a new notebook name (eg. `three_dict_nb_name`) to `nb_name_list`

#### **Make it easier for others to run and use our analysis**

Can other users run our data analysis online? It is convenient for others to run our code if they don't need to download the project locally. It shows we care about visitors looking to explore our portfolio projects. We can facilitate this using the MyBinder service. A visitor to our project on Github would need to specify the environment using `environment.yml`. So, a sample version of this file is provided by this template. The visitor can then follow instructions in the [basic example from the MyBinder documentation](https://mybinder.readthedocs.io/en/latest/introduction.html#a-binder-example) to create an online computing environment to run the notebooks in our portfolio project.

#### **Correctly handle the use of Python virtual environments**

This is a template based on Python. It is best for Python code to run in a self-contained and independent environment from the host system. One way to achieve this is through the use of a Python virtual environment. We will use the [`tox` Python library](https://tox.wiki/en/latest/) to manage all our Python virtual environments. These are specified in `tox.ini`, which will act as the central hub for installing all Python dependencies required for us to run the Python code that preforms the data analysis in our propject. This file will install the Python packages specified in `requirements.txt`.

#### **Keep sensitive info and files away from version control systems (`git`)**

For obvious reasons, our portfolio should not contain information like passwords. It makes us seem uninterested in paying attention to details and unreliable with sensitive data. `cookiecutter-portfolio` assumes that `git` ([link](https://www.atlassian.com/git/tutorials/what-is-git)) will be used for version control (and that we'll be posting our portfolio projects to [GitHub](https://kinsta.com/knowledgebase/what-is-github/)). To ensure we don't commit files containing sensitive or unnecessary information to `git`, a `.gitignore` file is provided. The contents of this file are specific to Python and are taken from [gitignore.io](https://www.toptal.com/developers/gitignore). We should add files that we don't want `git` to track to this `.gitignore` file.

#### **Ensure our code is properly formatted before it is uploaded to Github**

When we're publishing code for a portfolio project, we want the code to be properly formatted. As an example, we don't want our custom Python module to contain lines of code that are 350 characters long. There are many other best practices. See [PEP8](https://www.python.org/dev/peps/pep-0008/) and the [Google Style Guide for Python](https://code.google.com/archive/p/soc/wikis/PythonStyleGuide.wiki) for inspiration. This is especially important for collaborative projects. Code formatting is enforced using `.pre-commit-config.yaml`. This file will prevent code from being pushed to `git` if it does not meet requirements such as newline file-endings ([1](https://thoughtbot.com/blog/no-newline-at-end-of-file), [2](https://www.quora.com/Why-is-it-good-practice-to-add-a-newline-to-the-end-of-a-file)).

#### **Co-ordinate execution of our workflows using a `Makefile`**

Running notebooks programmatically, manually running our code formatting checks and starting the jupyter notebook server for local development are initiated by their respective CLI commands. We don't want to remember this every time we need to update our project, push changes to github, etc. For example, if we make a change to the second notebook in our project then we should be able to quickly run all the notebooks in the project to ensure that the chage we just made has not caused errors elsewhere in the analysis. Papermill is a quick way to do this but we need to remember the command to use this tool. Over time, we will get better at remembering these commands. But what about visitors to our portfolio project on Github? Should we also expect that they too will know the exact commands that are needed to perform these tasks in our project? A better approach is to simplify this as much as possible by reducing the amount of command-line code to be called in order to perform a task. We will do this with `make` ([link](https://www.gnu.org/software/make/manual/make.html)). On most Linux distributions, `make` comes pre-installed. Windows users can download `cmake` from [here](https://cmake.org/download/) and [follow this video to install it](https://www.youtube.com/watch?v=8_X5Iq9niDE).

To minimize user input, a `Makefile` ([link](https://www.gnu.org/software/make/manual/make.html#Introduction)) is provided which runs the relevant command in each Python environment. The following [`make` targets](https://www.gnu.org/software/make/manual/make.html#Phony-Targets) are provided
{% include info.html text="<b>build</b>" %}
...starts a Jupyter notebook server
{% include info.html text="<b>precommit</b>" %}
...manually runs all code-formatting checks defined in `.pre-commit-config.yaml`
{% include info.html text="<b>ci</b>" %}
...programmatically runs all notebooks in the project
{% include info.html text="<b>clean</b>" %}
...cleans up [Python artifacts](https://www.python.org/dev/peps/pep-3147/#python-behavior)

#### **Extract and Outsource code into Python modules**

We'll now focus on the folders that are created, excluding the folder with code for our custom Python package. Different types of files will be saved in these folders. Below is a quick overview of each folder

- `data/raw`
  - raw data that is retrieved in `01_get_data.ipynb` will be stored in the `raw/` sub-folder within `data/`
- `data/processed`
  - data that is processed using `02_process_data.ipynb` will be stored in the `processed/` sub-folder within `data/`
- `models`
  - any trained machine learning models should be serialized ([`scikit-learn`](https://scikit-learn.org/stable/modules/model_persistence.html), [`keras`](https://www.tensorflow.org/guide/keras/save_and_serialize#how_to_save_and_load_a_model), [`spaCy`](https://spacy.io/usage/saving-loading)) to this location on the disk
- `executed_notebooks`
  - when `papermill` executes a jupyter notebook, it saves a copy of the executed notebook in this folder
- `references`
  - here we can store whitepapers, research papers, etc. from our background research about the topic of our analysis
- `reports/figures`
  - if we choose to save figures we generated in the Jupyter notebooks to disk, then we should export them to the `figures/` sub-folder within `reports/`

Finally, we'll discuss the contents of the Python package that is created. The default name for this package is specified in `config.yaml` to be `{% raw %}{{cookiecutter.package_name}}{% endraw %}`. For more discussion about package naming and structure see the following links
- [The Hitchhiker's Guide to Python](https://docs.python-guide.org/writing/structure/)
- [Github issue in a repository of the Python Packaging Authority](https://github.com/pypa/packaging.python.org/issues/320)
- [Documentation by Python Packaging Authority](https://packaging.python.org/en/latest/tutorials/packaging-projects/)
- [Blog Post on the Medium publishing platform](https://medium.com/analytics-vidhya/explicit-understanding-of-python-package-building-structuring-4ac7054c0749)
- [Question on Stackoverflow](https://stackoverflow.com/questions/193161/what-is-the-best-project-structure-for-a-python-application)

Inside this package are
- four Python sub-packages ([1](https://pythonhosted.org/PyBindGen/module.html#modules-and-sub-modules), [2](https://stackoverflow.com/a/70027834/4057186), [3](https://stackoverflow.com/a/63906462/4057186))
- one blank file `__init__.py` file
- one Python module `utils.py`

`__init__.py` makes Python recognize the contents of the package (`{% raw %}{{cookiecutter.package_name}}{% endraw %}` folder) as Python modules. This means we can import modules in the sub-packages into a Python script or Jupyter notebook (with a Python kernel) using, as an example, the following approach to use the `visualization` sub-package
```python
from {% raw %}{{cookiecutter.package_name}}{% endraw %}.visualization import visualize as vis
```

This is similar to how we would have [imported a module from the `numpy` Python package](https://stackoverflow.com/a/54947660/4057186)
```python
from numpy import sum as np_sum
```

One of the main benefits of extracting common code from our Jupyter notebook and placing it in such modules is to keep the Jupyter notebooks as focused as possible without distracting the reader. Read more about the benefits of refactoring code [here](https://www.geeksforgeeks.org/7-reasons-why-code-refactoring-is-important-in-software-development/). The `cookiecutter-data-science` template provides blank modules in each sub-package.

In terms of re-factoring code into modules, the `cookiecutter-portfolio` template
- adds two sample modules to the `data` sub-package to demonstrate basic examples of
  - retrieving (`{% raw %}{{cookiecutter.package_name}}{% endraw %}/data/load_data.py`) raw data
  - processing (`{% raw %}{{cookiecutter.package_name}}{% endraw %}/data/process_data.py`) raw data
- adds one sample module in `{% raw %}{{cookiecutter.package_name}}{% endraw %}/utils.py` with a few custom utilities for exploring data stored in `DataFrame`s ([link](https://www.w3schools.com/python/pandas/pandas_dataframes.asp))
- shows examples of best-practices at developing custom modules using
  - [docstrings](https://www.python.org/dev/peps/pep-0257/) for all methods in the sample modules
  - Python [type-annotations](https://docs.python.org/3/library/typing.html) for all parameters in methods in the sample modules

  both of which improve the readability of the content in these modules

Below is a brief overview of the four Python sub-packages and the Python module in `{% raw %}{{cookiecutter.package_name}}{% endraw %}`
- `{% raw %}{{cookiecutter.package_name}}/data{% endraw %}`
  - contains three modules to retrieve and process raw data
- (optional) `{% raw %}{{cookiecutter.package_name}}{% endraw %}/features`
  - contains a module to build features for the machine learning (ML) step of a project (if present)
- (optional) `{% raw %}{{cookiecutter.package_name}}{% endraw %}/models`
  - contains two modules to train a ML model and make predictions with the trained model, in the ML step of a project (if present)
- `{% raw %}{{cookiecutter.package_name}}{% endraw %}/visualization`
  - contains a module to visualize processed data
- `{% raw %}{{cookiecutter.package_name}}{% endraw %}/utils.py`
  - a Python module containing convenience utilities for exploring a `DataFrame`

#### **Licensing**

Can users use our analysis in their own portfolios? If our analysis is useful, this would be a great way to get some recognition for our hard work. An open-source license is provided to allow visitors to understand how they can use the code in our portfolio project and whether we need them to give us credit. To get started with using open source licenses for data science projects, see [this blog post](https://towardsdatascience.com/a-data-scientists-guide-to-open-source-licensing-c70d5fe42079).

#### **Customize the `cookiecutter-portfolio` template**

The `cookiecutter-portfolio` template was developed with the intention of being used as-is with no need for changes. A project will be created and the two sample notebooks will be programmatically executed allowing us to immediately start developing code in `01_get_data.ipynb`.

If we would like to make changes to the `cookiecutter-portfolio` template, then the above walkthrough has provided us with a baseline understanding of the folder structure of the project that the template produces for us. This knowledge will help us to further personalize (modify) this template to suit our own custom workflows. Refer to the [documentation for the `cookiecutter` Python package](https://cookiecutter.readthedocs.io/en/latest/) for help with the basics of templating using `cookiecutter`. In particular, the sections [**Getting to Know Cookiecutter**](https://cookiecutter.readthedocs.io/en/latest/tutorial1.html) and [**Create a Cookiecutter From Scratch**](https://cookiecutter.readthedocs.io/en/latest/tutorial2.html) are useful starting points.

If we make any changes, then we can quickly check that the template is still rendered correctly, code formatting checks still pass and that the execution of the sample notebooks (in the template folder) runs through to completion by [simply running the following from the root folder](https://github.com/elsdes3/cookiecutter-portfolio#usage) (like we saw in the **Usage** section above)
```shell
make build
```
That's all. If the changes we made to the template were correctly implemented then everything should run through without any errors.

## Differences between `cookiecutter-portfolio` and `cookiecutter-data-science`

This section of the blog post is only needed to compare `cookiecutter-portfolio` to the Python `cookiecutter` it is based on (`cookiecutter-data-science`) and understand the differences. This section does not affect how to use the `cookiecutter-portfolio` template.

As was mentioned earlier, `cookiecutter-portfolio` is based on the `cookiecutter-data-science` template. We will now discuss how `cookiecutter-portfolio` is different from `cookiecutter-data-science`. This discussion will help to clarify the need for creating this template instead of using `cookiecutter-data-science`.

### Changes in the created project template relative to `cookiecutter-data-science`

We will first discuss changes made to the template itself. This means any changes inside the `{% raw %}{{cookiecutter.repo_name}}{% endraw %}` folder.

We will start by listing the files and folders from the `cookiecutter-data-science` template that were deleted
1. The `notebooks` folder is deleted. The notebooks will be placed directly in the root of the project.
2. `docs` is deleted. This feature was aimed at adding documentation for the {% raw %}`{{ cookiecutter.module }}`{% endraw %} Python module. In our case, for simplicity, we will not offer this feature with the template but it can be easily brought back later if required.
3. `.env` is removed from the root of the project. This file is used to store credentials that will be injected as environment variables using the `python-dotenv` library, which will iteratively walk up through the file system, starting from this project's root directory, until it finds this file. So, the file can be located one level above this project's root. The benefit of this is that we can re-use the same credentials for multiple projects.

We'll now discuss files that have been changed
1. `.gitignore` is [modified](https://github.com/drivendata/cookiecutter-data-science/issues/32#issuecomment-481078249) such that `data/` and its sub-folders (**excluding contents**) are not ignored by `git`. This means, `data/` and its (empty) sub-folders will be kept on github and won't have to be manually created after we pull code from the repository.
2. `tox.ini` is modified to manage all Python virtual environments required for this project. See a basic `tox.ini` configuration file [here](https://tox.wiki/en/3.24.5/example/basic.html#a-simple-tox-ini-default-environments). See the [`tox` documentation](https://tox.wiki/en/latest/) for more details.
3. `{% raw %}{{ cookiecutter.module_name }}{% endraw %}`
   - renamed to `{% raw %}{{ cookiecutter.package_name }}{% endraw %}` to be in line with standard Python conventions about the differences between packages and modules ([1](https://www.learnpython.org/en/Modules_and_Packages), [2](https://stackoverflow.com/a/70027834/4057186))
   - renamed to `{% raw %}{{cookiecutter.package_name}}{% endraw %}` since `.pre-commit-config.yaml` could not correctly detect the leading and trailing space in the name of this folder
   - `data`
     - two sample Python sub-modules `load_data.py` and `process_data.py` [^1] are added
   - `utils.py`
     - a few simple Python methods are included in this module to support brief exploration of `DataFrame`s
4. More details have been added to the provided `README.md` file.
5. A set of commonly-used Python libraries have been added to `requirements.txt` [^1].

The following files have been added
1. `environment.yml` is provided to support launching or sharing the project on the [MyBinder](https://mybinder.readthedocs.io/en/latest/introduction.html#a-binder-example) service.
2. `papermill_runner.py` is added to support executing notebooks. This is done using the [`papermill` Python API](https://papermill.readthedocs.io/en/latest/usage-execute.html#execute-via-the-python-api). See the [papermill documentation](https://papermill.readthedocs.io/en/latest/) for more details.
3. `.pre-commit-config.yaml` is added to ensure files and code follow proper formatting (eg. code linting) before every commit to `git`. See the [`pre-commit` Quick start](https://pre-commit.com/#quick-start) to get started using this framework.
4. The `executed_notebooks` folder is added as the location where notebooks executed with `papermill` will be saved.
5. Two sample notebooks to replicate downloading (`01_get_data.ipynb`) and processing (`02_process_data.ipynb`) raw data [^1].

The following files and folders are not been changed (except for sorting imports in files)
1. `setup.py`
2. `data` and its sub-folders
3. `models`
4. `references`
5. `reports`
6. `{% raw %}{{cookiecutter.package_name}}{% endraw %}`
   - `data/make_dataset.py`
   - `features`
   - `models`
   - `visualization`

Besides differences in the directory structure and file contents, `cookiecutter-portfolio` provides and runs two sample notebooks that are provided. By comparison, `cookiecutter-data-science` doesn't offer or run notebooks after rendering the template. Offering a sample notebook makes it a little easier for a user to start developing code using the created Python modules in {% raw %}`{{cookiecutter.package_name}}`{% endraw %} and the templates directories (eg. `data/raw`, `data/processed`, `executed_notebooks`) after generating the template. The user can quickly adapt this code for use in future steps in the workflow (i.e. in other notebooks).

### Changes at the project level relative to `cookiecutter-data-science`

Next, at the repository level, the implementation of `cookiecutter-portfolio` is fundamentally different to that in the `cookiecutter-data-science` template. Specifically, the implementation of this project (`cookiecutter-portfolio`) is purely that of a template that only follows the structure of a [Python `cookiecutter`](https://cookiecutter.readthedocs.io/en/latest/tutorial2.html). By comparison, the `cookiecutter-data-science` template can be installed in editable mode - this is not supported by `cookiecutter-portfolio`.

`tox` is used for managing python virtual environments at the repository level so it is added to `requirements.txt`. The other requirements file, `requirements-test.txt`, is only used to specify Python packages to use in the unit testing virtual environment. Tests are implemented exclusively using [`pytest-cookies`](https://pytest-cookies.readthedocs.io/en/latest/), a framework designed specifically to test generation of templates created using `cookiecutter`.

As such, two significant changes have been made
1. `tests`, {% raw %}`{{cookiecutter.repo_name}}`{% endraw %} and `hooks` are placed inside a `cookiecutter-project` sub-folder. This is done so that the user can generate this (`cookiecutter-portfolio`) template without any command-line prompts. To do this, the documentation from the `cookiecutter` project was used to
   - providing the command line arguments required to [suppress command-line prompts](https://cookiecutter.readthedocs.io/en/latest/advanced/suppressing_prompts.html)
     - `--no-input`
     - `--config-file`
   - [specify a user configuration file (`config.yaml`)](https://cookiecutter.readthedocs.io/en/latest/advanced/user_config.html)
     - by providing this file, the user can directly modify it following the instructions in the [README.md file](https://github.com/elsdes3/cookiecutter-portfolio/blob/main/README.md) (see [**Usage**](https://github.com/elsdes3/cookiecutter-portfolio/blob/main/README.md#usage) and [**Notes**](https://github.com/elsdes3/cookiecutter-portfolio/blob/main/README.md#notes)) rather than needing to manually create these files and populate their contents appropriately.
2. Several files in the template are not required in `cookiecutter-portfolio`
   - `ccds`  [^1]
   - `ccds.json` [^2]
   - `dev-requirements.txt`
     - a standard development environment is not supported here
   - `netlify.toml`
     - [Netlify](https://www.netlify.com/) is not supported here
   - `setup.py`
     - this template does not support [installation in editable mode](https://pip.pypa.io/en/stable/cli/pip_install/#local-project-installs) so use of this file is not supported here

## Summary

With the `cookiecutter-portfolio` template, we have access to an project built on the strong foundations of `cookiecutter-data-science` with
- programmatic execution of data analysis
- pre-populated code-formatting framework
- virtual environments to manage all Python code
- an implementation of continuous integration to ensure any modifications to our code-base result in
  - analysis that still executes from start to finish
  - code whose [formatting follows best practices](https://www.python.org/dev/peps/pep-0008/)

Combined, these add a layer of automation to the strong base offered to us by `cookiecutter-data-science`.

## Contributing to `cookiecutter-portfolio`

Contributions to `cookiecutter-portfolio` are very welcome, and are greatly appreciated. Every little bit helps, and credit will always be given.

If you do decide to use the `cookiecutter-portfolio` template and have recommendations to improve the structure of this template or corrections to mistakes, [create an issue on Github](https://github.com/elsdes3/cookiecutter-portfolio/issues/new).

Tests can be run with
```python
make test clean-tests
```
Please make sure that all tests are green before submitting a [pull request on Github](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests).

[^1]: These are only provided as a starting point for loading and processing data respectively. These are not repersentative of any real-world workflow.

[^2]: It is not indended that `cookiecutter-portfolio` should be called using the command `ccds`.
