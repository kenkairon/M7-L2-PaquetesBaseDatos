# M7-L2-PaquetesBaseDatos
Educativo y de Aprendizaje Personal

---
## Tabla de Contenidos
- [Tecnologías](#Tecnologías)
- [Configuración Inicial](#configuración-Inicial)
- [Configuración Base de datos](#configuración-Base-de-datos)
- [Creación del Modelo](#creación-del-modelo)
- [Creación de Vistas](#creación-de-vistas)
- [Configuracion de la vista de la base de datos](#Configuracion-de-la-vista-de-la-base-de-datos)
- [Creamos el superusuario](#creamos-el-superusuario)
---
# Tecnologías
- Django: Framework web en Python.
- PostgreSQL: Base de datos relacional avanzada 
--- 
# Configuración Inicial 
1. Entorno virtual 
    ```bash 
    python -m venv venv

2. Activar el entorno virtual
    ```bash 
    venv\Scripts\activate

3. Instalar Django
    ```bash 
    pip install django 

4. Actulizamos el pip 
    ```bash
    python.exe -m pip install --upgrade pip

5. Crear el proyecto de django
    ```bash 
    django-admin startproject mi_proyecto 

6. Ingresamos al proyecto mi_proyecto 
    ```bash 
    cd mi_proyecto

7. Creamos la aplicacion llamada mi_app
    ```bash     
    python manage.py startapp mi_app

8. Configuración de mi_proyecto/settings.py 
    ```bash 
    INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'mi_app',
    ]

# Configuración Base de datos
9. Instalar python-decouple: Es una biblioteca que ayuda manejar las variables de entorno 
    ```bash
    pip install python-decouple

10. Creamos el archivo .env a la altura del proyecto al lado manage.py 
    ```bash
    DATABASE_NAME=nombre_base_de_datos
    DATABASE_USER=postgres
    DATABASE_PASSWORD=yourpassword
    DATABASE_HOST=localhost
    DATABASE_PORT=5432

11. Configuracion de la base de datos ingresando los parametros de conexión 
    ```bash
    from decouple import config

    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.postgresql',
            'NAME': config('DATABASE_NAME'),
            'USER': config('DATABASE_USER'),
            'PASSWORD': config('DATABASE_PASSWORD'),
            'HOST': config('DATABASE_HOST'),
            'PORT': config('DATABASE_PORT'),
        }
    }
12. Instalacion de psycopg2: es un adaptador de base de datos para Python que permite interactuar con bases de datos PostgreSQL
    ```bash
    pip install psycopg2 

13. Guardo las dependencias me voy un cd .. mas atras del proyecto principal con el objetivo que quede al lado del README.md
    ```bash
    cd ..
    pip freeze > requirements.txt

# Creación del Modelo 

14. en mi_app/models.py
    ```bash
    from django.db import models

    class Usuario(models.Model):
        nombre = models.CharField(max_length=100)
        apellido = models.CharField(max_length=100)
        correo = models.EmailField(unique=True)
        pais = models.CharField(max_length=3)
        fecha_de_creacion = models.DateTimeField(auto_now_add=True)

        def __str__(self):
            return self.nombre

    class Producto(models.Model):
        nombre = models.CharField(max_length=200)
        precio = models.DecimalField(max_digits=10, decimal_places=2)
        stock = models.PositiveIntegerField()
        fecha_de_creacion = models.DateTimeField(auto_now_add=True)

        def __str__(self):
            return self.nombre

15. Ejecuta las migraciones para aplicar estos cambios a la base de datos:
    ```bash 
    python manage.py makemigrations
    python manage.py migrate

# Creación de Vistas

16. mi_app/views.py 
    ```bash 
    from django.shortcuts import render
    from .models import Usuario, Producto

    def lista_usuarios(request):
        usuarios = Usuario.objects.all()
        return render(request, 'lista_usuarios.html', {'usuarios': usuarios})

    def productos_en_stock(request):
        productos = Producto.objects.filter(stock__gt=0)
        return render(request, 'lista_productos.html', {'productos': productos})

17. creamos en hotel/templates/lista_usuarios.html 
    ```bash 
    <!DOCTYPE html>
        <html>

        <head>
            <title>Lista de Usuarios</title>
        </head>

        <body>
            <h1>Users</h1>
            <ul>
                {% for usuario in usuarios %}
                <li>{{usuario.nombre }} {{ usuario.apellido }} - {{ usuario.correo }}</li>
                {% endfor %}
            </ul>
        </body>

        </html>
18. creamos en hotel/templates/lista_productos.html 
    ```bash 
    <!DOCTYPE html>
    <html>

    <head>
        <title>Products In Stock</title>
        <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">
    </head>

    <body>
        <div class="container">
            <h1 class="my-4">Available Products</h1>
            <table class="table table-striped">
                <thead>
                    <tr>
                        <th scope="col">Product Name</th>
                        <th scope="col">Price</th>
                        <th scope="col">Stock</th>
                    </tr>
                </thead>
                <tbody>
                    {% for producto in productos %}
                    <tr>
                        <td>{{ producto.nombre }}</td>
                        <td>${{ producto.precio }}</td>
                        <td>{{ producto.stock }}</td>
                    </tr>
                    {% endfor %}
                </tbody>
            </table>
        </div>
    </body>

    </html>

# Configuracion de la vista de la base de datos
19. En mi_app/admin.py 
    ```bash	
    from django.contrib import admin
    from .models import Usuario, Producto
    # Register your models here.

    admin.site.register(Usuario)

    admin.site.register(Producto)

# Creamos el superusuario
20. Creamos el administrador de la base de datos
    ```bash	
    python manage.py createsuperuser