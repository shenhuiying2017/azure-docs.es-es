<properties
	pageTitle="Incorporación de autenticación a la aplicación de la Plataforma universal de Windows (UWP) | Aplicaciones móviles de Azure"
	description="Obtenga información acerca de cómo usar Aplicaciones móviles del Servicio de aplicaciones de Azure para autenticar a los usuarios de la aplicación de la Plataforma universal de Windows (UWP) en una variedad de proveedores de identidades, incluidos AAD,Google, Facebook, Twitter y Microsoft."
	services="app-service\mobile"
	documentationCenter="windows"
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="05/14/2016"
	ms.author="glenga"/>

# Incorporación de la autenticación a la aplicación de Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

En este tema se muestra cómo agregar la autenticación basada en la nube a su aplicación móvil. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido de la Plataforma universal de Windows (UWP) para Aplicaciones móviles mediante un proveedor de identidades compatible con el Servicio de aplicaciones de Azure. Una vez que el back-end de la aplicación móvil haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.

Este tutorial se basa en el inicio rápido de aplicaciones móviles. Primero debe completar el tutorial [Introducción a las aplicaciones móviles](app-service-mobile-windows-store-dotnet-get-started.md).

##<a name="register"></a>Registro de la aplicación para la autenticación y configuración del Servicio de aplicaciones

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Ahora, puede comprobar que se deshabilitó el acceso anónimo a su back-end. Con el proyecto de aplicación para UWP configurado como proyecto de inicio, implemente y ejecute la aplicación; compruebe que, cuando esta se inicia, se genera una excepción no controlada con el código de estado 401 (No autorizado). Esto se produce porque la aplicación intenta obtener acceso a su Código de aplicación móvil como usuario sin autenticar, pero la tabla *TodoItem* requiere ahora autenticación.

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del Servicio de aplicaciones.

##<a name="add-authentication"></a>Incorporación de autenticación a la aplicación

1. En el archivo de proyecto de aplicación para UWP MainPage.cs, agregue el siguiente fragmento de código a la clase MainPage:
	
		// Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;

        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now signed in - {0}", user.UserId);

                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }

    Este código autentica al usuario con un inicio de sesión de Facebook. Si está usando un proveedor de identidades diferente al de Facebook, cambie el valor de **MobileServiceAuthenticationProvider** anterior por el valor de su proveedor.

3. Elimine o convierta en comentario la llamada al método **ButtonRefresh\_Click** (o al método **InitLocalStoreAsync**) en el reemplazo del método **OnNavigatedTo** existente. Esto impide que los datos se carguen antes de que el usuario se haya autenticado. A continuación, agregará un botón **Iniciar sesión** a la aplicación que desencadena la autenticación.

4. Agregue el siguiente fragmento de código a la clase MainPage:

	    private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
	    {
	        // Login the user and then load data from the mobile app.
	        if (await AuthenticateAsync())
	        {
	            // Switch the buttons and load items from the mobile app.
	            ButtonLogin.Visibility = Visibility.Collapsed;
	            ButtonSave.Visibility = Visibility.Visible;
	            //await InitLocalStoreAsync(); //offline sync support.
	            await RefreshTodoItems();
	        }
	    }
		
5. Abra el archivo de proyecto MainPage.xaml, busque el elemento que define el botón **Guardar** y reemplácelo por el código siguiente:

        <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
				Click="ButtonSave_Click">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Add"/>
                <TextBlock Margin="5">Save</TextBlock>
            </StackPanel>
        </Button>
        <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
                Click="ButtonLogin_Click" TabIndex="0">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Permissions"/>
                <TextBlock Margin="5">Sign in</TextBlock> 
            </StackPanel>
        </Button>

9. Presione la tecla F5 para ejecutar la aplicación, haga clic en el botón **Iniciar sesión** e inicie sesión en la aplicación con el proveedor de identidad que haya elegido. Una vez iniciada la sesión correctamente, la aplicación se ejecutará sin errores y podrá consultar al back-end y realizar actualizaciones de datos.


##<a name="tokens"></a>Almacenamiento del token de autorización en el cliente

En el ejemplo anterior se mostró un inicio de sesión estándar, que requiere que el cliente se ponga en contacto con el proveedor de identidades y con el servicio de la aplicación cada vez que se inicia la aplicación. Este método no solo es ineficaz, sino que también puede enfrentarse a problemas relacionados con el uso si varios clientes inician la aplicación al mismo tiempo. Un método mejor es almacenar en caché el token de autorización devuelto por su servicio de aplicación e intentar usarlo primero antes de utilizar un inicio de sesión basado en proveedores.

>[AZURE.NOTE]Puede almacenar en caché el token emitido por los servicios de aplicaciones con independencia de si es una autenticación administrada por el cliente o por el servicio. Este tutorial utiliza la autenticación administrada por el servicio.

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

##Pasos siguientes

Ahora que ha completado este tutorial de autenticación básica, considere la posibilidad de continuar con uno de los siguientes tutoriales:

+ [Agregar notificaciones push a su aplicación Windows](app-service-mobile-windows-store-dotnet-get-started-push.md) Obtenga información sobre cómo agregar compatibilidad a las notificaciones push a la aplicación y cómo configurar su back-end de aplicación móvil para usar centros de notificaciones de Azure para enviar notificaciones push.

+ [Habilitar la sincronización sin conexión para su aplicación Windows](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) Obtenga información sobre cómo agregar compatibilidad sin conexión a su aplicación con un back-end de aplicación móvil. La sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil (ver, agregar o modificar datos), incluso cuando no hay ninguna conexión de red.


<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md

<!---HONumber=AcomDC_0518_2016-->