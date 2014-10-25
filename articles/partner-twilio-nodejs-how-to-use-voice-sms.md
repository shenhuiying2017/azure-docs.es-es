<properties linkid="dev-nodejs-how-to-twilio-voice-sms-service" urlDisplayName="Twilio Voice and SMS Service" pageTitle="Using Twilio for Voice, VoIP, and SMS Messaging in Azure" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="Node.js" title=" VoIP" authors="MicrosoftHelp@twilio.com" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com"/>

# Uso de Twilio para voz, VoIP y mensajería SMS en Azure

Esta guía demuestra cómo crear aplicaciones que se comunican con Twilio y Node.js en Azure.

## Tabla de contenido

-   [¿Qué es Twilio?][]
-   [Suscripción en Twilio (con descuento de Microsoft)][]
-   [Creación e implementación de un sitio web de Azure para Node.js][]
-   [Configuración del módulo de Twilio][]
-   [Realización de una llamada saliente][]
-   [Envío de un mensaje SMS][]
-   [Pasos siguientes][]

<span id="whatis"></span></a>

## ¿Qué es Twilio?

Twilio es una plataforma de API que hace que los desarrolladores puedan hacer y recibir llamadas telefónicas, enviar y recibir mensajes de texto e insertar llamadas de VoIP en aplicaciones móviles nativas o basadas en explorador de manera sencilla. Revisemos brevemente su funcionamiento antes de analizar los detalles.

### Recepción de llamadas y mensajes de texto

Twilio permite que los desarrolladores [compren números de teléfono programables][] que se pueden usar para enviar y recibir llamadas y mensajes de texto. Cuando un número de Twilio recibe una llamada o un texto entrante, Twilio le enviará a la aplicación web una solicitud HTTP, POST o GET, pidiéndole instrucciones sobre cómo controlar la llamada o el texto. El servidor responderá la solicitud HTTP de Twilio con [TwiML][], un conjunto simple de etiquetas XML que contienen instrucciones sobre cómo controlar una llamada o texto. Veremos ejemplos de TwiML en un momento.

### Realización de llamadas y envío de mensajes de texto

Al realizar solicitudes HTTP a la API del servicio web de Twilio, los desarrolladores pueden enviar mensajes de texto o iniciar llamadas telefónicas salientes. En el caso de las llamadas salientes, el desarrollador también debe especificar una dirección URL que devuelve instrucciones de TwiML sobre cómo controlar una llamada saliente una vez conectada.

### Inserción de funcionalidades de VoIP en código de interfaz de usuario (JavaScript, iOS o Android)

Twilio brinda un SDK de cliente que puede transformar cualquier explorador web de escritorio, aplicación iOS o aplicación Android en un teléfono de VoIP. En este artículo, nos centraremos en cómo usar la llamada de VoIP en el explorador. Además del SDK de JavaScript de Twilio que se ejecuta en el explorador, se debe usar una aplicación de servidor (nuestra aplicación Node.js) para emitir un "token de funcionalidad" al cliente de JavaScript. Lea más sobre el uso de VoIP con Node.js [en el blog de desarrolladores de Twilio][] (en inglés).

<span id="signup"></span></a>

## Suscripción en Twilio (con descuento de Microsoft)

Antes de usar servicios de Twilio, primero debe [suscribirse para obtener una cuenta][]. Los clientes de Azure reciben un descuento especial; [asegúrese de suscribirse aquí][suscribirse para obtener una cuenta].

<span id="azuresite"></span></a>

## Creación e implementación de un sitio web de Azure para Node.js

A continuación, deberá crear un sitio web de Node.js para que se ejecute en Azure. [La documentación oficial para hacerlo está aquí][]. En un alto nivel, hará lo siguiente:

-   Suscripción para obtener una cuenta de Azure, si todavía no tiene una
-   Uso de la consola de administración de Azure para crear un sitio web
-   Incorporación de compatibilidad con control de código fuente (supondremos que usó Git)
-   Creación de un archivo `server.js` con una simple aplicación web para Node.js
-   Implementación de esta aplicación simple en Azure

<span id="twiliomodule"></span></a>

## Configuración del módulo de Twilio

A continuación, comenzaremos a escribir una aplicación simple de Node.js que usa la API de Twilio. Antes de comenzar, necesitamos configurar nuestras credenciales para la cuenta de Twilio.

### Configuración de credenciales de Twilio en variables de entorno del sistema

A fin de realizar solicitudes autenticadas contra el back-end de Twilio, necesitamos el token de autenticación y el SID de nuestra cuenta, que funciona como nombre de usuario y contraseña definidos para la cuenta de Twilio. La forma más segura de configurarlos para su uso con el módulo de nodo en Azure es a través de variables de entorno del sistema, que puede definir directamente en la consola de administración de Azure.

Seleccione el sitio web de Node.js y haga clic en el vínculo "CONFIGURAR". Si se desplaza un poco hacia abajo, verá un área en la que puede definir las propiedades de configuración para la aplicación. Escriba las credenciales de su cuenta de Twilio ([que se encuentran en el panel de Twilio][]) como se muestra; asegúrese de llamarlas "TWILIO\_ACCOUNT\_SID" y "TWILIO\_AUTH\_TOKEN", respectivamente:

![Consola de administración de Azure][]

Una vez que ha configurado estas variables, reinicie la aplicación en la consola de Azure.

### Declaración del módulo de Twilio en package.json

A continuación, debemos crear un archivo package.json para administrar nuestras dependencias de módulo de nodo vía [npm][]. En el mismo nivel del archivo "server.js" que creó en el tutorial de Azure/Node.js, cree un archivo llamado "package.json". Dentro de este archivo coloque lo siguiente:

	{
	 "name": "application-name",
	 "version": "0.0.1",
	 "private": true,
	 "scripts": {
	 "start": "node server"
	 },
	 "dependencies": {
	 "express": "3.1.0",
	 "ejs": "*",
	 "twilio":"*"
	 }
	 }

Con esto, el módulo de Twilio se declara como una dependencia, al igual que el popular [express web framework][] y el motor de plantilla EJS. Ahora ya estamos: comencemos a escribir código.

<span id="makecall"></span></a>

## Realización de una llamada saliente

Creemos un formulario simple que realizará una llamada a un número de nuestra elección. Abra server.js y escriba el siguiente código. Observe que donde dice "CHANGE\_ME" debe poner el nombre del sitio web de Azure:

		// Dependencias de módulo
		 var express = require('express'),
		 path = require('path'),
		 http = require('http'),
		 twilio = require('twilio');
		
		// Crear aplicación web Express
		 var app = express();
		
		// Configuración Express
		 app.configure(function(){
		 app.set('port', process.env.PORT || 3000);
		 app.set('views', \_\_dirname + '/views');
		 app.set('view engine', 'ejs');
		 app.use(express.favicon());
		 app.use(express.logger('dev'));
		 app.use(express.bodyParser());
		 app.use(express.methodOverride());
		 app.use(app.router);
		 app.use(express.static(path.join(\_\_dirname, 'public')));
		 });
		 app.configure('development', function(){
		 app.use(express.errorHandler());
		 });
		
		// Generar una interfaz de usuario HTML para la página principal de la aplicación
		 app.get('/', function(request, response) {
		 response.render('index');
		 });
		
		// Controlar el formulario POST para realizar una llamada
		 app.post('/call', function(request, response) {
		 var client = twilio();
		 client.makeCall({
		 // llamar a este número
		 to:request.body.number,

          // Change to a Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // A URL in our app which generates TwiML
          // Change "CHANGE_ME" to your app's name
          url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });

		});
		
		// Generar TwiML para controlar una llamada saliente
		 app.post('/outbound\_call', function(request, response) {
		 var twiml = new twilio.TwimlResponse();
		
		      // Say a message to the call's receiver 
		      twiml.say('hello - thanks for checking out Twilio and Azure', {
		          voice:'woman'
		      });
		
		      response.set('Content-Type', 'text/xml');
		      response.send(twiml.toString());
		
		});
		
		// Servidor de inicio
		 http.createServer(app).listen(app.get('port'), function(){
		 console.log("Express server listening on port " + app.get('port'));
		 });

A continuación, crear un directorio llamado "views"; dentro de este directorio, crear un archivo llamado "index.ejs" con el siguiente contenido:

	\<!DOCTYPE html\>
	 \<html\>
	 \<head\>
	 \<title\>Twilio Test\</title\>
	 \<style\>
	 input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
	 \</style\>
	 \</head\>
	 \<body\>
	 \<h1\>Twilio Test\</h1\>
	 \<form action="/call" method="POST"\>
	 \<input placeholder="Escriba un número de teléfono" name="number"/\>
	 \<br/\>
	 \<input type="submit" value="Llame al número anterior"/\>
	 \</form\>
	 \</body\>
	 \</html\>

Ahora, implemente el sitio web en Azure y abra su página principal. Debe poder escribir el número de teléfono en el campo de texto y recibir una llamada desde el número de Twilio.

<span id="sendmessage"></span></a>

## Envío de un mensaje SMS

Ahora, configuremos una lógica de control de formulario e interfaz de usuario para enviar un mensaje de texto. Abra "server.js" y agregue el siguiente código después de la última llamada a "app.post":

		app.post('/sms', function(request, response) {
		 var client = twilio();
		 client.sendSms({
		 // enviar un texto a este número
		 to:request.body.number,

          // A Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // The body of the text message
          body: request.body.message
          
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });

	});

En "views/index.ejs", agregue otro formulario debajo del primero para enviar un número y un mensaje de texto:

	\<form action="/sms" method="POST"\>
	 \<input placeholder="Escribir un número de teléfono" name="number"/\>
	 \<br/\>
	 \<input placeholder="Escribir un mensaje para enviar" name="message"/\>
	 \<br/\>
	 \<input type="submit" value="Enviar texto al número anterior"/\>
	 \</form\>

Vuelva a implementar la aplicación en Azure y ahora debiera poder enviar ese formulario y enviarse usted mismo (o a cualquiera de sus amigos más cercanos) un mensaje de texto.

<span id="nextsteps"></span></a>

## Pasos siguientes

Ha aprendido los aspectos básicos relacionados con el uso de Node.js y Twilio para crear aplicaciones que se comuniquen. Pero estos ejemplos son solo una muestra muy pequeña de lo que es posible hacer con Twilio y Node.js. Si desea obtener más información sobre el uso de Twilio con Node.js, revise los siguientes recursos:

-   [Official module docs][]
-   [Tutorial on VoIP with node.js applications][en el blog de desarrolladores de Twilio]
-   [Votr - a real-time SMS voting application with node.js and CouchDB (three parts)][]
-   [Pair programming in the browser with node.js][]

Esperamos que disfrute del acceso a Node.js y Twilio en Azure.

  [¿Qué es Twilio?]: #whatis
  [Suscripción en Twilio (con descuento de Microsoft)]: #signup
  [Creación e implementación de un sitio web de Azure para Node.js]: #azuresite
  [Configuración del módulo de Twilio]: #twiliomodule
  [Realización de una llamada saliente]: #makecall
  [Envío de un mensaje SMS]: #sendmessage
  [Pasos siguientes]: #nextsteps
  [compren números de teléfono programables]: https://www.twilio.com/user/account/phone-numbers/available/local
  [TwiML]: https://www.twilio.com/docs/api/twiml
  [en el blog de desarrolladores de Twilio]: http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
  [suscribirse para obtener una cuenta]: http://ahoy.twilio.com/azure
  [La documentación oficial para hacerlo está aquí]: http://www.windowsazure.com/en-us/develop/nodejs/tutorials/create-a-website-(mac)/
  [que se encuentran en el panel de Twilio]: https://www.twilio.com/user/account
  [Consola de administración de Azure]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png
  [npm]: http://npmjs.org
  [express web framework]: http://expressjs.com
  [Official module docs]: http://twilio.github.io/twilio-node/
  [Votr - a real-time SMS voting application with node.js and CouchDB (three parts)]: http://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
  [Pair programming in the browser with node.js]: http://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
