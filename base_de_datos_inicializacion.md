# Inicialización de la base de datos (_Database Seeding_)

Laravel también facilita la inserción de datos iniciales en la base de datos. Esta opción es muy útil para tener datos de prueba cuando estamos desarrollando una web o para crear tablas que ya tienen que contener una serie de datos iniciales en producción. 

Los ficheros de "semillas" se encuentran en la carpeta `app/database/seeds`. En esta carpeta podemos añadir más ficheros PHP con clases que extiendan de `Seeder` para definir nuestros propios ficheros de "semillas". El nombre de los ficheros suele seguir el mismo patrón `<nombre-tabla>TableSeeder`, por ejemplo "`UserTableSeeder`".

Por defecto Laravel incluye el fichero `DatabaseSeeder`, que será el primero que se llamará. Dentro de su método `run` tendremos que añadir llamadas a nuestros ficheros de semillas para que se ejecuten. A continuación se incluye un ejemplo: 


```php
// Clase principal de semillas...
class DatabaseSeeder extends Seeder 
{
    public function run()
    {
        // Llamamos a nuestro fichero de semillas
        $this->call('UserTableSeeder');

        // Mostramos información por consola
        $this->command->info('User table seeded!');
    }
}

// Nuestro fichero de semillas
class UserTableSeeder extends Seeder 
{
    public function run()
    {
        // Dentro del método run es donde tenemos que inicializar la tabla

        // Borramos los datos de la tabla
        DB::table('users')->delete();

        // Añadimos una entrada a esta tabla
        User::create(array('email' => 'foo@bar.com'));
    }
}
```

> Otra opción es crear métodos dentro de la clase principal `DatabaseSeeder` que se llamen desde su método `run`.

Como se puede ver en el ejemplo en general tendremos que eliminar primero los datos de la tabla en cuestión y posteriormente añadir los datos. Para insertar datos en una tabla podemos utilizar el método que se usa en el ejemplo o alguna de las otras opciones que se verán en la sección sobre "modelos de datos".


Una vez definidos los ficheros de semillas, cuando queramos ejecutarlos para rellenar de datos la base de datos tendremos que usar el siguiente comando de Artisan:

```bash
php artisan db:seed
```


