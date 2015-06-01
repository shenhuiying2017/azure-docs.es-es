<properties 
	pageTitle="Uso del almacenamiento de blobs de Node.js | Microsoft Azure" 
	description="Aprenda a utilizar el servicio BLOB de Azure para cargar, descargar, incluir en un listado y eliminar contenido de blobs. Ejemplos escritos en Node.js." 
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
	ms.date="03/11/2015" 
	ms.author="mwasson"/>



# Uso del almacenamiento de blobs de Node.js

[AZURE.INCLUDE [storage-selector-blob-include](../includes/storage-selector-blob-include.md)]

## Información general

Esta guía muestra cómo realizar algunas tareas comunes a través del
servicio BLOB de Azure. Los ejemplos están escritos usando la
API Node.js. Entre los escenarios descritos se incluyen la **carga****, listado**,
**descarga** y **eliminación de** blobs.

[AZURE.INCLUDE [storage-blob-concepts-include](../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## Creación de una aplicación Node.js

Cree una aplicación Node.js vacía. Para obtener instrucciones acerca de cómo crear una aplicación Node.js, consulte [Creación e implementación de una aplicación Node.js en un sitio web de Azure], [Servicio en la nube de Node.js][Servicio en la nube de Node.js] (usando Windows PowerShell) o [Sitio web con WebMatrix].

## Configuración de la aplicación para acceder al almacenamiento

Para usar el almacenamiento de Azure necesitará el SDK de almacenamiento de Azure para Node.js, que incluye un conjunto de útiles bibliotecas que se comunican con los servicios REST de almacenamiento.

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
    **node_modules**. Dentro de esa carpeta, busque el paquete
    **azure-storage**, que contiene las bibliotecas que necesita para obtener acceso al
    almacenamiento.

### Importación del paquete

Con el Bloc de notas u otro editor de texto, agregue lo siguiente en la parte superior del archivo
**server.js** de la aplicación en la que pretenda usar el almacenamiento:

    var azure = require('azure-storage');

## Configuración de una conexión de almacenamiento de Azure

El módulo azure leerá las variables de entorno `AZURE_STORAGE_ACCOUNT` y `AZURE_STORAGE_ACCESS_KEY` o `AZURE_STORAGE_CONNECTION_STRING` para obtener la información necesaria para conectarse a la cuenta de almacenamiento de Azure. Si no se configuran estas variables de entorno, debe especificar la información de la cuenta llamando a **createBlobService**.

Para ver un ejemplo de cómo configurar las variables de entorno del Portal de administración para un sitio web de Azure, consulte [Aplicación web de Node.js con almacenamiento].

## Procedimientos: Creación de un contenedor

El objeto **BlobService** le permite trabajar con contenedores y blobs. El código siguiente crea un objeto **BlobService**. Agregue lo siguiente cerca de la parte superior de **server.js**.

    var blobSvc = azure.createBlobService();

> [AZURE.NOTE] Puede obtener acceso a un blob de forma anónima mediante el uso de **createBlobServiceAnonymous** y proporcionando la dirección del host. Por ejemplo: `var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`.

Todos los blobs residen en un contenedor. Para crear un nuevo contenedor, use **createContainerIfNotExists**. Lo siguiente crea un nuevo contenedor llamado 'mycontainer'

	blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
      if(!error){
        // Container exists and allows 
        // anonymous read access to blob 
        // content and metadata within this container
      }
	});

Si el contenedor se crea,  `result` será verdadero. Si el contenedor ya existe,  `result` será falso.  `response` contendrá información acerca de la operación, junto con la información de [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) del contenedor.

### Seguridad del contenedor

De forma predeterminada, los nuevos contenedores son privados, así que no se puede acceder a ellos de forma anónima. Para que el contenedor sea público de forma que sea posible el acceso anónimo, puede establecer el nivel de acceso del contenedor en**blob** o **contenedor**.

* **blob** permite el acceso de lectura anónimo al contenido del blob y a los metadatos del contenedor, pero no al listado de todos los blobs de un contenedor determinado.

* **contenedor** permite el acceso de lectura anónimo al contenido del blob y los metadatos, incluidos los del contenedor.

El ejemplo siguiente demuestra cómo configurar el nivel de acceso en**blob**: 

    blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
      if(!error){
        // Container exists and is private
      }
	});

También puede modificar el nivel de acceso de un contenedor usando **setContainerAcl** para especificarlo. El ejemplo siguiente cambia el nivel de acceso a container:

    blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, 'container' /* publicAccessLevel*/, function(error, result, response){
	  if(!error){
		// Container access level set to 'container'
	  }
	});

El resultado contendrá información acerca de la operación, junto con la información de **ETag** del contenedor.

### Filtros

Las operaciones de filtrado opcionales se pueden aplicar a las operaciones realizadas usando **BlobService**. Las operaciones de filtrado pueden incluir el registro y el reintento automático, entre otros. Los filtros son objetos que implementan un método con la firma siguiente:

		function handle (requestOptions, next)

Después de realizar el preprocesamiento en las opciones de solicitud, el método tiene que llamar a "next" pasando una devolución de llamada con la firma siguiente:

		function (returnObject, finalCallback, next)

En esta devolución de llamada y después de procesar returnObject (la respuesta de la solicitud al servidor), la devolución de llamada tiene que invocar a next, si existe, para continuar procesando otros filtros, o bien simplemente invocar a finalCallback para finalizar la invocación del servicio.

Se incluyen dos filtros que implementan la lógica de reintento con el SDK de Azure para Node.js: **ExponentialRetryPolicyFilter** y **LinearRetryPolicyFilter**. Lo siguiente crea un objeto **BlobService** que usa **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var blobSvc = azure.createBlobService().withFilter(retryOperations);

## Procedimientos: un blob en un contenedor

Un blob se puede basar en un bloque o en una página. Los blobs en bloques le permiten cargar datos de gran tamaño con una mayor eficiencia, mientras que los blobs en páginas están optimizados para operaciones de lectura/escritura. Para obtener más información, consulte [Introducción a los blobs en bloques y a los blobs en páginas](http://msdn.microsoft.com/library/azure/ee691964.aspx).

### Blobs en bloques

Para cargar datos en un blob en bloque, use lo siguiente:

* **createBlockBlobFromLocalFile**: crea un nuevo blob en bloque y carga el contenido de un archivo.

* **createBlockBlobFromStream**: crea un nuevo blob en bloque y carga el contenido de una secuencia.

* **createBlockBlobFromText**: crea un nuevo blob en bloque y carga el contenido de una cadena.

* **createWriteStreamToBlockBlob**: proporciona una secuencia de escritura a un blob en bloque.

En el siguiente ejemplo se carga el contenido del archivo**test.txt** en **myblob**.

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

Los  `result` que devuelven estos métodos contendrá información acerca de la operación, como el valor **ETag** del blob.

### Blobs en páginas

Para cargar datos en un blob en página, use lo siguiente:

* **createPageBlob**: crea un nuevo blob en página de una longitud especificada.

* **createPageBlobFromLocalFile**: crea un nuevo blob en página y carga el contenido de un archivo.

* **createPageBlobFromStream**: crea un nuevo blob en página y carga el contenido de una secuencia.

* **createWriteStreamToExistingPageBlob**: proporciona una secuencia de escritura a un blob en página existente.

* **createWriteStreamToNewPageBlob**: crea un nuevo blob y, a continuación, proporciona una secuencia para escribir en él.

En el siguiente ejemplo se carga el contenido del archivo**test.txt** en **mypageblob**.

	blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

> [AZURE.NOTE] Los blobs en páginas constan de 'pages' de 512 bytes. Puede que al cargar datos con un tamaño que no es múltiplo de 512 reciba un error.

## Procedimientos: Enumeración de los blobs de un contenedor

Para enumerar los blobs de un contenedor, use el método**listBlobsSegmented**. Si desea devolver blobs con un prefijo determinado, use **listBlobsSegmentedWithPrefix**.

    blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
      if(!error){
        // result contains the entries
	  }
	});

El  `result` contendrá una colección de  `entries`, que es una matriz de objetos que describen cada blob. Si no se pueden devolver todos los blobs, `result` proporcionará también un `continuationToken`, que se puede usar como el segundo parámetro para recuperar entradas adicionales.

## Procedimientos: blobs

Para descargar datos de un blob, use lo siguiente:

* **getBlobToFile**: escribe el contenido del blob en un archivo.

* **getBlobToStream**: escribe el contenido del blob en una secuencia.

* **getBlobToText**: escribe el contenido del blob en una cadena.

* **createReadStream**: proporciona una secuencia para leer del blob.

En el ejemplo siguiente se demuestra cómo usar **getBlobToStream** para descargar el contenido del blob **myblob** y almacenarlo en el archivo **output.txt** usando una secuencia:

    var fs = require('fs');
	blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
	  if(!error){
	    // blob retrieved
	  }
	});

El  `result` contendrá información acerca del blob, lo que incluye información de **ETag**.

## Procedimientos: Eliminación de un blob

Finalmente, para eliminar un blob, llame a **deleteBlob**. En el ejemplo siguiente se elimina el blob llamado **myblob**.

    blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
	  if(!error){
		// Blob has been deleted
	  }
	});

## Procedimientos: Acceso simultáneo

Para permitir el acceso simultáneo a un blob desde varios clientes o varias instancias de proceso, puede usar **ETags** o **concesiones**.

* **Etag**: proporciona una manera de detectar que otro proceso ha modificado el blob o el contenedor.

* **Concesión**: proporciona una manera de obtener acceso exclusivo y renovable de escritura o eliminación a un blob durante un período de tiempo.

### ETag

Las etiquetas ETag se deben usar si es necesario permitir que varios clientes o instancias escriban en el blob de manera simultánea. La etiqueta ETag le permite determinar si el contenedor o el blob se ha modificado desde que inicialmente lo leyera o creara. De esta forma, puede evitar que los cambios efectuados por otro cliente o proceso se sobrescriban.

Las condiciones de ETag se pueden definir mediante el parámetro `options.accessConditions` opcional. En el siguiente ejemplo solo se cargará el archivo **test.txt** si el blob ya existe y tiene el valor de ETag contenido por `etagToMatch`.

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { 'if-match': etagToMatch} }, function(error, result, response){
      if(!error){
	    // file uploaded
	  }
	});

El patrón general cuando se utilizan etiquetas ETag es el siguiente:

1. Obtener la etiqueta ETag como resultado de una operación create, list o get.

2. Realizar una acción, comprobando que el valor de ETag no se haya modificado.

Si el valor se ha modificado, significa que otro cliente u otra instancia ha modificado el blob o el contenedor desde que obtuvo el valor de ETag.

### Concesión

Se puede adquirir una nueva concesión mediante el método **acquireLease**, especificando el blob o el contenedor sobre el que desea obtenerla. Por ejemplo, lo siguiente adquiere una concesión sobre **myblob**.

	blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
	  if(!error) {
	    console.log('leaseId: ' + result.id);
	  }
	});

Las sucesivas operaciones sobre **myblob** deben proporcionar el parámetro  `options.leaseId`. El Id. de concesión se devuelve como  `result.id` desde **acquireLease**.

> [AZURE.NOTE] De manera predeterminada, la duración de la concesión es infinita. Puede especificar una duración no infinita (entre 15 y 60 segundos) con el parámetro  `options.leaseDuration`.

Para eliminar una concesión, use **releaseLease**. Para interrumpir una concesión, pero impedir que otros obtengan una nueva concesión hasta que expire la duración original, use **breakLease**.

## Procedimientos: Trabajo con firmas de acceso compartido

Las firmas de acceso compartido (SAS) constituyen una manera segura de ofrecer acceso granular a blobs y contenedores sin proporcionar el nombre o las claves de su cuenta de almacenamiento. Las SAS se usan con frecuencia para proporcionar acceso limitado a sus datos, por ejemplo, para permitir que una aplicación móvil acceda a los blobs.

> [AZURE.NOTE] Aunque también puede permitir el acceso anónimo a los blobs, SAS le permite proporcionar acceso más controlado, puesto que debe generar la SAS.

Una aplicación de confianza, como un servicio basado en la nube, genera una SAS mediante el valor **generateSharedAccessSignature** del elemento **BlobService**, y lo proporciona a una aplicación en la que no se confía o en la que se confía parcialmente. Por ejemplo, una aplicación móvil. La SAS se genera usando una directiva que describe las fechas de inicio y de finalización durante las cuales la SAS es válida, junto con el nivel de acceso otorgado al titular de la SAS.

En el siguiente ejemplo se genera una nueva directiva de acceso compartido que permitirá al titular de la SAS realizar operaciones de lectura en el blob **myblob**, y que expira 100 minutos después de la hora en que se crea.

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);
	    
	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
	    Start: startDate,
	    Expiry: expiryDate
	  },
	};
	
	var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
	var host = blobSvc.host;

Tenga en cuenta que también se debe proporcionar la información del host, puesto que es necesaria cuando el titular de la SAS intenta acceder al contenedor.

La aplicación cliente usa entonces la SAS con **BlobServiceWithSAS** para realizar operaciones en el blob. Lo siguiente obtiene información sobre **myblob**.

	var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
	sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
	  if(!error) {
	    // retrieved info
	  }
	});

Dado que la SAS se generó solo con acceso de lectura, si se realizara un intento para modificar el blob, se devolvería un error.

### Listas de control de acceso

Se puede usar una lista de control de acceso (ACL) para definir la directiva de acceso para una SAS. Esto es útil si desea permitir que varios clientes accedan a un contenedor, pero cada uno con directivas de acceso diferentes.

Una ACL se implementa mediante el uso de un conjunto de directivas de acceso, con un Id. asociado a cada directiva. En los siguientes ejemplos se definen dos directivas; una para "user1" y otra para "user2":

	var sharedAccessPolicy = [
	  {
	    AccessPolicy: {
	      Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user1'
	  },
	  {
	    AccessPolicy: {
	      Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user2'
	  }
	];

En el siguiente ejemplo se obtiene la ACL actual para **mycontainer** y luego se agregan las nuevas directivas mediante **setBlobAcl**. Este enfoque permite lo siguiente:

	blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
      if(!error){
		//push the new policy into signedIdentifiers
		result.signedIdentifiers.push(sharedAccessPolicy);
		blobSvc.setBlobAcl('mycontainer', result, function(error, result, response){
	  	  if(!error){
	    	// ACL set
	  	  }
		});
	  }
	});

Después de establecer una ACL, puede crear luego una SAS basada en el Id. de una directiva. En el siguiente ejemplo se crea una nueva SAS para 'user2':

	blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });

## Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del almacenamiento de blobs, siga estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

-   Lea la [Referencia del SDK de almacenamiento de Azure para la API de nodo][]
-   Consulte la referencia de MSDN: [Almacenamiento de datos y acceso a los mismos en Azure][].
-   Visite el [Blog del equipo de almacenamiento de Azure][].
-   Visite el repositorio del [SDK de almacenamiento de Azure para Node.js][] en GitHub.

[SDK de almacenamiento de Azure para Node.js]: https://github.com/Azure/azure-storage-node
[Creación e implementación de una aplicación Node.js en un sitio web de Azure]: /develop/nodejs/tutorials/create-a-website-(mac)/
[Servicio en la nube de Node.js con almacenamiento]: storage-nodejs-use-table-storage-cloud-service-app.md
[Aplicación web Node.js con almacenamiento]: storage-nodejs-use-table-storage-web-site.md
[Sitio web con WebMatrix]: web-sites-nodejs-use-webmatrix.md
[Mediante la API de REST]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Portal de administración de Azure]: http://manage.windowsazure.com
[Servicio en la nube de Node.js]: cloud-services-nodejs-develop-deploy-app.md
[Almacenamiento de datos y acceso a los mismos en Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Referencia del SDK de almacenamiento de Azure para la API de nodo]: http://dl.windowsazure.com/nodestoragedocs/index.html

<!--HONumber=49-->