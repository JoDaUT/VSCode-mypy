# Check types in a Python project with Visual Studio Code

When working in a team, it's important to establish a set of best practices that allows all the members, old and new, to be productive while reducing the number of bugs and time required to understand a block of code. In many cases using static typed languages can be a smart choice for the project, For this purpose, you can configure Python to act like static typed languages.

In this tutorial, we are going to configure real-time static type checking in VS Code for Python projects. I suggest that the setup must show errors only when you explicitly type a variable or function in your code, without affect your previous code.

## Configure Venv

Venv is a Python built-in tool for creating a virtual environment that isolates the project dependencies. It is pretty similar to the Node.js command `npm init`.

If you are on Windows:

    py -m venv venv

    .\venv\Scripts\activate

If you are on Linux or macOS:

    python3 -m venv venv

    venv/bin/activate

## Install Mypy

After that, you must install Mypy. You can use this package to check types in your Python code and set up a set of rules that according to your needs. See the [official documentation] for further information.

To install Mypy paste this line:

    pip install mypy

## Add Gitlens extension

Install [GitLens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens) extension for VS Code.

Next, you have to add these settings in your local settings.json

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

`--strict` option enables strict type checking. Although, it can be over strict in some cases. When you have existing code, and you don't want to change it because you know it works. Furthermore, so many errors can be distracting for developers. The flags below customize this strict mode to make it less strict.

`--ignore-missing-imports` suppresses error messages about unresolved imports. In Django, some imports can raise a warning without this flag. Only use it if your project contains warnings that you know aren't a real problem.

`--follow-imports=silent` turn off type checking in imported modules. Many libraries were not developed with static type checking in mind, so it's better to suppress type checking in imported modules.

`--show-column-numbers` shows column numbers in error messages.

`--allow-untyped-defs` suppress errors if you have non-typed functions in Python.

`--allow-subclassing-any` permits subclassing a value of type Any. Some frameworks can throw these errors with Mypy, therefore if you have them, add this line.

`--allow-untyped-calls` allow you to invoke non-typed functions in your code.

With this configuration, you should have no issues by adding type checking gradually in your project. If this configuration is not the best for your case, try other configurations. Mypy is a powerful tool and has a lot of options that you can use.

## Let's try some examples

Create a `example.py` in the root of your project.
Add this code into that file.

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

You should see a lot of errors because `sum` expect two int parameters, but then a float value is passed to the function. However, `sub` has no type definition, no errors are displayed.

![example](https://user-images.githubusercontent.com/47344349/158103410-7259f25d-b20c-49a8-9861-b5095e33aaa7.png)


This is really powerful, because you can see errors only if you begin typing functions and variables, and it will not conflict with your existing code and libraries you require.

### Create a Django project

Let's test our setup in a more complex scenario. Let's start by making a new Django project. This framework was not designed to be a static typed tool, using only the `--strict` mode in `settings.json` will result in a lot of errors. If you added all the rules previously explained, you shouldn't have issues after creating a project.

To create a new project:

    pip install django
    django-admin startproject myapp
    cd myapp
    py manage.py startapp clients

Now let's add some code that generates errors.

In your `client.views.py`

```python
from urllib.request import Request
from venv import create
from django.http import JsonResponse
from django.shortcuts import render

# Create your views here.


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

In your `clients.urls.py` you should see a lot of errors. However, there are no issues in your Django imports and created code, because it will only check when you type variables and functions.

![Django errors](https://user-images.githubusercontent.com/47344349/158103955-4cb6cd43-dbae-4369-83ff-52bfa606e1df.png)



# Conclusions

With this configuration, you can add types as much as you need whenever you need them. The best part is you can code without worrying about type errors warnings in legacy packages or frameworks that doesn't support types definition out of the box, such as Django.

# Bibliography

- [https://dev.to/tusharsadhwani/the-comprehensive-guide-to-mypy-561m#using-mypy-in-vscode](https://dev.to/tusharsadhwani/the-comprehensive-guide-to-mypy-561m#using-mypy-in-vscode)
- [https://dev.to/kracekumar/type-check-your-django-application-1gba](https://dev.to/kracekumar/type-check-your-django-application-1gba)
- [https://mypy.readthedocs.io/en/stable/](https://mypy.readthedocs.io/en/stable/)
