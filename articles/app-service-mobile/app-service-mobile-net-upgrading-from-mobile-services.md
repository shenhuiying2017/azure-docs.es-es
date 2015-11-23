<properties 
	pageTitle="Actualización de Servicios móviles a Servicio de aplicaciones de Azure" 
	description="Obtenga información acerca de cómo actualizar fácilmente la aplicación de Servicios móviles a una aplicación móvil del Servicio de aplicaciones." 
	services="app-service\mobile" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/03/2015" 
	ms.author="mahender"/>

# Migración de su Servicio móvil de Azure .NET a Servicio de aplicaciones

Aplicaciones móviles del Servicio de aplicaciones es una nueva forma de crear aplicaciones móviles con Microsoft Azure. Para obtener más información, consulte [¿Qué es Aplicaciones móviles?].

En este tema se describe cómo migrar una aplicación de back-end .NET existente de Servicios móviles de Azure a una nueva aplicación móvil del Servicio de aplicaciones. Cuando realice esta migración, la aplicación de Servicios móviles existente puede continuar funcionando.

Cuando un back-end móvil se actualiza a Servicio de aplicaciones de Azure, accede a todas las características de Servicio de aplicaciones y se facturan de acuerdo con los [precios de Servicio de aplicaciones], no según los precios de Servicios móviles.

##Migración frente a actualización

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

###Mejoras en el SDK de servidor .NET de Aplicaciones móviles

La actualización a nuevos [paquetes NuGet del SDK de Aplicaciones móviles](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) ofrece las siguientes ventajas:

- Más flexibilidad en las dependencias de NuGet. El entorno de hospedaje ya no proporciona sus propias versiones de paquetes NuGet, por lo que puede usar versiones compatibles alternativas. Sin embargo, si hay nuevas actualizaciones de seguridad o correcciones de errores importantes para el SDK de servidor móvil o dependencias, debe actualizar el servicio manualmente.

- Compatibilidad con nuevas características de autenticación de Servicio de aplicaciones, que le permiten usar una configuración de autenticación común a través de aplicaciones web y móviles. Las características de autenticación ahora también están en su propio componente middleware OWIN, lo que significa que puede usarse la misma configuración de autenticación para rutas móviles y web.

- Compatibilidad con otras rutas y tipos de proyecto ASP.NET. Ahora puede hospedar controladores Web API y MVC en el mismo proyecto que el proyecto de back-end móvil.

- Más flexibilidad en el SDK móvil. Puede controlar explícitamente qué características y rutas se configuran, como la autenticación, las API de la tabla y el punto de conexión de registro de inserción. Para obtener más información, consulte [Uso del SDK de servidor .NET para Aplicaciones móviles de Azure](app-service-mobile-net-upgrading-from-mobile-services.md#server-project-setup).

- Más opciones para marcos de DI. El SDK de servidor .NET de Servicios móviles tenía una dependencia en autofac, pero el SDK de servidor de Aplicaciones móviles permite usar otros marcos, como [Unity](https://unity.codeplex.com/).

>[AZURE.NOTE]Los SDK de cliente de Servicios móviles **no** son compatibles con el nuevo SDK de servidor de Aplicaciones móviles. Si tiene aplicaciones de cliente móviles que se conectan al servicio móvil existente, debe mantener el servicio en ejecución hasta que todos los clientes móviles se hayan actualizado al **SDK de cliente** de Aplicaciones móviles.
> 
> El servicio móvil existente se puede *migrar* automáticamente a Servicio de aplicaciones sin realizar cambios en las aplicaciones cliente existentes. Para obtener más información sobre la migración, consulte [Migración de un servicio móvil existente a Servicio de aplicaciones].


##<a name="overview"></a>Información general básica de actualización
La manera más sencilla de actualizar es crear una nueva instancia de una aplicación móvil del Servicio de aplicaciones. En muchos casos, la actualización será tan sencilla como cambiar al nuevo SDK de servidor de Aplicaciones móviles y volver a publicar el código en una nueva aplicación móvil. Sin embargo, hay algunos escenarios que requieren una configuración adicional, como los escenarios de autenticación avanzada y el trabajo con trabajos programados. Cada una de ellos se trata en las secciones siguientes.

>[AZURE.NOTE]Se aconseja leer y comprender totalmente el resto de este tema antes de iniciar una Se aconseja leer y comprender totalmente el resto de este tema antes de iniciar una actualización. Tome nota de cualquier característica usada que se mencione a continuación.

Puede mover y probar el código a su ritmo. Cuando el back-end de aplicaciones móvil está listo, puede publicar una nueva versión de la aplicación cliente. En este punto, tendrá dos copias del back-end de la aplicación ejecutándose en paralelo. Deberá asegurarse de que las correcciones de errores se apliquen a ambos. Por último, una vez que los usuarios hayan actualizado a la versión más reciente, puede eliminar el Servicio móvil original.

El conjunto completo de pasos para esta actualización es el siguiente:

1. Cree y configure una nueva aplicación móvil
2. Aborde cualquier problema de autenticación
3. Publique una versión nueva de la aplicación cliente
4. Elimine la instancia original de Servicios móviles


##<a name="mobile-app-version"></a>Configuración de una versión para Aplicación móvil de la aplicación
El primer paso en la actualización es crear el recurso de aplicación móvil que hospedará la nueva versión de la aplicación. Puede crear una nueva Aplicación móvil en el [Portal de administración de vista previa de Azure]. Puede consultar el tema [Creación de una aplicación móvil] para obtener más detalles.

Probablemente deseará usar la misma base de datos y la base de datos central de notificaciones como hizo en Servicios móviles. Puede copiar estos valores desde la pestaña **Configurar** de la sección de Servicios móviles del [Portal de administración de Azure]. En **Cadenas de conexión**, copie `MS_NotificationHubConnectionString` y `MS_TableConnectionString`. Vaya al sitio de la aplicación móvil y seleccione **Configuración** > **Configuración de la aplicación**, y agregue estas cadenas a **Cadenas de conexión** sobrescribiendo los valores existentes.

Las aplicaciones móviles ofrecen un nuevo [SDK de servidor de aplicaciones móviles] que proporciona en gran medida la misma funcionalidad que el tiempo de ejecución de Servicios móviles. Primero, debe quitar NuGet de Servicios móviles de un proyecto existente y, en su lugar, incluir el SDK de servidor. Para esta actualización, la mayoría de los desarrolladores prefieren descargar e instalar el paquete `Microsoft.Azure.Mobile.Server.Quickstart` ya que, de esta manera, se extrae el conjunto necesario completo. Luego, en WebApiConfig.cs, puede reemplazar

    // Use this class to set configuration options for your mobile service
    ConfigOptions options = new ConfigOptions();
    
    // Use this class to set WebAPI configuration options
    HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

por

    HttpConfiguration config = new HttpConfiguration();

    new MobileAppConfiguration()
	    .UseDefaultConfiguration()
	    .ApplyTo(config);


>[AZURE.NOTE]Si desea obtener más información sobre el nuevo SDK de servidor .NET y cómo agregar o quitar características de la aplicación, consulte el tema [Uso del SDK de servidor .NET].

Existen algunos otros cambios entre los SDK de Aplicaciones móviles y Servicios móviles, pero resultan fáciles de abordar. En todo el proyecto, puede que necesite modificar algo mediante instrucciones, y Visual Studio estará allí para ayudarle.

Debe agregar el atributo `[MobileAppController]` en todos los ApiControllers. Para ello, solo tiene que colocar ese elemento Decorator directamente antes de la definición de clase.

Ya no hay un atributo `[AuthorizeLevel]` y, en su lugar, debe decorar los controladores y los métodos con el atributo estándar `[Authorize]` de ASP.NET. Tenga en cuenta también que los controladores que no tenían `[AuthorizeLevel]` ya no estarán protegidos por una clave de aplicación y se tratarán como públicos. El nuevo SDK ya no hace uso de una clave de aplicación y una clave maestra.

Para la inserción, el elemento principal que puede faltar desde el SDK de servidor es la clase PushRegistrationHandler. Los registros se controlan de forma ligeramente diferente en las aplicaciones móviles y los registros sin etiqueta están habilitados de forma predeterminada. Se puede lograr la administración de etiquetas mediante API personalizadas. Consulte el tema [Incorporación de notificaciones push a la aplicación móvil] para obtener más información.

Los trabajos programados no están integrados en las aplicaciones móviles, por lo que es necesario actualizar individualmente los trabajos existentes que tiene en el back-end de .NET. Una opción es crear un [trabajo web] programado en el sitio del código de aplicación móvil. También puede configurar un controlador que contiene el código de trabajo y configurar el [Programador de Azure] para acertar ese extremo en la programación prevista.

El objeto `ApiServices` ya no forma parte del SDK. Para obtener acceso a la configuración de la aplicación móvil, puede usar lo siguiente:

    MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings(); 

De forma similar, el registro ahora se realiza mediante la escritura de seguimiento estándar de ASP.NET:

    ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
    traceWriter.Info("Hello, World");  

##<a name="authentication"></a>Consideraciones de autenticación
Una de las mayores diferencias entre Aplicaciones móviles y Servicios móviles es que el inicio de sesión ahora está controlado por la puerta de enlace de Servicio de aplicaciones en el caso de Aplicaciones móviles, y no por el sitio que ejecuta el código. Si el grupo de recursos aún no tiene una puerta de enlace, puede aprovisionar una. Para ello, vaya hasta la aplicación móvil de Azure en el Portal de administración. Después, seleccione **Configuración** y luego **Autenticación del usuario** en la categoría **Móvil**. Haga clic en **Crear** para asociar una puerta de enlace con la aplicación móvil.

Más allá de esto, para la mayoría de las aplicaciones, no será necesario realizar ninguna acción adicional, pero hay algunos escenarios avanzados que se deben tener en cuenta.

Para la mayoría de las aplicaciones será suficiente usar simplemente un nuevo registro con el proveedor de identidad de destino; puede obtener información acerca de cómo agregar una identidad a una aplicación del Servicio de aplicaciones siguiendo el tutorial [Adición de autenticación a una aplicación móvil].

Si la aplicación tiene dependencias de identificadores de usuario, por ejemplo, al almacenarlos en una base de datos, es importante tener en cuenta que los identificadores de usuario entre los Servicios móviles y las aplicaciones móviles del Servicio de aplicaciones son diferentes. Sin embargo, es posible obtener el identificador de usuario de Servicios móviles en la Aplicación móvil del Servicio de aplicaciones mediante lo siguiente (con Facebook como ejemplo):

    MobileAppUser = (MobileAppUser) this.User;
    FacebookCredentials creds = await user.GetIdentityAsync<FacebookCredentials>();
    string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

Además, si toma todas las dependencias de los identificadores de usuario, es importante aprovechar el mismo registro que un proveedor de identidad, si es posible. Los identificadores de usuario normalmente tienen como ámbito el registro de la aplicación que se utilizó, por lo que introducir un nuevo registro podría crear problemas con la correspondencia entre usuarios y sus datos. Si la aplicación necesita utilizar el mismo registro de proveedor de identidades por cualquier motivo, puede usar los siguientes pasos:

1. Copie sobre el identificador de cliente y la información de conexión del secreto de cliente para cada proveedor utilizado por la aplicación.
2. Agregue los extremos /signin-* de la puerta de enlace como un URI de redirección adicional para cada proveedor. 

>[AZURE.NOTE]Algunos proveedores, como Twitter y Cuenta Microsoft, no permiten especificar varios URI de redirección en dominios diferentes. Si su aplicación usa uno de estos proveedores y depende de identificadores de usuario, se recomienda no intentar actualizar en este momento.

##<a name="updating-clients"></a>Actualización de clientes
Una vez que tiene un back-end de aplicaciones móviles operativo, puede actualizar la aplicación cliente para utilizar la nueva aplicación móvil. Las aplicaciones móviles también incluirán una nueva versión del SDK de cliente de Aplicaciones móviles, que permitirá a los desarrolladores aprovechar las nuevas características del Servicio de aplicaciones. Una vez que tenga una versión de la Aplicación móvil de su back-end, puede publicar una nueva versión de la aplicación cliente que aprovecha la nueva versión del SDK.

El cambio principal necesario en el código de cliente está en el constructor. Además de la dirección URL de su sitio de aplicación móvil, debe proporcionar la dirección URL de la puerta de enlace del Servicio de aplicaciones que hospeda la configuración de autenticación:

    public static MobileServiceClient MobileService = new MobileServiceClient(
        "https://contoso.azurewebsites.net", // URL of the Mobile App
        "https://contoso-gateway.azurewebsites.net", // URL of the App Service Gateway
        "" // Formerly app key. To be removed in future client SDK update
    );

Esto permitirá que el cliente para enrutar las solicitudes a los componentes de la Aplicación móvil. Puede encontrar más detalles específicos para la plataforma de destino mediante el correspondiente tema [Creación de una aplicación móvil].

En la misma actualización, deberá ajustar las llamadas de registro de notificaciones push que realice. Hay nuevas API que realizar mejoras en el proceso de registro (con Windows como ejemplo):

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    await MobileService.GetPush().Register(channel.Uri); 

Consulte el tema [Incorporación de notificaciones push a la aplicación móvil] y [Envío de notificaciones push multiplataforma] para obtener detalles específicos de la plataforma de destino.

Una vez que los clientes han tenido la oportunidad de recibir estas actualizaciones, puede eliminar la versión de Servicios móviles de su aplicación. Llegados a este punto, ha actualizado completamente a una aplicación móvil del Servicio de aplicaciones mediante el SDK de servidor de Aplicaciones móviles más reciente.

<!-- URLs. -->

[Portal de administración de vista previa de Azure]: https://portal.azure.com/
[Portal de administración de Azure]: https://manage.windowsazure.com/
[¿Qué es Aplicaciones móviles?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /es-ES/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[SDK de servidor de aplicaciones móviles]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Creación de una aplicación móvil]: app-service-mobile-xamarin-ios-get-started.md
[Incorporación de notificaciones push a la aplicación móvil]: app-service-mobile-xamarin-ios-get-started-push.md
[Adición de autenticación a una aplicación móvil]: app-service-mobile-xamarin-ios-get-started-users.md
[Programador de Azure]: /es-ES/documentation/services/scheduler/
[trabajo web]: ../app-service-web/websites-webjobs-resources.md
[Envío de notificaciones push multiplataforma]: app-service-mobile-xamarin-ios-push-notifications-to-user.md
[Uso del SDK de servidor .NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-dotnet-backend-migrating-from-mobile-services.md
[Migración de un servicio móvil existente a Servicio de aplicaciones]: app-service-mobile-dotnet-backend-migrating-from-mobile-services.md
[precios de Servicio de aplicaciones]: https://azure.microsoft.com/es-ES/pricing/details/app-service/

<!---HONumber=Nov15_HO3-->