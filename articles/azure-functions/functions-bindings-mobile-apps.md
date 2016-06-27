<properties
	pageTitle="Enlaces de Aplicaciones móviles en funciones de Azure | Microsoft Azure"
	description="Descubra cómo utilizar los enlaces de Aplicaciones móviles en funciones de Azure."
	services="functions"
	documentationCenter="na"
	authors="ggailey777"
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
	ms.date="06/02/2016"
	ms.author="glenga"/>

# Enlaces de Aplicaciones móviles en funciones de Azure

Este artículo explica cómo configurar y codificar desencadenadores y enlaces de Aplicaciones móviles en funciones de Azure.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Las Aplicaciones móviles del Servicio de aplicaciones de Azure le permiten exponer los datos de punto de conexión de la tabla a clientes móviles. Estos mismos datos tabulares se pueden usar con los enlaces de entrada y salida de Funciones de Azure. Dado que admiten el esquema dinámico, las aplicaciones móviles del back-end de Node.js son ideales para exponer los datos tabulares que se van a usar con las funciones. El esquema dinámico está habilitado de forma predeterminada y debe deshabilitarse en una aplicación móvil de producción. Para obtener más información acerca de los puntos de conexión de tabla en un back-end de Node.js, consulte [Información general: Operaciones de tabla](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations). En Aplicaciones móviles, el back-end de Node.js admite la exploración y la edición de tablas en el portal. Para obtener más información, consulte [in-portal editing](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#in-portal-editing) (Edición en el portal) en el tema de SDK de Node.js. Si utiliza una aplicación móvil de back-end de .NET con Funciones de Azure, debe actualizar manualmente el modelo de datos, tal como lo requiera la función. Para obtener más información acerca de los punto de conexión de la tabla en una aplicación móvil del back-end .NET, consulte [Cómo definir un controlador de tabla](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller) en el tema de SDK de back-end de Node.js.

## Creación de una variable de entorno para la dirección URL de back-end de aplicación móvil

Los enlaces de Aplicaciones móviles actualmente requieren la creación de una variable de entorno que devuelva la dirección URL del propio back-end de la aplicación móvil. Esta dirección URL puede encontrarse en el [Portal de Azure](https://portal.azure.com); para ello, busque su aplicación móvil y abra la hoja.

![Hoja de Aplicaciones móviles en el portal de Azure](./media/functions-bindings-mobile-apps/mobile-app-blade.png)

Para establecer esta dirección URL como una variable de entorno en su Function App:

1. En la aplicación de función del [portal de Funciones de Azure](https://functions.azure.com/signin), haga clic en **Function App Settings** (Configuración de Function App) > **Go to App Service settings** (Ir a la configuración del Servicio de aplicaciones). 

	![Hoja Configuración de Function App](./media/functions-bindings-mobile-apps/functions-app-service-settings.png)

2. En Function App, haga clic en **Toda la configuración** y desplácese hacia abajo hasta **Configuración de la aplicación**. En **configuración de la aplicación**, escriba un nuevo **Nombre** para la variable de entorno, pegue la dirección URL en **Valor**, asegúrese de utilizar el esquema HTTPS y, a continuación, haga clic en **Guardar** y cierre la hoja Function App para volver al portal de funciones.

	![Agregar una variable de entorno de configuración de aplicación](./media/functions-bindings-mobile-apps/functions-app-add-app-setting.png)

Ahora puede establecer esta variable de entorno nuevo como el campo *conexión* en los enlaces.

## <a id="mobiletablesapikey"></a> Use una clave de API para proteger el acceso a los puntos de conexión de las tablas de Aplicaciones móviles.

En Funciones de Azure, los enlaces de tablas móviles permiten especificar una clave de API, que es un secreto compartido que se puede utilizar para evitar el acceso no deseado desde aplicaciones que no sean sus funciones. Las Aplicaciones móviles no incluyen compatibilidad integrada para la autenticación de la clave de API. Sin embargo, puede implementar una clave de API en su aplicación móvil de back-end de Node.js siguiendo los ejemplos descritos en [Azure App Service Mobile Apps backend implementing an API key](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key) (Back-end de Aplicaciones móviles del Servicio de aplicaciones de Azure que implementa una clave de API). De igual modo, puede implementar una clave de API en una [aplicación móvil de back-end de .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key).

>[AZURE.IMPORTANT] Esta clave de API no se debe distribuir con los clientes de la aplicación móvil, se debe distribuir de forma segura solo a los clientes del servicio, como Funciones de Azure.

## <a id="mobiletablesinput"></a>Enlace de entrada de Aplicaciones móviles de Azure

Los enlaces de entrada pueden cargar un registro desde un punto de conexión de tabla móvil y pasarlo directamente al enlace. Se puede determinar el identificador de registro según el desencadenador que invocó la función. En una función de C#, los cambios realizados en el registro se enviarán automáticamente a la tabla una vez que la función se complete correctamente.

#### function.json para el enlace de entrada de Aplicaciones móviles

El archivo *function.json* admite las siguientes propiedades:

- `name`: nombre de variable utilizado en el código de función para el nuevo registro.
- `type`: el tipo de enlace debe establecerse en *mobileTable*.
- `tableName`: tabla donde se creará el nuevo registro.
- `id`: identificador del registro que se recupera. Esta propiedad admite enlaces similares a `{queueTrigger}`, que usarán el valor de cadena del mensaje en cola como identificador del registro.
- `apiKey`: cadena que constituye el parámetro de la aplicación que especifica la clave de API opcional para la aplicación móvil. Esta es necesaria si la aplicación móvil usa una clave de API para restringir el acceso de cliente.
- `connection`: cadena que es el nombre de la variable de entorno en la configuración de la aplicación que especifica la dirección URL del back-end de su aplicación móvil.
- `direction`: dirección del enlace, que se debe establecer en *in*.

Archivo *function.json* de ejemplo:

	{
	  "bindings": [
	    {
	      "name": "record",
	      "type": "mobileTable",
	      "tableName": "MyTable",
	      "id" : "{queueTrigger}",
	      "connection": "My_MobileApp_Url",
	      "apiKey": "My_MobileApp_Key",
	      "direction": "in"
	    }
	  ],
	  "disabled": false
	}

#### Ejemplo de código de Aplicaciones móviles de Azure para un desencadenador de cola de C#

Según el archivo function.json del ejemplo anterior, el enlace de entrada recupera el registro de un punto de conexión de tabla de Aplicaciones móviles con el identificador que coincide con la cadena del mensaje en la cola y lo pasa al parámetro *record*. Si no se encuentra el registro, el parámetro será nulo. El registro se actualiza con el nuevo valor de *Text* cuando se sale de la función.

	#r "Newtonsoft.Json"	
	using Newtonsoft.Json.Linq;
	
	public static void Run(string myQueueItem, JObject record)
	{
	    if (record != null)
	    {
	        record["Text"] = "This has changed.";
	    }    
	}

#### Ejemplo de código de Aplicaciones móviles de Azure para un desencadenador de cola de Node.js

Según el archivo function.json del ejemplo anterior, el enlace de entrada recupera el registro de un punto de conexión de tabla de Aplicaciones móviles con el identificador que coincide con la cadena del mensaje en la cola y lo pasa al parámetro *record*. En las funciones de Node.js, los registros actualizados no se envían de nuevo a la tabla. Este ejemplo de código escribe el registro recuperado en el registro.

	module.exports = function (context, input) {    
	    context.log(context.bindings.record);
	    context.done();
	};


## <a id="mobiletablesoutput"></a>Enlace de salida de Aplicaciones móviles de Azure

La función puede escribir un registro en un punto de conexión de tabla de Aplicaciones móviles mediante un enlace de salida.

#### function.json para el enlace de salida de Aplicaciones móviles

El archivo function.json admite las siguientes propiedades:

- `name`: nombre de variable utilizado en el código de función para el nuevo registro.
- `type`: el tipo de enlace que se debe establecer en *mobileTable*.
- `tableName`: la tabla donde se crea el nuevo registro.
- `apiKey`: cadena que constituye el parámetro de la aplicación que especifica la clave de API opcional para la aplicación móvil. Esta es necesaria si la aplicación móvil usa una clave de API para restringir el acceso de cliente.
- `connection`: cadena que es el nombre de la variable de entorno en la configuración de la aplicación que especifica la dirección URL del back-end de su aplicación móvil.
- `direction`: dirección de enlace, que se debe establecer en *out*.

Function.json de ejemplo:

	{
	  "bindings": [
	    {
	      "name": "record",
	      "type": "mobileTable",
	      "tableName": "MyTable",
	      "connection": "My_MobileApp_Url",
	      "apiKey": "My_MobileApp_Key",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}

#### Ejemplo de código de Aplicaciones móviles de Azure para un desencadenador de cola de C#

Este ejemplo de código de C# inserta un registro nuevo en un punto de conexión de tabla de Aplicaciones móviles con una propiedad *Text* en la tabla especificada en el enlace anterior.

	public static void Run(string myQueueItem, out object record)
	{
	    record = new {
	        Text = $"I'm running in a C# function! {myQueueItem}"
	    };
	}

#### Ejemplo de código de Aplicaciones móviles de Azure para un desencadenador de cola de Node.js

Este ejemplo de código de Node.js inserta un registro nuevo en un punto de conexión de tabla de Aplicaciones móviles con una propiedad *Text* en la tabla especificada en el enlace anterior.

	module.exports = function (context, input) {
	
	    context.bindings.record = {
	        text : "I'm running in a Node function! Data: '" + input + "'"
	    }   
	
	    context.done();
	};

## Pasos siguientes

[AZURE.INCLUDE [pasos siguientes](../../includes/functions-bindings-next-steps.md)]

<!----HONumber=AcomDC_0608_2016-->