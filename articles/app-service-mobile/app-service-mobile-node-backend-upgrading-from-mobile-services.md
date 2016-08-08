<properties
	pageTitle="Actualización de Servicios móviles a Servicio de aplicaciones de Azure - Node.js"
	description="Aprenda a actualizar fácilmente la aplicación de Servicios móviles a una aplicación móvil del Servicio de aplicaciones."
	services="app-service\mobile"
	documentationCenter=""
	authors="adrianhall"
	manager="ggailey"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile"
	ms.devlang="node"
	ms.topic="article"
	ms.date="05/05/2016"
	ms.author="adrianha"/>

# Actualización del Servicio móvil de Azure de Node.js existente a Servicio de aplicaciones

Aplicaciones móviles del Servicio de aplicaciones es una nueva forma de crear aplicaciones móviles con Microsoft Azure. Para más información, vea [¿Qué es Aplicaciones móviles?].

En este tema se describe cómo actualizar una aplicación back-end de Node.js existente en Servicios móviles de Azure a una nueva instancia de Aplicaciones móviles del Servicio de aplicaciones. Cuando realice esta migración, la aplicación de Servicios móviles existente puede continuar funcionando. Si necesita actualizar una aplicación back-end de .NET, consulte [Actualización de los servicios móviles de .NET](./app-service-mobile-net-upgrading-from-mobile-services.md).

Cuando un back-end móvil se actualiza a Servicio de aplicaciones de Azure, accede a todas las características de Servicio de aplicaciones y se factura conforme a los [precios del Servicio de aplicaciones], no según los precios de Servicios móviles.

## Migración frente a actualización

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP] Se recomienda [realizar una migración](app-service-mobile-migrating-from-mobile-services.md) antes de pasar por una actualización. De este modo, puede colocar las dos versiones de la aplicación en el mismo Plan del Servicio de aplicaciones y no incurrir en ningún coste adicional.

### Mejoras en el SDK de servidor para Node.js de Aplicaciones móviles

La actualización al nuevo [SDK de Aplicaciones móviles](https://www.npmjs.com/package/azure-mobile-apps) ofrece muchas mejoras, entre las que se incluyen:

- Basado en la [plataforma Express](http://expressjs.com/en/index.html), el nuevo SDK para Node es ligero y está diseñado para mantenerse al día con nuevas versiones de Node a medida que salen. Puede personalizar el comportamiento de la aplicación con middleware de Express.

- Ofrece mejoras de rendimiento significativas en comparación con el SDK de Servicios móviles.

- Ahora puede hospedar un sitio web junto con el back-end móvil; asimismo, es fácil agregar el Azure Mobile SDK a cualquier aplicación v4 existente.

- Creado para desarrollo multiplataforma y local, el SDK de Aplicaciones móviles se puede desarrollar y ejecutar localmente en plataformas Windows, Linux y OSX. Ahora es fácil usar técnicas de desarrollo comunes de Node como, por ejemplo, ejecutar pruebas [Mocha](https://mochajs.org/) antes de la implementación.

## <a name="overview"></a>Información general básica de actualización

Para facilitar la actualización de un back-end de Node.js, el Servicio de aplicaciones de Azure ha proporcionado un paquete de compatibilidad. Tras la actualización, tendrá un nuevo sitio que se puede implementar en un nuevo sitio del Servicio de aplicaciones.

Los SDK de cliente de Servicios móviles **no** son compatibles con el nuevo SDK de servidor de Aplicaciones móviles. A fin de ofrecer continuidad del servicio para la aplicación, no debe publicar cambios en un sitio que actualmente presta servicio a clientes publicados. En su lugar, debe crear una aplicación móvil que actúe como duplicado. Puede colocar esta aplicación en el mismo Plan del Servicio de aplicaciones para evitar incurrir en costos financieros adicionales.

Entonces tendrá dos versiones de la aplicación: una que permanece igual y presta servicio a las aplicaciones publicadas en su estado natural, y otra que después se puede actualizar y destinar a una nueva versión del cliente. Puede mover y probar el código a su ritmo, pero debe asegurarse de que las correcciones de errores se apliquen a ambas. Cuando crea que la cantidad que elija de aplicaciones cliente en estado natural se han actualizado a la versión más reciente, puede eliminar si quiere la aplicación migrada original. No incurre en costos monetarios adicionales, si se hospeda en el mismo Plan del Servicio de aplicaciones que la aplicación móvil.

El esquema completo del proceso de actualización es el siguiente:

1. Descargue el servicio móvil de Azure existente (migrado).
2. Convierta el proyecto en una aplicación móvil de Azure mediante el paquete de compatibilidad.
3. Corrija cualquier diferencia (como la configuración de autenticación).
4. Implemente el proyecto de aplicación móvil de Azure convertido en un nuevo Servicio de aplicaciones.
4. Publique una nueva versión de la aplicación cliente que utilice la nueva aplicación móvil.
5. (Opcional) Eliminar la aplicación de servicio móvil migrada original

La eliminación puede tener lugar cuando no vea tráfico alguno en el servicio móvil migrado original.

## <a name="install-npm-package"></a> Instalación de los requisitos previos

Debe instalar [Node] en el equipo local. También debe instalar el paquete de compatibilidad. Después de instalar Node, puede ejecutar el comando siguiente desde un nuevo cmd o símbolo del sistema de PowerShell:

```npm i -g azure-mobile-apps-compatibility```

## <a name="obtain-ams-scripts"></a> Obtención de los scripts de Servicios móviles de Azure

- Inicie sesión en el [Portal de Azure].
- Mediante **Todos los recursos** o **Servicios de aplicaciones**, busque el sitio de Servicios móviles.
- En el sitio, haga clic en **Herramientas** -> **Kudu** -> **Ir** para abrir el sitio de Kudu.
- Haga clic en **Debug Console** (Consola de depuración) -> **PowerShell** para abrir la consola de depuración.
- Vaya a `site/wwwroot/App_Data/config` haciendo clic por turnos en cada directorio.
- Haga clic en el icono de descarga junto al directorio `scripts`.

Se descargarán los scripts en formato ZIP. Cree un nuevo directorio en la máquina local y desempaquete el archivo `scripts.ZIP` dentro del directorio. Se crea un directorio `scripts`.

## <a name="scaffold-app"></a> Realización de scaffold del nuevo back-end de Aplicaciones móviles de Azure

Ejecute el siguiente comando desde el directorio que contiene el directorio de scripts:

```scaffold-mobile-app scripts out```

Se crea un back-end de Aplicaciones móviles de Azure con scaffold en el directorio `out`. Aunque no es necesario, conviene comprobar el directorio `out` en un repositorio de código fuente de su elección.

## <a name="deploy-ama-app"></a> Implementación del back-end de Aplicaciones móviles de Azure

Durante la implementación, deberá realizar estos pasos:

1. Cree una nueva aplicación móvil en el [Portal de Azure].
2. Ejecute el script `createViews.sql` en la base de datos conectada.
3. Vincule la base de datos que está vinculada a su servicio móvil a su nuevo Servicio de aplicaciones.
4. Vincule otros recursos (como los Centros de notificaciones) al nuevo Servicio de aplicaciones.
5. Implemente el código generado en el nuevo sitio.

### Creación de una aplicación móvil

1. Inicie sesión en el [Portal de Azure].

2. Haga clic en **+NUEVO** > **Web y móvil** > **Aplicación móvil** y, luego, proporcione un nombre para el back-end de la aplicación móvil.

3. En **Grupo de recursos**, seleccione un grupo de recursos existente o cree uno nuevo (con el mismo nombre que su aplicación).
 
	Puede seleccionar un plan de Servicio de aplicaciones ya existente o crear uno nuevo. Para más información sobre los planes de Servicios de aplicaciones y cómo crear un nuevo plan en un plan de tarifa diferente en la ubicación deseada, consulte [Introducción detallada sobre los planes del Servicio de aplicaciones de Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

4. En **Plan del Servicio de aplicaciones**, se selecciona el plan predeterminado (en el [nivel estándar](https://azure.microsoft.com/pricing/details/app-service/)). También puede seleccionar otro plan o [crear uno](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan). La configuración del plan del Servicio de aplicaciones determina la [ubicación, las características, el costo y los recursos de proceso](https://azure.microsoft.com/pricing/details/app-service/) asociados a la aplicación.

	Después de decidir el plan, haga clic en **Crear**. Esto crea el back-end de aplicación móvil


### Ejecución de CreateViews.SQL

La aplicación con scaffold contiene un archivo llamado `createViews.sql`. Este script debe ejecutarse con la base de datos de destino. La cadena de conexión de la base de datos de destino puede obtenerse del servicio móvil migrado, en la hoja **Configuración** en **Cadenas de conexión**. Se llama `MS_TableConnectionString`.

Puede ejecutar este script desde SQL Server Management Studio o Visual Studio.

### Vinculación de la base de datos al Servicio de aplicaciones

Vincule la base de datos existente a su Servicio de aplicaciones:

- En el [Portal de Azure], abra el Servicio de aplicaciones.
- Seleccione **Toda la configuración de** -> **Conexiones de datos**.
- Haga clic en **+ Agregar**.
- En la lista desplegable, seleccione **Base de datos SQL**
- En **Base de datos SQL**, seleccione la base de datos existente y luego haga clic en **Seleccionar**.
- En **Cadena de conexión**, escriba el nombre de usuario y la contraseña de la base de datos y luego haga clic en **Aceptar**.
- En la hoja **Add data connections** (Agregar conexiones de datos), haga clic en **Aceptar**.

El nombre de usuario y la contraseña se pueden ver en la cadena de conexión de la base de datos de destino del servicio móvil migrado.


### Configuración de la autenticación

El servicio Aplicaciones móviles de Azure le permite configurar la autenticación de Azure Active Directory, Facebook, Google, Microsoft y Twitter en el servicio. La autenticación personalizada deberá desarrollarse por separado. Para más información, consulte la documentación sobre los [conceptos de autenticación] y el [inicio rápido de autenticación].

## <a name="updating-clients"></a>Actualización de los clientes móviles

Cuando tenga un back-end de aplicación móvil operativo, podrá trabajar en una nueva versión de la aplicación cliente que lo usa. Aplicaciones móviles incluye también una nueva versión de los SDK de cliente y, de forma similar a la actualización del servidor anterior, tendrá que quitar todas las referencias a los SDK de Servicios móviles antes de instalar las versiones de Aplicaciones móviles.

Uno de los principales cambios entre las versiones es que los constructores ya no requieren una clave de aplicación. Ahora basta con pasar la dirección URL de la aplicación móvil. Por ejemplo, en los clientes .NET, el constructor `MobileServiceClient` es ahora:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Encontrará información sobre cómo instalar los nuevos SDK y cómo usar la nueva estructura a través de los vínculos siguientes:

- [Versión de Android 2.2 o posterior](app-service-mobile-android-how-to-use-client-library.md)
- [iOS versión 3.0.0 o posterior](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin) versión 2.0.0 o posterior](app-service-mobile-dotnet-how-to-use-client-library.md)
- [Apache Cordova versión 2.0 o posterior](app-service-mobile-cordova-how-to-use-client-library.md)

Si la aplicación hace uso de notificaciones de inserción, tome nota de las instrucciones de registro específicas para cada plataforma, ya que también ha habido algunos cambios al respecto.

Cuando tenga la nueva versión de cliente lista, pruébela en el proyecto de servidor actualizado. Después de comprobar que funciona, puede publicar una nueva versión de la aplicación para clientes. Al final, cuando los clientes hayan tenido ocasión de recibir estas actualizaciones, puede eliminar la versión de Servicios móviles de su aplicación. Llegados a este punto, ha actualizado completamente a una aplicación móvil del Servicio de aplicaciones mediante el SDK de servidor de Aplicaciones móviles más reciente.

<!-- URLs. -->

[Azure portal]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[¿Qué es Aplicaciones móviles?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /es-ES/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /es-ES/documentation/services/scheduler/
[Web Job]: ../app-service-web/websites-webjobs-resources.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[precios del Servicio de aplicaciones]: https://azure.microsoft.com/es-ES/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[conceptos de autenticación]: ../app-service/app-service-authentication-overview.md
[inicio rápido de autenticación]: app-service-mobile-auth.md

[Portal de Azure]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/es-ES/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/es-ES/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

<!---HONumber=AcomDC_0727_2016-->