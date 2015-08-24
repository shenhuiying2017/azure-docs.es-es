<properties 
	pageTitle="Autenticación de una aplicación de la Tienda Windows con Live Connect" 
	description="Obtenga información acerca de cómo usar el inicio de sesión único de Live Connect en Servicios móviles de Azure desde una aplicación de la Tienda Windows." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/08/2015" 
	ms.author="glenga"/>

# Autenticación de una aplicación de la Tienda Windows con la autenticación del cliente administrada mediante la cuenta de Microsoft

[AZURE.INCLUDE [mobile-services-selector-single-signon](../../includes/mobile-services-selector-single-signon.md)]

##Información general
En este tema se muestra cómo obtener un token de autenticación de cuenta de Microsoft mediante el SDK de Live desde una aplicación de Windows universal. A continuación, use este token para autenticar usuarios con Servicios móviles de Azure. En este tutorial, podrá agregar la autenticación de cuenta de Microsoft a un proyecto existente mediante el SDK de Live. Una vez que haya realizado la autenticación correctamente, se dará la bienvenida al usuario que ha iniciado sesión por su nombre y se mostrará el valor de identificador de usuario.

>[AZURE.NOTE]Este tutorial muestra las ventajas de usar autenticación dirigida al cliente y el SDK de Live. Esto le permite autenticar más fácilmente a un usuario que ya haya iniciado sesión en el servicio móvil. También puede solicitar otros ámbitos para habilitar la aplicación para tener acceso a recursos, como OneDrive. La autenticación dirigida al servicio proporciona una experiencia más generalizada y admite varios proveedores de autenticación. Para obtener más información acerca de la autenticación dirigida al servicio, consulte el tema [Incorporación de la autenticación a la aplicación](mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md).

Este tutorial requiere lo siguiente:

+ [SDK de Live]
+ Microsoft Visual Studio 2013 Update 3 o una versión posterior.
+ Primero debe completar también los tutoriales [Introducción a Servicios móviles](mobile-services-javascript-backend-windows-store-dotnet-get-started.md) o [Agregar Servicios móviles a una aplicación existente].

##Registre la aplicación para usar la cuenta Microsoft para realizar la autenticación

Para autenticar usuarios, debe registrar la aplicación en el Centro para desarrolladores de la cuenta de Microsoft. A continuación, debe conectarse este registro con el servicio móvil. Complete los pasos descritos en el tema siguiente para crear un registro de la cuenta de Microsoft y conectarla al servicio móvil.

+ [Registre sus aplicaciones para usar un inicio de sesión de la cuenta Microsoft](mobile-services-how-to-register-microsoft-authentication.md)

##<a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

A continuación se debe restringir el acceso a un recurso, en este caso la tabla *TodoItems* para asegurarse de que solo sean accesibles para un usuario con sesión iniciada.

[AZURE.INCLUDE [mobile-services-restrict-permissions-windows](../../includes/mobile-services-restrict-permissions-windows.md)]

##<a name="add-authentication"></a>Incorporación de autenticación a la aplicación

Por último, agregue el SDK de Live y úselo para autenticar a los usuarios en la aplicación.

1. En el **Explorador de soluciones**, haga clic con el botón derecho en la solución y, a continuación, haga clic en **Administrar paquetes de NuGet**.

2. En el panel izquierdo, seleccione la categoría **En línea**, busque **LiveSDK**, haga clic en **Instalar** en el paquete del **SDK de Live**, seleccione todos los proyectos y, a continuación, acepte los contratos de licencia.

  	Esto agrega el SDK de Live a la solución.

3. Abra el archivo de proyecto compartido MainPage.xaml.cs y agregue la siguiente instrucción using:

        using Microsoft.Live;        

4. Agregue el siguiente fragmento de código a la clase **MainPage**:
	
        private LiveConnectSession session;
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {

            // Get the URL the mobile service.
            var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

            // Create the authentication client using the mobile service URL.
            LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);

            while (session == null)
            {
                // Request the authentication token from the Live authentication service.
				// The wl.basic scope is requested.
                LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
                if (result.Status == LiveConnectSessionStatus.Connected)
                {
                    session = result.Session;

                    // Get information about the logged-in user.
                    LiveConnectClient client = new LiveConnectClient(session);
                    LiveOperationResult meResult = await client.GetAsync("me");

                    // Use the Microsoft account auth token to sign in to Mobile Services.
                    MobileServiceUser loginResult = await App.MobileService
                        .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                    // Display a personalized sign-in greeting.
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
    
    De esta forma se crea una variable de miembro para el almacenamiento de la sesión de Live Connect actual y un método para gestionar el proceso de autenticación.

	>[AZURE.NOTE]Debe intentar usar tokens de autenticación en Live almacenados en la memoria caché o tokens de autorización de servicios móviles antes de solicitar nuevos tokens de los servicios. Si no lleva a cabo esto, es posible que se tenga que enfrentar a problemas relacionados con el uso si varios clientes inician la aplicación al mismo tiempo. Para obtener un ejemplo de cómo almacenar este token en caché, consulte [Introducción a la autenticación](../mobile-services-windows-store-dotnet-get-started-users.md#tokens).

5. Elimine o convierta en comentario la llamada al método **RefreshTodoItems** en el reemplazo del método **OnNavigatedTo** existente.

	Esto impide que los datos se carguen antes de que el usuario se haya autenticado. A continuación, agregará un botón para iniciar el proceso de inicio de sesión.

6. Agregue el siguiente fragmento de código a la clase MainPage:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            await RefreshTodoItems();
        }
		
7. En el proyecto de aplicación de la Tienda Windows, abra el archivo de proyecto MainPage.xaml y agregue el siguiente elemento **Botón** inmediatamente antes del elemento que define el botón **Guardar**:

		<Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>

8. Repita el paso anterior para el proyecto de aplicación de la Tienda de Windows Phone, pero esta vez agregue el **Botón** en el **TitlePanel**, después del elemento **TextBlock**.
		
9. Presione la tecla F5 para ejecutar la aplicación e iniciar sesión en Live Connect con su cuenta Microsoft.

	Cuando haya iniciado sesión correctamente, la aplicación debe ejecutarse sin errores y debe poder consultar a Servicios móviles y realizar actualizaciones de datos.

10. Haga clic con el botón secundario en el proyecto de aplicación de la Tienda Windows Phone, haga clic en **Establecer como proyecto de inicio** y repita el paso anterior para comprobar que la aplicación de la Tienda Windows Phone también se ejecuta correctamente.

Ahora, cualquier usuario autenticado por uno de los proveedores de identidad registrados puede tener acceso a la tabla *TodoItem*. Para proteger mejor datos específicos del usuario, también debe implementar la autorización. Para ello obtendrá el identificador de usuario de un usuario determinado, que puede utilizarse para determinar el nivel de acceso que el usuario debe tener para un recurso determinado.

## <a name="next-steps"> </a>Pasos siguientes

En el siguiente tutorial, [Autorización de usuarios con scripts], usará el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles. Para obtener información sobre cómo usar otros proveedores de identidades para la autenticación, consulte [Introducción a la autenticación]. Obtenga más información sobre cómo usar Servicios móviles con .NET en [Referencia conceptual de Servicios móviles con .NET].

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Agregar Servicios móviles a una aplicación existente]: ../mobile-services-windows-store-dotnet-get-started-data.md
[Introducción a la autenticación]: ../mobile-services-windows-store-dotnet-get-started-users.md
[Autorización de usuarios con scripts]: mobile-services-javascript-backend-service-side-authorization.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Referencia conceptual de Servicios móviles con .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md
[SDK de Live]: http://go.microsoft.com/fwlink/p/?LinkId=262253
 

<!---HONumber=August15_HO7-->