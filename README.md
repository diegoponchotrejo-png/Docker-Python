# Students App

Aplicación de ejemplo construida con Flask y PostgreSQL, pensada para enseñar
el flujo completo: primero clonar y preparar el entorno local, después
Dockerizar la aplicación.

## Cómo se conecta a la base de datos

La app se conecta a PostgreSQL con `psycopg2`, indicando host, usuario,
contraseña y nombre de la base de datos (ver [`app.py`](app.py)):

```python
psycopg2.connect(
    host=os.getenv("DB_HOST"),
    port=os.getenv("DB_PORT"),
    user=os.getenv("DB_USER"),
    password=os.getenv("DB_PASSWORD"),
    dbname=os.getenv("DB_NAME"),
)
```

Ninguno de esos valores está escrito directamente en el código
(`hardcodeado`). Cada uno se lee con `os.getenv(...)` desde una variable de
entorno, y esas variables se definen en un archivo `.env` que cada quien crea
localmente a partir de [`.env.example`](.env.example) y que **nunca se sube al
repositorio** (está en [`.gitignore`](.gitignore)). Así, las credenciales
pueden cambiar entre tu máquina, la de un compañero o producción, sin tocar
una sola línea de código.

## 1. Entorno local (sin Docker)

Este paso no requiere tener una base de datos levantada. La app arranca igual
y, si no encuentra la base de datos, la página de alumnos se muestra vacía en
vez de dar un error.

### Crear y activar el entorno virtual

```bash
python3 -m venv .venv
source .venv/bin/activate      # En Windows: .venv\Scripts\activate
```

### Instalar las dependencias

```bash
pip install -r requirements.txt
```

### Configurar las variables de entorno

```bash
cp .env.example .env
```

Puedes revisar y ajustar los valores en `.env` si lo necesitas.

### Ejecutar la aplicación

```bash
python3 app.py
```

Abre [http://localhost:5001](http://localhost:5001). Verás "No hay alumnos
registrados." porque todavía no hay ninguna base de datos disponible — y no
un error 500, que es justo el comportamiento esperado en este punto.

## 2. Ejecutar con Docker

Primero abre Docker Desktop y espera a que termine de iniciar.

### Crear la imagen

```bash
docker build -t docker-python .
```

### Iniciar el contenedor

```bash
docker run --rm -p 5001:5001 docker-python
```

Abre [http://127.0.0.1:5001](http://127.0.0.1:5001) en el navegador.
Deja la terminal abierta mientras usas la aplicación. Para detenerla,
presiona Ctrl + C.

En `-p 5001:5001`, el primer puerto es el de tu computadora y el segundo
es el de la aplicación dentro del contenedor.

Este comando inicia solamente la aplicación, no PostgreSQL. Sin una base
de datos disponible, la lista de alumnos se muestra vacía.
El archivo `database_students.sql` contiene los datos de ejemplo para
cargarlos cuando se configure PostgreSQL.

El archivo `.dockerignore` evita copiar el entorno virtual, el historial
de Git y el archivo `.env` dentro de la imagen.

## Estructura del proyecto

```
app.py                    # Aplicación Flask (rutas + conexión a la BD)
templates/                # Vistas Jinja2 (Bootstrap)
database_students.sql     # Datos iniciales para PostgreSQL
.env.example               # Plantilla de variables de entorno
```
