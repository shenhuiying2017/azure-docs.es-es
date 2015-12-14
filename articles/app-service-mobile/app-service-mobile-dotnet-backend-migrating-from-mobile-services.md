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
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/25/2015"
	ms.author="mahender"/>

# Migración del Servicio móvil de Azure existente a Servicio de aplicaciones

En este tema se muestra cómo migrar una aplicación existente de Servicios móviles de Azure a una nueva Aplicación móvil del Servicio de aplicaciones. Cualquier servicio móvil existente puede migrarse fácilmente a un nuevo back-end de aplicación móvil. Durante la migración, el servicio móvil existente, seguirá funcionando.

Cuando un servicio móvil se migra a Servicio de aplicaciones de Azure, accede a todas las características de Servicio de aplicaciones y se factura conforme a los [precios del Servicio de aplicaciones], no según los precios de Servicios móviles.

Cabe mencionar también que los trabajos programados se moverán de un plan del Programador de Azure administrado por Microsoft a un plan del Programador de Azure en su propia suscripción y bajo su control. De forma similar a las ventajas de migrar al Servicio de aplicaciones, esto le permite aprovechar la potencia completa del Programador de Azure.

### <a name="why-host"></a>¿Por qué hospedar en Servicio de aplicaciones?

El Servicio de aplicaciones proporciona un entorno de hospedaje repleto de características para su aplicación. Al hospedar en el Servicio de aplicaciones, el servicio obtiene acceso a ranuras de ensayo, dominios personalizados, soporte técnico del Administrador de tráfico y otras muchas características. Si bien puede actualizar un servicio móvil a un back-end de aplicación móvil después de migrar a Servicio de aplicaciones, puede que algunos clientes quieran sacar partido de estas características inmediatamente, sin realizar aún la actualización del SDK.

Para más información sobre las ventajas del Servicio de aplicaciones, vea el tema [Servicios móviles frente a Servicio de aplicaciones].

## Migración frente a actualización

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

- Para los proyectos de servidor basados en Node.js, el nuevo [SDK para Node.js de Aplicaciones móviles](https://github.com/Azure/azure-mobile-apps-node) proporciona varias nuevas características. Por ejemplo, ahora puede desarrollar y depurar localmente, usar cualquier versión de Node.js posterior a la 0.10 y personalizar con cualquier middleware de Express.js.

- Para proyectos de servidor basados en .NET, los nuevos [paquetes NuGet del SDK de Aplicaciones móviles](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) tienen más flexibilidad en las dependencias de NuGet, son compatibles con las nuevas características de autenticación del Servicio de aplicaciones y se componen con cualquier proyecto de ASP.NET, incluido MVC. Para más información sobre la actualización, vea [Actualización del Servicio móvil de .NET existente a Servicio de aplicaciones](app-service-mobile-net-upgrading-from-mobile-services.md).


## <a name="understand"></a>Descripción de las aplicaciones móviles del Servicio de aplicaciones

Aplicaciones móviles del Servicio de aplicaciones supone una nueva forma de crear aplicaciones móviles con Azure. Puede obtener más información sobre Aplicaciones móviles en el tema [¿Qué son las aplicaciones móviles?]

Al migrar a Aplicaciones móviles, se conserva todas las funciones de aplicaciones existentes, incluida la lógica de negocios. Además, hay nuevas características disponibles para la aplicación. En el modelo de aplicaciones móviles, el código se ejecuta realmente en una aplicación web del Servicio de aplicaciones (la nueva versión de Sitios web de Azure). El usuario tiene control total sobre la aplicación web y su funcionamiento. Además, ahora pueden usarse las características de aplicaciones web que antes no estaban disponibles para los clientes de Servicios móviles, como el Administrador de tráfico y Espacios de implementación.

La limitación principal de la actualización es que los trabajos programados de Servicios móviles no están integrados y, una de dos, se debe configurar el Programador de Azure para llamar a las API personalizadas o se deben habilitar las características de trabajos web.

## Migración del Servicio móvil con el asistente de Migrar al Servicio de aplicaciones

La forma más sencilla, y recomendable, de migrar el servicio móvil a Servicio de aplicaciones consiste en usar el asistente para Migrar al Servicio de aplicaciones, que está disponible en el Portal de Azure clásico. Navegue hasta el [Portal de Azure clásico], busque los servicios móviles y seleccione un servicio móvil que quiere migrar; en la parte inferior de la pantalla, verá un botón **Migrar al Servicio de aplicaciones** que le guiará a través del proceso de migración del servicio móvil.

### <a name="what-gets-migrated"></a>¿Qué es lo que se migra?

Con el Asistente para la migración, las granjas de servidores que hospeda el servicio móvil pasan de estar administradas por Servicios móviles a estarlo por el Servicio de aplicaciones. Este asistente también mueve los planes del Programador que Servicios móviles administraba de una suscripción administrada por Microsoft a su suscripción. Cuando las granjas de servidores se transfieren al control de la administración del Servicio de aplicaciones, se mueven a la vez todos los servicios móviles asociados a la granja de servidores.

Servicios móviles organiza los servicios móviles en granjas de servidores en función de la región y la SKU (plan gratuito, básico y estándar). Todos los servicios gratuitos de una región están en la misma granja de servidores y se migran juntos; algunos servicios básicos de una región se hospedan juntos en la misma granja, pero si tiene muchos servicios básicos, estarán en planes independientes; cada servicio estándar está en su propia granja de servidores. Si desea migrar un solo servicio o evitar que un servicio tenga que moverse con los demás sitios, puede actualizarlo a un plan básico y estará en su propio plan independiente.

Si estuviera usando trabajos programados, también tendrá que agregar planes del Programador de Azure a la suscripción. Se permite un número limitado de trabajos programados gratuitos por suscripción, por lo que puede haber algunos costos asociados que conviene revisar después de la suscripción.

###  Uso del Asistente para migración

1. Navegue hasta el [Portal de Azure clásico] y haga clic en Servicios móviles. 

2. Seleccione el servicio móvil que va a migrar y haga clic en "Migrar al Servicio de aplicaciones" en la barra de menús de la parte inferior. Se presentará un cuadro emergente con la lista de servicios móviles que se van a migrar. Vea la [sección anterior](#what-gets-migrated) para más información sobre los servicios que se muestran y por qué.

3. Escriba el nombre del servicio móvil para confirmar la migración y haga clic en la marca de verificación para continuar. Cuando se incluyan varios servicios móviles en la migración, todavía tendrá que escribir el nombre del servicio original que se seleccionó.

4. Después iniciarse la migración, puede ver el estado actual en la lista de Servicios móviles del [Portal de Azure clásico]. El estado aparecerá como "migrando" para todos los servicios que actualmente se migran. Cuando todas las migraciones aparezcan como completadas, podrá verlas en el [Portal de Azure] en Aplicaciones móviles. Durante la migración y después, los servicios móviles seguirán funcionando y la dirección URL no cambiará.

## Migración manual para el back-end .NET de Servicios móviles

>[AZURE.NOTE]Recuerde que la forma recomendada de migrar un servicio móvil es a través del asistente de Migrar al Servicio de aplicaciones, que se describe en la sección anterior. Solo debe usarse la migración manual en situaciones en las que no se puede usar el asistente.

En esta sección se muestra cómo hospedar un proyecto de Servicios móviles de .NET en el Servicio de aplicaciones de Azure. Con una aplicación hospedada de este modo puede usar todos los tutoriales del runtime de .NET de *Servicios móviles*, si bien es necesario sustituir las direcciones URL que usen el dominio azure-mobile.net por el dominio de la instancia del Servicio de aplicaciones.

Un proyecto de Servicios móviles también se puede hospedar en un [entorno del Servicio de aplicaciones]. Todo el contenido de este tema sigue siendo aplicable, pero el sitio tendrá el formato `contoso.contosoase.p.azurewebsites.net` en lugar de `contoso.azurewebsites.net`.

>[AZURE.NOTE]Si realiza una migración manual, *no puede* conservar su dirección URL **service.azure mobile.net** existente. Si tiene clientes móviles que se conectan a esta dirección URL, debe usar la opción de migración automática o mantener el servicio móvil en ejecución hasta que todos los clientes móviles se hayan actualizado a la nueva dirección URL.


### <a name="app-settings">Configuración de la aplicación</a>
Servicios móviles requiere que varias opciones de configuración de la aplicación estén disponibles en el entorno, lo que se describe en esta sección.

Para establecer las opciones de configuración de la aplicación en el back-end de aplicación móvil, inicie sesión primero en el [Portal de Azure]. Navegue a la aplicación del Servicio de aplicaciones que quiera usar como back-end de aplicación móvil, haga clic en **Configuración** > **Configuración de la aplicación** y desplácese hacia abajo hasta **Configuración de la aplicación**, donde puede establecer los siguientes pares de clave y valor necesarios:

+ **MS\_MobileServiceName** debe establecerse en el nombre de la aplicación. Por ejemplo, cuando la dirección URL de su back-end es `contoso.azurewebsites.net`, el valor correcto es *contoso*.

+ **MS\_MobileServiceDomainSuffix** debe establecerse en el nombre de la aplicación web. Por ejemplo, cuando la dirección URL de su back-end es `contoso.azurewebsites.net`, el valor correcto es *azurewebsites.net*.

+ **MS\_ApplicationKey** puede establecerse en cualquier valor, pero debe ser el mismo valor que use el SDK de cliente. Se recomienda un GUID.

+ **MS\_MasterKey** puede establecerse en cualquier valor, pero debe ser el mismo valor que usen los clientes o las API de administración. Se recomienda un GUID.

Al migrar un servicio móvil, la clave principal y la clave de aplicación se pueden obtener ambas en la pestaña [Configurar] de la sección Servicios móviles del **Portal de Azure clásico**. Haga clic en **Administrar claves** en la parte inferior y copie las claves.


### <a name="client-sdk"></a>Modificación del SDK de cliente

En el proyecto de la aplicación cliente, modifique el constructor del objeto cliente de Servicios móviles para aceptar la nueva dirección URL de aplicación (por ejemplo, `https://contoso.azurewebsites.net`) y la clave de aplicación que se configuró anteriormente. La versión del SDK de cliente debe ser una versión de **Servicios móviles** y **no** debe actualizarse. En clientes iOS y Android, use las versiones 2.x y en Windows/Xamarin, use 1.3.2. Los clientes JavaScript deben estar usando 1.2.7.

### <a name="data"></a>Habilitación de las características de datos

Para trabajar con las clases de Entity Framework predeterminadas en Servicios móviles, se requieren dos configuraciones de aplicación adicionales.

Las cadenas de conexión se almacenan en la sección **Cadenas de conexión** de la hoja Configuración de la aplicación, justo debajo de la sección **Configuración de aplicación**. La cadena de conexión de la base de datos debe establecerse en la clave **MS\_TableConnectionString**. Al migrar un servicio móvil existente a Servicio de aplicaciones, navegue a la sección **Cadenas de conexión** de la pestaña **Configurar** de Servicios móviles en el [Portal de Azure clásico](https://manage.windowsazure.com/). Haga clic en **Mostrar cadenas de conexión** y copie el valor.

De forma predeterminada, el esquema que se usará es **MS\_MobileServiceName**, pero se puede sobrescribir con la configuración **MS\_TableSchema**. De nuevo en **Configuración de la aplicación**, establezca **MS\_TableSchema** como nombre del esquema que se usará. Si va a migrar una aplicación existente de Servicios móviles, entonces ya se creó un esquema con Entity Framework. Se trata del nombre del servicio móvil, no de la instancia del Servicio de aplicaciones que va a hospedar el código ahora.

### <a name="push"></a>Habilitación de las características de inserción

Para que el proceso de inserción funcione, la aplicación web debe tener también información sobre el Centro de notificaciones.

En **Cadenas de conexión**, establezca **MS\_NotificationHubConnectionString** con la cadena de conexión DefaultFullSharedAccessSignature del Centro de notificaciones. Para migrar un servicio móvil existente, navegue a la sección **Cadenas de conexión** de la pestaña **Configurar** de Servicios móviles en el [Portal de Azure clásico](https://manage.windowsazure.com/). Haga clic en **Mostrar cadenas de conexión** y copie el valor.

La configuración de aplicación **MS\_NotificationHubName** se debe establecer en el nombre del centro. Al migrar un servicio móvil existente, puede obtener este valor de la pestaña **Insertar** del [Portal de Azure clásico](https://manage.windowsazure.com/). Los demás campos de esta pestaña están vinculados al propio centro y no es necesario copiarlos en ninguna parte.

### <a name="auth"></a>Habilitación de las características de autenticación

Las características de identidad también tienen requisitos de configuración de aplicación para los distintos proveedores. Si migra desde un servicio móvil existente, cada uno de los campos de la pestaña **Identidad** del Portal de Azure clásico tiene una configuración de aplicación correspondiente.

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

* **MS\_AadTenants** - Nota: **MS\_AadTenants** se almacena como una lista de dominios de inquilino separados por coma (los campos **Inquilinos permitidos** del Portal de Azure clásico).

### <a name="publish"></a>Publicación del proyecto de Servicios móviles

1. En el [Portal de Azure], navegue a la aplicación, haga clic en **Obtener perfil de publicación** en la barra de comandos y guarde el perfil descargado en el equipo local.
2. En Visual Studio, haga clic con el botón derecho en el proyecto de servidor de Servicios móviles y haga clic en **Publicar**. 
3. En la pestaña Perfil, elija **Importar** y busque el perfil descargado.
3. Haga clic en **Publicar** para implementar el código en el Servicio de aplicaciones.

Los registros se controlan mediante las características de registro estándar del Servicio de aplicaciones. Para más información sobre el registro, vea [Habilitación del registro de diagnóstico en el Servicio de aplicaciones de Azure].



<!-- URLs. -->

[Portal de Azure]: https://portal.azure.com/
[Configurar]: https://manage.windowsazure.com/
[Portal de Azure clásico]: https://manage.windowsazure.com/
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


[Habilitación del registro de diagnóstico en el Servicio de aplicaciones de Azure]: web-sites-enable-diagnostic-log.md
[precios del Servicio de aplicaciones]: https://azure.microsoft.com/es-ES/pricing/details/app-service/
[entorno del Servicio de aplicaciones]: app-service-app-service-environment-intro.md
[Servicios móviles frente a Servicio de aplicaciones]: app-service-mobile-value-prop-migration-from-mobile-services-preview.md
[migrate a mobile service to a mobile app on App Service]: app-service-mobile-dotnet-backend-migrating-from-mobile-services.md

<!---HONumber=AcomDC_1203_2015-->