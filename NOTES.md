# Ch1 - Hello World

- In python packages are available in public repo from where everyone can download them and install them. The offical repository in `PyPI`.
- The standard practise is to create a 'virtual environment' for every project that we are working with. The command for it is `python -m venv venv`  - The second venv name can be anything.

- A python application exists in a 'package'. In python, a subdirectory which contains a `__init__.py` file is considered a package and can be imported.
- In that file we do the following to set up a flask app
    - `from flask import Flask` -> imports Flask
    - `app = Flask(__name__)` -> instantiates Flask app, `__name__` works almost always - not sure why.
    - `from app import routes` -> imports routes module which does not exist yet.

- There are 2 entities names `app` 
    - a) The app package in the `app` directory having `__init__.py` script. This 'app' package is referenced in the `from app import routes` statement.
    - b) The `app` variable which refers to an object instantiated from the `Flask` class.

- Another peculiarity is that the `from app import routes` is at the bottom. Why ? To avoid circular imports because routes refer the `app` variable.

- `routes.py` contains the different routes handling the different URL which the application can support. The handlers for application routes are called _view functions_. 