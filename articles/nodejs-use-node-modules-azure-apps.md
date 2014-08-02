<properties linkid="develop-nodejs-common-tasks-working-with-node-modules" urlDisplayName="Working with Node.js Modules" pageTitle="Working with Node.js Modules" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="Node.js" title="Using Node.js Modules with Azure applications" authors="larryfr" solutions="" manager="paulettm" editor="mollybos" />

Uso de módulos Node.js con aplicaciones de Azure
================================================

Este documento le proporciona orientación sobre el uso de módulos Node.js con aplicaciones hospedadas en Azure. Mediante este documento podrá asegurarse de que su aplicación usa una versión específica del módulo y de que usa módulos nativos con Azure.

Si está ya familiarizado con el uso de módulos Node.js, archivos **package.json** y **npm-shrinkwrap.json**, a continuación se muestra un resumen rápido del contenido de este artículo:

-   Los sitios web de Azure entienden los archivos **package.json** y **npm-shrinkwrap.json** y pueden instalar módulos según las entradas de estos archivos.
-   Los servicios en la nube de Azure esperan que todos los módulos se instalen en el entorno de desarrollo y que el directorio **node\_modules** se incluya como parte del paquete de desarrollo.

**Nota:**

No se tratan las máquinas virtuales de Azure en este artículo, ya que la experiencia de implementación en una VM depende del sistema operativo hospedado por la máquina virtual.

**Nota:**

Es posible ofrecer compatibilidad para la instalación de módulos con archivos **package.json** o **npm-shrinkwrap.json** en Azure. Sin embargo, esto requiere la personalización de los scripts predeterminados que usan los proyectos del servicio en la nube. Para obtener un ejemplo sobre como conseguir esto, consulte [Azure Startup task to run npm install to avoid deploying node modules](http://nodeblog.azurewebsites.net/startup-task-to-run-npm-in-azure).

Módulos Node.js
---------------

Los módulos son paquetes JavaScript que pueden cargarse y que proporcionan una funcionalidad específica para su aplicación. Un módulo se instala normalmente con la herramienta de la línea de comandos **npm**. Sin embargo, algunos (como el módulo http) se proporcionan como parte del paquete Node.js principal.

Cuando se instalan los módulos, se almacenan en el directorio **node\_modules** en la raíz de la estructura del directorio de la aplicación. Los módulos dentro del directorio **node\_modules** mantienen sus propios directorios **node\_modules** que contienen los módulos de los que depende, y esto se vuelve a repetir para cada módulo hasta el final de la cadena de dependencia. De este modo, los módulos instalados pueden disponer de los requisitos de la propia versión para los módulos de los que depende. Sin embargo, esto puede provocar una estructura de directorio bastante amplia.

Cuando se implementa el directorio **node\_modules** como parte de su aplicación, aumentará el tamaño de la implementación en comparación con el uso del archivo **package.json** o **npm-shrinkwrap.json**; sin embargo, esto no garantiza que la versión de los módulos usados en la producción sea la misma que la de los usados en el desarrollo.

### Módulos nativos

Mientras que la mayoría de los módulos son archivos JavaScript sin formato, algunos módulos son imágenes binarias específicas de plataforma. Estos módulos se compilan en el momento de la instalación, normalmente mediante Python y node-gyp. Una limitación específica de los sitios web de Azure es que cuando entiende de forma nativa cómo instalar módulos especificados en un archivo **package.json** o **npm-shrinkwrap.json**, no proporciona Python ni node-gyp, y no puede generar módulos nativos.

Puesto que los servicios en la nube de Azure se basan en la carpeta **node\_modules** que se implementa como parte de la aplicación, los módulos nativos incluidos como parte de los módulos instalados deben funcionar en un servicio en la nube siempre que se haya instalado y compilado en un sistema de desarrollo de Windows.

Los módulos nativos no son compatibles con los sitios web de Azure. Algunos módulos como JSDOM y MongoDB cuentan con dependencias nativas opcionales y funcionarán con aplicaciones hospedadas en los sitios web de Azure.

### Uso de un archivo package.json

El archivo **package.json** sirve para especificar las dependencias de nivel superior que requiere la aplicación de manera que la plataforma de hospedaje pueda instalar las dependencias evitando la necesidad de incluir la carpeta **node\_packages** como parte de la implementación. Una vez implementada la aplicación, el comando **npm install** se usa para analizar el archivo **package.json** e instalar todas las dependencias enumeradas.

Durante el desarrollo, puede usar los parámetros **--save**, **--save-dev** o **--save-optional** cuando instale los módulos para agregar una entrada para el módulo al archivo **package.json** automáticamente. Para obtener más información, consulte [npm-install](https://npmjs.org/doc/install.html) (en inglés).

Un posible problema con el archivo **package.json** es que solo especifica la versión de dependencias de nivel superior. Cada módulo instalado puede o no especificar la versión de los módulos de los que depende y, por lo tanto, es posible que acabe con una cadena de dependencia distinta a la que usó en el desarrollo.

> [WACOM.NOTE] Cuando implemente un sitio web de Azure, si el archivo **package.json** hace referencia a un módulo nativo, verá un error parecido al siguiente cuando publique la aplicación con Git:

>        npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>        npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1    

### Uso de un archivo npm-shrinkwrap.json

El archivo **npm-shrinkwrap.json** es un intento de solucionar las limitaciones de la versión del módulo del archivo **package.json**. Mientras que el archivo **package.json** solo incluye versiones para los módulos de nivel superior, el archivo **npm-shrinkwrap.json** contiene los requisitos de la versión para la cadena de dependencia del módulo completa.

Cuando la aplicación esté preparada para la producción, puede bloquear los requisitos de la versión y crear un archivo **npm-shrinkwrap.json** mediante el comando **npm shrinkwrap**. De esta forma, se usarán las versiones instaladas actualmente en la carpeta **node\_modules** y se registrarán en el archivo **npm-shrinkwrap.json**. Una vez implementada la aplicación en el entorno de hospedaje, se usa el comando **npm install** para analizar el archivo **npm-shrinkwrap.json** e instalar todas las dependencias enumeradas. Para obtener más información, consulte [npm-install](https://npmjs.org/doc/install.html) (en inglés).

> [WACOM.NOTE] Cuando implemente un sitio web de Azure, si el archivo **npm-shrinkwrap.json** hace referencia a un módulo nativo, verá un error parecido al siguiente cuando publique la aplicación con Git:

>        npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>        npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1

Pasos siguientes
----------------

Ahora que sabe cómo usar los módulos Node.js con Azure, puede aprender a [especificar la versión de Node.js](/en-us/develop/nodejs/common-tasks/specifying-a-node-version/), [generar e implementar un sitio web de Node.js](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/) y [usar las herramientas de la línea de comandos de Azure para Mac y Linux](/en-us/develop/nodejs/how-to-guides/command-line-tools/).

