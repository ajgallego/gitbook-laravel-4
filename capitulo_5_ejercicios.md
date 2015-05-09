# Ejercicios

En esta sección de ejercicios vamos a terminar la web de gestión del videoclub añadiendo notificaciones, el funcionamiento de algunos botones que faltaban y por último, una API RESTful para el acceso externo.



<!-- ************************************ -->
## Ejercicio 1 - Notificaciones (0.5 puntos)

En este primer ejercicio vamos a instalar una librería externa para mostrar las notificaciones de nuestra aplicación, para esto tenéis que seguir los pasos indicados en el apartado de teoría "Ejemplo: instalación de paquete de notificaciones".

Una vez instalado y correctamente configurado vamos a modificar los controladores para mostrar un aviso tipo `success` después de guardar y editar una película (la notificación se tendrá que añadir antes de realizar la redirección). Además también mostraremos una notificación de tipo `error` cuando se produzca un error en la autenticación del usuario.

Por último vamos a modificar la vista con el layout principal, situada en `app/views/layouts/master.blade.php`, para indicar que se muestren las notificaciones justo antes del contenido principal:

```php
<div class="container">
    {{ Notification::showAll() }}

    @yield('content')
</div>
```



<!-- ************************************ -->
## Ejercicio 2 - Completando botones  (1 punto)

En este ejercicio vamos añadir la funcionalidad de los botones de alquilar, devolver y eliminar película. Todos estos botones están situados en la vista detalle de una película (el de eliminar lo tendremos que añadir). En todos los casos tendremos que crear una nueva ruta, un nuevo método en el controlador, actualizar el botón en la vista y mostrar una notificación después de realizar la acción. En la siguiente tabla se muestra un resumen de las rutas:

| Ruta                  | Tipo   | Controlador / Acción        |
| --------------------- | ------ | --------------------------- |
| /catalog/rent/{id}    | PUT    | CatalogController@putRent   |
| /catalog/return/{id}  | PUT    | CatalogController@putReturn |
| /catalog/delete/{id}  | DELETE | CatalogController@deleteMovie |

En primer lugar tenéis que añadir las rutas al fichero `routes.php` y posteriormente modificar el controlador `CatalogController` para añadir los tres nuevos métodos. Estos tres métodos son similares al método que ya habíamos implementado antes para editar los datos de una película. En el caso de `putRent` y `putReturn` únicamente modificaremos el campo `rented` asignándole el valor _true_ y _false_ respectivamente, y una vez guardado añadiremos la notificación y realizaremos una redirección a la pantalla con la vista detalle de la película. En el método `deleteMovie` también obtendremos el registro de la película pero tendremos que llamar al método `delete()` de la misma, una vez hecho esto añadiremos la notificación y realizaremos una redirección al listado general de películas.

A continuación tenemos que editar la vista detalle de películas para modificar los botones (`app/views/catalog/show.blade.php`). Dado que la acciones se tienen que realizar usando peticiones HTTP tipo PUT y DELETE no podemos poner un enlace normal (ya que este sería tipo GET). Para solucionarlo tenemos que crear un formulario alrededor del botón y asignar al formulario el método correspondiente, por ejemplo:

```php
{{ Form::open(array('method' => 'put', 'action'=>array('CatalogController@putReturn', $pelicula->id))) }}
	{{ Form::submit('Devolver película', array('class' => 'btn btn-danger')) }}
{{ Form::close() }}
```




<!-- ************************************ -->
## Ejercicio 3 - Api Restful y pruebas (1.5 puntos)

En este ejercicio vamos a crear una API tipo RESTful para permitir el acceso y gestión del catálogo del videoclub de forma externa. En la siguiente tabla se muestra el listado de todas las rutas que vamos a definir para la API:


| Ruta                        | Método  | Filtro   | Controlador / Método         |
| --------------------------- | ------- | -------- | ---------------------------- |
| /api/v1/catalog             | GET     |          | APICatalogController@index   |
| /api/v1/catalog/{id} 	      | GET    |  	      | APICatalogController@show    |
| /api/v1/catalog 	          | POST   | auth.once | APICatalogController@store  |
| /api/v1/catalog/{id} 	      | PUT    | auth.once | APICatalogController@update  |
| /api/v1/catalog/{id} 	      | DELETE | auth.once | APICatalogController@destroy |
| /api/v1/catalog/{id}/rent   | PUT     | auth.once | APICatalogController@putRent  |
| /api/v1/catalog/{id}/return | PUT     | auth.once | APICatalogController@putReturn |


Por lo tanto, tendremos que definir todas las rutas RESTful para el catálogo, además de dos especiales: `/rent` y `/return`.
Todos los métodos estarán protegidos con contraseña a excepción de `index` y `show` que serán públicos.
Tenéis que comprobar que las rutas y filtros sean los correctos usando el método de Artisan `php artisan routes`.

> Pista: para poder aplicar un filtro solamente a algunos de los métodos del controlador tendréis que separar la declaración de las rutas. Para esto podéis utilizar el tercer parámetro con las opciones `only` y `except`.


A continuación tenéis que añadir el nuevo controlador `APICatalogController` (usando el comando de Artisan) y completar sus métodos. Las acciones y contenidos de estos métodos serán muy similares a los de `CatalogController`, pero teniendo en cuenta que no tendremos que devolver una nueva vista sino directamente el contenido de la consulta en formato JSON, por ejemplo, el método que devuelve el listado de todas las películas sería simplemente:

```php
public function index()
{
	return Response::json( Movie::all() );
}
```

Para devolver una respuesta en los métodos que realizan alguna acción (por ejemplo para indicar que la película se ha modificado correctamente o si ha habido algún error de validación) podemos realizar lo siguiente:

```php
public function putRent($id)
{
	$m = Movie::findOrFail( $id );
	$m->rented = true;
	$m->save();

	return Response::json( array('error' => false,
	                              'msg' => 'La película se ha marcado como alquilada' ) );
}
```

Por último, utiliza `curl` para comprobar que todas las rutas que has creado funcionan correctamente. Recuerda que para enviar



```bash
curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X PUT -d '{"title":"nuevo titulo"}' http://localhost/catalog/21
```

> Aviso: Por defecto el filtro `auth.once` y `auth.basic` realizan la autenticación usando el campo `email` de la tabla de usuarios. Para utilizar otro campo es necesario modificar el filtro en el fichero `filters.php` (revisar la teoría).


> Aviso: hemos de tener cuidado con el método de actualizar los datos de una película, ya que los campos que no se envíen se asignarán como vacíos. Para solucionar esto podemos actualizar solamente los campos que contengan algún valor o enviar siempre todos los campos.




<!-- ************************************ -->
<!--
## Ejercicio Opcional - Validación

De forma opcional se pide realizar la validación de los datos enviados desde los formularios para crear y editar películas.
El formato de los datos tendrá que ser el siguiente:

| Campo    | Validaciones |
| -------- | ------------ |
| title    | requerido, longitud máxima 255 |
| year     | requerido, longitud máxima 8 |
| director | requerido, longitud máxima 64 |
| poster   | requerido, formato URL, lontitud máxima 255 |


En caso de error se tendrá que mostrar el error correspondiente usando la librería de notificaciones que hemos instalado en el ejercicio 1.

Podéis encontrar información sobre como realizar las validaciones en la documentación de Laravel: http://laravel.com/docs/4.2/validation


-->
