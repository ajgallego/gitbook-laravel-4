<!-- *********************************** -->
# Configuración de la Base de Datos

Como ejemplo vamos a configurar el acceso a una base de datos tipo MySQL. 
Editamos `app/config/database.php` y actualizamos los campos _database_, _username_ y _password_ de la sección de mysql con los valores apropiados para acceder a nuestra base de datos:


```php
'mysql' => array(
    'driver'    => 'mysql',
    'host'      => 'localhost',
    'database'  => 'laravel',       // Nombre de la base de datos
    'username'  => 'root',          // Usuario de acceso a la base de datos
    'password'  => '',              // Contraseña de acceso
    'charset'   => 'utf8',
    'collation' => 'utf8_unicode_ci',
    'prefix'    => '',
),
```

Para crear la base de datos que vamos a utilizar en MySQL podemos utilizar la herramienta _PHPMyAdmin_ que se ha instalado con el paquete XAMPP. Para esto accedemos a la ruta:


```bash
http://localhost/phpmyadmin
```

La cual nos mostrará un panel para la gestión de las bases de datos de MySQL, que nos permite, además de realizar cualquier tipo de consulta SQL, crear nuevas bases de datos o tablas, e insertar, modificar o eliminar los datos directamente. En nuestro caso apretamos en la pestaña "Bases de datos" y creamos una nueva base de datos llamada "laravel" (igual que hemos indicado antes en el fichero de configuración de Laravel).

A continuación vamos a crear la tabla de migraciones (en la siguiente sección veremos que es esto) en la nueva base de datos ejecutando el siguiente comando de Artisan:


```bash
php artisan migrate:install
```

Si ahora vamos al navegador y actualizamos nuestra base de datos en PHPMyAdmin podremos ver que se nos habrá creado la tabla `migrations`. Con esto ya tenemos configurada la base de datos y el acceso a la misma. En las siguientes secciones veremos como añadir tablas a esta base de datos y posteriormente como realizar consultas. 


