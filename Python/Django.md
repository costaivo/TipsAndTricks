### Install Django
easy_install django

### Create new website using Django
django-admin startproject <<projectName>>

### Run Website
python manage.py runserver

### Create app
python manage.py startapp <<AppName>>

### Apply Migratioins
python manage.py migrate

### Make Migrations
python manage.py makemigrations music

### Convert to sql 
py manage.py sqlmigrate music 0001

Database API Shell
py manage.py shell