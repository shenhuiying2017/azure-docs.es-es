<properties 
	pageTitle="Registro para la autenticación de Active Directory de Azure - servicios móviles" 
	description="Obtenga información acerca de cómo registrarse para la autenticación de Azure Active Directory en la aplicación de Servicios móviles de Azure." 
	authors="wesmc7777" 
	services="mobile-services" 
	documentationCenter="" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="wesmc"/>

# Registro de las aplicaciones para usar un inicio de sesión de la cuenta de Azure Active Directory
##Información general


Este tema indica cómo registrar las aplicaciones para que puedan usar Azure Active Directory como proveedor de autenticación para los Servicios móviles de Azure. 

##Registrar la aplicación

>[AZURE.NOTE] Los pasos descritos en este tema están diseñados para utilizarse con el tutorial [Incorporación de autenticación a la aplicación de Servicios móviles] (/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/) cuando desee usar [operaciones de inicio de sesión dirigidas por el servicio](http://msdn.microsoft.com/library/azure/dn283952.aspx) con su aplicación. Como alternativa, si la aplicación tiene un requisito de [operaciones de inicio de sesión dirigidas por el cliente](http://msdn.microsoft.com/library/azure/jj710106.aspx) para Azure Active Directory y un servicio móvil de back-end de .NET, debe comenzar por el tutorial [Autenticación de la aplicación con el inicio de sesión único de la biblioteca de autenticación de Active Directory](/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication/).


1. Inicie sesión en el [Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, haga clic en el servicio móvil.

    ![][1]

2. Haga clic en la pestaña **Identidad** de su servicio móvil. 

    ![][2]

3. Desplácese hasta la sección del proveedor de identidades de **Azure Active Directory** y copie el valor de la **Dirección URL de la aplicación** que aparece ahí.

    ![][3]

4. Diríjase a **Active Directory** en el portal de administración y haga clic en el directorio.

    ![][4] 

5. Haga clic en la pestaña **Aplicaciones** que aparece en la parte superior y, a continuación, haga clic en **AGREGAR** para agregar una aplicación. 

    ![][10]

6. Haga clic en **Agregar una aplicación que mi organización está desarrollando**.

7. En el asistente para agregar aplicaciones, escriba el valor de **Nombre** para la aplicación y haga clic en el tipo **Aplicación web y/o API web**. A continuación, haga clic para continuar.

    ![][5]

8. En el cuadro **Dirección URL de inicio de sesión**, pegue el identificador de la aplicación que copió de la configuración del proveedor de identidades de Active Directory del servicio móvil. Especifique un identificador de recurso único en el cuadro **URI de id. de aplicación**. A continuación, haga clic para continuar.
 
    ![][6]


9. Una vez que se haya agregado la aplicación, haga clic en la pestaña **Configurar**. A continuación, haga clic para copiar el **Id. de cliente** de la aplicación.

    Si creó el servicio móvil para que utilice el back-end de .NET del servicio móvil, edite además el valor de **Dirección URL de respuesta** bajo **Inicio de sesión único** de modo que sea la dirección URL del servicio móvil con la ruta de acceso, _signin-aad_. Por ejemplo, `https://todolist.azure-mobile.net/signin-aad`

    ![][8]


10. Vuelva a la pestaña **Identidad** del servicio móvil. En la parte inferior, pegue la configuración de **Id. de cliente** para el proveedor de identidades de Azure Active Directory.

  
11. En la lista **Inquilinos permitidos** debe agregar el dominio del directorio en el que registró la aplicación (por ejemplo, contoso.onmicrosoft.com). Para buscar el nombre de dominio predeterminado, haga clic en la pestaña **Dominios** de Active Directory.

    ![][11]
 
    Agregue el nombre de dominio a la lista **Inquilinos permitidos** y haga clic en **Guardar**.    


    ![][9]



Ahora está preparado para usar Azure Active Directory para realizar la autenticación en la aplicación. 



<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-selection.png
[2]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-identity-tab.png
[3]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-copy-app-url-waad-auth.png
[4]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-select-ad-waad-auth.png
[5]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-1-waad-auth.png
[6]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-2-waad-auth.png
[7]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-3-waad-auth.png
[8]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-waad-auth.png
[9]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-pasted-waad-auth.png
[10]:./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-waad-idenity-tab-selection.png
[11]:./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-default-domain.png

<!-- URLs. -->
[Portal de administración de Azure]: https://manage.windowsazure.com/


<!--HONumber=47-->
