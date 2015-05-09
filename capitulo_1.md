# Introducción


<!-- ************************************************************************-->
# ¿Qué es Laravel?

Laravel es un framework de código abierto para el desarrollo de aplicaciones web en PHP 5 que posee una sintaxis simple y elegante.

Características:

* Creado en 2011 por Taylor Otwell.
* Esta inspirado en Ruby on rails y Symfony, de quien posee muchas dependencias.
* Esta diseñado para desarrollar bajo el patrón MVC (ver siguiente sección).
* Posee un sistema de mapeado de datos relacional llamado Eloquent ORM.
* Utiliza un sistema de procesamiento de plantillas llamado Blade, el cual hace uso de la cache para darle mayor velocidad.



<!-- ************************************************************************-->
# Modelo - Vista - Controlador (MVC)

El modelo–vista–controlador (MVC) es un patrón de arquitectura de software que separa los datos y la lógica de negocio de una aplicación de la interfaz de usuario y el módulo encargado de gestionar los eventos y las comunicaciones. Para ello MVC propone la construcción de tres componentes distintos que son el modelo, la vista y el controlador, es decir, por un lado define componentes para la representación de la información, y por otro lado para la interacción del usuario. Este patrón de arquitectura de software se basa en las ideas de reutilización de código y la separación de conceptos, características que buscan facilitar la tarea de desarrollo de aplicaciones y su posterior mantenimiento.

![](images/web_laravel/esquema_mvc.png)

De manera genérica, los componentes de MVC se podrían definir como sigue:

* El Modelo: Es la representación de la información con la cual el sistema opera, por lo tanto gestiona todos los accesos a dicha información, tanto consultas como actualizaciones. Las peticiones de acceso o manipulación de información llegan al 'modelo' a través del 'controlador'.

* El Controlador: Responde a eventos (usualmente acciones del usuario) e invoca peticiones al 'modelo' cuando se hace alguna solicitud de información (por ejemplo, editar un documento o un registro en una base de datos). Por tanto se podría decir que el 'controlador' hace de intermediario entre la 'vista' y el 'modelo'.

* La Vista: Presenta el 'modelo' y los datos preparados por el controlador al usuario de forma visual. El usuario podrá interactuar con la vista y realizar otras peticiones que se enviarán al controlador.













<!-- ************************************************************************-->
<!-- ************************************************************************-->
<!-- ************************************************************************-->
<!-- ************************************************************************-->









