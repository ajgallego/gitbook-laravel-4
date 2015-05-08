# Laravel 4 - Datos de entrada y Control de usuarios



<!-- ************************************************************************-->
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








<!-- ************************************************************************-->
# Control de usuarios

Laravel también incluye una seríe de métodos y clases que harán que la implementación del control de usuarios sea muy sencilla. De hecho, casi todo el trabajo ya está hecho. La configuración del sistema de autenticación se puede encontrar en el fichero `app/config/auth.php`, el cual contiene varias opciones (bien documentadas) que nos permitirán, por ejemplo: cambiar el sistema de autenticación (que por defecto es a través de Eloquent), cambiar el modelo de datos usado para los usuarios (por defecto será `User`) y cambiar la tabla de usuarios (que por defecto será `users`). Si vamos a utilizar estos valores no será necesario que realicemos ninguna configuración. 

Por defecto, al crear un nuevo proyecto de Laravel, ya se incluye el modelo de usuario en la carpeta `app/models` con toda la configuración necesaria también puesta. 

El único trabajo que tendremos que realizar nosotros será crear la migración de la tabla `users`, que por defecto tendrá que ser como la siguiente: 

```php
Schema::create('users', function($table) {
        $table->increments('id');
        $table->string('username', 16)->unique();
        $table->string('password', 64);  // Mínimo 60
        $table->rememberToken();
        $table->timestamps();
});
```

Como se puede ver el nombre de la tabla a crear es `users`, con un índice `id` autoincremental, y los campos de `username`, `password`. Hemos marcado el campo _username_ para que sea único (no se permitirá que varios usuarios tengan el mismo _username_) y el _password_ con una longitud de 64 (Laravel indica que como mínimo tiene que ser de 60 caractereres). Además añadimos los _timestamps_ que usa Eloquent automáticamente y el `remember_token` que utiliza el sistema de autenticación para recordar la sesión del usuario. 

> Podemos añadir todos los campos que queramos a esta tabla, para por ejemplo guardar el nombre y apellidos del usuario, su dirección o teléfono, etc. 




<!-- ************************************ -->
## Almacenar un nuevo usuario

A la hora de almacenar un nuevo usuario en la tabla de usuarios la única precaución que tenemos que llevar es cifrar el password mediante el siguiente método: 

```php
$password_cifrado = Hash::make('mi-super-password');
```

Por ejemplo, si obtenemos los datos del nuevo usuario de un formulario podríamos utilizar el siguiente código para crear la instancia en la base de datos: 


```php
public function postCreate()
{
    $user = new User;
    $user->username = Input::get('username');
    $user->password = Hash::make( Input::get('password') );
    $user->save();
}
```



<!-- ************************************* -->
## Atenticar usuarios

Para comprobar los datos de un usuario y validarlo en el sistema tenemos que utilizar el método `Auth::attempt` como en el siguiente ejemplo: 

```php
if (Auth::attempt(array('username' => $username, 'password' => $password)))
{
    return Redirect::intended('url-a-redireccionar');
}
```

Este método recibe un array de credenciales y las valida comparándolas con los datos en la base de datos de usuarios, si existe un usuario con dichas credenciales devolverá _true_ y se creará la sesión del usuario; en caso contrario el método devolverá _false_. 

El campo "_username_" en realidad no es obligatorio que tenga ese nombre, sino que podría ser cualquier otro, por ejemplo "_email_"; solamente tiene que coincidir con el campo que se utilice en la base de datos para almacenarlo. 

El método utilizado en el ejemplo como respuesta, `Redirect::intended`, realiza una redirección a la URL que estaba intentando acceder el usuario, en caso de que hubiese accedido directamente al formulario de login se utilizará la dirección pasada por parámetro (en este caso `url-a-redireccionar`). 

A continuación se incluye un ejemplo de la validación de un usuario. El controlador `UserController` contiene dos métodos para realizar esta validación. El método `getLogin` se llamará cuando se realiza una petición tipo GET a la pantalla de _login_. Y el método `postLogin` cuando se envían los datos por POST del formulario de _login_: 


```php
class UserController extends BaseController 
{
    public function getLogin()
    {
        return View::make('user.login');
    }
    
    public function postLogin()
    {
        $username = Input::get('username');
        $password = Input::get('password');
    
        if (Auth::attempt(['username' => $username, 'password' => $password]))
        {
            return Redirect::intended('url-a-redireccionar');
        }
    
        return Redirect::action('UserController@getLogin')
            ->withInput(Input::except('password'))
            ->withErrors('Error en la validación del usuario');
    }
}
```

Como se puede ver, en el método `postLogin`, si no se realiza la validación correctamente se redirige otra vez al método `getLogin` con los errores que se hayan producido y la entrada del usuario. 



El método `attempt` además permite pasar un segundo parámetro para indicar que el usuario será recordado hasta que se cierre la sesión manualmente. Es decir, aunque se cierre el navegador y pasen varios días el usuario seguiría estando autorizado. Para utilizar esta opción es necesario que la tabla de usuarios tenga el campo `remember_token` como se ha especificado en la introducción. A continuación se incluye un ejemplo de uso: 

```php
if (Auth::attempt(array('email' => $email, 'password' => $password), true))
{
    // Usuario validado con la opción "remember me"
}
```

Además de usuario y contraseña, en el método `attempt` podemos pasar otra serie de parámetros en las credenciales, las cuales se utilizarán para buscar un usuario que cumpla todos esos requisitos. Esta opción es muy útil para buscar usuarios que hayan validado su cuenta de email o que hayan aceptado los términos y condiciones, por ejemplo: 

```php
if (Auth::attempt(array('email' => $email, 'password' => $password, 'active' => 1)))
{
    // Usuario validado 
}
```



<!-- ************************************* -->
## Comprobar si un usuario está autenticado

Para comprobar si el usuario actual se ha autenticado en la aplicación podemos utilizar el método `Auth::check()` de la forma: 

```php
if (Auth::check())
{
    // El usuario está correctamente autenticado
}
```


<!-- ************************************* -->
## Acceder a los datos del usuario atenticado

Una vez que el usuario está autenticado podemos acceder a los datos del mismo a través del método `Auth::user()`, por ejemplo: 

```php
$email = Auth::user()->email;
```




<!-- ************************************* -->
## Cerrar la sesión

Para cerra la sesión del usuario actualmente autenticado tenemos que utilizar el método: 

```php
Auth::logout();
```

Posteriormente podremos hacer una redirección a una página principal para usuarios no autenticados. 




<!-- ************************************* -->
## Proteger rutas

El sistema de autenticación de Laravel también incorpora una serie de filtros (ver el fichero `app/filters.php`) para comprobar que el usuario que accede a una determinada ruta o grupo de rutas esté autenticado. Para utilizar este filtro tenemos que editar el fichero `app/routes.php` y modificar las rutas o grupos de rutas que queramos proteger, por ejemplo: 


```php
Route::get('admin/catalog', array('before' => 'auth', function()
{
    // Solo se permite el acceso a usuarios autenticados
}));

// O por ejemplo para proteger un grupo de rutas: 
Route::group(array('before' => 'auth'), function()
{
    Route::get('catalog',        'CatalogController@getIndex');
    Route::get('catalog/create', 'CatalogController@getCreate');
});
```







<!-- ************************************************************************-->
<!-- ************************************************************************-->
<!-- ************************************************************************-->
<!-- ************************************************************************-->



<!-- ************************************************************************-->
# Ejercicios

En los ejercicios de esta sección vamos a completar el proyecto del videoclub terminando el procesamiento de los formularios y añadiendo el sistema de autenticación de usuarios.




<!-- ************************************ -->
## Ejercicio 1 - Migración de la tabla usuarios (0.5 puntos)

En primer lugar vamos a crear la migración para almacenar los usuarios que tendrán acceso a la plataforma de gestión del videoclub. Para esto utilizaremos el comando de artisan para generar una migración con el nombre `create_users_table` para la tabla `users`. Una vez creada editaremos el fichero generado para añadir todos los campos necesarios a la tabla, estos son:  


| Campo          | Tipo                   | Modificador |
| -------------- | ---------------------- | -- |
| id             | Autoincremental        |    |
| username       | String de longitud 16  | _unique_   |
| password       | String de longitud 64  |    |
| remember_token | Campo remember_token   |    |
| timestamps     | Timestamps de Eloquent |    |


> Recuerda que en el método `down` de la migración tienes que deshacer los cambios que has hecho en el método `up`, en este caso sería eliminar la tabla.

Por último usamos el comando de artisan que añade las nuevas migraciones y comprobamos en PHPMyAdmin que la tabla se ha creado correctamente con los campos que le hemos indicado. 




<!-- ************************************ -->
## Ejercicio 2 - _Seeder_ de usuarios (0.5 puntos)


Ahora vamos a proceder a rellenar la tabla `users` con los datos iniciales. Para esto editamos el fichero de semillas situado en `app/database/seeds/DatabaseSeeder.php` y seguiremos los siguientes pasos: 

* Creamos un método privado (dentro de la misma clase) llamado `seedUsers()` que se tendrá que llamar desde el método `run` de la forma: 
<br/>
```php
public function run()
{
    // ... Llamada al seed del catálogo

    self::seedUsers();
    $this->command->info('Tabla usuarios inicializada con datos!');
}
```
* Dentro del nuevo método `seedUsers()` realizamos las siguientes acciones: 
  * En primer lugar borramos el contenido de la tabla `users`. 
  * Y a continuación creamos un par de usuarios de prueba. Recuerda que para guardar el _password_ es necesario encriptarlo manualmente usando el método `Hash::make`.


Por último tendremos que ejecutar el comando de artisan que procesa las semillas y una vez realizado esto comprobamos en PHPMyAdmin que se han añadido los usuarios a la tabla _users_. 




<!-- ************************************ -->
## Ejercicio 3 - Sistema de autenticación (1 punto)

En este ejercicio vamos a completar el sistema de autenticación. 

En primer lugar editamos el fichero `routes.php` y realizamos las siguientes acciones: 

* Añadimos una nueva ruta tipo POST para la url `login` pero que apunte al método `postLogin` del controlador `UserController`.
* Añadimos un filtro de tipo grupo que aplique el filtro `auth` antes de la ejecución de las rutas. Este filtro tendrá que proteger todas las rutas menos a la raíz `/` y a la de `login`.

> Nota: El filtro `auth` ya está definido en Laravel, únicamente tendréis que modificar el fichero de rutas para indicar las rutas que queráis proteger (revisar la sección de teoría "Proteger rutas"). 


A continuación abrimos el controlador `UserController` para completar los siguientes puntos: 

* En el método `getHome` tendremos que comprobar si es un usuario validado o no. En caso de estar autenticado se le rediccionará a la ruta `catalog` y en caso contrario a `login`

* En el método `getLogout` ejecutamos el método para cerrar la sesión actual antes de realizar la redirección. 

* Añadimos el método `postLogin` el cual tendrá que recoger los datos enviados por el formulario y comprobar si las credenciales del usuario son válidas. En caso de que los datos del usuario sean correctos se le deberá redirigir a la ruta `catalog`, y en caso de haber algún error se le volverá a mostrar el formulario de `login`. 

> Nota: de momento en caso de error no se mostrará nada.





<!-- ************************************ -->
## Ejercicio 4 - Añadir y editar películas (1 punto)

En primer lugar vamos a añadir las rutas que nos van a hacer falta para recoger los datos al enviar los formularios. Para esto editamos el fichero de rutas y vamos a añadir dos rutas (también protegidas por el filtro `auth`): 

* Una ruta de tipo POST para la url `catalog/create` que apuntará al método `postCreate` del controlador `CatalogController`.
* Y otra ruta tipo PUT para la url `catalog/edit/{id}` que apuntará al método `putEdit` del controlador `CatalogController`. 

En ambos casos indicaremos que se aplique el filtro `csrf` antes de la ejecución de la ruta. 

A continuación vamos a editar la vista `catalog/edit.blade.php` con los siguientes cambios:

* Cambiamos al método de apertura del formulario por `Form::open(array('method' => 'put'))`, para que al enviar los datos se envíen por PUT. 
* Tenemos que modificar todos los inputs para que en lugar de poner la cadena vacía como valor del campo (2º parámetro de `Form::text`) se ponga el valor correspondiente de la película. Por ejemplo en el primer input tendríamos que poner `$pelicula->title`. 

Por último tenemos que actualizar el controlador `CatalogController` con los dos nuevos métodos: 

* En el método `postCreate` crearemos una nueva instancia del modelo `Movie`, asignaremos todos los campos (_title, year, director, poster_ y _synopsis_) y los guardaremos. Por último, después de guardar, haremos una redirección a la ruta `catalog`.
* En el método `putEdit` buscaremos la película con el identificador pasado por parámetro, actualizaremos sus campos y los guardaremos. Por último realizaremos una redirección a la pantalla de vista detalle de la película editada. 



> Nota: de momento en caso de error no se mostrará nada.





