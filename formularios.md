<!-- ************************************************************************-->
# Formularios

Laravel también incluye métodos que nos ayudan para la generación de formularios en nuestro código. En esta sección vamos a ver las opciones más básicas: como abrir y cerrar formularios, y como añadir etiquetas y algunos otros tipos de campos.

> Nota: Los métodos de Laravel únicamente generan de las etiquetas HTML del formulario, pero no aplican ningún estilo ni clase CSS.

Para abrir y cerrar un formulario que apunte a la URL actual y utilice el método POST utilizaríamos el siguiente código:

```php
{{ Form::open() }}
    //
{{ Form::close() }}
```

Si queremos cambiar la URL de envío de datos se lo podemos añadir por parámetro:

```php
{{ Form::open(array('url' => 'foo/bar')) }}
```

Por defecto el formulario se enviará por POST, pero si queremos cambiar el método de envío también lo podemos especificar en los parámetros:

```php
{{ Form::open(array('url' => 'foo/bar', 'method' => 'put')) }}
```

> Nota: el envío de formularios en realidad solamente soporta los métodos POST y GET, por lo que si indicamos que se utilice PUT o DELETE el sistema de forma automática añadirá un campo oculto para gestionarlo.


Laravel también permite especificar la URL de envío del formulario indicando la acción del controlador de la forma:

```php
{{ Form::open(array('action' => 'Controller@method')) }}

// También podemos pasarle parámetros a la ruta
{{ Form::open(array('action' => array('Controller@method', $user->id))) }}
```

Además, si queremos que el formulario pueda enviar archivos tendremos que especificarlo añadiendo el atributo `files` con valor `true` en los parámetros:

```php
{{ Form::open(array('url' => 'foo/bar', 'files' => true)) }}
```


## Protección contra CSRF

El CSRF (del inglés _Cross-site request forgery_ o falsificación de petición en sitios cruzados) es un tipo de exploit malicioso de un sitio web en el que comandos no autorizados son transmitidos por un usuario en el cual el sitio web confía.

Laravel proporciona una forma fácil de protegernos de este tipo de ataques. De forma automática, al crear un formulario que utilice el método POST, PUT o DELETE, se le añadirá un campo oculto con una clave utilizada para la validación de la sesión del usuario. Lo único que nos faltaría es añadir para esa ruta (la del envío de la petición POST, PUT o DELETE) que tiene que aplicar el filtro `csrf`, de la forma:

```php
Route::post('profile', array('before' => 'csrf', 'uses' => 'UserController@postProfile'));
```


<!-- *********************************** -->
## Etiquetas

Para crear una etiqueta simplemente tenemos que usar el constructor `Form::label`, el cual recibe como primer parámetro el atributo `for` de la etiqueta (para asociar una etiqueta con un _input_) y como segundo parámetro el texto a mostrar:

```php
{{ Form::label('email', 'Dirección de E-Mail') }}
```

Además podemos añadir parámetros extra a las etiqueta pasando un array asociativo como tercer parámetro. Esto nos permitirá especificar, por ejemplo las clases a aplicar sobre la etiqueta, estilos, etc.

```php
{{ Form::label('email', 'E-Mail Address', array('class' => 'awesome')) }}
```




<!-- *********************************** -->
## Campos de texto, textarea, password y ocultos

Para crear un campo de texto usamos el constructor `Form::text`:

```php
{{ Form::text('username') }}

// También podemos especificar un valor por defecto:
{{ Form::text('email', 'example@gmail.com') }}
```

A continuación se incluyen más ejemplos para crear campos de tipo _textarea_, _password_ y _hidden_:

```php
{{ Form::textarea('textarea') }}

{{ Form::password('password') }}

{{ Form::hidden('hidden') }}
```

También podemos crear otro tipo de _inputs_ (como _email_, _number_, etc.). En general recibirán como segundo parámetro el valor por defecto y como tercer parámetro el array de atributos del mismo (el cual nos permitirá indicar la clase a aplicar, estilos, etc.).

```php
{{ Form::email($name, $value = null, $attributes = array()) }}
```



<!-- *********************************** -->
## Checkbox y Radio buttons

Para crear campos tipo _checkbox_ o tipo _radio button_ utilizamos los siguientes constructores:

```php
{{ Form::checkbox('name', 'value') }}

{{ Form::radio('name', 'value') }}
```

Además podemos incluir un tercer parámetro para indicar que el campo tiene que estar seleccionado por defecto.

```php
{{ Form::checkbox('name', 'value', true) }}

{{ Form::radio('name', 'value', true) }}
```



<!-- *********************************** -->
## Ficheros

Para generar un _input_ para subir ficheros utilizamos el siguiente constructor:

```php
{{ Form::file('image') }}
```
> En este caso tendremos que crear el formulario especificando la opción `files` con valor _true_ para permitir el envío de archivos.




<!-- *********************************** -->
## Listas desplegables

Para crear un desplegable tipo _select_ utilizamos el constructor `Form::select` pasándole como primer parámetro el nombre del campo y como segundo el array de valores a mostrar:

```php
{{ Form::select('size', array('L' => 'Large', 'S' => 'Small')) }}

// Para seleccionar un valor por defecto lo añadimos como tercer parámetro:
{{ Form::select('size', array('L' => 'Large', 'S' => 'Small'), 'S') }}
```



<!-- *********************************** -->
## Botones

A continuación se incluyen ejemplo para crear un botón tipo `submit` y otro botón normal:

```php
{{ Form::submit('Enviar') }}

{{ Form::button('Enviar') }}
```

Si por ejemplo queremos aplicarle alguna clase lo podemos añadir como un array en el segundo parámetro:

```php
{{ Form::button('Enviar', array('class' => 'btn btn-primary') ) }}
```


