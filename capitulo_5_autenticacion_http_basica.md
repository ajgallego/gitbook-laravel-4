
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
