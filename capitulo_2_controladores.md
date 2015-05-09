<!-- ************************************************************************-->
# Controladores

Hasta el momento hemos visto solamente como devolver una cadena para una ruta y como asociar una vista a una ruta directamente en el fichero de rutas. Pero en general la forma recomendable de trabajar será asociar dichas rutas a un método de un controlador. Esto nos permitirá separar mucho mejor el código y crear clases (controladores) que agrupen toda la funcionalidad de un determinado recurso. Por ejemplo, podemos crear un controlador para gestionar toda la lógica asociada al control de usuarios o cualquier otro tipo de recurso. Como ya hemos visto antes, los controladores son el punto de entrada de las peticiones de los usuarios y son los que deben contener toda la lógica asociada al procesamiento de una petición, encargándose de realizar las consultas necesarias a la base de datos, de preparar los datos y de llamar a la vista correspondiente con dichos datos.

Los controladores se almacenan en ficheros PHP en la carpeta `app/controllers`, sin embargo también podemos crear sub-carpetas dentro de esta carpeta para organizarnos mejor. En este caso, la estructura de carpetas que creemos no tendrá nada que ver con la ruta asociada y, de hecho, a la hora de hacer referencia al controlador tampoco hará falta indicar la carpeta donde se encuentra.

A continuación se incluye un ejemplo básico de un controlador almacenado en el fichero `app/controllers/UserController.php`:


```php
class UserController extends BaseController
{
    /**
     * Mostrar información de un usuario.
     */
    public function showProfile($id)
    {
        $user = User::find($id);

        return View::make('user.profile', array('user' => $user));
    }
}
```

Como se puede ver el controlador de ejemplo extiende de la clase `BaseController`. Esta clase, que también está almacenada en la carpeta `app/controllers`, nos sirve para centralizar toda la lógica que vayan a utilizar de forma compartida los controladores de nuestra aplicación. Si abrimos esta clase veremos que a su vez extiende de la clase `Controller`, la cual se utiliza para definir un controlador en Laravel. Por defecto solo incluye un método, pero podemos añadir en la misma todos los métodos que necesitemos.

En el código de ejemplo, el método `showProfile($id)` lo único que realiza es obtener los datos de un usuario, generar la vista `user.profile` a partir de los datos obtenidos y devolverla como valor de retorno para que se muestre por pantalla.

Una vez definido un controlador ya podemos asociarlo a una ruta. Para esto tenemos que modificar el fichero de rutas `routes.php` de la forma:

```php
Route::get('user/{id}', 'UserController@showProfile');
```

Como se puede ver, en lugar de pasar una función como segundo parámetro, tenemos que escribir una cadena que contenga el nombre del controlador, seguido de una arroba `@` y del nombre del método que queremos asociar. No es necesario añadir nada más, ni la carpeta en la que se encuentra el controlador, ni los parámetros que recibe el método en cuestión, todo esto se hace de forma automática.


Además, si queremos generar la URL que apunte a una acción de un controlador, Laravel nos facilita los siguientes métodos (equivalentes):


```php
$url = URL::action('FooController@method');

$url = action('FooController@method');
```





<!-- ************************************************************************-->
## Definir el _layout_ a utilizar por un controlador

Una opción interesante de los controladores es poder indicar el _layout_ que van a utilizar las vistas que se devuelvan. De esta forma se puede desacoplar las vistas de los _layouts_, permitiendo que una misma vista se puede renderizar con diferentes _layouts_. A continuación se incluye un ejemplo:


```php
class UserController extends BaseController
{
    /**
     * Layout a utilizizar en las respuestas
     */
    protected $layout = 'layouts.master';

    /**
     * Mostrar información de un usuario.
     */
    public function showProfile()
    {
        $this->layout->content = View::make('user.profile');
    }
}
```

Como se puede ver, solo tenemos que establecer el valor de la variable protegida `layout` con el _layout_ a utilizar y posteriormente, en vez de devolver la vista, asignarla a una variable del _layout_.




<!-- ************************************ -->
## Controladores implícitos

Laravel también permite definir fácilmente la creación de controladores como recursos que capturen todas las rutas de un derminado dominio. Por ejemplo, capturar todas las consultas que se realicen a la URL "users" o "users" seguido de cualquier cosa (por ejemplo "users/profile"). Para esto en primer lugar tenemos que definir la ruta en el fichero de rutas usando `Route::controller` de la forma:

```php
Route::controller('users', 'UserController');
```

Esto quiere decir que todas las peticiones realizadas a la ruta "users" o subrutas de "users" se redirigirán al controlador `UserController`. Además se capturarán las peticiones de cualquier tipo, ya sean GET o POST, a dichas rutas. Para gestionar estas rutas en el controlador tenemos que seguir un patrón a la hora de definir el nombre de los métodos: primero tendremos que poner el tipo de petición y después la sub-ruta a la que debe de responder. Por ejemplo, para gestionar las peticiones tipo GET a la URL "users/profile" tendremos que crear el método "getProfile". La única excepción a este caso es "Index" que se referirá a las peticiones a la ruta raíz, por ejemplo "getIndex" gestionará las peticiones GET a "users". A continuación se incluye un ejemplo:

```php
class UserController extends BaseController
{
    public function getIndex()
    {
        //
    }

    public function postProfile()
    {
        //
    }

    public function anyLogin()
    {
        //
    }
}
```

Además, si queremos crear rutas con varias palabras lo podemos hacer usando la notación "_CamelCase_" en el nombre del método. Por ejemplo el método "getAdminProfile" será parseado a la ruta "users/admin-profile".

También podemos definir un método especial que capture las todas las peticiones "perdidas" o no capturadas por el resto de métodos. Para esto simplemente tenemos que definir un método con el nombre `missingMethod` que recibirá por parámetros la ruta y los parámetros de la petición:


```php
public function missingMethod($parameters = array())
{
    //
}
```


