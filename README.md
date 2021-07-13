# Development environment each version
- Python : 3.8.3
- Django : 3.2.5
- Docker : 20.10.5
- Docker-compose : 1.28.5

- MacBook Pro

# Development environment for Django using Docker

## 1. create folder
```terminal
$ mkdir [project_name]
$ cd [project_name]
```
__[project_name] = "docker-blog"__　 in this case.


In this folder, prepare the following three files.
- Dockerfile
- docker-compose.yml
- requirements.txt

```teminal
$ touch Dockerfile
$ touch docker-compose.yml
$ touch requirements.txt
```

```Dockerfile
[Dockerfile]
FROM python:3
ENV PYTHONUNBUFFERED 1

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/

RUN pip install -r requirements.txt
ADD . /code/
```

```yaml:docker-compose.yml
[docker-compose.yml]
version: '3'

services:
    db:
        image: postgres
        environment:
            - POSTGRES_DB=postgres
            - POSTGRES_USER=root
            - POSTGRES_PASSWORD=password
        volumes:
            - postgres_data:/var/lib/postgresql/data
    web:
        build: .
        command: python3 manage.py runserver 0.0.0.0:8000
        volumes:
            - .:/code
        ports:
            - "8000:8000"
        depends_on:
            - db
volumes:
    postgres_data: 
```

```text:requirements.txt
[requirements.txt]
Django
Psycopg2
```

## 2. create project

```terminal
$ docker-compose run web django-admin.py startproject dockerblog .
```

If you have succeeded, verify it with the following command.
```terminal
$ tree

=>result
.
├── Dockerfile
├── docker-compose.yml
├── dockerblog
│   ├── __init__.py
│   ├── asgi.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── manage.py
└── requirements.txt
```

Rewrite DAEABASES in __```dockerblog/settings.py```__ to connect to the database.

```python:settings.py
[settings.py]
...

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'postgres',
        'USER': 'root',
        'PASSWORD': 'password',
        'HOST': 'db',
        'PORT': 5432,
    }
}

...
```

Let7s try this command and if you have succeeded it , access to http://localhost:8000/ .
```terminal
$ docker-compose up -d

=> docker-blog_db_1 is up-to-date
=> Creating docker-blog_web_1 ... done
```

You can check which containers are running by using __```$ docker ps```__ command.

```terminal
CONTAINER ID   IMAGE                COMMAND                  CREATED          STATUS          PORTS                    NAMES
06d98d67a07f   docker-blog_web      "python3 manage.py r…"   8 minutes ago    Up 8 minutes    0.0.0.0:8000->8000/tcp   docker-blog_web_1
...
```
The command to create and start blog application
```
$ docker-compose exec web python manage.py startapp blog
```
stop:
```
$ docker-compose stop
```
restart:
```
$ docker-compose restart
```
