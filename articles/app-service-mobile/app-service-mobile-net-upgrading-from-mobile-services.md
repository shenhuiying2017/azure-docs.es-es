---
title: "Actualización de Mobile Services a Azure App Service"
description: "Aprenda a actualizar fácilmente la aplicación de Mobile Services a una aplicación móvil de Mobile App."
services: app-service\mobile
documentationcenter: 
author: conceptdev
manager: crdun
editor: 
ms.assetid: 9c0ac353-afb6-462b-ab94-d91b8247322f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: f07b1d6037ff8ca16b673e6a1a235769355a9993
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="upgrade-your-existing-net-azure-mobile-service-to-app-service"></a>Actualización del Servicio móvil de .NET existente a App Service
Aplicaciones móviles de App Service es una nueva forma de crear aplicaciones móviles con Microsoft Azure. Para más información, vea [¿Qué es Mobile Apps?].

En este tema se describe cómo actualizar una aplicación back-end de .NET existente en Azure Mobile Services a una nueva instancia de App Service Mobile Apps. Cuando realice esta migración, la aplicación de Mobile Services existente puede continuar funcionando.   Si necesita actualizar una aplicación back-end de Node.js, consulte [Actualización de Mobile Services de Node.js](app-service-mobile-node-backend-upgrading-from-mobile-services.md).

Cuando un back-end móvil se actualiza a Azure App Service, accede a todas las características de App Service y se factura conforme a los [precios de App Service], no según los precios de Mobile Services.

## <a name="migrate-vs-upgrade"></a>Migración frente a actualización
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> Se recomienda [realizar una migración](app-service-mobile-migrating-from-mobile-services.md) antes de pasar por una actualización. De este modo, puede colocar las dos versiones de la aplicación en el mismo plan de App Service y no incurrir en ningún coste adicional.
>
>

### <a name="improvements-in-mobile-apps-net-server-sdk"></a>Mejoras en el SDK de servidor .NET de Mobile Apps
La actualización al nuevo [SDK de Mobile Apps](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) ofrece las siguientes ventajas:

* Más flexibilidad en las dependencias de NuGet. El entorno de hospedaje ya no proporciona sus propias versiones de paquetes NuGet, por lo que puede usar versiones compatibles alternativas. Sin embargo, si hay nuevas actualizaciones de seguridad o correcciones de errores importantes para el SDK de servidor móvil o dependencias, debe actualizar el servicio manualmente.
* Más flexibilidad en el SDK móvil. Puede controlar explícitamente qué características y rutas se configuran, como la autenticación, las API de la tabla y el punto de conexión de registro de inserción. Para más información, vea [Trabajar con el SDK del servidor back-end de .NET para Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).
* Compatibilidad con otras rutas y tipos de proyecto ASP.NET. Ahora puede hospedar controladores Web API y MVC en el mismo proyecto que el proyecto de back-end móvil.
* Compatibilidad con nuevas características de autenticación de App Service, que le permiten usar una configuración de autenticación común a través de aplicaciones web y móviles.

## <a name="overview"></a>Información general básica de actualización
En muchos casos, la actualización será tan sencilla como cambiar al nuevo SDK de servidor de Mobile Apps y volver a publicar el código en una nueva instancia de Mobile Apps. Sin embargo, hay algunos escenarios que requieren una configuración adicional, como los escenarios de autenticación avanzada y el trabajo con trabajos programados. Cada uno de ellos se trata en las secciones siguientes.

> [!TIP]
> Se recomienda leer y comprender totalmente el resto de este tema antes de iniciar una actualización. Tome nota de cualquier característica usada que se mencione a continuación.
>
>

Los SDK de cliente de Mobile Services **no** son compatibles con el nuevo SDK de servidor de Mobile Apps. A fin de ofrecer continuidad del servicio para la aplicación, no debe publicar cambios en un sitio que actualmente presta servicio a clientes publicados. En su lugar, debe crear una aplicación móvil que actúe como duplicado. Puede colocar esta aplicación en el mismo plan de App Service para evitar incurrir en costos financieros adicionales.

Entonces tendrá dos versiones de la aplicación,: una que permanece igual y presta servicio a aplicaciones publicadas en su estado natural, y otra que después se puede actualizar y destinar a una nueva versión del cliente. Puede mover y probar el código a su ritmo, pero debe asegurarse de que las correcciones de errores se apliquen a ambas. Cuando crea que la cantidad que elija de aplicaciones cliente en estado natural se han actualizado a la versión más reciente, puede eliminar si quiere la aplicación migrada original.

El esquema completo del proceso de actualización es el siguiente:

1. Creación de una aplicación móvil
2. Actualización del proyecto para usar los nuevos SDK de servidor
3. Publique una versión nueva de la aplicación cliente
4. (Opcional) Eliminación de la instancia original migrada

## <a name="mobile-app-version"></a>Creación de una segunda instancia de aplicación
El primer paso en la actualización es crear el recurso de aplicación móvil que hospedará la nueva versión de la aplicación. Si ya ha migrado un servicio móvil existente, conviene crear esta versión en el mismo plan de hospedaje. Abra el [Portal de Azure] y vaya a la aplicación migrada. Tome nota del plan de App Service en el que se ejecuta.

A continuación, cree la segunda instancia de aplicación siguiendo las [instrucciones de creación de back-end de .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app). Cuando se le pida que seleccione el plan de App Service o el "plan de hospedaje", elija el plan de la aplicación migrada.

Probablemente deseará usar la misma base de datos y la base de datos central de notificaciones como hizo en Mobile Services. Para copiar estos valores, abra [Portal de Azure] y navegue hasta la aplicación original, haga clic en **Configuración** > **Configuración de aplicación**. En **Cadenas de conexión**, copie `MS_NotificationHubConnectionString` y `MS_TableConnectionString`. Navegue al nuevo sitio de actualización y péguelos en él, sobrescribiendo los valores existentes. Repita este proceso para cualquier otra opción de configuración de la aplicación que la aplicación requiera. Si no usa un servicio migrado, puede leer las cadenas de conexión y la configuración de la aplicación en la pestaña **Configurar** de la sección Mobile Services del [Portal de Azure clásico].

Haga una copia del proyecto de ASP.NET para la aplicación y publíquela en el nuevo sitio web. Mediante una copia de la aplicación cliente actualizada con la nueva dirección URL, compruebe que todo funciona según lo esperado.

## <a name="updating-the-server-project"></a>Actualización del proyecto de servidor
Mobile Apps ofrece un nuevo [SDK de servidor de aplicaciones móviles] que proporciona en gran medida la misma funcionalidad que el sistema de tiempo de ejecución de Mobile Services. En primer lugar, debe quitar todas las referencias a los paquetes de Mobile Services. En el Administrador de paquetes NuGet, busque `WindowsAzure.MobileServices.Backend`. En la mayoría de aplicaciones se verán varios paquetes aquí, incluidos `WindowsAzure.MobileServices.Backend.Tables` y `WindowsAzure.MobileServices.Backend.Entity`. En tal caso, empiece por el paquete más bajo en el árbol de dependencias, como `Entity`, y elimínelo. Cuando se le pregunte, no quite todos los paquetes dependientes. Repita este proceso hasta que haya quitado el propio `WindowsAzure.MobileServices.Backend` .

En este punto, tendrá un proyecto que ya no hace referencia a los SDK de Mobile Services.

A continuación, agregará referencias al SDK de Mobile Apps. Para esta actualización, la mayoría de los desarrolladores prefieren descargar e instalar el paquete `Microsoft.Azure.Mobile.Server.Quickstart` ya que, de esta manera, se extrae el conjunto necesario completo.

Habrá algunos errores del compilador resultantes de las diferencias entre los SDK, pero son fáciles de resolver y se tratan en el resto de esta sección.

### <a name="base-configuration"></a>Configuración base
Luego, en WebApiConfig.cs, puede reemplazar.

        // Use this class to set configuration options for your mobile service
        ConfigOptions options = new ConfigOptions();

        // Use this class to set WebAPI configuration options
        HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

por

        HttpConfiguration config = new HttpConfiguration();
        new MobileAppConfiguration()
            .UseDefaultConfiguration()
        .ApplyTo(config);

> [!NOTE]
> Si desea más información sobre el nuevo SDK de servidor .NET y sobre cómo agregar o quitar características de la aplicación, vea el tema [Uso del SDK de servidor .NET] .
>
>

Si la aplicación hace uso de las características de autenticación, también debe registrar un middleware de OWIN. En este caso, debe mover el código de configuración anterior a una nueva clase de inicio OWIN.

1. Agregue el paquete NuGet `Microsoft.Owin.Host.SystemWeb` si no está ya incluido en el proyecto.
2. En Visual Studio, haga clic con el botón derecho en el proyecto y seleccione **Agregar** -> **Nuevo elemento**. Seleccione **Web** -> **General** -> **Clase de inicio OWIN**.
3. Mueva el código anterior de MobileAppConfiguration del método `WebApiConfig.Register()` al `Configuration()` de la nueva clase de inicio.

Asegúrese de que el método `Configuration()` termina con:

        app.UseWebApi(config)
        app.UseAppServiceAuthentication(config);

Hay cambios adicionales relacionados con la autenticación que se tratan a continuación en la sección de autenticación completa.

### <a name="working-with-data"></a>Trabajo con datos
En Mobile Services, el nombre de la aplicación móvil actúa como nombre del esquema predeterminado en la configuración de Entity Framework.

Para asegurarse de que tiene el mismo esquema al que se hace referencia que antes, use lo siguiente para establecer el esquema en el DbContext de la aplicación:

        string schema = System.Configuration.ConfigurationManager.AppSettings.Get("MS_MobileServiceName");

Si hace lo anterior, asegúrese de que tiene MS_MobileServiceName establecido. También puede especificar otro nombre de esquema si la aplicación lo personalizó anteriormente.

### <a name="system-properties"></a>Propiedades del sistema
#### <a name="naming"></a>Nomenclatura
En el SDK de servidor de Azure Mobile Services, las propiedades del sistema siempre contienen un doble prefijo subrayado (`__`) para las propiedades:

* __createdAt
* __updatedAt
* __deleted
* __version

Los SDK de cliente de Mobile Services tienen una lógica especial para analizar las propiedades del sistema en este formato.

En Azure Mobile Apps, las propiedades del sistema ya no tienen un formato especial y tienen los nombres siguientes:

* createdAt
* updatedAt
* deleted
* version

Los SDK de cliente de Mobile Apps usan los nuevos nombres de propiedades del sistema, por lo que no es necesario realizar cambios al código de cliente. Pero, si realiza llamadas REST directamente al servicio, debe cambiar las consultas en consecuencia.

#### <a name="local-store"></a>Almacén local.
Los cambios en los nombres de propiedades del sistema se traducen en que una base de datos local de sincronización sin conexión para Mobile Services no es compatible con Mobile Apps. Si es posible, debe evitar la actualización de aplicaciones cliente de Mobile Services a Mobile Apps hasta que los cambios pendientes se hayan enviado al servidor. A continuación, la aplicación actualizada debe usar un nuevo nombre de archivo de base de datos.

Si una aplicación cliente se actualiza desde Mobile Services a Mobile Apps mientras hay cambios sin conexión pendientes en la cola de la operación, el sistema de base de datos debe actualizarse para reflejar los nuevos nombres de columna. En iOS, esto puede lograrse mediante migraciones ligeras para cambiar los nombres de columna. En Android y en el cliente administrado de .NET, debe escribir SQL personalizado para cambiar el nombre de las columnas de las tablas de objeto de datos.

En iOS, debe cambiar el esquema de datos principal de las entidades de datos para que coincida con el siguiente. Tenga en cuenta que las propiedades `createdAt`, `updatedAt` y `version` ya no tienen un prefijo `ms_`:

| Atributo | type | Nota: |
| --- | --- | --- |
| id |Cadena, marcado obligatorio |primary key in remote store |
| createdAt |Date |(opcional) se asigna a la propiedad del sistema createdAt |
| updatedAt |Date |(opcional) se asigna a la propiedad del sistema updatedAt |
| version |string |(opcional) se usa para detectar conflictos, se asigna a la versión |

#### <a name="querying-system-properties"></a>Consulta de propiedades del sistema
En Azure Mobile Services, las propiedades del sistema no se envían de forma predeterminada, sino únicamente cuando se solicitan mediante la cadena de consulta `__systemProperties`. En cambio, en el sistema de Azure Mobile Apps están **siempre activadas** dado que forman parte del modelo de objetos del SDK de servidor.

Este cambio afecta principalmente a las implementaciones personalizadas de los administradores de dominio, como las extensiones de `MappedEntityDomainManager`. En Mobile Services, si un cliente no solicita nunca ninguna propiedad del sistema, es posible utilizar una `MappedEntityDomainManager` que realmente no asigna todas las propiedades. Pero, en Azure Mobile Apps, estas propiedades sin asignar producirán un error en las consultas GET.

La manera más fácil de resolver el problema consiste en modificar los DTO para que se hereden de `ITableData` y no de `EntityData`. Seguidamente, agregue el atributo `[NotMapped]` a los campos que se deben omitir.

Por ejemplo, el código siguiente define `TodoItem` sin propiedades del sistema:

    using System.ComponentModel.DataAnnotations.Schema;

    public class TodoItem : ITableData
    {
        public string Text { get; set; }

        public bool Complete { get; set; }

        public string Id { get; set; }

        [NotMapped]
        public DateTimeOffset? CreatedAt { get; set; }

        [NotMapped]
        public DateTimeOffset? UpdatedAt { get; set; }

        [NotMapped]
        public bool Deleted { get; set; }

        [NotMapped]
        public byte[] Version { get; set; }
    }

Nota: En caso de errores en `NotMapped`, agregue una referencia al ensamblado `System.ComponentModel.DataAnnotations`.

### <a name="cors"></a>CORS
Mobile Services incluye cierta compatibilidad con CORS ajustando la solución ASP.NET CORS. Se ha quitado esta capa de ajuste para dar al desarrollador más control, a fin de que pueda aprovechar directamente la [compatibilidad con ASP.NET CORS](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

El principal motivo de preocupación si se usa CORS es que deben permitirse los encabezados `eTag` y `Location` para que los SDK de cliente funcionen correctamente.

### <a name="push-notifications"></a>Notificaciones de inserción
Para la inserción, el elemento principal que puede faltar desde el SDK de servidor es la clase PushRegistrationHandler. Los registros se controlan de forma ligeramente diferente en Mobile Apps y los registros sin etiqueta están habilitados de forma predeterminada. Se puede lograr la administración de etiquetas mediante API personalizadas. Vea la instrucciones de [registro de etiquetas](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags) para más información.

### <a name="scheduled-jobs"></a>Trabajos programados
Los trabajos programados no están integrados en Mobile Apps, por lo que tendrá que actualizar individualmente los trabajos existentes que tenga en el back-end de .NET. Una opción es crear un [trabajo web] programado en el sitio del código de aplicación móvil. También puede configurar un controlador que contiene el código de trabajo y configurar el [Azure Scheduler] para acertar ese extremo en la programación prevista.

### <a name="miscellaneous-changes"></a>Cambios varios
Todos los ApiControllers que use un cliente móvil deben tener ahora el atributo `[MobileAppController]` . Este ya no se incluye de forma predeterminada para que otros ApiControllers no se vean afectados por los formateadores de móviles.

El objeto `ApiServices` ya no forma parte del SDK. Para obtener acceso a la configuración de la aplicación móvil, puede usar lo siguiente:

    MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

De forma similar, el registro ahora se realiza mediante la escritura de seguimiento estándar de ASP.NET:

    ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
    traceWriter.Info("Hello, World");  

## <a name="authentication"></a>Consideraciones de autenticación
Ahora, los componentes de autenticación de Mobile Services se han movido a la característica Autenticación/autorización de App Service. Para aprender a habilitar esta característica para el sitio, lea el tema [Incorporación de autenticación a una aplicación móvil](app-service-mobile-ios-get-started-users.md) .

Con algunos proveedores, como AAD, Facebook y Google, podrá aprovechar el registro existente de la aplicación de copia. Basta con ir al portal del proveedor de identidades y agregar una nueva dirección URL de redirección al registro. Seguidamente, configure Autenticación/autorización de App Service con el identificador y el secreto del cliente.

### <a name="controller-action-authorization"></a>Autorización de acción de controlador
Todas las instancias del atributo `[AuthorizeLevel(AuthorizationLevel.User)]` ahora deben cambiarse para usar el atributo `[Authorize]` de ASP.NET estándar. Además, los controladores son ahora de tipo anónimo de forma predeterminada, al igual que en otras aplicaciones de ASP.NET.
Si usaba una de las demás opciones de AuthorizeLevel, como administrador o aplicación, tenga en cuenta que estas han desaparecido. En su lugar, puede configurar AuthorizationFilters para secretos compartidos o configurar una entidad de servicio de AAD para habilitar llamadas entre servicios de forma segura.

### <a name="getting-additional-user-information"></a>Información adicional del usuario
Puede obtener información adicional del usuario, como tokens de acceso a través del método `GetAppServiceIdentityAsync()` :

        FacebookCredentials creds = await this.User.GetAppServiceIdentityAsync<FacebookCredentials>();

Además, si la aplicación tiene dependencias de identificadores de usuario, por ejemplo, su almacenamiento en una base de datos, es importante tener en cuenta que los identificadores de usuario de Mobile Services y App Service Mobile Apps son diferentes. Aunque todavía puede obtener el identificador de usuario de Mobile Services. Todas las subclases ProviderCredentials tienen una propiedad UserId. Por lo que si se continúa con el ejemplo anterior:

        string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

Si la aplicación sí adopta las dependencias de los identificadores de usuario, es importante aprovechar el mismo registro con un proveedor de identidades siempre que sea posible. Los identificadores de usuario normalmente tienen como ámbito el registro de la aplicación que se utilizó, por lo que introducir un nuevo registro podría crear problemas con la correspondencia entre usuarios y sus datos.

### <a name="custom-authentication"></a>Autenticación personalizada
Si la aplicación usa una solución de autenticación personalizada, conviene asegurarse de que el sitio actualizado tiene acceso al sistema. Para integrar la solución, siga las nuevas instrucciones de autenticación personalizada del tema [Información general del SDK de .NET] . Tenga en cuenta que los componentes de autenticación personalizada siguen en vista previa.

## <a name="updating-clients"></a>Actualización de clientes
Cuando tenga un back-end de aplicación móvil operativo, podrá trabajar en una nueva versión de la aplicación cliente que lo usa. Mobile Apps incluye también una nueva versión de los SDK de cliente y, de forma similar a la actualización del servidor anterior, tendrá que quitar todas las referencias a los SDK de Mobile Services antes de instalar las versiones de Mobile Apps.

Uno de los principales cambios entre las versiones es que los constructores ya no requieren una clave de aplicación. Ahora basta con pasar la dirección URL de la aplicación móvil. Por ejemplo, en los clientes .NET, el constructor `MobileServiceClient` es ahora:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Encontrará información sobre cómo instalar los nuevos SDK y cómo usar la nueva estructura a través de los vínculos siguientes:

* [iOS versión 3.0.0 o posterior](app-service-mobile-ios-how-to-use-client-library.md)
* [.NET (Windows/Xamarin) versión 2.0.0 o posterior](app-service-mobile-dotnet-how-to-use-client-library.md)

Si la aplicación hace uso de notificaciones de inserción, tome nota de las instrucciones de registro específicas para cada plataforma, ya que también ha habido algunos cambios al respecto.

Cuando tenga la nueva versión de cliente lista, pruébela en el proyecto de servidor actualizado. Después de comprobar que funciona, puede publicar una nueva versión de la aplicación para clientes. Al final, cuando los clientes hayan tenido ocasión de recibir estas actualizaciones, puede eliminar la versión de Mobile Services de su aplicación. Llegados a este punto, ha actualizado completamente a una aplicación móvil de App Service mediante el SDK de servidor de Mobile Apps más reciente.

<!-- URLs. -->

[Portal de Azure]: https://portal.azure.com/
[Portal de Azure clásico]: https://manage.windowsazure.com/
[¿Qué es Mobile Apps?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[SDK de servidor de aplicaciones móviles]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /en-us/documentation/services/scheduler/
[trabajo web]: https://github.com/Azure/azure-webjobs-sdk/wiki
[Uso del SDK de servidor .NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[precios de App Service]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[Información general del SDK de .NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
