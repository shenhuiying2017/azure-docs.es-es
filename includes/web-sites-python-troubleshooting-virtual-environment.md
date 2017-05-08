El script de implementación omitirá la creación del entorno virtual en Azure si detecta que ya existe un entorno virtual compatible.  Esto puede agilizar la implementación considerablemente.  Pip omitirá los paquetes que ya están instalados.

En algunas situaciones, puede que desee forzar la eliminación de dicho entorno virtual.  Deberá hacer esto si decide incluir un entorno virtual como parte del repositorio.  También puede que desee hacerlo si necesita eliminar ciertos paquetes o probar cambios en requirements.txt.

Hay unas cuantas opciones para administrar el entorno virtual existente en Azure:

### <a name="option-1-use-ftp"></a>Opción 1: utilizar FTP
Con un cliente FTP, conéctese al servidor y podrá eliminar la carpeta env.  Tenga en cuenta que algunos clientes FTP (por ejemplo, los exploradores web) pueden ser de solo lectura y no le permiten eliminar carpetas, por lo que deberá asegurarse de usar un cliente FTP con esa capacidad.  El nombre de host FTP y el usuario se muestran en la hoja de la aplicación web en el [Portal de Azure](https://portal.azure.com).

### <a name="option-2-toggle-runtime"></a>Opción2: alternar el tiempo de ejecución
A continuación se ofrece una alternativa que aprovecha el hecho de que el script de implementación eliminará la carpeta env cuando no coincida con la versión deseada de Python.  Esto eliminará de manera eficaz el entorno existente y creará uno nuevo.

1. Cambiar a una versión diferente de Python (a través de runtime.txt o la hoja **Configuración de la aplicación** en el Portal de Azure)
2. Realizar cambios mediante inserciones de Git (ignorar cualquier error de instalación de PIP, si hay)
3. Volver a la versión inicial de Python
4. Volver a realizar cambios mediante inserciones de Git

### <a name="option-3-customize-deployment-script"></a>Opción 3: personalizar el script de implementación
Si ha personalizado el script de implementación, puede cambiar el código en deploy.cmd para forzarlo a eliminar la carpeta env.

