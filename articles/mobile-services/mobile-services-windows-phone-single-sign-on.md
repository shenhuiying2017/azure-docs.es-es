<properties 
	pageTitle="Autenticación de la aplicación con Live Connect | Centro de desarrollo móvil" 
	description="Obtenga información acerca de cómo usar el inicio de sesión único de Live Connect en Servicios móviles de Azure desde una aplicación de Windows Phone." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/09/2015" 
	ms.author="glenga"/>

# Autenticación de una aplicación de Windows Phone con la autenticación del cliente administrada mediante la cuenta de Microsoft

[AZURE.INCLUDE [mobile-services-selector-single-signon](../../includes/mobile-services-selector-single-signon.md)]
##Información general
En este tema se muestra cómo obtener un token de autenticación de cuenta de Microsoft mediante el SDK de Live desde una aplicación de Windows Phone 8 o Windows Phone 8.1 Silverlight. A continuación, use este token para autenticar usuarios con Servicios móviles de Azure. En este tutorial, podrá agregar la autenticación de cuenta de Microsoft a un proyecto existente mediante el SDK de Live. Una vez que haya realizado la autenticación correctamente, se dará la bienvenida al usuario que ha iniciado sesión por su nombre y se mostrará el valor de identificador de usuario.

>[AZURE.NOTE]Este tutorial muestra las ventajas de usar autenticación administrada por el cliente y el SDK de Live. Esto le permite autenticar más fácilmente a un usuario que ya haya iniciado sesión en el servicio móvil. También puede solicitar otros ámbitos para habilitar la aplicación para tener acceso a recursos, como OneDrive. La autenticación administrada por servicio proporciona una experiencia más generalizada y admite varios proveedores de autenticación. Para obtener más información acerca de la autenticación administrada de servicio, consulte el tema [Incorporación de la autenticación a la aplicación](mobile-services-windows-phone-get-started-users.md).

Este tutorial requiere lo siguiente:

+ [SDK de Live]
+ Microsoft Visual Studio 2013 Update 3 o una versión posterior.
+ Primero debe completar el tutorial [Incorporación de Servicios móviles a una aplicación existente].

##Registre la aplicación para usar la cuenta Microsoft 

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

5. Abra el archivo de proyecto mainpage.xaml.cs y agregue las siguientes instrucciones using:

        using Microsoft.Live;      

6. Agregue el siguiente fragmento de código a la clase MainPage:
	
        private LiveConnectSession session;
        private static string clientId = "<microsoft-account-client-id>";
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            // Create the authentication client using the client ID of the registration.
            LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

            while (session == null)
            {
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
                    MessageBox.Show(message, title, MessageBoxButton.OK);
                }
                else
                {
                    session = null;
                    MessageBox.Show("You must log in.", "Login Required", MessageBoxButton.OK);
                }
            }
        }

    De esta forma se crea una variable de miembro para el almacenamiento de la sesión de Live Connect actual y un método para gestionar el proceso de autenticación. LiveLoginResult contiene el token de autenticación que se asigna a los Servicios móviles para autenticar al usuario.

7. En el fragmento de código anterior, reemplace el marcador de posición `<microsoft-account-client-id>` por el identificador de cliente que ha obtenido en el registro de la cuenta de Microsoft para su aplicación.

5. Elimine o convierta en comentario la llamada al método **RefreshTodoItems** en el reemplazo del método **OnNavigatedTo** existente.

	Esto impide que los datos se carguen antes de que el usuario se haya autenticado. A continuación, agregará un botón para iniciar el proceso de inicio de sesión.

6. Agregue el siguiente fragmento de código a la clase MainPage:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = System.Windows.Visibility.Collapsed;
            RefreshTodoItems();
        }
		
7. En el proyecto de la aplicación, abra el archivo de proyecto MainPage.xaml y agregue el siguiente elemento **botón** en el **TitlePanel**, después del elemento **TextBlock**:

		<Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>
		
9. Presione la tecla F5 para ejecutar la aplicación e iniciar sesión con su cuenta Microsoft.

   Cuando haya iniciado sesión correctamente, la aplicación debe ejecutarse sin errores y debe poder consultar a Servicios móviles y realizar actualizaciones de datos.

Ahora, cualquier usuario autenticado por uno de los proveedores de identidad registrados puede tener acceso a la tabla *TodoItem*. Para proteger mejor datos específicos del usuario, también debe implementar la autorización. Para ello obtendrá el identificador de usuario de un usuario determinado, que puede utilizarse para determinar el nivel de acceso que el usuario debe tener para un recurso determinado.

## <a name="next-steps"> </a>Pasos siguientes

En el siguiente tutorial, [Autorización de usuarios con scripts], usará el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles. Para obtener información sobre cómo usar otros proveedores de identidades para la autenticación, consulte [Introducción a la autenticación].

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->


<!-- URLs. -->
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[SDK de Live]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Incorporación de Servicios móviles a una aplicación existente]: mobile-services-windows-phone-get-started-data.md
[Introducción a la autenticación]: mobile-services-windows-phone-get-started-users.md
[Autorización de usuarios con scripts]: ../mobile-services-windows-phone-authorize-users-in-scripts.md

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO4-->