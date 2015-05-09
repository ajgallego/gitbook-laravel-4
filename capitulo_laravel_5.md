# Laravel 5 - Paquetes, Rest y Curl






<!-- ************************************************************************-->
<!--
# Unión entre formulario y modelo de datos

Laravel incluye una forma muy sencilla de rellenar de datos un formulario que está basado en un modelo, simplemente tendremos que abrir el formulario utilizando el método `Form::model` en lugar de `Form::open`, pasándole como primer parámetro una instancia del modelo. Por ejemplo:

```php
{{ Form::model($user, array('action' => array('Controller@method', $user->id))) }}
```

El resto de parámetro de este método son exactamente iguales a los que utilizaríamos con `Form::open`.

Al utilizar este método, todos los campos del formulario cuyo nombre coincida con los del modelo serán rellenados con los datos del campo correspondiente que se ha pasado por parámetro. Pero no solo eso, sino que si se produce un error y se envía al usuario otra vez al formulario (con la opción de `withInput`), entonces el formulario automáticamente cogerá los valores que escribió (o modificó) el usuario y los pondrá (dándoles precedencia sobre los valores que ya estaban en el modelo).


> Nota: Para cerrar el formulario tenemos que seguir usando el mismo método que antes: `Form::close`
-->



