---
title: Autenticación y autorización de usuarios de extremo a extremo en Azure App Service | Microsoft Docs
description: Aprenda a usar la autenticación y autorización de App Service para proteger sus aplicaciones de App Service, como el acceso a las API remotas.
keywords: app service, azure app service, authN, authZ, secure, security, multi-tiered, azure active directory, azure ad
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/03/2018
ms.author: cephalin
ms.openlocfilehash: 4bdb182d93b842bf94e75672b1d7b4cf4f6da253
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
ms.locfileid: "31589159"
---
# <a name="tutorial-authenticate-and-authorize-users-end-to-end-in-azure-app-service"></a>Tutorial: Autenticación y autorización de usuarios de extremo a extremo en Azure App Service

[Azure App Service](app-service-web-overview.md) proporciona un servicio de hospedaje web muy escalable y con aplicación de revisiones de un modo automático. Además, App Service incluye compatibilidad integrada con la [autenticación y autorización de usuarios](app-service-authentication-overview.md). En este tutorial se muestra cómo proteger las aplicaciones con la autenticación y autorización de App Service. Se usa una aplicación de ASP.NET Core con un front-end Angular.js, pero solo como ejemplo. La autenticación y autorización de App Service admiten entornos de ejecución de todos los lenguajes; en este tutorial, aprenderá a aplicarlas a su lenguaje preferido.

En este tutorial se usa la aplicación de ejemplo para mostrarle cómo proteger una aplicación independiente (en [Habilitación de la autenticación y autorización en una aplicación de back-end](#enable-authentication-and-authorization-for-back-end-app)).

![Autenticación y autorización simples](./media/app-service-web-tutorial-auth-aad/simple-auth.png)

También se muestra cómo proteger una aplicación de niveles múltiples accediendo a una API back-end protegida en nombre del usuario autenticado, tanto [desde el código de servidor](#call-api-securely-from-server-code) como [desde el código de explorador](#call-api-securely-from-browser-code).

![Autenticación y autorización avanzadas](./media/app-service-web-tutorial-auth-aad/advanced-auth.png)

Estos son solo algunos de los posibles escenarios de autenticación y autorización de App Service. 

Esta es una lista más completa de lo que aprenderá en el tutorial:

> [!div class="checklist"]
> * Habilitación de la autenticación y autorización integradas
> * Protección de las aplicaciones frente a las solicitudes no autenticadas
> * Uso de Azure Active Directory como proveedor de identidades
> * Acceso a una aplicación remota en nombre del usuario que inició sesión
> * Protección de llamadas de servicio a servicio con autenticación de tokens
> * Uso de tokens de acceso desde el código de servidor
> * Uso de tokens de acceso desde el código de cliente (explorador)

También puede seguir los pasos de este tutorial en macOS, Linux, Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial:

* [Instale Git](https://git-scm.com/).
* [Instale .NET Core](https://www.microsoft.com/net/core/).

## <a name="create-local-net-core-app"></a>Creación de una aplicación .NET Core local

En este paso, configurará el proyecto .NET Core local. Usará el mismo proyecto para implementar una aplicación de API de back-end y una aplicación web de front-end.

### <a name="clone-and-run-the-sample-application"></a>Clonación y ejecución de la aplicación de ejemplo

Ejecute los comandos siguientes para clonar el repositorio de ejemplo y ejecutarlo.

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
cd dotnet-core-api
dotnet run
```

Vaya a `http://localhost:5000` e intente agregar, modificar y quitar elementos de lista de tareas. 

![API de ASP.NET Core en ejecución local](./media/app-service-web-tutorial-auth-aad/local-run.png)

Para detener ASP.NET Core en cualquier momento, presione `Ctrl+C` en el terminal.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-apps-to-azure"></a>Implementación de aplicaciones en Azure

En este paso, implementará el proyecto en dos aplicaciones de App Service. Una es la aplicación de front-end y la otra es la aplicación de back-end.

### <a name="create-azure-resources"></a>Creación de recursos de Azure

En Cloud Shell, ejecute los siguientes comandos para crear dos aplicaciones web. Reemplace _&lt;nombre\_aplicación\_front\_end>_ y _&lt;nombre\_aplicación\_back\_end>_ por dos nombres de aplicación globalmente únicos (los caracteres válidos son `a-z`, `0-9` y `-`). Para más información sobre cada comando, consulte [API RESTful con CORS en Azure App Service](app-service-web-tutorial-rest-api.md).

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front_end_app_name> --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back_end_app_name> --deployment-local-git --query deploymentLocalGitUrl
```

> [!NOTE]
> Guarde las direcciones URL de los repositorios Git remotos de su aplicación de front-end y de back-end, que se muestran en la salida de `az webapp create`.
>

### <a name="push-to-azure-from-git"></a>Inserción en Azure desde Git

De nuevo en la _ventana de terminal local_, ejecute los siguientes comandos de Git para implementar la aplicación de back-end. Reemplace _&lt;deploymentLocalGitUrl-of-back-end-app>_ por la dirección URL del repositorio Git remoto que guardó en [Creación de recursos de Azure](#create-azure-resources). Cuando el administrador de credenciales de Git le solicite las credenciales, asegúrese de especificar sus [credenciales de implementación](app-service-deployment-credentials.md), y no las usadas para iniciar sesión en Azure Portal.

```bash
git remote add backend <deploymentLocalGitUrl-of-back-end-app>
git push backend master
```

En la ventana de terminal local, ejecute los siguientes comandos de Git para implementar el mismo código en la aplicación de front-end. Reemplace _&lt;deploymentLocalGitUrl-of-front-end-app>_ por la dirección URL del repositorio Git remoto que guardó en [Creación de recursos de Azure](#create-azure-resources).

```bash
git remote add frontend <deploymentLocalGitUrl-of-front-end-app>
git push frontend master
```

### <a name="browse-to-the-azure-web-apps"></a>Navegación hasta las aplicaciones web de Azure

Use un explorador para ir a las siguientes direcciones URL y ver las dos aplicaciones en funcionamiento.

```
http://<back_end_app_name>.azurewebsites.net
http://<front_end_app_name>.azurewebsites.net
```

![API de ASP.NET Core que se ejecuta en Azure App Service](./media/app-service-web-tutorial-auth-aad/azure-run.png)

> [!NOTE]
> Si se reinicia la aplicación, quizás haya observado que se han borrado los nuevos datos. Este comportamiento se debe a que la aplicación de ASP.NET Core de ejemplo usa una base de datos en memoria.
>
>

## <a name="call-back-end-api-from-front-end"></a>Llamada a una API de back-end desde el front-end

En este paso, vinculará el código de servidor de la aplicación de front-end para acceder a la API de back-end. Más adelante, habilitará el acceso autenticado del front-end al back-end.

### <a name="modify-front-end-code"></a>Modificación del código de front-end

En el repositorio local, abra _Controllers/TodoController.cs_. Al comienzo de la clase `TodoController`, agregue las siguientes líneas y reemplace _&lt;nombre\_aplicación\_back\_end>_ por el nombre de la aplicación de back-end:

```cs
private static readonly HttpClient _client = new HttpClient();
private static readonly string _remoteUrl = "https://<back_end_app_name>.azurewebsites.net";
```

Busque el método `GetAll()` y reemplace el código entre llaves por:

```cs
var data = _client.GetStringAsync($"{_remoteUrl}/api/Todo").Result;
return JsonConvert.DeserializeObject<List<TodoItem>>(data);
```

La primera línea realiza una llamada `GET /api/Todo` a la aplicación de API de back-end.

A continuación, busque el método `GetById(long id)` y reemplace el código entre llaves por:

```cs
var data = _client.GetStringAsync($"{_remoteUrl}/api/Todo/{id}").Result;
return Content(data, "application/json");
```

La primera línea realiza una llamada `GET /api/Todo/{id}` a la aplicación de API de back-end.

A continuación, busque el método `Create([FromBody] TodoItem item)` y reemplace el código entre llaves por:

```cs
var response = _client.PostAsJsonAsync($"{_remoteUrl}/api/Todo", item).Result;
var data = response.Content.ReadAsStringAsync().Result;
return Content(data, "application/json");
```

La primera línea realiza una llamada `POST /api/Todo` a la aplicación de API de back-end.

A continuación, busque el método `Update(long id, [FromBody] TodoItem item)` y reemplace el código entre llaves por:

```cs
var res = _client.PutAsJsonAsync($"{_remoteUrl}/api/Todo/{id}", item).Result;
return new NoContentResult();
```

La primera línea realiza una llamada `PUT /api/Todo/{id}` a la aplicación de API de back-end.

A continuación, busque el método `Delete(long id)` y reemplace el código entre llaves por:

```cs
var res = _client.DeleteAsync($"{_remoteUrl}/api/Todo/{id}").Result;
return new NoContentResult();
```

La primera línea realiza una llamada `DELETE /api/Todo/{id}` a la aplicación de API de back-end.

Guarde todos los cambios. En la ventana de terminal local, implemente los cambios en la aplicación de front-end con los siguientes comandos de Git:

```bash
git add .
git commit -m "call back-end API"
git push frontend master
```

### <a name="check-your-changes"></a>Comprobación de los cambios

Vaya a `http://<front_end_app_name>.azurewebsites.net` y agregue algunos elementos, como `from front end 1` y `from front end 2`.

Vaya a `http://<back_end_app_name>.azurewebsites.net` para ver los elementos agregados desde la aplicación de front-end. Además, agregue algunos elementos, como `from back end 1` y `from back end 2`, y luego actualice la aplicación de front-end para ver si refleja los cambios.

![API de ASP.NET Core que se ejecuta en Azure App Service](./media/app-service-web-tutorial-auth-aad/remote-api-call-run.png)

## <a name="configure-auth"></a>Configuración de la autenticación

En este paso, habilitará la autenticación y autorización para las dos aplicaciones. También configurará la aplicación de front-end para generar un token de acceso que puede usar para realizar llamadas autenticadas a la aplicación de back-end.

Usará Azure Active Directory como proveedor de identidades. Para más información, consulte [Configuración de la autenticación de Azure Active Directory para una aplicación de App Services](app-service-mobile-how-to-configure-active-directory-authentication.md).

### <a name="enable-authentication-and-authorization-for-back-end-app"></a>Habilitación de la autenticación y autorización en una aplicación de back-end

En [Azure Portal](https://portal.azure.com), haga clic en el menú de la izquierda para abrir la página de administración de la aplicación de back-end: **Grupos de recursos** > **myAuthResourceGroup** > _\<nombre\_aplicación\_back\_end>_.

![API de ASP.NET Core que se ejecuta en Azure App Service](./media/app-service-web-tutorial-auth-aad/portal-navigate-back-end.png)

En el menú de la izquierda de su aplicación de back-end, haga clic en **Autenticación/autorización** y luego haga clic en **Activado** para habilitar la autenticación de App Service.

En **Action to take when request is not authenticated** (Acción necesaria cuando la solicitud no está autenticada), seleccione **Log in with Azure Active Directory** (Iniciar sesión con Azure Active Directory).

En **Proveedores de autenticación**, haga clic en **Azure Active Directory**. 

![API de ASP.NET Core que se ejecuta en Azure App Service](./media/app-service-web-tutorial-auth-aad/configure-auth-back-end.png)

Haga clic en **Rápido**, acepte la configuración predeterminada para crear una nueva aplicación de AD y haga clic en **Aceptar**.

En la página **Autenticación/autorización**, haga clic en **Guardar**. 

Cuando vea la notificación con el mensaje `Successfully saved the Auth Settings for <back_end_app_name> App`, actualice la página.

Haga clic de nuevo en **Azure Active Directory** y, a continuación, haga clic en **Administrar aplicación**.

En la página de administración de la aplicación de AD, copie el **identificador de aplicación** en el Bloc de notas. Este valor lo necesitará más adelante.

![API de ASP.NET Core que se ejecuta en Azure App Service](./media/app-service-web-tutorial-auth-aad/get-application-id-back-end.png)

### <a name="enable-authentication-and-authorization-for-front-end-app"></a>Habilitación de la autenticación y autorización en una aplicación de front-end

Siga los mismos pasos para la aplicación de front-end, pero omita el último paso. No es necesario el **identificador de aplicación** para la aplicación de front-end. Mantenga abierta la página **Configuración de Azure Active Directory**:

Si quiere, vaya a `http://<front_end_app_name>.azurewebsites.net`. Esta acción debería dirigirle ahora a una página de inicio de sesión. Después de iniciar la sesión, seguirá sin poder acceder a los datos desde la aplicación de back-end. El motivo es que aún es necesario hacer tres cosas:

- Conceder al front-end acceso al back-end
- Configurar App Service para devolver un token que se pueda usar
- Usar el token en el código

> [!TIP]
> Si se producen errores y vuelve a configurar la autenticación/autorización de la aplicación, es posible que no se regeneren los tokens del almacén de tokens con esta nueva configuración. Para asegurarse de que se regeneran, debe cerrar sesión en la aplicación e iniciarla de nuevo. Una manera sencilla de hacerlo es usar el explorador en modo privado y cerrarlo y abrirlo de nuevo en este modo después de cambiar la configuración en las aplicaciones.

### <a name="grant-front-end-app-access-to-back-end"></a>Conceder a la aplicación de front-end acceso al back-end

Ahora que ha habilitado la autenticación y autorización en las dos aplicaciones, cada una de ellas está respaldada por una aplicación de AD. En este paso, concederá a la aplicación de front-end permisos para acceder al back-end en nombre del usuario. (Técnicamente, asignará a la _aplicación de AD_ del front-end permisos para acceder a la _aplicación de AD_ del back-end en nombre del usuario).

En este punto, debería encontrarse en la página **Configuración de Azure Active Directory** de la aplicación de front-end. Si no es así, regrese a esa página. 

Haga clic en **Administrar permisos** > **Agregar** > **Select an API** (Seleccionar una API).

![API de ASP.NET Core que se ejecuta en Azure App Service](./media/app-service-web-tutorial-auth-aad/add-api-access-front-end.png)

En la página **Select an API** (Seleccionar una API), escriba el nombre de la aplicación de AD de su aplicación de back-end. Este nombre es, de forma predeterminada, el mismo que el de la aplicación de back-end. Selecciónelo en la lista y haga clic en **Seleccionar**.

Active la casilla junto a **Acceder a _&lt;nombre\_aplicación\_AD>_**. Haga clic en **Seleccionar** > **Listo**.

![API de ASP.NET Core que se ejecuta en Azure App Service](./media/app-service-web-tutorial-auth-aad/select-permission-front-end.png)

### <a name="configure-app-service-to-return-a-usable-access-token"></a>Configuración de App Service para devolver un token de acceso que se pueda usar

La aplicación de front-end tiene ahora los permisos necesarios. En este paso, configurará la autenticación y autorización de App Service para proporcionarle un token de acceso que se pueda usar para acceder al back end. En este paso, necesitará el identificador de aplicación del back-end, que copió en [Habilitación de la autenticación y autorización en una aplicación de back-end](#enable-authentication-and-authorization-for-back-end-app).

Inicie sesión en [Azure Resource Explorer](https://resources.azure.com). En la parte superior de la página, haga clic en **Read/Write** (Lectura/escritura) para permitir la edición de los recursos de Azure.

![API de ASP.NET Core que se ejecuta en Azure App Service](./media/app-service-web-tutorial-auth-aad/resources-enable-write.png)

En el explorador de la izquierda, haga clic en **subscriptions** >  (suscripciones) **_&lt;su\_suscripción>_** > **resourceGroups** > **myAuthResourceGroup** > **providers** (proveedores)  > **Microsoft.Web** > **sites** (sitios)  > **_\<nombre\_aplicación\_front\_end>_** > **config** > **authsettings**.

En la vista **authsettings**, haga clic en **Edit** (Editar). Establezca `additionalLoginParams` en la siguiente cadena JSON, mediante el identificador de aplicación que copió. 

```json
"additionalLoginParams": ["response_type=code id_token","resource=<back_end_application_id>"],
```

![API de ASP.NET Core que se ejecuta en Azure App Service](./media/app-service-web-tutorial-auth-aad/additional-login-params-front-end.png)

Haga clic en **PUT** para guardar la configuración.

Ahora las aplicaciones están configuradas. El front-end ahora está listo para acceder al back-end con un token de acceso apropiado.

Para información sobre cómo realizar esta configuración para otros proveedores, consulte [Refresh access tokens](app-service-authentication-how-to.md#refresh-access-tokens) (Actualización de los tokens de acceso).

## <a name="call-api-securely-from-server-code"></a>Llamada a la API de forma segura desde el código de servidor

En este paso, permitirá que el código de servidor modificado anteriormente realice llamadas autenticadas a la API de back-end.

La aplicación de front-end dispone ahora del permiso necesario y también agrega el identificador de aplicación del back-end a los parámetros de inicio de sesión. Por lo tanto, puede obtener un token de acceso para la autenticación con la aplicación de back-end. App Service suministra este token al código de servicio mediante la inserción de un encabezado `X-MS-TOKEN-AAD-ACCESS-TOKEN` en cada solicitud autenticada (consulte [Retrieve tokens in app code](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) ([Recuperación de tokens en el código de aplicación]).

> [!NOTE]
> Estos encabezados se insertan con todos los lenguajes admitidos. Se accede a ellos mediante el patrón estándar de cada lenguaje respectivo.

En el repositorio local, abra de nuevo _Controllers/TodoController.cs_. En la construcción `TodoController(TodoContext context)`, agregue el siguiente código:

```cs
public override void OnActionExecuting(ActionExecutingContext context)
{
    base.OnActionExecuting(context);

    _client.DefaultRequestHeaders.Accept.Clear();
    _client.DefaultRequestHeaders.Authorization =
        new AuthenticationHeaderValue("Bearer", Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"]);
}
```

Este código agrega el encabezado HTTP estándar `Authorization: Bearer <access_token>` a todas las llamadas de API remotas. En la canalización de ejecución de solicitudes de MVC de ASP.NET Core, `OnActionExecuting` se ejecuta justo antes del método de acción correspondiente (como `GetAll()`), de modo que cada una de las llamadas API salientes presenta ahora el token de acceso.

Guarde todos los cambios. En la ventana de terminal local, implemente los cambios en la aplicación de front-end con los siguientes comandos de Git:

```bash
git add .
git commit -m "add authorization header for server code"
git push frontend master
```

Vuelva a iniciar sesión en `http://<front_end_app_name>.azurewebsites.net`. En la página de contrato de uso de datos del usuario, haga clic en **Accept** (Aceptar).

Ahora deberá poder crear, leer, actualizar y eliminar datos de la aplicación de back-end como antes. La única diferencia ahora es que ambas aplicaciones están protegidas mediante la autenticación y autorización de App Service, incluidas las llamadas de servicio a servicio.

Felicidades. El código de servidor puede acceder ahora a los datos de back-end en nombre del usuario autenticado.

## <a name="call-api-securely-from-browser-code"></a>Llamada a la API de forma segura desde el código de explorador

En este paso, vinculará la aplicación de front-end de Angular.js a la API de back-end. De esta manera, aprenderá a recuperar el token de acceso y a realizar llamadas API con él a la aplicación de back-end.

Mientras que el código de servidor tiene acceso a los encabezados de solicitud, el código de cliente puede acceder a `GET /.auth/me` para obtener los mismos tokens de acceso (consulte [Retrieve tokens in app code](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) [Recuperación de tokens en el código de aplicación]).

> [!TIP]
> En esta sección se usan los métodos HTTP estándar para mostrar las llamadas HTTP seguras. Sin embargo, puede usar [Active Directory Authentication Library (ADAL) para JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js) para ayudar a simplificar el patrón de aplicación de Angular.js.
>

### <a name="configure-cors"></a>Configuración de CORS

En Cloud Shell, habilite CORS en la dirección URL del cliente mediante el comando [`az resource update`](/cli/azure/resource#az_resource_update). Reemplace los marcadores de posición _\<nombre\_aplicación\_back\_end>_ y _\<nombre\_aplicación\_front\_end>_.

```azurecli-interactive
az resource update --name web --resource-group myAuthResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<back_end_app_name> --set properties.cors.allowedOrigins="['https://<front_end_app_name>.azurewebsites.net']" --api-version 2015-06-01
```

Este paso no está relacionado con la autenticación y autorización. Sin embargo, son necesarios para que el explorador permita las llamadas API entre dominios desde la aplicación Angular.js. Para más información, consulte [Adición de funcionalidad CORS](app-service-web-tutorial-rest-api.md#add-cors-functionality).

### <a name="point-angularjs-app-to-back-end-api"></a>Vinculación de la aplicación Angular.js a la API de back-end

En el repositorio local, abra _wwwroot/index.html_.

En la línea 51, establezca la variable `apiEndpoint` en la dirección URL de la aplicación de back-end (`http://<back_end_app_name>.azurewebsites.net`). Reemplace _\<nombre\_aplicación\_back\_end>_ por el nombre de su aplicación en App Service.

En el repositorio local, abra _wwwroot/app/scripts/todoListSvc.js_ y observe que `apiEndpoint` está anexado a todas las llamadas API. La aplicación Angular.js ahora está llamando a las API de back-end. 

### <a name="add-access-token-to-api-calls"></a>Adición del token de acceso a las llamadas API

En _wwwroot/app/scripts/todoListSvc.js_, encima de la lista de llamadas API (encima de la línea `getItems : function(){`), agregue la siguiente función a la lista:

```javascript
setAuth: function (token) {
    $http.defaults.headers.common['Authorization'] = 'Bearer ' + token;
},
```

Esta función se invoca para establecer el encabezado `Authorization` predeterminado con el token de acceso. La invocará en el paso siguiente.

En el repositorio local, abra _wwwroot/app/scripts/app.js_ y busque el código siguiente:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
}).otherwise({ redirectTo: "/Home" });
```

Reemplace el bloque de código completo por el siguiente código:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
    resolve: {
        token: ['$http', 'todoListSvc', function ($http, todoListSvc) {
            return $http.get('/.auth/me').then(function (response) {
                todoListSvc.setAuth(response.data[0].access_token);
                return response.data[0].access_token;
            });
        }]
    },
}).otherwise({ redirectTo: "/Home" });
```

El nuevo cambio se agrega a la asignación `revolve` que invoca a `/.auth/me` y establece el token de acceso. Esto garantiza que tiene el token de acceso antes de crear una instancia del controlador `todoListCtrl`. De este modo, todas las llamadas API por el controlador incluyen el token.

### <a name="deploy-updates-and-test"></a>Implementación de actualizaciones y prueba

Guarde todos los cambios. En la ventana de terminal local, implemente los cambios en la aplicación de front-end con los siguientes comandos de Git:

```bash
git add .
git commit -m "add authorization header for Angular"
git push frontend master
```

Vaya de nuevo a `http://<front_end_app_name>.azurewebsites.net`. Ahora debería poder crear, leer, actualizar y eliminar datos de la aplicación back-end, directamente en la aplicación Angular.js.

Felicidades. El código de cliente puede acceder ahora a los datos de back-end en nombre del usuario autenticado.

## <a name="clean-up-resources"></a>Limpieza de recursos

En los pasos anteriores, creó recursos de Azure en un grupo de recursos. Si prevé que no necesitará estos recursos en el futuro, elimine el grupo de recursos ejecutando el siguiente comando en Cloud Shell:

```azurecli-interactive
az group delete --name myAuthResourceGroup
```

Este comando puede tardar varios segundos en ejecutarse.

<a name="next"></a>
## <a name="next-steps"></a>Pasos siguientes

¿Qué ha aprendido?

> [!div class="checklist"]
> * Habilitación de la autenticación y autorización integradas
> * Protección de las aplicaciones frente a las solicitudes no autenticadas
> * Uso de Azure Active Directory como proveedor de identidades
> * Acceso a una aplicación remota en nombre del usuario que inició sesión
> * Protección de llamadas de servicio a servicio con autenticación de tokens
> * Uso de tokens de acceso desde el código de servidor
> * Uso de tokens de acceso desde el código de cliente (explorador)

Vaya al siguiente tutorial para aprender a asignar un nombre DNS personalizado a una aplicación web.

> [!div class="nextstepaction"]
> [Asignar un nombre DNS personalizado a Azure Web Apps](app-service-web-tutorial-custom-domain.md)
