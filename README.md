# Tutorial de Django

Django es un `web framework` para Python de alto nivel que fomenta el desarrollo rápido con diseño limpio y pragmático. Creado por desarrolladores experimentados, se ocupa de gran parte de la molestia del desarrollo web, por lo que se puede concentrarse en escribir su aplicación sin necesidad de reinventar la rueda. [Es gratis y de código abierto.](https://www.djangoproject.com/)

## Contenido:
* [Instalación](#instalación)
* [Crear proyecto](#crear-proyecto)
* [ORM de Django](#orm-de-django)
* Modulo de administración
* Despliegue en Pythonanywhere

## Instalación

Para el pressente tutorial se usará Ubuntu 18.04.

1. Crear un `Virtual Enviroment` para el manejo de los paquetes que usaremos en el proyecto.
``` bash
$ virtualenv -p python3.6 .venv
```

2. Activar `Venv`
``` bash
$ source ./venv/bin/activate
```

3. Instalar **Django** via pip
``` bash
$ pip install Django
```

4. Verificar instalación
``` bash
$ python -m django --version
$ 3.1
```

## Crear proyecto

Iniciemos creando la el código base de nuestro proyecto. En el terminal ejecute lo siguiente:
``` bash
$ django-admin startproject miEjemplo
```

Se han creado varios archivos:

```
miEjemplo/
    manage.py
    miEjemplo/
        __init__.py
        settings.py
        asgi.py
        settings.py
        urls.py
        wsgi.py
```

Los archivos son:
Existen dos carpetas **miEjemplo**. La de más afuera, es la que contiene nuestro proyecto, su nombre puede ser cambiado en cualquier momento. La de adentro es la que define nuestro paquete de Python para nuestro proyecto. **No** debe de cambiar su nombre. Es importante porque podremos usarla para importar cualquier cosa dentro de nuestro proyecto. 

* **manage.py**: Es un archivo utilitario para interactuar con la línea de comandos de varias maneras, [aquí](https://docs.djangoproject.com/en/3.1/ref/django-admin/) puedes ver más detalle.
* **miEjemplo/__init__.py**: Este archivo le dice a Python que esta carpeta debe de considerarse como un paquete de Python.
* **settings.py**: Aquí se aloja las configuraciones para nuestro proyecto. [Aquí](https://docs.djangoproject.com/en/3.1/topics/settings/) más detalle.
* **urls.py**: Aquí se alojan las URLs declaradas para nuestro proyecto de Django, algo así como una tabla de contenido. [Aquí](https://docs.djangoproject.com/en/3.1/topics/http/urls/) más detalle.
* **mysite/asgi.py** y **mysite/wsgi.py**: Son archivos que nos permiten servir nuestro proyecto en servidores web. Aquí más detalles sobre [ASGI](https://docs.djangoproject.com/en/3.1/howto/deployment/asgi/) y [WSGI](https://docs.djangoproject.com/en/3.1/howto/deployment/wsgi/).

Levantemos nuestro servicio localmente:

``` bash
$ python manage.py runserver
```

Se mostrará el siguiente mensaje:
``` bash
Watching for file changes with StatReloaderPerforming system checks...

System check identified no issues (0 silenced).

You have 18 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.

August 08, 2020 - 03:03:24
Django version 3.1, using settings 'miEjemplo.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Las migraciones a las que hace referencia el mensaje son las migraciones de los modelos que vienen por defecto en Django que tratan sobre el manejo del modulo de adminsitrador, usuarios y otros. Lo veremos más adelante. Si abrimos nuestro navegador y consultamos la URL nos debe salir la siguiente pantalla: 

!['cap1'](https://raw.githubusercontent.com/gcmurillo/django_tutorial/master/capturas/cap_1.jpg)

## ORM de Django

El ORM (Object-Relational Mapping) es un modelo que consiste en la transformación de las tablas de una base de datos, en una serie de entidades que simplifiquen las tareas básicas de acceso a los datos. Según la documentación oficial, Django ofrece un API para la abstracción de la base de datos que permite crear, recuperar, actualizar y eliminar (*CRUD: Create, Retrieve, Update, Delete*). A continuación usaremos como ejemplo el ejercicio de la [documentación oficial.](https://docs.djangoproject.com/en/3.0/topics/db/queries/)

Primero creemos nuestra app.

``` bash
$ python manage.py startapp blog
```
Así como en el punto de creación de proyecto, aquí también se crearon varios archivos. Por lo pronto nos enfocaremos en `models.py` donde colocaremos las entidades que serán parte de nuestra aplicación.

`models.py`
``` python
from django.db import models

class Blog(models.Model):
    name = models.CharField(max_length=100)
    tagline = models.TextField()

    def __str__(self):
        return self.name

class Author(models.Model):
    name = models.CharField(max_length=200)
    email = models.EmailField()

    def __str__(self):
        return self.name

class Entry(models.Model):
    blog = models.ForeignKey(Blog, on_delete=models.CASCADE)
    headline = models.CharField(max_length=255)
    body_text = models.TextField()
    pub_date = models.DateField()
    mod_date = models.DateField()
    authors = models.ManyToManyField(Author)
    number_of_comments = models.IntegerField()
    number_of_pingbacks = models.IntegerField()
    rating = models.IntegerField()

    def __str__(self):
        return self.headline
```