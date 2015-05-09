

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

