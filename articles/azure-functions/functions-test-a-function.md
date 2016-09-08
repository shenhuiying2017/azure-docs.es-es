<properties
   pageTitle="Prueba de las funciones de Azure | Microsoft Azure"
   description="Pruebe las funciones de Azure con Postman, cURL y Node.js."
   services="functions"
   documentationCenter="na"
   authors="wesmc7777"
   manager="erikre"
   editor=""
   tags=""
   keywords="funciones de Azure, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/19/2016"
   ms.author="wesmc"/>

# Prueba de las funciones de Azure

## Información general

En este tutorial, lo guiaremos por los diferentes enfoques para la prueba de funciones. Definiremos una función de desencadenador de HTTP que acepta la entrada a través de un parámetro de cadena de consulta o el cuerpo de la solicitud. El código de plantilla **HttpTrigger Node.js Function** predeterminado admite un parámetro de cadena de consulta `name`. Además, agregaremos código para admitir ese parámetro junto con información sobre `address` para el usuario en el cuerpo de la solicitud.

## Creación de una función para realizar pruebas

Durante la mayor parte de este tutorial, utilizaremos una versión ligeramente modificada de la plantilla **HttpTrigger Nodejs Function** que está disponible al crear una nueva función. Puede revisar el [tutorial Create your first Azure Function](functions-create-first-azure-function.md) (Creación de su primera función de Azure) si necesita ayuda para crear una nueva función. Elija la plantilla **HttpTrigger Node.js Function** al crear la función de prueba en el [Portal de Azure].

Básicamente, la plantilla de función predeterminada es una función de tipo "Hola a todos" que devuelve el nombre desde el cuerpo de la solicitud o el parámetro de cadena de consulta, `name=<your name>`. Actualizaremos el código para que también pueda proporcionar el nombre y una dirección como contenido JSON en el cuerpo de la solicitud. A continuación, la función los enviará de vuelta al cliente cuando estén disponibles.

Actualice la función con el siguiente código que usamos para las pruebas:

	module.exports = function(context, req) {
	    context.log("Node.js HTTP trigger function processed a request. RequestUri=%s", req.originalUrl);
	    context.log("Request Headers = " + JSON.stringify(req.headers));    
	
	    if (req.query.name || (req.body && req.body.name)) {
	        if (typeof req.query.name != "undefined") {
	            context.log("Name was provided as a query string param..."); 
	            ProcessNewUserInformation(context, req.query.name);
	        }
	        else {
	            context.log("Processing user info from request body..."); 
	            ProcessNewUserInformation(context, req.body.name, req.body.address);
	        }
	    }
	    else {
	        context.res = {
	            status: 400,
	            body: "Please pass a name on the query string or in the request body"
	        };
	    }
	    context.done();
	};
	
	function ProcessNewUserInformation(context, name, address)
	{    
	    context.log("Processing User Information...");            
	    context.log("name = " + name);            
	    echoString = "Hello " + name;
	    
	    if (typeof address != "undefined")
	    {
	        echoString += "\n" + "The address you provided is " + address;
	        context.log("address = " + address);            
	    }
	    
	    context.res = {
	            // status: 200, /* Defaults to 200 */
	            body: echoString
	        };
	}


## Prueba de una función con herramientas

### Prueba con cURL

A menudo, al probar software, basta con la línea de comandos para depurar la aplicación, algo que también ocurre con las funciones.

Para probar la función anterior, copie la **dirección URL de la función** del portal. Tendrá este formato:

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>
    
Se trata de la dirección URL que desencadena la función; para probarla, podemos usar el comando cURL en la línea de comandos para enviar una solicitud Get (`-G` o `--get`) a nuestra función:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>
    
En el ejemplo anterior, se requiere un parámetro de cadena de consulta que se pueda pasar como datos (`-d`) en el comando cURL:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code> -d name=<Enter a name here>
    
Presione ENTRAR y verá el resultado de la función en la línea de comandos.

![](./media/functions-test-a-function/curl-test.png)

En la ventana **Registros** del portal, se registra una salida similar a la siguiente mientras se ejecuta la función:

    2016-04-05T21:55:09  Welcome, you are now connected to log-streaming service.
    2016-04-05T21:55:30.738 Function started (Id=ae6955da-29db-401a-b706-482fcd1b8f7a)
    2016-04-05T21:55:30.738 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/HttpTriggerNodeJS1?code=XXXXXXX&name=Azure Functions
    2016-04-05T21:55:30.738 Function completed (Success, Id=ae6955da-29db-401a-b706-482fcd1b8f7a)

### Prueba con un explorador

Las funciones que no requieren parámetros, o que solo necesitan parámetros de cadena de consulta, se pueden probar con un explorador.

Para probar la función que definimos antes, copie la **dirección URL de la función** del portal. Tendrá este formato:

	https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Anexe el parámetro de cadena de consulta `name` como sigue, con un nombre real en lugar del marcador de posición `<Enter a name here>`.

	https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>&name=<Enter a name here>

Pegue la dirección URL en el explorador y obtendrá una respuesta similar a la siguiente.

![](./media/functions-test-a-function/browser-test.png)

En la ventana **Registros** del portal, se registra una salida similar a la siguiente mientras se ejecuta la función:

	2016-03-23T07:34:59  Welcome, you are now connected to log-streaming service.
	2016-03-23T07:35:09.195 Function started (Id=61a8c5a9-5e44-4da0-909d-91d293f20445)
	2016-03-23T07:35:10.338 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==&name=Wes from a browser
	2016-03-23T07:35:10.338 Request Headers = {"cache-control":"max-age=0","connection":"Keep-Alive","accept":"text/html","accept-encoding":"gzip","accept-language":"es-ES"}
	2016-03-23T07:35:10.338 Name was provided as a query string param.
	2016-03-23T07:35:10.338 Processing User Information...
	2016-03-23T07:35:10.369 Function completed (Success, Id=61a8c5a9-5e44-4da0-909d-91d293f20445)

### Prueba con Postman

La herramienta recomendada para probar la mayoría de las funciones es Postman. Para instalar Postman, consulte [Postman](https://www.getpostman.com/). Postman proporciona control sobre muchos más atributos de una solicitud HTTP.

> [AZURE.TIP] Use el cliente REST que le resulte más cómodo. Estas son algunas alternativas a Postman:
> 
> * [Fiddler](http://www.telerik.com/fiddler)
> * [Paw](https://luckymarmot.com/paw)

Para probar la función con un cuerpo de solicitud en Postman:

1. Inicie Postman con el botón **Aplicaciones** en la esquina superior izquierda de una ventana del explorador Chrome.
2. Copie la **dirección URL de la función** y péguela en Postman. Incluye el parámetro de cadena de consulta de código de acceso.
3. Cambie el método HTTP a **POST**.
4. Haga clic en **Body** > **raw** (Cuerpo > sin formato) y agregue el cuerpo de la solicitud JSON que se parece al siguiente:

		{
		    "name" : "Wes testing with Postman",
		    "address" : "Seattle, W.A. 98101"
		}

5. Haga clic en **Enviar**.

La siguiente imagen muestra la prueba del ejemplo simple de función de eco en este tutorial.

![](./media/functions-test-a-function/postman-test.png)

En la ventana **Registros** del portal, se registra una salida similar a la siguiente mientras se ejecuta la función:

	2016-03-23T08:04:51  Welcome, you are now connected to log-streaming service.
	2016-03-23T08:04:57.107 Function started (Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)
	2016-03-23T08:04:57.763 Node.js HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==
	2016-03-23T08:04:57.763 Request Headers = {"cache-control":"no-cache","connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"es-ES"}
	2016-03-23T08:04:57.763 Processing user info from request body...
	2016-03-23T08:04:57.763 Processing User Information...
	2016-03-23T08:04:57.763 name = Wes testing with Postman
	2016-03-23T08:04:57.763 address = Seattle, W.A. 98101
	2016-03-23T08:04:57.795 Function completed (Success, Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)
    
### Prueba de un desencadenador de blob mediante el Explorador de almacenamiento

Puede probar una función de desencadenador de blob mediante el [Explorador de Almacenamiento de Microsoft Azure](http://storageexplorer.com/).

1. En el [Portal de Azure] para el contenedor de funciones, cree una nueva función de desencadenador de blob con Node o C#. Establezca la ruta de acceso que se va a supervisar en el nombre de su contenedor de blobs. Por ejemplo:

		files

2. Haga clic en el botón **+** para seleccionar o crear la cuenta de almacenamiento que desea usar. A continuación, haga clic en **Crear**.

3. Cree un archivo de texto con el siguiente texto y guárdelo:

		A text file for blob trigger function testing.

4. Ejecute el [Explorador de Almacenamiento de Microsoft Azure](http://storageexplorer.com/) y conéctese al contenedor de blobs en la cuenta de almacenamiento que se está supervisando.

5. Haga clic en el botón **Cargar** y cargue el archivo de texto.

	![](./media/functions-test-a-function/azure-storage-explorer-test.png)


	El código de la función de desencadenador de blobs predeterminada notificará el procesamiento del blob en los registros:

		2016-03-24T11:30:10  Welcome, you are now connected to log-streaming service.
		2016-03-24T11:30:34.472 Function started (Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)
		2016-03-24T11:30:34.472 C# Blob trigger function processed: A text file for blob trigger function testing.
		2016-03-24T11:30:34.472 Function completed (Success, Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)

## Prueba de una función dentro de funciones

### Prueba con el botón Ejecutar del portal de funciones

El portal proporciona un botón **Ejecutar** que le permitirá realizar algunas pruebas limitadas. Puede proporcionar un cuerpo de solicitud por medio del botón Ejecutar pero no puede proporcionar parámetros de cadena de consulta ni encabezados de solicitud de actualización.

Pruebe la función de desencadenador de HTTP que creamos antes agregando una cadena JSON similar a la siguiente en el campo **Cuerpo de la solicitud** y después haga clic en el botón **Ejecutar**.

	{
		"name" : "Wes testing Run button",
		"address" : "USA"
	} 

En la ventana **Registros** del portal, se registra una salida similar a la siguiente mientras se ejecuta la función:

	2016-03-23T08:03:12  Welcome, you are now connected to log-streaming service.
	2016-03-23T08:03:17.357 Function started (Id=753a01b0-45a8-4125-a030-3ad543a89409)
	2016-03-23T08:03:18.697 Node.js HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/wesmchttptriggernodejs1
	2016-03-23T08:03:18.697 Request Headers = {"connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"es-ES"}
	2016-03-23T08:03:18.697 Processing user info from request body...
	2016-03-23T08:03:18.697 Processing User Information...
	2016-03-23T08:03:18.697 name = Wes testing Run button
	2016-03-23T08:03:18.697 address = USA
	2016-03-23T08:03:18.744 Function completed (Success, Id=753a01b0-45a8-4125-a030-3ad543a89409)


### Prueba con un desencadenador de temporizador

Algunas funciones realmente no se pueden probar con las herramientas ya mencionadas. Por ejemplo, una función de desencadenador de cola que se ejecuta cuando se coloca un mensaje en el [Almacenamiento en cola de Azure](../storage/storage-dotnet-how-to-use-queues.md). Una posible opción es escribir código para colocar un mensaje en la cola; a continuación, se proporciona un ejemplo de esto en un proyecto de consola. Sin embargo, hay otro método que sirve para probar las funciones directamente.

Se podría usar un desencadenador de temporizador configurado con un enlace de salida de cola. Después, ese código de desencadenador de temporizador escribiría los mensajes de prueba en la cola. En esta sección lo guiaremos por un ejemplo.

Para ver información más detallada sobre el uso de enlaces con funciones de Azure, consulte la [referencia para desarrolladores de Funciones de Azure](functions-reference.md).


#### Creación del desencadenador de cola para las pruebas

Para demostrar este enfoque, en primer lugar crearemos una función de desencadenador de cola en la que se va a probar una cola llamada `queue-newusers`. Esta función procesará la información de nombre y dirección para un nuevo usuario que se coloca en el Almacenamiento en cola de Azure.

> [AZURE.NOTE] Si usa un nombre de cola diferente, asegúrese de que se ajuste a las reglas en [Asignar nombres a colas y metadatos](https://msdn.microsoft.com/library/dd179349.aspx). De lo contrario, obtendrá un código de estado HTTP 400: Solicitud incorrecta.

1. En el [Portal de Azure] para el contenedor de funciones, haga clic en **Nueva función** > **QueueTrigger - C#**.
2. Escriba el nombre de cola que se va a supervisar con la función de cola.

		queue-newusers 

3. Haga clic en el botón **+** (agregar) para seleccionar o crear la cuenta de almacenamiento que desea usar. A continuación, haga clic en **Crear**.
4. Deje esta ventana del explorador del portal abierta para poder supervisar las entradas del registro para el código de la plantilla de función de cola predeterminada.


#### Creación de un desencadenador de temporizador para colocar un mensaje en la cola

1. Abra el [Portal de Azure] en una nueva ventana del explorador y vaya al contenedor de funciones.
2. Haga clic en **Nueva función** > **TimerTrigger - C#**. Escriba una expresión cron para establecer la frecuencia con que el código del temporizador ejecutará las pruebas de la función de cola. A continuación, haga clic en **Crear**. Si desea que la prueba se ejecute cada 30 segundos, puede utilizar la siguiente [expresión CRON](https://wikipedia.org/wiki/Cron#CRON_expression):

		*/30 * * * * *


2. Haga clic en la pestaña **Integrar** para el nuevo desencadenador de temporizador.
3. En **Salida**, haga clic en el botón **+ Nueva salida**. Después, haga clic en **cola** y en el botón **Seleccionar**.
4. Anote el nombre que use para el **objeto de mensaje de cola**; lo utilizará en el código de la función de temporizador.

		myQueue

4. Escriba el nombre de la cola adonde se enviará el mensaje:

		queue-newusers 

3. Haga clic en el botón **+** (agregar) para seleccionar la cuenta de almacenamiento que usó antes con el desencadenador de cola. A continuación, haga clic en **Guardar**.
4. Haga clic en la pestaña **Desarrollar** del desencadenador de temporizador.
5. Puede usar el siguiente código para la función de temporizador de C#, siempre que use el mismo nombre de objeto de mensaje de cola mostrado antes. A continuación, haga clic en **Guardar**.

		using System;
		
		public static void Run(TimerInfo myTimer, out String myQueue, TraceWriter log)
		{
		    String newUser = 
		    "{"name":"User testing from C# timer function","address":"XYZ"}";
		
		    log.Verbose($"C# Timer trigger function executed at: {DateTime.Now}");   
		    log.Verbose($"{newUser}");   
		    
		    myQueue = newUser;
		}

En este momento, la función de temporizador de C# se ejecutará cada 30 segundos si usó la expresión cron de ejemplo. Los registros para la función de temporizador notificarán cada ejecución:

	2016-03-24T10:27:02  Welcome, you are now connected to log-streaming service.
	2016-03-24T10:27:30.004 Function started (Id=04061790-974f-4043-b851-48bd4ac424d1)
	2016-03-24T10:27:30.004 C# Timer trigger function executed at: 3/24/2016 10:27:30 AM
	2016-03-24T10:27:30.004 {"name":"User testing from C# timer function","address":"XYZ"}
	2016-03-24T10:27:30.004 Function completed (Success, Id=04061790-974f-4043-b851-48bd4ac424d1)

En la ventana del explorador para la función de cola, verá cada mensaje que se está procesando:

	2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
	2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
	2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"User testing from C# timer function","address":"XYZ"}
	2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
	
## Prueba de una función con código

### Prueba de una función de desencadenador de HTTP con código: Node.js

Puede usar el código de Node.js para ejecutar una solicitud HTTP para probar la función de Azure.

Asegúrese de establecer lo siguiente:

- El `host` en las opciones de solicitud en el host del contenedor de funciones.
- El nombre de la función en `path`.
- El código de acceso (`<your code>`) en `path`.

Ejemplo de código:

	var http = require("http");
	
	var nameQueryString = "name=Wes%20Query%20String%20Test%20From%20Node.js";
	
	var nameBodyJSON = {
	    name : "Wes testing with Node.JS code",
	    address : "Dallas, T.X. 75201"
	};
	
	var bodyString = JSON.stringify(nameBodyJSON);
	
	var options = {
	  host: "functions841def78.azurewebsites.net",
	  //path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9&" + nameQueryString,
	  path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9",
	  method: "POST",
	  headers : {
	      "Content-Type":"application/json",
	      "Content-Length": Buffer.byteLength(bodyString)
	    }    
	};
	
	callback = function(response) {
	  var str = ""
	  response.on("data", function (chunk) {
	    str += chunk;
	  });
	
	  response.on("end", function () {
	    console.log(str);
	  });
	}
	
	var req = http.request(options, callback);
	console.log("*** Sending name and address in body ***");
	console.log(bodyString);
	req.end(bodyString);



Salida:

	C:\Users\Wesley\testing\Node.js>node testHttpTriggerExample.js
	*** Sending name and address in body ***
	{"name" : "Wes testing with Node.JS code","address" : "Dallas, T.X. 75201"}
	Hello Wes testing with Node.JS code
	The address you provided is Dallas, T.X. 75201
		
En la ventana **Registros** del portal, se registra una salida similar a la siguiente mientras se ejecuta la función:

	2016-03-23T08:08:55  Welcome, you are now connected to log-streaming service.
	2016-03-23T08:08:59.736 Function started (Id=607b891c-08a1-427f-910c-af64ae4f7f9c)
	2016-03-23T08:09:01.153 Node.js HTTP trigger function processed a request. RequestUri=http://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1/?code=XXXXXXXXXX==
	2016-03-23T08:09:01.153 Request Headers = {"connection":"Keep-Alive","host":"functionsExample.azurewebsites.net"}
	2016-03-23T08:09:01.153 Name not provided as query string param. Checking body...
	2016-03-23T08:09:01.153 Request Body Type = object
	2016-03-23T08:09:01.153 Request Body = [object Object]
	2016-03-23T08:09:01.153 Processing User Information...
	2016-03-23T08:09:01.215 Function completed (Success, Id=607b891c-08a1-427f-910c-af64ae4f7f9c)
    

### Prueba de una función de desencadenador de cola con código: C# #

Ya hemos mencionado que puede probar un desencadenador de cola usando código para colocar un mensaje en la cola. El siguiente ejemplo de código se basa en el código C# que se presenta en el tutorial [Introducción al Almacenamiento en cola de Azure mediante .NET](../storage/storage-dotnet-how-to-use-queues.md). También se ofrece código para otros lenguajes en ese vínculo.

Para probar este código en una aplicación de consola, debe:

- [Configurar la cadena de conexión de almacenamiento en el archivo app.config](../storage/storage-dotnet-how-to-use-queues.md#setup-a-storage-connection-string).
- Este código acepta el nombre y la dirección de un nuevo usuario como argumentos de la línea de comandos durante el tiempo de ejecución. Pase `name` y `address` como parámetros a la aplicación. Por ejemplo: `C:\myQueueConsoleApp\test.exe "Wes testing queues" "in a console app"`.


Ejemplo de código C#:

    static void Main(string[] args)
    {
        string name = null;
        string address = null;
        string queueName = "queue-newusers";
        string JSON = null;

        if (args.Length > 0)
        {
            name = args[0];
        }
        if (args.Length > 1)
        {
            address = args[1];
        }

        // Retrieve storage account from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["StorageConnectionString"]);

        // Create the queue client
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

        // Retrieve a reference to a queue
        CloudQueue queue = queueClient.GetQueueReference(queueName);

        // Create the queue if it doesn't already exist
        queue.CreateIfNotExists();

        // Create a message and add it to the queue.
        if (name != null)
        {
            if (address != null)
                JSON = String.Format("{{"name":"{0}","address":"{1}"}}", name, address);
            else
                JSON = String.Format("{{"name":"{0}"}}", name);
        }

        Console.WriteLine("Adding message to " + queueName + "...");
        Console.WriteLine(JSON);

        CloudQueueMessage message = new CloudQueueMessage(JSON);
        queue.AddMessage(message);
    }

En la ventana del explorador para la función de cola, verá cada mensaje que se está procesando:

	2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
	2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
	2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"Wes testing queues","address":"in a console app"}
	2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)


<!-- URLs. -->

[Portal de Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0824_2016-->