# Programació Web

??? abstract "Duració i criteris d'evaluació"

    Duració estimada: 12 hores

    <hr />

    | Resultat d'aprenentatge  | Criteris d'avaluació  |
    | ------                    | -----                |
    | 4. Desenvolupa aplicacions Web embegudes en llenguatges de marques analitzant i incorporant funcionalitats segons especificacions     |  a) S'han identificat els mecanismes disponibles per al manteniment de la informació que concerneix a un client Web concret i s'han assenyalat els seus avantatges.<br /> b) S'han utilitzat sessions per a mantenir l'estat de les aplicacions Web. <br /> c) S'han utilitzat *cookies* per a emmagatzemar informació en el client Web i per a recuperar el seu contingut.  <br /> d) S'han identificat i caracteritzat els mecanismes disponibles per a l'autenticació d'usuaris. <br /> e) S'han escrit aplicacions que integren mecanismes d'autenticació d'usuaris.  <br /> f) S'han realitzat adaptacions a aplicacions Web existents com a gestors de continguts o unes altres.  <br /> g) S'han utilitzat eines i entorns per a facilitar la programació, prova i depuració del codi. |

## Variables de servidor

PHP emmagatzema la informació del servidor i de les peticions HTTP en sis arrays globals:

* `$_ENV`: informació sobre les variables d'entorn
* `$_GET`: paràmetres enviats en la petició GET
* `$_POST`: paràmetres enviats en el envio POST
* `$_COOKIE`: conté les cookies de la petició, les claus del array són els noms de les cookies
* `$_SERVER`: informació sobre el servidor
* `$_FILES`: informació sobre els fitxers carregats via upload

Si ens centrem en el array `$_SERVER` podem consultar les següents propietats:

* `PHP_SELF`: nom del script executat, relatiu al document root (p.ej: `/tendisca/carret.php`)
* `SERVER_PROGRAMARI`: (p.ej: Apatxe)
* `SERVER_NAME`: domini, àlies DNS (p.ej: `www.elche.es`)
* `REQUEST_METHOD`: GET
* `REQUEST_URI`: URI, sense el domini
* `QUERY_STRING`: tot el que va després de `?` en la URL (p.ej: `heroe=Batman&nomene=Bruce`)

Més informació en <https://www.php.net/manual/es/reserved.variables.server.php>

``` php
<?php
echo $_SERVER["PHP_SELF"]."<br>"; // /u4/401server.php
echo $_SERVER["SERVER_SOFTWARE"]."<br>"; // Apache/2.4.46 (Win64) OpenSSL/1.1.1g PHP/7.4.9
echo $_SERVER["SERVER_NAME"]."<br>"; // localhost

echo $_SERVER["REQUEST_METHOD"]."<br>"; // GET
echo $_SERVER["REQUEST_URI"]."<br>"; // /u4/401server.php?heroe=Batman
echo $_SERVER["QUERY_STRING"]."<br>"; // heroe=Batman
```

Altres propietats relacionades:

* `PATH_INFO`: ruta extra després de la petició. Si la URL és `http://www.php.com/php/pathinfo.php/algo/cosa?foo=bar`, llavors `$_SERVER['PATH_INFO']` serà `/alguna cosa/cosa`.
* `REMALNOM_HOST`: hostname que va fer la petició
* `REMALNOM_ADDR`: IP del client
* `AUTH_TYPE`: tipus d'autenticació (p.ej: Basic)
* `REMALNOM_USER`: nom de l'usuari autenticat

Apatxe crea una clau per a cada capçalera HTTP, en majúscules i substituint els guions per subratllats:

* `HTTP_USER_AGENT`: agent (navegador)
* `HTTP_REFERER`: pàgina des de la qual es va fer la petició


``` php
<?php
echo $_SERVER["HTTP_USER_AGENT"]."<br>"; // Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36
```

## Formularis

A l'hora d'enviar un formulari, hem de tindre clar quan usar GET o POST

* GET: els paràmetres es passen en la URL
    * <2048 caràcters, només ASCII
    * Permet emmagatzemar la direcció completa (marcador / historial)
    * Idempotent: dues crides amb les mateixes dades sempre ha de donar el mateix resultat
    * El navegador pot cachejar les cridades

* POST: paràmetres ocults (no encriptats)
    * Sense límit de dades, permet dades binàries.
    * No es poden escorcollar
    * No idempotent → actualitzar la BBDD

Així doncs, per a recollir les dades accedirem al array depenent del mètode del formulari que ens ha invocat:

``` php
<?php
$par = $_GET["parametro"]
$par = $_POST["parametro"]
```

A l'hora d'enviar un formulari, hem de tindre clar quan usar GET o POST. Per als següents apartats ens basarem en el següent exemple:

``` html
<form action="formulario.php" method="GET">
    <p><label for="nombre">Nombre del alumno:</label>
        <input type="text" name="nombre" id="nombre" value="" />
    </p>

    <p><input type="checkbox" name="modulos[]" id="modulosDWES" value="DWES" />
        <label for="modulosDWES">Desarrollo web en entorno servidor</label>
    </p>

    <p><input type="checkbox" name="modulos[]" id="modulosDWEC" value="DWEC" />
        <label for="modulosDWEC">Desarrollo web en entorno cliente</label>
    </p>

    <input type="submit" value="Enviar" name="enviar" />
</form>
```

### Validació

Respecte a la validació, és convenient sempre fer *validació doble*:

* En el client mitjançant JS
* En servidor, abans de cridar a negoci, és convenient tornar a validar les dades.

``` php
<?php
if (isset($_GET["parametro"])) {
    $par = $_GET["parametro"];
    // comprobar si $par tiene el formato adecuado, su valor, etc...
}
```

!!! info "Llibreries de validació"
    Existeixen diverses llibreries que faciliten la validació dels formularis, com són [respect/validation](https://respect-validation.readthedocs.io/en/latest/) o [particle/validator](http://validator.particle-php.com/en/latest/).
    Quan estudiem Laravel aprofundirem en la validació de manera declarativa.

### Parámetre multivalor

Existeixen elements HTML que envien diversos valors:

* `select multiple`
* `checkbox`

Per a recollir les dades, el nom de l'element ha de ser un array.

``` html
<select name="lenguajes[]" multiple="true">
    <option value="c">C</option>
    <option value="java">Java</option>
    <option value="php">PHP</option>
    <option value="python">Python</option>
</select>

<input type="checkbox" name="lenguajes[]" value="c" /> C<br />
<input type="checkbox" name="lenguajes[]" value="java" /> Java<br />
<input type="checkbox" name="lenguajes[]" value="php" /> Php<br />
<input type="checkbox" name="lenguajes[]" value="python" /> Python<br />
```

De manera que després en recollir les dades:

``` php
<?php
$lenguajes = $_GET["lenguajes"];

foreach ($lenguajes as $lenguaje) {
    echo "$lenguaje <br />";
}
```

### Tornant a emplenar un formulari

Un *sticky form* és un formulari que recorda els seus valors. Per a això, hem d'emplenar els atributs `value` dels elements HTML amb la informació que contenien:

``` html+php
<?php
if (!empty($_POST['modulos']) && !empty($_POST['nombre'])) {
  // Aquí se incluye el código a ejecutar cuando los datos son correctos
} else {
  // Generamos el formulario
  $nombre = $_POST['nombre'] ?? "";
  $modulos = $_POST['modulos'] ?? [];
  ?>
  <form action="<?php echo $_SERVER['PHP_SELF'];?>" method="POST">
   <p><label for="nombre">Nombre del alumno:</label>
    <input type="text" name="nombre" id="nombre" value="<?= $nombre ?>" /> 
   </p>
   <p><input type="checkbox" name="modulos[]" id="modulosDWES" value="DWES"
    <?php if(in_array("DWES",$modulos)) echo 'checked="checked"'; ?> />
    <label for="modulosDWES">Desarrollo web en entorno servidor</label>
   </p>
   <p><input type="checkbox" name="modulos[]" id="modulosDWEC" value="DWEC"
    <?php if(in_array("DWEC",$modulos)) echo 'checked="checked"'; ?> />
    <label for="modulosDWEC">Desarrollo web en entorno cliente</label>
   </p>
   <input type="submit" value="Enviar" name="enviar"/>
  </form>
<?php } ?>
```

### Pujant arxius

S'emmagatzemen en el servidor en el array `$_FILES` amb el nom del camp del tipus `file` del formulari.

``` html
<form enctype="multipart/form-data" action="<?php echo $_SERVER['PHP_SELF']; ?>"  method="POST">
    Archivo: <input name="archivoEnviado" type="file" />
    <br />
    <input type="submit" name="btnSubir" value="Subir" />
</form>
```

Configuració en `php.ini`

* `file_uploads`: on / off
* `upload_max_filesize`: 2M
* `upload_tmp_dir`: directori temporal. No és necessari configurar-ho, agafarà el predeterminat del sistema
* `post_max_size`: grandària màxima de les dades POST. Ha de ser major a upload_max_filesize.
* `max_file_uploads`: nombre màxim d'arxius que es poden carregar alhora.
* `max_input_estafe`: temps màxim emprat en la càrrega (GET/POST i upload → normalment es configura en 60)
* `memory_limit`: 128M
* `max_execution_estafe`: temps d'execució d'un script (no té en compte el upload)

Per a carregar els arxius, accedim al array `$_FILES`:

``` php
<?php
if (isset($_POST['btnSubir']) && $_POST['btnSubir'] == 'Subir') {
    if (is_uploaded_file($_FILES['archivoEnviado']['tmp_name'])) {
        // subido con éxito
        $nombre = $_FILES['archivoEnviado']['name'];
        move_uploaded_file($_FILES['archivoEnviado']['tmp_name'], "./uploads/{$nombre}");

        echo "<p>Archivo $nombre subido con éxito</p>";
    }
}
```

Cada arxiu carregat en `$_FILES` té:

* `name`: nom
* `tmp_name`: ruta temporal
* `size`: grandària en bytes
* `type`: tipus ACARONE
* `error`: si hi ha error, conté un missatge. Si ok → 0.

## Capçaleres de resposta

Ha de ser el primer a retornar. Es retornen mitjançant la funció `header(cadena)`. Mitjançant les capçaleres podem configurar el tipus de contingut, temps d'expiració, redirigir el navegador, especificar errors HTTP, etc.

``` php
<?php header("Content-Type: text/plain"); ?>
<?php header("Location: http://www.ejemplo.com/inicio.html");
exit(); 
```

Es pot comprovar en les eines del desenvolupador dels navegadors web mitjançant *Developer Tools --> Network --> Headers*.

És molt comú configurar les capçaleres per a evitar consultes a la caixet o provocar la seua renovació:

``` php
<?php
header("Expires: Sun, 31 Jan 2021 23:59:59 GMT");
// tres horas
$now = time();
$horas3 = gmstrftime("%a, %d %b %Y %H:%M:%S GMT", $now + 60 * 60 * 3);
header("Expires: {$horas3}");
// un año
$now = time();
$anyo1 = gmstrftime("%a, %d %b %Y %H:%M:%S GMT", $now + 365 * 86440);
header("Expires: {$anyo1}");
// se marca como expirado (fecha en el pasado)
$pasado = gmstrftime("%a, %d %b %Y %H:%M:%S GMT");
header("Expires: {$pasado}");
// evitamos cache de navegador y/o proxy
header("Expires: Mon, 26 Jul 1997 05:00:00 GMT");
header("Last-Modified: " . gmdate("D, d M Y H:i:s") . " GMT");
header("Cache-Control: no-store, no-cache, must-revalidate");
header("Cache-Control: post-check=0, pre-check=0", false);
header("Pragma: no-cache");
```

## Gestió de l'estat

HTTP és un protocol *stateless*, sense estat. Per això, se simula l'estat mitjançant l'ús de cookies, tokens o la sessió. L'estat és necessari per a processos com ara el carret de la compra, operacions associades a un usuari, etc...
El mecanisme de PHP per a gestionar la sessió empra cookies de manera interna.
Les cookies s'emmagatzemen en el navegador, i la sessió en el servidor web.

### Cookies

Les cookies s'emmagatzemen en el array global `$_COOKIE`. El que col·loquem dins del array, es guardarà en el client. Cal tindre present que el client pot no voler emmagatzemar-les.

Existeix una limitació de 20 cookies per domini i 300 en total en el navegador.

En PHP, per a crear una cookie s'utilitza la funció `setcookie`:

``` php
<?php
setcookie(nombre [, valor [, expira [, ruta [, dominio [, seguro [, httponly ]]]]]]);
setcookie(nombre [, valor = "" [, opciones = [] ]] )
?>
```
Destacar que el nom no pot contindre espais ni el caràcter `;`. Respecte al contingut de la cookie, no pot superar els 4 KB.

Per exemple, mitjançant *cookies* podem comprovar la quantitat de visites diferents que realitza un usuari:

``` php
<?php
$accesosPagina = 0;
if (isset($_COOKIE['accesos'])) { 
    $accesosPagina = $_COOKIE['accesos']; // recuperamos una cookie
    setcookie('accesos', ++$accesosPagina); // le asignamos un valor
}
?>
```

!!! tip "Inspeccionant les cookies"
    Si volem veure que contenen les cookies que tenim emmagatzemades en el navegador, es pot comprovar el seu valor en *Dev Tools --> Application --> Storage

El temps de vida de les cookies pot ser tan llarg com el lloc web en el qual resideixen. Elles seguiran ací, fins i tot si el navegador està tancat o obert.

Per a esborrar una cookie es pot posar que expiren en el passat:

``` php
<?php
setcookie(nombre, "", 1) // pasado
```

O que caduquen dins d'un període de temps deteminado:

``` php
<?php
setcookie(nombre, valor, time() + 3600) // Caducan dentro de una hora
```

<figure style="align: center;">
    <img src="imagenes/04/04cookies.png" width="700">
    <figcaption>Comunicació amb cookies</figcaption>
</figure>

S'utilitzen per a:

* Recordar els inicis de sessió
* Emmagatzemar valors temporals d'usuari
* Si un usuari està navegant per una llista paginada d'articles, ordenats d'una certa manera, podem emmagatzemar l'ajust de la classificació.

L'alternativa en el client per a emmagatzemar informació en el navegador és l'objecte [LocalStorage](https://developer.mozilla.org/es/docs/web/api/window/localstorage).

### Sessió

La sessió afig la gestió de l'estat a HTTP, emmagatzemant en aquest cas la informació en el servidor.
Cada visitant té un ANEU de sessió únic, el qual per defecte s'emmagatzema en una cookie denominada `PHPSESSID`.
Si el client no té les cookies actives, l'ANEU es propaga en cada URL dins del mateix domini.
Cada sessió té associat un magatzem de dades mitjançant el array global `$_SESSION`, en el qual podem emmagatzemar i recuperar informació.

La sessió comença en executar un script PHP. Es genera un nou ANEU i es carreguen les dades del magatzem:

<figure style="align: center;">
    <img src="imagenes/04/04sesion.png" width="700">
    <figcaption>Comunicació amb sessions</figcaption>
</figure>

Les operacions que podem realitzar amb la sessió són:

``` php
<?php
session_start(); // carga la sesión
session_id() // devuelve el id
$_SESSION[clave] = valor; // inserción
session_destroy(); // destruye la sesión
unset($_SESSION[clave]; // borrado
```

Veurem mitjançant un exemple com podem inserir en un pàgina dades en la sessió per a posteriorment en una altra pàgina accedir a aqueixes dades. Per exemple, en `sesion1.php` tindríem

``` php
<?php
session_start(); // inicializamos
$_SESSION["ies"] = "IES Severo Ochoa"; // asignación
$instituto = $_SESSION["ies"]; // recuperación
echo "Estamos en el $instituto ";
?>
<br />
<a href="sesion2.php">Y luego</a>
```

I posteriorment podem accedir a la sessió en `sesion2.php`:

``` php
<?php
session_start();
$instituto = $_SESSION["ies"]; // recuperación
echo "Otra vez, en el $instituto ";
?>
```

!!! note "Configurant la sessió en `php.ini`"
    Les següent propietats de `php.ini` permeten configurar alguns aspectes de la sessió:

      * `session.save_handler`: controlador que gestiona com s'emmagatzema (`files`)
      * `session.save_path`: ruta on s'emmagatzemen els arxius amb les dades (si tenim un clúster, podríem usar `/mnt/sessions` en tots els servidor de manera que apunten a una carpeta compartida)
      * `session.name`: nom de la sessió (`PHSESSID`)
      * `session.acte_start`: Es pot fer que s'autocarregue amb cada script. Per defecte està deshabilitat
      * `session.cookie_lifetime`: temps de vida per defecte

Més informació en la [documentació oficial](https://www.php.net/manual/es/session.configuration.php).

## Autenticació d'usuaris

Una sessió estableix una relació anònima amb un usuari particular, de manera que podem saber si és el mateix usuari entre dues peticions diferents. Si preparem un sistema de login, podrem saber qui utilitza la nostra aplicació.

Per a això, preparem un senzill sistema d'autenticació:

* Mostrar el formulari login/password
* Comprovar les dades enviades
* Afegir el login a la sessió
* Comprovar el login en la sessió per a fer tasques específiques de l'usuari
* Eliminar el login de la sessió quan l'usuari la tanca.

Veurem en codi cada pas del procés. Comencem amb l'arxiu `index.php`:
``` html
<form action='login.php' method='post'>
  <fieldset>
    <legend>Login</legend>
    <div><span class='error'><?php echo $error; ?></span></div>
    <div class='fila'>
        <label for='usuario'>Usuario:</label><br />
        <input type='text' name='inputUsuario' id='usuario' maxlength="50" /><br />
    </div>
    <div class='fila'>
        <label for='password'>Contraseña:</label><br />
        <input type='password' name='inputPassword' id='password' maxlength="50" /><br />
    </div>
    <div class='fila'>
        <input type='submit' name='enviar' value='Enviar' />
    </div>
  </fieldset>
  </form>
```

En fer *submit* ens porta a `login.php`, el qual fa de controlador:

``` php
<?php
// Comprobamos si ya se ha enviado el formulario
if (isset($_POST['enviar'])) {
    $usuario = $_POST['inputUsuario'];
    $password = $_POST['inputPassword'];

    // validamos que recibimos ambos parámetros
    if (empty($usuario) || empty($password)) {
        $error = "Debes introducir un usuario y contraseña";
        include "index.php";
    } else {
        if ($usuario == "admin" && $password == "admin") {
            // almacenamos el usuario en la sesión
            session_start();
            $_SESSION['usuario'] = $usuario;
            // cargamos la página principal
            include "main.php";
        } else {
            // Si las credenciales no son válidas, se vuelven a pedir
            $error = "Usuario o contraseña no válidos!";
            include "index.php";
        }
    }
}
```
Depenent de l'usuari que s'hi haja logueado, anirem a una vista o a una altra. Per exemple, en `main.php` tindríem:

``` html+php
<?php
    // Recuperamos la información de la sesión
    if(!isset($_SESSION)) {
        session_start();
    }
    
    // Y comprobamos que el usuario se haya autentificado
    if (!isset($_SESSION['usuario'])) {
       die("Error - debe <a href='index.php'>identificarse</a>.<br />");
    }
?>
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Listado de productos</title>
</head>
<body>
    <h1>Bienvenido <?= $_SESSION['usuario'] ?></h1>
    <p>Pulse <a href="logout.php">aquí</a> para salir</p>
    <p>Volver al <a href="main.php">inicio</a></p>
    <h2>Listado de productos</h2>
    <ul>
        <li>Producto 1</li>
        <li>Producto 2</li>
        <li>Producto 3</li>
    </ul>
</body>
</html>
```

Finalment, necessitem l'opció de tancar la sessió que col·loquem en `logout.php`:

``` php
<?php
// Recuperamos la información de la sesión
session_start();

// Y la destruimos
session_destroy();
header("Location: index.php");
?>
```

!!! warning "Autenticació en producció"
    En l'actualitat l'autenticació d'usuari no es realitza gestionant la sessió direcamente, sinó que es realitza mitjançant algun framekwork que abstrau tot el procés o la integració de mecanismes d'autenticació tipus *OAuth*, com estudiarem en l'última unitat mitjançant *Laravel*.

## Referències

* [Cookies en PHP](https://www.php.net/manual/es/features.cookies.php)
* [Manejo de sesiones en PHP](https://www.php.net/manual/es/book.session.php)

## Activitats

401. `401server.php`: igual que l'exemple vist en les anotacions, mostra els valors de `$_SERVER` en executar un script en el teu ordinador.
     Prova a passar-li paràmetres per GET (i a no passar-li cap).
     Prepara un formulari (`401post.html`) que faça un enviament per POST i comprova'l de nou.
     Crea una pàgina (`401enlace.html`) que tinga un enllaç a `401server.php` i comprova el valor de `HTTP_REFERER`.

### Formularis

402. `402formulario.html` i `402formulario.php`: Crea un formulari que sol·licite:

       * Nom i cognoms.
       * Email.
       * URL pàgina personal.
       * Sexe (ràdio).
       * Nombre de convivents en el domicili.
       * Aficions (caselles de selecció) – posar mínim 4 valors.
       * Menú favorit (llesta selecció múltiple) – posar mínim 4 valors.

    Mostra els valors carregats en una taula-resumeixen.

403. `403validacion.php`: A partir del formulari anterior, introdueix validacions en HTML mitjançant l'atribut `required` dels camps (ús els tipus adequats per a cada camp), i en comprova els tipus de les dades i que compleixen els valors esperats (per exemple, en les caselles de selecció que els valors recollits formen part de tots els possibles). Pots provar de passar-li dades erroneos via URL i comprovar el seu comportament.
     Tip: Investiga l'ús de la funció `filter_var`.

404. `404subida.html` i `404subida.php`: Crea un formulari que permeta pujar un arxiu al servidor.
     A més del fitxer, ha de demanar en el mateix formulari dos camps numèrics que sol·liciten l'amplària i l'altura. Comprova que tant el fitxer com les dades arriben correctament.

405. `405subidaImagen.php`: Modifica l'exercici anterior perquè únicament permeta pujar imatges (comprova la propietat `type` de l'arxiu pujat). Si l'usuari selecciona un altre tipus d'arxius, se l'ha d'informar de l'error i permetre que puge un nou arxiu.
     En el cas de pujar el tipus correcte, visualitzar la imatge amb la grandària d'amplària i altura rebut com a paràmetre.

### Cookies i Sessió

406. `406contadorVisitas.php`: Mitjançant l'ús de cookies, informa l'usuari de si és la seua primera visita, o si no ho és, mostre el seu valor (valor d'un comptador).
     A més, has de permetre que l'usuari reinicialitze el seu comptador de visites.

407. `407fondo.php`: Mitjançant l'ús de cookies, crea una pàgina amb un desplegable amb diversos colors, de manera que l'usuari puga canviar el color de fons de la pàgina (atribut `bgcolor`).
     En tancar la pàgina, aquesta ha de recordar, almenys durant 24h, el color triat i la pròxima vegada que es carregue la pàgina, ho faça amb l'últim color seleccionat.

408. `408fondoSesion1.php`: Modifica l'exercici anterior per a emmagatzemar el color de fons en la sessió i no emprar cookies. A més, ha de contindre un enllaç al següent arxiu.
     `408fondoSesion2.php`: Ha de mostrar el color i donar la possibilitat de:
* tornar a la pàgina anterior mitjançant un enllaç i mitjançant un altre enllaç, buidar la sessió i tornar a la pàgina anterior.

409. Fent ús de la sessió, dividirem el formulari de l'exercici `402formulario.php` en 2 subformularios:

     * `409formulario1.php` envia les dades (nom i cognoms, email, url i sexe) a `409formulario2.php`.
     * `409formulario2.php` llig les dades i els fica en la sessió. A continuació, mostra la resta de camps del formulari a emplenar (convivents, aficions i menú). Envia aquestes dades a `409formulario3.php`.
     * `409formulario3.php` recull les dades enviades en el pas anterior i al costat dels quals ja estaven en la sessió, es mostren totes les dades en una taula/llista desordenada.
     
### Autenticació

En els següents exercicis muntarem una estructura d'inici de sessió similar a la vista en les anotacions.

410. `410index.php`: formulari d'inici de sessió
411. `411login.php`: fa de controlador, per la qual cosa ha de comprovar les dades rebudes (només permet l'entrada de `usuari/usuari` i si tot és correcte, cedir el control a la vista del següent exercici. No conté codi HTML.
412. `412peliculas.php`: vista que mostra com a títol "Llistat de Pel·lícules", i una llista desordenada amb tres pel·lícules.
413. `413logout.php`: buida la sessió i ens porta de nou al formulari d'inici de sessió. No conté codi HTML
414. `414series.php`: Afig un nova vista similar a `412peliculas.php` que mostra un "Llistat de Sèries" amb una llista desordenada amb tres sèries. Tant `412pelicuas.php` com la vista recien creades, han de tindre un xicotet menú (senzill, mitjançant enllaços) que permeta passar d'un llistat a un altre.
     Comprova que si s'accedeix directament a qualsevol de les vistes sense tindre un usuari *loguejao* via URL del navegador, no es mostra el llistat.
415. Modifica tant el controlador com les vistes perquè:
     * les dades els obtinga el controlador (emmagatzema en la sessió un array de pel·lícules i un altre de sèries)
     * col·loque les dades en la sessió
     * En les vistes, les dades es recuperen de la sessió i es pinten* en la llista desordenada recorrent el array corresponent.

### Projecte Videoclub 3.0

420. Per al Videoclub, crearem una pàgina `index.php` amb un formulari que continga un formulari de login/password.
     Es comprovaran les dades en `login.php`. Els possibles usuaris són admin/admin o usuari/usuari
* Si l'usuari és correcte, en `main.php` mostrar un missatge de benvinguda amb el nom de l'usuari, al costat d'un enllaç per a tancar la sessió, que el portaria de nou al login.
* Si l'usuari és incorrecte, ha de tornar a carregar el formulari donant informació a l'usuari d'accés incorrecte.

421. Si l'usuari és administrador, es carregaran en la sessió les dades de suports i clients del videoclub que teníem en les nostres proves.
     En la següent unitat els obtindrem de la base de dades.
     En `mainAdmin.php`, a més de la benvinguda, ha de mostrar:
     * Llistat de clients
     * Llistat de suports

<figure style="float: right;">
 <img src="imagenes/04/04p423.png" width="400">
 <figcaption>Esquema navegació exercici 423</figcaption>
</figure>

422. Modificarem la classe `Client` per a emmagatzemar el `user` i la `password` de cada client.
     Després de codificar els canvis, modificar el llistat de clients de `mainAdmin.php` per a afegir al llistat l'usuari.

423. Si l'usuari que accedeix no és administrador i coincideix amb algun dels clients que tenim carregats després del login, ha de carregar `mainCliente.php` on es mostrarà un llistat dels lloguers del client. Per a això, modificarem la classe `Client` per a oferir el mètode `getAlquileres() : array`, el qual anomenarem i després recorrerem per a mostrar el llistat sol·licitat.

Ara tornem a la part d'administració

424. A més de mostrar el llistat de clients, oferirem l'opció de donar d'alta a un nou client en `formCreateCliente.php`.
     Les dades s'enviaran mitjançant POST a `createCliente.php` que els introduirà en la sessió.
     Una vegada creat el client, ha de tornar a carregar `mainAdmin.php` on es podrà veure el client inserit.
     Si hi ha alguna dada incorrecta, ha de tornar a carregar el formulari d'alta.

425. Crea en `formUpdateCliente.php` un formulari que permeta editar les dades d'un client.
     Has de recollir les dades en `updateCliente.php`
     Les dades de client s'han de poder modificar des de la pròpia pàgina d'un client, així com des del llistat de l'administrador.

426. Des del llistat de clients de l'administrador has d'oferir la possibilitat d'esborrar un client.
     En el navegador, abans de redirigir al servidor, l'usuari ha de confirmar mitjançant JS que realment desitja eliminar al client.
     Finalment, en `removeCliente.php` elimina al client de la sessió.
     Una vegada eliminat, ha de tornar al llistat de clients.

<figure style="align: center;">
    <img src="imagenes/04/04p426.png" width="600">
    <figcaption>Esquema navegación Videoclub 3.0</figcaption>
</figure>
