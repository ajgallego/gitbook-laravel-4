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


