<properties 
	pageTitle="Uso del servicio de colas (Node.js) | Microsoft Azure" 
	description="Aprenda a utilizar el servicio Cola de Azure para crear y eliminar colas e insertar, obtener y eliminar mensajes. Ejemplos escritos en Node.js." 
	services="storage" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="mwasson"/>





# Uso del servicio Cola desde Node.js

Esta guía le indicará cómo actuar en situaciones habituales usando el
de Azure de Windows. Los ejemplos están escritos usando la API
Python. Entre los escenarios descritos se incluyen la **inserción**, **inspección**,
**obtención** y **eliminación** de los mensajes en cola, así como la **creación y
eliminación de colas**. Para obtener más información acerca de las colas, consulte la sección [Pasos siguientes][].

## Tabla de contenido

* [¿Qué es el servicio Cola?][]   
* [Conceptos][]   
* [Creación de una cuenta de almacenamiento de Azure][]
* [Creación de una aplicación Node.js][]
* [Configuración de la aplicación para acceder al almacenamiento][]   
* [Configuración de una cadena de conexión de almacenamiento de Azure][]
* [Adición de una cola][]   
* [Adición de un mensaje en una cola][]   
* [Adición de siguiente mensaje][]   
* [Adición de siguiente mensaje de la cola][]   
* [Adición de contenido de un mensaje en cola][]   
* [Adición de quitar mensajes de la cola][]   
* [Adición de la longitud de la cola][]   
* [Adición de una cola][]   
* [Adición de firmas de acceso compartido][]
* [Pasos siguientes][]

[AZURE.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

<h2><a name="create-account"></a>Creación de una cuenta de almacenamiento de Azure</h2>

[AZURE.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-app"></a>Creación de una aplicación Node.js

Cree una aplicación Node.js vacía. Para obtener instrucciones acerca de cómo crear una aplicación Node.js, consulte [Creación e implementación de una aplicación Node.js en un sitio web de Azure], [Servicio en la nube de Node.js][Servicio en la nube Node.js] (usando Windows PowerShell) o [Sitio web con WebMatrix].

## <a name="configure-access"> </a>Configuración de la aplicación para acceder al almacenamiento

Para usar el almacenamiento de Azure necesitará el SDK de almacenamiento de Azure para Node.js, que incluye un conjunto de útiles bibliotecas que
se comunican con los servicios REST de almacenamiento.

### Uso del Administrador de paquetes para Node (NPM) para obtener el paquete

1.  Utilice una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix) y vaya a la carpeta donde ha creado la aplicación de ejemplo.

2.  Escriba **npm install azure-storage** en la ventana de comandos, lo que
    es el siguiente:

        azure-storage@0.1.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── underscore@1.4.4
		├── validator@3.1.0
		├── node-uuid@1.4.1
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

3.  Puede ejecutar manualmente el comando **ls** para verificar que se ha creado la carpeta
    **node\_modules**. Dentro de esa carpeta
    encontrará el paquete **azure-storage**, que contiene las bibliotecas que necesita para
    obtener acceso al almacenamiento.

### Importación del paquete

Con el Bloc de notas u otro editor de texto, agregue lo siguiente en la parte superior del archivo
**server.js** de la aplicación en la que pretenda usar el almacenamiento:

    var azure = require('azure-storage');

## <aConfiguración de una conexión de almacenamiento de Azure name="setup-connection-string"></a>

El módulo azure leerá las variables de AZURE\_STORAGE\_ACCOUNT and AZURE\_STORAGE\_ACCESS\_KEY o AZURE\_STORAGE\_CONNECTION\_STRING para obtener la información necesaria para conectarse a su cuenta de almacenamiento de Azure. Si no se configuran estas variables de entorno, debe especificar la información de la cuenta mediante la llamada a**createQueueService**.

Para ver un ejemplo de cómo configurar las variables de entorno del Portal de administración para un sitio web de Azure, consulte [Aplicación web de Node.js con almacenamiento].

## <a name="create-queue"> </a>Procedimiento: de una cola

El código siguiente crea un objeto **QueueService**, el que le permite
trabajar con colas.

    var queueSvc = azure.createQueueService();

Utilice el método **createQueueIfNotExists**, que devuelve la cola
especificada si ya existe o crea una nueva cola con el nombre especificado
si todavía no existe.

	queueSvc.createQueueIfNotExists('myqueue', function(error, result, response){
      if(!error){
        // Queue created or exists
	  }
	});

Si la cola se crea,  `result` es verdadero. Si la cola existe,  `result` es falso.

###Filtros

Las operaciones de filtrado opcionales pueden aplicarse a las tareas realizadas utilizando **QueueService**. Las operaciones de filtrado pueden incluir el registro y el reintento automático, entre otros. Los filtros son objetos que implementan un método con la firma siguiente:

		function handle (requestOptions, next)

Después de realizar el preprocesamiento en las opciones de solicitud, el método tiene que llamar a "next" pasando una devolución de llamada con la firma siguiente:

		function (returnObject, finalCallback, next)

En esta devolución de llamada y después de procesar returnObject (la respuesta de la solicitud al servidor), la devolución de llamada tiene que invocar a next, si existe, para continuar procesando otros filtros, o bien simplemente invocar a finalCallback para finalizar la invocación del servicio.

Se incluyen dos filtros que implementan la lógica de reintento con el SDK de Azure para Node.js: **ExponentialRetryPolicyFilter** y **LinearRetryPolicyFilter**. Lo siguiente crea un objeto **QueueService** que usa **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var queueSvc = azure.createQueueService().withFilter(retryOperations);

## <a name="insert-message"> </a>Procedimiento: un mensaje en una cola

Para insertar un mensaje en una cola, use el método **createMessage** para
crear un nuevo mensaje y agregarlo a la cola.

	queueSvc.createMessage('myqueue', "Hello world!", function(error, result, response){
	  if(!error){
	    // Message inserted
	  }
	});

## <a name="peek-message"> </a>Procedimiento: siguiente mensaje

Puede inspeccionar el mensaje situado en la parte delantera de una cola, sin quitarlo
de la cola, mediante una llamada al método **peekMessages**. De forma predetermianda,
**peekMessages** inspecciona un único mensaje.

	queueSvc.peekMessages('myqueue', function(error, result, response){
	  if(!error){
		// Messages peeked
	  }
	});

El  `result` contiene el mensaje.

> [AZURE.NOTE] Si se utiliza**peekMessages** cuando no existen mensajes en la cola, no se devolverá un error, pero tampoco se devolverán mensajes.

## <a name="get-message"> </a>Procedimiento: siguiente mensaje de la cola

El procesamiento de un mensaje es un proceso que consta de dos etapas:

1. Extracción del mensaje de la cola.

2. Eliminación del mensaje.

Para quitar un mensaje de la cola, use **getMessage**. De esta forma el mensaje se hace invisible en la cola, así que ningún otro cliente puede procesarlo. Después de que la aplicación ha procesado el mensaje, llame a **deleteMessage** para eliminarlo de la cola. En el siguiente ejemplo se obtiene un mensaje y luego se elimina:

	queueSvc.getMessages('myqueue', function(error, result, response){
      if(!error){
	    // message dequed
        var message = result[0];
        queueSvc.deleteMessage('myqueue', message.messageid, message.popreceipt, function(error, response){
	      if(!error){
		    //message deleted
		  }
		});
	  }
	});

> [AZURE.NOTE] De manera predeterminada, un mensaje solo está oculto durante 30 segundos, después de lo cual es visible para otros clientes. Puede especificar un valor diferente usando  `options.visibilityTimeout` con **getMessages**.

> [AZURE.NOTE]
> Si utiliza <b>getMessages</b> cuando no existen mensajes en la cola, no se devolverá un error, pero tampoco se devolverán mensajes.

## <a name="change-contents"> </a>Procedimiento: contenido de un mensaje en cola

Puede cambiar el contenido de un mensaje local en la cola mediante**updateMessage**. En el ejemplo siguiente se actualiza el texto de un mensaje:

    queueSvc.getMessages('myqueue', function(error, result, response){
	  if(!error){
		// Got the message
		var message = result[0];
		queueSvc.updateMessage('myqueue', message.messageid, message.popreceipt, 10, {messageText: 'new text'}, function(error, result, response){
		  if(!error){
			// Message updated successfully
		  }
		});
	  }
	});

## <a name="advanced-get"> </a>Procedimiento: quitar mensajes de la cola

Hay dos formas de personalizar la recuperación de mensajes de una cola:

* `options.numOfMessages` - Recuperar un lote de mensajes (hasta 32).
* `options.visibilityTimeout` - Establecer un tiempo de espera de invisibilidad más largo o más corto.

En el siguiente ejemplo se usa el método **getMessages** para obtener 15 mensajes en una llamada. A continuación,
procesa cada mensaje con un bucle for. También se establece el tiempo de espera de invisibilidad en cinco minutos para todos los mensajes que devuelve este método.

    queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, result, response){
	  if(!error){
		// Messages retreived
		for(var index in result){
		  // text is available in result[index].messageText
		  var message = result[index];
		  queueSvc.deleteMessage(queueName, message.messageid, message.popreceipt, function(error, response){
			if(!error){
			  // Message deleted
			}
		  });
		}
	  }
	});

## <a name="get-queue-length"> </a>Procedimiento: la longitud de la cola

El método **getQueueMetadata** devuelve metadatos sobre la cola, junto con el número aproximado de mensajes que esperan en la cola.

    queueSvc.getQueueMetadata('myqueue', function(error, result, response){
	  if(!error){
		// Queue length is available in result.approximatemessagecount
	  }
	});

## <a name="list-queue"> </a>Procedimiento: de colas

Para recuperar una lista de colas, use **listQueuesSegmented**. Para recuperar una lista filtrada por un prefijo determinado, use **listQueuesSegmentedWithPrefix**.

	queueSvc.listQueuesSegmented(null, function(error, result, response){
	  if(!error){
	    // result.entries contains the list of queues
	  }
	});

Si todas las colas no se pueden devolver,  `result.continuationToken` se puede usar como el primer parámetro de **listQueuesSegmented** o el segundo parámetro de **listQueuesSegmentedWithPrefix** para recuperar más resultados.

## <a name="delete-queue"> </a>Procedimiento: una cola

Para eliminar una cola y todos los mensajes contenidos en ella, llame al método
**deleteQueue** en el objeto de cola.

    queueSvc.deleteQueue(queueName, function(error, response){
		if(!error){
			// Queue has been deleted
		}
	});

Para borrar todos los mensajes de una cola sin eliminarla, use **clearMessages**.

## <a name="sas"></a>Procedimiento: con firmas de acceso compartido

Las firmas de acceso compartido (SAS) constituyen una manera segura de ofrecer acceso granular a las colas sin proporcionar el nombre o las claves de su cuenta de almacenamiento. Las SAS se usan con frecuencia para proporcionar acceso limitado a sus colas, por ejemplo, para permitir que una aplicación móvil envíe mensajes.

Una aplicación de confianza, como un servicio basado en la nube, genera una SAS mediante el valor **generateSharedAccessSignature** del elemento **QueueService**, y lo proporciona a una aplicación en la que no se confía o en la que se confía parcialmente. Por ejemplo, una aplicación móvil. La SAS se genera usando una directiva que describe las fechas de inicio y de finalización durante las cuales la SAS es válida, junto con el nivel de acceso otorgado al titular de la SAS.

En el siguiente ejemplo se genera una nueva directiva de acceso compartido que permitirá al titular de la SAS agregar mensajes a la cola, y que expira 100 minutos después de la hora en que se crea.

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);
	
	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
	    Start: startDate,
	    Expiry: expiryDate
	  }
	};

	var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
	var host = queueSvc.host;

Tenga en cuenta que también se debe proporcionar la información del host, puesto que es necesaria cuando el titular de la SAS intenta acceder a la cola.

La aplicación cliente usa entonces la SAS con **QueueServiceWithSAS** para realizar operaciones en la cola. En el siguiente ejemplo se realiza la conexión a la cola y se crea un mensaje.

	var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
	sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
	  if(!error){
	    //message added
	  }
	});

Dado que la SAS se generó solo con acceso para agregar, si se realizara un intento para leer, actualizar o eliminar mensajes, se devolvería un error.

###Listas de control de acceso

Se puede usar una lista de control de acceso (ACL) para definir la directiva de acceso para una SAS. Esto es útil si desea permitir que varios clientes accedan a la cola, pero cada uno con directivas de acceso diferentes.

Una ACL se implementa mediante el uso de un conjunto de directivas de acceso, con un Id. asociado a cada directiva. En los siguientes ejemplos se definen dos directivas; una para "user1" y otra para "user2":

	var sharedAccessPolicy = [
	  {
	    AccessPolicy: {
	      Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user1'
	  },
	  {
	    AccessPolicy: {
	      Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user2'
	  }
	];

En el siguiente ejemplo se obtiene la ACL actual para**myqueue** y luego se agregan las nuevas directivas mediante **setQueueAcl**. Este enfoque permite lo siguiente:

	queueSvc.getQueueAcl('myqueue', function(error, result, response) {
      if(!error){
		//push the new policy into signedIdentifiers
		result.signedIdentifiers.push(sharedAccessPolicy);
		queueSvc.setQueueAcl('myqueue', result, function(error, result, response){
	  	  if(!error){
	    	// ACL set
	  	  }
		});
	  }
	});

Después de establecer una ACL, puede crear luego una SAS basada en el Id. de una directiva. En el siguiente ejemplo se crea una nueva SAS para 'user2':

	queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });

## <a name="next-steps"> </a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de Almacenamiento de colas, siga estos vínculos
para obtener información sobre cómo hacer tareas de almacenamiento más complejas.

-   Consulte la referencia de MSDN: [Almacenamiento de datos y acceso a los mismos en Azure][].
-   Visite el [blog del equipo de almacenamiento de Azure][].
-   Visite el repositorio del [SDK de almacenamiento de Azure para Node.js][] en GitHub.

  [Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node
  [Next Steps]: #next-steps
  [What is the Queue Service?]: #what-is
  [Concepts]: #concepts
  [Create an Azure Storage Account]: #create-account
  [Create a Node.js Application]: #create-app
  [Configure your Application to Access Storage]: #configure-access
  [Setup an Azure Storage Connection String]: #setup-connection-string
  [How To: Create a Queue]: #create-queue
  [How To: Insert a Message into a Queue]: #insert-message
  [How To: Peek at the Next Message]: #peek-message
  [How To: Dequeue the Next Message]: #get-message
  [How To: Change the Contents of a Queued Message]: #change-contents
  [How To: Additional Options for Dequeuing Messages]: #advanced-get
  [How To: Get the Queue Length]: #get-queue-length
  [How To: Delete a Queue]: #delete-queue
  [How To: Working with Shared Access Signatures]: #sas
  [using the REST API]: http://msdn.microsoft.com/es-es/library/windowsazure/hh264518.aspx
  [Azure Management Portal]: http://manage.windowsazure.com
  [Create and deploy a Node.js application to an Azure Web Site]: /es-es/documentation/articles/web-sites-nodejs-develop-deploy-mac/
  [Node.js Cloud Service with Storage]: /es-es/documentation/articles/storage-nodejs-use-table-storage-cloud-service-app/
  [Node.js Web Application with Storage]: /es-es/documentation/articles/storage-nodejs-use-table-storage-web-site/

  
  [Queue1]: ./media/storage-nodejs-how-to-use-queues/queue1.png
  [plus-new]: ./media/storage-nodejs-how-to-use-queues/plus-new.png
  [quick-create-storage]: ./media/storage-nodejs-how-to-use-queues/quick-storage.png
  
  
  
  [Node.js Cloud Service]: /es-es/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Storing and Accessing Data in Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/gg433040.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
 [Web Site with WebMatrix]: /es-es/documentation/articles/web-sites-nodejs-use-webmatrix/
<!--HONumber=42-->
