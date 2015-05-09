# Capítulo 3 - Base de datos

Laravel facilita la configuración y el uso de diferentes tipos de base de datos: MySQL, Postgres, SQLite y SQL Server. En el fichero de configuración (`app/config/database.php`) tenemos que indicar todos los parámetros de acceso a nuestras bases de datos y además especificar cual es la conexión que se utilizará por defecto. En Laravel podemos hacer uso de varias bases de datos a la vez, aunque sean de distinto tipo. Por defecto se accederá a la que especifiquemos en la configuración y si queremos acceder a otra conexión lo tendremos que indicar expresamente al realizar la consulta. 










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





























