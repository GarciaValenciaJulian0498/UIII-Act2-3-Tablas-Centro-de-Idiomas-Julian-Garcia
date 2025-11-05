## 1. Procedimiento para crear carpeta del Proyecto: UIII_Centro_de_Idiomas_0498

Abre tu terminal o símbolo del sistema y navega hasta la ubicación donde quieres crear tu proyecto. Luego, ejecuta el siguiente comando:

    mkdir UIII_Centro_de_Idiomas_0498

## 2. Procedimiento para abrir VS Code sobre la carpeta UIII_Centro_de_Idiomas_0498

En la terminal, navega dentro de la carpeta que acabas de crear:

    cd     UIII_Centro_de_Idiomas_0498

Luego, abre VS Code en esa carpeta con el siguiente comando:

    code .
    
## 3. Procedimiento para abrir terminal en VS Code

Dentro de VS Code, puedes abrir la terminal yendo a Terminal > New Terminal o usando el atajo de teclado Ctrl + ` (la tecla de la tilde al lado del 1).

## 4. Procedimiento para crear carpeta entorno virtual “.venv” desde terminal de VS Code

En la terminal de VS Code, ejecuta:

    python -m venv .venv
    
## 5. Procedimiento para activar el entorno virtual

En Windows:

    .venv\Scripts\activate

En macOS/Linux:

    source .venv/bin/activate

Verás (.venv) al inicio de tu línea de comando, indicando que el entorno virtual está activo.

## 6. Procedimiento para activar intérprete de Python

Cuando el entorno virtual está activo, el intérprete de Python ya está configurado para usar el que está dentro de .venv. VS Code usualmente detecta esto automáticamente, pero puedes verificarlo en la barra inferior de VS Code (debería mostrar Python 3.x.x (.venv)). Si no es así, haz clic en la versión de Python en la barra inferior y selecciona el intérprete que está en tu carpeta .venv.

## 7. Procedimiento para instalar Django

Con el entorno virtual activado, instala Django:

    pip install django

## 8. Procedimiento para crear proyecto backend_Centro_Idiomas sin duplicar carpeta

    django-admin startproject backend_Centro_Idiomas .

El . al final es crucial para crear el proyecto en la carpeta actual UIII_Centro_de_Idiomas_0498 sin crear una subcarpeta duplicada.

## 9. Procedimiento para ejecutar servidor en el puerto 8498

Primero, asegúrate de estar en el directorio que contiene manage.py (que es UIII_Centro_de_Idiomas_0498 en este caso).


    python manage.py runserver 8498

## 10. Procedimiento para copiar y pegar el link en el navegador

El servidor te mostrará un enlace como http://127.0.0.1:8498/
Copia este enlace y pégalo en tu navegador. Deberías ver la página de bienvenida de Django.

## 11. Procedimiento para crear aplicación app_Centro_Idiomas

Asegúrate de estar en el mismo directorio donde se encuentra manage.py.

    python manage.py startapp app_Centro_Idiomas

## 12. Aquí los modelos models.py

Abre el archivo UIII_Centro_de_Idiomas_0498/app_Centro_Idiomas/models.py y agrega el siguiente código.

    from django.db import models

# ==========================================
# MODELO: IDIOMA
# ==========================================

    class Idioma(models.Model):
        nombre = models.CharField(max_length=50, unique=True)
        nivel = models.CharField(max_length=50)
        region = models.CharField(max_length=100, blank=True, null=True)
        descripcion = models.TextField(blank=True, null=True)
        codigo = models.CharField(max_length=10, unique=True)
        bandera_img = models.ImageField(upload_to='banderas/', blank=True, null=True)

        def __str__(self):
            return self.nombre

# ==========================================
# MODELO: PROFESOR
# ==========================================
    class Profesor(models.Model):
        nombre = models.CharField(max_length=100)
        ap_paterno = models.CharField(max_length=100)
        ap_materno = models.CharField(max_length=100, blank=True, null=True)
        telefono = models.CharField(max_length=15, blank=True, null=True)
        correo = models.EmailField(unique=True)
        id_idioma = models.ForeignKey(Idioma, on_delete=models.CASCADE, related_name="profesores")
    
        def __str__(self):
            return f"{self.nombre} {self.ap_paterno}"

# ==========================================
# MODELO: CLASE
# ==========================================
    class Clase(models.Model):
        nombre = models.CharField(max_length=150)
        descripcion = models.TextField(blank=True, null=True)
        capacidad = models.PositiveIntegerField()
        nivel = models.CharField(max_length=50, choices=[
            ('Básico A1', 'Básico A1'),
            ('Básico A2', 'Básico A2'),
            ('Intermedio B1', 'Intermedio B1'),
            ('Intermedio B2', 'Intermedio B2'),
            ('Avanzado C1', 'Avanzado C1'),
            ('Avanzado C2', 'Avanzado C2')
        ])
        id_profesor = models.ForeignKey(Profesor, on_delete=models.CASCADE, related_name="clases")
        idioma = models.ForeignKey(Idioma, on_delete=models.CASCADE, related_name="clases_idioma")

        def __str__(self):
            return f"{self.nombre} ({self.idioma.nombre})"
            
## 12.5 Procedimiento para realizar las migraciones (makemigrations y migrate)

Asegúrate de estar en el directorio de manage.py y con el entorno virtual activado.

    python manage.py makemigrations app_Centro_Idiomas
    python manage.py migrate
    
## 13. Primero trabajamos con el MODELO: IDIOMA

Nos centraremos en implementar el CRUD para el modelo Idioma por ahora.

## 14. En views de app_Centro_Idiomas crear las funciones con sus códigos correspondientes (inicio_centro_idiomas, agregar_idioma, actualizar_idioma, realizar_actualizacion_idioma, borrar_idioma)

Abre el archivo UIII_Centro_de_Idiomas_0498/app_Centro_Idiomas/views.py y reemplaza su contenido con el siguiente código:

    from django.shortcuts import render, redirect, get_object_or_404
    from .models import Idioma, Profesor, Clase # Importa todos los modelos

    def inicio_centro_idiomas(request):
        return render(request, 'inicio.html')
    
    def agregar_idioma(request):
        if request.method == 'POST':
            nombre = request.POST.get('nombre')
            nivel = request.POST.get('nivel')
            region = request.POST.get('region')
            descripcion = request.POST.get('descripcion')
            codigo = request.POST.get('codigo')
            # bandera_img = request.FILES.get('bandera_img') # Para manejar imágenes, se necesita configurar MEDIA_ROOT y MEDIA_URL en settings.py
    
            idioma = Idioma(
                nombre=nombre,
                nivel=nivel,
                region=region,
                descripcion=descripcion,
                codigo=codigo,
                # bandera_img=bandera_img
            )
            idioma.save()
            return redirect('ver_idiomas')
        return render(request, 'idioma/agregar_idioma.html')
    
    def ver_idiomas(request):
        idiomas = Idioma.objects.all()
        return render(request, 'idioma/ver_idiomas.html', {'idiomas': idiomas})
    
    def actualizar_idioma(request, id_idioma):
        idioma = get_object_or_404(Idioma, pk=id_idioma)
        if request.method == 'POST':
            idioma.nombre = request.POST.get('nombre')
            idioma.nivel = request.POST.get('nivel')
            idioma.region = request.POST.get('region')
            idioma.descripcion = request.POST.get('descripcion')
            idioma.codigo = request.POST.get('codigo')
            # if 'bandera_img' in request.FILES:
            #    idioma.bandera_img = request.FILES['bandera_img']
            idioma.save()
            return redirect('ver_idiomas')
        return render(request, 'idioma/actualizar_idioma.html', {'idioma': idioma})
    
    def borrar_idioma(request, id_idioma):
        idioma = get_object_or_404(Idioma, pk=id_idioma)
        if request.method == 'POST':
            idioma.delete()
            return redirect('ver_idiomas')
        return render(request, 'idioma/borrar_idioma.html', {'idioma': idioma})
    
    # **IMPORTANTE**: Las funciones para Profesor y Clase se dejarán pendientes como se indicó.
    # # Vistas para Profesor (PENDIENTE)
    # def agregar_profesor(request):
    #     pass
    # def ver_profesores(request):
    #     pass
    # def actualizar_profesor(request, id_profesor):
    #     pass
    # def borrar_profesor(request, id_profesor):
    #     pass
    
    # # Vistas para Clase (PENDIENTE)
    # def agregar_clase(request):
    #     pass
    # def ver_clases(request):
    #     pass
    # def actualizar_clase(request, id_clase):
    #     pass
    # def borrar_clase(request, id_clase):
    #     pass
    
## 15. Crear la carpeta “templates” dentro de “app_Centro_Idiomas”.

En VS Code, haz clic derecho en app_Centro_Idiomas y selecciona New Folder, nómbrala templates.

## 16. En la carpeta templates crear los archivos html (base.html, header.html, navbar.html, footer.html, inicio.html).

Dentro de app_Centro_Idiomas/templates, crea estos archivos:

base.html

header.html

navbar.html

footer.html

inicio.html

## 17. En el archivo base.html agregar bootstrap para CSS y JS.

Abre UIII_Centro_de_Idiomas_0498/app_Centro_Idiomas/templates/base.html y pega el siguiente código:

    <!DOCTYPE html>
    <html lang="es">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>{% block title %}Centro de Idiomas{% endblock %}</title>
        <!-- Bootstrap CSS -->
        <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">
        <!-- Font Awesome para iconos -->
        <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
        <style>
            body {
                display: flex;
                flex-direction: column;
                min-height: 100vh;
            }
            .content {
                flex: 1;
                padding-bottom: 70px; /* Espacio para el footer fijo */
            }
            .footer {
                position: fixed;
                bottom: 0;
                width: 100%;
                height: 60px; /* Altura del footer */
                background-color: #f8f9fa; /* Color de fondo suave */
                color: #6c757d; /* Color de texto suave */
                text-align: center;
                line-height: 60px;
                box-shadow: 0 -2px 5px rgba(0,0,0,.1); /* Sombra sutil */
            }
        </style>
    </head>
    <body>
        {% include 'navbar.html' %}
    
        <div class="container mt-4 content">
            {% block content %}
            {% endblock %}
        </div>
    
        {% include 'footer.html' %}
    
        <!-- Bootstrap JS -->
        <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-YvpcrYf0tY3lHB60NNkmXc5s9fDVZLESaAA55NDzOxhy9GkcIdslK1eN7N6jIeHz" crossorigin="anonymous"></script>
    </body>
    </html>
    
## 18. En el archivo navbar.html incluir las opciones...

Abre UIII_Centro_de_Idiomas_0498/app_Centro_Idiomas/templates/navbar.html y pega el siguiente código:

    <nav class="navbar navbar-expand-lg navbar-light" style="background-color: #e0f7fa;"> <!-- Color azul claro suave -->
        <div class="container-fluid">
            <a class="navbar-brand" href="{% url 'inicio_centro_idiomas' %}">
                <i class="fas fa-globe-americas me-2"></i>Sistema de Administración Centro de Idiomas
            </a>
            <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNavDropdown" aria-controls="navbarNavDropdown" aria-expanded="false" aria-label="Toggle navigation">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarNavDropdown">
                <ul class="navbar-nav ms-auto">
                    <li class="nav-item">
                        <a class="nav-link active" aria-current="page" href="{% url 'inicio_centro_idiomas' %}"><i class="fas fa-home me-1"></i>Inicio</a>
                    </li>
                    <li class="nav-item dropdown">
                        <a class="nav-link dropdown-toggle" href="#" id="idiomasDropdown" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                            <i class="fas fa-language me-1"></i>Idiomas
                        </a>
                        <ul class="dropdown-menu" aria-labelledby="idiomasDropdown">
                            <li><a class="dropdown-item" href="{% url 'agregar_idioma' %}">Agregar Idioma</a></li>
                            <li><a class="dropdown-item" href="{% url 'ver_idiomas' %}">Ver Idiomas</a></li>
                            <!-- Los enlaces de Actualizar y Borrar se harán desde la tabla de ver_idiomas -->
                        </ul>
                    </li>
                    <li class="nav-item dropdown">
                        <a class="nav-link dropdown-toggle" href="#" id="profesoresDropdown" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                            <i class="fas fa-chalkboard-teacher me-1"></i>Profesores
                        </a>
                        <ul class="dropdown-menu" aria-labelledby="profesoresDropdown">
                            <li><a class="dropdown-item" href="#">Agregar Profesor (Pendiente)</a></li>
                            <li><a class="dropdown-item" href="#">Ver Profesores (Pendiente)</a></li>
                        </ul>
                    </li>
                    <li class="nav-item dropdown">
                        <a class="nav-link dropdown-toggle" href="#" id="clasesDropdown" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                            <i class="fas fa-book-open me-1"></i>Clases
                        </a>
                        <ul class="dropdown-menu" aria-labelledby="clasesDropdown">
                            <li><a class="dropdown-item" href="#">Agregar Clases (Pendiente)</a></li>
                            <li><a class="dropdown-item" href="#">Ver Clases (Pendiente)</a></li>
                        </ul>
                    </li>
                </ul>
            </div>
        </div>
    </nav>
    
## 19. En el archivo footer.html incluir derechos de autor, fecha del sistema y “Creado por Ing. Eliseo Nava, Cbtis 128” y mantenerla fija al final de la página.

Abre UIII_Centro_de_Idiomas_0498/app_Centro_Idiomas/templates/footer.html y pega el siguiente código:

    <footer class="footer">
        <div class="container">
            <span>&copy; {{ "now"|date:"Y" }} Centro de Idiomas. Todos los derechos reservados. | Creado por Ing. Eliseo Nava, Cbtis 128</span>
        </div>
    </footer>
    
## 20. En el archivo inicio.html se usa para colocar información del sistema más una imagen tomada desde la red sobre un centro de idiomas.

Abre UIII_Centro_de_Idiomas_0498/app_Centro_Idiomas/templates/inicio.html y pega el siguiente código.

    {% extends 'base.html' %}
    
    {% block title %}Inicio - Centro de Idiomas{% endblock %}
    
    {% block content %}
    <div class="container text-center py-5">
        <h1 class="display-4 mb-4" style="color: #00796b;">Bienvenido al Sistema de Administración del Centro de Idiomas</h1>
        <p class="lead" style="color: #4db6ac;">Gestione de manera eficiente sus idiomas, profesores y clases.</p>
        <hr class="my-4">
        <p style="color: #80cbc4;">Utilice el menú de navegación para explorar las diferentes secciones del sistema.</p>
    
        <div class="mt-5">
            <p>Una imagen inspiradora sobre el aprendizaje de idiomas:</p>
            <img src="https://images.pexels.com/photos/380769/pexels-photo-380769.jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=1"
                 alt="Centro de Idiomas"
                 class="img-fluid rounded shadow-lg"
                 style="max-height: 500px; object-fit: cover;">
            <small class="d-block text-muted mt-2">Imagen de Pexels.com</small>
        </div>
    </div>
    {% endblock %}


## 21. Crear la subcarpeta idioma dentro de app_Centro_Idiomas\templates.

Haz clic derecho en app_Centro_Idiomas/templates y selecciona New Folder, nómbrala idioma.

## 22. Crear los archivos html con su código correspondientes de (agregar_idioma.html, ver_idiomas.html, actualizar_idioma.html, borrar_idioma.html) dentro de app_Centro_Idiomas\templates\idioma.
agregar_idioma.html

Abre UIII_Centro_de_Idiomas_0498/app_Centro_Idiomas/templates/idioma/agregar_idioma.html y pega:

    {% extends 'base.html' %}
    
    {% block title %}Agregar Idioma{% endblock %}
    
    {% block content %}
    <div class="container mt-4">
        <div class="card shadow-sm p-4" style="background-color: #e3f2fd;"> <!-- Fondo azul claro -->
            <h2 class="card-title text-center mb-4" style="color: #1a237e;">Agregar Nuevo Idioma</h2>
            <form method="post" enctype="multipart/form-data">
                {% csrf_token %}
                <div class="mb-3">
                    <label for="nombre" class="form-label">Nombre del Idioma:</label>
                    <input type="text" class="form-control" id="nombre" name="nombre" required>
                </div>
                <div class="mb-3">
                    <label for="nivel" class="form-label">Nivel:</label>
                    <input type="text" class="form-control" id="nivel" name="nivel" required>
                </div>
                <div class="mb-3">
                    <label for="region" class="form-label">Región:</label>
                    <input type="text" class="form-control" id="region" name="region">
                </div>
                <div class="mb-3">
                    <label for="descripcion" class="form-label">Descripción:</label>
                    <textarea class="form-control" id="descripcion" name="descripcion" rows="3"></textarea>
                </div>
                <div class="mb-3">
                    <label for="codigo" class="form-label">Código (ej. EN, ES, FR):</label>
                    <input type="text" class="form-control" id="codigo" name="codigo" required>
                </div>
                <!-- Para la imagen de la bandera, se necesita configurar MEDIA_ROOT y MEDIA_URL en settings.py -->
                <!--
                <div class="mb-3">
                    <label for="bandera_img" class="form-label">Bandera (Imagen):</label>
                    <input type="file" class="form-control" id="bandera_img" name="bandera_img" accept="image/*">
                </div>
                -->
                <button type="submit" class="btn btn-primary" style="background-color: #26c6da; border-color: #26c6da;">Guardar Idioma</button>
                <a href="{% url 'ver_idiomas' %}" class="btn btn-secondary ms-2">Cancelar</a>
            </form>
        </div>
    </div>
    {% endblock %}
ver_idiomas.html

Abre UIII_Centro_de_Idiomas_0498/app_Centro_Idiomas/templates/idioma/ver_idiomas.html y pega:

    {% extends 'base.html' %}
    
    {% block title %}Ver Idiomas{% endblock %}
    
    {% block content %}
    <div class="container mt-4">
        <div class="card shadow-sm p-4" style="background-color: #e0f2f7;"> <!-- Fondo azul claro -->
            <h2 class="card-title text-center mb-4" style="color: #006064;">Listado de Idiomas</h2>
            <a href="{% url 'agregar_idioma' %}" class="btn btn-success mb-3" style="background-color: #4db6ac; border-color: #4db6ac;"><i class="fas fa-plus-circle me-1"></i> Agregar Nuevo Idioma</a>
    
            {% if idiomas %}
            <div class="table-responsive">
                <table class="table table-hover table-striped">
                    <thead class="table-dark" style="background-color: #00796b;">
                        <tr>
                            <th scope="col">ID</th>
                            <th scope="col">Nombre</th>
                            <th scope="col">Nivel</th>
                            <th scope="col">Región</th>
                            <th scope="col">Descripción</th>
                            <th scope="col">Código</th>
                            <th scope="col">Acciones</th>
                        </tr>
                    </thead>
                    <tbody>
                        {% for idioma in idiomas %}
                        <tr>
                            <th scope="row">{{ idioma.id }}</th>
                            <td>{{ idioma.nombre }}</td>
                            <td>{{ idioma.nivel }}</td>
                            <td>{{ idioma.region|default:"N/A" }}</td>
                            <td>{{ idioma.descripcion|default:"Sin descripción" }}</td>
                            <td>{{ idioma.codigo }}</td>
                            <td>
                                <a href="{% url 'actualizar_idioma' idioma.id %}" class="btn btn-sm btn-info me-1" style="background-color: #26c6da; border-color: #26c6da;"><i class="fas fa-edit"></i> Editar</a>
                                <a href="{% url 'borrar_idioma' idioma.id %}" class="btn btn-sm btn-danger" style="background-color: #ff7043; border-color: #ff7043;"><i class="fas fa-trash-alt"></i> Borrar</a>
                            </td>
                        </tr>
                        {% endfor %}
                    </tbody>
                </table>
            </div>
            {% else %}
            <div class="alert alert-info text-center" role="alert" style="background-color: #e0f2f7; color: #00796b; border-color: #b2ebf2;">
                No hay idiomas registrados aún.
            </div>
            {% endif %}
        </div>
    </div>
    {% endblock %}
actualizar_idioma.html

Abre UIII_Centro_de_Idiomas_0498/app_Centro_Idiomas/templates/idioma/actualizar_idioma.html y pega:

    {% extends 'base.html' %}
    
    {% block title %}Actualizar Idioma{% endblock %}
    
    {% block content %}
    <div class="container mt-4">
        <div class="card shadow-sm p-4" style="background-color: #e8f5e9;"> <!-- Fondo verde claro suave -->
            <h2 class="card-title text-center mb-4" style="color: #2e7d32;">Actualizar Idioma: {{ idioma.nombre }}</h2>
            <form method="post" enctype="multipart/form-data">
                {% csrf_token %}
                <div class="mb-3">
                    <label for="nombre" class="form-label">Nombre del Idioma:</label>
                    <input type="text" class="form-control" id="nombre" name="nombre" value="{{ idioma.nombre }}" required>
                </div>
                <div class="mb-3">
                    <label for="nivel" class="form-label">Nivel:</label>
                    <input type="text" class="form-control" id="nivel" name="nivel" value="{{ idioma.nivel }}" required>
                </div>
                <div class="mb-3">
                    <label for="region" class="form-label">Región:</label>
                    <input type="text" class="form-control" id="region" name="region" value="{{ idioma.region|default_if_none:'' }}">
                </div>
                <div class="mb-3">
                    <label for="descripcion" class="form-label">Descripción:</label>
                    <textarea class="form-control" id="descripcion" name="descripcion" rows="3">{{ idioma.descripcion|default_if_none:'' }}</textarea>
                </div>
                <div class="mb-3">
                    <label for="codigo" class="form-label">Código (ej. EN, ES, FR):</label>
                    <input type="text" class="form-control" id="codigo" name="codigo" value="{{ idioma.codigo }}" required>
                </div>
                <!-- Para la imagen de la bandera, se necesita configurar MEDIA_ROOT y MEDIA_URL en settings.py -->
                <!--
                <div class="mb-3">
                    <label for="bandera_img" class="form-label">Bandera (Imagen actual: {% if idioma.bandera_img %}{{ idioma.bandera_img.url }}{% else %}Ninguna{% endif %}):</label>
                    <input type="file" class="form-control" id="bandera_img" name="bandera_img" accept="image/*">
                </div>
                -->
                <button type="submit" class="btn btn-primary" style="background-color: #66bb6a; border-color: #66bb6a;">Actualizar Idioma</button>
                <a href="{% url 'ver_idiomas' %}" class="btn btn-secondary ms-2">Cancelar</a>
            </form>
        </div>
    </div>
    {% endblock %}
borrar_idioma.html

Abre UIII_Centro_de_Idiomas_0498/app_Centro_Idiomas/templates/idioma/borrar_idioma.html y pega:

    {% extends 'base.html' %}
    
    {% block title %}Borrar Idioma{% endblock %}
    
    {% block content %}
    <div class="container mt-4">
        <div class="card shadow-sm p-4 bg-light">
            <h2 class="card-title text-center mb-4" style="color: #d32f2f;">Confirmar Eliminación</h2>
            <div class="alert alert-warning text-center" role="alert" style="background-color: #ffe0b2; color: #e65100; border-color: #ffcc80;">
                <i class="fas fa-exclamation-triangle me-2"></i> ¿Estás seguro de que quieres eliminar el idioma "<strong>{{ idioma.nombre }}</strong>"?
                Esta acción no se puede deshacer.
            </div>
            <dl class="row mb-4">
                <dt class="col-sm-3">ID:</dt>
                <dd class="col-sm-9">{{ idioma.id }}</dd>
    
                <dt class="col-sm-3">Nombre:</dt>
                <dd class="col-sm-9">{{ idioma.nombre }}</dd>
    
                <dt class="col-sm-3">Nivel:</dt>
                <dd class="col-sm-9">{{ idioma.nivel }}</dd>
    
                <dt class="col-sm-3">Región:</dt>
                <dd class="col-sm-9">{{ idioma.region|default:"N/A" }}</dd>
    
                <dt class="col-sm-3">Código:</dt>
                <dd class="col-sm-9">{{ idioma.codigo }}</dd>
            </dl>
    
            <form method="post" class="text-center">
                {% csrf_token %}
                <button type="submit" class="btn btn-danger me-2" style="background-color: #ef5350; border-color: #ef5350;"><i class="fas fa-trash-alt me-1"></i> Sí, Eliminar</button>
                <a href="{% url 'ver_idiomas' %}" class="btn btn-secondary"><i class="fas fa-times-circle me-1"></i> Cancelar</a>
            </form>
        </div>
    </div>
    {% endblock %}

## 23. No utilizar forms.py.

Confirmado. Las validaciones se manejan directamente en las vistas con los datos request.POST.

## 24. Procedimiento para crear el archivo urls.py en app_Centro_Idiomas con el código correspondiente para acceder a las funciones de views.py para operaciones de CRUD en idiomas.

Haz clic derecho en app_Centro_Idiomas y selecciona New File, nómbrala urls.py. Pega el siguiente código:

    from django.urls import path
    from . import views
    
    urlpatterns = [
    path('', views.inicio_centro_idiomas, name='inicio_centro_idiomas'),
    path('idiomas/agregar/', views.agregar_idioma, name='agregar_idioma'),
    path('idiomas/', views.ver_idiomas, name='ver_idiomas'),
    path('idiomas/actualizar/<int:id_idioma>/', views.actualizar_idioma, name='actualizar_idioma'),
    path('idiomas/borrar/<int:id_idioma>/', views.borrar_idioma, name='borrar_idioma'),
    ]

## 25. Procedimiento para agregar app_Centro_Idiomas en settings.py de backend_Centro_Idiomas

Abre UIII_Centro_de_Idiomas_0498/backend_Centro_Idiomas/settings.py y busca la lista INSTALLED_APPS. Agrega 'app_Centro_Idiomas' a la lista.

    INSTALLED_APPS = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
        'app_Centro_Idiomas', # <-- Agrega esta línea
    ]

## 26. Realizar las configuraciones correspondiente a urls.py de backend_Centro_Idiomas para enlazar con app_Centro_Idiomas

Abre UIII_Centro_de_Idiomas_0498/backend_Centro_Idiomas/urls.py y modifica su contenido para incluir las URLs de app_Centro_Idiomas:

    from django.contrib import admin
    from django.urls import path, include
    
    urlpatterns = [
        path('admin/', admin.site.urls),
        path('', include('app_Centro_Idiomas.urls')), # <-- Agrega esta línea para incluir las URLs de tu app
    ]
    
## 27. Procedimiento para registrar los modelos en admin.py y volver a realizar las migraciones.

Abre UIII_Centro_de_Idiomas_0498/app_Centro_Idiomas/admin.py y registra el modelo Idioma:

    from django.contrib import admin
    from .models import Idioma, Profesor, Clase
    
    # Registrar los modelos aquí.
    admin.site.register(Idioma)
    # admin.site.register(Profesor) # Dejar pendiente
    # admin.site.register(Clase)    # Dejar pendiente

No es necesario volver a realizar las migraciones si solo registras un modelo existente en admin.py. Las migraciones son para cambios en la estructura de la base de datos (models.py).

## 27 (Reiteración). Por lo pronto solo trabajar con “Idioma” dejar pendiente # MODELO: PROFESOR y # MODELO: CLASE

Confirmado, todas las vistas y plantillas se enfocan en Idioma.

## 28 (Continuación). Utilizar colores suaves, atractivos y modernos, el código de las páginas web sencillas.

Ya hemos incorporado Bootstrap y estilos en línea para lograr una estética limpia y agradable en los archivos HTML anteriores. Los colores elegidos (tonos de azul, verde y naranja suaves) buscan ser modernos y atractivos.

## 28 (Reiteración). No validar entrada de datos.

Confirmado. Como se especificó, no se implementarán validaciones de datos en este proyecto. Se asume que los datos ingresados son correctos.

## 29. Al inicio crear la estructura completa de carpetas y archivos.

Hasta este punto, hemos creado la siguiente estructura de archivos y carpetas:


    UIII_Centro_de_Idiomas_0498/
    ├── .venv/                         # Entorno virtual
    ├── backend_Centro_Idiomas/        # Proyecto Django principal
    │   ├── __init__.py
    │   ├── asgi.py
    │   ├── settings.py
    │   ├── urls.py
    │   └── wsgi.py
    ├── app_Centro_Idiomas/            # Aplicación Django del centro de idiomas
    │   ├── migrations/
    │   │   └── __init__.py
    │   ├── __init__.py
    │   ├── admin.py
    │   ├── apps.py
    │   ├── models.py
    │   ├── tests.py
    │   ├── views.py
    │   ├── urls.py                    # Creado manualmente
    │   └── templates/
    │       ├── base.html
    │       ├── footer.html
    │       ├── header.html (no usado directamente, contenido en base.html)
    │       ├── inicio.html
    │       ├── navbar.html
    │       └── idioma/                # Subcarpeta para plantillas de Idioma
    │           ├── agregar_idioma.html
    │           ├── actualizar_idioma.html
    │           ├── borrar_idioma.html
    │           └── ver_idiomas.html
    └── manage.py

Esta estructura está lista para funcionar con los requisitos dados.

## 30. Proyecto totalmente funcional.

Con todos los pasos anteriores completados, tu proyecto debería estar funcional para el CRUD del modelo Idioma. Ahora te daré el último paso para ejecutarlo.

## 31. Finalmente ejecutar servidor en el puerto 8498.

Para ejecutar el servidor, asegúrate de que tu terminal esté en la raíz de tu proyecto (UIII_Centro_de_Idiomas_0498/), donde se encuentra manage.py, y que tu entorno virtual esté activado.

Si el servidor ya estaba corriendo, ciérralo (generalmente con Ctrl+C). Luego, ejecuta:

    python manage.py runserver 8498

Una vez que el servidor esté activo, abre tu navegador web y visita:

http://127.0.0.1:8498/
