<properties urlDisplayName="Get Started with Authentication (Xamarin.Android)" pageTitle="Introducción a la autenticación (Xamarin.Android) - Servicios móviles" metaKeywords="registro de aplicación de Azure, autenticación de Azure, autenticación de aplicaciones, autenticar servicios móviles, Servicios móviles Xamarin.Android" description="Learn how to use authentication in your Azure Mobile Services app for Xamarin.Android." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with authentication in Mobile Services" services="mobile-services" documentationCenter="Mobile" manager="dwrede" authors="donnam"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="donnam" />

# Incorporación de autenticación a la aplicación de Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

<p>En este tema se muestra cómo autenticar usuarios en Servicios móviles de Azure desde su aplicación Xamarin.Android. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que se haya realizado la autenticación y autorización correctamente en los Servicios móviles, se mostrará el valor de identificador de usuario.</p>

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1. [Registro de la aplicación para la autenticación y configuración de Servicios móviles]
2. [Restricción de los permisos de tabla a usuarios autenticados]
3. [Incorporación de autenticación a la aplicación]

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a Servicios móviles]. 

Para completar este tutorial necesita Xamarin.Android y el SDK de Android 4.2 o una versión posterior. 

<h2><a name="register"></a>Registro de la aplicación para la autenticación y configuración de Servicios móviles</h2>

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<h2><a name="permissions"></a>Restricción de los permisos para los usuarios autenticados</h2>


[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 


3. En Eclipse, abra el proyecto que ha creado al completar el tutorial [Introducción a Servicios móviles]. 

4. En el menú **Ejecutar**, haga clic en **Ejecutar** para iniciar la aplicación y, a continuación, compruebe que se lleva a cabo una excepción no controlada con el código de estado 401 (No autorizado) después de que se inicie la aplicación. 

	 Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla _TodoItem_ requiere ahora autenticación.

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

<h2><a name="add-authentication"></a>Incorporación de autenticación a la aplicación</h2>

1. Agregue la propiedad siguiente a la clase **TodoActivity**:

			private MobileServiceUser user;

2. Agregue el método siguiente a la clase **TodoActivity**: 

	        private async Task Authenticate()
	        {
	            try
	            {
	                user = await client.LoginAsync(this, MobileServiceAuthenticationProvider.MicrosoftAccount);
	                CreateAndShowDialog(string.Format("you are now logged in - {0}", user.UserId), "Logged in!");
	            }
	            catch (Exception ex)
	            {
	                CreateAndShowDialog(ex, "Authentication failed");
	            }
	        }

    De este modo se crea un método para administrar el proceso de autenticación. El usuario se autentica mediante el inicio de sesión de la cuenta Microsoft. Aparecerá un diálogo que muestra el identificador del usuario autenticado. No puede continuar sin una autenticación positiva.

    <div class="dev-callout"><b>Nota:</b>
	<p>Si usa un proveedor de identidades que no sea Microsoft, cambie el valor que ha pasado al método <strong>login</strong> anterior por uno de los siguientes: <i>Facebook</i>, <i>Google</i>, <i>Twitter</i> o <i>WindowsAzureActiveDirectory</i>.</p>
    </div>

3. En el método **OnCreate**, agregue la línea de código siguiente después del código que crea instancias en el objeto `MobileServiceClient`.

		await Authenticate();

	Esta llamada inicia el proceso de autenticación y lo espera de manera asincrónica.

4. Mueva el código restante situado detrás de `await Authenticate();` del método **OnCreate** a un nuevo método **CreateTable** cuyo aspecto es el siguiente:

	        private async Task CreateTable()
	        {
	            // Get the Mobile Service Table instance to use
	            todoTable = client.GetTable<TodoItem>();

	            textNewTodo = FindViewById<EditText>(Resource.Id.textNewTodo);

	            // Create an adapter to bind the items with the view
	            adapter = new TodoItemAdapter(this, Resource.Layout.Row_List_To_Do);
	            var listViewTodo = FindViewById<ListView>(Resource.Id.listViewTodo);
	            listViewTodo.Adapter = adapter;

	            // Load the items from the Mobile Service
	            await RefreshItemsFromTableAsync();
	        }

5. A continuación, llame al nuevo método **CreateTable** en **OnCreate** después de la llamada de **autenticación** que agregó en el paso 2:

		await CreateTable();


6. En el menú **Ejecutar**, haga clic en **Ejecutar** para iniciar la aplicación e inicie sesión con el proveedor de identidades que haya elegido. 

   	Cuando haya iniciado sesión correctamente, la aplicación debe ejecutarse sin errores y debe poder consultar a Servicios móviles y realizar actualizaciones de datos.

## Obtener un ejemplo completado
Descargue el [proyecto de ejemplo completado]. Asegúrese de actualizar las variables **applicationURL** y **applicationKey** con su propia configuración de Azure.

## <a name="next-steps"></a>Pasos siguientes

En el siguiente tutorial, [Autorización de usuarios con scripts], usará el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles. 

<!-- Anchors. -->
[Registro de la aplicación para la autenticación y configuración de Servicios móviles]: #register
[Restricción de los permisos de tabla a usuarios autenticados]: #permissions
[Incorporación de autenticación a la aplicación]: #add-authentication
[Pasos siguientes]:#next-steps

<!-- Images. -->
[4]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-services-selection.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-service-uri.png

[13]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-identity-tab.png
[14]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-portal-data-tables.png
[15]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->

[Página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[SDK de Live para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introducción a Servicios móviles]: /es-es/develop/mobile/tutorials/get-started-xamarin-android
[Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-xamarin-android
[Autorización a los usuarios con scripts]: /es-es/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android

[Portal de administración de Azure]: https://manage.windowsazure.com/

[proyecto de ejemplo completo]: http://go.microsoft.com/fwlink/p/?LinkId=331328
