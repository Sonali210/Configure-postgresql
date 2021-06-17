# Configure-postgresql
Configure postgresql to allow remote connection with Django, ngnix, gunicorn on ubuntu 18.04</br>

We need to update the local apt package index and then download and install the packages
```
sudo apt-get update
sudo apt-get install python3-pip python3-dev libpq-dev postgresql postgresql-contrib nginx
```
# Create the PostgreSQL Database and User</br>
Log into an interactive Postgres session by typing:
```
sudo -u postgres psql

```
<h3>Inside PostgreSQL prompt: </h3>
Create Database

```
CREATE DATABASE myproject;
```

Create User

```
CREATE USER myprojectuser WITH PASSWORD 'password';
```

Set the default encoding to UTF-8 and timezone

```
ALTER ROLE myprojectuser SET client_encoding TO 'utf8';
ALTER ROLE myprojectuser SET timezone TO 'Asia/Kolkata';
```

<h3>Now give our new user access to administer our new database:</h3>

```
GRANT ALL PRIVILEGES ON DATABASE myproject TO myprojectuser;
```

Exit out of the PostgreSQL prompt

```
\q
```

# Inside virtual environment myproject venv

```
pip install django gunicorn psycopg2
```
Create and configure django project "myproject"</br>

Change ALLOWED HOSTS in settings.py file and add Database as

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': 'myproject',
        'USER': 'myprojectuser',
        'PASSWORD': 'password',
        'HOST': 'localhost',
        'PORT': '',
    }
}
```
Add these lines at end of setting.py

```
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'static/')
```
Run collect static command

```
~/myproject/manage.py collectstatic
```

<h2> Make remote Postgresql Connection</h2>
  By default PostgreSQL is configured to be bound to "localhost"</br>
  Check the connection using
  
  ```
  $ netstat -nlt
  ```
   You will see port 5432 is bound to 127.0.0.1</nr>
   
   <h3>Configure postgresql.conf</h3>
   Edit this code in postgresql.conf file
   
   ```
  listen_addresses = '*'
   ```
   
   Now restart Postgresql server
   
   ```
   sudo service postgresql restart
   ```
   You will see that "Local Address" for port 5432 has changed to 0.0.0.0</br>
   
   Now configure pg_hba.conf</br>
   Add the follwing code in the end 
   ```
host    all             all              0.0.0.0/0                       md5
host    all             all              ::/0
```
Restart server and then:
```
 psql -h 107.170.158.89 -U postgres
 ```
 
   
  

