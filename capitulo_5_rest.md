

<!-- ************************************************************************-->
# Controladores de recursos _RESTful_

Laravel incorpora un tipo especial de controlador, llamado controlador de recuso (_recource controller_), que facilita la construcción de controladores tipo _RESTful_. Para esto simplemente tendríamos que usar el comando de artisan `php artisan controller:make <nombre-controlador>` para crear el controlador y añadir la ruta al fichero de rutas `routes.php` usando `Route::resource`.

Por ejemplo, para crear un controlador para la gestión de imágenes almacenadas en la aplicación, en primer lugar ejecutaríamos el siguiente comando:

```bash
$ php artisan controller:make PhotoController
```

Esto crearía el controlador `PhotoController` en la carpeta `app/controllers`. Lo único que nos faltaría es registrar las rutas asociadas añadiendo al fichero `routes.php` la siguiente línea:

```php
Route::resource('photo', 'PhotoController');
```

Esta sola línea de ruta crea por si sola múltiples rutas para gestionar todos los tipos de peticiones RESTful. Además, el controlador creado mediante Artisan estará preparado con todos los métodos necesarios para responder a todos los tipos de peticiones. En la siguiente tabla se muestra un resumen de todas las rutas generadas, el tipo de petición a la que responden y la acción que realizan en el controlador:


| Verbo     | Ruta                   | Acción  | Controlador / método |
| --------- | ---------------------- | ------- | --------------- |
| GET       | /photo                 | index   | PhotoController@index  |
| GET    	| /photo/create 	        | create  | PhotoController@create |
| POST 	    | /photo 	            | store   | PhotoController@store  |
| GET 	    | /photo/{resource} 	    | show 	  | PhotoController@show   |
| GET 	    | /photo/{resource}/edit | edit 	  | PhotoController@edit   |
| PUT/PATCH | /photo/{resource} 	    | update  | PhotoController@update |
| DELETE 	| /photo/{resource} 	    | destroy | PhotoController@destroy|




<!-- *********************************** -->
## Restringir rutas en un controlador RESTful

En ocasiones nos interesará declarar solamente un subconjunto de las acciones que soporta REST, para esto, al declarar la ruta tipo `resource` tenemos que añadir un tercer parámetro con la opción `only` (para que solo se creen esas rutas) o `except` (para que se creen todas las rutas excepto las indicadas), por ejemplo:

```php
Route::resource('photo', 'PhotoController',
                array('only' => array('index', 'show')));

Route::resource('photo', 'PhotoController',
                array('except' => array('create', 'store', 'update', 'destroy')));
```


Además, a la hora de generar el controlador usando Artisan también podemos indicar de la misma forma que no nos genere todos los métodos:

```php
$ php artisan controller:make PhotoController --only=index,show

$ php artisan controller:make PhotoController --except=index
```




<!-- *********************************** -->
## Controladores de recursos anidados

Para "anidar" controladores de recursos se tiene que utilizar el punto "." como separador de los recursos en la declaración de la ruta, por ejemplo:

```php
Route::resource('photos.comments', 'PhotoCommentController');
```

Esta ruta representaría que el recurso "_comments_" estaría anidado o contenido en el recurso "_photos_".

Las rutas generadas para este tipo de recursos siguen el siguiente patrón: `photos/{photoResource}/comments/{commentResource}`, donde se tiene que especificar el identificador de ambos recursos para poder acceder.

Los controladores asociados recibirían en este caso dos identificadores, primero el del recurso base y segundo el del recurso anidado:

```php
class PhotoCommentController extends BaseController
{
    public function show($photoId, $commentId)
    {
        //
    }
}
```



<!-- *********************************** -->
## Rutas adicionales en un controlador tipo RESTful

Si queremos definir rutas adicionales para un controlador de recursos simplemente las tenemos que añadir al fichero de rutas `routes.php` antes que las rutas del propio recurso, por ejemplo:

```php
Route::get('photos/popular', 'PhotoController@getPopular');
Route::resource('photos', 'PhotoController');
```

