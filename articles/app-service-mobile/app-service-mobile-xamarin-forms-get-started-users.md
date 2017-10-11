---
title: "Introducción a la autenticación de Mobile Apps en la aplicación de Xamarin Forms | Microsoft Docs"
description: "Obtenga información acerca de cómo utilizar Mobile Apps para autenticar usuarios de la aplicación de Xamarin Forms a través de una variedad de proveedores de identidad, incluidos AAD, Google, Facebook, Twitter y Microsoft."
services: app-service\mobile
documentationcenter: xamarin
author: panarasi
manager: syntaxc4
editor: 
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 08/07/2017
ms.author: panarasi
ms.openlocfilehash: 9e14e95793bcc81ad46783fd50ba223eec4ea360
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Agregue autenticación a su aplicación de Xamarin Forms
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Información general
En este tema se muestra cómo autenticar usuarios de una aplicación móvil de App Service desde la aplicación cliente. En este tutorial se agrega autenticación al proyecto de inicio rápido de Xamarin Forms mediante un proveedor de identidades compatible con App Service. Una vez que la aplicación móvil haya realizado la autenticación y la autorización correctamente, se mostrará el valor de identificador de usuario y podrá acceder a datos de tabla restringida.

## <a name="prerequisites"></a>Requisitos previos
Para obtener el mejor resultado con este tutorial, se recomienda completar primero el tutorial [Creación de una aplicación Xamarin.Forms][1]. Después de completar este tutorial, tendrá un proyecto de Xamarin Forms que es una aplicación TodoList multiplataforma.

Si no usa el proyecto de servidor de inicio rápido descargado, debe agregar el paquete de extensión de autenticación al proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte el artículo sobre cómo [trabajar con el SDK del servidor back-end de .NET para Azure Mobile Apps][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registro de la aplicación para la autenticación y configuración de App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adición de la aplicación a las direcciones URL de redirección externa permitidas

La autenticación segura requiere que se defina un nuevo esquema de dirección URL para la aplicación. Esto permite que el sistema de autenticación se redirija a la aplicación una vez completado el proceso de autenticación. En este tutorial, se usará el esquema de dirección URL _appname_. Sin embargo, puede utilizar cualquier otro esquema de dirección URL que elija. Debe ser único para la aplicación móvil. Para habilitar la redirección en el lado de servidor:

1. En [Azure Portal], seleccione App Service.

2. Haga clic en la opción de menú **Autenticación/autorización**.

3. En **URL de redirección externas permitidas**, introduzca `url_scheme_of_your_app://easyauth.callback`.  El valor de **esquema_de_dirección_URL_de_la_aplicación** de esta cadena es el esquema de dirección URL para la aplicación móvil.  Debe seguir la especificación normal de las direcciones URL para un protocolo (usar únicamente letras y números, y comenzar por una letra).  Debe tomar nota de la cadena que elija ya que necesitará ajustar el código de la aplicación móvil con el esquema de direcciones URL en varios sitios.

4. Haga clic en **Aceptar**.

5. Haga clic en **Guardar**.

## <a name="restrict-permissions-to-authenticated-users"></a>Restricción de los permisos para los usuarios autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Agregar autenticación a la biblioteca de clases portables
Mobile Apps usa el método de extensión [LoginAsync][3] en [MobileServiceClient][4] para iniciar una sesión de usuario con autenticación de App Service. Este ejemplo usa un flujo de autenticación administrado por servidor que muestra la interfaz de inicio de sesión del proveedor en la aplicación. Para obtener más información, consulte [Autenticación administrada por el servidor][5]. Para proporcionar una mejor experiencia del usuario en la aplicación de producción, se recomienda usar la [autenticación administrada por el cliente][6].

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
                    MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
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

6. Agregue el código siguiente dentro del nodo <application> de AndroidManifest.xml:

```xml
    <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
      <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
      </intent-filter>
    </activity>
```

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
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
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

6. Actualice la clase AppDelegate mediante la adición de la sobrecarga del método OpenUrl(UIApplication app, NSUrl url, NSDictionary options)

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }

6. Agregue la siguiente línea de código al método **FinishedLaunching** antes de la llamada a `LoadApplication()`:

        App.Init(this);

    Este código garantiza que el autenticador se inicializa antes de que se cargue la aplicación.

6. Agregue **{esquema_URL_de_la_aplicación}** a los esquemas de dirección URL de Info.plist.

7. Vuelva a compilar la aplicación, ejecútela, inicie sesión con el proveedor de autenticación que elija y compruebe que pueda acceder a los datos como usuario autenticado.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Adición de autenticación a proyectos de aplicaciones de Windows 10 (incluyendo Phone)
En esta sección se muestra cómo implementar la interfaz **IAuthenticate** en proyectos de aplicaciones de Windows 10. Los mismos pasos se aplican a los proyectos de plataforma universal de Windows (UWP), pero se usa el proyecto **UWP** (con cambios anotados). Omita esta sección si no ofrece compatibilidad con dispositivos Windows.

1. En Visual Studio, haga clic con el botón derecho en el proyecto **UWP**, y luego en **Establecer como proyecto de inicio**.
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
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
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

3. Si utiliza **UWP**, agregue el siguiente reemplazo del método **OnActivated** a la clase **App**:

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(protocolArgs.Uri);
            }

       }

   Si dicho reemplazo ya existe, agregue el código condicional del fragmento de código anterior.  Este código no es necesario para los proyectos de Windows universal.

3. Agregue **{esquema_URL_de_la_aplicación}** en Package.appxmanifest. 

4. Vuelva a compilar la aplicación, ejecútela, inicie sesión con el proveedor de autenticación que elija y compruebe que pueda acceder a los datos como usuario autenticado.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha completado este tutorial de autenticación básica, considere la posibilidad de continuar con uno de los siguientes tutoriales:

* [Incorporación de notificaciones push a su aplicación](app-service-mobile-xamarin-forms-get-started-push.md)

  : aprenda a agregar a la aplicación compatibilidad con notificaciones push y a configurar su back-end de aplicación móvil para usar Azure Notification Hubs para enviar notificaciones push.
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
