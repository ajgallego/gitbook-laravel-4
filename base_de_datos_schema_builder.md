
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





