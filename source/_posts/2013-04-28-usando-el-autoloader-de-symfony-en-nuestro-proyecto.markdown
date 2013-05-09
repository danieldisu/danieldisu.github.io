---
layout: post
title: "Usando el Autoloader de Symfony en nuestro proyecto"
date: 2013-04-28 23:26
comments: true
categories: [php, Symfony]
---


Seguramente nos haya pasado muchas veces cuando estáis trabajando en algún pequeño proyecto de php en el cual no os interesa usar un framework, como pueden ser Symfony o Zend, ha crecido demasiado, y se hace difícil mantener las dependencias en algunas clases, llamar en cada archivo a todas las clases que usa y en general, localizar las clases dependiendo del lugar donde se encuentre la clase que inicia la petición.

Para solucionar esos y otros problemas, podemos usar un Autoloader, lo cual es una clase/función la cual cargara todos los archivos que le indiquemos dentro de un <a href="http://www.php.net/manual/es/language.namespaces.php">namespace</a>.

Podemos implementar nuestro propio Autoloader o usar alguno de los que provee la comunidad, como puede ser el ejemplo
del autoloader de Symfony ( <a href="https://github.com/symfony/ClassLoader">GitHub</a> ), en el propio repositorio de GitHub hay una pequeña guia de como usar el Autoloader, pero no queda del todo claro como han de ir los archivos y los namespaces conjuntamente para que todo funcione y el Autoloader sea capaz de encontrar tus archivos de clases dentro de tu proyecto, para explicar un poco mejor como deben ir los archivos y los namespaces he creado un pequeño proyecto en GitHub en el cual se ejemplifica como podríamos usarlo en nuestros proyectos.

Todo el código de esta entrada se puede encontrar en <a href="https://github.com/danieldisu/danieldisu.github.io">.GitHub</a>

## Preparando nuestra estructura de carpetas

# {% img /images/autoloader/folders.JPG Estructura Carpetas %}

Este es el aspecto que tiene el proyecto de ejemplo, en este caso, la clase principal es **app.php**, en autoloader.php será donde le indiquemos cuales son los namespaces que tiene nuestra app y donde están colocados.

{% codeblock lang:php %}
//autoloader.php
<?php
require_once('vendor/Symfony/Component/ClassLoader/UniversalClassLoader.php');

use Symfony\Component\ClassLoader\UniversalClassLoader;

$loader = new UniversalClassLoader();

/*
**Aquí podríamos registrar todos los namespaces, llamando al método
**registarNamespaces y pasándole un array, si se diese el caso 
**de que tuviésemos que cargar mas de un namespace
*/
$loader->registerNamespace("nombreProyecto", __DIR__.'/src');

$loader->register();
?>
{% endcodeblock %}

Es importante tener en cuenta que la ruta que le ponemos es la ruta que contiene el proyecto, no le decimos directamente la ruta donde se encuentra el proyecto. En este caso, buscará dentro de la ruta todos los archivos que estén ese namespace y los cargará automáticamente de manera que los tendremos disponibles.

En **app.php** muestro la sintaxis con la que llamariamos a nuestras clases:

{% codeblock lang:php %}
//app.php
<?php
namespace app;
require_once "autoloader.php";

use nombreProyecto\ClasesNegocio\Clase1;
use nombreProyecto\ClasesNegocio\Clase2;

$clase1 = new Clase1();

echo $clase1->metodo();

echo "<br/>";

$clase2 = new Clase2();

echo $clase2->metodo();

?>   
{% endcodeblock %}

Con la clausula use, no nos tenemos que preocupar de donde se encuentre el archivo, o de cual es la ruta relativa desde el archivo que nos encontramos para llegar al archivo que contiene la clase, lo que facilita mucho el trabajo cuando estamos trabajando desde distintos directorios, que a su vez incluyen clases de otros directorios.

Para demostrar el hecho de que se puede llamar desde cualquier sitio, está el archivo **OtraClase.php**, la única ruta de la que nos tenemos que preocupar es la del autoloader, la cual se encuentra en el directorio raíz de nuestro proyecto, por lo que no nos resultará difícil de encontrar.

{% codeblock lang:php %}
//app.php
<?php
namespace src;

require_once "../../../autoloader.php";

use nombreProyecto\ClasesNegocio\Clase1;
use nombreProyecto\ClasesNegocio\Clase2;

$clase1 = new Clase1();

echo $clase1->metodo();

echo "<br/>";

$clase2 = new Clase2();

echo $clase2->metodo();

?>   
{% endcodeblock %}

Como podemos comprobar lo unico que hay que cambiar es la ruta para acceder al autoloader, en cambio solo con el namespace, el autoloader de Symfony se encarga de buscar en el directorio indicado las clases que necesitamos.
