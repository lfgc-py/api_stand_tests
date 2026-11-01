# Pruebas de API -- Urban Grocers

Proyecto de práctica de **QA / API Testing** que automatiza pruebas
sobre la API de Urban Grocers. El entorno simula una aplicación de
distribución de productos y permite comprobar el comportamiento de
distintos recursos mediante solicitudes HTTP.

El proyecto utiliza **Python**, **Requests** y **Pytest** para enviar
peticiones, validar respuestas y comprobar el comportamiento del
servicio.

## Objetivo

Verificar el funcionamiento de los principales recursos de la API y,
especialmente, probar la creación de usuarios mediante casos positivos y
negativos.

## Recursos de la API

La URL base y las rutas utilizadas como referencia son:

  --------------------------------------------------------------------------
  Recurso                 Ruta                       Descripción
  ----------------------- -------------------------- -----------------------
  Documentación           `/docs/`                   Consulta de la
                                                     documentación
                                                     disponible de la API.

  Logs principales        `/api/logs/main/`          Obtención de los
                                                     registros principales
                                                     del servicio.

  Tabla de usuarios       `/api/users/table/`        Consulta de la tabla de
                                                     usuarios registrados.

  Crear usuario           `/api/v1/users/`           Envío de solicitudes
                                                     para crear usuarios.

  Kits de productos       `/api/v1/products/kits/`   Consulta de los kits de
                                                     productos.
  --------------------------------------------------------------------------

**URL base del entorno de práctica:**

``` text
https://cnt-4a5f0480-7bdd-46cb-994a-7a127a345fbc.containerhub.tripleten-services.com
```

Las rutas se concatenan con la URL base para construir las direcciones
completas de cada recurso.

## Tecnologías y herramientas

-   **Python:** lenguaje utilizado para desarrollar las pruebas.
-   **Pytest:** framework para organizar y ejecutar los casos de prueba.
-   **Requests:** biblioteca de Python para realizar solicitudes HTTP a
    la API.

## Estructura del proyecto

La estructura puede variar según la organización local de los archivos.
De acuerdo con el código compartido, los módulos principales son:

``` text
proyecto/
├── create_user_test.py       # Casos de prueba para la creación de usuarios
├── sender_stand_request.py   # Funciones para enviar solicitudes a la API
├── data.py                   # Datos utilizados en las solicitudes
└── README.md                 # Documentación del proyecto
```

## Pruebas implementadas

El archivo principal de pruebas verifica la creación de usuarios a
través del endpoint:

``` text
POST /api/v1/users/
```

### Casos positivos

Comprueban que la API permita crear usuarios cuando el nombre cumple las
condiciones establecidas:

  -----------------------------------------------------------------------
  Caso                    Dato de prueba          Resultado esperado
  ----------------------- ----------------------- -----------------------
  Nombre con longitud     `Aa` (2 caracteres)     HTTP 201 y token de
  mínima                                          autenticación no vacío.

  Nombre con longitud     `Aaaaaaaaaaaaaaa` (15   HTTP 201 y token de
  máxima                  caracteres)             autenticación no vacío.
  -----------------------------------------------------------------------

Además, el código consulta la tabla de usuarios y construye una cadena
con los datos del usuario y su token para buscarlo en la respuesta.

### Casos negativos

Comprueban que la API rechace nombres que no cumplen las reglas de
validación:

  -----------------------------------------------------------------------
  Caso                    Dato de prueba          Resultado esperado
  ----------------------- ----------------------- -----------------------
  Nombre demasiado corto  `A` (1 carácter)        HTTP 400.

  Nombre demasiado largo  `Aaaaaaaaaaaaaaaa` (16  HTTP 400.
                          caracteres)             

  Nombre con espacios     `Lu is`                 HTTP 400.

  Nombre con símbolo      `@na`                   HTTP 400.
  especial                                        

  Nombre con números      `Andr3a`                HTTP 400.

  Campo `firstName`       Se elimina la propiedad HTTP 400.
  ausente                 del cuerpo              

  Nombre vacío            `""`                    HTTP 400.

  Nombre de tipo numérico `123`                   HTTP 400.
  -----------------------------------------------------------------------

En los casos negativos también se validan el código de error y, cuando
corresponde, el mensaje devuelto por la API.

## Ejecución de las pruebas

### 1. Requisitos previos

Tener instalado:

-   Python 3.
-   pip.

Se recomienda ejecutar los comandos desde la terminal, dentro de la
carpeta del proyecto.

### 2. Instalar las dependencias

``` bash
pip install pytest requests
```

### 3. Ejecutar todas las pruebas

``` bash
python -m pytest -v
```

La opción `-v` (*verbose*) muestra el nombre y el resultado individual
de cada prueba.

### 4. Ejecutar un archivo específico

``` bash
python -m pytest -v create_user_test.py
```

## Criterios de validación

Las pruebas de creación de usuarios consideran, entre otros, los
siguientes aspectos:

-   Código de estado HTTP de la respuesta.
-   Presencia de un `authToken` no vacío en una creación exitosa.
-   Código y mensaje de error ante datos no válidos.
-   Comportamiento de la API ante nombres en los límites de longitud
    permitidos.
-   Consulta de la tabla de usuarios después de crear un usuario.

## Alcance del proyecto

Este proyecto está enfocado en la práctica de pruebas funcionales de
API. Incluye consultas a distintos recursos del entorno y pruebas
automatizadas de creación de usuarios.

No se debe interpretar como una auditoría integral de la aplicación ni
como una evaluación de seguridad, rendimiento o carga.

## Nota técnica

En el código actual, la expresión:

``` python
user_table_response.text.count(str_user) == 1
```

realiza una comparación, pero **no valida el resultado como aserción**.
Para que Pytest compruebe que el usuario aparece exactamente una vez en
la tabla, se recomienda escribir:

``` python
assert user_table_response.text.count(str_user) == 1
```

Así, la prueba fallará si el usuario no aparece o aparece más de una
vez.

------------------------------------------------------------------------

**Proyecto de práctica de QA Engineering -- API Testing con Python y
Pytest.**