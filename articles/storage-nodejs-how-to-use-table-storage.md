<properties 
	pageTitle="Uso del almacenamiento de tablas (Node.js) | Microsoft Azure" 
	description="Aprenda a usar el servicio de almacenamiento de tablas en Azure. Los ejemplos de código están escritos usando la API Node.js." 
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

# Uso del servicio Tabla desde Node.js

Esta guía le indicará cómo actuar en situaciones habituales usando el servicio Tabla de Azure. Los ejemplos se han escrito usando la API Node.js. Entre los escenarios descritos se incluyen la **creación y eliminación de una tabla, la inserción y la consulta de entidades en una tabla**. Para obtener más información acerca de las tablas, consulte la sección [Pasos siguientes][].

## Tabla de contenido

* [¿Qué es el servicio Tabla?][]   
* [Conceptos][]   
* [Creación de una cuenta de almacenamiento de Azure](#create-account)
* [Creación de una aplicación Node.js](#create-app)
* [Configuración de la aplicación para acceder al almacenamiento](#configure-access)
* [Configuración de una conexión de almacenamiento de Azure](#setup-connection-string)  
* [Adición de una tabla](#create-table)
* [Adición de una entidad a una tabla](#add-entity)
* [Adición de una entidad](#update-entity)
* [Uso de grupos de entidades](#change-entities)
* [Uso de una entidad](#query-for-entity)
* [Uso de un conjunto de entidades](#query-set-entities)
* [Adición de una entidad](#delete-entity)
* [Adición de de una tabla](#delete-table)   
* [Uso de firmas de acceso compartido](#sas)
* [Pasos siguientes][]

[AZURE.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

<h2><a name="create-account"></a>Creación de una cuenta de almacenamiento de Azure</h2>

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## <a name="create-app"> </a>Creación de una aplicación Node.js

Creación de una aplicación Node.js vacía.  Para obtener instrucciones acerca de cómo crear una aplicación Node.js, consulte [Creación e implementación de una aplicación Node.js en un sitio web de Azure], [Servicio en la nube Node.js][Node.js Cloud Service] (con Windows PowerShell) o [Sitio web con WebMatrix].

## <a name="configure-access"> </a>Configuración de la aplicación para acceder al almacenamiento

Para usar el almacenamiento de Azure necesitará el SDK de almacenamiento de Azure para Node.js, que incluye un conjunto de útiles bibliotecas que se comunican con los servicios REST de almacenamiento.

### Uso del Administrador de paquetes para Node (NPM) para obtener el paquete

1.  Utilice una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix) y vaya a la carpeta donde ha creado la aplicación de ejemplo.

2.  Escriba **npm install azure-storage** en la ventana de comandos. Esto debería devolver la salida siguiente:

        azure-storage@0.1.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── underscore@1.4.4
		├── validator@3.1.0
		├── node-uuid@1.4.1
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

3.  Puede ejecutar manualmente el comando **ls** para comprobar si se ha creado la carpeta **node\_modules**. Dentro de dicha carpeta, encontrará el paquete **azure-storage**, que contiene las bibliotecas necesarias para el acceso al almacenamiento.

### Importación del paquete

Con el Bloc de notas u otro editor de texto, agregue lo siguiente en la parte superior del archivo **server.js** de la aplicación en la que pretenda usar el almacenamiento:

    var azure = require('azure-storage');

## <a name="setup-connection-string">Configuración de una conexión de almacenamiento de Azure</a>

El módulo azure leerá las variables de AZURE\_STORAGE\_ACCOUNT and AZURE\_STORAGE\_ACCESS\_KEY o AZURE\_STORAGE\_CONNECTION\_STRING para obtener la información necesaria para conectarse a su cuenta de almacenamiento de Azure. Si no configura estas variables de entorno, debe especificar la información de la cuenta al llamar a **TableService**.

Para ver un ejemplo de cómo configurar las variables de entorno del Portal de administración para un sitio web de Azure, consulte [Aplicación web de Node.js con almacenamiento].

## <a name="create-table"> </a>Creación de una tabla

El código siguiente crea un objeto **TableService** que usa para crear una tabla. Agregue lo siguiente cerca de la parte superior de **server.js**.

    var tableSvc = azure.createTableService();

La llamada a **createTableIfNotExists** creará una nueva tabla con el nombre especificado si no existe ya. El ejemplo siguiente crea una tabla llamada "mytable", si es que no existe todavía:

    tableSvc.createTableIfNotExists('mytable', function(error, result, response){
		if(!error){
			// Table exists or created
		}
	});

El  `result` será  `true` si se crea una nueva tabla, y  `false` si la tabla ya existe.  `response` contendrá información sobre la solicitud.

###Filtros

Las operaciones opcionales de filtrado se pueden aplicar a las operaciones realizadas usando **TableService**. Las operaciones de filtrado pueden incluir el registro y el reintento automático, entre otros. Los filtros son objetos que implementan un método con la firma siguiente:

		function handle (requestOptions, next)

Después de realizar el preprocesamiento en las opciones de solicitud, el método tiene que llamar a "next" pasando una devolución de llamada con la firma siguiente:

		function (returnObject, finalCallback, next)

En esta devolución de llamada y después de procesar returnObject (la respuesta de la solicitud al servidor), la devolución de llamada tiene que invocar a next, si existe, para continuar procesando otros filtros, o bien simplemente invocar a finalCallback para finalizar la invocación del servicio.

Se incluyen dos filtros que implementan la lógica de reintento con el SDK de Azure para Node.js: **ExponentialRetryPolicyFilter** y **LinearRetryPolicyFilter**. Lo siguiente crea un objeto **TableService** que usa **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var tableSvc = azure.createTableService().withFilter(retryOperations);

## <a name="add-entity"> </a>Incorporación de una entidad a una tabla

Para agregar una entidad, primero cree un objeto que defina las propiedades de la entidad. Todas las entidades deben contener un valor para **PartitionKey** y **RowKey**, que son identificadores únicos de la entidad.

* **PartitionKey** - Determina la partición en la que se almacena la entidad.

* **RowKey**: identifica de forma única la entidad dentro de la partición.

Tanto **PartitionKey** como **RowKey** deben ser valores de cadena. Para obtener más información, consulte [Introducción al modelo de datos del servicio Tabla](http://msdn.microsoft.com/library/azure/dd179338.aspx).

Este es un ejemplo de la definición de una entidad. Tenga en cuenta que **dueDate** se define como un tipo de **Edm.DateTime**. La especificación del tipo es opcional, y los tipos se deducen si no se especifican.

	var task = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'},
	  description: {'_':'take out the trash'},
	  dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
	};

> [AZURE.NOTE] Hay también un campo**Timestamp** para cada registro, que Azure establece cuando se inserta o actualiza una entidad.

También puede usar **entityGenerator** para crear entidades. En el siguiente ejemplo se crea la misma entidad de tarea mediante **entityGenerator**.

	var entGen = azure.TableUtilities.entityGenerator;
    var task = {
	  PartitionKey: entGen.String('hometasks'),
      RowKey: entGen.String('1'),
      description: entGen.String('take out the trash'),
      dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
    };

Para agregar una entidad a su tabla, pase el objeto de la entidad al método **insertEntity**.

	tableSvc.insertEntity('mytable',task, function (error, result, response) {
		if(!error){
			// Entity inserted
		}
	});

Si la operación se realiza correctamente,  `result` contendrá la etiqueta [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) del registro insertado y  `response` contendrá información sobre la operación.

> [AZURE.NOTE] De forma predeterminada, **insertEntity** no devuelve la entidad insertada como parte de la información de  `response`. Si tiene pensado realizar otras operaciones en esta entidad o desea almacenar en caché la información, puede ser útil que la devuelvan como parte de  `result`. Para ello, puede habilitar **echoContent** de la manera siguiente:
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## <a name="update-entity"> </a>Actualización de una entidad

Hay varios métodos para actualizar una entidad existente:

* **updateEntity**: actualiza una entidad que ya existe reemplazándola.

* **mergeEntity**: actualiza una entidad que ya existe combinando los valores de las nuevas propiedades con la entidad existente.

* **insertOrReplaceEntity**: actualiza una entidad existente reemplazándola. Si no hay entidades, se insertará una nueva.

* **insertOrMergeEntity**: actualiza una entidad que ya existe combinando los valores de las nuevas propiedades con los existentes. Si no hay entidades, se insertará una nueva.

El ejemplo siguiente demuestra cómo actualizar una entidad usando **updateEntity**:

	tableSvc.updateEntity('mytable', updatedTask, function(error, result, response){
      if(!error) {
        // Entity updated
      }
    });

> [AZURE.NOTE] De manera predeterminada, al actualizar una entidad no se comprueba si otro proceso ha modificado anteriormente los datos que se actualizan. Para permitir las actualizaciones simultáneas:
> 
> 1. Obtenga la etiqueta ETag del objeto que se va a actualizar. Esta se devuelve como parte del valor de  `response` para cualquier operación relacionada con entidades y se puede recuperar a través de  `response['.metadata'].etag`.
> 
> 2. Al realizar una operación de actualización en una entidad, agregue la información de ETag anteriormente recuperada a la nueva entidad. Por ejemplo:
> 
>     `entity2['.metadata'].etag = currentEtag;`
>    
> 3. Realice la operación de actualización. Si la entidad se ha modificado desde que recuperara el valor de ETag, por ejemplo, otra instancia de la aplicación, se devolverá un  `error` indicando que la condición de actualización especificada en la solicitud no se ha satisfecho.
    
Con **updateEntity** y **mergeEntity**, si la entidad que se está actualizando no existe, se producirá un error en la operación de actualización. Por lo tanto, si desea almacenar una entidad independientemente de la que ya existe, debe usar **insertOrReplaceEntity** o **insertOrMergeEntity**.

El  `result` de operaciones de actualización correctas contendrá la etiqueta **Etag** de la entidad actualizada.

## <a name="change-entities"> </a>Trabajo con grupos de entidades

A veces resulta útil enviar varias operaciones juntas en un lote a fin de garantizar el procesamiento atómico por parte del servidor. Para realizar esto, se usa la clase **TableBatch** para crear un lote y luego el método **executeBatch** de **TableService** para realizar las operaciones por lotes.

 El ejemplo siguiente demuestra cómo enviar dos entidades en un lote:

    var task1 = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'},
	  description: {'_':'Take out the trash'},
	  dueDate: {'_':new Date(2015, 6, 20)}
	};
	var task2 = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '2'},
	  description: {'_':'Wash the dishes'},
	  dueDate: {'_':new Date(2015, 6, 20)}
	};

	var batch = new azure.TableBatch();
	
	batch.insertEntity(task1, {echoContent: true});
	batch.insertEntity(task2, {echoContent: true});

	tableSvc.executeBatch('mytable', batch, function (error, result, response) {
	  if(!error) {
	    // Batch completed
	  }
	});

En las operaciones por lotes realizadas correctamente,  `result` contendrá información de cada operación del lote.

###Trabajo con operaciones por lotes

Las operaciones agregadas a un lote se pueden inspeccionar mirando la propiedad  `operations`. También se pueden usar los siguientes métodos para trabajar con operaciones.

* **clear**: borra todas las operaciones de un lote.

* **getOperations**: obtiene una operación del lote.

* **hasOperations**: devuelve true si el lote contiene operaciones.

* **removeOperations**: quita una operación.

* **size**: devuelve el número de operaciones del lote.

## <a name="query-for-entity"> </a>Recuperación de una entidad

Si desea devolver una entidad específica en función de los valores de **PartitionKey** Y **RowKey**, use el método **retrieveEntity**.

    tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
	  if(!error){
	    // result contains the entity
	  }
    });

Después de completar esta operación,  `result` contendrá la entidad.

## <a name="query-set-entities"> </a>Consulta de un conjunto de entidades

Para consultar una tabla, use el objeto **TableQuery** para compilar una expresiónde consulta mediante las siguientes cláusulas:

* **select**: los campos que va a devolver la consulta.

* **where**: la cláusula where.

	* **and**: una condición where  `and`.

	* **or**: una condición where  `or`.

* **top**: el número de elementos que se obtendrán.


En el siguiente ejemplo se crea una consulta que devolverá los 5 elementos principales con un valor de PartitionKey de 'hometasks'.

	var query = new azure.TableQuery()
	  .top(5)
	  .where('PartitionKey eq ?', 'hometasks');

Dado que **select** no se usa, se devolverán todos los campos. Para realizar la consulta en una tabla, use **queryEntities**. En el siguiente ejemplo se usa esta consulta para devolver entidades de 'mytable'.

	tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
	  if(!error) {
	    // query was successful
	  }
	});

Si la operación se realiza correctamente,  `result.entries` contendrá un conjunto de entidades que coinciden con la consulta. Si la consulta no puede devolver todas las entidades, `result.continuationToken` será non-*null* y se puede usar como el tercer parámetro de **queryEntities** para recuperar más resultados. Para la consulta inicial, el tercer parámetro debe ser  *null*.

###Consulta de un subconjunto de propiedades de las entidades

Una consulta de tabla puede recuperar solo algunos campos de una entidad.
Esto reduce el ancho de banda y puede mejorar el rendimiento de las consultas, en especial en el caso de entidades de gran tamaño. Use la cláusula **select** y pase los nombres de los campos que se van a devolver. Por ejemplo, la siguiente consulta solo devolverá los campos **description** y **dueDate**.

	var query = new azure.TableQuery()
	  .select(['description', 'dueDate'])
	  .top(5)
	  .where('PartitionKey eq ?', 'hometasks');

## <a name="delete-entity"> </a>Eliminación de una entidad

Puede eliminar una entidad usando sus claves de partición y fila. En este ejemplo, el objeto **task1** contiene los valores de **RowKey** y
**PartitionKey** de la entidad que se va a eliminar. A continuación, el objeto pasa al método **deleteEntity**.

	var task = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'}
	};

    tableSvc.deleteEntity('mytable', task, function(error, response){
	  if(!error) {
		// Entity deleted
	  }
	});

> [AZURE.NOTE] Cuando elimine elementos, debería considerar el uso de etiquetas ETag para garantizar que otro proceso no haya modificado el elemento. Consulte [Actualización de una entidad][] para obtener información acerca del uso de etiquetas ETag.

## <a name="delete-table"> </a>Eliminación de una tabla

El código siguiente elimina una tabla de la cuenta de almacenamiento.

    tableSvc.deleteTable('mytable', function(error, response){
		if(!error){
			// Table deleted
		}
	});

Si no está seguro de si existe la tabla, use **deleteTableIfExists**.

## <a name="sas"></a>Procedimiento: con firmas de acceso compartido

Las firmas de acceso compartido (SAS) constituyen una manera segura de ofrecer acceso granular a las tablas sin proporcionar el nombre o las claves de su cuenta de almacenamiento. Las SAS se usan con frecuencia para proporcionar acceso limitado a sus datos, por ejemplo, para permitir que una aplicación móvil consulte registros.

Una aplicación de confianza, como un servicio basado en la nube, genera una SAS mediante el valor **generateSharedAccessSignature** del elemento **TableService**, y lo proporciona a una aplicación en la que no se confía o en la que se confía parcialmente. Por ejemplo, una aplicación móvil. La SAS se genera usando una directiva que describe las fechas de inicio y de finalización durante las cuales la SAS es válida, junto con el nivel de acceso otorgado al titular de la SAS.

En el siguiente ejemplo se genera una nueva directiva de acceso compartido que permitirá al titular de la SAS consultar ('r') la tabla, y que expira 100 minutos después de la hora en que se crea.

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);
		
	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
	    Start: startDate,
	    Expiry: expiryDate
	  },
	};

	var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
	var host = tableSvc.host;

Tenga en cuenta que también se debe proporcionar la información del host, puesto que es necesaria cuando el titular de la SAS intenta acceder a la tabla.

La aplicación cliente usa entonces la SAS con **TableServiceWithSAS** para realizar operaciones en la tabla. En el siguiente ejemplo se realiza la conexión a la tabla y se realiza una consulta.

	var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
	var query = azure.TableQuery()
	  .where('PartitionKey eq ?', 'hometasks');
		
	sharedTableService.queryEntities(query, null, function(error, result, response) {
	  if(!error) {
		// result contains the entities
	  }
	});

Dado que la SAS se generó solo con acceso de consulta, si se realizara un intento para insertar, actualizar o eliminar entidades, se devolvería un error.

###Listas de control de acceso

Se puede usar una lista de control de acceso (ACL) para definir la directiva de acceso para una SAS. Esto es útil si desea permitir que varios clientes accedan a la tabla, pero cada uno con directivas de acceso diferentes.

Una ACL se implementa mediante el uso de un conjunto de directivas de acceso, con un Id. asociado a cada directiva. En los siguientes ejemplos se definen dos directivas; una para "user1" y otra para "user2":

	var sharedAccessPolicy = [
	  {
	    AccessPolicy: {
	      Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user1'
	  },
	  {
	    AccessPolicy: {
	      Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user2'
	  }
	];

En el siguiente ejemplo se obtiene la ACL actual para la tabla **hometasks** y luego se agregan las nuevas directivas mediante **setTableAcl**. Este enfoque permite lo siguiente:

	tableSvc.getTableAcl('hometasks', function(error, result, response) {
      if(!error){
		//push the new policy into signedIdentifiers
		result.signedIdentifiers.push(sharedAccessPolicy);
		tableSvc.setTableAcl('hometasks', result, function(error, result, response){
	  	  if(!error){
	    	// ACL set
	  	  }
		});
	  }
	});

Después de establecer una ACL, puede crear luego una SAS basada en el Id. de una directiva. En el siguiente ejemplo se crea una nueva SAS para 'user2':

	tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });

## <a name="next-steps"> </a>Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del almacenamiento de tablas, siga estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

-   Consulte la referencia de MSDN: [Almacenamiento de datos y acceso a los mismos en Azure][].
-   Visite el [Blog del equipo de almacenamiento de Azure][] (en inglés).
-   Visite el repositorio del [SDK de almacenamiento de Azure para Node.js][] en GitHub.

  [SDK de almacenamiento de Azure para Node.js]: https://github.com/Azure/azure-storage-node
  [Pasos siguientes]: #next-steps
  [¿Qué es el servicio Tabla?]: #what-is
  [Conceptos]: #concepts
  [Create an Azure Storage Account]: #create-account
  [Create a Node.js Application]: #create-app
  [Configure your Application to Access Storage]: #configure-access
  [Setup an Azure Storage Connection]: #setup-connection-string
  [How To: Create a Table]: #create-table
  [How To: Add an Entity to a Table]: #add-entity
  [How To: Update an Entity]: #update-entity
  [How to: Work with Groups of Entities]: #change-entities
  [How to: Query for an Entity]: #query-for-entity
  [How to: Query a Set of Entities]: #query-set-entities
  [How To: Query a Subset of Entity Properties]: #query-entity-properties
  [How To: Delete an Entity]: #delete-entity
  [How To: Delete a Table]: #delete-table

  [OData.org]: http://www.odata.org/
  [using the REST API]: http://msdn.microsoft.com/library/windowsazure/hh264518.aspx
  [Azure Management Portal]: http://manage.windowsazure.com

  [Node.js Cloud Service]: /es-es/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Almacenamiento de datos y acceso a los mismos en Azure]: http://msdn.microsoft.com/library/windowsazure/gg433040.aspx
  [Blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Sitio web con WebMatrix]: /es-es/documentation/articles/web-sites-nodejs-use-webmatrix/
  [Node.js Cloud Service with Storage]: /es-es/documentation/articles/storage-nodejs-use-table-storage-cloud-service-app/
  [Node.js Web Application with Storage]: /es-es/documentation/articles/storage-nodejs-use-table-storage-web-site/
  [Creación e implementación de una aplicación Node.js en un sitio web de Azure]: /es-es/documentation/articles/web-sites-nodejs-develop-deploy-mac/
<!--HONumber=42-->
