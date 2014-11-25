<properties linkid="develop-mobile-how-to-guides-register-for-microsoft-waad-authentication" urlDisplayName="Register for Azure Active Directory Authentication" pageTitle="Register for Azure Active Directory authentication - Mobile Services" metaKeywords="Azure registering application, Azure Active Directory authentication, application authenticate, authenticate mobile services" description="Learn how to register for Azure Active Directory authentication in your Azure Mobile Services application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Register your apps to use an Azure Active Directory Account login" authors="dwrede" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="dwrede" />

# Registro de las aplicaciones para usar un inicio de sesión de la cuenta de Azure Active Directory

Este tema indica cómo registrar las aplicaciones para que puedan usar Azure Active Directory como proveedor de autenticación para los Servicios móviles de Azure.

<div class="dev-callout"><b>Nota:</b>
<p>Si desea proporcionar autenticaci&oacute;n centrada en el cliente para inicio de sesi&oacute;n &uacute;nico (SSO) o insertar notificaciones de una aplicaci&oacute;n de la Tienda Windows, considere la opci&oacute;n de registrar su aplicaci&oacute;n con la Tienda Windows. Para obtener m&aacute;s informaci&oacute;n, consulte <a href="/es-es/develop/mobile/how-to-guides/register-for-single-sign-on">Registro de sus aplicaciones de la Tienda Windows para la autenticaci&oacute;n de Live Connect de Windows</a>.</p>
</div>

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure].

2.  Diríjase a **Active Directory** en el portal de administración y haga clic en el directorio.

  ![][0]

1.  Haga clic en la pestaña **Aplicaciones** y después haga clic en **Add an App**.

  ![][1]

1.  Siga las indicaciones del asistente para la nueva aplicación eligiendo **Web Application And/Or Web API** para XXX. Habilite el inicio de sesión único. Cuando se le solicite la **dirección URL de la aplicación**, pegue la dirección URL de la aplicación de servicios móviles.

2. **MÁS INFORMACIÓN PRÓXIMAMENTE**

De este modo ya estará listo para usar una cuenta Azure Active Directory para autenticarse en su aplicación proporcionando los valores de identificador de cliente y secreto de cliente a Servicios móviles.





  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-app-active-directory-login/mobile-services-live-connect-add-app.png
  [1]: ./media/mobile-services-app-active-directory-login/mobile-live-connect-app-api-settings.png
