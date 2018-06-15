---
title: 'Tutorial: Concesión de acceso a una API web de Node.js desde una aplicación de escritorio mediante Azure Active Directory B2C | Microsoft Docs'
description: Tutorial acerca de cómo usar Active Directory B2C para proteger una API web de Node.js y llamarla desde una aplicación de escritorio de .NET.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 3/01/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 01c13b214d40fba278ce788047e2b158adc20287
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711603"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Tutorial: Concesión de acceso a una API web de Node.js desde una aplicación de escritorio mediante Azure Active Directory B2C

En este tutorial se muestra cómo llamar a un recurso de API web de Node.js protegido de Azure Active Directory (Azure AD) B2C desde una aplicación de escritorio de Windows Presentation Foundation (WPF).

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Registrar una API web en el inquilino de Azure AD B2C
> * Definir y configurar los ámbitos para una API web
> * Conceder a una aplicación permisos para la API web
> * Actualizar un código de ejemplo para usar Azure AD B2C a fin de proteger una API web

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>requisitos previos

* Realice el tutorial [Authenticate users with Azure Active Directory B2C in a desktop app](active-directory-b2c-tutorials-desktop-app.md) (Autenticación de los usuarios con Azure Active Directory B2C en una aplicación de escritorio).
* [Instale Visual Studio 2017](https://www.visualstudio.com/downloads/) con las cargas de trabajo de **desarrollo de escritorio de .NET** y de **desarrollo web y de ASP.NET**.
* Instalar [Node.js](https://nodejs.org/en/download/)

## <a name="register-web-api"></a>Registro de una API web

Los recursos de API web tienen que registrarse en el inquilino antes de que puedan aceptar y responder a [solicitudes de recursos protegidos](../active-directory/develop/active-directory-dev-glossary.md#resource-server) por [aplicaciones cliente](../active-directory/develop/active-directory-dev-glossary.md#client-application) que presenten un [token de acceso](../active-directory/develop/active-directory-dev-glossary.md#access-token) de Azure Active Directory. El registro establece el [objeto de aplicación y de entidad de servicio](../active-directory/develop/active-directory-dev-glossary.md#application-object) en el inquilino. 

Inicie sesión en [Azure Portal](https://portal.azure.com/) como administrador global del inquilino de Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Seleccione **Azure AD B2C** en la lista de servicios de Azure Portal.

2. En la configuración de B2C, haga clic en **Aplicaciones** y luego en **Agregar**.

    Para registrar la API web de ejemplo en el inquilino, utilice la siguiente configuración.
    
    ![Incorporación de una nueva API](media/active-directory-b2c-tutorials-desktop-app-webapi/web-api-registration.png)
    
    | Configuración      | Valor sugerido  | Descripción                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | My sample Node.js web API | Escriba un **Nombre** que describa su API web para los desarrolladores. |
    | **Incluir aplicación web o API web** | Sí | Seleccione **Sí** para una API web. |
    | **Permitir flujo implícito** | Sí | Seleccione **Sí**, ya que la API utiliza el [inicio de sesión con OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **URL de respuesta** | `http://localhost:5000` | Las direcciones URL de respuesta son puntos de conexión en los que Azure AD B2C devolverá los tokens que su API solicite. En este tutorial, la API web de ejemplo se ejecuta localmente (localhost) y escucha en el puerto 5000. |
    | **URI de id. de aplicación** | demoapi | El URI identifica de forma única la API en el inquilino. Esto le permite registrar varias API por inquilino. Los [ámbitos](../active-directory/develop/active-directory-dev-glossary.md#scopes) controlan el acceso al recurso de API protegido y se definen por cada URI de identificador de aplicación. |
    | **Cliente nativo** | Sin  | Como es una API web y no un cliente nativo, seleccione No. |
    
3. Haga clic en **Crear** para registrar la API.

Las API registradas aparecen en la lista de aplicaciones del inquilino de Azure AD B2C. Seleccione la API web de la lista. Se muestra el panel de propiedades de la API web.

![Propiedades de la API web](./media/active-directory-b2c-tutorials-web-api/b2c-web-api-properties.png)

Tome nota del **Id. del cliente de aplicación**. El identificador identifica la API de forma exclusiva y será necesario al configurar la API más adelante en el tutorial.

El registro de la API web con Azure AD B2C define una relación de confianza. Como la API está registrada con B2C, ya puede la API confiar en los tokens de acceso B2C que recibe de otras aplicaciones.

## <a name="define-and-configure-scopes"></a>Definición y configuración de ámbitos

Los [ámbitos](../active-directory/develop/active-directory-dev-glossary.md#scopes) proporcionan una manera de controlar el acceso a los recursos protegidos. La API web utiliza los ámbitos para implementar el control de acceso basado en el ámbito. Por ejemplo, algunos usuarios pueden tener tanto acceso de lectura como de escritura, mientras que otros usuarios pueden tener permisos de solo lectura. En este tutorial, definirá los permisos de lectura y escritura para la API web.

### <a name="define-scopes-for-the-web-api"></a>Definición de ámbitos para la API web

Las API registradas aparecen en la lista de aplicaciones del inquilino de Azure AD B2C. Seleccione la API web de la lista. Se muestra el panel de propiedades de la API web.

Haga clic en **Ámbitos publicados (versión preliminar)**.

Para configurar los ámbitos de la API, agregue las siguientes entradas. 

![Ámbitos definidos en API web](media/active-directory-b2c-tutorials-web-api/scopes-defined-in-web-api.png)

| Configuración      | Valor sugerido  | DESCRIPCIÓN                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Ámbito** | demo.read | Acceso de lectura a la API de demostración|

Haga clic en **Save**(Guardar).

Los ámbitos publicados se pueden utilizar para conceder a una aplicación cliente permiso para la API web.

### <a name="grant-app-permissions-to-web-api"></a>Concesión a una aplicación permisos para la API web

Para llamar a una API web protegida desde una aplicación, deberá conceder a su aplicación permisos para la API. En este tutorial, usará la aplicación de escritorio creada en el tutorial [Authenticate users with Azure Active Directory B2C in a desktop app](active-directory-b2c-tutorials-desktop-app.md) (Autenticación de los usuarios con Azure Active Directory B2C en una aplicación de escritorio).

1. En Azure Portal, seleccione **Azure AD B2C** en la lista de servicios y haga clic en **Aplicaciones** para ver la lista de aplicaciones registradas.

2. Seleccione **My Sample WPF App** en la lista de aplicaciones, haga clic en **Acceso de API (versión preliminar)** y, luego, en **Agregar**.

3. En la lista desplegable **Seleccionar API**, seleccione la API web registrada **My sample Node.js web API**.

4. En la lista desplegable **Seleccionar ámbitos**, seleccione los ámbitos que ha definido en el registro de la API web.

    ![Selección de ámbitos de aplicación](media/active-directory-b2c-tutorials-web-api/selecting-scopes-for-app.png)

5. Haga clic en **OK**.

**My Sample WPF App** está registrada para llamar a la API **My sample Node.js web API** protegida. Un usuario se [autentica](../active-directory/develop/active-directory-dev-glossary.md#authentication) con Azure AD B2C para usar la aplicación web de escritorio WPF. La aplicación de escritorio obtiene una [concesión de autorización](../active-directory/develop/active-directory-dev-glossary.md#authorization-grant) de Azure AD B2C para acceder a la API web protegida.

## <a name="update-web-api-code"></a>Actualización del código de API web

Ahora que se ha registrado la API web y tiene ámbitos definidos, debe configurar el código de la API web para usar el inquilino de Azure AD B2C. En este tutorial, configurará una aplicación web de ejemplo Node.js que puede descargar desde GitHub. 

[Descargue un archivo zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) o clone la aplicación web de ejemplo desde GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```
El ejemplo de API web de Node.js usa la biblioteca de Passport.js para permitir que Azure AD B2C proteja las llamadas a la API. 

### <a name="configure-the-web-api"></a>Configuración de la API web

1. Abra el archivo `index.html` en la API web de Node.js de ejemplo.
2. Configure el ejemplo con la información de registro del inquilino de Azure AD B2C. Cambie las siguientes líneas de código:

```nodejs
var tenantID = "<your-tenant-name>.onmicrosoft.com";
var clientID = "<Application ID for your Node.js Web API>";
var policyName = "B2C_1_SiUpIn";  // Sign-in / sign-up policy name
```

### <a name="configure-the-desktop-app"></a>Configuración de la aplicación de escritorio

1. Abra la solución `active-directory-b2c-wpf` desde el tutorial [Authenticate users with Azure Active Directory B2C in a desktop app](active-directory-b2c-tutorials-desktop-app.md) (Autenticación de los usuarios con Azure Active Directory B2C en una aplicación de escritorio) en Visual Studio.

## <a name="run-the-sample"></a>Ejecución del ejemplo

Ejecute la API web de Node.Js:

1. Inicie un símbolo del sistema de Node.js.
2. Cambie al directorio que contiene el ejemplo de Node.js. Por ejemplo, `cd c:\active-directory-b2c-javascript-nodejs-webapi`.
3. Ejecute los comandos siguientes:
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```
Ejecute la aplicación de escritorio:

1. Presione **F5** para ejecutar la aplicación de escritorio.
2. Inicie sesión con la dirección de correo electrónico y la contraseña que usó en el tutorial [Authenticate users with Azure Active Directory B2C in a desktop app tutorial](active-directory-b2c-tutorials-desktop-app.md) (Autenticación de los usuarios con Azure Active Directory B2C en una aplicación de escritorio).
3. Haga clic en el botón **Llamada API**. 

La aplicación de escritorio realiza una solicitud a la API web para y obtiene una respuesta con el nombre para mostrar del usuario que ha iniciado la sesión. Su aplicación de escritorio protegida llama a la API web protegida en el inquilino de Azure AD B2C.

## <a name="clean-up-resources"></a>Limpieza de recursos

Puede usar el inquilino de Azure AD B2C si tiene previsto leer otros tutoriales de Azure AD B2C. Cuando ya no sea necesario, puede [eliminar el inquilino de Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Pasos siguientes

En este artículo se explica la protección de ASP.NET Web API mediante el registro y la definición de ámbitos en Azure AD B2C. Para más información, examine los ejemplos de código de Azure AD B2C disponibles.

> [!div class="nextstepaction"]
> [Ejemplos de código de Azure AD B2C](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
