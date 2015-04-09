Azure determinará la versión de Python que se usará para su entorno virtual con la siguiente prioridad:

1. versión especificada en runtime.txt en la carpeta raíz
1. versión especificada por la configuración de Python en la configuración del sitio web (la página CONFIGURAR en el portal de Azure)
1. python-2.7 es el valor predeterminado si no se especifica ninguno de los anteriores

Los valores válidos para el contenido 

    \runtime.txt

son:

- python-2.7
- python-3.4

Si se especifica la versión micro (tercer dígito), se ignora.

<!--HONumber=49-->