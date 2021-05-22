# Installation



[TOC]

> Note: All runtime files are installed under a folder /env/apps/django-boards
>
> Choose an appropriate folder from local env and use where ever mentioned

## Python Dependencies

* Python 3.6 or higher

* pip

  ```
  wget https://bootstrap.pypa.io/get-pip.py
  sudo python3.9 get-pip.py
  ```

* virtualenv

  ```
  cd boards-app-django-fullstack-py
  pip install virtualenv
  virtualenv venv -p python3.9
  ```

  

  ```
  cd boards-app-django-fullstack-py
  source venv/bin/activate
  ```

  

  ```
  cd boards-app-django-fullstack-py
  deactivate
  ```

* Python Modules

  ```
  pip install -r requirements.txt
  ```

  

## Django Application Setup

### Python Project ROOT

```
django-boards/
```

### Django Configuration

Configure local environment in .env file

```
cp dot-env .env
#Edit .env
```

##### Django SECRET_KEY

* Generate a secret key

  ```
  python -c "import secrets; print(secrets.token_urlsafe())"
  
  PObj-HJY8mWCZuqX8Zjn88-UbLcrYMtgDMc55PdkIl4
  ```

* Copy secret key in '.env' file

  ```
  SECRET_KEY=PObj-HJY8mWCZuqX8Zjn88-UbLcrYMtgDMc55PdkIl4
  ```

##### DEBUG

```
DEBUG=True
```

##### ALLOWED_HOSTS

```
ALLOWED_HOSTS=.localhost,127.0.0.1
```

##### DATABASE_URL

```
DATABASE_URL='sqlite:///{BASE_DIR}/django-boards-db.sqlite3'
only BASE_DIR available for formatting
```

* See https://github.com/jacobian/dj-database-url for other database urls

### Migrate Database

```
#DATABASE_URL should be set correctly in .env

python manage.py migrate
```

### Collect Static Files

```
#edit STATIC_ROOT in django-boards/django-boards/settings.py
STATIC_ROOT = /env/apps/django-boards/static
```

```
python manage.py collectstatic
```

### Create Application Super-User

```
python manage.py createsuperuser
```

Note down username and password

## Gunicorn

```
pip install gunicorn
```

```
#Edit gunicorn_start to suite local environment
#run folder /env/app/django-boards/run/
#logs folder /env/app/django-boards/logs

#start
chmod u+x gunicorn_start
$gunicorn_start
```

## Supervisor

```
sudo apt install supervisor 
supervisord -v
systemctl status supervisor
```

```
#Create /etc/supervisor/conf.d/django-boards.conf

[program:django-boards]
command=/home/seegler/git/projects/boards-app-django-fullstack-py/gunicorn_start
user=seegler
autostart=true
autorestart=true
redirect_stderr=true
stdout_logfile=/env/apps/django-boards/logs/gunicorn.log

```

```
sudo supervisorctl reread
sudo supervisorctl update

sudo supervisorctl status django-boards
```

## NGINX

```
sudo apt-get -y install nginx
sudo systemctl status nginx
```

```
#Edit /etc/nginx/sites-available/django-boards-site

upstream app_server {
    server unix:/env/apps/django-boards/run/gunicorn.sock fail_timeout=0;
}

server {
    listen 80;
    server_name 127.0.0.1;  # here can also be the IP address of the server

    keepalive_timeout 5;
    client_max_body_size 4G;

    access_log /env/apps/django-boards/logs/nginx-access.log;
    error_log /env/apps/django-boards/logs/nginx-error.log;

    location /static/ {
        alias /env/apps/django-boards/static/;
    }

    # checks for static file, if not found proxy to app
    location / {
        try_files $uri @proxy_to_app;
    }

    location @proxy_to_app {
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header Host $http_host;
      proxy_redirect off;
      proxy_pass http://app_server;
    }
}
```

```
sudo ln -s   /etc/nginx/sites-available/django-boards-site /etc/nginx/sites-enabled/django-boards-site
sudo rm /etc/nginx/sites-enabled/default
```

```
sudo systemctrl daemon-reload
sudo systemctrl restart nginx

#Access http://127.0.0.1
```

## Seed Initial Data

Add a new Board 

```
# Run from python manage.py shell

from boards.models import Board

board = Board(name='Django Webframework', description='This is a board about Django Webframework.')
board.save()
board.id
board.description

```

Refresh website. Add topics and posts under this new board

## HTTPS Certificate

Install certbot

```
sudo apt-get update
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install python-certbot-nginx
```

Install certs in nginx

```
sudo certbot --nginx
#Follow prompts
```

> Optional
>
> ```
> sudo crontab -e
> 
> #Add at end of the file
> 0 4 * * * /usr/bin/certbot renew --quiet
> ```
>
> 