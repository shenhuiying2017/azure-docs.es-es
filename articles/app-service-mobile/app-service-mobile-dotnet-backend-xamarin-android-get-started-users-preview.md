<properties 
	pageTitle="Introducción a la autenticación de Aplicaciones móviles en Xamarin Android" 
	description="Obtenga información acerca de cómo utilizar Aplicaciones móviles para autenticar usuarios de su aplicación Xamarin Android a través de una variedad de proveedores de identidad, incluidos AAD, Google, Facebook, Twitter y Microsoft." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/27/2015" 
	ms.author="mahender"/>

# Adición de la autenticación a la aplicación Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

En este tema se muestra cómo autenticar usuarios de una aplicación móvil desde la aplicación cliente. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Aplicaciones móviles de Azure. Una vez que la aplicación móvil finalice la autenticación y autorización correctamente, se mostrará el valor del identificador de usuario.

Este tutorial se basa en el inicio rápido de aplicaciones móviles. Primero debe completar el tutorial [Creación de una aplicación Xamarin.Android]. Si no usa el proyecto de servidor de inicio rápido descargado, debe agregar el paquete de extensión de autenticación al proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="create-gateway"></a>Creación de una puerta de enlace de Servicio de aplicaciones

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-gateway-preview](../../includes/app-service-mobile-dotnet-backend-create-gateway-preview.md)]

##<a name="register"></a>Registro de la aplicación para la autenticación y configuración de Servicios de aplicaciones

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

<ol start="4">
<li><p>En Visual Studio o Xamarin Studio, ejecute el proyecto de cliente en un dispositivo o emulador. Compruebe que se produce una excepción no controlada con el código de estado 401 (No autorizado) después de iniciarse la aplicación.</p>
   
   	<p>Esto sucede porque la aplicación intenta obtener acceso al back-end de la aplicación móvil como usuario sin autenticar. La tabla <em>TodoItem</em> ahora requiere autenticación.</p></li>
</ol>

Luego, actualizará la aplicación cliente para solicitar recursos del back-end de la aplicación móvil con un usuario autenticado.

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

    Esto crea un nuevo método para autenticar un usuario. El usuario del código de ejemplo anterior se autentica mediante el inicio de sesión en Facebook. Se usa un cuadro de diálogo para mostrar el identificador de usuario una vez autenticado.

    > [AZURE.NOTE]Si usa un proveedor de identidades que no sea una cuenta de Facebook, cambie el valor que pasó anteriormente a **LoginAsync** a uno de los siguientes: _MicrosoftAccount_, _Twitter_, _Google_ o _WindowsAzureActiveDirectory_.

3. En el método **OnCreate**, agregue la siguiente línea de código después del código que crea una instancia del objeto `MobileServiceClient`.

		// Create the Mobile Service Client instance, using the provided
		// Mobile Service URL, Gateway URL and key
		client = new MobileServiceClient (applicationURL, gatewayURL, applicationKey);
		
		await Authenticate(); // Added for authentication

	Esta llamada inicia el proceso de autenticación y lo espera de manera asincrónica.


4. En Visual Studio o Xamarin Studio, ejecute el proyecto de cliente en un dispositivo o emulador, e inicie sesión con el proveedor de identidades elegido.

   	Una vez iniciada la sesión correctamente, la aplicación mostrará el identificador de inicio de sesión y la lista de tareas pendientes, y podrá realizar actualizaciones en los datos.


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Creación de una aplicación Xamarin.Android]: app-service-mobile-dotnet-backend-xamarin-android-get-started-preview.md

[Azure Management Portal]: https://portal.azure.com
 

<!---HONumber=Oct15_HO4-->