<properties linkid="develop-mobile-how-to-guides-register-for-active-directory-authentication" urlDisplayName="Register for Azure Active Directory Authentication" pageTitle="Register for Azure Active Directory authentication - Mobile Services" metaKeywords="Azure registering application, Azure Active Directory authentication, application authenticate, authenticate mobile services" description="Learn how to register for Azure Active Directory authentication in your Mobile Services application." title="Register your account to use an Azure Active Directory account login" authors="wesmc" services="mobile-services" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Registro de las aplicaciones para usar un inicio de sesión de la cuenta de Azure Active Directory

Este tema indica cómo registrar las aplicaciones para que puedan usar Azure Active Directory como proveedor de autenticación para los Servicios móviles de Azure.

> [WACOM.NOTE] Si desea proporcionar una autenticación impulsada por clientes para el inicio de sesión único (SSO) con Azure Active Directory, consulte el tutorial [Autenticación de la aplicación con el inicio de sesión único de la biblioteca de autenticación de Active Directory][Autenticación de la aplicación con el inicio de sesión único de la biblioteca de autenticación de Active Directory].

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, haga clic en el servicio móvil.

    ![][0]

2.  Haga clic en la pestaña **Identity** del servicio móvil.

    ![][1]

3.  Desplácese hasta la sección del proveedor de identidades de **Azure Active Directory** y copie el valor de la **Dirección URL de la aplicación** que aparece ahí.

    ![][2]

4.  Diríjase a **Active Directory** en el portal de administración y haga clic en el directorio.

    ![][3]

5.  Haga clic en la pestaña **Aplicaciones** que aparece en la parte superior y, a continuación, haga clic en **ADD** para agregar una aplicación.

    ![][4]

6.  Haga clic en **Agregar una aplicación que mi organización está desarrollando**.

7.  En el asistente para agregar aplicaciones, escriba el valor de **Nombre** para la aplicación y haga clic en el tipo **Aplicación web y/o API web**. A continuación, haga clic para continuar.

    ![][5]

8.  En el cuadro **Dirección URL de inicio de sesión**, pegue el identificador de la aplicación que copió de la configuración del proveedor de identidades de Active Directory del servicio móvil. Especifique un identificador de recurso único en el cuadro **URI de id. de aplicación**. A continuación, haga clic para continuar.

    ![][6]

9.  Una vez que se haya agregado la aplicación, haga clic en la pestaña **Configurar**. A continuación, haga clic para copiar el **Id. de cliente** de la aplicación.

    Si creó el servicio móvil para que utilice el back-end de .NET del servicio móvil, edite además el valor de **Dirección URL de respuesta** bajo **Inicio de sesión único** de modo que sea la dirección URL del servicio móvil con la ruta de acceso *signin-aad*anexada. Por ejemplo, `https://todolist.azure-mobile.net/signin-aad`

    ![][7]

10. Vuelva a la pestaña **Identidad** del servicio móvil. En la parte inferior, pegue la configuración de **Id. de cliente** para el proveedor de identidades de Azure Active Directory.

11. En la lista **Inquilinos permitidos** debe agregar el dominio del directorio en el que registró la aplicación (por ejemplo, contoso.onmicrosoft.com). Para buscar el nombre de dominio predeterminado, haga clic en la pestaña **Dominios** en Active Directory.

    ![][8]

    Agregue el nombre de dominio a la lista **Inquilinos permitidos** y haga clic en **Guardar**.

    ![][9]

Ahora está preparado para usar Azure Active Directory para realizar la autenticación en la aplicación.

 
 


  [Autenticación de la aplicación con el inicio de sesión único de la biblioteca de autenticación de Active Directory]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication/
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-selection.png
  [1]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-identity-tab.png
  [2]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-copy-app-url-waad-auth.png
  [3]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-select-ad-waad-auth.png
  [4]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-waad-idenity-tab-selection.png
  [5]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-1-waad-auth.png
  [6]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-2-waad-auth.png
  [7]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-waad-auth.png
  [8]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-default-domain.png
  [9]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-pasted-waad-auth.png
