# Intro
Django (https://docs.djangoproject.com/) is a fantastic web framework that allows you to develop web sites and web apps at pace, but rolling it out on Ubuntu (https://ubuntu.com/) can still be a bit of a pain. That is why I created this file.

## Notes

Each line is prefixed by the environment you should run it in. A dollar (`$`) is Bash\shell\terminal. Triple greater-than (`>>>`) is the python shell (or the django shell). A hash (`#`) is the shell for Postgres.

```
$ something
$ something

$ something else
```

Spaces (or 'new line spaces' if you like) exist between different bits of work (project setup, installing packages, apache, etc...)

```

...

```

Dots with empty lines before and after indicate that this is not a necessary step but you might want to do it (They are found towards the bottom. E.g. setting up postgres (https://postgresql.org/), setting up certbot (https://certbot.eff.org/))

# Instructions

```
$ sudo apt update
$ sudo apt upgrade
$ sudo apt install git apache2 python3-pip libapache2-mod-wsgi-py3 libpq-dev
$ dpkg -S mod_wsgi
$ ps -e | grep apache
$ sudo ufw allow 'Apache Full'

$ cd /var/www/
$ sudo mkdir some_project
$ sudo chown local_user:local_user some_project
$ git clone https://github.com/some_account/some_project.git
$ cd some_project
$ git config credential.helper store
$ git pull origin master
$ cd some_project
$ cp settings.py.example settings.py
$ python3
>>> import random, string
>>> ''.join([random.SystemRandom().choice(string.ascii_letters + string.digits + string.punctuation) for _ in range(50)])
...
>>> exit()
$ vim settings.py
...
SECRET_KEY = '...'
ALLOWED_HOSTS = ['...']
...
TEMPLATES = [
    {
        ...
        'DIRS': [
            '/var/www/django/some_project/some_project/templates',
            '/var/www/django/some_project/some_app/templates',
            '/var/www/django/some_project/some_other_app/templates',
            '...',
        ],
        ...
...

$ cd /var/www
$ pip3 install virtualenv
$ python3 -m virtualenv venv
$ source venv/bin/activate
(venv) $ pip3 install -r requirements.txt
(venv) $ cd django
(venv) $ python some_project/manage.py collectstatic
(venv) $ deactivate
$ ...

$ sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/000-default.conf.OLD
$ sudo vim /etc/apache2/sites-available/000-default.conf
...
WSGIDaemonProcess www-data python-path=/var/www/django/some_project python-home=/var/www/venv
WSGIProcessGroup www-data
WSGIScriptAlias / /var/www/django/some_project/some_project/wsgi.py

<VirtualHost *:80>

    <Directory /var/www/venv/django/some_project/some_project>
        <Files wsgi.py>
            Require all granted
        </Files>
    </Directory>

    Alias /static /var/www/django/some_project/static
    <Directory /var/www/django/some_project/static>
        Require all granted
    </Directory>

    Alias /media /var/www/django/some_project/media
    <Directory /var/www/django/some_project/media>
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>

<VirtualHost *:443>

    <Directory /var/www/venv/django/some_project/some_project>
        <Files wsgi.py>
            Require all granted
        </Files>
    </Directory>

    Alias /static /var/www/django/some_project/static
    <Directory /var/www/django/some_project/static>
        Require all granted
    </Directory>

    Alias /media /var/www/django/some_project/media
    <Directory /var/www/django/some_project/media>
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
...

$ sudo service apache2 restart
$ tail -f /var/log/apache2/error.log

...

$ sudo apt-get install postgresql postgresql-contrib
$ sudo -i -u postgres
$ createuser --interactive
Enter name of role to add: db_user
Shall the new role be a superuser? (y/n) n
Shall the new role be allowed to create databases? (y/n) y
Shall the new role be allowed to create more new roles? (y/n) n
$ exit
$ sudo -i -u postgres psql
=# ALTER USER db_user WITH PASSWORD 'db_password';
=# \q
$ sudo -i -u postgres createdb db_name
$ cd /var/www/django/some_project/some_project
$ vim settings.py
...
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'db_name',
        'USER': 'db_user',
        'PASSWORD': 'dp_password',
        'HOST': 'localhost',
        'PORT': '5432',
    }
}
...
$ ...

...

$ sudo su
$ apt-get update
$ apt-get install software-properties-common
$ add-apt-repository universe
$ add-apt-repository ppa:certbot/certbot
$ apt-get update
$ apt-get install certbot python3-certbot-apache
$ certbot --apache
...
```
