<properties urlDisplayName="Website Using Socket.IO" pageTitle="Sitio web de Node.js con Socket.io: tutorial de Azure" metaKeywords="tutorial de socket.io de Node.js de Azure, Azure Node.js socket.io, tutorial de Node.js de Azure" description="Este tutorial muestra el uso de socket.io en un sitio web de node.js hospedado en Azure." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build a Node.js Chat Application with Socket.IO on an Azure Website" authors="larryfr" solutions="" videoId="" scriptId="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />




#Creación de una aplicación de chat Node.js con Socket.IO en un sitio web de Azure

Socket.IO proporciona comunicación en tiempo real entre su servidor node.js y los clientes con WebSockets. También es compatible con la reserva a otros transportes (como el sondeo largo) que funcionan con otros exploradores. Este tutorial le llevara por el hospedaje de una aplicación de chat basada en Socket.IO como un sitio web de Azure. Para obtener más información sobre Socket.IO, consulte [http://socket.io/][socketio].

> [WACOM.NOTE] Los procedimientos de esta tarea se aplican a Sitios web Azure; para Servicios en la nube, consulte <a href="http://www.windowsazure.com/es-es/develop/nodejs/tutorials/app-using-socketio/">Creación de una aplicación de chat Node.js con Socket.IO en un servicio en la nube de Azure</a>.


## <a id="Download"></a>Descarga del ejemplo de chat

Para este proyecto, usaremos el ejemplo de chat del [repositorio GitHub
de Socket.IO]. Realice los pasos siguientes para descargar el ejemplo
y agregarlo al proyecto que creó anteriormente.

1.  Descargue una [versión archivada ZIP o GZ][release] del proyecto Socket.IO (la versión 1.0.6 se ha utilizado para este documento)


3.  Extraiga el archivo y copie el directorio **examples\\chat**
 a una nueva ubicación. Por ejemplo, .
    **\\node\\chat**.

## <a id="Modify"></a>Modificación de App.js e instalación de módulos

Antes de implementar la aplicación en Azure, debemos
realizar algunas modificaciones menores.

1.  Cambie el nombre del archivo **index.js** a **app.js**. Esto permite a Azure detectar que se trata de una aplicación Node.js.

1.  Abra el archivo **app.js** en el Bloc de notas u otro editor de texto.

2.  Busque la sección **Dependencias de módulo** al comienzo del archivo app.js y cambie la línea que contiene `var io = require('../..')(server);` por `var io = require('socket.io')(server);` como se muestra a continuación:

		var express = require('express');
		var app = express();
		var server = require('http').createServer(app);
		// var io = require('../..')(server);
		var io = require('socket.io')(server);
		var port = process.env.PORT || 3000;


Después de guardar los cambios en app.js, siga estos pasos para
instalar los módulos necesarios::

1.  Desde la línea de comandos, cambie los directorios al directorio **\\node\\chat** y use el siguiente comando para instalar los módulos requeridos por esta aplicación:

        npm install

Esta acción instalará los módulos que se mencionan en el archivo package.json. Después de
    que el comando se completa, debería ver un resultado similar al
    siguiente:

	    express@3.4.8 node_modules\express
		├── methods@0.1.0
		├── merge-descriptors@0.0.1
		├── debug@0.8.1
		├── cookie-signature@1.0.1
		├── range-parser@0.0.4
		├── fresh@0.2.0
		├── buffer-crc32@0.2.1
		├── cookie@0.1.0
		├── mkdirp@0.3.5
		├── commander@1.3.2 (keypress@0.1.0)
		├── send@0.1.4 (mime@1.2.11)
		└── connect@2.12.0 (uid2@0.0.3, pause@0.0.1, qs@0.6.6, bytes@0.2.1, raw-body@1.1.2, batch@0.5.0, negotiator@0.3.0, multiparty@2.2.0)

2.  Dado que este ejemplo originalmente formaba parte del repositorio GitHub de Socket.IO
    y se hace referencia directamente a la biblioteca de Socket.IO por parte de la
    ruta de acceso relativa, no se hace referencia a Socket.IO en el archivo package.json,
    por lo que debemos instalarlo ejecutando el comando siguiente:

        npm install socket.io@1.0.6 -save

	> [WACOM.NOTE] Aunque versiones más recientes de Socket.IO pueden funcionar con los pasos incluidos en este artículo, realmente se comprobaron con la versión 1.0.6.

## <a id="Publish"></a>Creación de un sitio web de Azure

Siga estos pasos para crear un sitio web de Azure, habilite la publicación con Git y, a continuación, habilite la compatibilidad de WebSocket para el sitio web.

> [WACOM.NOTE]Para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener información, consulte <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Azure Free Trial</a>.

1. From the command-line, change directories to the **\\node\chat** directory and use the following command to create a new Azure Website and enable a Git repository for the website and the local directory. This will also create a Git remote named 'azure'.

		azure site create mysitename --git

	You must replace 'mysitename' with a unique name for your website.

2. Commit the existing files to the local repository by using the following commands:

		git add .
		git commit -m "Initial commit"

3. Push the files to the Azure Website repository with the following command:

		git push azure master

	You will receive status messages as modules are imported on the server. Once this process has completed, the application will be hosted on your Azure Website.

 	> [WACOM.NOTE] During module installation, you may notice errors that 'The imported project ... was not found'. These can safely be ignored.

4. Socket.IO uses WebSockets, which are not enabled by default on Azure. To enable web sockets, use the following command:

		azure site set -w

	If prompted, enter the name of the website.

	>[WACOM.NOTE]
	>The 'azure site set -w' command will only work with version 0.7.4 or higher of the Azure Cross-Platform Command-Line Interface. You can also enable WebSocket support using the Azure Management Portal.
	>
	>To enable WebSockets using the [Azure Management Portal](https://manage.windowsazure.com), select the Configure page for your website, select 'ON' for the Web Sockets entry, and then click Save.
	>	
	>![websockets](./media/web-sites-nodejs-chat-app-socketio/websockets.png)
	
5. To view the website on Azure, use the following command to launch your web browser and navigate to the hosted website:

		azure site browse

Your application is now running on Azure, and can relay chat
messages between different clients using Socket.IO.

> [WACOM.NOTE] For simplicity, this sample is limited to chatting between users connected to the same instance. This means that if the cloud service creates two worker role instances, users will only be able to chat with others connected to the same worker role instance. To scale the application to work with multiple role instances, you could use a technology like Service Bus to share the Socket.IO store state across instances. For examples, see the Service Bus Queues and Topics usage samples in the <a href="https://github.com/WindowsAzure/azure-sdk-for-node">Azure SDK for Node.js GitHub repository</a>.

##Scale out

Socket.IO applications can be scaled out by using an __adapter__ to distribute messages and events between multiple application instances. While there are several adapters available, the [socket.io-redis](https://github.com/automattic/socket.io-redis) adapter can be easily used with the Azure Redis Cache feature.

> [WACOM.NOTE] An additional requirement for scaling out a Socket.IO solution is support for sticky sessions. Sticky sessions are enabled by default for Azure Websites through Azure Request Routing. For more information, see [Instance Affinity in Azure Web Sites](http://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/)

###Create a Redis cache

Perform the steps in [Create a cache in Azure Redis Cache](http://go.microsoft.com/fwlink/p/?linkid=398592&clcid=0x409) to create a new cache.

> [WACOM.NOTE] Save the __Host name__ and __Primary key__ for your cache, as these will be needed in the next steps.

###Add the redis and socket.io-redis modules

1. From a command-line, change to the __\\node\\chat__ directory and use the following command.

		npm install socket.io-redis@0.1.3 redis@0.11.0 --save

	> [WACOM.NOTE] The versions specified in this command are the versions used when testing this article.

2. Modify the __app.js__ file to add the following lines immediately after `var io = require('socket.io')(server);`

		var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
		var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
		
		var redis = require('socket.io-redis');
		io.adapter(redis({pubClient: pub, subClient: sub}));


	Replace __redishostname__ and __rediskey__ with the host name and key for your Redis cache.

	This will create a publish and subscribe client to the Redis cache created previously. The clients are then used with the adapter to configure Socket.IO to use the Redis cache for passing messages and events between instances of your application

	> [WACOM.NOTE] While the __socket.io-redis__ adapter can communicate directly to Redis, the current version (as of 7/14/2014) does not support the authentication required by Azure Redis cache. So the initial connection is created using the __redis__ module, then the client is passed to the __socket.io-redis__ adapter.
	> 
	> While Azure Redis Cache supports secure connections using port 6380, the modules used in this example do not support secure connections as of 7/14/2014. The above code uses the default, unsecure port of 6380.

3. Save the modified __app.js__

###Commit changes and redeploy

From the command-line in the __\\node\\chat__ directory, use the following commands to commit changes and redeploy the application.

	git add .
	git commit -m "implementing scale out"
	git push azure master

Once the changes have been pushed to the server, you can scale your site across multiple instances by using the following command.

	azure site scale instances --instances #

Where __#__ is the number of instances to create. 

You can connect to your website from multiple browsers or computers to verify that messages are correctly sent to all clients.

##<a id="tshooting"></a>Troubleshooting

###Connection limits

Azure Websites is available in multiple SKUs, which determine the resources available to your site. This includes the number of allowed WebSocket connections. For more information, see the [Web Sites Pricing page][pricing].

###Messages aren't being sent using WebSockets

If client browsers keep falling back to long polling instead of using WebSockets, it may be because of one of the following.

* **Try limiting the transport to just WebSockets**

	In order for Socket.IO to use WebSockets as the messaging transport, both the server and client must support WebSockets. If one or the other does not, Socket.IO will negotiate another transport, such as long polling. The default list of transports used by Socket.IO is ` websocket, htmlfile, xhr-polling, jsonp-polling`. You can force it to only use WebSockets by adding the following code to the **app.js** file, after the line containing `, nicknames = {};`.

		io.configure(function() {
		  io.set('transports', ['websocket']);
		});

	> [WACOM.NOTE] Note that older browsers that do not support WebSockets will not be able to connect to the site while the above code is active, as it restricts communication to WebSockets only.

* **Use SSL**

	WebSockets relies on some lesser used HTTP headers, such as the **Upgrade** header. Some intermediate network devices, such as web proxies, may remove these headers. To avoid this problem, you can establish the WebSocket connection over SSL.

	An easy way to accomplish this is to configure Socket.IO to `match origin protocol`. This instructs Socket.IO to secure WebSockets communication the same as the originating HTTP/HTTPS request for the web page. If a browser uses an HTTPS URL to visit your website, subsequent WebSocket communications through Socket.IO will be secured over SSL.

	To modify this example to enable this configuration, add the following code to the **app.js** file after the line containing `, nicknames = {};`.

		io.configure(function() {
		  io.set('match origin protocol', true);
		});

* **Verify web.config settings**

	Azure Websites that host Node.js applications use the **web.config** file to route incoming requests to the Node.js application. For WebSockets to function correctly with Node.js applications, the **web.config** must contain the following entry.

		<webSocket enabled="false"/>

	Esto deshabilita el módulo WebSockets de IIS, que incluye su propia implementación de WebSockets y está en conflicto con los módulos WebSocket específicos de Node.js, como por ejemplo Socket.IO. Si esta línea no está presente, o si está configurada en `true`, esto puede ser el motivo por el que el transporte WebSocket no funciona para su aplicación.

Normalmente, las aplicaciones Node.js no incluyen un archivo **web.config**, por lo que Sitios web Azure automáticamente generará uno para las aplicaciones Node.js cuando se implementan. Como este archivo se genera automáticamente en el servidor, debe utilizar la URL FTP o FTPS en su sitio web para ver este archivo. Puede encontrar URL FTP y FTPS para su sitio en el Portal de administración de Azure si selecciona el sitio web y a continuación el vínculo **Dashboard**. Las URL se muestran en la sección **vista rápida**.

	> [WACOM.NOTE]Sitios web Azure solo genera el archivo **web.config** si la aplicación no proporciona uno. Sitios web Azure utilizará el archivo **web.config** en la raíz de su proyecto de aplicación si proporciona uno.

Si la entrada no está presente, o si se ha ajustado en el valor `true`, entonces deberá crear un **web.config** en la raíz de su aplicación Node.js y especificar un valor de `false`.  Como referencia a continuación mostramos el **web.config** predeterminado para una aplicación que utiliza **app.js** como punto de entrada.

		<?xml version="1.0" encoding="utf-8"?>
		<!--
		     This configuration file is required if iisnode is used to run node processes behind
		     IIS or IIS Express.  For more information, visit:
		
		     https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config
		-->
		
		<configuration>
		  <system.webServer>
		    <!-- Visit http://blogs.msdn.com/b/windowsazure/archive/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites.aspx for more information on WebSocket support -->
		    <webSocket enabled="false" />
		    <handlers>
		      <!-- Indicates that the server.js file is a node.js site to be handled by the iisnode module -->
		      <add name="iisnode" path="app.js" verb="*" modules="iisnode"/>
		    </handlers>
		    <rewrite>
		      <rules>
		        <!-- Do not interfere with requests for node-inspector debugging -->
		        <rule name="NodeInspector" patternSyntax="ECMAScript" stopProcessing="true">
		          <match url="^app.js\/debug[\/]?" />
		        </rule>
		
		        <!-- First we consider whether the incoming URL matches a physical file in the /public folder -->
		        <rule name="StaticContent">
		          <action type="Rewrite" url="public{REQUEST_URI}"/>
		        </rule>
		
		        <!-- All other URLs are mapped to the node.js site entry point -->
		        <rule name="DynamicContent">
		          <conditions>
		            <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="True"/>
		          </conditions>
		          <action type="Rewrite" url="app.js"/>
		        </rule>
		      </rules>
		    </rewrite>
		    <!--
		      You can control how Node is hosted within IIS using the following options:
		        * watchedFiles: semi-colon separated list of files that will be watched for changes to restart the server
		        * node_env: will be propagated to node as NODE_ENV environment variable
		        * debuggingEnabled - controls whether the built-in debugger is enabled
		
		      See https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config for a full list of options
		    -->
		    <!--<iisnode watchedFiles="web.config;*.js"/>-->
		  </system.webServer>
		</configuration>

	> [WACOM.NOTE] Si la aplicación utiliza un punto de entrada distinto de **app.js**, deberá sustituir todas las ocurrencias de **app.js** por el punto de entrada correcto. Por ejemplo, sustituyendo **app.js** por **server.js**.

##Pasos siguientes

En este tutorial ha aprendido a crear una aplicación de chat hospedada en un sitio web de Azure. También puede hospedar esta aplicación como un servicio en la nube de Azure. Para conocer los pasos y así llevarlo a cabo, consulte [Creación de una aplicación de chat Node.js con Socket.IO en un servicio en la nube de Azure][cloudservice].

[socketio]: http://socket.io/
[completed-app]: ./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png
[Socket.IO GitHub repository]: https://github.com/Automattic/socket.io
[release]: https://github.com/Automattic/socket.io/releases
[cloudservice]: /es-es/develop/nodejs/tutorials/app-using-socketio/

[chat-example-view]: ./media/web-sites-nodejs-chat-app-socketio/socketio-2.png
[npm-output]: ./media/web-sites-nodejs-chat-app-socketio/socketio-7.png
[pricing]: /es-es/pricing/details/web-sites/
