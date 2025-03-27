


# Steps

1. [Github](#github)
2. [Python](#python)
3. [PostgreSQL](#postgresql)
4. [Web](#web)
    - [Nginx](#nginx)
    - [Supervisor](#supervisor)
5. [Services](#services)
    - [Gunicorn](#gunicorn)
    - [Redis](#redis)
    - [Celery](#celery)

### More:
[Update Guide](update.md)


# Github

### Update installer

```
sudo su
apt-get update
apt-get install git
```

### Setup

First install git:
```
apt-get install git
```

Then create SSH Key:
```
ssh-keygen -t ed25519
cat ~/.ssh/id_ed25519.pub
```
The output is the public key, add this to your github account (Settings -> SSH and GPG keys)

Clone Project
```
git clone git@github.com:username/repository_name.git
cd repository_name
```
This is the `Base Directory` (For Django / virtual environment)



# Python

```
apt install python3-pip python3-dev python3-venv
```

In the `Base Directory` create and activate the virtual environment:
```
python3 -m venv venv
source venv/bin/activate
```

Upgrade pip and install project requirments:
```
pip install --upgrade pip
pip install -r requirements.txt
```



In the `Base Directory` create `.env`

```
DEBUG=False
DJANGO_ENV=production
```

Django Key
```
# Enter Shell
python manage.py shell
# In Shell:
from django.core.management.utils import get_random_secret_key
print(get_random_secret_key())
```
Copy output into `.env`
```
SECRET_KEY='replace_this_with_output'
```


# PostgreSQL

Install packages for PostgreSQL
```
apt install libpq-dev postgresql postgresql-contrib
```


postgres command line
```
sudo -u postgres psql
```


Add to `.env` and replace with your own values:
```
DB_NAME=db_name
DB_USER=db_user
DB_PASSWORD=db_password
DB_HOST=localhost
DB_PORT=5432
```
Port and host are defaults that don't need to be changed



Create database for Django:
```
CREATE DATABASE db_name;
CREATE USER db_user WITH PASSWORD db_password;
ALTER DATABASE db_name OWNER TO db_user;
# -- the next two commands are needed on PostgreSQL 15 and later
\connect db_name;
GRANT CREATE ON SCHEMA public TO db_user;
check
$ psql --username db_user --password --host localhost db_name
\conninfo
```
All definitions need to be the same as `.env`.


### Initilize Django
Then in `Base Directory` with venv active migrate - transfer DB schema and create admin profile:
```
python manage.py migrate
python manage.py createsuperuser
python manage.py collectstatic
```

Load model if needed:
```
python manage.py loaddata dev/dev_data.json
```

# Web

```
apt install nginx curl ufw
```

Allow access:
```
sudo ufw allow ssh #If using ssh
sudo ufw allow 'Nginx Full'
```
Enable:
```
sudo ufw enable
```

## Nginx

Create and enter Nginx config file:
```
nano /etc/nginx/sites-available/project_name.conf
```

Paste:
```
server {
        listen 80;
        server_name your_domain.com www.your_domain.com;

        location /favicon.ico {
                access_log off;
                log_not_found off;
        }

        location /static/ {
                alias /base_dir/staticfiles/; #Static path - same as settings.py
                autoindex on;
        }
        location / {
								proxy_pass http://unix:/base_dir/project_name.sock;
                proxy_set_header Host $host;  # Make sure this is set correctly
    }
}
```

Make project availble:
```
ln -s /etc/nginx/sites-available/project_name.conf /etc/nginx/sites-enabled/project_name.conf
```

## Supervisor

```
sudo service supervisor start
```
With folder `/etc/supervisor/conf.d/` for all processes.


Useful commands for services (gunicorn, celery, redis):
```
sudo supervisorctl start project_name_service
sudo supervisorctl stop project_name_service
sudo supervisorctl restart project_name_service
```

# Services


## Gunicorn

Make sure python package is installed, then create supervisor config file for Gunicorn:
```
pip install gunicorn
touch /etc/supervisor/conf.d/project_name-gunicorn.conf
```

Copy this in: `nano /etc/supervisor/conf.d/project_name-gunicorn.conf`
```
[program:project_name_gunicorn]
user=root
directory=/base_dir/django_project/ #Working dir / base dir
command=/base_dir/venv/bin/gunicorn --workers 3 --bind unix:project_name.sock project_name.wsgi:application
 
autostart=true
autorestart=true
stdout_logfile=/var/log/proj/gunicorn.log
stderr_logfile=/var/log/proj/gunicorn.err.log
```

Restart Supervisor:
```
supervisorctl reread
supervisorctl update
```

## Redis

Make sure python package is installed:
```
---pip install redis
sudo apt install redis-server -y
```

Check:
```
redis-cli ping
```
Should output: `PONG`

Restart / Reload:
```
systemctl daemon-reload
sudo systemctl restart redis.service
```


## Celery

Make sure python package is installed, then create supervisor config file for Celery:
```
pip install celery django-celery-beat django-celery-results
touch /etc/supervisor/conf.d/project_name-celery.conf
```

Copy this in: `nano /etc/supervisor/conf.d/project_name-celery.conf`
```
[program:project_name_celery]
user=root #my user to run
directory=/var/www/myproject/src/ #working dir
command=/var/www/myproject/bin/celery -A proj worker -l info
 
autostart=true
autorestart=true
stdout_logfile=/var/log/proj/celery.log
stderr_logfile=/var/log/proj/celery.err.log
```

### Make celery beat:
Copy this in: `nano /etc/supervisor/conf.d/project_name-celerybeat.conf`
```
[program:project_name_celerybeat]
user=root #my user to run
directory=/var/www/myproject/src/ #working dir
command=/var/www/myproject/bin/celery -A proj beat -l info
 
autostart=true
autorestart=true
stdout_logfile=/var/log/proj/celerybeat.log
stderr_logfile=/var/log/proj/celerybeat.err.log
```



Update and check:

```
supervisorctl reread
supervisorctl update
sudo supervisorctl status proj_celery
sudo supervisorctl status proj_celerybeat

```
