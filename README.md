# Configure Visual Studio Code to check types in a Python Project

## Configure Venv

Venv is a tool for create a virtual environment in Python that isolates the project dependencies. It is very similar to `npm init` command in Node.js.

You can create a virtual environment with venv, that is built-in with Python.

If you are in Windows:

    py -m venv venv
    .\venv\Scripts\activate

_Pending. For MacOS_

## Install mypy

First, you need to install MyPy. This package allows you to check types in your Python code.

    pip install mypy

## VSCode

Install [GitLens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens) extension

Then, you have to add these settings in your local settings.json

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

_Pending. Explain these lines_

Create a `example.py` in your project root path.
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

You should see a lot of erros because `sum` expect two int parameters, but then a float value is passed to the function. However, `sub` has no types definition, so it doesn't show errors.

# Create a Django project

    pip install django
    django-admin startproject myapp
    cd myapp
    py manage.py startapp clients

In your `client.views.py`

```python
from urllib.request import Request
from venv import create
from django.http import JsonResponse
from django.shortcuts import render


def createGreeting(name: str) -> str:
    return f'Hello {name}!!!!'


def index(request):
    message = createGreeting(3, 'asdf')
    message = createGreeting(100)
    message: int = 'asdf'
    message: str = 1234
    return JsonResponse({'message': message})

```

You should see a lot of errors in your `clients.urls.py`. However, your Django imports and Django files are not showing errors, because it only check when you specify types in your variables and functions.

_Error. Some files are showing erros while are opened in the editor. When you close it the error disappear_

# Conclusions

With this configuration you can add types as more as you need, and when you need it. The best part is that you can code without care about type errors warnings in legacy packages or that doesn't provide types definition, such as Django and other Python frameworks

# Bibliography

- [https://dev.to/tusharsadhwani/the-comprehensive-guide-to-mypy-561m#using-mypy-in-vscode](https://dev.to/tusharsadhwani/the-comprehensive-guide-to-mypy-561m#using-mypy-in-vscode)
- [https://dev.to/kracekumar/type-check-your-django-application-1gba](https://dev.to/kracekumar/type-check-your-django-application-1gba)
