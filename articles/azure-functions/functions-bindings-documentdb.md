<properties
	pageTitle="Enlaces de DocumentDB en funciones de Azure | Microsoft Azure"
	description="Descubra cómo utilizar los enlaces de DocumentDB en funciones de Azure."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="funciones de azure, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="08/22/2016"
	ms.author="chrande; glenga"/>

# Enlaces de DocumentDB en funciones de Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artículo explica cómo configurar y codificar enlaces de Azure DocumentDB en funciones de Azure.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a id="docdbinput"></a> Enlace de entrada de Azure DocumentDB

Los enlaces de entrada pueden cargar un documento desde una colección de DocumentDB y pasarlo directamente al enlace. Se puede determinar el identificador de documento según el desencadenador que invocó la función. En una función de C#, los cambios realizados en el registro se enviarán automáticamente a la colección una vez que la función se complete correctamente.

#### function.json para el enlace de entrada de DocumentDB

El archivo *function.json* ofrece las siguientes propiedades:

- `name`: nombre de la variable que se usa en el código de la función para el documento.
- `type`: se debe establecer en "documentdb".
- `databaseName`: la base de datos que contiene el documento.
- `collectionName`: la colección que contiene el documento.
- `id`: el identificador del documento que se debe recuperar. Esta propiedad admite enlaces similares a "{queueTrigger}", que usarán el valor de cadena del mensaje en cola como identificador del documento.
- `connection`: esta cadena debe ser una configuración de la aplicación establecida en el punto de conexión de su cuenta de DocumentDB. Si elige la cuenta en la pestaña Integrar, se creará una nueva configuración de la aplicación con un nombre que tiene el formato siguiente: yourAccount\_DOCUMENTDB. Si necesita crear manualmente la configuración de la aplicación, la cadena de conexión real deberá tener el formato siguiente: AccountEndpoint=<punto de conexión de su cuenta>;AccountKey=<su clave de acceso primaria>;.
- `direction: debe establecerse en *"in"*.

Ejemplo de *function.json*:
 
	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "id" : "{queueTrigger}",
	      "connection": "MyAccount_DOCUMENTDB",     
	      "direction": "in"
	    }
	  ],
	  "disabled": false
	}

#### Ejemplo de código de entrada de Azure DocumentDB para un desencadenador de cola de C#
 
Con el archivo function.json de ejemplo anterior, el enlace de entrada de DocumentDB recuperará el documento con el identificador que coincida con la cadena del mensaje en la cola y lo pasará al parámetro 'document'. Si no se encuentra el documento, el parámetro 'document' será nulo. El documento se actualiza entonces con el nuevo valor de texto cuando se completa la función.
 
	public static void Run(string myQueueItem, dynamic document)
	{   
	    document.text = "This has changed.";
	}

#### Ejemplo de código de entrada de Azure DocumentDB para un desencadenador de cola de Node.js
 
Con el ejemplo de function.json anterior, el enlace de entrada de DocumentDB recuperará el documento con el identificador que coincida con la cadena del mensaje en cola y lo pasará a la propiedad del enlace `documentIn`. En las funciones de Node.js, los documentos actualizados no se envían de nuevo a la colección. Sin embargo, puede pasar el enlace de entrada directamente a un enlace de salida de DocumentDB denominado `documentOut` para admitir las actualizaciones. Este ejemplo de código actualiza la propiedad de texto del documento de entrada y lo establece como documento de salida.
 
	module.exports = function (context, input) {   
	    context.bindings.documentOut = context.bindings.documentIn;
	    context.bindings.documentOut.text = "This was updated!";
	    context.done();
	};

## <a id="docdboutput"></a> Enlaces de salida de Azure DocumentDB

Las funciones pueden escribir documentos JSON en una base de datos de Azure DocumentDB mediante el enlace de salida de **documento de Azure DocumentDB**. Para obtener más información sobre Azure DocumentDB, consulte [Introducción a DocumentDB: una base de datos de JSON NoSQL](../documentdb/documentdb-introduction.md) y [Tutorial de NoSQL: Crear una aplicación de consola de C# DocumentDB](../documentdb/documentdb-get-started.md).

#### function.json para enlace de salida de DocumentDB

El archivo function.json ofrece las siguientes propiedades:

- `name`: nombre de la variable utilizado en el código de función para el documento nuevo.
- `type`: se debe establecer en *"documentdb"*.
- `databaseName`: la base de datos que contiene la colección en la que se creará el nuevo documento.
- `collectionName`: la colección en la que se creará el nuevo documento.
- `createIfNotExists`: es un valor booleano que indica si la colección se creará si no existe. El valor predeterminado es *false*. Esto se debe a que se crean nuevas colecciones con rendimiento reservado, lo cual tiene implicaciones de precios. Para obtener más información, visite la [página de precios](https://azure.microsoft.com/pricing/details/documentdb/).
- `connection`: esta cadena debe ser una **configuración de la aplicación** establecida en el punto de conexión de su cuenta de DocumentDB. Si elige la cuenta en la pestaña **Integrar**, se creará una nueva configuración de la aplicación con un nombre que tiene el formato siguiente: `yourAccount_DOCUMENTDB`. Si necesita crear manualmente la configuración de la aplicación, la cadena de conexión real deberá tener el formato siguiente: `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;`.
- `direction`: debe establecerse en *out*.
 
Function.json de ejemplo:

	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "createIfNotExists": false,
	      "connection": "MyAccount_DOCUMENTDB",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}


#### Ejemplo de código de salida de Azure DocumentDB para un desencadenador de cola de Node.js

	module.exports = function (context, input) {
	   
	    context.bindings.document = {
	        text : "I'm running in a Node function! Data: '" + input + "'"
	    }   
	 
	    context.done();
	};

El documento de salida:

	{
	  "text": "I'm running in a Node function! Data: 'example queue data'",
	  "id": "01a817fe-f582-4839-b30c-fb32574ff13f"
	}
 

#### Ejemplo de código de salida de Azure DocumentDB para un desencadenador de cola de C#


	using System;

	public static void Run(string myQueueItem, out object document, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
	   
	    document = new {
	        text = $"I'm running in a C# function! {myQueueItem}"
	    };
	}


#### Nombre del archivo de configuración de ejemplo de código de salida de Azure DocumentDB

Si desea establecer el nombre del documento en la función, establezca el valor `id`. Por ejemplo, si se descartó el contenido JSON de un empleado en una cola similar a la siguiente:

	{
	  "name" : "John Henry",
      "employeeId" : "123456",
	  "address" : "A town nearby"
	}

Podría utilizar el siguiente código de C# en una función de desencadenador de cola:
	
	#r "Newtonsoft.Json"
	
	using System;
	using Newtonsoft.Json;
	using Newtonsoft.Json.Linq;
	
	public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
	    
	    dynamic employee = JObject.Parse(myQueueItem);
	    
	    employeeDocument = new {
	        id = employee.name + "-" + employee.employeeId,
	        name = employee.name,
	        employeeId = employee.employeeId,
	        address = employee.address
	    };
	}

Salida de ejemplo:

	{
	  "id": "John Henry-123456",
	  "name": "John Henry",
	  "employeeId": "123456",
	  "address": "A town nearby"
	}

## Pasos siguientes

[AZURE.INCLUDE [pasos siguientes](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0824_2016-->