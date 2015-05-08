<!-- ************************************************************************-->
# Ejercicios

En los ejercicios de esta sección del curso vamos a desarrollar una pequeña web para la gestión interna de un videoclub, empezaremos por definir las rutas y vistas del sitio y poco a poco en los siguientes ejercicios la iremos completando hasta terminar el sitio web completo.

El objetivo es un sitio web para su utilización de forma interna en un videoclub, el cual estará protegido por usuario y contraseña. Una vez validado el usuario permitirá listar el catálogo de películas, ver información detalle de una película, realizar búsquedas o filtrados y algunas operaciones de gestión.



<!-- ************************************* -->
## Ejercicio 1 - Instalación de Laravel (0.5 puntos)

En primer lugar tenemos que instalar todo lo necesario para poder realizar el sitio web con Laravel. Para esto seguiremos las explicaciones del apartado que hemos visto en la teoría "Instalación de Laravel" para instalar un servidor Web y Composer.

Una vez instalado crearemos un nuevo proyecto de Laravel en la carpeta `videoclub`, lo configuraremos (clave de seguridad, permisos, etc.) y probaremos que todo funcione correctamente.



<!-- ************************************* -->
## Ejercicio 2 - Definición de las rutas (0.5 puntos)

En este ejercicio vamos a definir las rutas principales que va a tener nuestro sitio web. Para empezar simplemente indicaremos que las rutas devuelvan una cadena (así podremos comprobar que se han creado correctamente). A continuación se incluye una tabla con las rutas a definir (todas de tipo GET) y el texto que tienen que mostrar:

| Ruta              | Texto a mostrar    |
| ----------------- | ------------------ |
| /                 | Pantalla principal |
| login             | Login usuario      |
| logout            | Logout usuario     |
| catalog           | Listado películas  |
| catalog/show/{id} | Vista detalle película {id} |
| catalog/create    | Añadir película    |
| catalog/edit/{id} | Modificar película {id} |

Para comprobar que las rutas se hayan creado correctamente utiliza el comando de artisan que devuelve un listado de rutas y además prueba también las rutas en el navegador.




<!-- ************************************* -->
## Ejercicio 3 - _Layout_ principal de las vistas con Bootstrap (1 punto)

En este ejercicio vamos a crear el _layout_ base que van a utilizar el resto de vistas del sitio web y además incluiremos la librería Bootstrap para utilizarla como estilo base.

En primer lugar accedemos a la web "http://getbootstrap.com/" y descargamos la librería. Esto nos bajará un fichero zip comprimido con tres carpetas (_js_, _css_ y _fonts_) que tenemos que extraer en la carpeta "`public/assets/bootstrap`" (tendremos que crear las carpetas "/assets/bootstrap").

También nos tenemos que descargar desde los materiales de los ejercicios la plantilla para la barra de navegación principal (`navbar.blade.php`) y la almacenamos en la carpeta `views/partials`.

A continuación vamos a crear el _layout_ principal de nuestro sitio:

* Creamos el fichero `app/views/layouts/master.blade.php`.
* Le añadimos como contenido la plantilla base HTML que propone Bootstrap en su documentación "http://getbootstrap.com/getting-started/#template", modificando los siguientes elementos:
  * Cambiamos las rutas para acceder a los assets (_css_ y _js_) que hemos almacenado en local. Para generar la ruta completa y que encuentre los recursos tendremos que escribir los siguientes comandos:
<br/>
```php
{{ URL::to('/assets/bootstrap/css/bootstrap.min.css') }}
{{ URL::to('/assets/bootstrap/js/bootstrap.min.js') }}
```
    `assets/bootstrap/css/bootstrap.min.css` (sin barra `/` inicial).
  * Dentro de la sección `<body>` del HTML incluimos la barra de navegación que hemos guardado antes utilizando el siguiente código:
<br/>
```html
@include('partials.navbar')
```
  * A continuación de la barra de navegación añadimos la sección principal donde aparecerá el contenido de la web:
<br/>
```html
<div class="container">
    @yield('content')
</div>
```

Con esto ya hemos definido el layout principal, sin embargo todavía no podemos probarlo ya que no está asociado a ninguna ruta. En el siguiente ejercicio realizaremos los cambios necesarios para poder verlo y además añadiremos el resto de vistas hijas.




<!-- ************************************* -->
## Ejercicio 4 - Crear el resto de vistas (1 punto)

En este ejercicio vamos terminar una primera versión estable de la web. En primer lugar crearemos las vistas asociadas a cada ruta, las cuales tendrán que extender del _layout_ que hemos hecho en el ejercicio anterior y mostrar, en la sección de `content` del mismo, el texto de ejemplo que habíamos definido para las vistas en el ejercicio 2. En general todas las vistas tendrán un código similar al siguiente (variando únicamente la sección `content`):

```
@extends('layouts.master')

@section('content')

	Pantalla principal

@stop
```

Para organizar mejor las vistas las vamos a agrupar en sub-carpetas dentro de la carpeta `views` siguiendo la siguiente estructura:

| Vista              | Carpeta          | Ruta asociada     |
| ------------------ | ---------------- | ----------------- |
| `home.blade.php`   | `views/`         | /                 |
| `login.blade.php`  | `views/user/`    | login             |
| `index.blade.php`  | `views/catalog/` | catalog           |
| `show.blade.php`   | `views/catalog/` | catalog/show/{id} |
| `create.blade.php` | `views/catalog/` | catalog/create    |
| `edit.blade.php`   | `views/catalog/` | catalog/edit/{id} |

> Creamos una vista separada para todas las rutas excepto para la ruta "logout", la cual no tendrá ninguna vista.


Por último vamos a actualizar las rutas del fichero `routes.php` para que se carguen las vistas que acabamos de crear. Acordaros que para referenciar las vistas que están dentro de carpetas la barra `/` de separación se transforma en un punto, y que además, como segundo parámetro, podemos pasar datos a la vista. A continuación se incluyen algunos ejemplos:

```php
return View::make('home');
return View::make('catalog.index');
return View::make('catalog.show', array('id'=>$id));
```

Una vez hechos estos cambios ya podemos probarlo en el navegador, el cual debería mostrar en todos los casos la plantilla base con la barra de navegación principal y los estilos de Bootstrap aplicados. En la sección principal de contenido de momento solo podremos ver los textos que hemos puesto de ejemplo.



