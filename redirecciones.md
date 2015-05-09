<!-- ************************************************************************-->
# Redirecciones

Como respuesta a una petición también podemos devolver una redirección. Esta opción será interesante cuando, por ejemplo, el usuario no esté _logueado_ y lo queramos redirigir al formulario de login, o cuando se produzca un error en la validación de una petición y queramos redirigir a otra ruta. Para esto simplemente tenemos que utilizar:


```php
return Redirect::to('user/login');

// O redirigir a un método de un controlador:

return Redirect::action('HomeController@index');

// Si queremos añadir parámetros al llamar al método podemos hacer:
return Redirect::action('UserController@profile', array(1));
return Redirect::action('UserController@profile', array('user' => 1));
```

Las redirecciones también se suelen utilizar tras obtener algún error en la validación del formulario. En este caso, para que al mostrar el formulario con los errores producidos podamos añadir los datos que había escrito el usuario tendremos que añadir:

```php
return Redirect::to('form')->withInput();

// O para reenviar los datos de entrada excepto algunos:
return Redirect::to('form')->withInput(Input::except('password'));
```
