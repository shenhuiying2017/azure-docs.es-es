El script de implementación omitirá la creación del entorno virtual en Azure si detecta que ya existe un entorno virtual compatible.  Esto puede agilizar la implementación considerablemente.  Pip omitirá los paquetes que ya están instalados.

En algunas situaciones, puede que desee forzar la eliminación de dicho entorno virtual.  Deberá hacer esto si decide incluir un entorno virtual como parte del repositorio.  También puede que desee hacerlo si necesita eliminar ciertos paquetes o probar cambios en requirements.txt.

Hay unas cuantas opciones para administrar el entorno virtual existente en Azure:

### Opción 1: usar FTP

Con un cliente FTP, conéctese al servidor y podrá eliminar la carpeta env.  Tenga en cuenta que algunos clientes FTP (por ejemplo, los exploradores web) pueden ser de solo lectura y no le permiten eliminar carpetas, por lo que deberá asegurarse de usar un cliente FTP con esa capacidad.  El nombre de host FTP y el usuario se muestran en la página del panel de su sitio web en el portal de Azure.

### Opción 2: alternancia del tiempo de ejecución

A continuación se ofrece una alternativa que aprovecha el hecho de que el script de implementación eliminará la carpeta env cuando no coincida con la versión deseada de Python.  Esto eliminará de manera eficaz el entorno existente y creará uno nuevo.

1. Cambiar a una versión diferente de Python (a través de runtime.txt o de la página Configurar del sitio web)
1. Realizar cambios mediante inserciones de Git (ignorar cualquier error de instalación de PIP, si hay)
1. Volver a la versión inicial de Python
1. Volver a realizar cambios mediante inserciones de Git

### Opción 3: personalizar el script de implementación

Si ha personalizado el script de implementación, puede cambiar el código en deploy.cmd para forzarlo a eliminar la carpeta env.

<!--HONumber=49-->