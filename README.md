# Tutorial de Django

Django es un `web framework` para Python de alto nivel que fomenta el desarrollo rápido con diseño limpio y pragmático. Creado por desarrolladores experimentados, se ocupa de gran parte de la molestia del desarrollo web, por lo que se puede concentrarse en escribir su aplicación sin necesidad de reinventar la rueda. [Es gratis y de código abierto.](https://www.djangoproject.com/)

## Contenido:
* [Instalación](#instalación)
* [Crear proyecto](#crear-proyecto)
* [ORM de Django](#orm-de-django)
    * [Interacción con Shell y creación de Queries](#interacción-con-shell-y-creación-de-queries)
        * C: (Create) - Creando objetos
        * R: (Retrieve) - Recuperando objetos
        * U: (Update) - Actualizando objetos
        * D: (Delete) - Eliminando objetos
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

``` python
# blog/models.py
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

Como podemos observar en el código, los modelos son definidos mediante la clase `Model`. Cada modelo representa una tabla en la base de datos y una instancia de cada clase representa un registro en particular de nuestra tabla. Dentro de cada modelo podremos definir las `columnas` o `campos` que deseamos en nuestros modelos. Django nos da muchos tipos de campos que podemos usar según lo que necesitemos. Para mayor detalle consulte la [documentación oficial.](https://docs.djangoproject.com/en/3.0/topics/db/models/)

Ahora que tenemos nuestros modelos definidos, realicemos las migraciones necesarias para que se refleje en la base de datos. Para el presente tutorial trabajaremos con la instancia de `sqlite3` que maneja Django por defecto.

Primero registraremos nuestra app en nuestro proyecto, para eso iremos a `miEjemplo/settings.py`.

``` python
# miEjemplo/settings.py
...

INSTALLED_APPS = [
    'blog', # agregamos nuestra app
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
...
```

Luego ejecutamos lo siguiente:

``` bash
$ python manage.py makemigrations
```

Obtendremos la siguiente respuesta:

``` bash
Migrations for 'blog':
  blog/migrations/0001_initial.py
    - Create model Author            
    - Create model Blog    
    - Create model Entry
```

El comando `makemigrations` crea en nuestra aplicación archivos de migraciones que definen los que se hará en la base de datos al realizar la migración, veamos el contenido de `blog/migrations/0001_initial.py`

``` python
# blog/migrations/0001_initial.py

# Generated by Django 3.1 on 2020-08-08 17:43

from django.db import migrations, models
import django.db.models.deletion


class Migration(migrations.Migration):

    initial = True

    dependencies = [
    ]

    operations = [
        migrations.CreateModel(
            name='Author',
            fields=[
                ('id', models.AutoField(auto_created=True, primary_key=True, serialize=False, verbose_name='ID')),
                ('name', models.CharField(max_length=200)),
                ('email', models.EmailField(max_length=254)),
            ],
        ),
        migrations.CreateModel(
            name='Blog',
            fields=[
                ('id', models.AutoField(auto_created=True, primary_key=True, serialize=False, verbose_name='ID')),
                ('name', models.CharField(max_length=100)),
                ('tagline', models.TextField()),
            ],
        ),
        migrations.CreateModel(
            name='Entry',
            fields=[
                ('id', models.AutoField(auto_created=True, primary_key=True, serialize=False, verbose_name='ID')),
                ('headline', models.CharField(max_length=255)),
                ('body_text', models.TextField()),
                ('pub_date', models.DateField()),
                ('mod_date', models.DateField()),
                ('number_of_comments', models.IntegerField()),
                ('number_of_pingbacks', models.IntegerField()),
                ('rating', models.IntegerField()),
                ('authors', models.ManyToManyField(to='blog.Author')),
                ('blog', models.ForeignKey(on_delete=django.db.models.deletion.CASCADE, to='blog.blog')),
            ],
        ),
    ]
```

Como se mencionó, define las accione a ejecutar en la migración. Vemos que también agrega un campo `id` que no definimos en nuestro modelo. Esto ocurre porque no definimos ningún campo como `primary key`, así que lo agrega automáticamente.

Corramos la migración:

``` bash
$ python manage.py migrate
```

Tendremos lo siguiente:

``` bash
Operations to perform:  Apply all migrations: admin, auth, blog, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying auth.0009_alter_user_last_name_max_length... OK
  Applying auth.0010_alter_group_name_max_length... OK
  Applying auth.0011_update_proxy_permissions... OK
  Applying auth.0012_alter_user_first_name_max_length... OK
  Applying blog.0001_initial... OK
  Applying sessions.0001_initial... OK
```

Vemos más migraciones que la que creamos antes porque son las que vienen por defecto con Django, que hemos mencionado antes. A continuación vamos a interactuar con el `shell` de Django y crear varios registros.

### Interacción con Shell y creación de Queries
Para ingresar al `shell` en el terminal ejecutamos:

``` bash
$ python manage.py shell
```

Tendremos lo siguiente:

``` bash
Python 3.6.8 (default, Jan 14 2019, 11:02:34) 
[GCC 8.0.1 20180414 (experimental) [trunk revision 259383]] on linux  
Type "help", "copyright", "credits" or "license" for more information.
(InteractiveConsole)
>>>
```
Y podremos comenzar a interactuar con la consola.

#### C: (Create) - Creando objetos

Crearemos nuestro primer `blog`

``` python
>>> from blog.models import Blog
>>> b = Blog(name='Mi primer Blog', tagline='Mi ejemplo de blog')
>>> b.save()
```

Django en la llamada del método `save()` realiza la ejecución de un **INSERT** en la base de datos. Antes de `save()`, no se realiza ninguna operación en la base de datos. Se puede también realizar esta operación con la función `create`. Las operaciones que involucran manejo de datos en base, se ejecutan como `transactions`, si no está familiazirado con el término, revise la [documentación](https://docs.djangoproject.com/en/3.0/topics/db/transactions/)

A continuación usaremos los campos `ForeignKey` y `ManyToManyField`

``` python
>>> entry = Entry()
>>> entry.pub_date = '2020-08-08'
>>> entry.mod_date = '2020-08-08'
>>> entry.number_of_comments = 0
>>> entry.number_of_pingbacks = 0
>>> entry.rating = 0
>>> entry.blog = b
>>> entry.save()
```

En el paso anterior, creamos nuestra entrada, a la que hemos asociado con un blog, usando de esta forma el `ForeignKey` de ese modelo. Bastante sencillo, ¿cierto?. Agreguemos varios autores.

``` python
>>> from blog.models import Author
>>> au1 = Author.objects.create(name='au1')>>> au2 = Author.objects.create(name='au2')
>>> au3 = Author.objects.create(name='au3')
>>> entry.authors.add(au1, au2, au3)
```

Django mostrará un mensaje de error si se asigna un objeto de tipo inapropiado.

#### R: (Retrieve) - Recuperando objetos
#### U: (Update) - Actualizando objetos
#### D: (Delete) - Eliminando objetos
