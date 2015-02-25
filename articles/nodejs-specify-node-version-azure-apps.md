<properties urlDisplayName="Specifying a Node.js Version" pageTitle="Especificación de una versión de Node.js" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="nodejs" title="Specifying a Node.js version in an Azure application" authors="larryfr" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="na" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />





# Especificación de una versión de Node.js en una aplicación Azure

Cuando hospeda una aplicación de Node.js, es posible que desee asegurarse de que su aplicación utiliza una versión específica de Node.js. Hay muchas maneras de hacer esto con las aplicaciones hospedadas en Azure.

## Versiones predeterminadas

Actualmente Azure proporciona las versiones 0.6.17, 0.6.20 y 0.8.4 de Node.js. A menos que se especifique lo contrario, 0.6.20 es la versión predeterminada que se utilizará.

<div class="dev-callout">
<strong>Nota:</strong>
<p>Si hospeda su aplicación en un servicio en la nube de Azure (rol web o de trabajo) y es primera vez que ha implementado la aplicación, Azure intentará utilizar la misma versión de Node.js que ha instalado en su entorno de desarrollo si coincide con una de las versiones predeterminadas disponibles en Azure.</p>
</div>


## Control de versiones con package.json

Puede especificar la versión de Node.js que se va a utilizar si agrega lo siguiente a su archivo **package.json**:

	"engines":{"node":version}

Donde *version* es el número específico de la versión que se utilizará. Puede especificar condiciones más complejas para la versión, como:

	"engines":{"node": "0.6.22 || 0.8.x"}

Como 0.6.22 no es una de las versiones disponibles en el entorno de hospedaje, se utilizará en su lugar la versión superior de la serie 0.8 que se encuentra disponible, es decir, 0.8.4.

## Control de versiones de Servicios en la nube con PowerShell

Si hospeda la aplicación en un servicio en la nube y la aplicación se implementa con Azure PowerShell, puede reemplazar la versión predeterminada de Node.js mediante el uso del cmdlet de **Set-AzureServiceProjectRole**. Por ejemplo:

	Set-AzureServiceProjectRole WebRole1 node 0.8.4

También puede utilizar **Get-AzureServiceProjectRoleRuntime** para recuperar una lista de las versiones disponibles de Node.js para las aplicaciones hospedadas como Servicio en la nube.

## Uso de una versión personalizada con Sitios web Azure

A pesar de que Azure proporciona varias versiones predeterminadas de Node.js, es posible que desee utilizar una versión que no se brinda de manera predeterminada. Si su aplicación está hospedada como un sitio web de Azure, puede hacer esto con el archivo **iisnode.yml**. Los siguientes pasos describen el proceso de usar una versión personalizada de Node.js con un sitio web Azure:

1. Cree un directorio nuevo y, a continuación, cree un archivo **server.js** dentro del directorio. El archivo **server.js** debe contener lo siguiente:

		var http = require('http');
		http.createServer(function(req,res) {
		  res.writeHead(200, {'Content-Type': 'text/html'});
		  res.end('Hello from Azure running node version: ' + process.version + '</br>');
		}).listen(process.env.PORT || 3000);

	Con esto aparecerá la versión de Node.js que se utiliza cuando navega en el sitio web.

2. Cree un sitio web y anote el nombre del sitio. Por ejemplo, el ejemplo siguiente utiliza las [herramientas de línea de comandos de Azure] para crear un sitio web de Azure llamado **mywebsite** y luego se habilita un repositorio Git para el sitio web.

		azure site create mywebsite --git

3. Cree un directorio nuevo llamado **bin** como secundario del directorio que contiene el archivo **server.js**.

4. Descargue la versión específica de **node.exe** (la versión para Windows) que desea utilizar con su aplicación. Por ejemplo, a continuación se utiliza **curl** para descargar la versión 0.8.1:

		curl -O http://nodejs.org/dist/v0.8.1/node.exe

	Guarde el archivo **node.exe** en la carpeta **bin** que se creó anteriormente.

5. Cree un archivo **iisnode.yml** en el mismo directorio en que se encuentra el archivo **server.js** y, a continuación, agregue el siguiente contenido al archivo **iisnode.yml**:

		nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

	Esta ruta de acceso es donde el archivo **node.exe** se ubicará dentro del proyecto una vez que haya publicado su aplicación en el sitio web de Azure.

6. Publique la aplicación. Por ejemplo, dado que anteriormente creé un sitio web con el parámetro --git, los siguientes comandos agregarán los archivos de aplicación a mi repositorio Git local y luego los insertarán en el repositorio del sitio web:

		git add .
		git commit -m "testing node v0.8.1"
		git push azure master

	Cuando se haya publicado la aplicación, abra el sitio web en un explorador. Debe aparecer un mensaje que diga "Hello from Azure running node version: v0.8.1".

## Pasos siguientes

Ahora que sabe cómo especificar la versión de Node.js que utiliza su aplicación, obtenga más información acerca del [funcionamiento con módulos], la [compilación e implementación de un sitio web Node.js] y el [Uso de las herramientas de línea de comandos de Azure para Mac y Linux].

[Uso de las herramientas de línea de comandos de Azure para Mac y Linux]: /es-es/documentation/articles/xplat-cli/
[Herramientas de línea de comandos de Azure]: /es-es/documentation/articles/xplat-cli/
[funcionamiento con módulos]: /es-es/documentation/articles/nodejs-use-node-modules-azure-apps/
[compilación e implementación de un sitio web de Node.js]: /es-es/documentation/articles/web-sites-nodejs-develop-deploy-mac/
