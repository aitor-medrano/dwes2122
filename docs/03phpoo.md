# PHP Orientado a Objetos

??? abstract "Duración y criterios de evaluación"

    Duración estimada: 20 sesiones

    <hr />

    Resultado de aprendizaje:

    5. Desarrolla aplicaciones Web identificando y aplicando mecanismos para separar el código de presentación de la lógica de negocio.

    Criterios de evaluación:

    1. Se han identificado las ventajas de separar la lógica de negocio de los aspectos de presentación de la aplicación. 
    2. Se han analizado tecnologías y mecanismos que permiten realizar esta separación y sus características principales. 
    3. Se han utilizado objetos y controles en el servidor para generar el aspecto visual de la aplicación web en el cliente. 
    4. Se han utilizado formularios generados de forma dinámica para responder a los eventos de la aplicación Web. 
    6. Se han escrito aplicaciones Web con mantenimiento de estado y separación de la lógica de negocio. 
    7. Se han aplicado los principios de la programación orientada a objetos. 
    8. Se ha probado y documentado el código.

## Clases y Objetos

PHP sigue un paradigma de programación orientada a objetos (POO) basada en clases.

Un clase es un plantilla que define las propiedades y métodos para poder crear objetos. De este manera, un objeto es una instancia de una clase.

Tanto las propiedades como los métodos se definen con una visibilidad (quien puede acceder)

* Privado - `private`:  Sólo puede acceder la propia clase
* Protegido - `protected`: Sólo puede acceder la propia clase o sus descendientes
* Público - `public`: Puede acceder cualquier otra clase

Para declarar una clase, se utiliza la palabra clave `class` seguido del nombre de la clase. Para instanciar un objeto a partir de la clase, se utiliza `new`:

``` php
<?php
class NombreClase {
// propiedades
// y métodos
}

$ob = new NombreClase();
```

!!! important "Clases con mayúscula"
 Todas las clases empiezan por letra mayúscula.

Cuando un proyecto crece, es normal modelar las clases mediante UML (¿recordáis *Entornos de Desarrollo*?). La clases se representan mediante un cuadrado, separando el nombre, de las propiedades y los métodos:

![UML](imagenes/03/uml.png){ width=500 }

Una vez que hemos creado un objeto, se utiliza el operador `->` para acceder a una propiedad o un método:

``` php
$objeto->propiedad;
$objeto->método(parámetros);
```

Si desde dentro de la clase, queremos acceder a una propiedad o método de la misma clase, utilizaremos la referencia `$this`;

``` php
$this->propiedad;
$this->método(parámetros);  
```

Así pues, como ejemplo, codificaríamos una persona en el fichero `Persona.php` como:

``` php
<?php
class Persona {
    private $nombre;

    public function setNombre($nom) {
        $this->nombre=$nom;
    }

    public function imprimir(){
        echo $this->nombre;
        echo '<br>';
    }
}

$bruno = new Persona(); // creamos un objeto
$bruno->setNombre("Bruno Díaz");
$bruno->imprimir();
```

## Encapsulación

Las propiedades se definen privadas o protegidas (si queremos que las clases heredadas puedan acceder).

Para cada propiedad, se añaden métodos públicos (*getter/setter*):

``` php
public setPropiedad($param)
public getPropiedad()
```

Las constantes se definen públicas para que sean accesibles por todos los recursos.

``` php
<?php
class MayorMenor {
    private $mayor;
    private $menor;

    public function setMayor(int $may) {
        $this->mayor = $may;
    }

    public function setMenor(int $men) {
        $this->menor = $men;
    }

    public function getMayor() : int {
        return $this->mayor;
    }

    public function getMenor() : int {
        return $this->menor;
    }
}
```

### Recibiendo y enviando objetos

Es recomendable indicarlo en el tipo de parámetros. Si el objeto puede devolver nulos se pone `?` delante del nombre de la clase.

!!! important "Objetos por referencia"
 Los objetos que se envían y reciben como parámetros siempre se pasan por referencia.

``` php hl_lines="2"
<?php
function maymen(array $numeros) : ?MayorMenor {
    $a = max($numeros);
    $b = min($numeros);

    $result = new MayorMenor();
    $result->setMayor($a);
    $result->setMenor($b);

    return $result;
}

$resultado =  maymen([1,76,9,388,41,39,25,97,22]);
echo "<br>Mayor: ".$resultado->getMayor();
echo "<br>Menor: ".$resultado->getMenor();
```

## Constructor

El constructor de los objetos se define mediante el método mágico `__construct`.
Puede o no tener parámetros, pero sólo puede haber un único constructor.

``` php hl_lines="5"
<?php
class Persona {
    private $nombre;

    public function __construct($nom) {
      $this->nombre=$nom;
    }

    public function imprimir(){
      echo $this->nombre;
      echo '<br>';
    }
}

$bruno = new Persona("Bruno Díaz");
$bruno->imprimir();
```

## Clases estáticas

Son aquellas que tienes propiedades y/o métodos estáticos (también se conocen como *de clase*).
Se declaran con `static` y se referencian con `::`. Si desde un método estático queremos acceder a una propiedad estática de la misma clase, se utiliza la referencia `self`.

``` php
<?php
class Producto {
    const IVA = 0.23;
    private static $numProductos = 0; 

    public static function nuevoProducto() {
        self::$numProductos++;
    }
}

Producto::nuevoProducto();
$impuesto = Producto::IVA;
```

También podemos tener clases normales que tengan alguna propiedad estática:

``` php
<?php
class Producto {
    const IVA = 0.23;
    private static $numProductos = 0; 
    private $codigo;

    public function __construct(string $cod) {
        self::$numProductos++;
        $this->codigo = $cod;
    }

    public function mostrarResumen() : string {
        return "El producto ".$this->codigo." es el número ".self::$numProductos;
    }
}

$prod1 = new Producto("PS5");
$prod2 = new Producto("XBOX Series X");
$prod3 = new Producto("Nintendo Switch");
echo $prod3->mostrarResumen();
```

## Funciones predefinidas

Al trabajar con clases y objetos, existen un conjunto de funciones ya definidas por el lenguaje que conviene conocer:

* `instanceof`: permite comprobar si un objeto es de una determinada clase
* `get_class`: devuelve el nombre de la clase
* `get_declared_class`: devuelve un array con los nombres de las clases definidas
* `class_alias`: crea un alias
* `class_exists` / `method_exists` / `property_exists`: `true` si la clase / método / propiedad está definida
* `get_class_methods` / `get_class_vars` / `get_object_vars`: Devuelve un array con los nombres de los métodos / propiedades de una clase / propiedades de un objeto que son accesibles desde dónde se hace la llamada.

Un ejemplo de estas funciones puede ser el siguiente:

``` php
<?php
$p = new Producto("PS5");
if ($p instanceof Producto) {
    echo "Es un producto";
    echo "La clase es ".get_class($p);

    class_alias("Producto", "Articulo");
    $c = new Articulo("Nintendo Switch");
    echo "Un articulo es un ".get_class($c);

    print_r(get_class_methods("Producto"));
    print_r(get_class_vars("Producto"));
    print_r(get_object_vars($p));

    if (method_exists($p, "mostrarResumen")) {
        $p->mostrarResumen();
    }
}
```

!!! caution "Clonado"
    Al asignar dos objetos no se copian, se crea una nueva referencia. Si queremos una copia, hay que clonarlo mediante el método `clone(object) : object`

    Si queremos modificar el clonado por defecto, hay que definir el método mágico `__clone()` que se llamará después de copiar todas las propiedades.

    Más información en <https://www.php.net/manual/es/language.oop5.cloning.php>

## Herencia

PHP soporta herencia simple, de manera que una clase solo puede heredar de otra, no de dos clases a la vez. Si queremos que la clase A hereda de la clase B haremos:

```
class A extends B
```

El hijo hereda los atributos y métodos públicos y protegidos.

Por ejemplo, tenemos una clase Producto y una Tv que hereda de Producto:

``` php
<?php
class Producto {
    public $codigo;
    public $nombre;
    public $nombreCorto;
    public $PVP;

    public function mostrarResumen() {
        echo "<p>Prod:".$this->codigo."</p>";
    }
}

class Tv extends Producto {
    public $pulgadas;
    public $tecnologia;
}
```

Podemos utilizar las siguientes funciones para averiguar si hay relación entre dos clases:

* `get_parent_class(object): string`
* `is_subclass_of(object, string): bool`

``` php
<?php
$t = new Tv();
$t->codigo = 33;
if ($t instanceof Producto) {
    echo $t->muestra();
}

$padre = get_parent_class($t);
echo "<br>La clase padre es: " . $padre;
$objetoPadre = new $padre;
echo $objetoPadre->mostrarResumen();

if (is_subclass_of($t, 'Producto')) {
    echo "<br>Soy un hijo de Producto";
}
```

### Sobreescribir métodos

Podemos crear métodos en los hijos con el mismo nombre que el padre, cambiando su comportamiento.
Para invocar a los métodos del padre -> `parent::nombreMetodo()`

``` php
<?php
class Tv extends Producto {
   public $pulgadas;
   public $tecnologia;

   public function mostrarResumen() {
      parent::muestra();
      echo "<p>TV ".$this->tecnologia." de ".$this->pulgadas."</p>";
   }
}
```

### Constructor en hijos

En los hijos no se crea ningún constructor de manera automática. Por lo que si no lo hay, se invoca automáticamente al del padre. En cambio, si lo definimos en el hijo, hemos de invocar al del padre de manera explícita.

``` php
<?php
public function __construct(string $codigo, int $pulgadas, string $tecnologia) {
    parent::__construct($codigo);
    $this->pulgadas = $pulgadas;
    $this->tecnologia = $tecnologia;
}
```

## Clases abstractas

Obliga a heredar de una clase, ya que no se permite su instanciación. Se define mediante `abstract class NombreClase {`.  
Una clase abstracta puede contener propiedades y métodos no-abstractos, y/o métodos abstractos.

``` php
<?php
// Clase abstracta
abstract class Producto {
    private $codigo;
    public function getCodigo() : string {
        return $this->codigo;
    }
    // Método abstracto
    abstract public function mostrarResumen();
}
```

Cuando una clase hereda de una clase abstracta, obligatoriamente debe implementar los métodos marcados como abstractos.

``` php
<?php
class Tv extends Producto {
    public $pulgadas;
    public $tecnologia;

    public function mostrarResumen() { //obligado a implementarlo
        echo "<p>Código ".$this->getCodigo()."</p>";
        echo "<p>TV ".$this->tecnologia." de ".$this->pulgadas."</p>";
    }
}

$t = new Tv();
echo $t->getCodigo();
```

## Clases finales

Son clases opuestas a abstractas, ya que evita que se pueda heredar una clase o método para sobreescribirlo.

``` php
<?php
class Producto {
    private $codigo;

    public function getCodigo() : string {
        return $this->codigo;
    }

    final public function mostrarResumen() : string {
        return "Producto ".$this->codigo;
    }
}

final class Microondas extends Producto {
    private $potencia;

    public function getPotencia() : int {
        return $this->potencia;
    }
}
```

## Interfaces

Permite definir un contrato con las firmas de los métodos a cumplir. Así pues, sólo contiene declaraciones de funciones y todas deben ser públicas.

Se declaran con `interface` y luego las clases que cumplan el contrato lo realizan mediante `implements`.

``` php
<?php
interface Nombreable {
// declaración de funciones
}
class NombreClase implements NombreInterfaz {
// código de la clase
```

Permite herencia de interfaces. Una clase puede implementar varios interfaces

``` php
<?php
interface Mostrable {
    public function mostrarResumen() : string;
}

interface MostrableTodo extends Mostrable {
    public function mostrarTodo() : string;
}

interface Facturable {
    public function generarFactura() : string;
}

class Producto implements MostrableTodo, Facturable {
    // Implementaciones de los métodos
    
}
```

## Referencias

* [Manual de PHP](https://www.php.net/manual/es/index.php)

## Actividades

300. Investiga

### Objetos

301. `301Empleado.php`: Crea una clase `Empleado` con su nombre, apellidos y sueldo.
Encapsula las propiedades mediante *getters/setters* y añade métodos para:
    * Obtener su nombre completo -> `getNombreCompleto(): string`
    * Que devuelva un booleano indicando si debe o no pagar impuestos (se pagan cuando el sueldo es superior a 3333€) -> `debePagarImpuestos(): bool`
302. `302EmpleadoTelefonos.php`: Copia la clase del ejercicio anterior y modifícala.
Añade una propiedad privada que almacene un array de números de telefonos.
Añade los siguientes métodos:
    * `public function anyadirTelefono(int $telefono) : void` -> Añade un teléfono al array
    * `public function listarTelefonos(): string` -> Muestra los teléfonos separados por comas
    * `public function vaciarTelefonos(): void` -> Elimina todos los teléfonos
303. `303EmpleadoConstructor.php`: Copia la clase del ejercicio anterior y modifícala.
Elimina los *setters* de `nombre` y `apellidos`, de manera que dichos datos se asignan mediante el constructor.
Si el constructor recibe un tercer parámetro, será el sueldo del Empleado. Si no, se le asignará 1000€ como sueldo inicial.
304. `304EmpleadoConstante.php`: Copia la clase del ejercicio anterior y modifícala.
Añade una constante `SUELDO_TOPE` con el valor del sueldo que debe pagar impuestos, y modifica el código para utilizar la constante.
305. `305EmpleadoSueldo.php`: Copia la clase del ejercicio anterior y modifícala.
Cambia la constante por una variable estática `sueldoTope`, de manera que mediante *getter/setter* puedas modificar su valor.
306. `306EmpleadoStatic.php`: Copia la clase del ejercicio anterior y modifícala.
Completa el siguiente método con una cadena HTML que muestre los datos de un empleado dentro de un párrafo y todos los teléfonos mediante una lista ordenada:
    * `public static function toHtml(Empleado $emp): string`

    <figure style="float: right;">
    <img src="imagenes/03/03p307.png">
    <figcaption>Ejercicio 307</figcaption>
    </figure>

307. `307Persona.php`: Copia la clase del ejercicio anterior en `307Empleado.php` y modifícala.  
Crea una clase `Persona` que sea padre de `Empleado`, de manera que `Persona` contenga el nombre y los apellidos, y en `Empleado` quede el salario y los teléfonos.
308. `308PersonaH.php`: Copia las clases del ejercicio anterior y modifícalas.  
Crea en `Persona` el método estático `toHtml(Persona $p)`.  
Modifica en `Empleado` el mismo método `toHtml()`, pero cambia la firma para que reciba una `Persona` como parámetro.
309. `309PersonaE.php`: Copia las clases del ejercicio anterior y modifícalas.  
Añade en `Persona` un atributo `edad`  
A la hora de saber si un empleado debe pagar impuestos, lo hará siempre y cuando tenga más de 21 años.  
Modifica todo el código necesario para mostrar y/o editar la edad cuando sea necesario.

310. `310PersonaS.php`: Copia las clases del ejercicio anterior y modifícalas.  
Añade nuevos métodos que hagan una representación de todas las propiedades de las clases `Persona` y `Empleado`, de forma similar a los realizados en HTML, pero sin que sean estáticos, de  manera que obtenga los datos mediante `$this`.
    * `function public __toString(): string`

!!! tip "*Magic methods*"
 El método `__toString()` es un método mágico que se invoca automáticamente cuando queremos obtener la representación en cadena de un objeto.

311. `311PersonaA.php`: Copia las clases del ejercicio anterior y modifícalas.  
Transforma `Persona` a una clase abstracta donde su método `toHtml()` tenga que ser redefinido en todos sus hijos.

    <figure style="float: right;">
    <img src="imagenes/03/03p312.png">
    <figcaption>Ejercicio 312</figcaption>
    </figure>

312. `312Trabajador.php`: Copia las clases del ejercicio anterior y modifícalas.
    * Cambia la estructura de clases conforme al gráfico respetando todos los métodos que ya están hechos.
    * `Trabajador` es una clase abstracta que ahora almacena los `telefonos` y donde `calcularSueldo` es un método abstracto.
    * El sueldo de un `Empleado` se calcula a partir de las horas trabajadas y lo que cobra por hora.
    * Para los `Gerente`s, su sueldo se incrementa porcentualmente en base a su edad: `salario + salario*edad/100`

313. `313Empresa.php`: Copia las clases del ejercicio anterior y modifícalas.
    * Crea una clase `Empresa` que además del nombre y la dirección, contenga una propiedad con un array de `Trabajador`es, ya sean `Empleado`s o `Gerente`s. 
    * Añade *getters/setters* para el nombre y dirección.
    * Añade métodos para añadir y listar los trabajadores.
        * `public function anyadirTrabajador(Trabajador $t)`
        * `public function listarTrabajadoresHtml() : string`
    * Añade un método para obtener el coste total en nóminas.
        * `public function getCosteNominas(): float`

314. `314EmpresaI.php`: Copia las clases del ejercicio anterior y modifícalas.
    * Crea un interfaz JSerializable, de manera que ofrezca los métodos:
        * `toJSON(): string` -> utiliza la función `json_encode(mixed)`
        * `toSerialize(): string` -> utiliza la función `serialize(mixed)`
    * Modifica todas las clases que no son abstractas para que implementen el interfaz creado.

### Proyecto Videoclub

