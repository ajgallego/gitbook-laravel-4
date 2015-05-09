# Probar nuestra API con _cURL_

Para probar una API lo podemos hacer fácilmente utilizando el comando `curl` desde consola, el cual permite enviar peticiones de cualquier tipo a una URL, especificar las cabeceras, parámetros, etc.

Por ejemplo, para realizar una petición tipo GET a una URL simplemente tenemos que hacer:

```bash
$ curl -i http://localhost/recurso

HTTP/1.1 200 OK
Transfer-Encoding: chunked
Date: Fri, 27 Jul 2012 05:11:00 GMT
Content-Type: text/plain

¡Hola Mundo!
```

Donde la opción `-i` indica que se muestren las cabeceras de la respuesta.

Opcionalmente, al hacer la petición podemos indicar las cabeceras con el parámetro `-H`. Por ejemplo, para solicitar datos en formato JSON tenemos que hacer:

```bash
$ curl -i -H "Accept: application/json" http://localhost/recurso

HTTP/1.1 200 OK
Date: Fri, 27 Jul 2012 05:12:32 GMT
Cache-Control: max-age=42
Content-Type: application/json
Content-Length: 27

{
    "text": "¡Hola Mundo!"
}
```

Como hemos visto por defecto se realiza una petición tipo GET. Si queremos realizar otro tipo de petición lo tendremos que indicar con el parámetro `-X` seguido del método a utilizar (POST, PUT, DELETE). Además, con la opción `-d` podemos añadir los parámetros de la petición. Los parámetros tendrán que ir entre comillas y en caso de indicar varios los separaremos con `&`. Por ejemplo, para realizar una petición tipo POST con dos parámetros:

```bash
$ curl -i -H "Accept: application/json" -X POST -d "name=javi&phone=800999800" http://localhost/users
```

De la misma forma podemos hacer una petición tipo PUT (para actualizar datos) o tipo DELETE (para eliminarlos). Por ejemplo:

```bash
$ curl -i -H "Accept: application/json" -X PUT -d "name=new-name" http://localhost/users/1

$ curl -i -H "Accept: application/json" -X DELETE http://localhost/users/1
```

Para añadir más de una cabecera tenemos que indicar varias veces la opción `-H`, por ejemplo:

```bash
$ curl -i -H "Accept: application/json" -H "Content-Type: application/json" http://localhost/resource

$ curl -i -H "Accept: application/xml" -H "Content-Type: application/xml" http://localhost/resource
```

Por ejemplo, si queremos realizar una petición tipo POST que envíe código JSON y que también espere la respuesta en JSON tendríamos que indicar ambas cabeceras y añadir el JSON que queramos en los parámetros con `-d` de forma normal:

```bash
$ curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X POST -d '{"title":"xyz","year":"xyz"}' http://localhost/resource
```


Como resumen, las opciones más importantes de `curl` son:

| Opción        | Descripción |
| ------------- | ----------- |
| `-i`          | Mostrar las cabeceras de respuesta |
| `-H "header"` | Configurar las cabeceras de la petición |
| `-X <type>`   | Indicar el método de la petición: POST, PUT, DELETE. Si no indicamos nada la petición será de tipo GET. |
| `-d "params"` | Añadir parámetros a la petición. Los parámetros tendrán que ir entre comillas "". Si queremos pasar varios parámetros utilizaremos como separador "`&`" |



<!-- ************************************ -->
## Plugins o extensiones

Además de `curl` también podemos utilizar otro tipo de programas para probar una API. En Firefox y Chrome podemos encontrar extensiones que nos facilitarán este tipo de trabajo. Por ejemplo, en Firefox podemos encontrar _HttpRequester_ (https://addons.mozilla.org/en-US/firefox/addon/httprequester/) o _Poster_ (https://addons.mozilla.org/en-US/firefox/addon/poster/). Una vez instalado los podremos abrir desde el menú `Herramientas`, y utilizarlo a través de una interfaz visual muy sencilla.

Para Chrome también podemos encontrar muchas extensiones si buscamos en su tienda (https://chrome.google.com/webstore/category/apps). Algunas opciones interesantes son "_Advanced REST client_" o "_Postman - REST Client_".


