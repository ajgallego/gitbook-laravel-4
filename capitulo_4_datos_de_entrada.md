# Datos de entrada

Laravel facilita el acceso a los datos de entrada del usuario a través de solo unos poco métodos. No importa el tipo de petición que se haya realizado (POST, GET, PUT, DELETE), si los datos son de un formulario o si se han añadido a la _query string_, en todos los casos se obtendrán de la misma forma: 

```php
$name = Input::get('name');

// También podemos especificar un valor por defecto como segundo parámetro
$name = Input::get('name', 'Laura');
```

Si lo necesitamos podemos comprobar si un determinado valor existe en los datos de entrada:

```php
if (Input::has('name'))
{
    //
}
```

O también podemos obtener todos los datos de entrada a la vez (en un array) o solo algunos de ellos: 

```php
// Obtener todos: 
$input = Input::all();

// Obtener solo los campos indicados: 
$input = Input::only('username', 'password');

// Obtener todos excepto los indicados: 
$input = Input::except('credit_card');
```

Si la entrada proviene de un _input_ tipo array de un formulario (por ejemplo una lista de _checkbox_), si queremos podremos utilizar la siguiente notación con puntos para acceder a los elementos del array de entrada: 

```php
$input = Input::get('products.0.name');
```

Si la entrada está en formato JSON (por ejemplo cuando nos comunicamos a través de una API es bastante común) también podremos acceder a los diferentes campos de los datos de entrada de forma normal (con `Input::get`).





<!-- ************************************ -->
## Ficheros de entrada 

Laravel facilita una serie de clases para trabajar con los ficheros de entrada. Por ejemplo para obtener un fichero que se ha enviado en el campo con nombre `photo` y guardarlo en una variable, tenemos que hacer: 

```php
$file = Input::file('photo');
```

Si queremos podemos comprobar si un determinado campo tiene un fichero asignado: 

```php
if (Input::hasFile('photo'))
{
    //
}
```

El objeto que recuperamos con `Input::file` es una instancia de la clase `Symfony\Component\HttpFoundation\File\UploadedFile`, la cual extiende la clase de PHP `SplFileInfo` (http://php.net/manual/es/class.splfileinfo.php), por lo tanto, tendremos muchos métodos que podemos utilizar para obtener datos del fichero o para gestionarlo. 


Por ejemplo, para comprobar si el fichero que se ha subido es válido: 

```php
if (Input::file('photo')->isValid())
{
    //
}
```


O para mover el fichero de entrada a una ruta determinada: 

```php
// Mover el fichero a la ruta conservando el nombre original: 
Input::file('photo')->move($destinationPath);

// Mover el fichero a la ruta con un nuevo nombre:
Input::file('photo')->move($destinationPath, $fileName);
```


Otros métodos que podemos utilizar para recuperar información del fichero son: 

```php
// Obtener la ruta:
$path = Input::file('photo')->getRealPath();

// Obtener el nombre original:
$name = Input::file('photo')->getClientOriginalName();

// Obtener la extensión: 
$extension = Input::file('photo')->getClientOriginalExtension();

// Obtener el tamaño: 
$size = Input::file('photo')->getSize();

// Obtener el MIME Type:
$mime = Input::file('photo')->getMimeType();
```





