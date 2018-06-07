---
title: 'Tutorial: Conceder acceso a una API web ASP.NET Core desde una aplicación de una sola página mediante Azure Active Directory B2C | Microsoft Docs'
description: Tutorial sobre cómo usar Active Directory B2C para proteger una API web de ASP.NET e invocarla desde una aplicación de una sola página.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 3/02/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 5b99f60c1bd81b77a5fc2be5575f65fc63eb0c11
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34711100"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-app-using-azure-active-directory-b2c"></a>Tutorial: Conceder acceso a una API web de ASP.NET Core desde una aplicación de una sola página mediante Azure Active Directory B2C

En este tutorial se muestra cómo llamar a un recurso de API web ASP.NET Core protegido con Azure Active Directory (Azure AD) B2C desde una aplicación de una sola página.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Registrar una API web en el inquilino de Azure AD B2C
> * Definir y configurar los ámbitos para una API web
> * Conceder a una aplicación permisos para la API web
> * Actualizar un código de ejemplo para usar Azure AD B2C a fin de proteger una API web

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>requisitos previos

* Realice el tutorial [Authenticate users with Azure Active Directory B2C in a single page application](active-directory-b2c-tutorials-spa.md) (Autenticación de los usuarios con Azure Active Directory B2C en una aplicación de una sola página).
* Instalar [Visual Studio 2017](https://www.visualstudio.com/downloads/) con la carga de trabajo de **ASP.NET y desarrollo web**.
* [SDK de .NET Core 2.0.0](https://www.microsoft.com/net/core) o posterior
* Instalar [Node.js](https://nodejs.org/en/download/)

## <a name="register-web-api"></a>Registro de una API web

Los recursos de API web tienen que registrarse en el inquilino antes de que puedan aceptar y responder a [solicitudes de recursos protegidos](../active-directory/develop/active-directory-dev-glossary.md#resource-server) por [aplicaciones cliente](../active-directory/develop/active-directory-dev-glossary.md#client-application) que presenten un [token de acceso](../active-directory/develop/active-directory-dev-glossary.md#access-token) de Azure Active Directory. El registro establece el [objeto de aplicación y de entidad de servicio](../active-directory/develop/active-directory-dev-glossary.md#application-object) en el inquilino. 

Inicie sesión en [Azure Portal](https://portal.azure.com/) como administrador global del inquilino de Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Seleccione **Azure AD B2C** en la lista de servicios de Azure Portal.

2. En la configuración de B2C, haga clic en **Aplicaciones** y luego en **Agregar**.

    Para registrar la API web de ejemplo en el inquilino, utilice la siguiente configuración.
    
    ![Incorporación de una nueva API](media/active-directory-b2c-tutorials-spa-webapi/web-api-registration.png)
    
    | Configuración      | Valor sugerido  | Descripción                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | Hello Core API | Escriba un **Nombre** que describa su API web para los desarrolladores. |
    | **Incluir aplicación web o API web** | Sí | Seleccione **Sí** para una API web. |
    | **Permitir flujo implícito** | Sí | Seleccione **Sí**, ya que la API utiliza el [inicio de sesión con OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **URL de respuesta** | `http://localhost:44332` | Las direcciones URL de respuesta son puntos de conexión en los que Azure AD B2C devolverá los tokens que su API solicite. En este tutorial, la API web de ejemplo se ejecuta localmente (localhost) y escucha en el puerto 5000. |
    | **URI de id. de aplicación** | HelloCoreAPI | El URI identifica de forma única la API en el inquilino. Esto le permite registrar varias API por inquilino. Los [ámbitos](../active-directory/develop/active-directory-dev-glossary.md#scopes) controlan el acceso al recurso de API protegido y se definen por cada URI de identificador de aplicación. |
    | **Cliente nativo** | Sin  | Como es una API web y no un cliente nativo, seleccione No. |
    
3. Haga clic en **Crear** para registrar la API.

Las API registradas aparecen en la lista de aplicaciones del inquilino de Azure AD B2C. Seleccione la API web de la lista. Se muestra el panel de propiedades de la API web.

![Propiedades de la API web](./media/active-directory-b2c-tutorials-spa-webapi/b2c-web-api-properties.png)

Tome nota del **Id. del cliente de aplicación**. El identificador identifica la API de forma exclusiva y será necesario al configurar la API más adelante en el tutorial.

El registro de la API web con Azure AD B2C define una relación de confianza. Como la API está registrada con B2C, ya puede la API confiar en los tokens de acceso B2C que recibe de otras aplicaciones.

## <a name="define-and-configure-scopes"></a>Definición y configuración de ámbitos

Los [ámbitos](../active-directory/develop/active-directory-dev-glossary.md#scopes) proporcionan una manera de controlar el acceso a los recursos protegidos. La API web utiliza los ámbitos para implementar el control de acceso basado en el ámbito. Por ejemplo, algunos usuarios pueden tener tanto acceso de lectura como de escritura, mientras que otros usuarios pueden tener permisos de solo lectura. En este tutorial, definirá los permisos de lectura para la API web.

### <a name="define-scopes-for-the-web-api"></a>Definición de ámbitos para la API web

Las API registradas aparecen en la lista de aplicaciones del inquilino de Azure AD B2C. Seleccione la API web de la lista. Se muestra el panel de propiedades de la API web.

Haga clic en **Ámbitos publicados (versión preliminar)**.

Para configurar los ámbitos de la API, agregue las siguientes entradas. 

![Ámbitos definidos en API web](media/active-directory-b2c-tutorials-spa-webapi/scopes-web-api.png)

| Configuración      | Valor sugerido  | DESCRIPCIÓN                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Ámbito** | demo.read | Acceso de lectura a la API de demostración |

Haga clic en **Save**(Guardar).

Los ámbitos publicados se pueden utilizar para conceder a una aplicación cliente permiso para la API web.

### <a name="grant-app-permissions-to-web-api"></a>Concesión a una aplicación permisos para la API web

Para llamar a una API web protegida desde una aplicación, deberá conceder a su aplicación permisos para la API. En este tutorial, usará la aplicación de una sola página creada en [Authenticate users with Azure Active Directory B2C in a single page application (JavaScript)](active-directory-b2c-tutorials-spa.md) (Autenticación de los usuarios con Azure Active Directory B2C en una aplicación de una sola página [JavaScript]).

1. En Azure Portal, seleccione **Azure AD B2C** en la lista de servicios y haga clic en **Aplicaciones** para ver la lista de aplicaciones registradas.

2. Seleccione **My sample single page app** (Mi aplicación de una página de ejemplo) en la lista de aplicaciones, haga clic en **Acceso de API (versión preliminar)** y luego en **Agregar**.

3. En la lista desplegable **Seleccionar API**, seleccione la API web registrada **Hello Core API**.

4. En la lista desplegable **Seleccionar ámbitos**, seleccione los ámbitos que ha definido en el registro de la API web.

    ![Selección de ámbitos de aplicación](media/active-directory-b2c-tutorials-spa-webapi/selecting-scopes-for-app.png)

5. Haga clic en **OK**.

Su **aplicación de una sola página de ejemplo** está registrada para llamar a la API **Hello Core API** protegida. Un usuario se [autentica](../active-directory/develop/active-directory-dev-glossary.md#authentication) con Azure AD B2C para usar la aplicación web de escritorio WPF. La aplicación de escritorio obtiene una [concesión de autorización](../active-directory/develop/active-directory-dev-glossary.md#authorization-grant) de Azure AD B2C para acceder a la API web protegida.

## <a name="update-code"></a>Actualización del código

Ahora que se ha registrado la API web y tiene ámbitos definidos, debe configurar el código de la API web para usar el inquilino de Azure AD B2C. En este tutorial, configurará una aplicación web de ejemplo .NET Core que puede descargar desde GitHub. 

[Descargue un archivo zip](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) o clone la aplicación web de ejemplo desde GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Configuración de la API web

1. Abra la solución **B2C-WebAPI.sln** en Visual Studio.

2. Abra el archivo **appsettings.json**. Actualice los valores siguientes para configurar la API web para usar su inquilino:

    ```javascript
    "AzureAdB2C": 
      {
        "Tenant": "<your tenant name>.onmicrosoft.com", 
        "ClientId": "<The Application ID for your web API obtained from the Azure portal>",
        "Policy": "<Your sign up sign in policy e.g. B2C_1_SiUpIn>",
        "ScopeRead": "demo.read"  
      },
    ```

#### <a name="enable-cors"></a>Habilitación de CORS

Para permitir que la aplicación de una sola página llame a la API web ASP.NET Core, debe habilitar [CORS](https://docs.microsoft.com/aspnet/core/security/cors).

1. En **Startup.cs**, agregue CORS al método `ConfigureServices()`.

    ```C#
    public void ConfigureServices(IServiceCollection services) {
      services.AddCors();
    ```

2. En **Startup.cs**, configure CORS en el método `Configure()`.

    ```C#
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory) {
      app.UseCors(builder =>
        builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

3. Abra el archivo **launchSettings.json** en **Propiedades**, busque el valor *applicationURL* y grabe el valor para utilizarlo en la sección siguiente.

### <a name="configure-the-single-page-app"></a>Configuración de la aplicación de una sola página

La aplicación de una sola página usa Azure AD B2C para realizar la suscripción de los usuarios e iniciar su sesión, y llama a la API web de ASP.NET Core protegida. Deberá actualizar la aplicación de una sola página para que llame a la API web .NET Core.
Para cambiar la configuración de la aplicación:

1. Abra el archivo `index.html` en la aplicación de una sola página de Node.js de ejemplo.
2. Configure el ejemplo con la información de registro del inquilino de Azure AD B2C. En el siguiente código, agregue el nombre de inquilino a **b2cScopes** y cambie el valor de **webApi** por el valor de *applicationURL* que grabó anteriormente:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var applicationConfig = {
        clientID: '<Application ID for your SPA obtained from portal app registration>',
        authority: "https://login.microsoftonline.com/tfp/<your-tenant-name>.onmicrosoft.com/B2C_1_SiUpIn",
        b2cScopes: ["https://<Your tenant name>.onmicrosoft.com/HelloCoreAPI/demo.read"],
        webApi: 'http://localhost:64791/api/values',
    };
    ```

## <a name="run-the-spa-app-and-web-api"></a>Ejecución de la aplicación SPA y la API web

Deberá ejecutar la aplicación de una sola página de Node.js y la API web .NET Core.

### <a name="run-the-aspnet-core-web-api"></a>Ejecución de la API web ASP.NET Core 

Presione **F5** para depurar la solución **B2C-WebAPI.sln** en Visual Studio.

Cuando se inicia el proyecto, se muestra una página web en el explorador predeterminado que anuncia que la API web está disponible para las solicitudes.

### <a name="run-the-single-page-app"></a>Ejecución de la aplicación de una sola página

1. Inicie un símbolo del sistema de Node.js.
2. Cambie al directorio que contiene el ejemplo de Node.js. Por ejemplo, `cd c:\active-directory-b2c-javascript-msal-singlepageapp`.
3. Ejecute los comandos siguientes:
    ```
    npm install && npm update
    node server.js
    ```

    La ventana de la consola muestra el número de puerto de donde se hospeda la aplicación.
    
    ```
    Listening on port 6420...
    ```

4. Use un explorador para ir a la dirección `http://localhost:6420` y ver la aplicación.
5. Inicie sesión con la dirección de correo electrónico y la contraseña que usó en [Authenticate users with Azure Active Directory B2C in a single page application (JavaScript)](active-directory-b2c-tutorials-spa.md) (Autenticación de los usuarios con Azure Active Directory B2C en una aplicación de una sola página [JavaScript]).
6. Haga clic en el botón **Llamada API**.

Después de registrarse o iniciar sesión con una cuenta de usuario, el ejemplo llama a la API web protegida y devuelve un resultado.

## <a name="clean-up-resources"></a>Limpieza de recursos

Puede usar el inquilino de Azure AD B2C si tiene previsto leer otros tutoriales de Azure AD B2C. Cuando ya no sea necesario, puede [eliminar el inquilino de Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Pasos siguientes

En este artículo se explica cómo proteger una API web mediante el registro y la definición de ámbitos en Azure AD B2C. Para más información, examine los ejemplos de código de Azure AD B2C disponibles.

> [!div class="nextstepaction"]
> [Ejemplos de código de Azure AD B2C](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
