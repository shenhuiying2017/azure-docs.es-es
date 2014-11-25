<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-dotnet" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your Windows Store app with Live Connect" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, mobile services sso, Windows Store app sso" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Store application." metaCanonical="" services="mobile-services" documentationCenter="" title="Authenticate your Windows Store app with Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Autenticación de una aplicación de la Tienda Windows con el inicio de sesión único de Live Connect

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/es-es/develop/mobile/tutorials/single-sign-on-windows-8-dotnet" title="C# para Tienda Windows" class="current">C# para Tienda Windows</a><a href="/es-es/develop/mobile/tutorials/single-sign-on-windows-8-js" title="JavaScript para Tienda Windows">JavaScript para Tienda Windows</a><a href="/es-es/develop/mobile/tutorials/single-sign-on-wp8" title="Windows Phone">Windows Phone</a>
</div>

En este tema se muestra cómo utilizar el inicio de sesión único de Live Connect para autenticar a los usuarios en Servicios móviles de Azure desde una aplicación de la Tienda Windows o de la Tienda Windows Phone 8.1. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante Live Connect. Una vez que Live Connect haya realizado la autenticación correctamente, se dará la bienvenida al usuario que ha iniciado sesión por su nombre y se mostrará el valor de identificador de usuario.

> [WACOM.NOTE] Este tutorial muestra las ventajas de usar la experiencia de inicio de sesión único que ofrece Live Connect para las aplicaciones de la Tienda Windows. Esto le permite autenticar más fácilmente a un usuario que ya haya iniciado sesión en el servicio móvil. Para ver una experiencia de autenticación más generalizada que admita varios proveedores de autenticación, consulte el tema [Introducción a la autenticación][Introducción a la autenticación].

Este tutorial le guiará a través de estos pasos básicos para habilitar la autenticación de Live Connect:

1.  [Registro de la aplicación para la autenticación y configuración de Servicios móviles][Registro de la aplicación para la autenticación y configuración de Servicios móviles]
2.  [Restricción de los permisos de tabla a usuarios autenticados][Restricción de los permisos de tabla a usuarios autenticados]
3.  [Incorporación de autenticación a la aplicación][Incorporación de autenticación a la aplicación]

Este tutorial requiere lo siguiente:

-   [SDK de Live para Windows][SDK de Live para Windows]
-   Microsoft Visual Studio 2012 Express para Windows 8 RC o una versión posterior.

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles][Introducción a los Servicios móviles].

> [WACOM.NOTE] Este tutorial usa un servicio móvil back-end de JavaScript. La autenticación administrada por clientes que usa Live Connect u otros clientes de autenticación todavía no se admite en un servicio móvil de back-end de .NET.

## <a name="register"></a>Registro de la aplicación para la Tienda Windows

Para poder autenticar a los usuarios, debe enviar la aplicación a la Tienda Windows. A continuación, debe registrar el secreto de cliente para integrar Live Connect en Servicios móviles.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

## <a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  En Visual Studio 2012 Express para Windows 8, abra el proyecto que creó cuando completó el tutorial [Introducción a los Servicios móviles][1].

2.  Presione la tecla F5 para ejecutar esta aplicación basada en el inicio rápido; compruebe que se lleva a cabo una excepción no controlada con el código de estado 401 (No autorizado) después de que se inicie la aplicación.

    Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla *TodoItem* requiere ahora autenticación.

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

## <a name="add-authentication"></a>Incorporación de autenticación a la aplicación

1.  Descargue e instale el [SDK de Live para Windows][SDK de Live para Windows].

2.  En el menú **Proyecto** de Visual Studio, haga clic en **Agregar referencia**, a continuación expanda **Windows**, haga clic en **Extensiones**, active la casilla **SDK de Live** y, a continuación, haga clic en **Aceptar**.

    ![][0]

    De esta forma se agrega al proyecto una referencia al SDK de Live.

3.  Abra el archivo de proyecto MainPage.xaml.cs y agregue la siguiente instrucción using:

        using Microsoft.Live;        

4.  Agregue el siguiente fragmento de código a la clase MainPage:

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

    > [WACOM.NOTE] No debería solicitar los tokens de autenticación de Live Connection o los tokens de autorización de Servicios móviles cada vez que se ejecute la aplicación. Esto no solo es ineficaz, sino que también puede enfrentarse a problemas relacionados con el uso si varios clientes inician la aplicación al mismo tiempo. Un mejor método sería almacenar en caché los tokens y primero intentar usar el token de Servicios móviles almacenado en caché antes de llamar a **LoginWithMicrosoftAccountAsync**. Para obtener un ejemplo de cómo almacenar este token en caché, consulte [Introducción a la autenticación][2].

5.  Actualice la cadena *\<\< INSERT REDIRECT DOMAIN HERE \>\>* del paso anterior con el dominio de redirección que se especificó al configurar la aplicación en Live Connect, con el formato **<https://_service-name_.azure-mobile.net/>**.

    <div class="dev-callout"><b>Nota:</b>
<p>En una aplicaci&oacute;n de la Tienda Windows, para crear una instancia de la clase <strong>LiveAuthClient</strong> se pasa el valor del URI del dominio de redirecci&oacute;n al constructor de clases. En una <a href="/es-es/develop/mobile/tutorials/single-sign-on-wp8/">aplicaci&oacute;n de Windows Phone 8</a>, para crear instancias de la misma clase se pasa el identificador de cliente.</p>
</div>

6.  Reemplace el controlador de eventos **OnNavigatedTo** existente por el controlador que llama al nuevo método **Authenticate**:

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();
            RefreshTodoItems();
        }

7.  Presione la tecla F5 para ejecutar la aplicación e iniciar sesión en Live Connect con su cuenta Microsoft.

	Cuando haya iniciado sesión correctamente, la aplicación debe ejecutarse sin errores y debe poder consultar a Servicios móviles y realizar actualizaciones de datos.

## <a name="next-steps"> </a>Pasos siguientes

En el siguiente tutorial, [Autorización de usuarios con scripts][Autorización de usuarios con scripts], usará el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles. Para obtener información sobre cómo usar otros proveedores de identidades para la autenticación, consulte [Introducción a la autenticación][3]. Obtenga más información sobre cómo usar Servicios móviles con .NET en [Referencia conceptual de Servicios móviles con .NET][Referencia conceptual de Servicios móviles con .NET].

 
 


  [Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [Registro de la aplicación para la autenticación y configuración de Servicios móviles]: #register
  [Restricción de los permisos de tabla a usuarios autenticados]: #permissions
  [Incorporación de autenticación a la aplicación]: #add-authentication
  [SDK de Live para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
  [Introducción a los Servicios móviles]: /es-es/develop/mobile/tutorials/get-started
  [1]: /es-es/documentation/articles/mobile-services-windows-store-get-started
  [0]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-add-reference-live-dotnet.png
  [2]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/#tokens
  [Autorización de usuarios con scripts]: /es-es/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
  [3]: /es-es/develop/mobile/tutorials/get-started-with-users-dotnet
  [Referencia conceptual de Servicios móviles con .NET]: /es-es/develop/mobile/how-to-guides/work-with-net-client-library
