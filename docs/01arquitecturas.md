# Arquitecturas Web

??? abstract "Duración y criterios de evaluación"

    Duración estimada: 4 sesiones

    Resultado de aprendizaje:

    1. Selecciona las arquitecturas y tecnologías de programación Web en entorno servidor, analizando sus capacidades y características propias.

    Criterios de evaluación:

    1. Se han caracterizado y diferenciado los modelos de ejecución de código en el servidor y en el cliente Web.
    2. Se han reconocido las ventajas que proporciona la generación dinámica de páginas Web y sus diferencias con la inclusión de sentencias de guiones en el interior de las páginas Web.
    3. Se han identificado los mecanismos de ejecución de código en los servidores Web.
    4. Se han reconocido las funcionalidades que aportan los servidores de aplicaciones y su integración con los servidores Web.
    5. Se han identificado y caracterizado los principales lenguajes y tecnologías relacionados con la programación Web en entorno servidor.
    6. Se han verificado los mecanismos de integración de los lenguajes de marcas con los lenguajes de programación en entorno servidor.
    7. Se han reconocido y evaluado las herramientas de programación en entorno servidor.

Las arquitecturas web definen la forma en que las páginas de un sitio web están estructuradas y enlazadas entre sí.

## Cliente / Servidor

![Arquitectura Cliente Servidor](imagenes/01/clienteservidor.png)

N Clientes ←→ 1 Servidor / N servidores

Cliente hace la petición (*request*) y el servidor responde (*response*).

### Página web dinámica

* *Front-end* → Cliente / Navegador
    * HTML + CSS + JS
* *Back-end* → Servidor Web + BBDD
    * El HTML se genera tras la ejecución de código en el servidor
    * `.php`, `.jsp`, `.asp`, `.cgi`
* *Full-stack* → *front-end* + *back-end*

![Página web dinámica](imagenes/01/paginadinamica.png)

Las páginas web dinámicas utilizan recursos del servidor (base de datos, servicios web externos, etc...)

## Arquitectura de 3 capas

Hay que distinguir entre capas **físicas** (*tier*) y capas **lógicas** (*layer*).

### Tier

Ejemplo de arquitectura en tres capas físicas (*3 tier*):

* Servidor Web
* Servidor de Aplicaciones
* Servidor de base de datos

![Arquitectura de tres capas físicas](imagenes/01/tier3.png)

No confundir las capas con la cantidad de servidores. Actualmente se trabaja con arquitecturas con múltiples servidores en una misma capa física mediante un cluster, para ofrecer tolerancia a errores y escalabilidad horizontal.

### Layer

![Arquitectura de tres capas físicas](imagenes/01/layer3.png){ align=right }

En cambio, las capas lógicas (*layers*) organizan el código respecto a su funcionalidad:

* Presentación
* Negocio / Aplicación / Proceso
* Datos / Persistencia

Como se observa, cada una de las capas se puede implementar con diferentes lenguajes de programación y/o herramientas.

![Arquitectura de tres capas físicas en tres lógicas](imagenes/01/tierlayer.png)

## MVC

Model-View-Controller / Modelo-Vista-Controlador

![MVC](imagenes/01/mvc.png)

## Decisiones de diseño

* ¿Qué tamaño tiene el proyecto?
* ¿Qué lenguajes de programación conozco? ¿Vale la pena el esfuerzo de aprender uno nuevo?
* ¿Voy a usar herramientas de código abierto o herramientas propietarias? ¿Cuál es el coste de utilizar soluciones comerciales?
* ¿Voy a programar la aplicación yo solo o formaré parte de un grupo de programadores?
* ¿Cuento con algún servidor web o gestor de base de datos disponible o puedo decidir libremente utilizar el que crea necesario?
* ¿Qué tipo de licencia voy a aplicar a la aplicación que desarrolle?

## Herramientas

### Servidor Web

* Software que recibe peticiones HTTP
* Devuelve el recurso solicitado
    * HTML
    * JSON
* Apache: <https://httpd.apache.org/>
    * Software libre y multiplataforma
    * Modular → PHP, Python, Perl

### Servidor de Aplicaciones

* Software que ofrece servicios adicionales a los de un servidor web:
    * Clustering
    * Balanceo de carga
    * Tolerancia a fallos
* Tomcat: <http://tomcat.apache.org/>
    * Software libre y multiplataforma
    * Contenedor Web Java: Servlets, JSP

### Lenguajes en el servidor

Las aplicaciones que generan las páginas web se programan en:

* PHP
* JavaEE: Servlets / JSP
* Python
* ASP.NET → Visual Basic .NET / C#
* Ruby
* ...

#### JavaEE

![JavaEE](imagenes/01/javaee.png)

#### PHP

* Lenguaje de propósito general diseñado para el desarrollo de páginas web dinámicas
* Código embebido en el HTML
* Instrucciones entre etiquetas `<?php` y `?>`
* Multitud de librerías y frameworks:
    * Symfony, Laravel, Codeigniter, Zend

https://www.php.net/manual/es/index.php

![PHP](imagenes/01/php.jpg)

## Puesta en marcha

### XAMPP

Apache + MySQL + PHP + Perl

Descargar
Añadir permisos de ejecución
Instalar como root
http://localhost

Los archivos se colocan dentro de ....htdocs

``` console
sudo /opt/lampp/manager-linux-x64.run
```

### Docker

Docker (<https://www.docker.com>) es un gestor de contenedores, considerando un contenedor como un método de virtualización del sistema operativo.

El uso de contenedores requiere menos recursos que una máquina virtual, así pues, su lanzamiento y detención son más rápidos que las máquinas virtuales.

Así pues, *Docker* permite crear, probar e implementar aplicaciones rápidamente, a partir de una serie de plantillas que se conocen como imágenes de *Docker*.

Para ello es necesario tener instalado *Docker Desktop* (<https://www.docker.com/products/docker-desktop>) en nuestros entornos de desarrollo. En los ordenadores de clase ya está instalado. Para instalarlo en casa, en el caso de Windows, es necesario instalar previamente *WSL 2*, el cual es un subsistema de *Linux* dentro de *Windows*.

### Entorno de desarrollo

En este curso vamos a emplear VSCode..

Alternativas:

* Eclipse
* PhpStorm

### Hola Mundo

``` html+php hl_lines="9-11"
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hola Mundo</title>
</head>
<body>
    <?php
        echo "Hola Mundo";
    ?>
</body>
</html>
```

## Referencias

* Curso de introducción a Docker, por *Sergi García Barea* : <https://sergarb1.github.io/CursoIntroduccionADocker/>

## Actividades

101. Busca en internet cuales son los tres frameworks PHP más utilizados, y indica:

    * Nombre y URL
    * Año de creación
    * Última versión

102. Busca tres ofertas de trabajo de *desarrollo de software* en Infojobs en la provincia de Alicante que citen PHP:

    * Empresa + puesto + frameworks PHP + requísitos + sueldo + enlace a la oferta.

103. Una vez arrancado el servicio PHP, crea el archivo `info.php` y añade el siguiente fragmento de código:

    ``` php
    <?php phpinfo() ?>
    ```
    Anota los valores de:

    * Versión de PHP
    * *Loaded Configuration File*
    * `memory_limit`
    * `DOCUMENT_ROOT`

104. Repite el ejercicio anterior utilizando Docker, y comprueba de nuevo los valores. ¿Coinciden? ¿Por qué?

105. Abre el archivo `php.ini` e indica para qué sirven las siguientes propiedades y qué valores tienen:

    * `file_uploads`
    * `max_execution_time`
    * `short_open_tag`
