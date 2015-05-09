# Capítulo 3 - Base de datos

Laravel facilita la configuración y el uso de diferentes tipos de base de datos: MySQL, Postgres, SQLite y SQL Server. En el fichero de configuración (`app/config/database.php`) tenemos que indicar todos los parámetros de acceso a nuestras bases de datos y además especificar cual es la conexión que se utilizará por defecto. En Laravel podemos hacer uso de varias bases de datos a la vez, aunque sean de distinto tipo. Por defecto se accederá a la que especifiquemos en la configuración y si queremos acceder a otra conexión lo tendremos que indicar expresamente al realizar la consulta. 






<!-- *********************************** -->
## _Schema Builder_

Una vez creada una migración tenemos que completar los métodos `up` y `down` de la misma para indicar la tabla que queremos crear o el campo que queremos modificar. Además, en el método `down` siempre tendremos que añadir la operación inversa, eliminar la tabla que se ha creado en el método `up` o eliminar la columna que se ha añadido. Esto nos permitirá deshacer migraciones dejando la base de datos en el mismo estado en el que se encontraban antes de que se añadieran.

Para especificar la tabla a crear o modificar así como las columnas y tipos de datos de las mismas se utiliza _Schema_, la cual tiene una API que nos permite especificar la estructura de las tablas independientemente del sistema de base de datos que utilicemos. 

Para añadir una tabla a la base de datos se utiliza el siguiente constructor: 

```php
Schema::create('users', function($table)
{
    $table->increments('id');
});
```

Donde, el primer argumento es el nombre de la tabla y el segundo es una función que recibe como parámetro la tabla para que podamos configurar las columnas que va a contener. 

En la sección `down` de la migración tendremos que eliminar la tabla que hemos creado, para esto podemos utilizar alguno de estos métodos: 

```php
Schema::drop('users');

Schema::dropIfExists('users');
```

Al crear una migración con los comandos que hemos visto en la sección anterior ya nos viene este código añadido por defecto, la creación y eliminación de la tabla que se ha indicado y además se añaden un par de columnas por defecto (_id_ y _timestamps_). 

Como hemos dicho, el constructor `Schema::create` recibe como segundo parámetro una función que nos permite especificar las columnas que va a tener dicha tabla. En esta función podemos ir añadiendo todos los campos que queramos, indicando para cada uno de ellos su tipo y nombre, y además si queremos también podremos indicar una serie de modificadores como valor por defecto, índices, etc. Por ejemplo: 

```php
Schema::create('users', function($table)
{
    $table->increments('id');
    $table->string('username', 32);
    $table->string('password');
    $table->smallInteger('votos');
    $table->string('direccion');
    $table->boolean('confirmado')->default(false);
    $table->timestamps();
});
```

_Schema_ define muchos tipos de datos que podemos utilizar para definir las columnas de una tabla, algunos de los principales son: 


| Comando                                       | Tipo de campo |
| --------------------------------------------- | -- |
| $table->boolean('confirmed'); 	               | BOOLEAN |
| $table->enum('choices', array('foo', 'bar')); | ENUM |
| $table->float('amount');                      | FLOAT |
| $table->increments('id');                     | Clave principal tipo INTEGER con Auto-Increment |
| $table->integer('votes');                     | INTEGER |
| $table->mediumInteger('numbers');             | MEDIUMINT |
| $table->smallInteger('votes'); 	           | SMALLINT |
| $table->tinyInteger('numbers'); 	           | TINYINT |
| $table->string('email'); 	                    | VARCHAR |
| $table->string('name', 100); 	                | VARCHAR con la longitud indicada |
| $table->text('description'); 	                | TEXT |
| $table->timestamp('added_on'); 	           | TIMESTAMP |
| $table->timestamps(); 	                       | Añade los _timestamps_ "created_at" y "updated_at" |
| ->nullable() 	                                | Indicar que la columna permite valores NULL |
| ->default($value) 	                           | Declare a default value for a column |
| ->unsigned() 	                                | Añade UNSIGNED a las columnas tipo INTEGER | 


Para consultar todos los tipos de datos que podemos utilizar podéis consultar la documentación de Laravel en: 

http://laravel.com/docs/4.2/schema#adding-columns





<!-- *********************************** -->
### Añadir índices

_Schema_ soporta los siguientes tipos de índices: 

| Comando                                    | Descripción | 
| ------------------------------------------ | -- |
| $table->primary('id');                     | Añadir una clave primaria |
| $table->primary(array('first', 'last')); 	| Definir una clave primaria compuesta |
| $table->unique('email'); 	                | Definir el campo como UNIQUE |
| $table->index('state'); 	                | Añadir un índice a una columna |

En la tabla se especifica como añadir estos índices después de crear el campo, pero también permite indicar estos índices a la vez que se crea el campo: 

```php
$table->string('email')->unique();
```




<!-- *********************************** -->
### Claves ajenas

Desde _Schema_ también podemos definir claves ajenas entre tablas:

```php
$table->integer('user_id')->unsigned();
$table->foreign('user_id')->references('id')->on('users');
```

En este ejemplo, en primer lugar añadimos la columna "`user_id`" de tipo UNSIGNED INTEGER (siempre tendremos que crear primero la columna sobre la que se va a aplicar la clave ajena). A continuación creamos la clave ajena entre la columna "`user_id`" y la columna "`id`" de la tabla "`users`".


> La columna con la clave ajena tiene que ser del mismo tipo que la columna a la que apunta. Si por ejemplo creamos una columna a un índice auto-incremental tendremos que especificar que la columna sea _unsigned_ para que no se produzcan errores.


También podemos especificar las acciones que se tienen que realizar para "_on delete_" y "_on update_": 

```php
$table->foreign('user_id')
      ->references('id')->on('users')
      ->onDelete('cascade');
```


Para eliminar una clave ajena, en el método `down` de la migración tenemos que utilizar el siguiente código:  

```php
$table->dropForeign('posts_user_id_foreign');
```

Para indicar la clave ajena a eliminar tenemos que seguir el siguiente patrón para especificar el nombre `<tabla>_<columna>_foreign`. Donde "tabla" es el nombre de la tabla actual y "columna" el nombre de la columna sobre la que se creo la clave ajena.







<!-- ************************************************************************-->
# Inicialización de la base de datos (_Database Seeding_)

Laravel también facilita la inserción de datos iniciales en la base de datos. Esta opción es muy útil para tener datos de prueba cuando estamos desarrollando una web o para crear tablas que ya tienen que contener una serie de datos iniciales en producción. 

Los ficheros de "semillas" se encuentran en la carpeta `app/database/seeds`. En esta carpeta podemos añadir más ficheros PHP con clases que extiendan de `Seeder` para definir nuestros propios ficheros de "semillas". El nombre de los ficheros suele seguir el mismo patrón `<nombre-tabla>TableSeeder`, por ejemplo "`UserTableSeeder`".

Por defecto Laravel incluye el fichero `DatabaseSeeder`, que será el primero que se llamará. Dentro de su método `run` tendremos que añadir llamadas a nuestros ficheros de semillas para que se ejecuten. A continuación se incluye un ejemplo: 


```php
// Clase principal de semillas...
class DatabaseSeeder extends Seeder 
{
    public function run()
    {
        // Llamamos a nuestro fichero de semillas
        $this->call('UserTableSeeder');

        // Mostramos información por consola
        $this->command->info('User table seeded!');
    }
}

// Nuestro fichero de semillas
class UserTableSeeder extends Seeder 
{
    public function run()
    {
        // Dentro del método run es donde tenemos que inicializar la tabla

        // Borramos los datos de la tabla
        DB::table('users')->delete();

        // Añadimos una entrada a esta tabla
        User::create(array('email' => 'foo@bar.com'));
    }
}
```

> Otra opción es crear métodos dentro de la clase principal `DatabaseSeeder` que se llamen desde su método `run`.

Como se puede ver en el ejemplo en general tendremos que eliminar primero los datos de la tabla en cuestión y posteriormente añadir los datos. Para insertar datos en una tabla podemos utilizar el método que se usa en el ejemplo o alguna de las otras opciones que se verán en la sección sobre "modelos de datos".


Una vez definidos los ficheros de semillas, cuando queramos ejecutarlos para rellenar de datos la base de datos tendremos que usar el siguiente comando de Artisan:

```bash
php artisan db:seed
```






<!-- ************************************************************************-->
# Constructor de consultas (_Query Builder_)

Laravel incluye una serie de clases que nos facilita la construcción de consultas y otro tipo de operaciones con la base de datos. Además, al utilizar estas clases, creamos una notación mucho más legible, compatible con todos los tipos de bases de datos soportados por Laravel y que nos previene de cometer errores o de ataques por inyección de código SQL. 



<!-- *************************************** -->
## Consultas 

Para realizar una "Select" que devuelva todas las filas de una tabla utilizaremos el siguiente código: 

```php
$users = DB::table('users')->get();

foreach ($users as $user)
{
    var_dump($user->name);
}
```

En el ejemplo se utiliza el constructor `DB::tabla` indicando la tabla sobre la que se va a realizar la consulta, y por último se llama al método `get()` para obtener todas las filas de la misma.

Si queremos obtener un solo elemento podemos añadir una clausula `where` y usar `first` para obtener los datos:

```php
$user = DB::table('users')->where('name', 'Pedro')->first();

var_dump($user->name);
```

En este ejemplo, la clausula `where` filtrará todas las filas cuya columna `name` sea igual a `Pedro`. Si queremos realizar otro tipo de filtrados, como columnas que tengan un valor mayor (`>`), menor (`<`) o distinto del indicado (`<>`), lo podemos indicar como segundo parámetro de la forma: 


```php
$users = DB::table('users')->where('votes', '>', 100)->get();
```

Si añadimos más clausulas `where` a la consulta por defecto se utilizará el operador lógico `AND`. En caso de que queramos utilizar el operador lógico `OR` lo tendremos que realizar de la forma: 


```php
$users = DB::table('users')
                    ->where('votes', '>', 100)
                    ->orWhere('name', 'Pedro')
                    ->get();
```


También podemos utilizar los métodos `orderBy`, `groupBy` y `having` en las consultas: 

```php
$users = DB::table('users')
                    ->orderBy('name', 'desc')
                    ->groupBy('count')
                    ->having('count', '>', 100)
                    ->get();
```


Si queremos indicar un _offset_ o _limit_ lo realizaremos mediante los métodos `skip` (para el _offset_) y `take` (para _limit_), por ejemplo: 


```php
$users = DB::table('users')->skip(10)->take(5)->get();
```


Para más información sobre la construcción de _Querys_ (_join, insert, update, delete_, agregados, etc.) podéis consultar la documentación de Laravel en su sitio web: 

http://laravel.com/docs/4.2/queries




<!-- *************************************** -->
## Transacciones

Laravel también permite crear transacciones sobre un conjunto de operaciones:

```php
DB::transaction(function()
{
    DB::table('users')->update(array('votes' => 1));

    DB::table('posts')->delete();
});
```

En caso de que se produzca cualquier excepción en las operaciones que se realizan en la transacción se desharían todos los cambios aplicados hasta ese momento de forma automática. 








<!-- ************************************************************************-->
# Modelos de datos mediante ORM

El mapeo objeto-relacional (más conocido por su nombre en inglés, _Object-Relational mapping_, o sus siglas ORM) es una técnica de programación para convertir datos entre el sistema de tipos utilizado en un lenguaje de programación orientado a objetos y la utilización de una base de datos relacional como motor de persistencia. En la práctica esto crea una base de datos orientada a objetos virtual, sobre la base de datos relacional. Esto posibilita el uso de las características propias de la orientación a objetos (básicamente herencia y polimorfismo). 

Laravel incluye su propio sistema de ORM llamado _Eloquent_, el cual nos proporciona una manera elegante y fácil de interactuar con la base de datos. Para esto cada tabla en la base datos debe tener su correspondiente modelo el cual se utilizará para interactuar desde código con la tabla. 

Los modelos se guardarán como clases PHP en la carpeta `app/models`. Para definir un modelo que use _Eloquent_ únicamente tenemos que crear una clase que herede de `Eloquent`:

```php
<?php 
// Todos los modelos tienen que extender la clase Eloquent
class User extends Eloquent {} 
```

En general el nombre de los modelos se pone en singular con la primera letra en mayúscula, mientras que el nombre de las tablas suele estar en plural. Gracias a esto, al definir un modelo no es necesario indicar el nombre de la tabla asociada, sino que Eloquent automáticamente buscará la tabla transformando el nombre del modelo a minúsculas y buscando su plural (en inglés). En el caso de que la tabla tuviese otro nombre lo podemos indicar definiendo la propiedad protegida `$table` del modelo: 

```php
<?php 
class User extends Eloquent 
{
    protected $table = 'my_users';
}
```

Laravel también asume que cada tabla tiene declarada una clave primaria con el nombre `id`. En el caso de que no sea así y queramos cambiarlo tendremos que sobrescribir el valor de la propiedad protegida `$primaryKey` del modelo, por ejemplo: `protected $primaryKey = 'my_id';`. Es importante definir este valor ya que se utiliza en determinados métodos, como por ejemplo para buscar registros o para crear las relaciones entre modelos. 

Otra propiedad que en ocasiones tendremos que establecer son los _timestamps_ automáticos. Por defecto Eloquent asume que todas las tablas contienen los campos `updated_at` y `created_at` (los cuales los podemos añadir muy fácilmente con _Schema_ añadiendo `$table->timestamps()` en la migración). Estos campos se actualizarán automáticamente cuando se cree un nuevo registro o se modifique. En el caso de que no queramos utilizarlos (y que no estén añadidos a la tabla) tendremos que indicarlo en el modelo, de otra forma nos daría un error. Para indicar que no los actualice automáticamente tendremos que modificar el valor de la propiedad pública `$timestamps` a _false_, por ejemplo: `public $timestamps = false;`.

A continuación se muestra un ejemplo de un modelo de _Eloquent_ en el que se añaden todas las especificaciones que hemos visto: 

```php
<?php 
class User extends Eloquent 
{
    protected $table = 'my_users';
    protected $primaryKey = 'my_id'
    public $timestamps = false;
}
```



<!-- ************************************** -->
### Consultar datos

Una vez creado el modelo ya podemos empezar a utilizarlo para recuperar datos de la base de datos o para actualizarlos. Por ejemplo, para obtener todas las filas de la tabla asociada a un modelo usaremos el método `all()`: 


```php
$users = User::all();

foreach( $users as $user ) {
    var_dump( $user->name );
}
```

Este método nos devolverá un array de resultados, donde cada item del array será una instancia del modelo `User`. Gracias a esto al obtener un elemento del array podemos acceder a los campos o columnas de la tabla como si fueran propiedades del objeto (`$user->name`).


> Nota: Todos los métodos que se describen en la sección de "Constructor de consultas" y en la documentación de Laravel sobre "Query Builder" también se pueden utilizar en los modelos Eloquent. Por lo tanto podremos utilizar _where, orWhere, first, get, orderBy, groupBy, having, skip, take,_ etc. para elaborar las consultas. 


Eloquent también incorpora el método `find($id)` para buscar un elemento a partir del identificador único que tenga definido, por ejemplo: 

```php
$user = User::find(1);
var_dump($user->name);
```

Si queremos que se lance una excepción cuando no se encuentre un modelo podemos utilizar los métodos `findOrFail` o `firstOrFail`. Esto nos permite capturar las excepciones y mostrar un error 404 cuando sucedan.

```php
$model = User::findOrFail(1);

$model = User::where('votes', '>', 100)->firstOrFail();
```


A continuación se incluyen otros ejemplos de consultas usando Eloquent con algunos de los métodos que ya habíamos visto en la sección "Constructor de consultas": 

```php
$users = User::where('votes', '>', 100)->take(10)->get();

foreach ($users as $user)
{
    var_dump($user->name);
}

// O para obtener el primer usuario de la lista
$user = User::where('votes', '>', 100)->first();
```


También podemos utilizar los métodos agregados para calcular el total de registros obtenidos, o el máximo, mínimo, media o suma de una determinada columna. Por ejemplo: 

```php
$count = User::where('votes', '>', 100)->count();
$price = Orders::max('price');
$price = Orders::min('price');
$price = Orders::avg('price');
$total = User::sum('votes');
```






<!-- ************************************** -->
### Insertar datos

Para añadir una entrada en la tabla de la base de datos asociada con un modelo simplemente tenemos que crear una nueva instancia de dicho modelo, asignar los valores que queramos y por último utilizar el método `save()`: 

```php
$user = new User;
$user->name = 'Juan';
$user->save();
```

Para obtener el identificador asignado en la base de datos después de guardar (cuando se trate de tablas con índice auto-incremental), lo podremos recuperar simplemente accediendo al campo `id` del objeto que habíamos creado, por ejemplo: 

```php
$insertedId = $user->id;
```



<!-- ************************************** -->
### Actualizar datos 

Para actualizar una instancia de un modelo es muy sencillo, solo tendremos que recuperar en primer lugar la instancia que queremos actualizar, a continuación modificarla y por último guardar los datos: 

```php
$user = User::find(1);
$user->email = 'juan@gmail.com';
$user->save();
```



<!-- ************************************** -->
### Borrar datos

Para borrar una instancia de un modelo en la base de datos simplemente tenemos que usar su método `delete()`: 

```php
$user = User::find(1);
$user->delete();
```

Si por ejemplo queremos borrar un conjunto de resultados también podemos usar el método `delete()` de la forma: 

```php
$affectedRows = User::where('votes', '>', 100)->delete();
```




<!-- ************************************** -->
### Más información

Para más información sobre como crear relaciones entre modelos, _eager loading_, etc. podéis consultar directamente la documentación de Laravel en:

http://laravel.com/docs/4.2/eloquent












<!-- ************************************************************************-->
<!-- ************************************************************************-->
<!-- ************************************************************************-->
<!-- ************************************************************************-->



<!-- ************************************************************************-->
# Ejercicios

En estos ejercicios vamos a continuar con el proyecto del videoclub que habíamos empezado en sesiones anteriores y le añadiremos todo lo referente a la gestión de la base de datos. 




<!-- ************************************ -->
# Ejercicio 1 - Configuración de la base de datos y migraciones (1 punto)

En este primer ejercicio en primer lugar vamos a configurar correctamente la base de datos. Para esto tenemos que ir al fichero `app/config/database.php` e indicar que vamos a usar una base de datos de MySQL llamada "videoclub". Además tendremos que añadir nuestro nombre de usuario y contraseña. 

> Nota: XAMPP por defecto crea el usuario de base de datos _root_ sin contraseña. 

Ahora tendremos que abrir PHPMyAdmin y crear esta base de datos para poder empezar a trabajar con ella. Para comprobar que todo se ha configurado correctamente abrimos un terminal en la carpeta de nuestro proyecto y ejecutamos el comando que crea la tabla de migraciones. Si todo va bien podremos actualizar desde PHPMyAdmin y comprobar que se ha creado esta tabla dentro de nuestra nueva base de datos. 

A continuación vamos a crear la tabla que utilizaremos para almacenar el catálogo de películas. Ejecuta el comando de artisan para crear la migración llamada `create_movies_table` para la tabla `movies`. Una vez creado edita este fichero para añadir todos los campos necesarios, estos son: 

| Campo      | Tipo                   | Valor por defecto |
| ---------- | ---------------------- | ---- |
| id         | Autoincremental        |      |
| title      | String                 |      |
| year       | String de longitud 8   |      |
| director   | String de longitud 64  |      |
| poster     | String                 |      |
| rented     | Booleano               | false |
| synopsis   | Text                   |      |
| timestamps | Timestamps de Eloquent |      |


> Recuerda que en el método `down` de la migración tienes que deshacer los cambios que has hecho en el método `up`, en este caso sería eliminar la tabla.

Por último ejecutaremos el comando de artisan que añade las nuevas migraciones y comprobaremos en PHPMyAdmin que la tabla se ha creado correctamente con los campos que le hemos indicado. 



<!-- ************************************ -->
# Ejercicio 2 - Modelo de datos (0.5 puntos)

En este ejercicio vamos a crear el modelo de datos asociado con la tabla _movies_. Para esto crearemos un nuevo fichero PHP llamado `Movie.php` en la carpeta `app/models` con las siguientes características: 

* El modelo tiene que heredar de la clase `Eloquent`.

Nada más, el cuerpo de la clase puede estar vacío (`{}`), todo lo demás se hace automáticamente!



<!-- ************************************ -->
# Ejercicio 3 - Semillas (1 punto)

Ahora vamos a proceder a rellenar la tabla de la base de datos con los datos iniciales. Para esto editamos el fichero de semillas situado en `app/database/seeds/DatabaseSeeder.php` y seguiremos los siguientes pasos: 

* Creamos un método privado (dentro de la misma clase) llamado `seedCatalog()` que se tendrá que llamar desde el método `run` de la forma: 
<br/>
```php
public function run()
{
    self::seedCatalog();
    $this->command->info('Tabla catálogo inicializada con datos!');
}
```

* Movemos el array de películas que se facilitaba en los materiales y que habíamos copiado dentro del controlador `CatalogController` a la clase de semillas (`DatabaseSeeder.php`), guardándolo de la misma forma, como variable privada de la clase. 

* Dentro del nuevo método `seedCatalog()` realizamos las siguientes acciones: 
  * En primer lugar borramos el contenido de la tabla `movies` con `DB::table('movies')->delete();`. 
  * Y a continuación añadimos el siguiente código: 
<br/>
```php
foreach( $this->arrayPeliculas as $pelicula ) {
	$p = new Movie;
	$p->title = $pelicula['title'];
	$p->year = $pelicula['year'];
	$p->director = $pelicula['director'];
	$p->poster = $pelicula['poster'];
	$p->rented = $pelicula['rented'];
	$p->synopsis = $pelicula['synopsis'];
	$p->save();
}
```

Por último tendremos que ejecutar el comando de artisan que procesa las semillas y una vez realizado abriremos PHPMyAdmin para comprobar que se rellenado la tabla _movies_ con el listado de películas. 




<!-- ************************************ -->
# Ejercicio 4 - Uso de la base de datos (1 punto) 

En este último ejercicio vamos a actualizar los métodos del controlador `CatalogController` para que obtengan los datos de la base de datos: 

* Modificar el método `getIndex` para que obtenga toda la lista de películas de la base de datos (usando el modelo `Movie`) y se la pase a la vista. 
* Modificar el método `getShow` para que obtenga la película pasada por parámetro usando el método `findOrFail` y se la pase a la vista. 
* Modificar el método `getEdit` para que obtenga la película pasada por parámetro usando el método `findOrFail` y se la pase a la vista. 

Ahora tendremos que actualizar las vistas para que en lugar de acceder a los datos del array los obtenga del objeto con la película. Para esto cambiaremos en todos los sitios donde hayamos puesto `$pelicula['campo']` por `$pelicula->campo`. 

Además, en la vista `catalog/index.blade.php`, en vez de utilizar el índice del array como identificador para crear el enlace a `catalog/show/{id}`, tendremos que utilizar el campo `id` de la película. Lo mismo en la vista `catalog/show.blade.php`, para generar el enlace de editar película tendremos que añadir el identificador de la película a la ruta `catalog/edit`. 





























