---
title: "Actualización de Mobile Services a Azure App Service - Node.js"
description: "Aprenda a actualizar fácilmente la aplicación de Mobile Services a una aplicación móvil de Mobile App."
services: app-service\mobile
documentationcenter: 
author: conceptdev
manager: yochayk
editor: 
ms.assetid: c58f6df0-5aad-40a3-bddc-319c378218e3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 9a641bc667d07d1b674d2864c1f29151d527f46a
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="upgrade-your-existing-nodejs-azure-mobile-service-to-app-service"></a>Actualización del servicio móvil de Azure de Node.js existente a App Service
Aplicaciones móviles de App Service es una nueva forma de crear aplicaciones móviles con Microsoft Azure. Para más información, vea [¿Qué es Mobile Apps?].

En este artículo se describe cómo actualizar una aplicación back-end de Node.js existente en Azure Mobile Services a una nueva instancia de App Service Mobile Apps. Cuando realice esta migración, la aplicación de Mobile Services existente puede continuar funcionando.  Si necesita actualizar una aplicación back-end de Node.js, consulte [Actualización de Mobile Services de .NET](app-service-mobile-net-upgrading-from-mobile-services.md).

Cuando un back-end móvil se actualiza a Azure App Service, accede a todas las características de App Service y se factura conforme a los [precios de App Service], no según los precios de Mobile Services.

## <a name="migrate-vs-upgrade"></a>Migración frente a actualización
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> Se recomienda [realizar una migración](app-service-mobile-migrating-from-mobile-services.md) antes de pasar por una actualización. De este modo, puede colocar las dos versiones de la aplicación en el mismo plan de App Service y no incurrir en ningún coste adicional.
>
>

### <a name="improvements-in-mobile-apps-nodejs-server-sdk"></a>Mejoras en el SDK de servidor para Node.js de Mobile Apps
La actualización al nuevo [SDK de Mobile Apps](https://www.npmjs.com/package/azure-mobile-apps) ofrece muchas mejoras, entre las que se incluyen:

* Basado en la [plataforma Express](http://expressjs.com/en/index.html), el nuevo SDK para Node es ligero y está diseñado para mantenerse al día con nuevas versiones de Node a medida que salen. Puede personalizar el comportamiento de la aplicación con middleware de Express.
* Ofrece mejoras de rendimiento significativas en comparación con el SDK de Mobile Services.
* Ahora puede hospedar un sitio web junto con el back-end móvil; asimismo, es fácil agregar el Azure Mobile SDK a cualquier aplicación v4 existente.
* Creado para desarrollo multiplataforma y local, el SDK de Mobile Apps se puede desarrollar y ejecutar localmente en plataformas Windows, Linux y OSX. Ahora es fácil usar técnicas de desarrollo comunes de Node como, por ejemplo, ejecutar pruebas [Mocha](https://mochajs.org/) antes de la implementación.

## <a name="overview"></a>Información general básica de actualización
Para facilitar la actualización de un back-end de Node.js, Azure App Service ha proporcionado un paquete de compatibilidad.  Tras la actualización, tendrá un nuevo sitio que se puede implementar en un nuevo sitio de App Service.

Los SDK de cliente de Mobile Services **no** son compatibles con el nuevo SDK de servidor de Mobile Apps. A fin de ofrecer continuidad del servicio para la aplicación, no debe publicar cambios en un sitio que actualmente presta servicio a clientes publicados. En su lugar, debe crear una aplicación móvil que actúe como duplicado. Puede colocar esta aplicación en el mismo plan de App Service para evitar incurrir en costos financieros adicionales.

Entonces tendrá dos versiones de la aplicación: una que permanece igual y presta servicio a las aplicaciones publicadas en su estado natural, y otra que después se puede actualizar y destinar a una nueva versión del cliente. Puede mover y probar el código a su ritmo, pero debe asegurarse de que las correcciones de errores se apliquen a ambas. Cuando crea que la cantidad que elija de aplicaciones cliente en estado natural se han actualizado a la versión más reciente, puede eliminar si quiere la aplicación migrada original. No incurre en costos monetarios adicionales, si se hospeda en el mismo plan de App Service que la aplicación móvil.

El esquema completo del proceso de actualización es el siguiente:

1. Descargue el servicio móvil de Azure existente (migrado).
2. Convierta el proyecto en una aplicación móvil de Azure mediante el paquete de compatibilidad.
3. Corrija cualquier diferencia (como la configuración de autenticación).
4. Implemente el proyecto de aplicación móvil de Azure convertido en un nuevo servicio de aplicaciones.
5. Publique una nueva versión de la aplicación cliente que utilice la nueva aplicación móvil.
6. (Opcional) Eliminar la aplicación de servicio móvil migrada original

La eliminación puede tener lugar cuando no vea tráfico alguno en el servicio móvil migrado original.

## <a name="install-npm-package"></a> Instalación de los requisitos previos
Debe instalar [Node] en el equipo local.  También debe instalar el paquete de compatibilidad.  Después de instalar Node, puede ejecutar el comando siguiente desde un nuevo cmd o símbolo del sistema de PowerShell:

```npm i -g azure-mobile-apps-compatibility```

## <a name="obtain-ams-scripts"></a> Obtención de los scripts de Azure Mobile Services
* Inicie sesión en [Azure Portal].
* Mediante **Todos los recursos** o **App Services**, busque el sitio de Mobile Services.
* En el sitio, haga clic en **Herramientas** -> **Kudu** -> **Ir** para abrir el sitio de Kudu.
* Haga clic en **Debug Console** -> **PowerShell** para abrir la consola de depuración.
* Vaya a `site/wwwroot/App_Data/config` haciendo clic por turnos en cada directorio.
* Haga clic en el icono de descarga junto al directorio `scripts` .

Se descargarán los scripts en formato ZIP.  Cree un nuevo directorio en la máquina local y desempaquete el archivo `scripts.ZIP` dentro del directorio.  Se crea un directorio `scripts` .

## <a name="scaffold-app"></a> Realización de scaffold del nuevo back-end de Azure Mobile Apps
Ejecute el siguiente comando desde el directorio que contiene el directorio de scripts:

```scaffold-mobile-app scripts out```

Se crea un back-end de Azure Mobile Apps con scaffold en el directorio `out` .  Aunque no es necesario, conviene comprobar el directorio `out` en un repositorio de código fuente de su elección.

## <a name="deploy-ama-app"></a> Implementación del back-end de Azure Mobile Apps
Durante la implementación, deberá realizar estos pasos:

1. Cree una nueva aplicación móvil en el [Azure Portal].
2. Ejecute el script `createViews.sql` en la base de datos conectada.
3. Vincule la base de datos que está vinculada a su servicio móvil a su nuevo servicio de aplicaciones.
4. Vincule otros recursos (como Notification Hubs) al nuevo App Service.
5. Implemente el código generado en el nuevo sitio.

### <a name="create-a-new-mobile-app"></a>Creación de una aplicación móvil
1. Inicie sesión en [Azure Portal].
2. Haga clic en **+NUEVO** > **Web y móvil** > **Aplicación móvil** y, después, proporcione un nombre para el back-end de la aplicación móvil.
3. En **Grupo de recursos**, seleccione un grupo de recursos existente o cree uno nuevo (con el mismo nombre que su aplicación).

    Puede seleccionar un plan de App Service ya existente o crear uno nuevo. Para más información acerca de los planes de App Service y cómo crear un nuevo plan en un plan de tarifa diferente en la ubicación deseada, consulte [Introducción detallada sobre los planes de Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
4. Para **plan de App Service**, se selecciona el plan predeterminado (en el [nivel estándar](https://azure.microsoft.com/pricing/details/app-service/)). También puede seleccionar otro plan o [crear uno](../app-service/app-service-plan-manage.md#create-an-app-service-plan). La configuración del plan de App Service determina la [ubicación, las características, el costo y los recursos de proceso](https://azure.microsoft.com/pricing/details/app-service/) asociados con la aplicación.

    Después de decidir el plan, haga clic en **Crear**. Esto crea el back-end de aplicación móvil

### <a name="run-createviewssql"></a>Ejecución de CreateViews.SQL
La aplicación con scaffold contiene un archivo llamado `createViews.sql`.  Este script debe ejecutarse con la base de datos de destino.  La cadena de conexión de la base de datos de destino puede obtenerse del servicio móvil migrado en la página **Configuración** en **Cadenas de conexión**.  Se llama `MS_TableConnectionString`.

Puede ejecutar este script desde SQL Server Management Studio o Visual Studio.

### <a name="link-the-database-to-your-app-service"></a>Vinculación de la base de datos a App Service
Vincule la base de datos existente a su servicio de aplicaciones:

* En el [Azure Portal], abra App Service.
* Seleccione **Toda la configuración** -> **Conexiones de datos**.
* Haga clic en **+ Agregar**.
* En la lista desplegable, seleccione **SQL Database**
* En **SQL Database**, seleccione la base de datos existente y haga clic en **Seleccionar**.
* En **Cadena de conexión**, escriba el nombre de usuario y la contraseña de la base de datos y haga clic en **Aceptar**.
* En la página **Add data connections** (Agregar conexiones de datos), haga clic en **Aceptar**.

El nombre de usuario y la contraseña se pueden ver en la cadena de conexión de la base de datos de destino del servicio móvil migrado.

### <a name="set-up-authentication"></a>Configuración de la autenticación
Azure Mobile Apps le permite configurar la autenticación de Azure Active Directory, Facebook, Google, Microsoft y Twitter en el servicio.  La autenticación personalizada deberá desarrollarse por separado.  Para más información, consulte la documentación sobre los [conceptos de autenticación] y el [inicio rápido de autenticación].  

## <a name="updating-clients"></a>Actualización de los clientes móviles
Cuando tenga un back-end de aplicación móvil operativo, podrá trabajar en una nueva versión de la aplicación cliente que lo usa. Mobile Apps incluye también una nueva versión de los SDK de cliente y, de forma similar a la actualización del servidor anterior, tendrá que quitar todas las referencias a los SDK de Mobile Services antes de instalar las versiones de Mobile Apps.

Uno de los principales cambios entre las versiones es que los constructores ya no requieren una clave de aplicación.
Ahora basta con pasar la dirección URL de la aplicación móvil. Por ejemplo, en los clientes .NET, el constructor `MobileServiceClient` es ahora:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net" // URL of the Mobile App
        );

Encontrará información sobre cómo instalar los nuevos SDK y cómo usar la nueva estructura a través de los vínculos siguientes:

* [Versión de Android 2.2 o posterior](app-service-mobile-android-how-to-use-client-library.md)
* [iOS versión 3.0.0 o posterior](app-service-mobile-ios-how-to-use-client-library.md)
* [.NET (Windows/Xamarin) versión 2.0.0 o posterior](app-service-mobile-dotnet-how-to-use-client-library.md)
* [Apache Cordova versión 2.0 o posterior](app-service-mobile-cordova-how-to-use-client-library.md)

Si la aplicación hace uso de notificaciones de inserción, tome nota de las instrucciones de registro específicas para cada plataforma, ya que también ha habido algunos cambios al respecto.

Cuando tenga la nueva versión de cliente lista, pruébela en el proyecto de servidor actualizado. Después de comprobar que funciona, puede publicar una nueva versión de la aplicación para clientes. Al final, cuando los clientes hayan tenido ocasión de recibir estas actualizaciones, puede eliminar la versión de Mobile Services de su aplicación. Llegados a este punto, ha actualizado completamente a una aplicación móvil de App Service mediante el SDK de servidor de Mobile Apps más reciente.

<!-- URLs. -->

[Azure portal]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[¿Qué es Mobile Apps?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /en-us/documentation/services/scheduler/
[Web Job]: https://github.com/Azure/azure-webjobs-sdk/wiki
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[precios de App Service]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[conceptos de autenticación]: ../app-service/app-service-authentication-overview.md
[inicio rápido de autenticación]: app-service-mobile-auth.md

[Azure Portal]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
