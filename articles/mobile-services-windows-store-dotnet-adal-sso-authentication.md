<properties urlDisplayName="Active Directory SSO Authentication with ADAL" pageTitle="Autenticación de la aplicación con el inicio de sesión único de la biblioteca de autenticación de Active Directory (Tienda Windows) | Centro de desarrollo móvil" metaKeywords="" description="Obtenga información acerca de cómo autenticar usuarios para inicio de sesión único con ADAL en su aplicación de la Tienda Windows." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Authenticate your app with Active Directory Authentication Library Single Sign-On" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="10/14/2014" ms.author="wesmc" />

# Autenticación de la aplicación con el inicio de sesión único de la biblioteca de autenticación de Active Directory

[WACOM.INCLUDE [mobile-services-selector-adal-sso](../includes/mobile-services-selector-adal-sso.md)]

En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante la biblioteca de autenticación de Active Directory para admitir las [operaciones de inicio de sesión dirigidas por el cliente](http://msdn.microsoft.com/es-es/library/azure/jj710106.aspx) con Azure Active Directory. Para admitir las [operaciones de inicio de sesión dirigidas por el servicio](http://msdn.microsoft.com/es-es/library/azure/dn283952.aspx) con Azure Active Directory, comience con el tutorial [Incorporación de la autenticación a su aplicación de Servicios móviles](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/).

Para poder autenticar a los usuarios, debe registrar su aplicación en Azure Active Directory (AAD). Para ello, debe realizar dos pasos. Primero, debe registrar su servicio móvil y exponer los permisos sobre él. En segundo lugar, debe registrar su aplicación de la Tienda Windows y otorgar acceso a esos permisos.


>[AZURE.NOTE] Este tutorial está destinado a ayudarle a comprender mejor cómo los Servicios móviles le permiten realizar la autenticación de Azure Active Directory en un inicio de sesión único para aplicaciones de la Tienda Windows mediante una [operación de inicio de sesión dirigida por el cliente](http://msdn.microsoft.com/es-es/library/azure/jj710106.aspx). Si esta es la primera vez que usa los Servicios móviles, complete el tutorial [Introducción a Servicios móviles].

Este tutorial le guiará a través de estos pasos básicos:

1. [Registro del servicio móvil en Azure Active Directory]
2. [Registro de la aplicación en Azure Active Directory] 
3. [Configuración del servicio móvil para exigir autenticación]
4. [Incorporación de código de autenticación a la aplicación cliente]
5. [Prueba del cliente mediante autenticación]

Este tutorial requiere lo siguiente:

* Visual Studio 2013 en Windows 8.1.
* Finalización del tutorial [Introducción a Servicios móviles] o [Introducción a los datos].
* Paquete NuGet del SDK de Servicios móviles de Microsoft Azure
* Paquete NuGet de la biblioteca de autenticación de Active Directory 

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-service](../includes/mobile-services-dotnet-adal-register-service.md)]

## <a name="register-app-aad"></a>Registro de la aplicación en Azure Active Directory

Para registrar la aplicación con Azure Active Directory, debe asociarla a la Tienda Windows y tener un identificador de seguridad de paquete (SDI) para la aplicación. El SID del paquete se registra con la configuración de la aplicación nativa en Azure Active Directory.


### Asociación de la aplicación con un nuevo nombre de aplicación de la tienda

1. En Visual Studio, haga clic con el botón secundario en el proyecto de aplicación cliente y haga clic en **Tienda** y en **Asociar aplicación con la Tienda**

    ![][1]

2. Inicie sesión en su cuenta del Centro de desarrollo.

3. Escriba el nombre de la aplicación que desea reservar para esta y haga clic en **Reservar**.

    ![][2]

4. Seleccione el nombre de la aplicación nueva y haga clic en **Siguiente**.

5. Haga clic en **Asociar** para asociar la aplicación con el nombre de la tienda.


### Recuperación del SID del paquete para la aplicación

Ahora, debe recuperar el SID del paquete que se configurará con la configuración de la aplicación nativa.

1. Inicie sesión en su [panel del Centro de desarrollo de Windows] y haga clic en **Editar** en la aplicación.

    ![][3]

2. Luego haga clic en **Servicios**

    ![][4]

3. Después, haga clic en **sitio de Live Services**. 

    ![][5]

4. Copie el SID del paquete de la parte superior de la página.

    ![][6]

### Creación del registro de la aplicación nativa

1. Vaya a **Active Directory** en el [Portal de administración de Azure] y, a continuación, haga clic en el directorio.

    ![][7] 

2. Haga clic en la pestaña **Aplicaciones** que aparece en la parte superior y, a continuación, haga clic en **AGREGAR** una aplicación. 

    ![][8]

3. Haga clic en **Agregar una aplicación que mi organización está desarrollando**.

4. En el asistente para agregar aplicaciones, escriba un **nombre** para la aplicación y haga clic en el tipo **Aplicación de cliente nativo**. A continuación, haga clic para continuar.

    ![][9]

5. En el cuadro **URI de redirección**, pegue el SID del paquete de la aplicación que copió anteriormente y luego haga clic para completar el registro de la aplicación nativa.

    ![][10]

6. Haga clic en la pestaña **Configurar** de la aplicación nativa y copie el **Id. de cliente**. Lo necesitará más adelante.

    ![][11]

7. Desplácese hacia abajo, hasta la sección de **permisos para otras aplicaciones**, y conceda acceso total a la aplicación de servicio móvil que registró anteriormente. A continuación, haga clic en **Guardar**.

    ![][12]

El servicio móvil está ahora configurado en AAD para recibir inicios de sesión únicos desde su aplicación.



## <a name="require-authentication"></a>Configuración del servicio móvil para exigir autenticación

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-code"></a>Incorporación de código de autenticación a la aplicación cliente

1. Abra su proyecto de aplicación cliente de la Tienda Windows en Visual Studio.

[WACOM.INCLUDE [mobile-services-dotnet-adal-install-nuget](../includes/mobile-services-dotnet-adal-install-nuget.md)]

4. En la ventana del Explorador de soluciones de Visual Studio, abra el archivo MainPage.xaml.cs y agregue las siguientes instrucciones using:

        using Windows.UI.Popups;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Newtonsoft.Json.Linq;


5. Agregue el siguiente código a la clase MainPage que declara el método `AuthenticateAsync`.

        private MobileServiceUser user; 
        private async Task AuthenticateAsync()
        {
            string authority = "<INSERT-AUTHORITY-HERE>";
            string resourceURI = "<INSERT-RESOURCE-URI-HERE>";
            string clientID = "<INSERT-CLIENT-ID-HERE>"; 
            while (user == null)
            {
                string message;
                try
                {
                  AuthenticationContext ac = new AuthenticationContext(authority);
                  AuthenticationResult ar = await ac.AcquireTokenAsync(resourceURI, clientID, (Uri) null);
                  JObject payload = new JObject();
                  payload["access_token"] = ar.AccessToken;
                  user = await App.MobileService.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                  message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                  message = "You must log in. Login Required";
                } 
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            } 
        }

6. En el código para el método `AuthenticateAsync` anterior, reemplace **INSERT-AUTHORITY-HERE** por el nombre del inquilino en el que aprovisionó la aplicación; el formato debe ser https://login.windows.net/nombre-de-inquilino.onmicrosoft.com. Este valor se puede copiar de la pestaña Dominio en Azure Active Directory en el [Portal de administración de Azure].

7. En el código del método `AuthenticateAsync` anterior, reemplace **INSERT-RESOURCE-URI-HERE** por el **URI de Id. de aplicación** de su dispositivo móvil. Si ha seguido el tema [Registro en Azure Active Directory], el URI de Id. de aplicación debe ser similar a https://todolist.azure-mobile.net/login/aad.

8. En el código del método `AuthenticateAsync` anterior, reemplace **INSERT-CLIENT-ID-HERE** por el Id. de cliente que copió de la aplicación de cliente nativo.

9. En la ventana del Explorador de soluciones de Visual Studio, abra el archivo Package.appxmanifest en el proyecto cliente. Haga clic en la pestaña **Funcionalidades** y habilite **Aplicación de empresa** y **Redes privadas (cliente y servidor)**. Guarde el archivo.

    ![][14]

10. En el archivo MainPage.cs, actualice el controlador de eventos `OnNavigatedTo` para llamar al método `AuthenticateAsync` de la manera siguiente.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();
            await RefreshTodoItems();
        }


## <a name="test-client"></a>Prueba del cliente mediante autenticación

1. En Visual Studio, ejecute la aplicación cliente.
2. Recibirá un mensaje para que inicie sesión en Azure Active Directory.  
3. La aplicación se autentica y devuelve los elementos todo.

    ![][15]



<!-- Anchors. -->
[Registro del servicio móvil en Azure Active Directory]: #register-mobile-service-aad
[Registro de la aplicación en Azure Active Directory]: #register-app-aad
[Configuración del servicio móvil para exigir autenticación]: #require-authentication
[Servicio móvil de back-end de JavaScript]: #javascript-authentication
[Servició móvil de back-end de .NET]: #dotnet-authentication
[Incorporación de código de autenticación a la aplicación cliente]: #add-authentication-code
[Prueba del cliente mediante autenticación]: #test-client

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-aad-app-manage-manifest.png
[1]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-vs-associate-app.png
[2]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-vs-reserve-store-appname.png
[3]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-edit.png
[4]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-services.png
[5]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-live-services-site.png
[6]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-package-sid.png
[7]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-select-aad.png
[8]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-aad-applications-tab.png
[9]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-selection.png
[10]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-sid-redirect-uri.png
[11]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-client-id.png
[12]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-add-permissions.png
[14]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-package-appxmanifest.png
[15]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-app-run.png

<!-- URLs. -->
[Registro en Azure Active Directory]: /es-es/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Portal de administración de Azure]: https://manage.windowsazure.com/
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[Introducción a Servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[Panel del Centro de desarrollo de Windows]: http://go.microsoft.com/fwlink/p/?LinkID=266734
