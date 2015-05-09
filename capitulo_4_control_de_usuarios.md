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



