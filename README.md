# django-blog-app publication with heroku

Deploy django blog application with heroku.

[link to application](https://mydjangoblogappsujiayi.herokuapp.com/)

## KEY POINTS

__*Using AWS S3 for file uploads to prepare for publishing on Heroku*__
*	aws.amazon.com, S3 (Simple Storage Service)
	 - create bucket: django-blog-files-sujiayi   permissions->CORS configuration (Cross-Origin Resource Sharing)
	 - IAM (identity access management  users->add user->username: django_user accesstype: Programmatic access
	 - attach existing policies directly
	 - AmazonS3FullAcess => Access key ID, Secret access Key

*	Set environment veriables:
	 - AWS_ACCESS_KEY_ID = "..."
	 - AWS_SECRET_ACCESS_KEY = "..."
	 - AWS_STORAGE_BUCKET_NAME = "..."

*	pip install boto3
*	pip install django-storages

*	in settings.py
```python
INSTALLED_APPS = [
    'blog.apps.BlogConfig',
    'users.apps.UsersConfig',
    'crispy_forms',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    'storages'
]

AWS_ACCESS_KEY_ID = os.environ.get('AWS_ACCESS_KEY_ID')
AWS_SECRET_ACCESS_KEY = os.environ.get('AWS_SECRET_ACCESS_KEY')
AWS_STORAGE_BUCKET_NAME = os.environ.get('AWS_STORAGE_BUCKET_NAME')

AWS_S3_FILE_OVERWRITE = False
AWS_DEFAULT_ACL = None

DEFAULT_FILE_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
```
*	users/models.py comment out saving part (not changing size)
 

__*Deploy app with Heroku*__
*	install Herokucli from https://devcenter.heroku.com
	 - heroku login
	 - pip install gunicorn
	 - pip freeze > requirements.txt

	 - git init
	 - git add -A
	 - git status
	 - git commit -m "initial commit"

	 - heroku create mydjangoblogappsujiayi
	 - heroku open
	 - git push heroku master

	 - Error while running '$python manage.py collectstatic --noinput'... You're using the staticfiles app without having set the STATIC_ROOT setting to a filesystem path

*	in settings.py
```python
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
STATIC_URL = '/static/'
```
	 - git status
	 - git add -A
	 - git commit -m "Updated STATIC_ROOT"
	 - git push heroku master
	 - heroku open

	 - Application Error ...  heroku logs --tail 

*	create Procfile in root project folder
	 - web: gunicorn django_project.wsgi

	 - git add -A
	 - git push heroku master

	 - Invalid HTTP_HOST header:'mydjangoblogappsujiayi.herokuapp.com', you may need to add 'mydjangoblogappsujiayi.herokuapp.com' to ALLOWED_HOSTS

*	in settings.py
```python
ALLOWED_HOSTS = ['mydjangoblogappsujiayi.herokuapp.com']
```

*	to generate a new random key
```python
>>> import secrets
>>> secrets.token_hex(24)
'fd9bd89bfdee4...5571a54eb'
>>> exit()
```

	 - set environment variable 
	 - SECRET_KEY = 'fd9bd89bfdee4...5571a54eb'
	 - DEBUG_VALUE = 'True'

*	set environment variables to heroku
	 - heroku config:set SECRET_KEY="..."
	 - heroku config:set DEBUG_VALUE="..."
	 - heroku config:set AWS_ACCESS_KEY_ID="..."
	 - heroku config:set AWS_SECRET_ACCESS_KEY="..."
	 - heroku config:set AWS_STORAGE_BUCKET_NAME="..."
	 - heroku config:set EMAIL_USER="..."
	 - heroku config:set EMAIL_PASS="..."
	 - git add -A
	 - git commit -m "..."
	 - git push heroku master

	 - Operational Error at /  no such table: blog_post

*	install postgresql
	 - heroku addons
	 - =>heroku-postgresql (postgresql-cubic-42347)  Plan: hobby-dev  Price: free   State: created
	 - (heroku addons:create heroku-postgresql:hobby-dev)
	 - heroku pg
=== DATABASE_URL
Plan:                  Hobby-dev
Status:                Available
Connections:           0/20
PG Version:            11.2
Created:               2019-04-06 17:08 UTC
Data Size:             7.7 MB
Tables:                0
Rows:                  0/10000 (In compliance)
Fork/Follow:           Unsupported
Rollback:              Unsupported
Continuous Protection: Off
Add-on:                postgresql-cubic-42347

*	add credentials to settings.py to let app talk to database
	 - pip install django-heroku
	 - in settings.py
```python
import django_heroku
#...

django_heroku.settings(locals())
```
	 - pip freeze > requirements.txt
	 - git status ...
	 - Programming Error at /  relation "blog_post" does not exist

*	run commands to  create tables and create a superuser that has admin access
	 - heroku run python manage.py migrate
	 - heroku run bash
	 - ls => blog manage.py  posts.json  profile_pics  requirements.txt staticfiles django_project  media Procfile README.md runtime.txt users
	 - python manage.py createsuperuser           jiayisu    sujiayi317@gmail.com
	 - exit

	 - heroku open




