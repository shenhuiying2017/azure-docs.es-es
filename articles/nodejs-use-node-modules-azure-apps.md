<properties 
	pageTitle="Trabajo con módulos Node.js" 
	description="" 
	services="" 
	documentationCenter="nodejs" 
	title="Using Node.js Modules with Azure applications" 
	authors="larryfr" 
	manager="wpickett" 
	editor="mollybos" />

<tags 
	ms.service="na" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr" />





# Uso de módulos Node.js con aplicaciones de Azure

Este documento le proporciona orientación sobre el uso de módulos Node.js con aplicaciones hospedadas en Azure. Mediante este documento podrá asegurarse de que su aplicación usa una versión específica del módulo y de que usa módulos nativos con Azure.

Si ya está familiarizado con el uso de módulos Node.js, archivos **package.json** y **npm-shrinkwrap.json**, a continuación se muestra un resumen rápido del contenido de este artículo:

* Los sitios web de Azure entienden los archivos **package.json** y **npm-shrinkwrap.json** y pueden instalar módulos según las entradas de estos archivos.
* Los servicios en la nube de Azure esperan que todos los módulos se instalen en el entorno de desarrollo y que el directorio **node\_modules** se incluya como parte del paquete de desarrollo.

<div class="dev-callout">
<strong>Nota:</strong>
<p>No se tratan las máquinas virtuales de Azure en este artículo, ya que la experiencia de implementación en una VM depende del sistema operativo hospedado por la máquina virtual.</p>
</div>

<div class="dev-callout">
<strong>Nota:</strong>
<p>Es posible ofrecer compatibilidad para la instalación de módulos con archivos <b>package.json</b> o <b>npm-shrinkwrap.json</b> en Azure. Sin embargo, esto requiere la personalización de los scripts predeterminados que usan los proyectos del servicio en la nube. Para obtener un ejemplo sobre como conseguir esto, consulte <a href="http://nodeblog.azurewebsites.net/startup-task-to-run-npm-in-azure">Azure Startup task to run npm install to avoid deploying node modules</a></p>.
</div>

##Módulos Node.js

Los módulos son paquetes JavaScript que pueden cargarse y que proporcionan una funcionalidad específica para su aplicación. Un módulo se instala normalmente con la herramienta de la línea de comandos **npm**. Sin embargo, algunos (como el módulo http) se proporcionan como parte del paquete Node.js principal.

Cuando se instalan los módulos, se almacenan en el directorio **node\_modules** en la raíz de la estructura del directorio de la aplicación. Los módulos dentro del directorio **node\_modules** mantienen sus propios directorios **node\_modules** que contienen los módulos de los que dependen y esto se repite para cada módulo hasta el final de la cadena de dependencia. De este modo, los módulos instalados pueden disponer de los requisitos de la propia versión para los módulos de los que depende. Sin embargo, esto puede provocar una estructura de directorio bastante amplia.

Cuando se implementa el directorio **node\_modules** como parte de su aplicación, aumentará el tamaño de la implementación en comparación con el uso del archivo **package.json** o **npm-shrinkwrap.json**. Sin embargo, esto no garantiza que la versión de los módulos usados en la producción sea la misma que la de los usados en el desarrollo.

###Módulos nativos

Mientras que la mayoría de los módulos son archivos JavaScript sin formato, algunos módulos son imágenes binarias específicas de plataforma. Estos módulos se compilan en el momento de la instalación, normalmente mediante Python y node-gyp. Una limitación específica de los sitios web de Azure es que aunque entiende de forma nativa cómo instalar módulos especificados en un archivo **package.json** o **npm-shrinkwrap.json**, no proporciona Python ni node-gyp y no puede generar módulos nativos.

Puesto que los servicios en la nube de Azure se basan en la carpeta **node\_modules** que se implementa como parte de la aplicación, los módulos nativos incluidos como parte de los módulos instalados deben funcionar en un servicio en la nube siempre que se haya instalado y compilado en un sistema de desarrollo de Windows. 

Los módulos nativos no son compatibles con los sitios web de Azure. Algunos módulos como JSDOM y MongoDB cuentan con dependencias nativas opcionales y funcionarán con aplicaciones hospedadas en los sitios web de Azure.

###Uso de un archivo package.json

El archivo **package.json** sirve para especificar las dependencias de nivel superior que requiere la aplicación de manera que la plataforma de hospedaje pueda instalar las dependencias evitando la necesidad de incluir la carpeta **node\_packages** como parte de la implementación. Una vez implementada la aplicación, el comando **npm install** se usa para analizar el archivo **package.json** e instalar todas las dependencias enumeradas.

Durante el desarrollo, puede usar los parámetros **--save**, **--save-dev** o **--save-optional** cuando instale los módulos para agregar una entrada para el módulo al archivo **package.json** automáticamente. Para obtener más información, consulte [npm-install](https://npmjs.org/doc/install.html).

Un posible problema con el archivo **package.json** es que solo especifica la versión de dependencias de nivel superior. Cada módulo instalado puede o no especificar la versión de los módulos de los que depende y, por lo tanto, es posible que acabe con una cadena de dependencia distinta a la que usó en el desarrollo. 

> [WACOM.NOTE]
> Cuando implemente un sitio web de Azure, si el archivo <b>package.json</b> hace referencia a un módulo nativo, verá un error parecido al siguiente cuando publique la aplicación con Git:

>		npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>		npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1	


###Uso de un archivo npm-shrinkwrap.json

El archivo **npm-shrinkwrap.json** es un intento de solucionar las limitaciones de la versión del módulo del archivo **package.json**. Mientras que el archivo **package.json** solo incluye versiones para los módulos de nivel superior, el archivo **npm-shrinkwrap.json** contiene los requisitos de la versión para la cadena de dependencia del módulo completa.

Cuando la aplicación esté preparada para la producción, puede bloquear los requisitos de la versión y crear un archivo **npm-shrinkwrap.json** mediante el comando **npm shrinkwrap**. De esta forma, se usarán las versiones instaladas actualmente en la carpeta **node\_modules** y se registrarán en el archivo **npm-shrinkwrap.json**. Una vez implementada la aplicación en el entorno de hospedaje, se usa el comando **npm install** para analizar el archivo **npm-shrinkwrap.json** e instalar todas las dependencias enumeradas. Para obtener más información, consulte [npm-install](https://npmjs.org/doc/install.html).

> [WACOM.NOTE]
>Cuando implemente un sitio web de Azure, si el archivo <b>npm-shrinkwrap.json</b> hace referencia a un módulo nativo, verá un error parecido al siguiente cuando publique la aplicación con Git:
		
>		npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>		npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


##Pasos siguientes

Ahora que sabe cómo usar los módulos Node.js con Azure, puede aprender a [especificar la versión de Node.js], [generar e implementar un sitio web de Node.js] y [usar las herramientas de la línea de comandos de Azure para Mac y Linux].

[especificación de la versión de Node.js]: /es-es/documentation/articles/nodejs-specify-node-version-azure-apps/
[Uso de las herramientas de línea de comandos de Azure para Mac y Linux]: /es-es/documentation/articles/xplat-cli/
[compilación e implementación de un sitio web de Node.js]: /es-es/documentation/articles/web-sites-nodejs-develop-deploy-mac/
[Aplicación web de Node.js con almacenamiento en MongoDB (MongoLab)]: /es-es/documentation/articles/store-mongolab-web-sites-nodejs-store-data-mongodb/
[Publicación con Git]: /es-es/documentation/articles/web-sites-publish-source-control/
[Compilación e implementación de una aplicación Node.js en un Servicio en la nube de Azure]: /es-es/documentation/articles/cloud-services-nodejs-develop-deploy-app/



<!--HONumber=46--> 
