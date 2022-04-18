# Python type checking with Visual Studio Code

When working in a team, it's important to establish a set of best practices that allows all the developers, old and new, to be productive while reducing the number of bugs and time required to understand a block of code.

If you are working with other developers and need to be explicit with function signatures and variables, using static typed languages can be a good choice for your project. Since Python 3, you can add static types, but the IDE and code editor does not display any errors if you assign the incorrect value to a variable.

In this tutorial, we will set up Python to behave like a static typed languages. We will configure real-time static types checking in VS Code for Python projects. I prefer that the setup only show errors when you explicitly add the type of variable or function parameter in your code, while still allowing for normal variables.

## Configure Venv

Venv is a Python built-in tool for creating a virtual environment that isolates the project dependencies, so you can install dependencies only for that specific project without affecting other projects in your computer.

### Windows setup

    py -m venv venv

    .\venv\Scripts\activate

### Linux or MacOS setup

    python3 -m venv venv

    venv/bin/activate

## Install Mypy

Next, install [Mypy](https://mypy.readthedocs.io/en/stable/). This package checks types in your Python code and define a set of rules according to your needs.

### Setup

    pip install mypy

## Add Gitlens extension

Install [GitLens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens) extension for VS Code.

Next, add these settings in your local `settings.json`.

```json
{
  "python.linting.mypyEnabled": true,
  "python.linting.mypyArgs": [
    "--ignore-missing-imports",
    "--follow-imports=silent",
    "--show-column-numbers",
    "--allow-untyped-defs",
    "--allow-subclassing-any",
    "--allow-untyped-calls",
    "--strict"
  ]
}
```

`--strict`: This option enables strict type checking. Although, it can be over strict in some cases. When you have existing code, and you don't want to change it because you know it works. Furthermore, so many errors can be distracting for developers. The flags below customize this strict mode to make it less strict.

`--ignore-missing-imports`: Suppresses error messages about unresolved imports. In Django, some imports can raise a warning without this flag. Only use it if your project contains warnings that you know aren't a real problem.

`--follow-imports=silent`: Turn off type checking in imported modules. Many libraries were not developed with static type checking in mind, so it's better to suppress type checking in imported modules.

`--show-column-numbers`: Shows column numbers in error messages.

`--allow-untyped-defs`: Suppress errors if you have non-typed functions in Python.

`--allow-subclassing-any`: Permits subclassing a value of type Any. Some frameworks can throw these errors with Mypy, therefore if you have them, add this line.

`--allow-untyped-calls`: Allows you to invoke non-typed functions in your code.

You should not have any issues by adding it gradually to your project with this setup. Mypy has a lot of options, feel free to try other configurations for your specific case.

## First simple example

Create a `example.py` in the root of your project.
Add this code (yo lo dejaria hasta aqui) into that file.

```python
def sum(n1: int, n2: int) -> int:
    return n1 + n2


def sub(n1, n2):
    return n1-n2


result = sum(1, 2.5)
print(result)
result = sub(1, 2.5)
print(result)

```

As you can see in the image below, a lot of errors are displayed because `sum` expect two int parameters, but is given a float value. However, `sub` lacks a type definition, no errors are displayed.

![First Simple Example](https://user-images.githubusercontent.com/47344349/163740741-0162f7e2-2cf4-4059-a319-442ae0243aa1.png)


This is really powerful. Errors are displayed only in functions and variables that are required, and it will not interfere with your existing codebase.

### Django example

Let's try in a more complex scenario using a Django project. Django is a framework to build web applications. See the docs [here](https://docs.djangoproject.com/en/4.0/). But don't worry, there is no need to know it to follow this section.

Keep in mind that Django is not a static typed Framework. Using only the `--strict` mode in `settings.json` will result in a lot of errors. If you configured the `settings.json` file as previously described, you shouldn't have any issues.

To create a new project:

```bash
    pip install django
    django-admin startproject myapp
    cd myapp
    py manage.py startapp clients
```

Now let's add some code that generates errors.

In your `client.views.py`

```python
from django.http import JsonResponse
from django.shortcuts import render
from urllib.request import Request
from venv import create


def createGreeting(name: str) -> str:
    return f'Hello {name}!!!!'


def getPower(n):
    x = 42 == 'no'
    return n**2


def index(request):
    message = createGreeting(3, 'asdf')
    message = createGreeting(100)
    message: int = 'asdf'
    message: str = 1234
    n = getPower('asdf')
    return JsonResponse({'message': message})

```

Despite many errors appeared in `clients.urls.py`, the project is still running. Django imports and default code are fine because it will only check variables and functions that you manually type.

![02](https://user-images.githubusercontent.com/47344349/163740770-08bce502-8666-488a-9817-409eb3a91262.png)


# Conclusions

During this tutorial, we learned how to configure Visual Studio Code to check static types in Python projects and display errors in development.

Remember that you can be strict as needed, but be careful. Here is a list of considerations to keep in mind:

Pros:

- If working in a team it reduces cognitive effort to understand how to use a piece of code.
- Increases productivity because the code documents itself.
- Improves code documentation and readability.
- Reduce redundant tests
- Constant real-time feedback.
- Reduces development errors
- It is customizable.

Cons:

- Increases the complexity of your project.
- Many Python developers doesn't like typing code.
- It is impossible to be certain that a function will return what it says.
- The less restrictive your configuration is, the fewer errors are displayed.

Use it to develop new libraries and complex modules that must be thoroughly tested. It's not recommended for typing third-party libraries or legacy codebases (unless strictly necessary), because it could cause a lot of problems and time spent. Feel free to try with different configurations. In the run, you and your team will be grateful for incorporating this tool into the workflow.

# Bibliography

Tushar Sadhwani - 2021 - [The Comprehensive Guide to mypy](https://dev.to/tusharsadhwani/the-comprehensive-guide-to-mypy-561m#using-mypy-in-vscode)

Kracekumar - 2021 - [Type Check Your Django Application](https://dev.to/kracekumar/type-check-your-django-application-1gba)
