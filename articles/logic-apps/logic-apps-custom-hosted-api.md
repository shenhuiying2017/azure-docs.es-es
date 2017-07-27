---
title: "Implementación, llamada y autenticación de API web y API de REST para Azure Logic Apps | Microsoft Docs"
description: Implemente, autentique y llame a API web y API de REST en flujos de trabajo para integraciones de sistemas con Azure Logic Apps
keywords: API web, API de REST, conectores, flujos de trabajo, integraciones de sistemas, autenticar
services: logic-apps
author: stepsic-microsoft-com
manager: anneta
editor: 
documentationcenter: 
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: LADocs; stepsic
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: 88c62d5ab046d8cf4bce5d23b776e517bb0e1d87
ms.contentlocale: es-es
ms.lasthandoff: 06/08/2017

---

# <a name="deploy-call-and-authenticate-custom-apis-as-connectors-for-logic-apps"></a>Implementación, llamada y autenticación de API personalizadas como conectores para aplicaciones lógicas

Después de [crear API personalizadas](./logic-apps-create-api-app.md) que proporcionan acciones o desencadenadores para usarlos en flujos de trabajo de aplicaciones lógicas, debe implementar las API antes de poder llamarlas. Aunque se puede llamar a cualquier API desde una aplicación lógica, para lograr una experiencia óptima, agregue [metadatos de Swagger](http://swagger.io/specification/) que describan las operaciones y los parámetros de la API. Este archivo de Swagger ayuda a que su API funcione mejor y se integre con más facilidad con aplicaciones lógicas.

Puede implementar las API como [aplicaciones web](../app-service-web/app-service-web-overview.md), pero considere la posibilidad de implementarlas como [aplicaciones de API](../app-service-api/app-service-api-apps-why-best-platform.md), que pueden facilitarle su trabajo al compilar, hospedar y consumir API en la nube y en el entorno local. No tiene que cambiar ningún código en las API; basta con implementar el código en una aplicación de API. Puede hospedar las API en [Azure App Service](../app-service/app-service-value-prop-what-is.md), una oferta de plataforma como servicio (PaaS) que proporciona una de las maneras más adecuadas, fáciles y escalables de hospedar API.

Para autenticar llamadas desde aplicaciones lógicas a las API, puede configurar Azure Active Directory en Azure Portal para no tener que actualizar el código. También puede requerir y aplicar la autenticación a través del código de la API.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Implementación de la API como aplicación web o aplicación de API

Para poder llamar a su API personalizada desde una aplicación lógica, impleméntela como aplicación web o aplicación de API en Azure App Service. Además, para mejorar la legibilidad del documento de Swagger en el Diseñador de aplicación lógica, establezca las propiedades de definición de la API y active [Uso compartido de recursos entre orígenes (CORS)](../app-service-api/app-service-api-cors-consume-javascript.md#corsconfig) para su aplicación web o de API.

1. En Azure Portal, seleccione la aplicación web o de API.

2. En la hoja que se abre, en **API**, elija **Definición de la API**. Establezca **Ubicación de la definición de la API** en la dirección URL del archivo swagger.json.

   Por lo general, la dirección URL aparece en este formato: `https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Vincular al archivo de Swagger para la API personalizada](media/logic-apps-custom-hosted-api/custom-api-swagger-url.png)

3. En **API**, elija **CORS**. Establezca la directiva CORS para **Orígenes permitidos** en  **"*"** (permitir todos).

   Esta configuración permite solicitudes del Diseñador de aplicación lógica.

   ![Permitir solicitudes del Diseñador de aplicación lógica a la API personalizada](media/logic-apps-custom-hosted-api/custom-api-cors.png)

Para obtener más información, consulte estos artículos:

* [Incorporación de metadatos de Swagger para las API web de ASP.NET](../app-service-api/app-service-api-dotnet-get-started.md#use-swagger-api-metadata-and-ui)
* [Implementación de las API web de ASP.NET en Azure App Service](../app-service-api/app-service-api-dotnet-get-started.md)

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Llamada a la API personalizada desde flujos de trabajo de aplicación lógica

Después de configurar las propiedades de definición de la API y de CORS, los desencadenadores y las acciones de la API personalizada deberían estar disponibles para incluirlos en el flujo de trabajo de la aplicación lógica. 

*  Para ver los sitios web que tengan direcciones URL de Swagger, puede examinar los sitios web de su suscripción en el Diseñador de aplicación lógica.

*  Para ver las acciones y entradas disponibles señalando a un documento de Swagger, use la [acción HTTP + Swagger](../connectors/connectors-native-http-swagger.md).

*  Para llamar a cualquier API, incluso aquellas que no tengan ni expongan un documento de Swagger, siempre puede crear una solicitud con la [acción HTTP](../connectors/connectors-native-http.md).

## <a name="authenticate-calls-to-your-custom-api"></a>Autenticación de las llamadas a la API personalizada

Puede proteger las llamadas a la API personalizada de las siguientes maneras:

* [Ningún cambio de código](#no-code): proteja su API con [Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) por medio de Azure Portal, de forma que no tenga que actualizar el código ni volver a implementar la API.

  > [!NOTE]
  > De forma predeterminada, la autenticación de Azure AD que se activa en Azure Portal no lleva a cabo una autorización específica. Por ejemplo, esta autenticación bloquea la API a un solo inquilino concreto, no a una aplicación o un usuario específicos. 

* [Actualice el código de la API](#update-code): proteja su API aplicando la [autenticación de certificado](#certificate), la [autenticación básica](#basic) o la [autenticación de Azure AD](#azure-ad-code) mediante código.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>Autenticación de las llamadas a la API sin cambiar el código

Estos son los pasos generales para este método:

1. Cree dos [identidades de aplicación de Azure Active Directory (Azure AD)](../app-service-api/app-service-api-dotnet-service-principal-auth.md): una para la aplicación lógica y otra para la aplicación web (o aplicación de API).

2. Para autenticar llamadas a la API, use las credenciales (identificador de cliente y secreto) de la [entidad de servicio](../app-service-api/app-service-api-dotnet-service-principal-auth.md) asociada con la identidad de aplicación de Azure AD para su aplicación lógica.

3. Incluya los identificadores de aplicación en la definición de la aplicación lógica.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>Parte 1: Creación de una identidad de aplicación de Azure AD para la aplicación lógica

La aplicación lógica usa esta identidad de aplicación de Azure AD para autenticarse en Azure AD. Solo tiene que establecer esta identidad una vez para el directorio. Por ejemplo, puede usar la misma identidad para todas las aplicaciones lógicas, aunque puede crear identidades únicas para cada una. Puede configurar estas identidades en Azure Portal, en el [Portal de Azure clásico](#app-identity-logic-classic) o con [PowerShell](#powershell).

**Creación de la identidad de aplicación para la aplicación lógica en Azure Portal**

1. En [Azure Portal](https://portal.azure.com "https://portal.azure.com"), elija **Azure Active Directory**. 

2. Confirme que se encuentra en el mismo directorio que la aplicación web o de API.

   > [!TIP]
   > Para cambiar de directorio, haga clic en el perfil y seleccione otro. O bien, elija **Información general** > **Cambiar directorio**.

3. En el menú de directorio, en **Administrar**, elija **Registros de aplicaciones** > **Nuevo registro de aplicaciones**.

   > [!TIP]
   > De forma predeterminada, la lista de registros de aplicaciones muestra todos los presentes en el directorio. Para ver solo sus registros de aplicaciones, junto al cuadro de búsqueda, seleccione **Mis aplicaciones**. 

   ![Crear registro de aplicaciones](./media/logic-apps-custom-hosted-api/new-app-registration-azure-portal.png)

4. Asigne un nombre a la identidad de aplicación, deje **Tipo de aplicación** establecido en **Aplicación web o API**, proporcione una cadena única con formato de dominio para **Dirección URL de inicio de sesión** y elija **Crear**.

   ![Proporcionar un nombre y una dirección URL de inicio de sesión para la identidad de aplicación](./media/logic-apps-custom-hosted-api/logic-app-identity-azure-portal.png)

   La identidad de aplicación que ha creado para la aplicación lógica aparece ahora en la lista de registros de aplicaciones.

   ![Identidad de aplicación para la aplicación lógica](./media/logic-apps-custom-hosted-api/logic-app-identity-created.png)

5. En la lista de registros de aplicaciones, seleccione la nueva identidad de aplicación. Copie y guarde el valor de **Id. de la aplicación** para usarlo como "identificador de cliente" de la aplicación lógica en la parte 3.

   ![Copiar y guardar el identificador de aplicación para la aplicación lógica](./media/logic-apps-custom-hosted-api/logic-app-application-id.png)

6. Si la configuración de la identidad de aplicación no está visible, elija **Configuración** o **Toda la configuración**.

7. En **Acceso de API**, elija **Claves**. En **Descripción**, proporcione un nombre para la clave. En **Expira**, seleccione una duración para la clave.

   La clave que va a crear actúa como "secreto" o contraseña de la identidad de aplicación para la aplicación lógica.

   ![Crear clave para la identidad de aplicación lógica](./media/logic-apps-custom-hosted-api/create-logic-app-identity-key-secret-password.png)

8. En la barra de herramientas, elija **Guardar**. En **Valor**, ahora aparece su clave. 
**Asegúrese de copiar y guardar la clave** para usarla más adelante, ya que está oculta una vez que se sale de la hoja Claves.

   Cuando configure la aplicación lógica en la parte 3, especifique esta clave como "secreto" o contraseña.

   ![Copiar y guardar la clave para más tarde](./media/logic-apps-custom-hosted-api/logic-app-copy-key-secret-password.png)

<a name="app-identity-logic-classic"></a>

**Creación de la identidad de aplicación para la aplicación lógica en el Portal de Azure clásico**

1. En el Portal de Azure clásico, elija [**Active Directory**](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory).

2. Seleccione el directorio que usó para la aplicación web o de API.

3. En la pestaña **Aplicaciones**, elija **Agregar** en la parte inferior de la página.

4. Asigne un nombre a la identidad de aplicación y elija **Siguiente** (flecha derecha).

5. En **Propiedades de la aplicación**, proporcione una cadena única con formato de dominio para **Dirección URL de inicio de sesión** y **URI de id. de aplicación**, y elija **Completar** (marca de verificación).

6. En la pestaña **Configurar**, copie y guarde el valor de **Id. de cliente** para usarlo con la aplicación lógica en la parte 3.

7. En **Claves**, abra la lista **Seleccionar duración**. Seleccione una duración para la clave.

   La clave que va a crear actúa como "secreto" o contraseña de la identidad de aplicación para la aplicación lógica.

8. En la parte inferior de la página, elija **Guardar**. Quizás tenga que esperar unos segundos.

9. En **Claves**, asegúrese de copiar y guardar la clave que ahora aparece. 

   Cuando configure la aplicación lógica en la parte 3, especifique esta clave como "secreto" o contraseña.

Para más información, aprenda a [configurar la aplicación de App Service para usar el inicio de sesión de Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

<a name="powershell"></a>

**Creación de la identidad de aplicación para la aplicación lógica en PowerShell**

Puede realizar esta tarea mediante Azure Resource Manager con PowerShell. En PowerShell, ejecute estos comandos:

1. `Switch-AzureMode AzureResourceManager`

2. `Add-AzureAccount`

3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password "identity-password"`

4. Asegúrese de copiar el valor de **Identificador de inquilino** (GUID para el inquilino de Azure AD), el de **Id. de la aplicación** y la contraseña que usó.

Para más información, consulte cómo [crear una entidad de servicio con PowerShell para acceder a recursos](../azure-resource-manager/resource-group-authenticate-service-principal.md).

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>Parte 2: Creación de una identidad de aplicación de Azure AD para la aplicación web o de API

Si ya se ha implementado la aplicación web o de API, puede activar la autenticación y crear la identidad de aplicación en Azure Portal. En caso contrario, puede [activar la autenticación cuando implemente con una plantilla de Azure Resource Manager](#authen-deploy). 

**Creación de la identidad de aplicación y activación de la autenticación en Azure Portal para aplicaciones implementadas**

1. En [Azure Portal](https://portal.azure.com "https://portal.azure.com"), busque y seleccione la aplicación web o de API. 

2. Haga clic en **Configuración** y elija **Autenticación/autorización**. En **Autenticación de App Service**, elija **Activar** la autenticación. En **Proveedores de autenticación**, elija **Azure Active Directory**.

   ![Activar la autenticación](./media/logic-apps-custom-hosted-api/custom-web-api-app-authentication.png)

3. Ahora cree una identidad de aplicación para la aplicación web o de API como se muestra aquí. En la hoja **Configuración de Azure Active Directory**, establezca **Modo de administración** en **Rápido**. Elija **Crear nueva aplicación de AD**. Asigne un nombre a la identidad de aplicación y elija **Aceptar**. 

   ![Crear una identidad de aplicación para la aplicación web o de API](./media/logic-apps-custom-hosted-api/custom-api-application-identity.png)

4. En la **hoja Autenticación o autorización**, elija **Guardar**.

Ahora debe buscar el identificador de cliente y el de inquilino para la identidad de aplicación que esté asociada con la aplicación web o de API. Ha usado estos identificadores en la parte 3. Por tanto, continúe con estos pasos para Azure Portal o el [Portal de Azure clásico](#find-id-classic).

**Búsqueda de los identificadores de cliente y de inquilino de la identidad de aplicación para su aplicación web o de API en Azure Portal**

1. En **Proveedores de autenticación**, elija **Azure Active Directory**. 

   ![Elegir "Azure Active Directory"](./media/logic-apps-custom-hosted-api/custom-api-app-identity-client-id-tenant-id.png)

2. En la hoja **Configuración de Azure Active Directory**, establezca **Modo de administración** en **Avanzado**.

3. Copie el valor de **Id. de cliente** y guarde ese GUID para usarlo en la parte 3.

   > [!TIP] 
   > Si **Id. de cliente** y **URL del emisor** no aparecen, pruebe a actualizar Azure Portal y repita el paso 1.

4. En **URL del emisor**, copie y guarde solo el GUID para la parte 3. También puede usar este GUID en la plantilla de implementación de su aplicación web o de API, si es necesario.

   Este GUID es para su inquilino específico ("Identificador de inquilino") y debería aparecer en esta dirección URL: `https://sts.windows.net/{GUID}`

5. Sin guardar los cambios, cierre la hoja **Configuración de Azure Active Directory**.

<a name="find-id-classic"></a>

**Búsqueda de los identificadores de cliente y de inquilino de la identidad de aplicación para su aplicación web o de API en Portal de Azure clásico**

1. En el Portal de Azure clásico, elija [**Active Directory**](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory).

2.  Seleccione el directorio que usó para la aplicación web o de API.

3. En el cuadro **Búsqueda**, busque y seleccione la identidad de aplicación para su aplicación web o de API.

4. En la pestaña **Configurar**, copie el valor de **Id. de cliente** y guarde ese GUID para usarlo en la parte 3.

5. Después de obtener el identificador de cliente, en la parte inferior de la pestaña **Configurar**, elija **Ver extremos**.

6. Copie la dirección URL para **Documento de metadatos de federación** y vaya a ella.

7. En el documento de metadatos que se abre, busque el elemento **Id. de EntityDescriptor** raíz, que tiene un atributo **entityID** con este formato: `https://sts.windows.net/{GUID}` 

      El GUID de este atributo es el del inquilino específico (Identificador de inquilino).

8. Copie el identificador del inquilino y guárdelo para usarlo en la parte 3 y también en la plantilla de implementación de su aplicación web o de API, si es necesario.

Para más información, consulte los temas siguientes:

* [Autenticación de usuario para aplicaciones de API en Azure App Service](../app-service-api/app-service-api-dotnet-user-principal-auth.md)
* [Autenticación y autorización en el Servicio de aplicaciones de Azure](../app-service/app-service-authentication-overview.md)

<a name="authen-deploy"></a>

**Activación de la autenticación cuando implemente con una plantilla de Azure Resource Manager**

Todavía debe crear para su aplicación web o de API una identidad de aplicación de Azure AD que difiera de la de la aplicación lógica. Para crear la identidad de aplicación, siga los pasos anteriores en la parte 2 para Azure Portal. También puede seguir los pasos en la parte 1, pero asegúrese de usar `https://{URL}` para la aplicación web o de API en **Dirección URL de inicio de sesión** y **URI de id. de aplicación**. En estos pasos, tiene que guardar el identificador de cliente y el de inquilino para usarlos en la plantilla de implementación de la aplicación y también en la parte 3.

> [!NOTE]
> Cuando cree la identidad de aplicación de Azure AD para su aplicación web o de API, debe usar Azure Portal o el Portal de Azure clásico, en lugar de PowerShell. El cmdlet de PowerShell no configura los permisos necesarios para iniciar la sesión de los usuarios en un sitio web.

Una vez que obtenga los identificadores de cliente y de inquilino, inclúyalos como un subrecurso de la aplicación web o de API en la plantilla de implementación:

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
                 "clientId": "{client-ID}",
                 "issuer": "https://sts.windows.net/{tenant-ID}/",
               }
           }
       }
   ]
   ```

Para implementar automáticamente una aplicación web en blanco y una aplicación lógica juntas con la autenticación de Azure Active Directory, [vea la plantilla completa aquí](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json) o haga clic en **Implementar en Azure** aquí:

[![Implementación en Azure](media/logic-apps-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>Parte 3: Relleno de la sección de autorización en la aplicación lógica

La plantilla anterior ya tiene esta sección de autorización configurada pero, si va a crear la aplicación lógica directamente, debe incluir la sección de autorización completa.

Abra la definición de aplicación lógica en la vista de código, vaya a la sección de la acción **HTTP**, busque la sección **Authorization** e incluya esta línea:

`{"tenant": "{tenant-ID}", "audience": "{client-ID-from-Part-2-web-app-or-API app}", "clientId": "{client-ID-from-Part-1-logic-app}", "secret": "{key-from-Part-1-logic-app}", "type": "ActiveDirectoryOAuth" }`

| Elemento | Descripción |
| ------- | ----------- |
| tenant |El GUID para el inquilino de Azure AD |
| audience |Obligatorio. El GUID para el recurso de destino al que desea acceder: el identificador de cliente de la identidad de aplicación para su aplicación web o de API |
| clientId |El GUID para el cliente que solicita acceso: el identificador de cliente de la identidad de aplicación para la aplicación lógica |
| secret |Obligatorio. La clave o contraseña de la identidad de aplicación para el cliente que solicita el token de acceso |
| type |El tipo de autenticación. En autenticación ActiveDirectoryOAuth, el valor es `ActiveDirectoryOAuth`. |

Por ejemplo:

```
{
   ...
   "actions": {
      "some-action": {
         "conditions": [],
         "inputs": {
            "method": "post",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         },
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>Protección de las llamadas API mediante código

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>Autenticación de certificados

Para validar las solicitudes entrantes desde la aplicación lógica hacia la aplicación web o de API, puede usar certificados de cliente. Para configurar su código, aprenda a [configurar la autenticación mutua de TLS](../app-service-web/app-service-web-configure-tls-mutual-auth.md).

En la sección **Authorization**, incluya esta línea: 

`{"type": "clientcertificate", "password": "password", "pfx": "long-pfx-key"}`

| Elemento | Descripción |
| ------- | ----------- |
| type |Obligatorio. El tipo de autenticación. Para los certificados de cliente SSL, el valor debe ser `ClientCertificate`. |
| Contraseña |Obligatorio. La contraseña para acceder al certificado de cliente (archivo PFX) |
| pfx |Obligatorio. Contenido con codificación base64 del certificado del cliente (archivo PFX) |

<a name="basic"></a>

#### <a name="basic-authentication"></a>Autenticación básica

Para validar solicitudes entrantes desde la aplicación lógica hacia la aplicación web o de API, puede usar la autenticación básica, por ejemplo, un nombre de usuario y una contraseña. La autenticación básica es un patrón habitual, y puede usarla en cualquier lenguaje utilizado para compilar la aplicación web o de API.

En la sección **Authorization**, incluya esta línea:

`{"type": "basic", "username": "username", "password": "password"}`.

| Elemento | Descripción |
| --- | --- |
| type |Obligatorio. El tipo de autenticación. Para la autenticación básica, el valor debe ser `Basic`. |
| nombre de usuario |Obligatorio. El nombre de usuario para la autenticación |
| Contraseña |Obligatorio. La contraseña para la autenticación |

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>Autenticación de Azure Active Directory mediante código

De forma predeterminada, la autenticación de Azure AD que se activa en Azure Portal no lleva a cabo una autorización específica. Por ejemplo, esta autenticación bloquea la API a un solo inquilino concreto, no a una aplicación o un usuario específicos. 

Para restringir el acceso de la API a la aplicación lógica por medio de código, extraiga el encabezado que tenga JSON Web Token (JWT). Compruebe la identidad del llamador y rechace las solicitudes que no coinciden.

Además, para implementar esta autenticación totalmente en su propio código sin usar Azure Portal, aprenda a [autenticarse con su entorno Active Directory local en su aplicación de Azure](../app-service-web/web-sites-authentication-authorization.md).

Para crear una identidad de aplicación para la aplicación lógica y usarla para llamar a la API, debe seguir los pasos anteriores.

## <a name="next-steps"></a>Pasos siguientes

* [Comprobación del rendimiento de la aplicación lógica con alertas y registros de diagnóstico](logic-apps-monitor-your-logic-apps.md)
