# Uso avanzado de Frameworks

## Plantillas con Blade

## Integración de CSS y JS

## Autenticación y autorización

https://igomis.github.io/apunts/docs/4.8.Laravel.html

!!! tip Proyecto
    1. Pregunta
    2. Respuesta
    3. Relacion P-R
    4. Autenticación
    5. Relacion con usuario en modelo
    6. Relacionar con migraciones
    7. Relacionar en vistas
    8. Proteger edit, update por Auth:id
    9. página profile ... listado de preguntas y respuestas del usuario

    Utilizar la fución diffForHumas para ellapsed time

Antes se hacía así....

Run scaffolding

``` console
php artisan make:auth
```

Crea el `HomeController`, que utiliza el middleware `auth`.

### Middleware

Componente que se situa entre el enrutador y el controlador.

Explicar

Ejecutar las migraciones

Explorar los ficheros generados

Auth:routes() ???


## Laravel Breeze

Laravel Breeze es un *starter kit* que se compone de un conjunto de rutas, controladores y vistas necesarias para regitrar y autenticar usuarios en cualquier aplicación.

Las vistas están creadas con Laravel y los estilos con Tailwind CSS.

Primero hemos de añadir la dependencia mediante Composer:

``` console
composer require laravel/breeze --dev
```

Tras ello, ejecutaremos el comando `breeze:install` para generar todo el contenido necesario (rutas, vistas, controladores, recursos, ...), compilaremos todos los *assets* CSS y generaremos las migraciones:

``` console
php artisan breeze:install

npm install
npm run dev
php artisan migrate
```


!!! tip "Mailtrap"
    Para poder probar el envío de correo
    mailtrap.io, servidor de correo para pruebas para equipos de desarrollo (realmente no está envíando los correos)

## i18n
