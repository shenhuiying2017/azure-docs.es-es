<properties
	pageTitle="Cómo trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles | Servicio de aplicaciones de Azure"
	description="Obtenga información sobre cómo trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles del Servicio de aplicaciones de Azure"
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/18/2015"
	ms.author="glenga"/>

# Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure

En este tema se muestra cómo usar el SDK del servidor back-end de .NET en escenarios clave de Aplicaciones móviles del Servicio de aplicaciones de Azure. El SDK de Aplicaciones móviles de Azure le permite trabajar con clientes móviles de su aplicación ASP.NET.

## Cómo descargar e inicializar el SDK

El SDK está disponible en [NuGet.org]. Este paquete incluye la funcionalidad básica necesaria para comenzar a usar el SDK. Para inicializar el SDK, tendrá que realizar acciones en el objeto **HttpConfiguration**.

###Instalación del SDK

Para instalar el SDK, haga doble clic en el proyecto de servidor en Visual Studio, seleccione **Administrar paquetes de NuGet**, busque el paquete [Microsoft.Azure.Mobile.Server](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) y haga clic en **Instalar**.

###Inicialización del proyecto de servidor

Un proyecto de servidor backend de .NET se inicializa de manera similar a otros proyectos ASP.NET mediante la inclusión de una clase de inicio OWIN. Para agregar esta clase en Visual Studio, haga clic con el botón derecho en el proyecto de servidor y seleccione **Agregar** -> **Nuevo elemento** y después en **Web** -> **General** -> **Clase de inicio OWIN**.

Esto generará una clase con el atributo siguiente:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

En el método `Configuration()` de la clase de inicio OWIN, configure el proyecto de servidor mediante un objeto **HttpConfiguration** que representa las opciones de configuración para el servicio. En el ejemplo siguiente se inicializa el proyecto de servidor, sin características agregadas:

	// in OWIN startup class
	public void Configuration(IAppBuilder app)
	{
	    HttpConfiguration config = new HttpConfiguration();
	   
	    new MobileAppConfiguration()
	        // no added features
	        .ApplyTo(config);  
	    
	    app.UseWebApi(config);
	}

Para habilitar características individuales, debe llamar a los métodos de extensión del objeto **MobileAppConfiguration** antes de llamar a **ApplyTo**. Por ejemplo, el siguiente código agrega las rutas predeterminadas a todos los controladores de API durante la inicialización:

	new MobileAppConfiguration()
	    .MapApiControllers()
	    .ApplyTo(config);

Muchos de los métodos de extensión de características están disponibles a través de paquetes de NuGet adicionales que puede incluir, que se describen en la sección siguiente. El inicio rápido de servidor desde el Portal de Azure llama a **UseDefaultConfiguration()**. Es equivalente a la siguiente configuración:
    
		new MobileAppConfiguration()
			.AddMobileAppHomeController()             // from the Home package
			.MapApiControllers()
			.AddTables(                               // from the Tables package
				new MobileAppTableConfiguration()
					.MapTableControllers()
					.AddEntityFramework()             // from the Entity package
				)
			.AddPushNotifications()                   // from the Notifications package
			.MapLegacyCrossDomainController()         // from the CrossDomain package
			.ApplyTo(config);


### Extensiones de SDK

Los siguientes paquetes de extensión basados en NuGet proporcionan diversas características móviles que puede usar la aplicación. Las extensiones se habilitan durante la inicialización mediante el objeto **MobileAppConfiguration**.

- [Microsoft.Azure.Mobile.Server.Quickstart] admite la configuración básica de Aplicaciones móviles. Se agrega a la configuración mediante una llamada al método de extensión **UseDefaultConfiguration** durante la inicialización. Esta extensión incluye las siguientes extensiones: notificaciones, autenticación, entidad, tablas y paquetes principales y entre dominios . Esto es equivalente al proyecto de servidor de inicio rápido que se descarga desde el Portal de Azure.

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) Implementa el valor predeterminado de *esta página de aplicación móvil está funcionando* para la raíz del sitio web. Se agrega a la configuración mediante una llamada al método de extensión **AddMobileAppHomeController**.

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) incluye clases para trabajar con datos y configura la canalización de datos. Se agrega a la configuración mediante una llamada al método de extensión **AddTables**.

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) permite a Entity Framework acceder a los datos de la base de datos SQL. Se agrega a la configuración mediante una llamada al método de extensión **AddTablesWithEntityFramework**.

- [Microsoft.Azure.Mobile.Server.Authentication] habilita la autenticación y configura el middleware OWIN que se usa para validar los tokens. Se agrega a la configuración mediante una llamada a los métodos de extensión **AddAppServiceAuthentication** y **IAppBuilder**.**UseMobileAppAuthentication**.

- [Microsoft.Azure.Mobile.Server.Notifications] habilita las notificaciones push y define un punto de conexión de registro de inserción. Se agrega a la configuración mediante una llamada al método de extensión **AddPushNotifications**.

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) crea un controlador que sirve datos de la aplicación móvil a los exploradores web heredados. Se agrega a la configuración mediante una llamada al método de extensión **MapLegacyCrossDomainController**.

## Cómo definir un controlador de API personalizada

El controlador de API personalizada proporciona la funcionalidad más básica al back-end de la aplicación móvil mediante la exposición de un extremo. Controlador de API personalizada

1. En Visual Studio, haga clic con el botón derecho en la carpeta Controladores, luego, haga clic en **Agregar** > **Controlador**, seleccione **Controlador 2 de API web&mdash;Vacío** y haga clic en **Agregar**.

2. Asigne un **nombre de controlador**, como `CustomController`, y haga clic en **Agregar**. Esto crea una nueva clase **CustomController**, que se hereda de **ApiController**.

3. En el archivo de clase de controlador nuevo, agregue la siguiente instrucción Using:

		using Microsoft.Azure.Mobile.Server.Config;

4. Aplique el **MobileAppControllerAttribute** a la definición de clase de controlador de API, como en el ejemplo siguiente:

		[MobileAppController] 
		public class CustomController : ApiController
		{
		      //...
		}

4. En el archivo App\_Start/Startup.MobileApp.cs, agregue una llamada al método de extensión **MapApiControllers**, como en el ejemplo siguiente:

		new MobileAppConfiguration()
		    .MapApiControllers()
		    .ApplyTo(config);
    
	Tenga en cuenta que no es necesario llamar a **MapApiControllers** cuando se llama a **UseDefaultConfiguration**, ya que se inicializan todas las características.

Los clientes pueden acceder a un controlador aunque este no tenga **MobileAppControllerAttribute** aplicado, pero no lo consumirán correctamente si usan un SDK de cliente de aplicación móvil.

## Cómo definir un controlador de tabla

Un controlador de tabla proporciona acceso a datos de entidad en un almacén de datos basado en tabla, como la base de datos SQL o el almacenamiento de tablas de Azure. Los controladores de tabla se heredan de la clase genérica **TableController**, en la que el tipo genérico es una entidad del modelo que representa el esquema de tabla, de la siguiente manera:

	public class TodoItemController : TableController<TodoItem>
    {  
		//...
	}

Los controladores de tabla se inicializan mediante el método de extensión **AddTables**. En el ejemplo siguiente se inicializa un controlador de tabla que usa Entity Framework para el acceso a los datos:

    new MobileAppConfiguration().AddTables(
        new MobileAppTableConfiguration()
        .MapTableControllers()
        .AddEntityFramework()).ApplyTo(config);
 
Para obtener un ejemplo de un controlador de tabla que usa Entity Framework para tener acceso a los datos desde una base de datos SQL de Azure, consulte la clase **TodoItemController** en la descarga del proyecto de servidor de inicio rápido del Portal de Azure.

## Cómo agregar autenticación a un proyecto de servidor

Para agregar autenticación al proyecto de servidor, extienda el objeto **MobileAppConfiguration** y configure el middleware OWIN. Cuando instala el paquete [Microsoft.Azure.Mobile.Server.Quickstart] y llama al método de extensión **UseDefaultConfiguration**, puede continuar desde el paso 3.

1. En Visual Studio, instale el paquete [Microsoft.Azure.Mobile.Server.Authentication]. 

2. En el archivo de proyecto Startup.cs, agregue la siguiente línea de código al principio del método **Configuration**:

		app.UseMobileAppAuthentication(config);

	Esto agrega el componente middleware OWIN que permite que su aplicación móvil de Azure valide los tokens que emite la puerta de enlace asociada del Servicio de aplicaciones.

3. Agregue el atributo `[Authorize]` a cualquier controlador o método que requiera autenticación. Ahora, los usuarios deben autenticarse para tener acceso a ese extremo o a aquellas API específicas.

Para obtener información sobre cómo autenticar a los clientes en el back-end de Aplicaciones móviles, consulte [Incorporación de autenticación a la aplicación](app-service-mobile-dotnet-backend-ios-get-started-users.md).

## Cómo agregar notificaciones push a un proyecto de servidor

Para agregar notificaciones push al proyecto de servidor, extienda el objeto **MobileAppConfiguration** y cree un cliente de Centros de notificaciones. Cuando instala el paquete [Microsoft.Azure.Mobile.Server.Quickstart] y llama al método de extensión **UseDefaultConfiguration**, puede continuar desde el paso 3.

1. En Visual Studio, haga clic con el botón derecho en el proyecto de servidor, luego, haga clic en **Administrar paquetes de NuGet**, busque Microsoft.Azure.Mobile.Server.Notifications` y, por último, haga clic en **Instalar**. Esto instala el paquete [Microsoft.Azure.Mobile.Server.Notifications].
 
3. Repita este paso para instalar el paquete `Microsoft.Azure.NotificationHubs`, que incluye la biblioteca de cliente de Centros de notificaciones.

2. Vaya a la carpeta App\_Startup, abra el archivo de proyecto WebApiConfig.cs y agregue una llamada al método de extensión **AddPushNotifications** durante la inicialización, que tiene el siguiente aspecto:

		new MobileAppConfiguration()
			// other features...
			.AddPushNotifications()
			.ApplyTo(config);

	Esto crea el extremo de registro de notificaciones push en el proyecto de servidor. Los clientes usan este extremo para registrarse en el centro de notificaciones asociado. Ahora, deberá agregar el cliente de Centros de notificaciones que se usa para enviar notificaciones.

3. En un controlador desde el que desee enviar notificaciones push, agregue la siguiente instrucción Using:

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;

4. Agregue el siguiente código, que crea un nuevo cliente de Centros de notificaciones:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

En este momento, puede usar el cliente de Centros de notificaciones para enviar notificaciones push a dispositivos registrados. Para obtener más información, consulte [Incorporación de notificaciones push a la aplicación](app-service-mobile-ios-get-started-push.md). Para obtener más información acerca de todo lo que puede hacer con los Centros de notificaciones, consulte [Información general de los Centros de notificaciones](../notification-hubs/notification-hubs-overview.md).

## Cómo publicar el proyecto de servidor

Siga los pasos que se indican a continuación para publicar el proyecto de servidor en Azure:

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]


[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/

<!---HONumber=Nov15_HO1-->