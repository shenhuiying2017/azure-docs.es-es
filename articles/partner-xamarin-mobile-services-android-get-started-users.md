<properties linkid="develop-mobile-tutorials-get-started-with-users-xamarin-android" urlDisplayName="Get Started with Authentication (Xamarin.Android)" pageTitle="Get started with authentication (Xamarin.Android) - Mobile Services" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services Xamarin.Android" description="Learn how to use authentication in your Azure Mobile Services app for Xamarin.Android." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with authentication in Mobile Services" services="mobile-services" documentationCenter="Mobile" manager="dwrede" authors="donnam"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="donnam"></tags>

# Introducción a la autenticación en Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started-users][]]

En este tema se muestra cómo autenticar usuarios en Servicios móviles de Azure desde su aplicación Xamarin.Android. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que Servicios móviles haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1.  [Registro de la aplicación para la autenticación y configuración de Servicios móviles][]
2.  [Restricción de los permisos de tabla a usuarios autenticados][]
3.  [Incorporación de autenticación a la aplicación][]

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles][].

Para completar este tutorial necesita Xamarin.Android y el SDK de Android 4.2 o una versión posterior.

## <a name="register"></a><span class="short-header">Registro de la aplicación</span>Registro de la aplicación para la autenticación y configuración de Servicios móviles

[WACOM.INCLUDE [mobile-services-register-authentication][]]

## <a name="permissions"></a><span class="short-header">Restricción de permisos</span>Restricción de permisos a usuarios autenticados

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend][]]

1.  En Eclipse, abra el proyecto que ha creado al completar el tutorial [Introducción a los Servicios móviles][].

2.  En el menú **Run**, haga clic en **Run** para iniciar la aplicación y, a continuación, compruebe que se lleva a cabo una excepción no controlada con el código de estado 401 (No autorizado) después de que se inicie la aplicación.

    Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla *TodoItem* requiere ahora autenticación.

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

## <a name="add-authentication"></a><span class="short-header">Incorporación de autenticación</span>Incorporación de autenticación a la aplicación

1.  Agregue la propiedad siguiente a la clase **TodoActivity**:

            private MobileServiceUser user;

2.  Agregue el método siguiente a la clase **TodoActivity**:

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
<p>Si usa un proveedor de identidades que no sea Microsoft, cambie el valor que ha pasado al m&eacute;todo <strong>login</strong> anterior por uno de los siguientes: <i>Facebook</i>, <i>Google</i>, <i>Twitter</i> o <i>WindowsAzureActiveDirectory</i>.</p>
</div>

3.  En el método **OnCreate**, agregue la línea de código siguiente después del código que crea instancias en el objeto `MobileServiceClient`.

        await Authenticate();

    Esta llamada inicia el proceso de autenticación y lo espera de manera asincrónica.

4.  Mueva el código restante situado detrás de `await Authenticate();` del método **OnCreate** a un nuevo método **CreateTable** cuyo aspecto es el siguiente:

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

5.  A continuación, llame al nuevo método **CreateTable** en **OnCreate** después de la llamada a **Authenticate** que se agregó en el paso 2:

        await CreateTable();

6.  En el menú **Run**, haga clic en **Run** para iniciar la aplicación e inicie sesión con el proveedor de identidades que haya elegido.

    Cuando haya iniciado sesión correctamente, la aplicación debe ejecutarse sin errores y debe poder consultar a Servicios móviles y realizar actualizaciones de datos.

## Obtener un ejemplo completado

Descargue el [proyecto de ejemplo completado][]. Asegúrese de actualizar las variables **applicationURL** y **applicationKey** con su propia configuración de Azure.

## <a name="next-steps"></a>Pasos siguientes

En el siguiente tutorial, [Autorización de usuarios con scripts][], usará el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [Registro de la aplicación para la autenticación y configuración de Servicios móviles]: #register
  [Restricción de los permisos de tabla a usuarios autenticados]: #permissions
  [Incorporación de autenticación a la aplicación]: #add-authentication
  [Introducción a los Servicios móviles]: /es-es/develop/mobile/tutorials/get-started-xamarin-android
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [proyecto de ejemplo completado]: http://go.microsoft.com/fwlink/p/?LinkId=331328
  [Autorización de usuarios con scripts]: /es-es/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android
