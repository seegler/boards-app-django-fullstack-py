# Installation

## Dependencies

* Python 3.6 or higher

* virtualenv

  ```
  pip install virtualenv
  virtualenv venv -p python3.9
  ```

* Python Modules

  ```
  pip install requirements.txt
  ```

  



## Configuration

Configure local environment in .env file

```
cp dot-env .env
#Edit .env
```

#### Django SECRET_KEY

* Generate a secret key

  ```
  python -c "import secrets; print(secrets.token_urlsafe())"
  
  PObj-HJY8mWCZuqX8Zjn88-UbLcrYMtgDMc55PdkIl4
  ```

* Copy secret key in '.env' file

  ```
  SECRET_KEY=PObj-HJY8mWCZuqX8Zjn88-UbLcrYMtgDMc55PdkIl4
  ```

#### DEBUG

```
DEBUG=True
```

#### ALLOWED_HOSTS

```
ALLOWED_HOSTS=.localhost,127.0.0.1
```

#### DATABASE_URL

```
DATABASE_URL='sqlite:///{BASE_DIR}/django-boards-db.sqlite3'
only BASE_DIR available for formatting
```

* See https://github.com/jacobian/dj-database-url for other database urls

