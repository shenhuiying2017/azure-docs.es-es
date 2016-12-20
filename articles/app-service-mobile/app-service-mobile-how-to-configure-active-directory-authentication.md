---
title: "Configuración de la autenticación de Azure Active Directory para la aplicación de Servicios de aplicaciones"
description: "Obtenga información acerca de cómo configurar la autenticación de Azure Active Directory para la aplicación de Servicios de aplicaciones"
author: mattchenderson
services: app-service
documentationcenter: 
manager: erikre
editor: 
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 425ecfeec54df9c52a4325964b2b48f6a2856693


---
# <a name="how-to-configure-your-app-service-application-to-use-azure-active-directory-login"></a>Configuración de la aplicación del Servicio de aplicaciones para usar el inicio de sesión de Azure Active Directory
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

En este tema se muestra cómo configurar Servicios de aplicaciones de Azure para usar Azure Active Directory como proveedor de autenticación.

## <a name="express"> </a>Configuración de Azure Active Directory mediante la configuración rápida
1. En el [Portal de Azure], vaya a la aplicación. Haga clic en **Configuración** y luego en **Autenticación/autorización**.
2. Si esta característica no está habilitada, mueva el interruptor a la posición de **activada**.
3. Haga clic en **Azure Active Directory** y luego en **Rápida** en **Modo de administración**.
4. Haga clic en **Aceptar** para registrar la aplicación en Azure Active Directory. Se creará un nuevo registro. Si, por el contrario, desea elegir un registro existente, haga clic en **Seleccionar una aplicación existente** y busque el nombre de un registro creado anteriormente en el inquilino.
   Haga clic en el registro para seleccionarlo y haga clic en **Aceptar**. A continuación, haga clic en **Aceptar** en la hoja de configuración de Azure Active Directory.
   
   ![][0]
   
   De forma predeterminada, el Servicio de aplicaciones ofrece autenticación pero no restringe el acceso autorizado al contenido del sitio y a las API. Debe autorizar a los usuarios en el código de la aplicación.
5. (Opcional) Para restringir el acceso al sitio solo a los usuarios autenticados mediante Azure Active Directory, en **Acción necesaria cuando la solicitud no está autenticada**, seleccione **Iniciar sesión con Azure Active Directory**. Esto requiere que todas las solicitudes se autentiquen y que todas las solicitudes no autenticadas se redirijan a Azure Active Directory para la autenticación.
6. Haga clic en **Guardar**.

Ahora está preparado para usar Azure Active Directory para realizar la autenticación en la aplicación.

## <a name="advanced"> </a>(Método alternativo) Configuración manual de Azure Active Directory con la configuración avanzada
También puede elegir proporcionar los valores de configuración manualmente. Esta es la solución preferida si el inquilino de AAD que desea usar es diferente de aquel con el que inicia sesión en Azure. Para completar la configuración, primero debe crear un registro en Azure Active Directory y luego proporcionar algunos de los detalles de registro al Servicio de aplicaciones.

### <a name="register"> </a>Registro de la aplicación con Azure Active Directory
1. Inicie sesión en el [Portal de Azure]y vaya a la aplicación. Copie el valor de **Dirección URL**. Se usará para configurar la aplicación de Azure Active Directory.
2. Inicie sesión en el [Portal de Azure clásico] y vaya a **Active Directory**.
   
    ![][2]
3. Seleccione el directorio y, a continuación, seleccione la pestaña **Aplicaciones** en la parte superior. Haga clic en **AGREGAR** en la parte inferior para crear un nuevo registro de aplicación.
4. Haga clic en **Agregar una aplicación que mi organización está desarrollando**.
5. En el Asistente para agregar aplicaciones, escriba el **nombre** de la aplicación y haga clic en el tipo **Aplicación web y/o API web**. A continuación, haga clic para continuar.
6. En el cuadro **URL DE INICIO DE SESIÓN** , pegue la dirección URL que copió anteriormente. Escriba esa misma dirección URL en el cuadro **URI de id. de aplicación** . A continuación, haga clic para continuar.
7. Una vez que se haya agregado la aplicación, haga clic en la pestaña **Configurar** . Edite el valor de **URL de respuesta**, que encontrará en **Inicio de sesión único**, para que sea la dirección URL de la aplicación anexada con la ruta de acceso, */.auth/login/aad/callback*. Por ejemplo: `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Asegúrese de que está utilizando el esquema HTTPS.
   
    ![][3]
8. Haga clic en **Guardar**. A continuación, copie **Id. de cliente** de la aplicación. Más adelante configurará la aplicación para usar este valor.
9. En la barra de comandos inferior, haga clic en **Ver extremos** y luego copie la dirección URL del **documento de metadatos de federación** y descargue dicho documento o navegue hasta él en un explorador.
10. En el elemento **EntityDescriptor** raíz, debe haber un atributo **entityID** del formulario `https://sts.windows.net/` seguido de un GUID específico para el inquilino (denominado "identificador de inquilino"). Copie este valor, actuará como **URL del emisor**. Más adelante configurará la aplicación para usar este valor.

### <a name="secrets"> </a>Incorporación de información de Azure Active Directory a la aplicación
1. Vuelva al [Portal de Azure]y vaya a la aplicación. Haga clic en **Configuración** y luego en **Autenticación/autorización**.
2. Si esta característica no está habilitada, mueva el interruptor a la posición de **activada**.
3. Haga clic en **Azure Active Directory** y luego haga clic en **Avanzada** en **Modo de administración**. Pegue el valor de id. de cliente y URL del emisor que obtuvo anteriormente. y, a continuación, haga clic en **Aceptar**.
   
   ![][1]
   
   De forma predeterminada, el Servicio de aplicaciones ofrece autenticación pero no restringe el acceso autorizado al contenido del sitio y a las API. Debe autorizar a los usuarios en el código de la aplicación.
4. (Opcional) Para restringir el acceso al sitio solo a los usuarios autenticados mediante Azure Active Directory, en **Acción necesaria cuando la solicitud no está autenticada**, seleccione **Iniciar sesión con Azure Active Directory**. Esto requiere que todas las solicitudes se autentiquen y que todas las solicitudes no autenticadas se redirijan a Azure Active Directory para la autenticación.
5. Haga clic en **Guardar**.

Ahora está preparado para usar Azure Active Directory para realizar la autenticación en la aplicación.

## <a name="optional-configure-a-native-client-application"></a>(Opcional) Configurar una aplicación de cliente nativo
Azure Active Directory también permite registrar a los clientes nativos, lo que proporciona un mayor control sobre la asignación de permisos. Esto es necesario si desea realizar inicios de sesión con una biblioteca como la **Biblioteca de autenticación de Active Directory**.

1. Vaya a **Active Directory** en el [Portal de Azure clásico].
2. Seleccione el directorio y, a continuación, seleccione la pestaña **Aplicaciones** en la parte superior. Haga clic en **AGREGAR** en la parte inferior para crear un nuevo registro de aplicación.
3. Haga clic en **Agregar una aplicación que mi organización está desarrollando**.
4. En el Asistente para agregar aplicaciones, escriba el **nombre** de la aplicación y haga clic en el tipo **Aplicación de cliente nativo**. A continuación, haga clic para continuar.
5. En el cuadro **URI de redirección**, especifique el punto de conexión */.auth/login/done* del sitio, con el esquema HTTPS. Este valor debe ser similar a *https://contoso.azurewebsites.net/.auth/login/done*. Si crea una aplicación de Windows, en su lugar, use el valor de [SID del paquete](app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) como URI.
6. Una vez que haya agregado la aplicación nativa, haga clic en la pestaña **Configurar** . Busque el **Identificador de cliente** y tome nota de este valor.
7. Desplácese hacia abajo por la página hasta la sección **Permisos para otras aplicaciones** y haga clic en **Agregar aplicación**.
8. Busque la aplicación web que registró y haga clic en el icono del signo de suma. A continuación, haga clic en la marca de verificación para cerrar el cuadro de diálogo. Si la aplicación web no se encuentra, vaya a su registro y agregue una nueva URL de respuesta (por ejemplo, la versión HTTP de la dirección URL actual), haga clic en Guardar y, a continuación, repita estos pasos; la aplicación debería aparecer en la lista.
9. En la entrada nueva que acaba de agregar, abra la lista desplegable **Permisos delegados** y seleccione **Acceso (nombreaplic)**. A continuación, haga clic en **Guardar**.

Ahora ha configurado una aplicación de cliente nativo que puede acceder a la aplicación del Servicio de aplicaciones.

## <a name="related-content"> </a>Contenido relacionado
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-express-settings.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-advanced-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[Portal de Azure]: https://portal.azure.com/
[Portal de Azure clásico]: https://manage.windowsazure.com/
[método alternativo]:#advanced



<!--HONumber=Nov16_HO3-->


