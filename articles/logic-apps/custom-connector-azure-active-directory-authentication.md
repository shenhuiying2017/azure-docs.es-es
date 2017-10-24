---
title: "Protección de conectores personalizados con Azure AD - Azure Logic Apps | Microsoft Docs"
description: "Incorpore características de autenticación y seguridad a las API y el conector con Azure Active Directory (Azure AD)."
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: bcee842cb880002daaa3ae9d9110ee1734941b6d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="add-security-to-your-api-and-connector-with-azure-active-directory-azure-ad"></a>Incorporación de características de seguridad a las API y el conector con Azure Active Directory (Azure AD)

Para proteger las llamadas entre la API y un conector personalizado, agregue la autenticación de Azure AD a la API web y el conector. Para este escenario, es necesario crear dos aplicaciones de Azure Active Directory (Azure AD): una aplicación de Azure AD protege su API web, mientras que la otra aplicación de Azure AD protege el registro del conector y agrega el acceso delegado. 

En este tutorial se utiliza como ejemplo la API de Azure Resource Manager. Azure Resource Manager le ayuda a administrar los componentes de una solución que ha creado en Azure, como las bases de datos, las máquinas virtuales y las aplicaciones web. Un conector personalizado para Azure Resource Manager puede resultar útil si desea administrar recursos de Azure desde los flujos de trabajo. Para obtener más información, consulte [Información general del Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md).

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure. Si no tiene una suscripción, puede [comenzar con una cuenta de Azure gratuita](https://azure.microsoft.com/free/). También puede registrarse para obtener una [suscripción de pago por uso](https://azure.microsoft.com/pricing/purchase-options/).

* Para este tutorial, se utiliza el [archivo de OpenAPI de ejemplo para Azure Resource Manager](https://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json).

  > [!NOTE] 
  > El archivo de ejemplo de OpenAPI no define todas las operaciones de Azure Resource Manager y actualmente contiene solo la operación para [enumerar todas las suscripciones](https://docs.microsoft.com/rest/api/resources/subscriptions#Subscriptions_List). Puede editar este OpenAPI o crear otro archivo de OpenAPI mediante el [editor de OpenAPI en línea](http://editor.swagger.io/).
  >
  > Este tutorial se puede aplicar a cualquier API de RESTful donde desee utilizar la autenticación de Azure AD.

## <a name="1-create-your-first-azure-ad-app-for-securing-your-web-api"></a>1. Creación de la primera aplicación de Azure AD para proteger su API web

Su primera aplicación de Azure AD realiza la autenticación cuando el conector personalizado llama a la API, que es la API de Azure Resource Manager en este ejemplo.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com). Si tiene más de un inquilino de Azure Active Directory, confirme que ha iniciado sesión en el directorio correcto; para ello, compruebe el directorio bajo su nombre de usuario. 

   ![Confirmación del directorio](./media/custom-connector-azure-active-directory-authentication/check-user-directory.png)

   > [!TIP]
   > Para cambiar los directorios, elija el nombre de usuario y luego seleccione el directorio que desee.

2. En el menú principal de Azure, elija **Azure Active Directory** para ver el directorio actual.

   ![Elegir "Azure Active Directory"](./media/custom-connector-azure-active-directory-authentication/azure-active-directory.png)

   > [!TIP]
   > Si en el menú principal de Azure no aparece **Azure Active Directory**, elija **Más servicios**. En el cuadro **Filtro**, escriba "Azure Active Directory" como filtro y luego elija **Azure Active Directory**.

3. En el menú de directorio, en **Administrar**, elija **Registros de aplicaciones**. En la lista de aplicaciones registradas, elija **+ Nuevo registro de aplicaciones**.

   ![Seleccione "Registros de aplicaciones" "+ Nuevo registro de aplicaciones".](./media/custom-connector-azure-active-directory-authentication/add-app-registrations.png)

4. En **Crear**, proporcione los detalles de la aplicación de Azure AD como se describe en la tabla y después elija **Crear**. 

   ![Creación de una aplicación de Azure AD](./media/custom-connector-azure-active-directory-authentication/create-app1-registration.png)

   | Configuración | Valor sugerido | Descripción | 
   | ------- | --------------- | ----------- | 
   | **Name** | *nombre-aplicación-api-web* | Nombre de la aplicación de Azure AD de la API web | 
   | **Tipo de aplicación** | **API/aplicación web** | Tipo de aplicación | 
   | **URL de inicio de sesión** | `https://login.windows.net` | | 
   |||| 

5. Cuando vuelva a la lista de **Registros de aplicaciones** de su directorio, seleccione la aplicación de Azure AD.

   ![Selección de la aplicación de Azure AD de la lista](./media/custom-connector-azure-active-directory-authentication/app1-registration-created.png)

6. Cuando aparezca la página de detalles de la aplicación, asegúrese de que **copia y guarda el *Id. de la aplicación	* en un lugar seguro**. Necesitará este identificador en un momento posterior.

   ![Guardado del "Id. de la aplicación" para su uso posterior](./media/custom-connector-azure-active-directory-authentication/application-id-app1.png)

7. Ahora proporcione una dirección URL de respuesta para la aplicación de Azure AD. En el menú **Configuración** de la aplicación, elija **URL de respuesta**. Escriba esta dirección URL y luego elija **Guardar**.

   ![URL de respuesta](./media/custom-connector-azure-active-directory-authentication/add-reply-url1.png)

   | Configuración | Valor sugerido | Descripción | 
   | ------- | --------------- | ----------- | 
   | **URL de respuesta** | Para su propia API, escriba esta dirección URL: </br>`https://{your-web-app-root-URL}/.auth/login/aad/callback` | | 
   | **Permisos delegados** | {no es necesario} | | 
   | **Clave de cliente** | {no es necesario} | | 
   |||| 

   > [!TIP]
   > Si el menú **Configuración** no apareció antes, elija **Configuración** aquí:
   >
   > ![Selección de "Configuración"](./media/custom-connector-azure-active-directory-authentication/show-app1-settings-menu.png)

## <a name="2-create-your-second-azure-ad-app-for-your-custom-connector"></a>2. Creación de la segunda aplicación de Azure AD para el conector personalizado

La segunda aplicación de Azure AD protege el registro del conector personalizado y agrega acceso delegado a la API web protegida por la primera aplicación de Azure AD. 

> [!IMPORTANT]
> Asegúrese de que ambas aplicaciones de Azure AD existen en el mismo directorio.

1. Haga clic en la lista **Registros de aplicaciones** y elija otra vez **+Nuevo registro de aplicaciones**.

   ![Seleccione "Registros de aplicaciones" "+ Nuevo registro de aplicaciones".](./media/custom-connector-azure-active-directory-authentication/add-app-registrations2.png)

2. En **Crear**, indique los detalles de la segunda aplicación de Azure AD como se describe en la tabla y, a continuación, seleccione **Crear**. 

   ![Creación de una aplicación de Azure AD](./media/custom-connector-azure-active-directory-authentication/create-app2-registration.png)

   | Configuración | Valor sugerido | Descripción | 
   | ------- | --------------- | ----------- | 
   | **Name** | *nombre-de-conector* | Nombre de la aplicación de Azure AD para el conector | 
   | **Tipo de aplicación** | **API/aplicación web** | Tipo de aplicación | 
   | **URL de inicio de sesión** | `https://login.windows.net` | | 
   |||| 

3. Cuando vuelva a la lista de **Registros de aplicaciones** de su directorio , seleccione la segunda aplicación de Azure AD.

   ![Selección de la segunda aplicación de Azure AD de la lista](./media/custom-connector-azure-active-directory-authentication/app2-registration-created.png)

4. Cuando aparezca la página de detalles de la aplicación, asegúrese de que **también copia y guarda este *Id. de la aplicación* en un lugar seguro**. Necesitará este identificador en un momento posterior.

   ![Guardado del "Id. de la aplicación" para su uso posterior](./media/custom-connector-azure-active-directory-authentication/application-id-app2.png)

5. Ahora proporcione una dirección URL de respuesta para la aplicación de Azure AD. En el menú **Configuración** de la aplicación, elija **URL de respuesta**. Escriba esta dirección URL y luego elija **Guardar**.

   | Configuración | Valor sugerido | 
   | ------- | --------------- | 
   | **URL de respuesta** | Para el conector personalizado de Azure Resource Manager, escriba esta dirección URL:`https://msmanaged-na.consent.azure-apim.net/redirect` | 
   ||| 

   > [!TIP]
   > Si el menú **Configuración** no apareció antes, elija **Configuración** aquí:
   >
   > ![Selección de "Configuración"](./media/custom-connector-azure-active-directory-authentication/show-app2-settings-menu.png)

6. En el menú **Configuración**, seleccione **Permisos necesarios** > **Agregar**.

   ![Permisos necesarios > Agregar](./media/custom-connector-azure-active-directory-authentication/add-api-access1-select-permissions.png)

7. Cuando se abra el menú **Agregar acceso de API**, elija **Seleccionar una API** > 
**Microsoft Azure Service Management API** > **Seleccionar**.

   ![Selección de una API](./media/custom-connector-azure-active-directory-authentication/add-api-access2-select-api.png)

8. En el menú **Agregar acceso de API**, elija **Seleccionar permisos**. En **Permisos delegados**, seleccione **Acceso a Azure Service Management como usuarios de la organización** > **Seleccionar**.

   ![Selección de "Permisos delegados" > "Acceso a Azure Service Management como usuarios de la organización"](./media/custom-connector-azure-active-directory-authentication/add-api-access3-select-permissions.png)

   En caso contrario, para otras opciones:

   | Opción | Valor sugerido | Descripción | 
   | ------ | --------------- | ----------- | 
   | **Permisos delegados** | | Selección de los permisos para el acceso delegado a su API web | 
   |||| 

9. En el menú **Agregar acceso de API**, elija **Listo**.

   ![Menú "Agregar acceso de API" > "Listo"](./media/custom-connector-azure-active-directory-authentication/add-api-access4-done.png)

10. Ahora, genere una *clave de cliente*, o "secreto", para la aplicación de Azure AD de su conector. 

    1. De nuevo en el menú **Configuración**, seleccione **Claves**. 
    Ponga a su clave un nombre que tenga como máximo 16 caracteres, seleccione un período de expiración y luego elija **Guardar**.

       ![Creación de una clave de cliente](./media/custom-connector-azure-active-directory-authentication/add-key.png)

    2. Cuando aparezca la clave generada, **asegúrese de copiarla en un lugar seguro** y luego salga de la página **Claves**.
    
       ![Copia y guardado manual de la clave](./media/custom-connector-azure-active-directory-authentication/save-key.png)

11. Después de guardar la clave, puede cerrar el menú **Configuración**.

## <a name="3-add-azure-ad-authentication-to-your-web-api-app"></a>3. Adición de la autenticación de Azure AD a la aplicación de API web

Ahora active la autenticación para la API web con su primera aplicación de Azure AD. Para más información, aprenda a [configurar la aplicación de App Service para usar el inicio de sesión de Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

1. En [Azure Portal](https://portal.azure.com), busque la aplicación de API web que publicó anteriormente en [Creación de conectores personalizados desde API web ](../logic-apps/custom-connector-build-web-api-app-tutorial.md).

2. En **Configuración**, elija **Autenticación/autorización**. 

   1. En **Autenticación de App Service**, seleccione **Activar**.
   2. En **Acción necesaria cuando la solicitud no está autenticada**, seleccione **Iniciar sesión con Azure Active Directory**.
   3. En **Proveedores de autenticación,** seleccione **Azure Active Directory**. 

   ![Elección de "Autenticación/autorización"](./media/custom-connector-azure-active-directory-authentication/web-api-app-authentication.png)

3. En la página **Configuración de Azure Active Directory**:

   1. En **Modo de administración**, elija **Rápido**.

   2. Ahora seleccione la aplicación de Azure AD que usa su aplicación de API web para la autenticación. 
   Elija **Seleccionar aplicación de AD existente** > **Aplicación de Azure AD**.

   3. En **Aplicaciones de Azure AD**, seleccione la aplicación de Azure AD que creó anteriormente para la aplicación de API web. 
   
   4. Elija **Aceptar** hasta que regrese a la página **Autenticación / autorización**.

   Por ejemplo:

   ![Selección de la aplicación de Azure AD que representa a la aplicación de API web](./media/custom-connector-azure-active-directory-authentication/web-api-app-select-azure-ad-app.png)

4. En la página **Autenticación/autorización**, elija **Guardar**.

   ![Guardado de configuración de autenticación](./media/custom-connector-azure-active-directory-authentication/web-api-app-azure-ad-app-save.png)

   Ahora la aplicación de API web puede usar Azure AD para la autenticación.

## <a name="4-set-up-azure-ad-authentication-in-your-web-apis-openapi-file"></a>4. Configuración de la autenticación de Azure AD en el archivo de OpenAPI de API web

Abra el archivo de OpenAPI para la aplicación de API web para que pueda agregar el objeto `securityDefintions` y la autenticación de Azure AD en la propiedad `host`. Este es un ejemplo en el que se muestra la propiedad `host` y el objeto `securityDefinitions`:

``` json
// Your OpenAPI file header appears here...

"host": "{your-web-api-app-root-url}",
"schemes": [
    "https" // Make sure this is https!
],
"securityDefinitions": {
    "AAD": {
        "type": "oauth2",
        "flow": "accessCode",
        "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
        "tokenUrl": "https://login.windows.net/common/oauth2/token",
        "scopes": {}
    }
},

// Your OpenAPI document continues here...
```
Ahora ya puede crear y registrar el conector personalizado.

## <a name="next-steps"></a>Pasos siguientes

* [Registro del conector](../logic-apps/logic-apps-custom-connector-register.md)
* [Preguntas más frecuentes sobre conectores personalizados](../logic-apps/custom-connector-faq.md)
