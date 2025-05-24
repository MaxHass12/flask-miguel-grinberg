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

- The routes are defined like this
```
from app import app

@app.route('/')
@app.route('/index')
def index():
    return "Hello, World!"
```

- The decorators with `@app.route(<a_route>)` creates an association between the URL given as an argument and the string.

- To complete we need to create the script which runs the application. Typically named with the same name. 

- Before running the app we add the script name as env var by `export FLASK_APP=microblog.py`

# Ch4 - Database

- 'SQLAlchemy' is one of the most popular for Python. `flask-sqlalchemy` if the flask friendly wrapper to it.
- Database migrations help with making updates to an existing database as it grows. 'Alembic' is the standard database migration framework for 'SQLAlchemy'. `flask-migrate` is a wrapper for this.

- For setting up database, we pass the DB URI in the `Config` class in `config.py`
- Then we set it up at initialization of app ie `app/__init__.py`
    - We initialize database by `db = SQLAlchemy(app)`
    - Then migration engine `migrate = Migrate(app, db)`

- In `app/model.py` we define our models
    - We import `sqlalchemy` and `sqlalchemy.orm` modules
    - We create a model (`User`) by defining a class of that name and inheriting it from `db.Model`. It defines several class variables, which are the corresponding columns in the database.
    - The column types are defined by python typehint wrapped from `so.Mapped` generic type. To make a column nullable `Optional` is added.
    - Furthere conditions on types are added by `so.mapped_columns()` - where we define whether a column is unique, indexed, etc.

- Flask-migrate exposes its command through `flask` command. `flask db` sub-command is added by Flask-migrate to manage everything related to database migrations.
- `flask db init` sets up the migration repository, `flask db migrate` creates the migration files.
- `flask db migrate` does not make any changed to database. For changes in the database, `flask db upgrade` must be used.

- Say we have a `Post` model as well. 
    - For joining it to `User`, we need a `user_id` field in `Post`. Defined as `sa.ForeignKey`
    - `User` class also needs to have `posts` field which is initialized with `so.relationshp()` - note that this is a field for ORM and not an actual field in the database. Likewise, `Post` has an `author` field.
        - The `so.relationship()` take `back_populates` as arguments referencing the opposite table.

- To access the database in a REPL, we need to import `app`, `db`, `User`, `Post`, `sa` first.
    - The for flask and its exentions to have access to flask application, an _application context_ must be created and pushed. By `app.app_context().push()`
    - Then we create a user by regular Python
    - We can add it to database by `db.session.add(u)` and `db.session.commit()` - Untill `commit()` all changes are accumulated and then saved atomically.
    - `query = sa.select(User)`, `db.session.scalars(query).all()` is how we get all Users.
    - There are tonnes of ways to read data.

- `flask shell` is the second core command implemented after `run` - it enable to start python REPL in the context of interpretor.
    - For this we create a shell context in the runner file. The function wrapper by the decorator `@app.shell_context_processer` returns a dict whose keys are available in the intepretor.