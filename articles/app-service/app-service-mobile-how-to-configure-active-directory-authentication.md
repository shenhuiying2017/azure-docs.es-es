---
title: "Configuración de la autenticación de Azure Active Directory para la aplicación de Servicios de aplicaciones"
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
ms.openlocfilehash: 25f0578a9e273c30ecc98af5b66c6dd43305aa03
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-your-app-service-application-to-use-azure-active-directory-login"></a>Configuración de la aplicación del Servicio de aplicaciones para usar el inicio de sesión de Azure Active Directory
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

En este tema se muestra cómo configurar Servicios de aplicaciones de Azure para usar Azure Active Directory como proveedor de autenticación.

## <a name="express"></a>Configuración de Azure Active Directory mediante la configuración rápida
1. En el [Portal de Azure], vaya a la aplicación. Haga clic en **Configuración** y luego en **Autenticación/autorización**.
2. Si esta característica no está habilitada, mueva el interruptor a la posición de **activada**.
3. Haga clic en **Azure Active Directory** y luego en **Rápida** en **Modo de administración**.
4. Haga clic en **Aceptar** para registrar la aplicación en Azure Active Directory. Se creará un nuevo registro. Si, por el contrario, desea elegir un registro existente, haga clic en **Seleccionar una aplicación existente** y busque el nombre de un registro creado anteriormente en el inquilino.
   Haga clic en el registro para seleccionarlo y haga clic en **Aceptar**. A continuación, haga clic en **Aceptar** en la hoja de configuración de Azure Active Directory.
   De forma predeterminada, el Servicio de aplicaciones ofrece autenticación pero no restringe el acceso autorizado al contenido del sitio y a las API. Debe autorizar a los usuarios en el código de la aplicación.
5. (Opcional) Para restringir el acceso al sitio solo a los usuarios autenticados mediante Azure Active Directory, en **Acción necesaria cuando la solicitud no está autenticada**, seleccione **Iniciar sesión con Azure Active Directory**. Esto requiere que todas las solicitudes se autentiquen y que todas las solicitudes no autenticadas se redirijan a Azure Active Directory para la autenticación.
6. Haga clic en **Guardar**.

Ahora está preparado para usar Azure Active Directory para realizar la autenticación en la aplicación.

## <a name="advanced"></a>(Método alternativo) Configuración manual de Azure Active Directory con la configuración avanzada
También puede elegir proporcionar los valores de configuración manualmente. Esta es la solución preferida si el inquilino de AAD que desea usar es diferente de aquel con el que inicia sesión en Azure. Para completar la configuración, primero debe crear un registro en Azure Active Directory y luego proporcionar algunos de los detalles de registro al Servicio de aplicaciones.

### <a name="register"></a>Registro de la aplicación con Azure Active Directory
1. Inicie sesión en el [Portal de Azure]y vaya a la aplicación. Copie la **dirección URL** de la aplicación. Se usará para configurar la aplicación de Azure Active Directory.
2. Navegue a **Active Directory**, seleccione **Registros de aplicaciones** y haga clic en **Nuevo registro de aplicaciones** en la parte superior para iniciar un nuevo registro de aplicaciones. 
3. En el cuadro de diálogo Crear registro de aplicación, escriba un **nombre** para la aplicación, seleccione el tipo **API Web App** y en el cuadro **Dirección URL de inicio de sesión**, pegue la dirección URL de la aplicación (del paso 1). Luego haga clic en **Crear**.
4. En cuestión de segundos, debería ver aparecer el nuevo registro de aplicación que acaba de crear.
5. Una vez que se agrega la aplicación, haga clic en el nombre del registro de la aplicación, en **Configuración** (que aparece en la parte superior) y, luego, en **Propiedades** 
6. En el cuadro **URI del identificador de la aplicación**, pegue la dirección URL de la aplicación (del paso 1) y péguela también en **Dirección URL de la página principal** y, luego, haga clic en **Guardar**
7. Ahora haga clic en **Direcciones URL de respuesta**, edite la **dirección URL de respuesta**, pegue la dirección URL de la aplicación (del paso 1), modifique el protocolo para asegurarse de que tiene el protocolo **https://** (no http://) y, luego anexe */.auth/login/aad/callback* al final de la dirección URL. (Por ejemplo, `https://contoso.azurewebsites.net/.auth/login/aad/callback`). Haga clic en **Guardar**.   
8.  En este punto, copie el **identificador de la aplicación** en cuestión. Consérvelo para usarlo más adelante. Lo necesitará para configurar la aplicación web.
9. Cierre la hoja de detalles del registro de la aplicación. Debería volver al resumen del registro de la aplicación de Azure Active Directory. Haga clic en el botón **Puntos de conexión** que aparece en la parte superior y, luego, copie la dirección URL del **documento de metadatos de federación**. 
10. Abra una ventana del explorador y navegue a la dirección URL pegando y yendo a la página XML. En la parte superior del documento, habrá un elemento **EntityDescriptor** raíz, debe haber un atributo **entityID** del formulario `https://sts.windows.net/` seguido de un GUID específico para el inquilino (denominado "identificador de inquilino"). Copie este valor, actuará como **URL del emisor**. Más adelante configurará la aplicación para usar este valor.

### <a name="secrets"></a>Incorporación de información de Azure Active Directory a la aplicación
1. Vuelva al [Portal de Azure]y vaya a la aplicación. Haga clic en **Autenticación y autorización**. Si esta característica no está habilitada, mueva el interruptor a la posición de **activada**. Haga clic en **Azure Active Directory**, en Proveedores de autenticación, para configurar la aplicación. (Opcional) De manera predeterminada, App Service ofrece autenticación pero no restringe el acceso autorizado al contenido del sitio y a las API. Debe autorizar a los usuarios en el código de la aplicación. Seleccione **Acción necesaria cuando la solicitud no está autenticada** y establézcala en **Iniciar sesión con Azure Active Directory**. Esto requiere que todas las solicitudes se autentiquen y que todas las solicitudes no autenticadas se redirijan a Azure Active Directory para la autenticación.
2.En la configuración de autenticación de Active Directory, haga clic en **Avanzadas** en el **modo de administración**. Pegue el identificador de la aplicación en el cuadro Id. de cliente (del paso 8) y pegue el valor de entityId (del paso 10) en el valor de la dirección URL del emisor. y, a continuación, haga clic en **Aceptar**.
3. En la hoja de configuración de la autenticación de Active Directory, haga clic en **Guardar**.

Ahora está preparado para usar Azure Active Directory para realizar la autenticación en la aplicación.

## <a name="optional-configure-a-native-client-application"></a>(Opcional) Configurar una aplicación de cliente nativo
Azure Active Directory también permite registrar a los clientes nativos, lo que proporciona un mayor control sobre la asignación de permisos. Esto es necesario si desea realizar inicios de sesión con una biblioteca como la **Biblioteca de autenticación de Active Directory**.

1. Vaya a **Active Directory** en el [Portal de Azure clásico].
2. Seleccione el directorio y, a continuación, seleccione la pestaña **Aplicaciones** en la parte superior. Haga clic en **AGREGAR** en la parte inferior para crear un nuevo registro de aplicación.
3. Haga clic en **Agregar una aplicación que mi organización está desarrollando**.
4. En el Asistente para agregar aplicaciones, escriba el **nombre** de la aplicación y haga clic en el tipo **Aplicación de cliente nativo**. A continuación, haga clic para continuar.
5. En el cuadro **URI de redirección**, especifique el punto de conexión */.auth/login/done* del sitio, con el esquema HTTPS. Este valor debe ser similar a *https://contoso.azurewebsites.net/.auth/login/done*. Si crea una aplicación de Windows, en su lugar, use el valor de [SID del paquete](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) como URI.
6. Una vez que haya agregado la aplicación nativa, haga clic en la pestaña **Configurar** . Busque el **Identificador de cliente** y tome nota de este valor.
7. Desplácese hacia abajo por la página hasta la sección **Permisos para otras aplicaciones** y haga clic en **Agregar aplicación**.
8. Busque la aplicación web que registró y haga clic en el icono del signo de suma. A continuación, haga clic en la marca de verificación para cerrar el cuadro de diálogo. Si la aplicación web no se encuentra, vaya a su registro y agregue una nueva URL de respuesta (por ejemplo, la versión HTTP de la dirección URL actual), haga clic en Guardar y, a continuación, repita estos pasos; la aplicación debería aparecer en la lista.
9. En la entrada nueva que acaba de agregar, abra la lista desplegable **Permisos delegados** y seleccione **Acceso (nombreaplic)**. A continuación, haga clic en **Guardar**.

Ahora ha configurado una aplicación de cliente nativo que puede acceder a la aplicación del Servicio de aplicaciones.

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

[Portal de Azure]: https://portal.azure.com/
[Portal de Azure clásico]: https://manage.windowsazure.com/
[alternative method]:#advanced
