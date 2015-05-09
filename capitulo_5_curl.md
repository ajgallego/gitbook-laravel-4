
<!-- ************************************************************************-->
# Probar nuestra API con _cURL_

Para probar una API lo podemos hacer fácilmente utilizando el comando `curl` desde consola, el cual permite enviar peticiones de cualquier tipo a una URL, especificar las cabeceras, parámetros, etc.

Por ejemplo, para realizar una petición tipo GET a una URL simplemente tenemos que hacer:

```bash
$ curl -i http://localhost/recurso

HTTP/1.1 200 OK
Transfer-Encoding: chunked
Date: Fri, 27 Jul 2012 05:11:00 GMT
Content-Type: text/plain

¡Hola Mundo!
```

Donde la opción `-i` indica que se muestren las cabeceras de la respuesta.

Opcionalmente, al hacer la petición podemos indicar las cabeceras con el parámetro `-H`. Por ejemplo, para solicitar datos en formato JSON tenemos que hacer:

```bash
$ curl -i -H "Accept: application/json" http://localhost/recurso

HTTP/1.1 200 OK
Date: Fri, 27 Jul 2012 05:12:32 GMT
Cache-Control: max-age=42
Content-Type: application/json
Content-Length: 27

{
    "text": "¡Hola Mundo!"
}
```

Como hemos visto por defecto se realiza una petición tipo GET. Si queremos realizar otro tipo de petición lo tendremos que indicar con el parámetro `-X` seguido del método a utilizar (POST, PUT, DELETE). Además, con la opción `-d` podemos añadir los parámetros de la petición. Los parámetros tendrán que ir entre comillas y en caso de indicar varios los separaremos con `&`. Por ejemplo, para realizar una petición tipo POST con dos parámetros:

```bash
$ curl -i -H "Accept: application/json" -X POST -d "name=javi&phone=800999800" http://localhost/users
```

De la misma forma podemos hacer una petición tipo PUT (para actualizar datos) o tipo DELETE (para eliminarlos). Por ejemplo:

```bash
$ curl -i -H "Accept: application/json" -X PUT -d "name=new-name" http://localhost/users/1

$ curl -i -H "Accept: application/json" -X DELETE http://localhost/users/1
```

Para añadir más de una cabecera tenemos que indicar varias veces la opción `-H`, por ejemplo:

```bash
$ curl -i -H "Accept: application/json" -H "Content-Type: application/json" http://localhost/resource

$ curl -i -H "Accept: application/xml" -H "Content-Type: application/xml" http://localhost/resource
```

Por ejemplo, si queremos realizar una petición tipo POST que envíe código JSON y que también espere la respuesta en JSON tendríamos que indicar ambas cabeceras y añadir el JSON que queramos en los parámetros con `-d` de forma normal:

```bash
$ curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X POST -d '{"title":"xyz","year":"xyz"}' http://localhost/resource
```


Como resumen, las opciones más importantes de `curl` son:

| Opción        | Descripción |
| ------------- | ----------- |
| `-i`          | Mostrar las cabeceras de respuesta |
| `-H "header"` | Configurar las cabeceras de la petición |
| `-X <type>`   | Indicar el método de la petición: POST, PUT, DELETE. Si no indicamos nada la petición será de tipo GET. |
| `-d "params"` | Añadir parámetros a la petición. Los parámetros tendrán que ir entre comillas "". Si queremos pasar varios parámetros utilizaremos como separador "`&`" |



<!-- ************************************ -->
## Plugins o extensiones

Además de `curl` también podemos utilizar otro tipo de programas para probar una API. En Firefox y Chrome podemos encontrar extensiones que nos facilitarán este tipo de trabajo. Por ejemplo, en Firefox podemos encontrar _HttpRequester_ (https://addons.mozilla.org/en-US/firefox/addon/httprequester/) o _Poster_ (https://addons.mozilla.org/en-US/firefox/addon/poster/). Una vez instalado los podremos abrir desde el menú `Herramientas`, y utilizarlo a través de una interfaz visual muy sencilla.

Para Chrome también podemos encontrar muchas extensiones si buscamos en su tienda (https://chrome.google.com/webstore/category/apps). Algunas opciones interesantes son "_Advanced REST client_" o "_Postman - REST Client_".






<!-- ************************************************************************-->
# Convertir modelos en Arrays o JSON

Laravel incluye métodos para transformar fácilmente el resultado obtenido de la consulta a un modelo de datos a formato JSON o a formato array. Esto es especialmente útil cuando estamos diseñando una API y queremos enviar los datos en formato JSON. Además, al realizar la transformación se incluirán los datos de las relaciones que se hayan cargado al hacer la consulta. Para realizar esta transformación simplemente tenemos que usar los métodos `toJson()` o `toArray()` sobre el resultado de la consulta:

```php
$user = User::first();

$arrayUsuario = $user->toArray();

$jsonUsuario = $user->toJson();
```

También podemos transformar una colección entera de datos a este formato, por ejemplo:

```php
$arrayUsuarios = User::all()->toArray();

$jsonUsuarios = User::all()->toJson();
```


En ocasiones nos interesará ocultar determinados atributos de nuestro modelo en la conversión a array o a JSON, como por ejemplo, el password o el identificador. Para hacer esto tenemos que definir el campo protegido `hidden` de nuestro modelo con el array de atributos a ocultar:

```php
class User extends Eloquent {

    protected $hidden = array('password');

    // O también podemos indicar solamente aquellos que queramos mostrar:
    //protected $visible = array('name', 'address');

}
```


<!-- ************************************************************************-->
## Respuestas especiales

Con lo que hemos visto en la sección anterior solo se realizaría la transformación a JSON, pero si queremos devolverlo como respuesta de una petición (por ejemplo, como respuesta a una petición de un método de una API), tendremos que utilizar el método `Response::json`, el cual además añadirá en la cabecera de la respuesta que los datos enviados están en formato JSON. Por ejemplo:


```php
$usuarios = User::all();

return Response::json( $usuarios );
```

Este método también puede recibir otro tipo de valores (como una variable, un array, etc.) y los transformará también a JSON para devolverlos como respuesta a la petición:

```php
return Response::json( array('name' => 'Steve', 'state' => 'CA') );
```

Si queremos especificar el código de la respuesta, por ejemplo cuando queremos indicar que ha sucedido algún error, podemos añadirlo como segundo parámetro, por ejemplo:


```php
return Response::json( array('error'=>true, 'msg'=>'Error al procesar la petición' ), 500 );
```

La lista completa de los códigos que podemos utilizar la podéis encontrar en:


http://es.wikipedia.org/wiki/Anexo:C%C3%B3digos_de_estado_HTTP







<!-- ************************************************************************-->
# Grupos de rutas con prefijo

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




<!-- ************************************************************************-->
# Autenticación HTTP básica

El sistema de autenticación HTTP básica proporciona una forma rápida de identificar a los usuarios sin necesidad de crear una página con un formulario de login. Este sistema, cuando se accede a través de la web, automáticamente mostrará una ventana emergente para solicitar los datos de acceso:

![](images/web_laravel/auth_basic.png)

Pero es más común su utilización para proteger las rutas de una API. En este caso las credenciales se tendrían que enviar en la cabecera de la petición.

Para proteger una ruta usando el sistema de autenticación básico simplemente tenemos que añadir el filtro llamado `auth.basic` a la ruta o grupo de rutas, de la forma:

```php
Route::get('profile', array('before' => 'auth.basic', function()
{
    // Zona de acceso restringido
}));
```

Por defecto este filtro utiliza la columna `email` de la tabla de usuarios para la validación. En caso de querer utilizar otro campo tendríamos que editar el fichero de filtros `app/filters.php` y modificar el filtro `auth.basic` para pasarme como primer parámetro el nombre de la columna. Por ejemplo, si queremos utilizar la columna `username` tendríamos que hacer:

```php
Route::filter('auth.basic', function()
{
    return Auth::basic('username');
});
```

Una vez superada la autenticación básica se crea la sesión del usuario y en cliente se almacenaría una cookie con el identificador de la sesión. 

Si no queremos que la sesión se mantenga (por ejemplo, en una API si queremos solicitar siempre el usuario y contraseña), simplemente tendremos que cambiar el filtro `auth.basic` por `auth.once`, el cual funciona exactamente igual pero sin persistir la sesión del usuario.

**Importante: ** Este filtro no viene implementado por defecto en el fichero de filtros `app/filters.php`. Así que tenemos que para utilizarlo tendréis que añadir el siguiente código: 

```php
Route::filter('auth.once', function()
{
    return Auth::onceBasic('username');
});
```


<!-- ************************************************************************-->
## Pruebas con _cURL_

Si intentamos acceder a una ruta protegida mediante autenticación básica utilizando los comando de _curl_ que hemos visto obtendremos el siguiente error:

```bash
HTTP/1.1 401 Unauthorized
```

Pero _curl_ permite también indicar el usuario y contraseña añadiendo el parámetro `-u` o también `--user` (equivalente):


```bash
$ curl --user username:password http://localhost/recurso
$ curl -u username:password http://localhost/recurso
```

> Si solamente indicamos el usuario (y no el password) se nos solicitará inmediatamente, y además al introducirlo no se verá escrito en la pantalla.

