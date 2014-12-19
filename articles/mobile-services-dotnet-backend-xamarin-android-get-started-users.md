<properties urlDisplayName="Get Started with authentication in Mobile Services for Xamarin Android apps" pageTitle="Introducción a autenticación en Servicios móviles para aplicaciones de Android de Xamarin - Servicios móviles de Azure" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Xamarin Android app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get Started with authentication in Mobile Services" authors="donnam" solutions="" manager="dwrede" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="donnam" />

# Introducción a la autenticación en Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

En este tema se muestra cómo autenticar usuarios en Servicios móviles de Azure desde su aplicación. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que se haya realizado la autenticación y autorización correctamente en los Servicios móviles, se mostrará el valor de identificador de usuario.

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1. [Registro de la aplicación para la autenticación y configuración de Servicios móviles]
2. [Restricción de los permisos de tabla a usuarios autenticados]
3. [Incorporación de autenticación a la aplicación]

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a Servicios móviles]. 

##<a name="register"></a>Registro de la aplicación para la autenticación y configuración de Servicios móviles

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

##<a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="6">
<li><p>En Visual Studio o Xamarin Studio, ejecute el proyecto de cliente en un dispositivo o simulador. Compruebe que se produce una excepción no controlada con el código de estado 401 (No autorizado) después de iniciarse la aplicación.</p>
   
   	<p>Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla <em>TodoItem</em> requiere ahora autenticación.</p></li>
</ol>

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

##<a name="add-authentication"></a>Incorporación de autenticación a la aplicación

1. Agregue la propiedad siguiente a la clase **TodoActivity**:

			private MobileServiceUser user;

2. Agregue el método siguiente a la clase **TodoActivity**: 

	        private async Task Authenticate()
	        {
	            try
	            {
	                user = await client.LoginAsync(this, MobileServiceAuthenticationProvider.Facebook);
	                CreateAndShowDialog(string.Format("you are now logged in - {0}", user.UserId), "Logged in!");
	            }
	            catch (Exception ex)
	            {
	                CreateAndShowDialog(ex, "Authentication failed");
	            }
	        }

    De este modo se crea un método para administrar el proceso de autenticación. El usuario se autentica mediante el inicio de sesión en Facebook. Aparecerá un diálogo que muestra el identificador del usuario autenticado. 

    <div class="dev-callout"><b>Nota:</b>
	<p>Si usa un proveedor de identidades que no sea Facebook, cambie el valor que ha pasado al método <strong>LoginAsync</strong> anterior por uno de los siguientes: <i>MicrosoftAccount</i>, <i>Twitter</i>, <i>Google</i> o <i>WindowsAzureActiveDirectory</i>.</p>
    </div>

3. En el método **OnCreate**, agregue la línea de código siguiente después del código que crea instancias en el objeto `MobileServiceClient`.

		// Get the Mobile Service Table instance to use
        toDoTable = client.GetTable <ToDoItem> ();

        await Authenticate(); // add this line

	Esta llamada inicia el proceso de autenticación y lo espera de manera asincrónica.


4. En el menú **Ejecutar**, haga clic en **Ejecutar** para iniciar la aplicación e inicie sesión con el proveedor de identidades que haya elegido. 

   	Cuando haya iniciado sesión correctamente, la aplicación debe ejecutarse sin errores y debe poder consultar a Servicios móviles y realizar actualizaciones de datos.


<!-- ## <a name="next-steps"> </a>Pasos siguientes

En el siguiente tutorial, [Autorización en el servicio de los usuarios de Servicios móviles][Autorización de usuarios con scripts], usará el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles. 
 -->
 
<!-- Anchors. -->
[Registro de la aplicación para la autenticación y configuración de Servicios móviles]: #register
[Restricción de los permisos de tabla a usuarios autenticados]: #permissions
[Incorporación de autenticación a la aplicación]: #add-authentication
[Pasos siguientes]:#next-steps


<!-- URLs. -->
[Página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[SDK de Live para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introducción a Servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started/
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users/
[Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push/
[Autorización de usuarios con scripts]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts
[JavaScript y HTML]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/

[Portal de administración de Azure]: https://manage.windowsazure.com/
