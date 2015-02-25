<properties 
	pageTitle="Cómo depurar sitios web de Azure en Node.js" 
	description="Obtenga información acerca de cómo depurar un sitio web de Azure en Node.js." 
	services="web-sites" 
	documentationCenter="nodejs" 
	authors="blackmist" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>





#Depuración de una aplicación Node.js en Sitios web Azure

Azure proporciona diagnósticos integrados para ayudar a depurar aplicaciones Node.js hospedadas en Sitios web Azure. En este artículo aprenderá a habilitar el registro de stdout y stderr, mostrar información de error en el explorador y a descargar y ver los archivos de registro.

El diagnóstico de las aplicaciones Node.js hospedadas en Azure lo proporciona [IISNode]. Si bien este artículo analiza la configuración más común para recopilar información de diagnóstico, no proporciona una referencia completa para trabajar con IISNode. Para obtener más información sobre el trabajo con IISNode, consulte el [archivo léame de IISNode] (en inglés) en GitHub.

##<a id="enablelogging"></a>Habilitación del registro

De manera predeterminada, Sitios web Azure solo captura información de diagnóstico sobre las implementaciones, como cuando se realiza la implementación de un sitio web con Git. Esta información es útil si está teniendo problemas durante la implementación, como un error al instalar un módulo al que se hace referencia en **package.json** o si va a usar un script de implementación personalizado.

Para habilitar el registro de las secuencias stdout y stderr, debe crear un archivo **IISNode.yml** en la raíz de su aplicación Node.js y agregar lo siguiente:

	loggingEnabled: true

Esto permite el registro de stderr y stdout desde su aplicación Node.js.

El archivo **IISNode.yml** puede usarse también para controlar si los errores descriptivos o del desarrollador se devuelven al explorador cuando se produce un error. Para habilitar los errores del desarrollador, agregue la siguiente línea al archivo **IISNode.yml**:

	devErrorsEnabled: true

Después de que se habilita esta opción, IISNode devolverá los últimos 64 K de información que se enviaron a stderr en vez de un error descriptivo como "se produjo un error de servidor interno".

> [AZURE.NOTE] Si bien devErrorsEnabled es útil para diagnosticar los problemas que se producen durante el desarrollo, su habilitación en un entorno de producción puede tener como resultado que se envíen errores de desarrollo a los usuarios finales.

Si el archivo **IISNode.yml** no existía ya en su aplicación, debe reiniciar su sitio web después de publicar la aplicación actualizada. Si solamente va a cambiar la configuración de un archivo **IISNode.yml** existente que ya se publicó anteriormente, no es necesario reiniciar.

> [AZURE.NOTE] Si su sitio web se creó usando las herramientas de línea de comandos de Azure o los cmdlets de Azure PowerShell, automáticamente se crea un archivo predeterminado **IISNode.yml**.

Puede reiniciar el sitio web si selecciona el sitio desde el [Portal de administración de Azure] y, a continuación, selecciona el botón **RESTART**:

![restart button][restart-button]

Si las herramientas de línea de comandos de Azure están instaladas en su entorno de desarrollo, puede usar el siguiente comando para reiniciar el sitio web:

	azure site restart [sitename]

> [AZURE.NOTE] Si bien loggingEnabled y devErrorsEnabled son las opciones de configuración de IISNode.yml de uso más común para capturar información de diagnóstico, se puede usar IISNode.yml para configurar diversas opciones para su entorno de hospedaje. Para obtener una lista completa de las opciones de configuración, consulte el archivo [iisnode_schema.xml](https://github.com/tjanczuk/iisnode/blob/master/src/config/iisnode_schema.xml).

##<a id="viewlogs"></a>Acceso a los registros

Se puede tener acceso a los registros de diagnósticos de tres formas; mediante el protocolo de transferencia de archivos (FTP), la descarga de un archivo Zip o como una secuencia en directo actualizada del registro (lo que se conoce también como cola). La descarga del archivo Zip de los archivos de registro o la visualización de la secuencia en directo requieren las herramientas de línea de comandos de Azure. Estas se pueden instalar usando el siguiente comando:

	npm install azure-cli -g

Una vez instalado, se tiene acceso a las herramientas mediante el comando 'azure'. Las herramientas de línea de comandos se deben configurar primero para usar su suscripción de Azure. Para obtener información sobre cómo llevar a cabo esta tarea, consulte la sección **Descarga e importación de la configuración de publicación** del artículo [Uso de las herramientas de línea de comandos de Azure].

###FTP

Para tener acceso a la información de diagnóstico a través de FTP, visite el [portal de Azure], seleccione su sitio web y, a continuación, seleccione el **PANEL**. En la sección **vínculos rápidos**, los vínculos **FTP DIAGNOSTIC LOGS** y **FTPS DIAGNOSTIC LOGS** proporcionan acceso a los registros usando el protocolo FTP.

> [AZURE.NOTE] Si no ha configurado antes el nombre de usuario y la contraseña de FTP o la implementación, puede hacerlo desde la página de administración **Inicio rápido** al seleccionar **Configurar las credenciales de implementación**.

La URL de FTP devuelta en el panel es para el directorio **LogFiles**, que contendrá los siguientes subdirectorios:

* [Deployment Method]: Si usa un método de implementación como Git, se creará un directorio del mismo nombre que contendrá información relacionada con las implementaciones.

* nodejs: La información de Stdout y stderr capturada desde todas las instancias de su aplicación (cuando loggingEnabled es verdadero).

###Archivo Zip

Para descargar un archivo Zip de los registros de diagnóstico, use el siguiente comando de las herramientas de línea de comandos de Azure:

	azure site log download [sitename]

De esta manera se descargará un archivo **diagnostics.zip** en el directorio actual. Este archivo contiene la siguiente estructura de directorios:

* deployments: Un registro de información sobre las implementaciones de su aplicación

* LogFiles

	* [Deployment Method]: si usa un método de implementación como Git, se creará un directorio del mismo nombre que contendrá información relacionada con las implementaciones.

	* nodejs: La información de Stdout y stderr capturada desde todas las instancias de su aplicación (cuando loggingEnabled es verdadero).

###Secuencia en directo (cola)

Para ver una secuencia en directo de la información de registro de diagnóstico, use el siguiente comando en las herramientas de línea de comandos de Azure:

	azure site log tail [sitename]

De esta manera se devolverá una secuencia de eventos de registro que se actualizan a medida que se producen en el servidor. Esta secuencia devolverá información de implementación además de información de stdout y stderr (cuando loggingEnabled es verdadero).

##<a id="nextstepsPasos siguientes"></a>

En este artículo aprendió a habilitar y a tener acceso a información de diagnóstico en Azure. Si bien esta información es útil para comprender los problemas que se producen con su aplicación, puede indicar un problema con un módulo que está usando o que la versión de Node.js que usan los Sitios web Azure es diferente a la usada en su entorno de implementación.

Para obtener información sobre el trabajo con módulos en Azure, consulte [Uso de módulos Node.js con aplicaciones de Azure].

Para obtener información sobre la especificación de una versión de Node.js para su aplicación, consulte [Especificación de una versión de Node.js en una aplicación de Azure].

[IISNode]: https://github.com/tjanczuk/iisnode
[IISNode Readme]: https://github.com/tjanczuk/iisnode#readme
[Uso de las herramientas de línea de comandos de Azure]: /es-es/documentation/articles/xplat-cli/
[Uso de módulos Node.js con aplicaciones de Azure]: /es-es/documentation/articles/nodejs-use-node-modules-azure-apps/
[Especificación de una versión de Node.js en una aplicación Azure]: /es-es/documentation/articles/nodejs-specify-node-version-azure-apps/
[Portal de administración de Azure]: https://manage.windowsazure.com/

[restart-button]: ./media/web-sites-nodejs-debug/restartbutton.png




<!--HONumber=42-->
