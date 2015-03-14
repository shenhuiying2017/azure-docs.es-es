<properties
	pageTitle="Autorización de usuarios del lado servidor en Servicios móviles con el back-end de .NET | Centro de desarrollo móvil"
	description="Obtenga información sobre cómo autorizar a los usuarios en el back-end de .NET de Servicios móviles de Azure"
	services="mobile-services"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.topic="article"
	ms.date="2/18/2015"
	ms.author="krisragh"/>

# Autorización de usuarios del lado servidor en Servicios móviles

> [AZURE.SELECTOR-LIST (Plataforma | Backend)]
- [(Cualquiera | .NET)](/es-es/documentation/articles/mobile-services-dotnet-backend-service-side-authorization/)
- [(Cualquiera | Javascript)](/es-es/documentation/articles/mobile-services-javascript-backend-service-side-authorization/)

En este tema se muestra cómo usar la lógica del lado servidor para autorizar a los usuarios.  En este tutorial, modificará los métodos de acceso a datos en .NET, filtrará consultas según los identificadores de usuario y proporcionará a los usuarios acceso solamente a sus propios datos.

Este tutorial se basa en el Inicio rápido de Servicios móviles y en el tutorial [Agregar autenticación a la aplicación de Servicios móviles existente]. Complete primero [Agregar autenticación a la aplicación de Servicios móviles existente].

## <a name="register-scripts"></a>Modificación de los métodos de acceso a datos

1. En Visual Studio, abra el proyecto móvil, expanda la carpeta DataObjects y abra **TodoItem.cs**. La clase **TodoItem** define el objeto de datos y tiene que agregar una propiedad **UserId** que usar para el filtrado. Agregue la nueva propiedad UserId siguiente a la clase**TodoItem**:

		public string UserId { get; set; }

	>[AZURE.NOTE] Para realizar este cambio en el modelo de datos y mantener los datos existentes en la base de datos, debe usar [Migraciones de Code First](/es-es/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations).

2. En el Explorador de soluciones de Visual Studio, expanda la carpeta Controladores y abra **TodoItemController.cs**. Busque el método **PostTodoItem** y agregue el código siguiente al principio del método. Este código agrega el identificador de usuario del usuario autenticado al elemento antes de que este se inserte en la tabla TodoItem.

			// Get the logged in user
			var currentUser = User as ServiceUser;

			// Set the user ID on the item
			item.UserId = currentUser.Id;

3. Busque el método **GetAllTodoItems** y reemplace la instrucción **return** siguiente con esta línea de código. Esta consulta filtra los objetos TodoItem devueltos de manera que cada usuario solo recibe los elementos que inserte.

				// Get the logged in user
				var currentUser = User as ServiceUser;

				return Query().Where(todo => todo.UserId == currentUser.Id);

4. Vuelva a publicar el proyecto de servicio móvil en Azure.


## <a name="test-app"></a>Prueba de la aplicación

1. Observe que cuando ejecute la aplicación del lado cliente, aunque ya existan elementos en la base de datos de tutoriales anteriores, no se devuelven elementos. Esto ocurre porque se insertaron elementos anteriores sin la columna Id. de usuario y ahora cuentan con valores nulos.

2. Si dispone de cuentas de inicio de sesión adicionales, compruebe que los usuarios puedan ver solamente sus propios datos si cierran y eliminan la aplicación, y luego la vuelven a ejecutar. Cuando aparezca el cuadro de diálogo de credenciales de inicio de sesión, escriba otro inicio de sesión y compruebe que los elementos especificados no se muestran en el inicio de sesión anterior.



<!-- Anchors. -->
[Registro de scripts de servidor]: #register-scripts
[Pasos siguientes]:#next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[Agregar autenticación a la aplicación de Servicios móviles existente]: /es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
[Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push

[Referencia conceptual de Servicios móviles con .NET]: /es-es/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/

<!--HONumber=45--> 
