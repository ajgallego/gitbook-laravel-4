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



