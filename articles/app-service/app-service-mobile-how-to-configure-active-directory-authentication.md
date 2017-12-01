---
title: "Configuración de la autenticación de Azure Active Directory para una aplicación de App Services"
description: "Obtenga información acerca de cómo configurar la autenticación de Azure Active Directory para la aplicación de Servicios de aplicaciones"
author: mattchenderson
services: app-service
documentationcenter: 
manager: syntaxc4
editor: 
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 990fab9aeea71b8cf344b9a49a5ed438db6663c0
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2017
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-login"></a>Configuración de una aplicación de App Service para usar el inicio de sesión de Azure Active Directory
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

En este artículo se muestra cómo configurar Azure App Services para usar Azure Active Directory como proveedor de autenticación.

## <a name="express"></a>Configuración de Azure Active Directory mediante la configuración rápida
1. En [Azure Portal], vaya a su aplicación de App Service. En el panel de navegación izquierdo, seleccione **Autenticación / Autorización**.
2. Si **Autenticación / Autorización** no está habilitado, seleccione **Activar**.
3. Seleccione **Azure Active Directory** y luego **Rápida**, en **Modo de administración**.
4. Seleccione **Aceptar** para registrar la aplicación de App Service en Azure Active Directory. Se crea un nuevo registro de aplicaciones. Si, por el contrario, desea elegir un registro de aplicación existente, haga clic en **Seleccionar una aplicación existente** y busque el nombre de un registro creado de aplicación anteriormente en el inquilino.
   Haga clic en el registro de aplicación para seleccionarlo y en **Aceptar**. A continuación, haga clic en **Aceptar** en la página de configuración de Azure Active Directory.
   De forma predeterminada, App Service ofrece autenticación pero no restringe el acceso autorizado al contenido del sitio y a las API. Debe autorizar a los usuarios en el código de la aplicación.
5. (Opcional) Para restringir el acceso al sitio solo a los usuarios autenticados mediante Azure Active Directory, en **Acción necesaria cuando la solicitud no está autenticada**, seleccione **Iniciar sesión con Azure Active Directory**. Esto requiere que todas las solicitudes se autentiquen y que todas las solicitudes no autenticadas se redirijan a Azure Active Directory para la autenticación.
6. Haga clic en **Guardar**.

Ahora está preparado para usar Azure Active Directory para realizar la autenticación en la aplicación de App Service.

## <a name="advanced"></a>(Método alternativo) Configuración manual de Azure Active Directory con la configuración avanzada
También puede elegir proporcionar los valores de configuración manualmente. Esta es la solución preferida si el inquilino de AAD que desea usar es diferente de aquel con el que inicia sesión en Azure. Para completar la configuración, primero debe crear un registro en Azure Active Directory y luego proporcionar algunos de los detalles de registro a App Service.

### <a name="register"> </a>Registro de una aplicación de App Service con Azure Active Directory
1. Inicie sesión en [Azure Portal] y vaya a la aplicación de App Service. Copie la dirección **URL** de su aplicación. Se usará para configurar el registro de la aplicación de Azure Active Directory.
2. Navegue a **Active Directory**, seleccione **Registros de aplicaciones** y haga clic en **Nuevo registro de aplicaciones** en la parte superior para iniciar un nuevo registro de aplicaciones. 
3. En la página **Crear**, escriba un **nombre** para el registro de su aplicación, seleccione el tipo **Web App / API** y, en el cuadro **Dirección URL de inicio de sesión**, pegue la dirección URL de la aplicación (del paso 1). Luego haga clic en **Crear**.
4. En cuestión de segundos, debería ver aparecer el nuevo registro de aplicación recién creado.
5. Una vez que se agrega el registro de aplicación, haga clic en el nombre del registro de aplicación, en **Configuración** en la parte superior y, luego, en **Propiedades** 
6. En el cuadro **URI del identificador de la aplicación**, pegue la dirección URL de la aplicación (del paso 1) y péguela también en **Dirección URL de la página principal** y, luego, haga clic en **Guardar**
7. Ahora haga clic en las **direcciones URL de respuesta**, edite la **dirección URL de respuesta**, pegue la dirección URL de la aplicación (del paso 1), modifique el protocolo para asegurarse de que tiene el protocolo **https://** (no http://) y, luego anexe */.auth/login/aad/callback* al final de la dirección URL (por ejemplo, `https://contoso.azurewebsites.net/.auth/login/aad/callback`). Haga clic en **Guardar**.   
8.  En este punto, copie el **identificador de la aplicación** en cuestión. Consérvelo para usarlo más adelante. Lo necesitará para configurar la aplicación de App Service.
9. Cierre la página **Aplicación registrada**. En la página **Registros de aplicación**, haga clic en el botón **Puntos de conexión** en la parte superior y luego copie la dirección URL del **documento de metadatos de federación**. 
10. Abra una ventana del explorador y navegue a la dirección URL pegando y yendo a la página XML. En la parte superior del documento, hay un elemento **EntityDescriptor**; debe haber un atributo **entityID** del formulario `https://sts.windows.net/` seguido de un GUID específico para el inquilino (denominado "identificador de inquilino"). Copie este valor: actúa como **dirección URL del emisor**. Más adelante configurará la aplicación para usarla.

### <a name="secrets"> </a>Incorporación de información de Azure Active Directory a la aplicación de App Service
1. Vuelva a [Azure Portal] y vaya a su aplicación de App Service. Haga clic en **Autenticación y autorización**. Si esta característica no está habilitada, mueva el interruptor a la posición de **activada**. Haga clic en **Azure Active Directory**, en Proveedores de autenticación, para configurar la aplicación. (Opcional) De manera predeterminada, App Service ofrece autenticación pero no restringe el acceso autorizado al contenido del sitio y a las API. Debe autorizar a los usuarios en el código de la aplicación. Establezca **Acción necesaria cuando la solicitud no está autenticada**, en **Iniciar sesión con Azure Active Directory**. Esta opción requiere que todas las solicitudes se autentiquen y que todas las solicitudes no autenticadas se redirijan a Azure Active Directory para la autenticación.
2.En la configuración de autenticación de Active Directory, haga clic en **Avanzadas** en el **modo de administración**. Pegue el identificador de la aplicación en el cuadro Id. de cliente (del paso 8) y pegue el valor de entityId (del paso 10) en el valor de la dirección URL del emisor. y, a continuación, haga clic en **Aceptar**.
3. En la página de configuración de la autenticación de Active Directory, haga clic en **Guardar**.

Ahora está preparado para usar Azure Active Directory para realizar la autenticación en la aplicación de App Service.

## <a name="optional-configure-a-native-client-application"></a>(Opcional) Configurar una aplicación de cliente nativo
Azure Active Directory también permite registrar a los clientes nativos, lo que proporciona un mayor control sobre la asignación de permisos. Esto es necesario si desea realizar inicios de sesión con una biblioteca como la **Biblioteca de autenticación de Active Directory**.

1. Vaya a **Active Directory** en [Azure Portal].
2. En el panel de la izquierda, seleccione **Registros de aplicaciones**. Haga clic en **Nuevo registro de aplicación** en la parte superior.
4. En la página **crear**, escriba un **nombre** para el registro de aplicación. Seleccione **Nativo** en **Tipo de aplicación**.
5. En el cuadro **URI de redirección**, especifique el punto de conexión */.auth/login/done* del sitio, con el esquema HTTPS. Este valor debe ser similar a *https://contoso.azurewebsites.net/.auth/login/done*. Si crea una aplicación de Windows, en su lugar, use el valor de [SID del paquete](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) como URI.
5. Haga clic en **Crear**.
6. Una vez que se ha agregado el registro de aplicación, selecciónelo para abrirlo. Busque el **Identificador de aplicación** y tome nota de este valor.
7. Haga clic en **Toda la configuración** > **Permisos necesarios** > **Agregar** > **Seleccionar una API**.
8. Escriba el nombre de la aplicación de App Service que registró anteriormente para buscar en ella, selecciónela y haga clic en **Seleccionar**. 
9. Seleccione **Acceso a \<app_name>**. Después, haga clic en **Seleccionar**. A continuación, haga clic en **Hecho**.

Ahora ha configurado una aplicación cliente nativa que puede acceder a la aplicación de App Service.

## <a name="related-content"></a>Contenido relacionado
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-url.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app_registration.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-create.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-appidurl.png
[4]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-replyurl.png
[5]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints.png
[6]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints-fedmetadataxml.png
[7]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth.png
[8]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth-config.png



<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
[alternative method]:#advanced
