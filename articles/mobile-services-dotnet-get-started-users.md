<properties  linkid="develop-mobile-tutorials-get-started-with-users-dotnet" urlDisplayName="Get Started with Users" pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="" solutions="" manager="" editor="" />

# Introducción a la autenticación en Servicios móviles

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/develop/mobile/tutorials/get-started-with-users-dotnet" title="Windows Store C#" class="current">Windows Store C#</a><a href="/es-es/develop/mobile/tutorials/get-started-with-users-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/es-es/develop/mobile/tutorials/get-started-with-users-wp8" title="Windows Phone">Windows Phone</a><a href="/es-es/develop/mobile/tutorials/get-started-with-users-ios" title="iOS">iOS</a><a href="/es-es/develop/mobile/tutorials/get-started-with-users-android" title="Android">Android</a><a href="/es-es/develop/mobile/tutorials/get-started-with-users-html" title="HTML">HTML</a><a href="/es-es/develop/mobile/tutorials/get-started-with-users-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/es-es/develop/mobile/tutorials/get-started-with-users-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>

<div  class="dev-onpage-video-clear clearfix">
<div  class="dev-onpage-left-content" markdown="1">
<p>En este tema se muestra cómo autenticar usuarios en Servicios móviles de
Azure desde su aplicación. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que Servicios móviles haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.</p>

<p>Puede ver la versión en vídeo de este tutorial haciendo clic en el clip
de la derecha.</p>

</div>
<div  class="dev-onpage-video-wrapper">
<a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="label">Ver el tutorial (en inglés)</a>

<a style="background-image: url('/media/devcenter/mobile/videos/get-started-with-users-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproducir vídeo (en
inglés)</span></a>

<span class="time">10:04</span>
</div>
</div>

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1.  [Registro de la aplicación para la autenticación y configuración de Servicios móviles](#register)
2.  [Restricción de los permisos de tabla a usuarios autenticados](#permissions)
3.  [Incorporación de autenticación a la aplicación](#add-authentication)

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles](/es-es/develop/mobile/tutorials/get-started).

<div  class="dev-callout">
<strong>Nota:</strong>

<p>En este tutorial se muestra el método básico que proporciona Servicios
móviles para autenticar usuarios mediante una serie de proveedores de identidades. Este método es fácil de configurar y es compatible con varios proveedores. Sin embargo, el método también requiere que los usuarios inicien sesión cada vez que se inicie la aplicación. Para usar Live Connect para proporcionar una experiencia de inicio de sesión único en la aplicación de la Tienda Windows, consulte el tema <a href="/es-es/develop/mobile/tutorials/single-sign-on-windows-8-dotnet">Inicio de sesión único para aplicaciones de la Tienda Windows mediante Live Connect</a>.</p>


</div>

<h2><a  name="register" ></a><span  class="short-header">Registro de la aplicación</span>Registro de la aplicación para la autenticación y configuración de Servicios móviles</h2>


Para poder autenticar usuarios, debe registrar la aplicación con un proveedor de identidades. A continuación, debe registrar el secreto de cliente generado por el proveedor con Servicios móviles.

1.  Inicie sesión en el [Portal de administración de Azure][3], haga clic en **Servicios móviles** y, a continuación, haga clic en el servicio móvil.
    
    ![][4]

2.  Haga clic en la pestaña **Panel** y anote el valor de **Mobile Service URL**.
    
    ![][5]
    
    Es posible que tenga que proporcionar ese valor al proveedor de identidades cuando registre la aplicación.

3.  Seleccione un proveedor de identidades compatible en la lista siguiente y siga los pasos para registrar la aplicación con ese proveedor:

	* [Cuenta Microsoft](/es-es/develop/mobile/how-to-guides/register-for-microsoft-authentication/)
	* [Inicio de sesión en Facebook](/es-es/develop/mobile/how-to-guides/register-for-facebook-authentication/)
	* [Inicio de sesión en Twitter](/es-es/develop/mobile/how-to-guides/register-for-twitter-authentication/)
	* [Inicio de sesión en Google](/es-es/develop/mobile/how-to-guides/register-for-google-authentication/) 
	* [Azure Active Directory](/es-es/documentation/articles/mobile-services-how-to-register-active-directory-authentication/)
  
  <p>Recuerde anotar los valores secretos y la identidad del cliente que
  genera el proveedor.</p>
  
  <div  class="dev-callout" markdown="1">
  <strong>Nota de seguridad:</strong>
  <p>El secreto que genera el proveedor es una credencial de seguridad
  importante, por lo que no debe compartirlo con nadie ni distribuirlo
  con su aplicación.</p>
  
  
  </div>

1.  Vuelva al Portal de administración, haga clic en la pestaña **Identity**, especifique el identificador de la aplicación y los valores secretos compartidos que le proporcionó el proveedor de identidades y haga clic en **Save**.
    
     ![][13]

2.  (Opcional) Complete los pasos en [Registro del paquete de la aplicación de la Tienda Windows para la autenticación de Microsoft](/es-es/develop/mobile/how-to-guides/register-windows-store-app-package).
    
<div  class="dev-callout" markdown="1">

<strong>Nota:</strong>
<p>Este paso es opcional porque solo se aplica al proveedor de inicio de sesión de la cuenta de Microsoft. Cuando registre la información del paquete de la aplicación de la Tienda Windows con Servicios móviles, el cliente podrá volver a usar las credenciales de inicio de sesión de la cuenta de Microsoft para conseguir una experiencia de inicio de sesión único. Si no realiza este procedimiento, los usuarios de inicio de sesión de la cuenta de Microsoft visualizarán una solicitud de inicio de sesión cada vez que se llame al método de inicio de sesión. Complete este paso cuando tenga previsto utilizar el proveedor de identidades de la cuenta de Microsoft.</p>


</div>


El servicio móvil y la aplicación están ahora configurados para que funcionen con el proveedor de autenticación seleccionado.

<h2><a  name="permissions" ></a><span  class="short-header">Restricción de permisos</span>Restricción de permisos a usuarios autenticados</h2>


1.  En el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **TodoItem**.
    
    ![][14]

2.  Haga clic en la pestaña **Permissions**, establezca todos los permisos en **Only authenticated users** y, a continuación, haga clic en **Save**. De esta forma, se garantiza que todas las operaciones en la tabla **TodoItem** requieren un usuario autenticado. Esto también simplifica los scripts del próximo tutorial, ya que no existirá la posibilidad de permitir usuarios anónimos.
    
    ![][15]

3.  En Visual Studio 2012 Express para Windows 8, abra el proyecto que creó cuando completó el tutorial [Introducción a los Servicios móviles](/es-es/develop/mobile/tutorials/get-started).

4.  Presione la tecla F5 para ejecutar esta aplicación basada en el inicio rápido; compruebe que se lleva a cabo una excepción no controlada con el código de estado 401 (No autorizado) después de que se inicie la aplicación.
    
    Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla _TodoItem_ requiere ahora autenticación.

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

<h2><a  name="add-authentication" ></a><span  class="short-header">Incorporación de autenticación</span>Incorporación de autenticación a la aplicación</h2>


1.  Abra el archivo de proyecto mainpage.xaml.cs y agregue la siguiente instrucción using:
    
         using Windows.UI.Popups;

2.  Agregue el siguiente fragmento de código a la clase MainPage:
    
         private MobileServiceUser user;
         private async System.Threading.Tasks.Task Authenticate()
         {
             while (user == null)
             {
                 string message;
                 try
                 {
                     user = await App.MobileService
                         .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                     message = 
                         string.Format("You are now logged in - {0}", user.UserId);
                 }
                 catch (InvalidOperationException)
                 {
                     message = "You must log in. Login Required";
                 }
                            
                 var dialog = new MessageDialog(message);
                 dialog.Commands.Add(new UICommand("OK"));
                 await dialog.ShowAsync();
             }
         }
    
    De esta forma se crea una variable de miembro para el almacenamiento del usuario actual y un método para administrar el proceso de autenticación. El usuario se autentica mediante el inicio de sesión en Facebook. Si está usando un proveedor de identidades diferente al de Facebook, cambie el valor de
    **MobileServiceAuthenticationProvider** anterior por el valor de su
    proveedor.
    
	<div  class="dev-callout" markdown="1">
	
	<strong>Nota:</strong>
	<p>Si ha registrado la información del paquete de la aplicación de la Tienda Windows con Servicios móviles, debe llamar al método <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a>  proporcionando un valor <b>true</b> para el parámetro <i>useSingleSignOn</i>. Si no realiza este procedimiento, los usuarios seguirán visualizando una solicitud de inicio de sesión cada vez que se llame al método de inicio de sesión/</p>
	
	
	</div>


3.  Reemplace la anulación del método **OnNavigatedTo** existente por el siguiente método que llama al nuevo método **Authenticate**:
    
         protected override async void OnNavigatedTo(NavigationEventArgs e)
         {
             await Authenticate();
             RefreshTodoItems();
         }

4.  Presione la tecla F5 para ejecutar la aplicación e iniciar sesión en la aplicación con el proveedor de identidades seleccionado.
    
    Cuando haya iniciado sesión correctamente, la aplicación debe ejecutarse sin errores y debe poder consultar a Servicios móviles y realizar actualizaciones de datos.

## <a  name="next-steps"> </a>Pasos siguientes

En el siguiente tutorial, [Autorización de usuarios con scripts](/es-es/develop/mobile/tutorials/authorize-users-in-scripts-dotnet), usará el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles. Obtenga más información sobre cómo usar Servicios móviles con .NET en [Referencia conceptual de Servicios móviles con.NET](/es-es/develop/mobile/how-to-guides/work-with-net-client-library).

<!-- Anchors. -->
[Registro de la aplicación para la autenticación y configuración de Servicios móviles]: #register
[Restricción de los permisos de tabla a usuarios autenticados]: #permissions
[Incorporación de autenticación a la aplicación]: #add-authentication
[Pasos siguientes]:#next-steps

<!-- Images. -->

[4]: ./media/mobile-services-dotnet-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-dotnet-get-started-users/mobile-service-uri.png


[13]: ./media/mobile-services-dotnet-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-dotnet-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-dotnet-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single sign-on for Windows Store apps by using Live Connect]: ./mobile-services-single-sign-on-win8-dotnet.md
[Get started with Mobile Services]: /es-es/develop/mobile/tutorials/get-started
[Get started with data]: /es-es/develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /es-es/develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /es-es/develop/mobile/tutorials/get-started-with-push-dotnet
[Authorize users with scripts]: /es-es/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /es-es/develop/mobile/tutorials/get-started-with-users-js

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /es-es/develop/mobile/how-to-guides/work-with-net-client-library
[Register your Windows Store app package for Microsoft authentication]: /es-es/develop/mobile/how-to-guides/register-windows-store-app-package