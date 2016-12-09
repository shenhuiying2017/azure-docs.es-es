---
title: "Introducción a la autenticación de Mobile Apps en la aplicación de Xamarin Forms | Microsoft Docs"
description: "Obtenga información acerca de cómo utilizar Aplicaciones móviles para autenticar usuarios de la aplicación de Xamarin Forms a través de una variedad de proveedores de identidad, incluidos AAD, Google, Facebook, Twitter y Microsoft."
services: app-service\mobile
documentationcenter: xamarin
author: adrianhall
manager: dwrede
editor: 
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 65c8ff42c9c34eb51cb26153eff9b45aa0926838


---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Agregue autenticación a su aplicación de Xamarin Forms
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Información general
En este tema se muestra cómo autenticar usuarios de una Aplicación móvil del Servicio de aplicaciones desde la aplicación cliente. En este tutorial se agrega autenticación al proyecto de inicio rápido de Xamarin Forms mediante un proveedor de identidades compatible con App Service. Una vez que la aplicación móvil haya realizado la autenticación y la autorización correctamente, se mostrará el valor de identificador de usuario y podrá acceder a datos de tabla restringida.

## <a name="prerequisites"></a>Requisitos previos
Para obtener el mejor resultado en este tutorial, se recomienda completar primero el tutorial [Creación de una aplicación Xamarin.Forms][1]. Después de completar este tutorial, tendrá un proyecto de Xamarin Forms que es una aplicación TodoList multiplataforma.

Si no usa el proyecto de servidor de inicio rápido descargado, debe agregar el paquete de extensión de autenticación al proyecto. Para más información acerca de los paquetes de extensión del servidor, consulte [Trabajar con el SDK del servidor back-end de .NET para Azure Mobile Apps][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registro de la aplicación para la autenticación y configuración de Servicios de aplicaciones
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="restrict-permissions-to-authenticated-users"></a>Restricción de los permisos para los usuarios autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Agregar autenticación a la biblioteca de clases portables
Mobile Apps usa el método de extensión [LoginAsync][3] en [MobileServiceClient][4] para iniciar la sesión de un usuario con autenticación de App Service. Este ejemplo usa un flujo de autenticación administrado por servidor que muestra la interfaz de inicio de sesión del proveedor en la aplicación. Para más información, consulte [Autenticación administrada por el servidor][5]. Para proporcionar una mejor experiencia del usuario en la aplicación de producción, se recomienda considerar la posibilidad de usar [autenticación administrada por el cliente][6].

Para realizar la autenticación con un proyecto de Xamarin Forms, defina en la biblioteca de clases portable una interfaz **IAuthenticate** para la aplicación. Luego, agregue un botón **Inicio de sesión** a la interfaz de usuario definida en la biblioteca de clases portable, en el que se haga clic para iniciar la autenticación. Tras una autenticación correcta, los datos se cargan desde el back-end de la aplicación móvil.

Implemente la interfaz **IAuthenticate** en cada plataforma que admitida la aplicación.

1. En Visual Studio o Xamarin Studio, abra App.cs desde el proyecto con **Portable** en el nombre, que es el proyecto de la biblioteca de clases portable, y agregue las siguientes instrucción `using`:
   
        using System.Threading.Tasks;
2. En el archivo App.cs, agregue la siguiente definición de interfaz `IAuthenticate` inmediatamente antes de la definición de la clase `App`.
   
        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. Para inicializar la interfaz con una implementación específica de la plataforma, agregue los siguientes miembros estáticos a la clase **App**.
   
        public static IAuthenticate Authenticator { get; private set; }
   
        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Abra TodoList.xaml desde el proyecto de biblioteca de clases portable y agregue el siguiente elemento **Button** en el elemento de diseño *buttonsPanel* , después del botón existente:
   
          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>
   
    Este botón desencadena una autenticación administrada por el servidor con el back-end de aplicación móvil.
5. Abra TodoList.xaml.cs desde el proyecto de biblioteca de clases portable y agregue el siguiente campo a la clase `TodoList` :
   
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
   
    Este código garantiza que los datos solo se actualizan desde el servicio una vez que se haya realizado la autenticación.
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

## <a name="add-authentication-to-the-android-app"></a>Agregar autenticación a la aplicación de Android
En esta sección se muestra cómo implementar la interfaz **IAuthenticate** en el proyecto de aplicación de Android. Omita esta sección si no ofrece compatibilidad con dispositivos Android.

1. En Visual Studio o Xamarin Studio, haga clic con el botón derecho en el proyecto **droid** y luego en **Establecer como proyecto de inicio**.
2. Presione F5 para iniciar el proyecto en el depurador y después compruebe que, después de iniciarse la aplicación, se genera una excepción no controlada con el código de estado 401 (No autorizado). El código 401 aparece porque el acceso en el back-end está restringido únicamente a usuarios autorizados.
3. Abra MainActivity.cs en el proyecto de Android y agregue las siguientes instrucciones `using`:
   
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Actualice la clase **MainActivity** para implementar la interfaz **IAuthenticate**, como sigue:
   
        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Actualice la clase **MainActivity**, para lo que debe agregar un campo **MobileServiceUser** y un método **Authenticate**, lo que necesita la interfaz **IAuthenticate**, como se indica a continuación:
   
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

    Si usa un proveedor de identidades que no sea Facebook, elija otro valor para [MobileServiceAuthenticationProvider][7].

1. Agregue el siguiente código al método **onCreate** de la clase **MainActivity** antes de la llamada a `LoadApplication()`:
   
        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);
   
    Este código garantiza que el autenticador se inicializa antes de que se cargue la aplicación.
2. Vuelva a compilar la aplicación, ejecútela, inicie sesión con el proveedor de autenticación que elija y compruebe que pueda acceder a los datos como usuario autenticado.

## <a name="add-authentication-to-the-ios-app"></a>Agregar autenticación a la aplicación de iOS
En esta sección se muestra cómo implementar la interfaz **IAuthenticate** en el proyecto de aplicación de iOS. Omita esta sección si no ofrece compatibilidad con dispositivos iOS.

1. En Visual Studio o Xamarin Studio, haga clic con el botón derecho en el proyecto **iOS** y luego en **Establecer como proyecto de inicio**.
2. Presione F5 para iniciar el proyecto en el depurador y después compruebe que, después de iniciarse la aplicación, se genera una excepción no controlada con el código de estado 401 (No autorizado). La respuesta 401 aparece porque el acceso en el back-end está restringido únicamente a usuarios autorizados.
3. Abra el archivo AppDelegate.cs en el proyecto de iOS y agregue las siguientes instrucciones `using`:
   
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Actualice la clase **AppDelegate** para implementar la interfaz **IAuthenticate** como se indica a continuación:
   
        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Actualice la clase **AppDelegate**, para lo que debe agregar un campo **MobileServiceUser** y un método **Authenticate**, lo que necesita la interfaz **IAuthenticate**, como se indica a continuación:
   
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
   
    Si usa un proveedor de identidades que no sea Facebook, elija otro valor para [MobileServiceAuthenticationProvider].
6. Agregue la siguiente línea de código al método **FinishedLaunching** antes de la llamada a `LoadApplication()`:
   
        App.Init(this);
   
    Este código garantiza que el autenticador se inicializa antes de que se cargue la aplicación.
7. Vuelva a compilar la aplicación, ejecútela, inicie sesión con el proveedor de autenticación que elija y compruebe que pueda acceder a los datos como usuario autenticado.

## <a name="add-authentication-to-windows-81-including-phone-app-projects"></a>Adición de autenticación a proyectos de aplicaciones de Windows 8.1 (incluyendo Phone)
En esta sección se muestra cómo implementar la interfaz **IAuthenticate** en proyectos de aplicación de Windows 8.1 y Windows Phone 8.1. Los mismos pasos se aplican a los proyectos de plataforma universal de Windows (UWP), pero se usa el proyecto **UWP** (con cambios anotados). Omita esta sección si no ofrece compatibilidad con dispositivos Windows.

1. En Visual Studio, haga clic con el botón derecho en los proyectos **WinApp** o **WinPhone81**, y luego en **Establecer como proyecto de inicio**.
2. Presione F5 para iniciar el proyecto en el depurador y después compruebe que, después de iniciarse la aplicación, se genera una excepción no controlada con el código de estado 401 (No autorizado). La respuesta 401 de produce porque el acceso en el back-end está restringido únicamente a usuarios autorizados.
3. Abra el archivo MainPage.xaml.cs en el proyecto de aplicación de Windows y agregue las siguientes instrucciones `using` :
   
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;
   
    Reemplace `<your_Portable_Class_Library_namespace>` por el espacio de nombres de la biblioteca de clases portable.
4. Actualice la clase **MainPage** para implementar la interfaz **IAuthenticate** como se indica a continuación:
   
        public sealed partial class MainPage : IAuthenticate
5. Actualice la clase **MainPage**, para lo que debe agregar un campo **MobileServiceUser** y un método **Authenticate**, lo que necesita la interfaz **IAuthenticate**, como se indica a continuación:
   
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

    Si usa un proveedor de identidades que no sea Facebook, elija otro valor para [MobileServiceAuthenticationProvider].

1. Agregue la siguiente línea de código en el constructor para la clase **MainPage** antes de la llamada a `LoadApplication()`:
   
        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);
   
    Reemplace `<your_Portable_Class_Library_namespace>` por el espacio de nombres de la biblioteca de clases portable.
   
    Si va a modificar el proyecto WinApp, diríjase al paso 8. El siguiente paso solo se aplica al proyecto WinPhone81, donde debe completar la devolución de llamada de inicio de sesión.
2. (Opcional) En el proyecto de la aplicación **WinPhone81**, abra App.xaml.cs y agregue las siguientes instrucciones `using`:
   
        using Microsoft.WindowsAzure.MobileServices;
        using <your_Portable_Class_Library_namespace>;
   
    Reemplace `<your_Portable_Class_Library_namespace>` por el espacio de nombres de la biblioteca de clases portable.
3. Si utiliza **WinPhone81** o **WinApp**, agregue el siguiente reemplazo del método **OnActivated** a la clase **App**:
   
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
   
   Si dicho reemplazo ya existe, agregue el código condicional del fragmento de código anterior.  Este código no es necesario para los proyectos de Windows universal.
4. Vuelva a compilar la aplicación, ejecútela, inicie sesión con el proveedor de autenticación que elija y compruebe que pueda acceder a los datos como usuario autenticado.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha completado este tutorial de autenticación básica, considere la posibilidad de continuar con uno de los siguientes tutoriales:

* [Agregar notificaciones push a la aplicación](app-service-mobile-xamarin-forms-get-started-push.md)
  
  : aprenda a agregar a la aplicación compatibilidad con notificaciones push y a configurar su back-end de aplicación móvil para usar centros de notificaciones de Azure para enviar notificaciones push.
* [Activación de la sincronización sin conexión para la aplicación de Windows](app-service-mobile-xamarin-forms-get-started-offline-data.md)
  
  Aprenda a agregar compatibilidad sin conexión a una aplicación con un back-end de aplicación móvil. La sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil (ver, agregar o modificar datos), incluso cuando no haya conexión de red.

<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-xamarin-forms-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[4]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[5]: app-service-mobile-dotnet-how-to-use-client-library.md#serverflow
[6]: app-service-mobile-dotnet-how-to-use-client-library.md#clientflow
[7]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx



<!--HONumber=Nov16_HO3-->


