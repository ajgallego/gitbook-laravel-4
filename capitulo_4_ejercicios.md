
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

