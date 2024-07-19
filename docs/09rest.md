<style>
    img { margin: 20px 0; border-radius: 8px; }

    .alert { color: #BD1550; }
    .warning { color: #E97F02; }
    .success { color: #8A9B0F; }

    .center { text-align: center; }
    .right { text-align: right; }

    .img-small { max-width: 200px; margin: auto; }
    .img-medium { max-width: 400px; margin: auto; }
    .img-large { max-width: 800px; margin: auto; }

    .leyenda {
        font-size: small;
        margin: 10px 0;
    }
</style>

# Servicios REST

> Duración estimada: 32 sesiones

## API

Una API (Application Programming Interface) es un conjunto de funciones y procedimientos por los cuales, una aplicación externa accede a los datos, a modo de biblioteca como una capa de abstracción y la API se encarga de enviar el dato solicitado.

Una de las características fundamentales de las API es que son ***Sateless***, lo que quiere decir que las peticiones se hacen y desaparecen, no hay usuarios logueados ni datos que se quedan almacenados.

Ejemplos de APIs gratuitas:

- [ChuckNorris IO](https://api.chucknorris.io/#!)
- [OMDB](https://www.omdbapi.com/)
- [PokeAPI - Pokemon](https://pokeapi.co/)
- [RAWg - Videojuegos](https://rawg.io/)
- [The Star Wars API](https://swapi.dev/)

Para hacer pruebas con estas APIs podemos implementar el código para consumirlas o utilizar un cliente especial para el consumo de estos servicios.

- [PostMan](https://www.postman.com/)
- [Thunder Client](https://marketplace.visualstudio.com/items?itemName=rangav.vscode-thunder-client)
- [Insomnia](https://insomnia.rest/download)
- [Advance REST Client (desde el navegador)](https://chrome.google.com/webstore/detail/advanced-rest-client/hgmloofddffdnphfgcellkdfbfbjeloo?hl=es)

## REST

Con esta metodología llamada REST vamos a poder construir APIs para que desde un cliente externo se puedan consumir.

Gracias a este *standard* de la arquitectura del software vamos a poder montar un API que utilice los métodos standard GET, POST, PUT y DELETE.

### Creando Recurso (Resource)

Para crear un recurso dentro de nuestra aplicación hecha con Laravel, necesitamos crear un controlador del tipo ***resource*** donde establezcamos los métodos que nosotros queramos realizar a la hora de trabajar con los datos

```console
php artisan make:controller ChollosController --resource
```

Artisan nos creará un nuevo controlador en la carpeta `controllers` con el nombre `ChollosController` o el nombre que le hayamos pasado.

La estructura de este archivo es un poco diferente a los controladores que ya hemos visto anteriormente. Ahora tenemos los siguientes métodos creados de manera automática:

- `index()` normalmente para listar, en nuestro caso los chollos
- `create()` para crear plantillas (no lo vamos a usar)
- `store()` para guardar los datos que pasemos a la API
- `update()` para actualizar un dato ya existente en la BDD
- `delete()` para eliminar un dato ya existente en la BDD

En el caso de devolver un listado con todos los chollos, lo primero que debemos hacer es importar nuestro modelo Chollo.

```php
<?php

use App\Models\Chollo;
```

Y como hemos ido haciendo en controladores anteriores, necesitamos hacer la consulta apropiada para devolver todos los chollos. <span class="alert">**CUIDADO CON EL RETURN**</span> porque ahora no estamos devolviendo una vista sino un array de datos en formato JSON.

```php
<?php

public function index()
{
    $chollos = Chollo::all();
    return $chollos;
}
```

El último paso sería configurar el archivo de rutas, pero en este caso <span class="warning">**el archivo de rutas de la api se llama `api.php`**</span>

```php
<?php
// estamos en ▓▓▓ api.php 

use App\Http\Controllers\ChollosController;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;

Route::get('/chollos', [ ChollosController::class, 'index' ]);
```

Una vez hecho ésto, debemos poner en marcha nuestro servidor.

```console
php artisan serve
```

Ahora ya podemos usar el Postman o cualquier cliente de la misma índole para testear nuestra API a través de la URL de nuestro servidor `http://127.0.0.1:8000/api/chollos`

### Recuperando datos en vista ***BLADE***

Ya tenemos nuestra API montada y puede ser consumida a través del navegador o cualquier cliente como `PostMan` o `Thunder Client` pero lo que nos interesa ahora es **poder listar la información en una vista** como hacíamos en el tema anterior.

Lo que vamos a tener que hacer es lo siguiente

- Modificar el `Controlador` para que consuma de la API
- Convertir el tipo de dato que nos devuelve la API como respuesta
- Utilizar una estructura de control `forEach` dentro de nuestra vista
- Acceder a cada clave del objeto JSON recibido en la vista `blade`


Vamos a modificar el controlador para que consuma de la API y convertimos los datos con el método `collect()`.

Para la llamada debemos importar `Http`.

```php
<?php

// estamos en ▓▓▓ CholloController.php

use App\Models\Chollo; // EN CASO DE API LOCAL
use Illuminate\Support\Facades\Http; // INDISPENSABLE EN CASO DE API EXTERNA

class RestController extends Controller
{
    public function index() {
        // $restChollos = Http::get('http://localhost/api/chollos') -> collect(); // PARA API EXTERNA

        $chollos = Chollo::all();

        return view('rest', compact('chollos'));
    }
}
```

Modificamos nuestra vista `blade` para poder listar los datos correctamente o si lo prefieres, puedes crear una ruta nueva e incluso un nuevo controlador que se encargue de manejar las llamadas a la API.

El nombre que va entre corchetes es el nombre de las columnas de la tabla de la base de datos

```php
<?php
// estamos en ▓▓▓ chollos.blade.php

@foreach ($chollos as $chollo)
    <p>ID:  {{ $chollo -> id }}</p> // Columna ID
    <p>Nombre:  {{ $chollo -> nombre }}</p>  // Columna NOMBRE
    <p>Descripción:  {{ $chollo -> descripcion }}</p>  // Columna DESCRIPCION

    // [...]
    <hr>
@endforeach
```

### Enviando datos (POST)

En una API, para poder enviar datos y que se guarden en la base de datos, debemos usar el método post bajo `Http` y pasarle los nuevos datos como un array asociativo, poniendo como `clave` el nombre de las columnas de la base de datos

```php
<?php

$response = Http::post('http://localhost:8000/api/chollo-severo', [
    'titulo' => 'Chollazo para este producto',
    'descripcion' => 'Lorem ipsum dolo...',

// [...]
]);
```

## Actividades

901. <span class="success">**Chollo Severo V 2.0**</span> Crea un controlador nuevo que se llame `RestController` (o como quieras llamarlo) y escribe todos los métodos que necesites para convertir tu `app` que, consume de una Base de Datos, a una `Restful App` que reciba los datos a través de la API de tu proyecto de Chollos.

<!-- 
## Producción y consumo

## AJAX con JSON

## Interacción con Vue.js

<https://manuais.iessanclemente.net/index.php/LARAVEL_Framework_-_Tutorial_01_-_Creación_de_API_RESTful_(actualizado)>
<https://github.com/jmnavarro/http-api-design>
<https://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api>
<https://leanpub.com/build-apis-you-wont-hate> -->