# Migraciones

Las migraciones son un sistema de control de versiones para bases de datos. Permiten que un equipo trabaje sobre una base de datos añadiendo y modificando campos, manteniendo un histórico de los cambios realizados y del estado actual de la base de datos. Las migraciones se utilizan de forma conjunta con la herramienta _Schema builder_ (que veremos en la siguiente sección) para gestionar el esquema de base de datos de la aplicación. 

La forma de funcionar de las migraciones es crear ficheros (PHP) con la descripción de la tabla a crear y posteriormente, si se quiere modificar dicha tabla se añadiría una nueva migración (un nuevo fichero PHP) con los campos a modificar. Artisan incluye comando para crear migraciones, para ejecutar las migraciones o para hacer _rollback_ de las mismas (volver atrás). 

Para crear una nueva migración se utiliza el comando de Artisan `migrate:make`, al cual le pasaremos el nombre del fichero a crear y el nombre de la tabla: 

```bash
php artisan migrate:make create_users_table --create=users
```

Esto nos creará un fichero de migración en la carpeta `app/database/migrations` con el nombre `<TIMESTAMP>_create_users_table.php`. Al añadir un _timestamp_ a las migraciones el sistema sabe el orden en el que tiene que ejecutar (o deshacer) las mismas. 

Si lo que queremos es añadir una migración que actualice los campos de una tabla existente tendremos que ejecutar el siguiente comando: 

```bash
php artisan migrate:make add_votes_to_user_table --table=users
```

En este caso se creará también un fichero en la misma carpeta, con el nombre `<TIMESTAMP>_add_votes_to_user_table.php` pero preparado para modificar los campos de dicha tabla. 

Por defecto, al indicar el nombre del fichero de migraciones se suele seguir siempre el mismo patrón (aunque el realidad el nombre es libre). Si es una migración que crea una tabla el nombre tendrá que ser `create_<table-name>_table` y si es una migración que modifica una tabla será `<action>_to_<table-name>_table`.


El fichero generado para una migración siempre tiene una estructura similar a la siguiente: 

```php
class CreateUsersTable extends Migration 
{
	/**
	 * Run the migrations.
	 * @return void
	 */
	public function up()
	{
		//
	}

	/**
	 * Reverse the migrations.
	 * @return void
	 */
	public function down()
	{
		//
	}
}
```

En el método `up` es donde tendremos crear o modificar la tabla, y en el método `down` tendremos que deshacer los cambios que se hagan en el `up` (eliminar la tabla o eliminar el campo que se haya añadido). Esto nos permitirá poder ir añadiendo y eliminando cambios sobre la base de datos y tener un control o histórico de los mismos. 


Después de crear una migración y de definir la tabla que tiene que crear o los campos que tiene que modificar (en la siguiente sección veremos como especificar esto) tenemos que lanzar la migración con el siguiente comando: 

```bash
php artisan migrate
```

Este comando aplicará la migración sobre la base de datos. Posteriormente en caso de que queramos deshacer estos cambios (los últimos) podremos ejecutar: 

```bash
php artisan migrate:rollback

# O si queremos deshacer todas las migraciones
php artisan migrate:reset
```

Un comando interesante cuando estamos desarrollando un nuevo sitio web es `migrate:refresh`, el cual deshará todos los cambios y volver a aplicar las migraciones: 

```bash
php artisan migrate:refresh
```

