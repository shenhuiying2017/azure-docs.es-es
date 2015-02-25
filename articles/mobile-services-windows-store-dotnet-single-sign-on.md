<properties pageTitle="Autenticación de una aplicación de la Tienda Windows con Live Connect" description="Obtenga información acerca de cómo usar el inicio de sesión único de Live Connect en Servicios móviles de Azure desde una aplicación de la Tienda Windows." services="mobile-services" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="11/22/2014" ms.author="glenga"/>

# Autenticación de una aplicación de la Tienda Windows con el inicio de sesión único de Live Connect
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on/" title="Windows Store C#" class="current">C# para Tienda Windows</a><a href="/es-es/documentation/articles/mobile-services-windows-store-javascript-single-sign-on/" title="Windows Store JavaScript">JavaScript para Tienda Windows</a><a href="/es-es/documentation/articles/mobile-services-windows-phone-single-sign-on/" title="Windows Phone">Windows Phone</a>
</div>	

En este tema se muestra cómo utilizar el inicio de sesión único de Live Connect para autenticar a los usuarios en Servicios móviles de Azure desde una aplicación de la Tienda Windows o de la Tienda de Windows Phone 8.1.  En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante Live Connect. Una vez que Live Connect haya realizado la autenticación correctamente, se dará la bienvenida al usuario que ha iniciado sesión por su nombre y se mostrará el valor de identificador de usuario.  

>[AZURE.NOTE]Este tutorial muestra las ventajas de usar la experiencia de inicio de sesión único que ofrece Live Connect para las aplicaciones de la Tienda Windows. Esto le permite autenticar más fácilmente a un usuario que ya haya iniciado sesión en el servicio móvil. Para ver una experiencia de autenticación más generalizada que admita varios proveedores de autenticación, consulte el tema <a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/">Introducción a la autenticación</a>. 

Este tutorial le guiará a través de estos pasos básicos para habilitar la autenticación de Live Connect:

1. [Registro de la aplicación para la autenticación y configuración de Servicios móviles]
2. [Restricción de los permisos de tabla a usuarios autenticados]
3. [Incorporación de autenticación a la aplicación]

Este tutorial requiere lo siguiente:

+ [SDK de Live para Windows]
+ Microsoft Visual Studio 2012 Express para Windows 8 RC o una versión posterior.
+ Primero debe completar el tutorial [Incorporación de servicios móviles a una aplicación existente].

##<a name="register"></a>Registro de la aplicación para la Tienda Windows

Para poder autenticar a los usuarios, debe enviar la aplicación a la Tienda Windows. A continuación, debe registrar el secreto de cliente para integrar Live Connect en Servicios móviles.

[AZURE.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

##<a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

<ol start="3">
<li><p>En Visual Studio 2012 Express para Windows 8, abra el proyecto que creó cuando completó el tutorial <a href="/es-es/documentation/articles/mobile-services-windows-store-get-started">Introducción a los Servicios móviles</a>.</p></li> 
<li><p>Presione la tecla F5 para ejecutar esta aplicación basada en el inicio rápido; compruebe que se lleva a cabo una excepción no controlada con el código de estado 401 (No autorizado) después de que se inicie la aplicación.</p>
   
   	<p>Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla <em>TodoItem</em> requiere ahora autenticación.</p></li>
</ol>

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

##<a name="add-authentication"></a>Incorporación de autenticación a la aplicación

1. Descargue e instale el [SDK de Live para Windows].

2. En el menú **Proyecto** de Visual Studio, haga clic en **Agregar referencia**, a continuación expanda **Windows**, haga clic en **Extensiones**, active la casilla **SDK de Live** y, a continuación, haga clic en **Aceptar**. 

  	![][16]

  	De esta forma se agrega al proyecto una referencia al SDK de Live.

5. Abra el archivo de proyecto MainPage.xaml.cs y agregue la siguiente instrucción using:

        using Microsoft.Live;        

6. Agregue el siguiente fragmento de código a la clase MainPage:
	
        private LiveConnectSession session;
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            LiveAuthClient liveIdClient = new LiveAuthClient("<< INSERT REDIRECT DOMAIN HERE >>");

            while (session == null)
            {
                // Force a logout to make it easier to test with multiple Microsoft Accounts
                if (liveIdClient.CanLogout)
                    liveIdClient.Logout();
	
                LiveLoginResult result = await liveIdClient.LoginAsync(new[] { "wl.basic" });
                if (result.Status == LiveConnectSessionStatus.Connected)
                {
                    session = result.Session;
                    LiveConnectClient client = new LiveConnectClient(result.Session);
                    LiveOperationResult meResult = await client.GetAsync("me");
                    MobileServiceUser loginResult = await App.MobileService
                        .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);
	
                    string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                    var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                    var dialog = new MessageDialog(message, title);
                    dialog.Commands.Add(new UICommand("OK"));
                    await dialog.ShowAsync();
                }
                else
                {
                    session = null;
                    var dialog = new MessageDialog("You must log in.", "Login Required");
                    dialog.Commands.Add(new UICommand("OK"));
                    await dialog.ShowAsync();
                }
            }
         }

    De esta forma se crea una variable de miembro para el almacenamiento de la sesión de Live Connect actual y un método para gestionar el proceso de autenticación. Este código fuerza un cierre de sesión, cuando sea posible, para asegurarse de que se solicitan las credenciales al usuario cada vez que se ejecute la aplicación. Esto hace más fácil probar la aplicación con diversas cuentas Microsoft a fin de asegurarse de que la autenticación funciona correctamente. Este mecanismo solo funcionará si el usuario que ha iniciado sesión no tiene una cuenta Microsoft conectada. 

	>[AZURE.NOTE]No debería solicitar los tokens de autenticación de Live Connection o los tokens de autorización de Servicios móviles cada vez que se ejecute la aplicación. Esto no solo es ineficaz, sino que también puede enfrentarse a problemas relacionados con el uso si varios clientes inician la aplicación al mismo tiempo. Un mejor método sería almacenar en caché los tokens y primero intentar usar el token de Servicios móviles almacenado en caché antes de llamar a **LoginWithMicrosoftAccountAsync**. Para obtener un ejemplo de cómo almacenar este token en caché, consulte [Introducción a la autenticación].(/es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/#tokens)
	

7. Actualice la cadena _<< INSERT REDIRECT DOMAIN HERE >>_ del paso anterior con el dominio de redirección que se especificó al configurar la aplicación en Live Connect, con el formato **https://_service-name_.azure-mobile.net/**.

    > [AZURE.NOTE] En una aplicación de la Tienda Windows, para crear una instancia de la clase <strong>LiveAuthClient</strong> se pasa el valor del URI del dominio de redirección al constructor de clases. En una [aplicación de Windows Phone 8](/es-es/develop/mobile/tutorials/single-sign-on-wp8/), para crear instancias de la misma clase se pasa el identificador de cliente.

8. Reemplace el controlador de eventos **OnNavigatedTo** existente por el controlador que llama al nuevo método **Authenticate**:

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();
            RefreshTodoItems();
        }
		
9. Presione la tecla F5 para ejecutar la aplicación e iniciar sesión en Live Connect con su cuenta Microsoft. 

   Cuando haya iniciado sesión correctamente, la aplicación debe ejecutarse sin errores y debe poder consultar a Servicios móviles y realizar actualizaciones de datos.

## <a name="next-steps"> </a>Pasos siguientes

En el siguiente tutorial, [Autorización de usuarios con scripts], usará el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles. Para obtener información sobre cómo usar otros proveedores de identidades para la autenticación, consulte [Introducción a la autenticación]. Obtenga más información sobre cómo usar Servicios móviles con .NET en [Referencia conceptual de Servicios móviles con .NET].

<!-- Anchors. -->
[Registro de la aplicación para la autenticación y configuración de Servicios móviles]: #register
[Restricción de los permisos de tabla a usuarios autenticados]: #permissions
[Incorporación de autenticación a la aplicación]: #add-authentication
[Pasos siguientes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-store-association.png
[3]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-select-app-name.png
[4]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-selection.png
[5]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-service-uri.png
[6]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-live-connect-apps-list.png
[7]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-live-connect-app-api-settings.png





[13]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-identity-tab-ma-only.png
[14]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-portal-data-tables.png
[15]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-portal-change-table-perms.png
[16]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-add-reference-live-dotnet.png

<!-- URLs. -->
[Página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[SDK de Live para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Incorporación de Servicios móviles a una aplicación existente]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-users
[Autorización de usuarios con scripts]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts/

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Referencia conceptual de Servicios móviles con .NET]: /es-es/develop/mobile/how-to-guides/work-with-net-client-library


<!--HONumber=42-->
