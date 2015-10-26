<properties
	pageTitle="Uso del SDK de iOS para Aplicaciones móviles de Azure"
	description="Uso del SDK de iOS para Aplicaciones móviles de Azure"
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="09/28/2015"
	ms.author="krisragh"/>

# Uso de la biblioteca de cliente de iOS para Aplicaciones móviles de Azure

[AZURE.INCLUDE [app-service-mobile-selector-offline-preview](../../includes/app-service-mobile-selector-client-library.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Esta guía muestra cómo realizar algunas tareas comunes a través del [SDK de iOS de Aplicaciones móviles de Azure](https://go.microsoft.com/fwLink/?LinkID=266533&clcid=0x409). Si está familiarizado con Aplicaciones móviles de Azure, complete primero [Inicio rápido de Aplicaciones móviles de Azure] para crear un back-end, crear una tabla y descargar un proyecto de Xcode para iOS pregenerada. En esta guía, nos centramos en el SDK de iOS de cliente. Para obtener más información sobre el SDK del lado servidor de .NET para el back-end, consulte [Trabajo con el back-end de .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

##<a name="Setup"></a>Configuración y requisitos previos

En esta guía se asume que ha creado un back-end con una tabla. En esta guía se asume que la tabla tiene el mismo esquema que las tablas de dichos tutoriales. En esta guía también se supone que en el código hace referencia a `WindowsAzureMobileServices.framework` e importa `WindowsAzureMobileServices/WindowsAzureMobileServices.h`.

##<a name="create-client"></a>Creación del cliente

Para obtener acceso al back-end de Aplicaciones móviles de Azure en el proyecto, cree un `MSClient`. Reemplace `AppUrl` por la dirección URL de la aplicación. Puede dejar `gatewayURLString` y `applicationKey` vacías. Si configura una puerta de enlace para la autenticación, rellene `gatewayURLString` con la dirección URL de la puerta de enlace.

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl" gatewayURLString:@"" applicationKey:@""];
```

##<a name="table-reference"></a>Creación de una referencia de tabla

Para acceder a los datos o actualizarlos, cree una referencia a la tabla de back-end. Reemplace `TodoItem` por el nombre de la tabla.

```
	MSTable *table = [client tableWithName:@"TodoItem"];
```

##<a name="querying"></a>Consulta de datos

Para crear una consulta de base de datos, consulte el objeto `MSTable`. La consulta siguiente obtiene todos los elementos de `TodoItem` y registra el texto de cada elemento.

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) { // error is nil if no error occured
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) { // items is NSArray of records that match query
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

##<a name="filtering"></a>Filtro de datos devueltos

Para filtrar los resultados, hay muchas opciones disponibles.

Para filtrar mediante un predicado, use un `NSPredicate` y `readWithPredicate`. Los siguientes filtros devolvieron datos para buscar solo los elementos Todo incompletos.

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table and update the items property with the results from the service
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

##<a name="query-object"></a>Uso de MSQuery

Para realizar una consulta compleja (como de ordenación y paginación), cree un objeto `MSQuery` directamente o mediante un predicado:

```
    MSQuery *query = [table query];
    MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

`MSQuery` le permite controlar varios comportamientos de consulta, incluidos los siguientes. Ejecutar una consulta `MSQuery` llamando a `readWithCompletion` en ella, como se muestra en el ejemplo siguiente. * Especificar el orden de los resultados * Limitar los campos que desea devolver * Limitar el número de registros a devolver * Especificar el recuento total de la respuesta * Especificar parámetros de cadena de consulta personalizados en la solicitud * Aplicar funciones adicionales


## <a name="sorting"></a>Ordenación de datos con MSQuery

Para ordenar los resultados, echemos un vistazo a un ejemplo. Para ordenar en primer lugar en orden ascendente por campo `text` y, a continuación, por orden descendente por campo `completion`, invoque `MSQuery`:

```
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```


## <a name="selecting"></a><a name="parameters"></a>Limitación de campos y expansión de los parámetros de cadena de consulta con MSQuery

Para limitar los campos que se devolverán en una consulta, especifique los nombres de los campos en la propiedad **selectFields**. Esto solamente devuelven los campos de texto y aquellos que se hayan rellenado:

```
	query.selectFields = @[@"text", @"completed"];
```

Para incluir parámetros de cadena de consulta adicionales en la solicitud al servidor (por ejemplo, porque los utiliza un script de servidor personalizado), introduzca `query.parameters`:

```
	query.parameters = @{
		@"myKey1" : @"value1",
		@"myKey2" : @"value2",
	};
```

##<a name="inserting"></a>Insertar datos

Para insertar una nueva fila en la tabla, cree un nuevo `NSDictionary` e invoque `table insert`. Servicios móviles genera automáticamente columnas nuevas basadas en `NSDictionary` si [Esquema dinámico] no está deshabilitado.

Si no se proporciona `id`, el backend genera automáticamente un nuevo identificador único. Proporcione su propio `id` para utilizar direcciones de correo electrónico, nombres de usuario o sus propios valores personalizados como Id. Proporcionar su propio ID puede facilitar las combinaciones y la lógica de la base de datos de tipo empresarial.

```
	NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
	[self.table insert:newItem completion:^(NSDictionary *result, NSError *error) {
		// The result contains the new item that was inserted,
		// depending on your server scripts it may have additional or modified
		// data compared to what was passed to the server.
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
						NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
		}
	}];
```

##<a name="modifying"></a>Modificación de datos

Para actualizar una fila existente, modifique un elemento y llame a `update`:

```
	NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
	[newItem setValue:@"Updated text" forKey:@"text"];
	[self.table update:newItem completion:^(NSDictionary *item, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

Como alternativa, proporcione el identificador de fila y el campo actualizado:

```
	[self.table update:@{@"id":@"37BBF396-11F0-4B39-85C8-B319C729AF6D", @"Complete":@YES} completion:^(NSDictionary *item, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

Como mínimo, debe establecerse el atributo `id` al realizar actualizaciones.

##<a name="deleting"></a>Eliminación de datos

Para eliminar un elemento, invoque `delete` con el elemento:

```
	[self.table delete:item completion:^(id itemId, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

O bien elimínelo proporcionando un identificador de fila:

```
	[self.table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
		// Handle error or perform additional logic as needed
	}];   
```

Como mínimo, el atributo `id` debe establecerse a la hora de efectuar eliminaciones.


##<a name="errors"></a>Gestión de errores

Al realizar una llamada a un servicio móvil, el bloque de finalización contiene un parámetro `NSError *error`. En caso de producirse un error, este parámetro no será nulo. En su código, debe marcar este parámetro y administrar el error según sea necesario.

El archivo [`<WindowsAzureMobileServices/MSError.h>`](https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h) define las constantes `MSErrorResponseKey`, `MSErrorRequestKey` y `MSErrorServerItemKey` para obtener más datos relacionados con el error. Además, el archivo define constantes para cada código de error. Para obtener un ejemplo sobre cómo utilizar estas constantes, consulte [Controlador de conflictos] para su uso de `MSErrorServerItemKey` y `MSErrorPreconditionFailed`.

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Inicio rápido de Aplicaciones móviles de Azure]: app-service-mobile-dotnet-backend-ios-get-started-preview.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[Esquema dinámico]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[Controlador de conflictos]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

<!---HONumber=Oct15_HO3-->