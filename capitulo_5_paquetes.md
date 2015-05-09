
<!-- ************************************************************************-->
# Instalación de paquetes

Además de todas las utilidades que incorpora Laravel y que podemos utilizar directamente sin instalar nada más, también nos permite añadir de forma muy sencilla paquetes para complementar su funcionalidad.

Para instalar un paquete tenemos dos opciones, utilizar el comando de `composer`:

```bash
sudo composer require <nombre-del-paquete-a-instalar>
```

O editar el fichero `composer.json` de la raíz de nuestro proyecto, añadir el paquete en su sección "`require`" y  por último ejecutar el comando:


```bash
sudo composer update
```

Esta última opción es más interesante ya que nos permitirá una mayor configuración, por ejemplo, indicar la versión del paquete o repositorio, etc.

La lista de todos los paquetes disponibles la podemos encontrar en "https://packagist.org", en la cual permite realizar búsquedas, ver el número de instalaciones de un paquete, etc.

Algunos de los paquetes más utilizados en Laravel son:

| Nombre        | Descripción                     | Url                        |
| ------------- | ------------------------------- | -------------------------- |
| L4 Generators | Auto generación de código       | https://github.com/JeffreyWay/Laravel-4-Generators |
| Debugbar      | Barra de depuración de Laravel  | https://github.com/barryvdh/laravel-debugbar |
| L4 IDE Helper | Helper para IDEs                | https://github.com/barryvdh/laravel-ide-helper |
| Confide       | Autenticación                   | https://github.com/Zizaco/confide |
| Entrust       | Roles, permisos                 | https://github.com/Zizaco/entrust |
| Sentry        | Autenticación, roles, permisos  | https://github.com/cartalyst/sentry |
| Ardent        | Modelos auto-validados          | https://github.com/laravelbook/ardent |
| MongoDB       | Extensión para soportar MongoDB | https://github.com/jenssegers/laravel-mongodb |
| Notification  | Notificaciones                  | https://github.com/edvinaskrucas/notification |
| Former        | Automatización de formularios   | https://github.com/anahkiasen/former |
| Image         | Manipulación de imágenes        | https://github.com/Intervention/image |
| Stapler       | Manipulación de ficheros        | https://github.com/CodeSleeve/laravel-stapler |
| User Agent    | Obtener info. del _user agent_  | https://github.com/jenssegers/laravel-agent |
| OAuth 4 Laravel | OAuth para Laravel            | https://github.com/artdarek/oauth-4-laravel |
| Rocketeer     | Despliegue en servidor con Git  | https://github.com/Anahkiasen/rocketeer |
| Sitemap       | Genearción del Sitemap          | https://github.com/RoumenDamianoff/laravel-sitemap |
| Excel         | Trabajar con Excel y csv        | https://github.com/Maatwebsite/Laravel-Excel |
| DOMPdf        | Trabajar con PDF                | https://github.com/barryvdh/laravel-dompdf |



Una vez añadido el paquete tendremos que modificar también el fichero de configuración `app/config/app.php`, en su sección `providers` y `aliases`, para que la plataforma encuentre el paquete que acabamos de añadir. Los detalles de configuración en general se encuentran indicados en la web o repositorio de GitHub de cada proyecto.



<!-- ********************************* -->
## Ejemplo: instalación de paquete de notificaciones

Por ejemplo, para instalar el paquete "Notification" de _edvinaskrucas_ tendríamos que editar el fichero `composer.json` y en su sección `require` añadir la siguiente línea:

```json
"edvinaskrucas/notification": "3.*"
```

> Al añadir el paquete al fichero `composer.json` tenemos que llevar mucho cuidado de añadir una coma como separador excepto al final del último elemento.


Una vez añadido ejecutamos el siguiente comando para instalar el paquete:

```bash
sudo composer update
```

Y después de instalar tendríamos que editar el fichero `app/config/app.php` para añadir, en la sección `providers`, la siguiente línea:

```php
'Krucas\Notification\NotificationServiceProvider'
```

Y en la sección `aliases` lo siguiente:

```php
'Notification' => 'Krucas\Notification\Facades\Notification'
```

Con esto ya tendríamos instalada esta librería y podríamos empezar a utilizarla. Por ejemplo, para añadir una notificación desde un controlador podemos utilizar los siguientes métodos:

```php
Notification::success('Success message');
Notification::error('Error message');
Notification::info('Info message');
Notification::warning('Warning message');
```

Y para mostrar las notificaciones, desde una vista (preferiblemente desde el _layout_ principal), añadiríamos el siguiente código:

```php
{{ Notification::showAll() }}
```



