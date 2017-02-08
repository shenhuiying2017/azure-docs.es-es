---
title: "Habilitación de la publicación de aplicaciones de cliente nativas con aplicaciones proxy | Microsoft Docs"
description: "Explica cómo habilitar las aplicaciones cliente nativas para comunicarse con el conector del proxy de la aplicación de Azure AD para proporcionar acceso remoto seguro a las aplicaciones locales."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: f0cae145-e346-4126-948f-3f699747b96e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: e65393c9582056f84530a32804e0d82fd451b688
ms.openlocfilehash: 34cacff4e8b13fa9d91387ca8762439908ed01fd


---
# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>Habilitación de las aplicaciones cliente nativas para interactuar con el proxy de la aplicación
El proxy de la aplicación de Azure Active Directory se usa por lo general para publicar aplicaciones de explorador como SharePoint, Outlook Web Access y aplicaciones de línea de negocio personalizadas. También puede utilizarse para publicar aplicaciones de cliente nativo que se diferencian de las aplicaciones web porque se instalan en un dispositivo. Esto se lleva a cabo mediante la compatibilidad de tokens emitidos por Azure AD que se envían en encabezados HTTP de autorización estándar.

![Relación entre los usuarios finales, Azure Active Directory y las aplicaciones publicadas](./media/active-directory-application-proxy-native-client/richclientflow.png)

El método recomendado para publicar estas aplicaciones es usar Azure AD Authentication Library, que se encarga de todos los problemas de autenticación y admite muchos entornos de cliente diferentes. El proxy de la aplicación se adapta a la [aplicación nativa para el escenario de Web API](develop/active-directory-authentication-scenarios.md#native-application-to-web-api). El proceso para llevarlo a cabo es el siguiente:

## <a name="step-1-publish-your-application"></a>Paso 1: Publicación de la aplicación
Publique su aplicación proxy, al igual que haría con cualquier otra aplicación, asigne los usuarios y concédales licencias básicas o premium. Para obtener más información, consulte [Publicación de aplicaciones con el proxy de la aplicación](active-directory-application-proxy-publish.md).

## <a name="step-2-configure-your-application"></a>Paso 2: Configuración de la aplicación
Configure su aplicación nativa de la manera siguiente:

1. Inicie sesión en el portal clásico de Azure.
2. Seleccione el icono de Active Directory en el menú de la izquierda y luego su directorio.
3. En el menú superior, haga clic en **Aplicaciones**. Si no se han agregado aplicaciones al directorio, esta página solo muestra el vínculo **Agregar una aplicación** . Haga clic en el vínculo; como alternativa, puede hacer clic en el botón **Agregar** de la barra de comandos.
4. En la página **¿Qué desea hacer?**, haga clic en el vínculo para **Agregar una aplicación que mi organización está desarrollando**.
5. En la página **Proporcione información sobre su aplicación**, especifique un nombre para la aplicación y elija **Aplicación de cliente nativo**. Haga clic en el icono de flecha para continuar.
6. En la página **Información de la aplicación**, proporcione el **URI de redirección** para la aplicación cliente nativa y haga clic en la marca de verificación para finalizar.

La aplicación se agrega y le llevará a la página Inicio rápido de la aplicación.

## <a name="step-3-grant-access-to-other-applications"></a>Paso 3: Conceder acceso a otras aplicaciones
Habilite la aplicación nativa para que se exponga a otras aplicaciones en el directorio:

1. En el menú superior, haga clic en **Aplicaciones**, seleccione la nueva aplicación nativa y haga clic en **Configurar**.
2. Desplácese hacia abajo hasta la sección **Permisos para otras aplicaciones** . Haga clic en el botón **Agregar aplicación** y seleccione la aplicación proxy a la que desea conceder acceso a la aplicación nativa y haga clic en la marca de verificación de la esquina inferior derecha. En el menú desplegable **Permisos delegados** , seleccione el nuevo permiso.

![Captura de pantalla de Permisos para otras aplicaciones - agregar aplicación](./media/active-directory-application-proxy-native-client/delegate_native_app.png)

## <a name="step-4-edit-the-active-directory-authentication-library"></a>Paso 4: Editar la biblioteca de autenticación de Active Directory
Edite el código de la aplicación nativa en el contexto de autenticación de Active Directory Authentication Library (ADAL) para incluir lo siguiente:

        // Acquire Access Token from AAD for Proxy Application
        AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<TenantId>");
        AuthenticationResult result = authContext.AcquireToken("< Frontend Url of Proxy App >",
                                                        "< Client Id of the Native app>",
                                                        new Uri("< Redirect Uri of the Native App>"),
                                                        PromptBehavior.Never);

        //Use the Access Token to access the Proxy Application
        HttpClient httpClient = new HttpClient();
        httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
        HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");

Las variables deben reemplazarse como sigue:

* **TenantId** puede encontrarse en el GUID de la dirección URL de la página **Configuración** del solicitante, justo después de “/Directory/”.
* **Frontend URL** es la dirección URL de front-end especificada en la aplicación proxy y se encuentra en la página **Configuración** de la aplicación proxy.
* **Client Id** de la aplicación nativa se encuentra en la página **Configurar** de la aplicación nativa.
* **Redirect URI of the native app** se puede encontrar en la página**Configurar** de la aplicación nativa.

![Captura de pantalla de la página de configuración de la nueva aplicación nativa](./media/active-directory-application-proxy-native-client/new_native_app.png)

Para más información sobre el flujo de la aplicación nativa, consulte el escenario de [Aplicación nativa a API web](develop/active-directory-authentication-scenarios.md#native-application-to-web-api).

## <a name="see-also"></a>Consulte también
* [Publicar aplicaciones mediante su propio nombre de dominio](active-directory-application-proxy-custom-domains.md)
* [Habilitar el acceso condicional](active-directory-application-proxy-conditional-access.md)
* [Trabajar con las aplicaciones para notificaciones](active-directory-application-proxy-claims-aware-apps.md)
* [Habilitar el inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)

Para ver las últimas noticias y actualizaciones, consulte el [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/)




<!--HONumber=Jan17_HO3-->


