<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-wp8" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your app with Live Connect (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Phone application." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Authenticate your Windows Phone 8 app with Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Autenticación de una aplicación de Windows Phone 8 con el inicio de sesión único de Live Connect

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/es-es/develop/mobile/tutorials/single-sign-on-windows-8-dotnet" title="C# para Tienda Windows">C# para Tienda Windows</a><a href="/es-es/develop/mobile/tutorials/single-sign-on-windows-8-js" title="JavaScript para Tienda Windows">JavaScript para Tienda Windows</a><a href="/es-es/develop/mobile/tutorials/single-sign-on-wp8/" title="Windows Phone" class="current">Windows Phone</a>
</div>

En este tema se muestra cómo utilizar el inicio de sesión único de Live Connect para autenticar a los usuarios en Servicios móviles de Azure desde una aplicación de Windows Phone 8. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante Live Connect. Una vez que Live Connect haya realizado la autenticación correctamente, se dará la bienvenida al usuario que ha iniciado sesión por su nombre y se mostrará el valor de identificador de usuario.

<div class="dev-callout"><b>Nota:</b>
    <p>Este tutorial muestra las ventajas de usar la experiencia de inicio de sesi&oacute;n &uacute;nico que ofrece Live Connect para las aplicaciones de Windows Phone. Esto le permite autenticar m&aacute;s f&aacute;cilmente a un usuario que ya haya iniciado sesi&oacute;n en el servicio m&oacute;vil. Para ver una experiencia de autenticaci&oacute;n m&aacute;s generalizada que admita varios proveedores de autenticaci&oacute;n, consulte el tema <a href="/es-es/develop/mobile/tutorials/get-started-with-users-wp8/">Introducci&oacute;n a la autenticaci&oacute;n</a>. </p>
</div>

Este tutorial le guiará a través de estos pasos básicos para habilitar la autenticación de Live Connect:

1.  [Registro de la aplicación para la autenticación y configuración de Servicios móviles][]
2.  [Restricción de los permisos de tabla a usuarios autenticados][]
3.  [Incorporación de autenticación a la aplicación][]

Este tutorial requiere lo siguiente:

-   [SDK de Live para Windows y Windows Phone][]
-   Microsoft Visual Studio 2012 Express para Windows Phone

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles][].

## <a name="register"></a><span class="short-header">Registro de la aplicación</span>Registro de la aplicación con Live Connect

Para autenticar usuarios, debe registrar la aplicación en el Centro para desarrolladores de Live Connect. A continuación, debe registrar el secreto de cliente para integrar Live Connect en Servicios móviles.

1.  Inicie sesión en el [Portal de administración de Azure][], haga clic en **Servicios móviles** y, a continuación, haga clic en el servicio móvil.

    ![][]

2.  Haga clic en la pestaña **Panel** y anote el valor de **Site URL**.

    ![][1]

    Este valor se usará para definir el dominio de redirección.

3.  Vaya a la página [Mis aplicaciones][] del Centro para desarrolladores de Live Connect e inicie sesión con cuenta Microsoft, si procede.

4.  Haga clic en **Create application** y, a continuación, escriba el **nombre de la aplicación** y haga clic en **I accept**.

    ![][2]

    Así se registra la aplicación con Live Connect.

5.  Haga clic en la **página de configuración de la aplicación**, después en **API Settings** y, a continuación, anote los valores de **Client ID** y **secreto de cliente**.

    ![][3]

    <div class="dev-callout"><b>Nota de seguridad</b>
<p>El secreto de cliente es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicaci&oacute;n.</p>
</div>

6.  En **Redirect domain**, especifique la dirección URL del servicio móvil del paso 2, haga clic en **Yes** en **Mobile client app** y, a continuación, haga clic en **Save**.

7.  Una vez haya vuelto al Portal de administración, haga clic en la pestaña **Identity**, escriba el valor de **Client secret** obtenido de Live Connect y, a continuación, haga clic en **Save**.

    ![][4]

El servicio móvil y la aplicación están ahora configurados para funcionar con Live Connect.

## <a name="permissions"></a><span class="short-header">Restricción de permisos</span>Restricción de permisos a usuarios autenticados

1.  En el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **TodoItem**.

    ![][5]

2.  Haga clic en la pestaña **Permissions**, establezca todos los permisos en **Only authenticated users** y, a continuación, haga clic en **Save**. De esta forma, se garantiza que todas las operaciones en la tabla **TodoItem** requieren un usuario autenticado. Esto también simplifica los scripts del próximo tutorial, ya que no existirá la posibilidad de permitir usuarios anónimos.

    ![][6]

3.  En Visual Studio 2012 Express para Windows Phone, abra el proyecto que creó cuando completó el tutorial [Introducción a los Servicios móviles][].

4.  Presione la tecla F5 para ejecutar esta aplicación basada en el inicio rápido; compruebe que se genera una excepción con el código de estado 401 (No autorizado).

    Esto se produce porque la aplicación obtiene acceso a Servicios móviles como usuario autenticado, pero ahora la tabla *TodoItem* requiere autenticación.

A continuación, actualizará la aplicación para autenticar a los usuarios con Live Connect antes de solicitar recursos del servicio móvil.

## <a name="add-authentication"></a><span class="short-header">Incorporación de autenticación</span>Incorporación de autenticación a la aplicación

1.  Descargue e instale el [SDK Live para Windows y Windows Phone][SDK de Live para Windows y Windows Phone].

2.  En el menú **Proyecto** de Visual Studio, haga clic en **Agregar referencia**, a continuación expanda **Ensamblados**, haga clic en **Extensiones**, active la casilla **Microsoft.Live** y, a continuación, haga clic en **Aceptar**.

    ![][7]

    De esta forma se agrega al proyecto una referencia al SDK de Live.

3.  Abra el archivo de proyecto mainpage.xaml.cs y agregue las siguientes instrucciones using:

        using Microsoft.Live;      

4.  Agregue el siguiente fragmento de código a la clase MainPage:

        private LiveConnectSession session;
        private async System.Threading.Tasks.Task Authenticate()
        {
            LiveAuthClient liveIdClient = new LiveAuthClient("<< INSERT CLIENT ID HERE >>");

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

    De esta forma se crea una variable de miembro para el almacenamiento de la sesión de Live Connect actual y un método para gestionar el proceso de autenticación.

5.  Actualice la cadena *\<\< INSERTAR IDENTIFICADOR DE CLIENTE AQUÍ \>\>* del paso anterior con el valor del identificador de cliente que se generó cuando registró su aplicación con Live Connect.

    <div class="dev-callout"><b>Nota:</b>
<p>En una aplicaci&oacute;n de Windows Phone 8, para crear una instancia de la clase <strong>LiveAuthClient</strong> se pasa el valor del identificador de cliente al constructor de clases. En una <a href="/es-es/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/">aplicaci&oacute;n de la Tienda Windows</a>, para crear instancias de la misma clase se pasa el URI del dominio de redirecci&oacute;n.</p>
</div>

6.  Elimine o convierta en comentario el método **OnNavigatedTo** existente y reemplácelo por el siguiente método que administra el evento **Loaded** para la página.

        async void MainPage_Loaded(object sender, RoutedEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }

    Este método llama al nuevo método **Authenticate**.

7.  Reemplace el constructor MainPage con el siguiente código:

        // Constructor
        public MainPage()
        {
            InitializeComponent();
            this.Loaded += MainPage_Loaded;
        }

    Este constructor también registra el controlador para el evento Loaded.

8.  Presione la tecla F5 para ejecutar la aplicación e iniciar sesión en Live Connect con su cuenta Microsoft.

    Cuando haya iniciado sesión correctamente, la aplicación se ejecuta sin errores y podrá consultar a Servicios móviles y realizar actualizaciones de datos.

## <a name="next-steps"> </a>Pasos siguientes

En el siguiente tutorial, [Autorización de usuarios con scripts][], usará el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles. Para obtener información sobre cómo usar otros proveedores de identidades para la autenticación, consulte [Introducción a la autenticación][8].

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [C# para Tienda Windows]: /es-es/develop/mobile/tutorials/single-sign-on-windows-8-dotnet "C# para Tienda Windows"
  [JavaScript para Tienda Windows]: /es-es/develop/mobile/tutorials/single-sign-on-windows-8-js "JavaScript para Tienda Windows"
  [Windows Phone]: /es-es/develop/mobile/tutorials/single-sign-on-wp8/ "Windows Phone"
  [Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-wp8/
  [Registro de la aplicación para la autenticación y configuración de Servicios móviles]: #register
  [Restricción de los permisos de tabla a usuarios autenticados]: #permissions
  [Incorporación de autenticación a la aplicación]: #add-authentication
  [SDK de Live para Windows y Windows Phone]: http://go.microsoft.com/fwlink/p/?LinkId=262253
  [Introducción a los Servicios móviles]: /es-es/develop/mobile/tutorials/get-started-wp8
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-phone-single-sign-on/mobile-service-uri.png
  [Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
  [2]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-live-connect-add-app.png
  [3]: ./media/mobile-services-windows-phone-single-sign-on/mobile-live-connect-app-api-settings-mobile.png
  [4]: ./media/mobile-services-windows-phone-single-sign-on/mobile-identity-tab-ma-only.png
  [5]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-data-tables.png
  [6]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-change-table-perms.png
  [7]: ./media/mobile-services-windows-phone-single-sign-on/mobile-add-reference-live-wp8.png
  [aplicación de la Tienda Windows]: /es-es/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
  [Autorización de usuarios con scripts]: /es-es/develop/mobile/tutorials/authorize-users-in-scripts-wp8
  [8]: /es-es/develop/mobile/tutorials/get-started-with-users-wp8
