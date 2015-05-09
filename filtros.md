
<!-- ************************************************************************-->
# Aplicar filtros a las rutas

Laravel nos permite aplicar filtros a las rutas que definimos en el fichero `routes.php`. Esta característica es muy útil para por ejemplo validar que solo puedan acceder usuarios validados a determinadas áreas de nuestro sitio web o realizar otro tipo de validaciones.



<!-- ************************************** -->
## Definir filtros

Laravel incluye por defecto algunos de los filtros más comunes, los cuales están definidos en el fichero `app/filters.php`. Por ejemplo incluye los filtros `auth` y `guest` que nos permiten validar si el usuario que visita la ruta está _logueado_ o no, o el filtro `csrf` para protegernos de ataques tipo _cross-site request forgery_ como veremos más adelante.

Pero además de estos filtros nos permite definir nuestros propios filtros. Para esto tenemos que añadir el filtro al fichero `app/filters.php`, por ejemplo:


```php
Route::filter('old', function()
{
    if (Input::get('age') < 200)
    {
        return Redirect::to('home');
    }
});
```

Si el filtro devuelve una respuesta, una redirección u otra cosa, entonces esta se considerará la respuesta de la petición. Si no devuelve nada entonces se continuará con el procesamiento normal.




<!-- ************************************** -->
## Asignar un filtro a una ruta

Para añadir un filtro a una ruta tenemos que editar el fichero `routes.php` e incluir, para la ruta en cuestión el filtro como un array asociativo de la forma:

```php
Route::get('user', array('before' => 'old', function()
{
    return 'Tienes más de 200 años!';
}));
```

Donde _before_ indica que el filtro se ejecutará antes que el procesamiento de la función respuesta (en general será siempre así) y `old` es el nombre del filtro (el que habíamos creado antes de ejemplo, también podría ser `auth`, `guest`, `csrf` o alguno otro que nos hayamos definido).


Para añadir un filtro cuando indicamos que la ruta la procese un controlador lo tenemos que hacer de la forma:

```php
Route::get('user', array('before' => 'old', 'uses' => 'UserController@showProfile'));
```

Para añadir varios filtros a una ruta simplemente los tenemos que separar mediante `|`:

```php
Route::get('user', array('before' => 'auth|old', function()
{
    return 'Estás logueado y tienes más de 200 años!';
}));
```

El comando `php artisan routes`, además de mostrar una tabla con las rutas definidas para la aplicación, también incluye los filtros que tienen asignadas estas rutas. Por lo tanto es muy útil para comprobar que todas las rutas y filtros que hemos definido se hayan creado correctamente.





<!-- ************************************** -->
## Grupos de rutas

Laravel permite aplicar un filtro a un grupo de rutas agrupándolas todas dentro de un `Route::group`, de esta forma solo tendremos que especificar el filtro una vez y además nos permitirá dividir las rutas en secciones (distinguiendo mejor a que secciones se les está aplicando un filtro):

```php
Route::group(array('before' => 'auth'), function()
{
    Route::get('/', function()
    {
        //
    });

    Route::get('user/profile', function()
    {
        //
    });
});
```



<!-- ************************************************************************-->
## Grupos de rutas con prefijo

Una opción interesante al incluir rutas en el fichero de rutas es poder agruparlas. Ya hemos visto esta opción para aplicar un determinado filtro a un grupo de rutas, pero también podemos utilizarlo para indicar un prefijo. Por ejemplo, si queremos que un grupo de rutas empiece por el prefijo `api/v1` tendríamos que hacer lo siguiente:

```php
Route::group(array('prefix' => 'api/v1'), function()
{
	Route::get('recurso',      'Controller@getRecurso');
    Route::post('recurso',     'Controller@postRecurso');
	Route::get('recurso/{id}', 'Controller@putRecurso');
	//...
});
```

De esta forma podemos crear secciones dentro de nuestro fichero de rutas para agrupar, por ejemplo, todas las rutas públicas, todas las de la sección privada de administración, sección privada de usuario y las rutas de las diferentes versiones de la API de nuestro sitio. 



