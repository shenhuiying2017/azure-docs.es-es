<properties pageTitle="Autenticación de la aplicación con Live Connect | Centro de desarrollo móvil" description="Obtenga información acerca de cómo usar el inicio de sesión único de Live Connect en Servicios móviles de Azure desde una aplicación de Windows Phone." services="mobile-services" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="11/22/2014" ms.author="glenga"/>

# Autenticación de una aplicación de Windows Phone 8 con el inicio de sesión único de Live Connect

<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on/" title="Windows Store C#">C# para Tienda Windows</a><a href="/es-es/documentation/articles/mobile-services-windows-store-javascript-single-sign-on/" title="Windows Store JavaScript">JavaScript para Tienda Windows</a><a href="/es-es/documentation/articles/mobile-services-windows-phone-single-sign-on/" title="Windows Phone" class="current">Windows Phone</a>
</div>	

En este tema se muestra cómo utilizar el inicio de sesión único de Live Connect para autenticar a los usuarios en Servicios móviles de Azure desde una aplicación de Windows Phone 8.  En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante Live Connect. Una vez que Live Connect haya realizado la autenticación correctamente, se dará la bienvenida al usuario que ha iniciado sesión por su nombre y se mostrará el valor de identificador de usuario.  

>[AZURE.NOTE]Este tutorial muestra las ventajas de usar la experiencia de inicio de sesión único que ofrece Live Connect para las aplicaciones de Windows Phone. Esto le permite autenticar más fácilmente a un usuario que ya haya iniciado sesión en el servicio móvil. Para una experiencia de autenticación más generalizada que admita varios proveedores de autenticación, vea el tema <a href="/es-es/documentation/articles/mobile-services-windows-phone-get-started-users">Incorporación de autenticación a la aplicación</a>. 

Este tutorial le guiará a través de estos pasos básicos para habilitar la autenticación de Live Connect:

1. [Registro de la aplicación para la autenticación y configuración de Servicios móviles]
2. [Restricción de los permisos de tabla a usuarios autenticados]
3. [Incorporación de autenticación a la aplicación]

Este tutorial requiere lo siguiente:

+ [SDK de Live para Windows y Windows Phone]
+ Microsoft Visual Studio 2012 Express para Windows Phone
+ Primero debe completar el tutorial [Incorporación de Servicios móviles a una aplicación existente].

<h2><a name="register"></a>Registro de su aplicación con Live Connect</h2>

Para autenticar usuarios, debe registrar la aplicación en el Centro para desarrolladores de Live Connect. A continuación, debe registrar el secreto de cliente para integrar Live Connect en Servicios móviles.

1. Inicie sesión en el [Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, haga clic en el servicio móvil.

   	![][4]

2. Haga clic en la pestaña **Panel** y anote el valor de la **dirección URL del sitio**.

   	![][5]

    Este valor se usará para definir el dominio de redirección.

3. Vaya a la página <a href="http://go.microsoft.com/fwlink/p/?LinkId=262039" target="_blank">Mis aplicaciones</a> del Centro para desarrolladores de Live Connect e inicie sesión con su cuenta Microsoft, si procede. 

4. Haga clic en **Crear aplicación** y, a continuación, escriba el **nombre de la aplicación** y haga clic en **Acepto**.

   	![][1] 

   	Así se registra la aplicación con Live Connect.

5. Haga clic en la **página de configuración de la aplicación**, después en **Configuración de API** y anote los valores del**identificador de cliente** y **secreto de cliente**. 

   	![][2]

 > [AZURE.NOTE] **Nota de seguridad**: el secreto del cliente es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación.

6. En **Dominio de redirección**, escriba la dirección URL de su servicio móvil que anotó en el paso 2, haga clic en **Sí** bajo **Aplicación de cliente móvil** y, a continuación, haga clic en **Guardar**.

7. Una vez haya vuelto al Portal de administración, haga clic en la pestaña **Identidad**, escriba el valor de **secreto del cliente** obtenido de Live Connect y, a continuación, haga clic en **Guardar**.

   	![][13]

El servicio móvil y la aplicación están ahora configurados para funcionar con Live Connect.

<h2><a name="permissions"></a>Restricción de los permisos para los usuarios autenticados</h2>

1. En el Portal de administración, haga clic en la pestaña **Datos** y, a continuación, en la tabla **TodoItem**. 

   	![][14]

2. Haga clic en la pestaña **Permisos**, establezca todos los permisos en **Solo usuarios autenticados** y, a continuación, haga clic en **Guardar**. De esta forma, se garantiza que todas las operaciones en la tabla **TodoItem** requieran un usuario autenticado. Esto también simplifica los scripts del próximo tutorial, ya que no existirá la posibilidad de permitir usuarios anónimos.

   	![][15]

3. En Visual Studio 2012 Express para Windows Phone, abra el proyecto que creó cuando completó el tutorial [Incorporación de Servicios móviles a una aplicación existente]. 

4. Presione la tecla F5 para ejecutar esta aplicación basada en el inicio rápido; compruebe que se genera una excepción con el código de estado 401 (No autorizado). 
   
   	Esto se produce porque la aplicación obtiene acceso a Servicios móviles como usuario autenticado, pero ahora la tabla _TodoItem_ requiere autenticación.

A continuación, actualizará la aplicación para autenticar a los usuarios con Live Connect antes de solicitar recursos del servicio móvil.

<h2><a name="add-authentication"></a>Incorporación de autenticación a la aplicación</h2>

1. Descargue e instale el [SDK Live para Windows y Windows Phone].

2. En el menú **Proyecto** de Visual Studio, haga clic en **Agregar referencia**, a continuación expanda **Ensamblados**; haga clic en **Extensiones**, active la casilla **Microsoft.Live** y, a continuación, haga clic en **Aceptar**. 

   	![][16]

  	De esta forma se agrega al proyecto una referencia al SDK de Live.

5. Abra el archivo de proyecto mainpage.xaml.cs y agregue las siguientes instrucciones using:

        using Microsoft.Live;      

6. Agregue el siguiente fragmento de código a la clase MainPage:
	
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

7. Actualice la cadena << INSERTAR IDENTIFICADOR DE CLIENTE AQUÍ >> del paso anterior con el valor del identificador de cliente que se generó cuando registró su aplicación con Live Connect.

    > [AZURE.NOTE] En una aplicación de Windows Phone 8, para crear una instancia de la clase **LiveAuthClient** se pasa el valor del identificador de cliente al constructor de clases. En una [aplicación de Tienda Windows](/es-es/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/), se crea una instancia de la misma clase pasando el URI del dominio de redirección.

8. Elimine o convierta en comentario el método **OnNavigatedTo** existente y reemplácelo por el siguiente método que administra el evento **Loaded** para la página. 

        async void MainPage_Loaded(object sender, RoutedEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }

   	Este método llama al nuevo método **Authenticate**. 

9. Reemplace el constructor MainPage con el siguiente código:

        // Constructor
        public MainPage()
        {
            InitializeComponent();
            this.Loaded += MainPage_Loaded;
        }

   	Este constructor también registra el controlador para el evento Loaded.
		
9. Presione la tecla F5 para ejecutar la aplicación e iniciar sesión en Live Connect con su cuenta Microsoft. 

   	Cuando haya iniciado sesión correctamente, la aplicación se ejecuta sin errores y podrá consultar a Servicios móviles y realizar actualizaciones de datos.

## <a name="next-steps"> </a>Pasos siguientes

En el siguiente tutorial, [Autorización de usuarios con scripts], usará el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles. Para obtener información sobre cómo usar otros proveedores de identidades para la autenticación, consulte [Introducción a la autenticación]. 

<!-- Anchors. -->
[Registro de la aplicación para la autenticación y configuración de Servicios móviles]: #register
[Restricción de los permisos de tabla a usuarios autenticados]: #permissions
[Incorporación de autenticación a la aplicación]: #add-authentication
[Pasos siguientes]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-windows-phone-single-sign-on/mobile-live-connect-app-api-settings-mobile.png
[4]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-selection.png
[5]: ./media/mobile-services-windows-phone-single-sign-on/mobile-service-uri.png

[13]: ./media/mobile-services-windows-phone-single-sign-on/mobile-identity-tab-ma-only.png
[14]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-data-tables.png
[15]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-change-table-perms.png
[16]: ./media/mobile-services-windows-phone-single-sign-on/mobile-add-reference-live-wp8.png

<!-- URLs. -->
[Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[SDK de Live para Windows y Windows Phone]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Incorporación de Servicios móviles a una aplicación existente]: /es-es/documentation/articles/mobile-services-windows-phone-get-started-data/
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-windows-phone-get-started-users
[Autorización de usuarios con scripts]: /es-es/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts/

[Portal de administración de Azure]: https://manage.windowsazure.com/


<!--HONumber=42-->
