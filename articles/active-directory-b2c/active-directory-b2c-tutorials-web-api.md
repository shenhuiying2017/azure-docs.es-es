---
title: 'Tutorial: Uso de Azure Active Directory B2C para proteger ASP.NET Web API'
description: "Tutorial sobre cómo usar Active Directory B2C para proteger ASP.NET Web API y llamarlo desde una aplicación web para ASP.NET."
services: active-directory-b2c
author: PatAltimore
ms.author: patricka
ms.reviewer: saraford
ms.date: 1/23/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory-b2c
ms.openlocfilehash: 0e9f324cec0d242c013a461d8580abd4faa97c8d
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-use-azure-active-directory-b2c-to-protect-an-aspnet-web-api"></a>Tutorial: Uso de Azure Active Directory B2C para proteger ASP.NET Web API

Este tutorial muestra cómo llamar a un recurso de API web protegido de Azure Active Directory (Azure AD) B2C desde una aplicación web para ASP.NET.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Registrar una API web en el inquilino de Azure AD B2C
> * Definir y configurar los ámbitos para una API web
> * Conceder a una aplicación permisos para la API web
> * Actualizar un código de ejemplo para usar Azure AD B2C a fin de proteger una API web

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>requisitos previos

* Completar el [Tutorial: Uso de Azure Active Directory B2C para la autenticación de usuarios en una aplicación web para ASP.NET](active-directory-b2c-tutorials-web-app.md).
* Instalar [Visual Studio 2017](https://www.visualstudio.com/downloads/) con la carga de trabajo de **ASP.NET y desarrollo web**.

## <a name="register-web-api"></a>Registro de una API web

Los recursos de API web tienen que registrarse en el inquilino antes de que puedan aceptar y responder a [solicitudes de recursos protegidos](../active-directory/develop/active-directory-dev-glossary.md#resource-server) por [aplicaciones cliente](../active-directory/develop/active-directory-dev-glossary.md#client-application) que presenten un [token de acceso](../active-directory/develop/active-directory-dev-glossary.md#access-token) de Azure Active Directory. El registro establece el [objeto de aplicación y de entidad de servicio](../active-directory/develop/active-directory-dev-glossary.md#application-object) en el inquilino. 

Inicie sesión en [Azure Portal](https://portal.azure.com/) como administrador global del inquilino de Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Seleccione **Azure AD B2C** en la lista de servicios de Azure Portal.

2. En la configuración de B2C, haga clic en **Aplicaciones** y luego en **Agregar**.

    Para registrar la API web de ejemplo en el inquilino, utilice la siguiente configuración.
    
    ![Incorporación de una nueva API](media/active-directory-b2c-tutorials-web-api/web-api-registration.png)
    
    | Configuración      | Valor sugerido  | Descripción                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | Mi API web de ejemplo | Escriba un **Nombre** que describa su API web para los desarrolladores. |
    | **Incluir aplicación web o API web** | Sí | Seleccione **Sí** para una API web. |
    | **Permitir flujo implícito** | Sí | Seleccione **Sí**, ya que la API utiliza el [inicio de sesión con OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **URL de respuesta** | `https://localhost:44332` | Las direcciones URL de respuesta son puntos de conexión en los que Azure AD B2C devolverá los tokens que su API solicite. En este tutorial, la API web de ejemplo se ejecuta localmente (localhost) y escucha en el puerto 44332. |
    | **URI de id. de aplicación** | myAPISample | El URI identifica de forma única la API en el inquilino. Esto le permite registrar varias API por inquilino. Los [ámbitos](../active-directory/develop/active-directory-dev-glossary.md#scopes) controlan el acceso al recurso de API protegido y se definen por cada URI de identificador de aplicación. |
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
| **Ámbito** | Hello.Read | Acceso de lectura a Hello |
| **Ámbito** | Hello.Write | Acceso de escritura a Hello |

Los ámbitos publicados se pueden utilizar para conceder a una aplicación cliente permiso para la API web.

### <a name="grant-app-permissions-to-web-api"></a>Concesión a una aplicación permisos para la API web

Para llamar a una API web protegida desde una aplicación, deberá conceder a su aplicación permisos para la API. 

1. En Azure Portal, seleccione **Azure AD B2C** en la lista de servicios y haga clic en **Aplicaciones** para ver la lista de aplicaciones registradas.

2. Seleccione **Mi aplicación web de ejemplo** en la lista de aplicaciones, haga clic en **Acceso de API (versión preliminar)** y luego en **Agregar**.

3. En la lista desplegable **Seleccionar API**, seleccione la API web registrada **Mi API web de ejemplo**.

4. En la lista desplegable **Seleccionar ámbitos**, seleccione los ámbitos que ha definido en el registro de la API web.

    ![Selección de ámbitos de aplicación](media/active-directory-b2c-tutorials-web-api/selecting-scopes-for-app.png)

5. Haga clic en **OK**.

**Mi aplicación web de ejemplo** está registrada para llamar a **Mi API web de ejemplo** protegida. Un usuario [se autentica](../active-directory/develop/active-directory-dev-glossary.md#authentication) con Azure AD B2C para utilizar la aplicación web. La aplicación web obtiene una [concesión de autorización](../active-directory/develop/active-directory-dev-glossary.md#authorization-grant) de Azure AD B2C para tener acceso a la API web protegida.

## <a name="update-web-api-code"></a>Actualización del código de API web

Ahora que se ha registrado la API web y tiene ámbitos definidos, debe configurar el código de la API web para usar el inquilino de Azure AD B2C. En este tutorial, configurará una API web de ejemplo. 

La API web de ejemplo se incluye en el proyecto que ha descargado en el tutorial de requisitos previos: [Tutorial: Uso de Azure Active Directory B2C para la autenticación de usuarios en una aplicación web para ASP.NET](active-directory-b2c-tutorials-web-app.md). Si no ha completado el tutorial de requisitos previos, complételo antes de continuar.

Hay dos proyectos en la solución de ejemplo:

**Aplicación de ejemplo de aplicación web (TaskWebApp)**: aplicación web para crear y editar una lista de tareas. La aplicación web utiliza la directiva de **registro o de inicio de sesión** para registrar a los usuarios o iniciar sesión con ellos mediante una dirección de correo electrónico.

**Aplicación de ejemplo de API web (TaskService)**: API web que admite la funcionalidad de creación, lectura, actualización y eliminación de la lista de tareas. Azure AD B2C protege a la API web y la aplicación web la llama.

La aplicación web y la API web de ejemplo definen los valores de configuración como configuración de la aplicación en el archivo Web.config de cada proyecto.

Abra la solución **B2C-WebAPI-DotNet** en Visual Studio.

### <a name="configure-the-web-app"></a>Configuración de la aplicación web

1. Abra **Web.config** en el proyecto **TaskWebApp**.

2. Para ejecutar la API localmente, utilice la configuración de localhost para **api:TaskServiceUrl**. Cambie el archivo Web.config de la manera siguiente: 

    ```C#
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

3. Configure el identificador URI de la API. Es el URI que utiliza la aplicación web para realizar la solicitud de API. Configure además los permisos solicitados.

```C#
<add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/myAPISample/" />
<add key="api:ReadScope" value="Hello.Read" />
<add key="api:WriteScope" value="Hello.Write" />
```

### <a name="configure-the-web-api"></a>Configuración de la API web

1. Abra **Web.config** en el proyecto **TaskService**.

2. Configure la API para usar el inquilino.

    ```C#
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

3. Establezca el identificador de cliente en el identificador de aplicación registrado para la API.

    ```C#
    <add key="ida:ClientId" value="<The Application ID for your web API obtained from the Azure portal>"/>
    ```

4. Actualice la configuración de directiva con el nombre generado al crear la directiva de registro y de inicio de sesión.

    ```C#
    <add key="ida:SignUpSignInPolicyId" value="b2c_1_SiUpIn" />
    ```

5. Defina la configuración de los ámbitos para que coincida con lo que ha creado en el portal.

    ```C#
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

## <a name="run-the-sample-web-app-and-web-api"></a>Ejecución de la aplicación web de ejemplo y la API web

Debe ejecutar tanto el proyecto **TaskWebApp** como el de **TaskService**. 

1. En el Explorador de soluciones, haga clic con el botón derecho en la solución y seleccione **Establecer proyectos de inicio**. 
2. Seleccione el botón de radio **Proyectos de inicio múltiples**.
3. Cambie la **Acción** de ambos proyectos a **Iniciar**.
4. Haga clic en Aceptar para guardar la configuración.
5. Presione **F5** para ejecutar las dos aplicaciones. Cada aplicación se abre en su propia pestaña del explorador. `https://localhost:44316/` es la aplicación web.
    `https://localhost:44332/` es la API web.

6. En la aplicación web, haga clic en el vínculo de registro o de inicio de sesión en el mensaje emergente del menú para registrarse en la aplicación web. Utilice la cuenta que ha creado en el [tutorial de aplicación web](active-directory-b2c-tutorials-web-app.md). 
7. Una vez realizado el inicio de sesión, haga clic en el vínculo **Lista de tareas pendientes** y cree un elemento de la lista de tareas pendientes.

Al crear un elemento de la lista de tareas pendientes, la aplicación web realiza una solicitud a la API web para generar el elemento de la lista de tareas pendientes. Su aplicación web protegida está llamando a la API web protegida en el inquilino de Azure AD B2C.

## <a name="clean-up-resources"></a>Limpieza de recursos

Puede usar el inquilino de Azure AD B2C si tiene previsto leer otros tutoriales de Azure AD B2C. Cuando ya no sea necesario, puede [eliminar el inquilino de Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>pasos siguientes

En este artículo se explica la protección de ASP.NET Web API mediante el registro y la definición de ámbitos en Azure AD B2C. Para más información sobre el desarrollo de este escenario, incluidos los tutoriales de código, continúe en el siguiente tutorial.

> [!div class="nextstepaction"]
> [Creación de una aplicación web de ASP.NET con procesos de registro e inicio de sesión, edición de perfil y restablecimiento de contraseña de Azure Active Directory B2C](active-directory-b2c-devquickstarts-web-dotnet-susi.md)