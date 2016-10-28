<properties
	pageTitle="Introducción a la autenticación de Aplicaciones móviles en la aplicación de Xamarin.Forms | Microsoft Azure"
	description="Obtenga información acerca de cómo utilizar Aplicaciones móviles para autenticar usuarios de la aplicación de Xamarin Forms a través de una variedad de proveedores de identidad, incluidos AAD, Google, Facebook, Twitter y Microsoft."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="wesmc"/>

# Agregar autenticación a la aplicación de Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

##Información general

En este tema se muestra cómo autenticar usuarios de una Aplicación móvil del Servicio de aplicaciones desde la aplicación cliente. En este tutorial podrá agregar autenticación al proyecto de inicio rápido de Xamarin.Forms mediante un proveedor de identidades compatible con Servicio de aplicaciones. Una vez que la aplicación móvil haya realizado la autenticación y la autorización correctamente, se mostrará el valor de identificador de usuario y podrá acceder a datos de tabla restringida.

##Requisitos previos

Para obtener el mejor resultado con este tutorial, se recomienda completar primero el tutorial [Creación de una aplicación Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md). Después de completar este tutorial, tendrá un proyecto de Xamarin.Forms que es una aplicación TodoList multiplataforma.

Si no usa el proyecto de servidor de inicio rápido descargado, debe agregar el paquete de extensión de autenticación al proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Registro de la aplicación para la autenticación y configuración de Servicios de aplicaciones

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##Restricción de los permisos para los usuarios autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]


##Agregar autenticación a la biblioteca de clases portables

Aplicaciones móviles usa el método de extensión [LoginAsync] en [MobileServiceClient] para iniciar una sesión de usuario con autenticación del Servicio de aplicaciones. Este ejemplo usa un flujo de autenticación administrado por servidor que muestra la interfaz de inicio de sesión del proveedor en la aplicación. Para más información, consulte [Autenticación administrada por el servidor](app-service-mobile-dotnet-how-to-use-client-library.md#serverflow). Para proporcionar una mejor experiencia del usuario en la aplicación de producción, puede considerar usar en su lugar [Autenticación administrada por el cliente](app-service-mobile-dotnet-how-to-use-client-library.md#clientflow).

Para autenticarse con un proyecto de Xamarin.Forms, se define una interfaz **IAuthenticate** en la biblioteca de clases portable para la aplicación. También se actualiza la interfaz de usuario definida en la biblioteca de clases portable para agregar un botón **Inicio de sesión**, en el que el usuario hace clic para iniciar la autenticación. Tras la autenticación correcta, los datos se cargan desde el back-end de aplicación móvil.

Debe implementar la interfaz **IAuthenticate** para cada plataforma admitida por la aplicación.


1. En Visual Studio o Xamarin Studio, abra App.cs desde el proyecto con **Portable** en el nombre, que es el proyecto de biblioteca de clases portable, y agregue las siguientes instrucciones `using`:

		using System.Threading.Tasks;

2. En el archivo App.cs, agregue la siguiente definición de interfaz `IAuthenticate` inmediatamente antes de la definición de la clase `App`.

	    public interface IAuthenticate
	    {
	        Task<bool> Authenticate();
	    }

3. Agregue los siguientes miembros estáticos a la clase **App** para inicializar la interfaz con una implementación específica de la plataforma.

	    public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }

4. Abra TodoList.xaml desde el proyecto de biblioteca de clases portable y agregue el siguiente elemento **Button** en el elemento de diseño *buttonsPanel*, después del botón existente:

      	<Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30" 
			Clicked="loginButton_Clicked"/>

	Este botón desencadena una autenticación administrada por el servidor con el back-end de aplicación móvil.

5. Abra TodoList.xaml.cs desde el proyecto de biblioteca de clases portable y agregue el siguiente campo a la clase `TodoList`:

		// Track whether the user has authenticated. 
        bool authenticated = false;


6. Reemplace el método **OnAppearing** por el código siguiente:

	    protected override async void OnAppearing()
	    {
	        base.OnAppearing();
	
	        // Refresh items only when authenticated.
	        if (authenticated == true)
	        {
	            // Set syncItems to true in order to synchronize the data 
	            // on startup when running in offline mode.
	            await RefreshItems(true, syncItems: false);

				// Hide the Sign-in button.
                this.loginButton.IsVisible = false;
	        }
	    }

	Esto asegura que los datos solo se actualicen desde el servicio una vez que se haya autenticado el usuario.

7. Agregue el siguiente controlador para el evento **Clicked** a la clase **TodoList**:

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }

8. Guarde los cambios, vuelva a compilar el proyecto de biblioteca de clases portable y compruebe que no haya errores.


##Agregar autenticación a la aplicación de Android

En esta sección se muestra cómo implementar la interfaz **IAuthenticate** en el proyecto de aplicación de Android. Omita esta sección si no ofrece compatibilidad con dispositivos Android.

1. En Visual Studio o Xamarin Studio, haga clic con el botón derecho en el proyecto **droid** y haga clic en **Establecer como proyecto de inicio**.

2. Presione F5 para iniciar el proyecto en el depurador y después compruebe que, después de iniciarse la aplicación, se genera una excepción no controlada con el código de estado 401 (No autorizado). Esto sucede porque el acceso en el back-end está restringido únicamente a los usuarios autorizados.

3. Abra MainActivity.cs en el proyecto de Android y agregue las siguientes instrucciones `using`:

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. Actualice la clase **MainActivity** para implementar la interfaz **IAuthenticate**, como sigue:

		public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate


5. Actualice la clase **MainActivity** con la adición de un campo **MobileServiceUser** y un método **Authenticate**, necesario para la interfaz **IAuthenticate**, como sigue:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this,
                    MobileServiceAuthenticationProvider.Facebook);
                if (user != null)
                {
                    message = string.Format("you are now signed-in as {0}.",
                        user.UserId);
                    success = true;
                }
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display the success or failure message.
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(message);
            builder.SetTitle("Sign-in result");
            builder.Create().Show();

            return success;
        }


	Si usa un proveedor de identidades que no sea Facebook, elija un valor diferente para [MobileServiceAuthenticationProvider].

6. Agregue el siguiente código al método **onCreate** de la clase **MainActivity** antes de la llamada a `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

	Esto asegura que se inicialice el autenticador antes de que se cargue la aplicación.

7. Vuelva a compilar la aplicación, ejecútela, inicie sesión con el proveedor de autenticación que eligió y compruebe que pueda acceder a los datos como usuario autenticado.

##Agregar autenticación a la aplicación de iOS

En esta sección se muestra cómo implementar la interfaz **IAuthenticate** en el proyecto de aplicación de iOS. Omita esta sección si no ofrece compatibilidad con dispositivos iOS.

1. En Visual Studio o Xamarin Studio, haga clic con el botón derecho en el proyecto **iOS** y haga clic en **Establecer como proyecto de inicio**.

2. Presione F5 para iniciar el proyecto en el depurador y después compruebe que, después de iniciarse la aplicación, se genera una excepción no controlada con el código de estado 401 (No autorizado). Esto sucede porque el acceso en el back-end está restringido únicamente a los usuarios autorizados.

4. Abra el archivo AppDelegate.cs en el proyecto de iOS y agregue las siguientes instrucciones `using`:

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. Actualice la clase **AppDelegate** para implementar la interfaz **IAuthenticate**, como sigue:

		public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate

5. Actualice la clase **AppDelegate** con la adición de un campo **MobileServiceUser** y un método **Authenticate**, necesario para la interfaz **IAuthenticate**, como sigue:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                        success = true;                        
                    }
                }        
            }
            catch (Exception ex)
            {
               message = ex.Message;
            }

            // Display the success or failure message.
            UIAlertView avAlert = new UIAlertView("Sign-in result", message, null, "OK", null);
            avAlert.Show();         

            return success;
        }

	Si usa un proveedor de identidades que no sea Facebook, elija un valor diferente para [MobileServiceAuthenticationProvider].

6. Agregue la siguiente línea de código al método **FinishedLaunching** antes de la llamada a `LoadApplication()`:

        App.Init(this);

	Esto asegura que se inicialice el autenticador antes de que se cargue la aplicación.

7. Vuelva a compilar la aplicación, ejecútela, inicie sesión con el proveedor de autenticación que eligió y compruebe que pueda acceder a los datos como usuario autenticado.


##Incorporación de la autenticación a proyectos de aplicación de Windows

En esta sección se muestra cómo implementar la interfaz **IAuthenticate** en proyectos de aplicación de Windows 8.1 y Windows Phone 8.1. Los mismos pasos valen para los proyectos de Plataforma universal de Windows (UWP). Omita esta sección si no ofrece compatibilidad con dispositivos Windows.

1. En Visual Studio, haga clic con el botón derecho en el proyecto **WinApp** o **WinPhone81**, y haga clic en **Establecer como proyecto de inicio**.

2. Presione F5 para iniciar el proyecto en el depurador y después compruebe que, después de iniciarse la aplicación, se genera una excepción no controlada con el código de estado 401 (No autorizado). Esto sucede porque el acceso en el back-end está restringido únicamente a los usuarios autorizados.

3. Abra el archivo MainPage.xaml.cs en el proyecto de aplicación de Windows y agregue las siguientes instrucciones `using`:

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;
		using Windows.UI.Popups;
		using <your_Portable_Class_Library_namespace>;

	Reemplace `<your_Portable_Class_Library_namespace>` por el espacio de nombres de la biblioteca de clases portable.

4. Actualice la clase **MainPage** para implementar la interfaz **IAuthenticate**, como sigue:

	    public sealed partial class MainPage : IAuthenticate


5. Actualice la clase **MainPage** con la adición de un campo **MobileServiceUser** y un método **Authenticate**, necesario para la interfaz **IAuthenticate**, como sigue:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            string message = string.Empty;
            var success = false;

            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
						success = true;
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                    }
                }
                
            }
            catch (Exception ex)
            {
                message = string.Format("Authentication Failed: {0}", ex.Message);
            }

			// Display the success or failure message.
            await new MessageDialog(message, "Sign-in result").ShowAsync();

            return success;
        }


	Si usa un proveedor de identidades que no sea Facebook, elija un valor diferente para [MobileServiceAuthenticationProvider].

6. Agregue la siguiente línea de código en el constructor para la clase **MainPage** antes de la llamada a `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);
 
    Reemplace `<your_Portable_Class_Library_namespace>` por el espacio de nombres de la biblioteca de clases portable. Si se trata del proyecto WinApp, puede continuar directamente en el paso 8. El siguiente paso solo se aplica al proyecto WinPhone81, donde debe completar la devolución de llamada de inicio de sesión.

7. (Opcional) En el proyecto de aplicación **WinPhone81**, abra App.xaml.cs y agregue las siguientes instrucciones `using`:

		using Microsoft.WindowsAzure.MobileServices;
		using <your_Portable_Class_Library_namespace>;

	Reemplace `<your_Portable_Class_Library_namespace>` por el espacio de nombres de la biblioteca de clases portable.

8.  Agregue el siguiente reemplazo del método **OnActivated** a la clase **App**:

		protected override void OnActivated(IActivatedEventArgs args)
		{
		    base.OnActivated(args);

			// We just need to handle activation that occurs after web authentication. 
		    if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
		    {
				// Get the client and call the LoginComplete method to complete authentication.
		        var client = TodoItemManager.DefaultManager.CurrentClient as MobileServiceClient;
		        client.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
		    }
		}

	Cuando el reemplazo del método ya existe, basta con agregar el código condicional del fragmento de código anterior.

7. Vuelva a compilar la aplicación, ejecútela, inicie sesión con el proveedor de autenticación que eligió y compruebe que pueda acceder a los datos como usuario autenticado.

##Pasos siguientes

Ahora que ha completado este tutorial de autenticación básica, considere la posibilidad de continuar con uno de los siguientes tutoriales:

+ [Agregar notificaciones push a la aplicación](app-service-mobile-xamarin-forms-get-started-push.md) Aprenda a agregar a la aplicación compatibilidad con notificaciones push y a configurar su back-end de aplicación móvil para que use Centros de notificaciones de Azure para enviar notificaciones push.

+ [Enable offline sync for your app](app-service-mobile-xamarin-forms-get-started-offline-data.md) (Habilitación de la sincronización sin conexión para su aplicación): aprenda a agregar compatibilidad sin conexión a su aplicación con un back-end de aplicación móvil. La sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil (ver, agregar o modificar datos), incluso cuando no hay ninguna conexión de red.

<!-- Images. -->

<!-- URLs. -->
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[MobileServiceClient]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx

<!---HONumber=AcomDC_0629_2016-->