<properties 
	pageTitle="Migración desde Servicios móviles a una Aplicación móvil del Servicio de aplicaciones" 
	description="Obtenga información acerca de cómo migrar fácilmente la aplicación Servicios móviles a una Aplicación móvil del Servicio de aplicaciones" 
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
	ms.date="11/04/2015" 
	ms.author="mahender"/>

# Migración de su Servicio móvil de Azure a Servicio de aplicaciones 

En este tema se muestra cómo migrar una aplicación existente de Servicios móviles de Azure a una nueva Aplicación móvil del Servicio de aplicaciones. Todas las aplicaciones de Servicios móviles existentes pueden migrarse fácilmente a una Aplicación móvil del Servicio de aplicaciones. Durante una migración, la aplicación de Servicios móviles existente puede continuar funcionando.

>[AZURE.NOTE]Actualmente, Servicios móviles de back-end de .NET se puede migrar a Servicio de aplicaciones mediante una migración manual. Próximamente estará disponible una experiencia de migración inmediata para Node.js y .NET. Sin embargo, si realiza una migración manual, *no puede* conservar su dirección URL **service.azure mobile.net** existente.

Cuando una aplicación se migra a Servicio de aplicaciones de Azure, accede a todas las características de Servicio de aplicaciones y se facturan de acuerdo con los [precios de Servicio de aplicaciones], no según los precios de Servicios móviles.

### <a name="why-host"></a>¿Por qué hospedar en Servicio de aplicaciones?

El Servicio de aplicaciones proporciona un entorno de hospedaje repleto de características para su aplicación. Al hospedar en el Servicio de aplicaciones, el servicio obtiene acceso a ranuras de ensayo, dominios personalizados, soporte técnico del Administrador de tráfico y otras muchas características. Si bien puede [migrar un servicio móvil a una aplicación móvil en Servicio de aplicaciones], puede que algunos clientes quieran aprovechar estas características inmediatamente, sin realizar aún la actualización del SDK.

La limitación principal del hospedaje en el Servicio de aplicaciones es que los trabajos programados de Servicios móviles no están integrados y, o bien se debe configurar el Programador de Azure para llamar a las API personalizadas, o las características de trabajos web deben estar habilitadas.

Para obtener más información sobre las ventajas del Servicio de aplicaciones, consulte el tema [Servicios móviles frente a Servicio de aplicaciones].

##Migración frente a actualización

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

  - Para los proyectos de servidor basados en Node.js, el nuevo [SDK de Node.js de Aplicaciones móviles](https://github.com/Azure/azure-mobile-apps-node) proporciona una serie de nuevas características. Por ejemplo, ahora puede desarrollar y depurar localmente, usar cualquier versión de Node.js posterior a la 0.10 y personalizar con cualquier middleware de Express.js.

  - Para proyectos de servidor basados en .NET, los nuevos [paquetes NuGet del SDK de Aplicaciones móviles](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) tienen más flexibilidad en las dependencias de NuGet, son compatibles con las nuevas características de autenticación del Servicio de aplicaciones y están compuestos por cualquier proyecto de ASP.NET, incluido MVC. Para obtener más información sobre la actualización, consulte [Actualización de un servicio móvil de .NET existente a Servicio de aplicaciones](app-service-mobile-net-upgrading-from-mobile-services.md).


##<a name="understand"></a>Descripción de Aplicaciones móviles del Servicio de aplicaciones 

Aplicaciones móviles del Servicio de aplicaciones es una nueva forma de crear aplicaciones móviles con Microsoft Azure. Puede obtener más información acerca de las aplicaciones móviles en el tema [¿Qué son las aplicaciones móviles?]

En una migración de las aplicaciones móviles, se conserva toda la funcionalidad (y el código) de la aplicación existente. Además, hay nuevas características disponibles para la aplicación. En el modelo de aplicaciones móviles, el código se ejecuta realmente en una aplicación web (la nueva versión de Sitios web de Azure). El usuario tiene control total sobre la aplicación web y su funcionamiento. Además, ahora pueden usarse las características de Aplicaciones web que antes no estaban disponibles para los clientes de Servicios móviles, como el Administrador de tráfico y Espacios de implementación.


##Migración manual para el back-end .NET de Servicios móviles

En esta sección se muestra cómo hospedar un proyecto de Servicios móviles de .NET en el Servicio de aplicaciones de Azure. Con una aplicación hospedada de este modo puede usar todos los tutoriales de runtime de .NET de *Servicios móviles*, si bien es necesario sustituir las direcciones URL que usen el dominio azure-mobile.net por el dominio de la instancia del Servicio de aplicaciones.

Un proyecto de Servicios móviles también se puede hospedar en un [entorno del Servicio de aplicaciones]. Todo el contenido de este tema se sigue aplicando, pero el sitio tendrá el formato contoso.contosoase.p.azurewebsites.net en lugar de contoso.azurewebsites.net.

>[AZURE.NOTE]Si realiza una migración manual, *no puede* conservar su dirección URL **service.azure mobile.net** existente. Si tiene clientes móviles que se conectan a esta dirección URL, debe usar la opción de migración automática o mantener el servicio móvil en ejecución hasta que todos los clientes móviles se hayan actualizado a la nueva dirección URL.


### <a name="app-settings"></a>Configuración de la aplicación
Servicios móviles requiere varias configuraciones de aplicación para que esté disponible en el entorno. Dichas configuraciones se describen en esta sección.

Para establecer la configuración de la aplicación en su aplicación web, primero inicie sesión en el [Portal de administración de vista previa de Azure]. Vaya a la aplicación web, móvil o de API que quiera usar y seleccione "Configuración" y luego "Configuración de la aplicación". Desplácese hacia abajo hasta la sección "Configuración de la aplicación". Aquí puede establecer los pares de clave y valor necesarios.
 
**MS\_MobileServiceName** debe establecerse en el nombre de la aplicación. Por ejemplo, si va a trabajar en una aplicación con la dirección URL contoso.azurewebsites.net, "contoso" sería entonces el valor correcto.
 
**MS\_MobileServiceDomainSuffix** se debe establecer en el nombre de la aplicación web. Por ejemplo, si va a trabajar en una aplicación con la dirección URL contoso.azurewebsites.net, "azurewebsites.net" sería entonces el valor correcto.
 
**MS\_ApplicationKey** puede establecerse en cualquier valor, pero debe ser el mismo valor usado por el SDK de cliente. Se recomienda un GUID.
 
**MS\_MasterKey** puede establecerse en cualquier valor, pero debe ser el mismo valor usado por cualquier cliente o API de administración. Se recomienda un GUID.
 
Al migrar una aplicación existente de Servicios móviles, la clave maestra y la clave de aplicación se pueden obtener ambas de la pestaña "Configurar" del [Portal de administración de Azure]. Seleccione la acción "Administrar claves" en la parte inferior y copie las claves.


### <a name="client-sdk"></a>Modificación del SDK de cliente

En el proyecto de la aplicación cliente, modifique el constructor del objeto de cliente de Servicios móviles para aceptar la nueva dirección URL de aplicación (por ejemplo, `https://contoso.azurewebsites.net`) y la clave de aplicación que se configuró anteriormente. La versión del SDK de cliente debe ser una versión de **Servicios móviles** y **no** debe actualizarse. En clientes iOS y Android, use las versiones 2.x y en Windows/Xamarin, use 1.3.2. Los clientes JavaScript deben estar usando 1.2.7.

### <a name="data"></a>Habilitación de las características de datos

Para trabajar con las clases de Entity Framework predeterminadas en Servicios móviles, son necesarias dos configuraciones de aplicación adicionales.
 
Las cadenas de conexión se almacenan en la sección "Cadenas de conexión" de la hoja Configuración de la aplicación, justo debajo de la sección **Configuración de la aplicación**. La cadena de conexión de la base de datos debe establecerse en la clave **MS\_TableConnectionString**. Para migrar una aplicación existente de Servicios móviles, vaya a la sección "Cadenas de conexión" de la pestaña Configurar del portal de Servicios móviles. Haga clic en "Mostrar cadenas de conexión" y copie el valor.
 
De forma predeterminada, el esquema que se usará es **MS\_MobileServiceName**, pero se puede sobrescribir con la configuración **MS\_TableSchema**. De nuevo en **Configuración de la aplicación**, establezca **MS\_TableSchema** como el nombre del esquema que se usará. Si va a migrar una aplicación existente de Servicios móviles, entonces ya se ha creado un esquema con Entity Framework y este es el nombre del servicio móvil, no la instancia del Servicio de aplicaciones que va a hospedar el código ahora.

### <a name="push"></a>Habilitación de las características de inserción

Para que el proceso de inserción funcione, la aplicación web debe conocer además información sobre el centro de notificaciones.
 
En "Cadenas de conexión", establezca **MS\_NotificationHubConnectionString** con la cadena de conexión DefaultFullSharedAccessSignature del Centro de notificaciones. Para migrar una aplicación existente de Servicios móviles, vaya a la sección "Cadenas de conexión" de la pestaña Configurar del portal de Servicios móviles. Haga clic en "Mostrar cadenas de conexión" y copie el valor.

La configuración de aplicación **MS\_NotificationHubName** se debe establecer en el nombre del centro. Al migrar una aplicación existente de Servicios móviles, puede obtener este valor de la pestaña Insertar del portal de Servicios móviles. Los demás campos de esta pestaña están vinculados al propio centro y no es necesario copiarlos en ninguna parte.
 
### <a name="auth"></a>Habilitación de las características de autenticación

Las características de identidad también tienen requisitos de configuración de aplicación para los distintos proveedores. Si migra desde una aplicación existente de Servicios móviles, cada uno de los campos de la pestaña Identidad del portal de Servicios móviles tiene una configuración de aplicación correspondiente.
 
Cuenta Microsoft

* **MS\_MicrosoftClientID**

* **MS\_MicrosoftClientSecret**

* **MS\_MicrosoftPackageSID**
 
Facebook

* **MS\_FacebookAppID**

* **MS\_FacebookAppSecret**
 
Twitter

* **MS\_TwitterConsumerKey**

* **MS\_TwitterConsumerSecret**
 
Google

* **MS\_GoogleClientID**

* **MS\_GoogleClientSecret**
 
AAD

* **MS\_AadClientID**

* **MS\_AadTenants** - Nota: **MS\_AadTenants** se almacena como una lista de dominios de inquilino separados por coma (los campos "Inquilinos permitidos" del portal de Servicios móviles).

### <a name="publish"></a>Publicación del proyecto de Servicios móviles

1. En el Portal de vista previa, desplácese a la aplicación y seleccione "Obtener perfil de publicación" en la barra de comandos. Guarde el perfil descargado en el equipo local.
2. En Visual Studio, haga clic con el botón derecho en el servidor de Servicios móviles y seleccione "Publicar". En la pestaña Perfil, elija "Importar" y busque el perfil descargado.
3. Haga clic en Publicar para implementar el código en el Servicio de aplicaciones.

Los registros se controlan mediante las características de registro estándar del Servicio de aplicaciones. Para obtener más información sobre el registro, consulte [Habilitación del registro de diagnóstico en Servicio de aplicaciones de Azure].



<!-- URLs. -->

[Portal de administración de vista previa de Azure]: https://portal.azure.com/
[Portal de administración de Azure]: https://manage.windowsazure.com/
[¿Qué son las aplicaciones móviles?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /es-ES/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /es-ES/documentation/services/scheduler/
[Web Job]: ../app-service-web/websites-webjobs-resources.md
[Send cross-platform push notifications]: app-service-mobile-xamarin-ios-push-notifications-to-user.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md


[Habilitación del registro de diagnóstico en Servicio de aplicaciones de Azure]: web-sites-enable-diagnostic-log.md
[precios de Servicio de aplicaciones]: https://azure.microsoft.com/es-ES/pricing/details/app-service/
[entorno del Servicio de aplicaciones]: app-service-app-service-environment-intro.md
[Servicios móviles frente a Servicio de aplicaciones]: app-service-mobile-value-prop-migration-from-mobile-services-preview.md
[migrar un servicio móvil a una aplicación móvil en Servicio de aplicaciones]: app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview.md

<!---HONumber=Nov15_HO3-->