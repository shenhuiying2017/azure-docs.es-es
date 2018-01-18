---
title: Trabajo con el SDK del servidor back-end de .NET para Mobile Apps | Microsoft Docs
description: "Obtenga información sobre cómo trabajar con el SDK del servidor back-end de .NET para Azure App Service Mobile Apps"
keywords: "servicio de aplicaciones, servicio de aplicaciones de azure, aplicación móvil, servicio móvil, escala, escalable, implementación de aplicaciones, implementación de aplicaciones de azure"
services: app-service\mobile
documentationcenter: 
author: conceptdev
manager: crdun
editor: 
ms.assetid: 0620554f-9590-40a8-9f47-61c48c21076b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: a1a29d87864bff8cb2ecda70d8a0a7833c70d481
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Trabajar con el SDK del servidor back-end de .NET para Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

En este tema se muestra cómo usar el SDK del servidor back-end de .NET en escenarios clave de Azure App Service Mobile Apps. El SDK de Azure Mobile Apps le permite trabajar con clientes móviles de su aplicación ASP.NET.

> [!TIP]
> El [SDK de servidor de .NET para Azure Mobile Apps][2] es de código abierto en GitHub. El repositorio contiene todo el código fuente incluido en el conjunto de pruebas de unidad SDK del servidor completo, así como algunos proyectos de ejemplo.
>
>

## <a name="reference-documentation"></a>Documentación de referencia
La documentación de referencia del SDK del servidor se encuentra aquí: [Referencia de .NET de Azure Mobile Apps][1].

## <a name="create-app"></a>Creación de un back-end de aplicación móvil .NET
Si va a iniciar un nuevo proyecto, puede crear una aplicación de App Service mediante el [Portal de Azure] o Visual Studio. Puede ejecutar la aplicación de App Service localmente o publicarla en la aplicación móvil de App Service basada en la nube.

Si va a agregar funcionalidades móviles a un proyecto existente, consulte la sección [Descarga e inicialización del SDK](#install-sdk) .

### <a name="create-a-net-backend-using-the-azure-portal"></a>Creación de un back-end .NET mediante el Portal de Azure
Para crear una instancia de App Service de back-end móvil, siga el [tutorial rápido][3] o estos pasos:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

De nuevo en la hoja *Comenzar*, en **Create a table API** (Crear una API de tabla), elija **C#** como el valor de **Backend language** (Lenguaje de back-end). Haga clic en **Descargar**, extraiga los archivos de proyecto comprimidos en el equipo local y abra la solución en Visual Studio.

### <a name="create-a-net-backend-using-visual-studio-2013-and-visual-studio-2015"></a>Creación de un back-end .NET con Visual Studio 2013 y Visual Studio 2015
Para crear un proyecto de Azure Mobile Apps en Visual Studio, instale la versión 2.9.0 o posterior de [Azure SDK para .NET][4]. Una vez haya instalado el SDK, cree una aplicación de ASP.NET mediante los siguientes pasos:

1. Abra el cuadro de diálogo **Nuevo proyecto** (desde *Archivo* > **Nuevo** > **Proyecto...**).
2. Expanda **Plantillas** > **Visual C#** y seleccione **Web**.
3. Seleccione **Aplicación web ASP.NET**.
4. Rellene el nombre del proyecto. A continuación, haga clic en **Aceptar**.
5. En *Plantillas de ASP.NET 4.5.2*, seleccione **Aplicación móvil de Azure**. Seleccione **Host en la nube** para crear un back-end móvil en la nube, en el que puede publicar este proyecto.
6. Haga clic en **OK**.

## <a name="install-sdk"></a>Descarga e inicialización del SDK
El SDK está disponible en [NuGet.org]. Este paquete incluye la funcionalidad básica necesaria para comenzar a usar el SDK. Para inicializar el SDK, tendrá que realizar acciones en el objeto **HttpConfiguration** .

### <a name="install-the-sdk"></a>Instalación del SDK
Para instalar el SDK, haga clic con el botón derecho en el proyecto de servidor en Visual Studio, seleccione **Administrar paquetes de NuGet**, busque el paquete [Microsoft.Azure.Mobile.Server] y haga clic en **Instalar**.

### <a name="server-project-setup"></a> Inicializar el proyecto de servidor
Un proyecto de servidor backend de .NET se inicializa de manera similar a otros proyectos ASP.NET mediante la inclusión de una clase de inicio OWIN. Asegúrese de que ha hecho referencia al paquete de NuGet `Microsoft.Owin.Host.SystemWeb`. Para agregar esta clase en Visual Studio, haga clic con el botón derecho en el proyecto de servidor y seleccione **Agregar** >
**Nuevo elemento**, luego **Web** > **General** > **Clase de inicio OWIN**.  Se genera una clase con el atributo siguiente:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

En el método `Configuration()` de la clase de inicio OWIN, use un objeto **HttpConfiguration** para configurar el entorno de Azure Mobile Apps.
En el ejemplo siguiente se inicializa el proyecto de servidor sin características agregadas:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Para habilitar características individuales, debe llamar a los métodos de extensión del objeto **MobileAppConfiguration** antes de llamar a **ApplyTo**. Por ejemplo, el siguiente código agrega las rutas predeterminadas a todos los controladores de API que tengan el atributo `[MobileAppController]` durante la inicialización:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

El inicio rápido de servidor desde el Portal de Azure llama a **UseDefaultConfiguration()**. Es equivalente a la siguiente configuración:

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

Los métodos de extensión utilizados son:

* `AddMobileAppHomeController()` proporciona la página principal predeterminada de Azure Mobile Apps.
* `MapApiControllers()` proporciona funcionalidades de API personalizadas para controladores de WebAPI que contienen el atributo `[MobileAppController]`.
* `AddTables()` proporciona una asignación de los puntos de conexión `/tables` a los controladores de la tabla.
* `AddTablesWithEntityFramework()` es un elemento abreviado para la asignación de los puntos de conexión `/tables` mediante controladores basados en Entity Framework.
* `AddPushNotifications()` proporciona un método sencillo de registrar los dispositivos para Notification Hubs.
* `MapLegacyCrossDomainController()` proporciona los encabezados de CORS estándar para el desarrollo local.

### <a name="sdk-extensions"></a>Extensiones de SDK
Los siguientes paquetes de extensión basados en NuGet proporcionan diversas características móviles que puede usar la aplicación. Las extensiones se habilitan durante la inicialización mediante el objeto **MobileAppConfiguration** .

* [Microsoft.Azure.Mobile.Server.Quickstart] admite la configuración básica de Mobile Apps. Se agrega a la configuración mediante una llamada al método de extensión **UseDefaultConfiguration** durante la inicialización. Esta extensión incluye las siguientes extensiones: paquetes de notificaciones, autenticación, entidad, tablas, entre dominios y principal. Inicio rápido de Mobile Apps, disponible en Azure Portal, usa este paquete.
* [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) Implementa el valor predeterminado de *esta página de aplicación móvil está funcionando* para la raíz del sitio web. Se agrega a la configuración mediante una llamada al método de extensión **AddMobileAppHomeController** .
* [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) incluye clases para trabajar con datos y configura la canalización de datos. Se agrega a la configuración mediante una llamada al método de extensión **AddTables** .
* [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) permite a Entity Framework obtener acceso a los datos de SQL Database. Se agrega a la configuración mediante una llamada al método de extensión **AddTablesWithEntityFramework** .
* [Microsoft.Azure.Mobile.Server.Authentication] habilita la autenticación y configura el middleware OWIN que se usa para validar los tokens. Se agrega a la configuración mediante una llamada a los métodos de extensión **AddAppServiceAuthentication** e **IAppBuilder**.**UseAppServiceAuthentication**.
* [Microsoft.Azure.Mobile.Server.Notifications] habilita las notificaciones push y define un punto de conexión de registro de inserción. Se agrega a la configuración mediante una llamada al método de extensión **AddPushNotifications** .
* [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) crea un controlador que sirve datos de la aplicación móvil a los exploradores web heredados. Se agrega a la configuración mediante una llamada al método de extensión **MapLegacyCrossDomainController** .
* [Microsoft.Azure.Mobile.Server.Login] proporciona el método AppServiceLoginHandler.CreateToken(), que es un método estático usado en escenarios de autenticación personalizada.

## <a name="publish-server-project"></a>Procedimientos: Publicación del proyecto de servidor
En esta sección se muestra cómo publicar el proyecto de back-end de .NET desde Visual Studio. También puede implementar el proyecto de back-end con [Git](../app-service/app-service-deploy-local-git.md) o con cualquiera de los demás métodos que están disponibles allí.

1. En Visual Studio, vuelva a generar el proyecto para restaurar los paquetes de NuGet.
2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto y luego haga clic en **Publicar**. La primera vez que publique, debe definir un perfil de publicación. Si ya tiene un perfil definido, puede seleccionarlo y hacer clic en **Publicar**.
3. Si se le pide que seleccione un destino de publicación, haga clic en **Microsoft Azure App Service** > **Siguiente** y, luego, (si es necesario), inicie sesión con sus credenciales de Azure.
   Visual Studio descarga y almacena la configuración de publicación directamente desde Azure.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. Elija su suscripción en **Suscripción**, seleccione **Tipo de recurso** en **Vista**, expanda **Aplicación móvil** y haga clic en el back-end de aplicación móvil y en **Aceptar**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. Compruebe la información del perfil de publicación y haga clic en **Publicar**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    Cuando el back-end de la aplicación móvil se haya publicado correctamente, verá una página de aterrizaje que indica el éxito.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="define-table-controller"></a> Definición de un controlador de tabla
Defina un controlador de tabla para exponer una tabla de SQL para clientes móviles.  La configuración de un controlador de tabla requiere tres pasos:

1. Creación de una clase Data Transfer Object (DTO).
2. Configuración de una referencia de tabla en la clase Mobile DbContext.
3. Creación de un controlador de tabla.

Un objeto de transferencia de datos (DTO) es un objeto de C# simple heredado de `EntityData`.  Por ejemplo: 

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

El objeto DTO se utiliza para definir la tabla en SQL Database.  Para crear la entrada de la base de datos, agregue una propiedad `DbSet<>` a la instancia de DbContext que esté utilizando.  En la plantilla de proyecto predeterminada para Azure Mobile Apps, la instancia de DbContext se llama `Models\MobileServiceContext.cs`:

    public class MobileServiceContext : DbContext
    {
        private const string connectionStringName = "Name=MS_TableConnectionString";

        public MobileServiceContext() : base(connectionStringName)
        {

        }

        public DbSet<TodoItem> TodoItems { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceColumnTable", (property, attributes) => attributes.Single().ColumnType.ToString()));
        }
    }

Si tiene instalado Azure SDK, ahora puede crear un controlador de tabla de la plantilla como sigue:

1. Haga clic con el botón derecho en la carpeta Controladores y seleccione **Agregar** > **Controlador...**.
2. Seleccione la opción **Controlador de tabla de Azure Mobile Apps** y haga clic en **Agregar**.
3. En el cuadro de diálogo **Agregar controlador** :
   * En la lista desplegable **Clase de modelo** , seleccione el nuevo DTO.
   * En la lista desplegable **DbContext** , seleccione la clase Mobile Service DbContext.
   * Se crea el nombre del controlador.
4. Haga clic en **Agregar**.

El proyecto de servidor de inicio rápido contiene un ejemplo de un controlador **TodoItemController**simple.

### <a name="adjust-pagesize"></a>Cómo ajustar el tamaño de paginación de la tabla
De forma predeterminada, Azure Mobile Apps devuelve 50 registros por solicitud.  La paginación garantiza que el cliente no mantenga ocupado su subproceso de interfaz de usuario ni el servidor durante mucho tiempo, con lo que se asegura una buena experiencia del usuario. Para cambiar el tamaño de paginación de la tabla, aumente el "tamaño de consulta permitido" del lado del servidor y cambie el tamaño de la página de lado del cliente. El "tamaño de consulta permitido" del lado del servidor se ajusta con el atributo `EnableQuery`:

    [EnableQuery(PageSize = 500)]

Asegúrese de que el valor de PageSize sea igual o mayor que el tamaño solicitado por el cliente.  Consulte la documentación de procedimientos para obtener más información sobre cómo cambiar el tamaño de página de cliente.

## <a name="how-to-define-a-custom-api-controller"></a>Cómo definir un controlador de API personalizada
El controlador de API personalizada proporciona la funcionalidad más básica al back-end de la aplicación móvil mediante la exposición de un extremo. Puede registrar un controlador de API específico de dispositivos móviles con el atributo [MobileAppController]. El atributo `MobileAppController` registra la ruta, configura el serializador JSON de Mobile Apps y activa la [comprobación de la versión del cliente](app-service-mobile-client-and-server-versioning.md).

1. En Visual Studio, haga clic con el botón derecho en la carpeta Controladores y, luego, haga clic en **Agregar** > **Controladores**, seleccione **Controlador de Web API 2&mdash;Vacío** y haga clic en **Agregar**.
2. Proporcione un valor de **Nombre de controlador**, como `CustomController`, y haga clic en **Agregar**.
3. En el archivo de clase de controlador nuevo, agregue la siguiente instrucción Using:

        using Microsoft.Azure.Mobile.Server.Config;
4. Aplique el atributo **[MobileAppController]** a la definición de clase del controlador de API, como en el ejemplo siguiente:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. En el archivo App_Start/Startup.MobileApp.cs, agregue una llamada al método de extensión **MapApiControllers**, como en el ejemplo siguiente:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

También puede utilizar el método de extensión `UseDefaultConfiguration()`, en lugar de `MapApiControllers()`. Los clientes pueden tener acceso a un controlador aunque este no tenga un elemento **MobileAppControllerAttribute** aplicado, pero puede que no lo consuman correctamente si usan un SDK de cliente de aplicación móvil.

## <a name="how-to-work-with-authentication"></a>Procedimiento: Autenticación
Azure Mobile Apps usa la autenticación o autorización de App Service para proteger su back-end móvil.  En esta sección se muestra cómo realizar las siguientes tareas relacionadas con la autenticación en el proyecto de servidor back-end. NET:

* [Cómo agregar autenticación a un proyecto de servidor](#add-auth)
* [Procedimiento: Uso de autenticación personalizada en la aplicación](#custom-auth)
* [Procedimiento: Recuperación de la información de usuario de autenticado](#user-info)
* [Cómo restringir el acceso a datos para los usuarios autorizados](#authorize)

### <a name="add-auth"></a>Cómo agregar autenticación a un proyecto de servidor
Para agregar autenticación al proyecto de servidor, extienda el objeto **MobileAppConfiguration** y configure el middleware OWIN. Cuando instale el paquete [Microsoft.Azure.Mobile.Server.Quickstart] y llame al método de extensión **UseDefaultConfiguration** , puede continuar desde el paso 3.

1. En Visual Studio, instale el paquete [Microsoft.Azure.Mobile.Server.Authentication] .
2. En el archivo de proyecto Startup.cs, agregue la siguiente línea de código al principio del método **Configuration** :

        app.UseAppServiceAuthentication(config);

    Este componente del middleware OWIN valida los tokens emitidos por la puerta de enlace de App Service asociada.
3. Agregue el atributo `[Authorize]` a cualquier controlador o método que requiera autenticación.

Para más información sobre cómo autenticar a los clientes en el back-end de Mobile Apps, consulte [Incorporación de la autenticación a la aplicación](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Procedimiento: Uso de autenticación personalizada en la aplicación
> [!IMPORTANT]
> Con el fin de habilitar la autenticación personalizada, primero debe habilitar la autenticación de App Service sin seleccionar un proveedor para la instancia en Azure Portal. Esto habilitará la variable de entorno WEBSITE_AUTH_SIGNING_KEY al hospedarla.
> 
> 
Si no desea usar uno de los proveedores de autenticación y autorización de App Service, puede implementar su propio sistema de inicio de sesión. Instale el paquete [Microsoft.Azure.Mobile.Server.Login] para ayudar a la generación de tokens de autenticación.  Proporcione su propio código para validar las credenciales del usuario. Por ejemplo, podría comprobar las contraseñas con sal y hash de una base de datos. En el ejemplo siguiente, el método `isValidAssertion()` (definido en otra parte) es responsable de estas comprobaciones.

La autenticación personalizada se expone mediante la creación de un controlador ApiController y la exposición de las acciones `register` y `login`. El cliente debe utilizar una interfaz de usuario personalizada para recopilar la información del usuario.  A continuación, la información se envía a la API con una llamada HTTP POST estándar. Una vez que el servidor valida la aserción, se emite un token con el método `AppServiceLoginHandler.CreateToken()` .  El controlador ApiController **no debería** utilizar el atributo `[MobileAppController]`.

Ejemplo de la acción `login` :

        public IHttpActionResult Post([FromBody] JObject assertion)
        {
            if (isValidAssertion(assertion)) // user-defined function, checks against a database
            {
                JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
                    mySigningKey,
                    myAppURL,
                    myAppURL,
                    TimeSpan.FromHours(24) );
                return Ok(new LoginResult()
                {
                    AuthenticationToken = token.RawData,
                    User = new LoginResultUser() { UserId = userName.ToString() }
                });
            }
            else // user assertion was not valid
            {
                return this.Request.CreateUnauthorizedResponse();
            }
        }

En el ejemplo anterior, LoginResult y LoginResultUser son objetos serializables que exponen las propiedades necesarias. El cliente espera que las repuestas de inicio de sesión se devuelvan como objetos JSON con este formato:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

El método `AppServiceLoginHandler.CreateToken()` incluye un parámetro *audience* y un parámetro *issuer*. Ambos parámetros se establecen en la dirección URL de la raíz de la aplicación, mediante el esquema HTTPS. De igual modo, debe establecer *secretKey* como el valor de la clave de firma de la aplicación. No distribuya la clave de firma en un cliente, ya que se puede usar para inventar claves y suplantar a los usuarios. Puede obtener la clave de firma mientras se hospeda en App Service mediante la referencia a la variable de entorno *WEBSITE\_AUTH\_SIGNING\_KEY*. Si es necesario en un contexto de depuración local, siga las instrucciones de la sección [Depuración local con autenticación](#local-debug) para recuperar la clave y almacenarla como un valor de configuración de la aplicación.

El token emitido también puede incluir otras notificaciones y una fecha de expiración.  Como mínimo, el token emitido debe incluir una notificación de asunto (**sub**).

Puede admitir el método `loginAsync()` del cliente estándar mediante la sobrecarga de la ruta de autenticación.  Si el cliente llama a `client.loginAsync('custom');` para iniciar sesión, la ruta debe ser `/.auth/login/custom`.  Puede establecer la ruta para el controlador de autenticación personalizada con `MapHttpRoute()`:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> Con el enfoque `loginAsync()` se garantiza que el token de autenticación se asocia a cada llamada posterior al servicio.
>
>

### <a name="user-info"></a>Procedimiento: Recuperación de la información de usuario autenticada
Cuando un usuario se autentica mediante App Service, se puede tener acceso al id. de usuario asignado y otra información en el código del back-end. NET. La información de usuario se puede utilizar para tomar decisiones de autorización en el back-end. El código siguiente obtiene el identificador de usuario asociado a una solicitud:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

El SID se deriva del identificador de usuario específico del proveedor y es estático para un usuario y un proveedor de inicio de sesión dados.  El SID es nulo para los tokens de autenticación no válidos.

App Service también le permite solicitar notificaciones específicas de su proveedor de inicio de sesión. Cada proveedor de identidades puede proporcionar más información mediante el SDK del proveedor de identidades.  Por ejemplo, puede usar la API Graph de Facebook para la información de los amigos.  Puede especificar notificaciones solicitadas en la hoja del proveedor en Azure Portal. Algunas notificaciones requieren configuración adicional con el proveedor de identidades.

El código siguiente llama al método de extensión **GetAppServiceIdentityAsync** para obtener las credenciales de inicio de sesión, que incluyen el token de acceso necesario para realizar solicitudes en la API Graph de Facebook:

    // Get the credentials for the logged-in user.
    var credentials =
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token="
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

Agregue una instrucción de uso para que `System.Security.Principal` proporcione el método de extensión **GetAppServiceIdentityAsync** .

### <a name="authorize"></a>Cómo restringir el acceso a datos para los usuarios autorizados
En la sección anterior, hemos mostrado cómo recuperar el identificador de usuario de un usuario autenticado. Puede restringir el acceso a datos y otros recursos basándose en este valor. Por ejemplo, agregar una columna de identificador de usuario (userId) a las tablas y filtrar los resultados de la consulta por el identificador de usuario es una manera sencilla de limitar los datos devueltos únicamente a los usuarios autorizados. El código siguiente solo devuelve filas de datos cuando el SID coincide con el valor de la columna UserId de la tabla TodoItem:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

El método `Query()` devuelve un `IQueryable` que LINQ puede manipular para controlar el filtrado.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Cómo agregar notificaciones push a un proyecto de servidor
Para agregar notificaciones push al proyecto de servidor, extienda el objeto **MobileAppConfiguration** y cree un cliente de Notification Hubs.

1. En Visual Studio, haga clic con el botón derecho en el proyecto de servidor, haga clic en **Administrar paquetes de NuGet**, busque `Microsoft.Azure.Mobile.Server.Notifications` y, por último, haga clic en **Instalar**.
2. Repita este paso para instalar el paquete `Microsoft.Azure.NotificationHubs` , que incluye la biblioteca de cliente de Notification Hubs.
3. En App_Start/Startup.MobileApp.cs, agregue una llamada al método de extensión **AddPushNotifications()** durante la inicialización:

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);
4. Agregue el siguiente código, que crea un nuevo cliente de Notification Hubs:

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

Ahora puede usar el cliente de Notification Hubs para enviar notificaciones push a dispositivos registrados. Para más información, vea [Incorporación de notificaciones push a la aplicación](app-service-mobile-ios-get-started-push.md). Aprenda más sobre Notification Hubs en la [introducción a Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="tags"></a>Habilitación de la inserción de destino mediante etiquetas
Notification Hubs permite enviar notificaciones dirigidas a registros específicos mediante el uso de etiquetas. Se crean varias etiquetas automáticamente:

* El identificador de instalación identifica un dispositivo específico.
* El identificador de usuario basado en el SID autenticado identifica un usuario específico.

Se puede acceder al identificador de instalación desde la propiedad **installationId** en **MobileServiceClient**.  En el ejemplo siguiente se muestra cómo usar un identificador de instalación para agregar una etiqueta a una instalación específica en Notification Hubs:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Al crear la instalación, el back-end ignora las etiquetas proporcionadas por el cliente durante el registro de notificaciones push. Para que un cliente pueda agregar etiquetas a la instalación, debe crear una API personalizada que agregue etiquetas mediante el patrón anterior.

Consulte la información sobre las [etiquetas de notificaciones push agregadas por el cliente][5] en el ejemplo de inicio rápido completado de App Service Mobile Apps para ver un ejemplo.

## <a name="push-user"></a>Envío de notificaciones push a un usuario autenticado
Cuando un usuario autenticado se registra para las notificaciones push, se agrega automáticamente una etiqueta con el identificador de usuario al registro. Mediante esta etiqueta, puede enviar notificaciones push a todos los dispositivos registrados por ese usuario. El código siguiente obtiene el SID del usuario que realiza la solicitud y envía una notificación push de plantilla a cada registro de dispositivo para esa persona:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Cuando se registre para notificaciones push desde un cliente autenticado, asegúrese de que la autenticación se ha completado antes de intentar el registro. Para más información, consulte [Push to users][6] (Notificación push a usuarios) en el ejemplo de inicio rápido de App Service Mobile Apps completado para el back-end de .NET.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Procedimientos: Depuración y solución de problemas del SDK de .NET Server
Azure App Service proporciona varias técnicas de depuración y solución de problemas para las aplicaciones ASP.NET.

* [Supervisión de un servicio de Azure App Service](../app-service/web-sites-monitor.md)
* [Habilitación del registro de diagnóstico para aplicaciones web en Azure App Service](../app-service/web-sites-enable-diagnostic-log.md)
* [Solución de problemas de Azure App Service en Visual Studio](../app-service/web-sites-dotnet-troubleshoot-visual-studio.md)

### <a name="logging"></a>Registro
Puede escribir en registros de diagnóstico de App Service mediante la escritura de seguimiento estándar de ASP.NET. Antes de poder escribir en los registros, debe habilitar los diagnósticos en su back-end de aplicación móvil.

Para habilitar los diagnósticos y escribir en los registros:

1. Siga los pasos que se indican en [Habilitación de diagnósticos](../app-service/web-sites-enable-diagnostic-log.md#enablediag).
2. Agregue la siguiente instrucción using en el archivo de código:

        using System.Web.Http.Tracing;
3. Cree un escritor de seguimiento para escribir desde el back-end de .NET en los registros de diagnóstico, de la manera siguiente:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. Vuelva a publicar el proyecto de servidor y acceda al back-end de aplicación móvil para ejecutar la ruta de acceso del código con el registro.
5. Descargue y evalúe los registros, como se describe en [Procedimiento: Descarga de registros](../app-service/web-sites-enable-diagnostic-log.md#download).

### <a name="local-debug"></a>Depuración local con autenticación
Puede ejecutar la aplicación localmente para probar los cambios antes de publicarlos en la nube. Para la mayoría de los servidores back-end de Azure Mobile Apps, presione *F5* mientras está en Visual Studio. Sin embargo, hay algunas consideraciones adicionales cuando se usa la autenticación.

Debe tener una aplicación móvil basada en la nube que tenga configurada la característica Autenticación/autorización de App Service, y su cliente debe haber especificado el punto de conexión de nube como host de inicio de sesión alternativo. Consulte la documentación de la plataforma cliente para los pasos específicos requeridos.

Asegúrese de que el back-end tenga instalado [Microsoft.Azure.Mobile.Server.Authentication] . Después, en la clase de inicio OWIN de la aplicación, agregue lo siguiente, después de aplicar `MobileAppConfiguration` a `HttpConfiguration`:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

En el ejemplo anterior, debe configurar los parámetros de la aplicación *authAudience* y *authIssuer* en el archivo Web.config para que cada uno sea la dirección URL de la raíz de la aplicación; para ello, usará el esquema HTTPS. De igual modo, debe establecer *authSigningKey* como el valor de la clave de firma de la aplicación.
Para obtener la clave de firma:

1. Vaya a la aplicación en [Portal de Azure]
2. Haga clic en **Herramientas**, **Kudu**, **Ir**.
3. En el sitio de administración de Kudu, haga clic en **Entorno**.
4. Busque el valor de *WEBSITE\_AUTH\_SIGNING\_KEY*.

Use la clave de firma para el parámetro *authSigningKey* en la configuración de la aplicación local.  Ahora, el back-end móvil está equipado para validar los tokens cuando se ejecuta localmente; el cliente obtiene el token del punto de conexión basado en la nube.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[Portal de Azure]: https://portal.azure.com
[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx
