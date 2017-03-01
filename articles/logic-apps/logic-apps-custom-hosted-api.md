---
title: Llamada a API personalizadas en Azure Logic Apps | Microsoft Docs
description: Llame a sus propias API personalizadas hospedadas en Azure App Service con Azure Logic Apps
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2016
ms.author: stepsic
translationtype: Human Translation
ms.sourcegitcommit: fc509ef8b30fadb6e026f346d4adbd6ef759624a
ms.openlocfilehash: 74aae9f757f56e94b583069a1fdee7efaafe467c
ms.lasthandoff: 02/16/2017


---
# <a name="call-custom-apis-hosted-on-azure-app-service-with-azure-logic-apps"></a>Llame a API personalizadas hospedadas en Azure App Service con Azure Logic Apps

Aunque Azure Logic Apps ofrece más de 40 conectores para diversos servicios, es posible que prefiera llamar a su propia API personalizada que puede ejecutar su propio código. Una de las maneras más fáciles y escalables de hospedar sus propias API web personalizadas es usar Azure App Service. En este artículo se explica cómo llamar a cualquier API web hospedada en una aplicación de API, aplicación web o aplicación móvil de App Service.
Aprenda a [compilar API como un desencadenador o una acción en las aplicaciones lógicas](../logic-apps/logic-apps-create-api-app.md).

## <a name="deploy-your-web-app"></a>Implementación de la aplicación web

En primer lugar, debe implementar la API como una aplicación web en Azure App Service. Obtenga información acerca de la [implementación básica cuando se crea una aplicación web ASP.NET](../app-service-web/web-sites-dotnet-get-started.md). Aunque puede llamar a cualquier API desde una aplicación lógica, le recomendamos que agregue metadatos de Swagger para que la integración con las acciones de aplicación lógica sea sencilla. Obtenga información acerca de cómo [agregar metadatos de Swagger](../app-service-api/app-service-api-dotnet-get-started.md#use-swagger-api-metadata-and-ui).

### <a name="api-settings"></a>API settings

Para que el diseñador de aplicaciones lógicas pueda analizar sus datos de Swagger, debe habilitar CORS y establecer las propiedades de la definición de API de la aplicación web. 

1.    En Azure Portal, seleccione la aplicación web.
2.    En la hoja que se abre, busque **API** y seleccione **Definición de la API**. Establezca la **Ubicación de la definición de la API** en dirección URL del archivo swagger.json.

    Normalmente, esta dirección URL es https://{name}.azurewebsites.net/swagger/docs/v1).

3.    Para permitir las solicitudes desde el diseñador de aplicaciones lógicas, en **API**, seleccione **CORS** y establezca una directiva CORS para '*'.

## <a name="call-into-your-custom-api"></a>Llamada a la API personalizada

Si ha establecido las propiedades de la definición de la API y de CORS, podrá agregar fácilmente las acciones de API personalizadas a su flujo de trabajo en el portal de Logic Apps. En el diseñador de aplicaciones lógicas, puede examinar los sitios web de suscripción para mostrar los sitios web que tengan definida una dirección URL de Swagger. También puede usar la acción HTTP + Swagger para apuntar a Swagger y enumerar las acciones y las entradas disponibles. Por último, siempre puede crear una solicitud mediante la acción HTTP para llamar a cualquier API, incluso aquellas que no tengan ni expongan un documento de Swagger.

Existen distintas formas de proteger su API:

*    No se requiere ningún cambio de código. Puede usar Azure Active Directory para proteger su API sin necesidad de cambiar el código ni volver a implementarlo.
*    En el código de la API, aplique la autenticación básica, la autenticación de certificados o la autenticación de Azure Active Directory.

## <a name="secure-calls-to-your-api-without-changing-code"></a>Llamadas seguras a la API sin cambiar el código

En esta sección, creará dos aplicaciones de Azure Active Directory: una para la aplicación lógica y otra para la aplicación web. Autentique las llamadas a la aplicación web mediante la entidad de servicio (identificador de cliente y secreto) asociada a la aplicación de Azure Active Directory de la aplicación lógica. Por último, incluya los identificadores de la aplicación en la definición de la aplicación lógica.

### <a name="part-1-set-up-an-application-identity-for-your-logic-app"></a>Parte 1: Configuración de una identidad de aplicación para la aplicación lógica

La aplicación lógica usa esta identidad de aplicación para autenticarse en Azure Active Directory. Solo tiene que establecer esta identidad una vez para el directorio. Por ejemplo, puede usar la misma identidad para todas las aplicaciones lógicas, aunque también puede crear identidades únicas para cada aplicación lógica. Puede configurar estas identidades en Azure Portal o usar PowerShell.

#### <a name="create-the-application-identity-in-the-azure-classic-portal"></a>Creación de la identidad de aplicación en el Portal de Azure clásico

1. En el Portal de Azure clásico, vaya a su directorio de [Azure Active Directory](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory). 
2. Seleccione el directorio que use para la aplicación web.
3. Elija la pestaña **Aplicaciones**. Seleccione **Agregar** en la barra de comandos de la parte inferior de la página.
5. Asigne un nombre a la identidad y haga clic en la flecha Siguiente.
6. En **Propiedades de la aplicación**, indique una cadena única con formato de dominio y haga clic en la marca de verificación.
7. Elija la pestaña **Configurar**. Vaya a **Id. de cliente** y copie el identificador de cliente que usará en la aplicación lógica.
8. En **Claves**, abra la lista **Seleccionar duración** y seleccione la duración de la clave.
9. En la parte inferior de la página, haga clic en **Guardar**. Quizás tenga que esperar unos segundos.
10. Asegúrese de copiar la clave que aparece ahora en **Claves**, para poder usarla en la aplicación lógica.

#### <a name="create-the-application-identity-using-powershell"></a>Creación de la identidad de aplicación mediante PowerShell

1. `Switch-AzureMode AzureResourceManager`
2. `Add-AzureAccount`
3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://someranddomain.tld" -IdentifierUris "http://someranddomain.tld" -Password "Pass@word1!"`
4. No olvide copiar el **Identificador de inquilino**, el **Identificador de la aplicación** y la contraseña usada.

### <a name="part-2-protect-your-web-app-with-an-azure-active-directory-app-identity"></a>Parte 2: Protección de la aplicación web con una identidad de aplicación de Azure Active Directory

Si ya implementó la aplicación web, puede habilitar la autorización en Azure Portal. También puede incluir la habilitación de la autorización como parte de la implementación de Azure Resource Manager.

#### <a name="enable-authorization-in-the-azure-portal"></a>Habilitación de la autorización en Azure Portal

1. Busque y seleccione la aplicación web. Haga clic en **Configuración** y elija **Autenticación/autorización**.
2. En **Autenticación de App Service**, elija **Activar** la autenticación y seleccione **Guardar**.

En este momento, se crea una aplicación automáticamente. Como necesita el identificador de cliente de la aplicación en la parte 3, haga lo siguiente:

1. En el Portal de Azure clásico, vaya a su directorio de [Azure Active Directory](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory).
2.    Seleccione el directorio.
3. En el cuadro de búsqueda, busque la aplicación.
4. En la lista, seleccione la aplicación.
5. Elija la pestaña **Configurar** donde verá el **Id. de cliente**.

#### <a name="deploy-your-web-app-using-an-azure-resource-manager-template"></a>Implementación de la aplicación web mediante una plantilla de Azure Resource Manager

En primer lugar, debe crear para la aplicación web una aplicación que sea diferente de la aplicación que usa para la aplicación lógica. Para empezar, siga los pasos de la parte 1 pero, en **HomePage** e **IdentifierUris**, use la dirección https://**dirección URL** real de la aplicación web.

> [!NOTE]
> Cuando se crea la aplicación para la aplicación web, debe usar el [Portal de Azure clásico](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory). El cmdlet de PowerShell no configura los permisos necesarios para iniciar la sesión de los usuarios en un sitio web.

Una vez que tenga tanto el identificador de cliente como el de inquilino, incluya esta parte como un subrecurso de la aplicación web en la plantilla de implementación:

```
"resources": [
    {
        "apiVersion": "2015-08-01",
        "name": "web",
        "type": "config",
        "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
        "properties": {
            "siteAuthEnabled": true,
            "siteAuthSettings": {
              "clientId": "<<clientID>>",
              "issuer": "https://sts.windows.net/<<tenantID>>/",
            }
        }
    }
]
```

Para ejecutar automáticamente una implementación conjunta de una aplicación web vacía y una aplicación lógica que usen Azure Active Directory, haga clic en el botón **Implementación en Azure**:

[![Implementación en Azure](media/logic-apps-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

Para ver la plantilla completa, consulte la página sobre las [llamadas de aplicación lógica a una API personalizada hospedada en App Service y protegida mediante Azure Active Directory](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-custom-api/azuredeploy.json).

### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>Parte 3: Relleno de la sección de autorización en la aplicación lógica

En la sección **Autorización** de la acción **HTTP**:

`{"tenant":"<<tenantId>>", "audience":"<<clientID from Part 2>>", "clientId":"<<clientID from Part 1>>","secret": "<<Password or Key from Part 1>>","type":"ActiveDirectoryOAuth" }`

| Elemento | Descripción |
| ------- | ----------- |
| type |Tipo de autenticación. En autenticación ActiveDirectoryOAuth, el valor es `ActiveDirectoryOAuth`. |
| tenant |Identificador de inquilino que se usa para identificar al inquilino de AD. |
| audience |Obligatorio. El recurso al que se está conectando. |
| clientID |Identificador de cliente para la aplicación de Azure AD. |
| secret |Obligatorio. Secreto del cliente que solicita el token. |

La plantilla anterior ya tiene esta sección de autorización configurada pero, si va a crear la aplicación lógica directamente, debe incluir la sección de autorización completa.

## <a name="secure-your-api-in-code"></a>Protección de la API en el código

### <a name="certificate-authentication"></a>Autenticación de certificados

Puede usar certificados de cliente para validar las solicitudes entrantes a la aplicación web. Consulte [Configuración de la autenticación mutua de TLS para una aplicación web](../app-service-web/app-service-web-configure-tls-mutual-auth.md) para ver cómo configurar su código.

En la sección **Autorización**, debe proporcionar: 

`{"type": "clientcertificate","password": "test","pfx": "long-pfx-key"}`

| Elemento | Descripción |
| ------- | ----------- |
| type |Obligatorio. Tipo de autenticación. Para los certificados de cliente SSL, el valor debe ser `ClientCertificate`. |
| pfx |Obligatorio. Contenido codificado en base&64; del archivo PFX. |
| contraseña |Obligatorio. Contraseña de acceso al archivo PFX. |

### <a name="basic-authentication"></a>Autenticación básica

Para validar las solicitudes entrantes, puede usar la autenticación básica (por ejemplo, nombre de usuario y contraseña). La autenticación básica es un patrón común, y puede usarla en cualquier lenguaje que use para compilar la aplicación.

En la sección **Autorización**, debe proporcionar:

`{"type": "basic","username": "test","password": "test"}`.

| Elemento | Descripción |
| --- | --- |
| type |Obligatorio. Tipo de autenticación. En autenticación básica, el valor debe ser `Basic`. |
| nombre de usuario |Obligatorio. Nombre de usuario para autenticar. |
| contraseña |Obligatorio. Contraseña para autenticar. |

### <a name="handle-azure-active-directory-authentication-in-code"></a>Control de la autenticación de Azure Active Directory en el código

De forma predeterminada, la autenticación de Azure Active Directory que se habilita en Azure Portal no lleva a cabo una autorización específica. Por ejemplo, esta autenticación no bloquea la API para una aplicación o un usuario específicos, sino solo para un inquilino determinado.

Para restringir la API a la aplicación lógica, por ejemplo, en el código, extraiga el encabezado que tiene el código JWT. Compruebe la identidad del llamador y rechace las solicitudes que no coinciden.

Además, para implementar esta autenticación completamente en su propio código y no utilizar la característica de Azure Portal, consulte [Autenticación con Active Directory local en aplicaciones de Azure](../app-service-web/web-sites-authentication-authorization.md).
Para crear una identidad de aplicación para la aplicación lógica y usarla para llamar a la API, siga los pasos anteriores.
