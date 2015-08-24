<properties
	pageTitle="Modelo de aplicaciones v2.0 | Microsoft Azure"
	description="Cómo crear una aplicación nativa .NET con la que los usuarios pueden iniciar sesión utilizando tanto la cuenta personal de Microsoft como sus cuentas profesionales o educativas."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
  ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# Vista previa del modelo de aplicaciones v2.0: agregar inicio de sesión a una aplicación de escritorio de Windows

Con el modelo de aplicaciones v2.0 puede agregar rápidamente la autenticación para sus aplicaciones de escritorio compatibles tanto con las cuentas personales de Microsoft como con las cuentas profesionales o educativas. También permite que la aplicación se comunique de forma segura con una api web back-end, así como con algunas de las [API unificadas de Office 365](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

> [AZURE.NOTE]Esta información se aplica a la vista previa pública del modelo de aplicaciones v2.0. Para obtener instrucciones sobre cómo integrarse en el servicio de Azure AD, disponible con carácter general, consulte la [Guía para desarrolladores de Azure Active Directory](active-directory-developers-guide.md).

Para las [aplicaciones nativas .NET que se ejecutan en un dispositivo](active-directory-v2-flows.md#mobile-and-native-apps), Azure AD proporciona la biblioteca de autenticación de Active Directory o ADAL. El único propósito de ADAL es facilitar a su aplicación la obtención de token de acceso para llamar a servicios web. Para demostrar lo fácil que es, crearemos aquí una aplicación de lista de tareas pendientes de .NET WPF que permita realizar las siguientes acciones:

-	Iniciar la sesión del usuario y obtener token de acceso mediante el [protocolo de autenticación de OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow).
-	Llama a un servicio web de lista de tareas pendientes back-end, que también está protegido con OAuth 2.0.
-	Cierre la sesión de los usuarios.

Para crear la aplicación de trabajo completa, tendrá que:

2. Registrar su aplicación
3. Instalar y configurar ADAL
5. Usar ADAL para obtener tokens de Azure AD

El código de este tutorial se conserva [en GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet). Para poder continuar, puede [descargar el esqueleto de la aplicación como .zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) o clonar el esqueleto:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

The completed app is provided at the end of this tutorial as well.

## 1. Register an App
Create a new app at [apps.dev.microsoft.com](https://apps.dev.microsoft.com), or follow these [detailed steps](active-directory-v2-app-registration.md).  Make sure to:

- Copy down the **Application Id** assigned to your app, you'll need it soon.
- Add the **Mobile** platform for your app.
- Copy down the **Redirect URI** from the portal. You must use the default value of `urn:ietf:wg:oauth:2.0:oob`.

## 2. Install & Configure ADAL
Now that you have an app registered with Microsoft, you can install ADAL and write your identity-related code.  In order for ADAL to be able to communicate the v2.0 endpoint, you need to provide it with some information about your app registration.

-	Begin by adding ADAL to the TodoListClient project using the Package Manager Console.

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TodoListClient -IncludePrerelease ```

-	En el proyecto TodoListClient, abra `app.config`. Reemplace los valores de los elementos de la sección `<appSettings>` para que reflejen los valores especificados en el portal de registro de aplicaciones. El código hará referencia a estos valores cada vez que use ADAL.
    -	`ida:ClientId` es el **Id. de aplicación** de su aplicación que copió del portal.
    -	`ida:RedirectUri` es el **uri de redireccionamiento** desde el portal.
- En el proyecto de servicio de TodoList, abra `web.config` en la raíz del proyecto.  
    - Reemplace el valor `ida:Audience` con el mismo **Id. de aplicación** desde el portal.

## 3\. Uso de ADAL para obtener token
El principio básico inherente a ADAL es que cada vez que la aplicación necesite un token de acceso, basta con llamar a `authContext.AcquireToken(...)` y ADAL se encarga del resto.

-	En el proyecto `TodoListClient`, abra `MainWindow.xaml.cs` y busque el método `OnInitialized(...)`. El primer paso consiste en inicializar el `AuthenticationContext` de la aplicación, que es la clase principal de ADAL. Este es el lugar en el que pasa a ADAL las coordenadas que necesita para comunicarse con Azure AD e indicarle cómo almacenar en caché los tokens.

```C#
protected override async void OnInitialized(EventArgs e)
{
		base.OnInitialized(e);

		authContext = new AuthenticationContext(authority, new FileCache());
		AuthenticationResult result = null;
		...
}
```

- Cuando se inicia la aplicación, queremos comprobar y asegurarnos de si el usuario ya está registrado en la aplicación. Sin embargo, no deseamos invocar una IU de inicio de sesión todavía, sino que haremos que el usuario haga clic en "Iniciar sesión" para hacerlo. En el método `OnInitialized(...)` también ocurre lo siguiente:

```C#
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from ADAL, passing in the parameter
// PromptBehavior.Never.  This forces ADAL to throw an exception if it cannot
// get a token for the user without showing a UI.

try
{
		result = await authContext.AcquireTokenAsync(new string[] { clientId }, null, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never, null));

		// If we got here, a valid token is in the cache.  Proceed to
		// fetch the user's tasks from the TodoListService via the
		// GetTodoList() method.

		SignInButton.Content = "Clear Cache";
		GetTodoList();
}
catch (AdalException ex)
{
		if (ex.ErrorCode == "user_interaction_required")
		{
				// If user interaction is required, the app should take no action,
				// and simply show the user the sign in button.
		}
		else
		{
				// Here, we catch all other AdalExceptions

				string message = ex.Message;
				if (ex.InnerException != null)
				{
						message += "Inner Exception : " + ex.InnerException.Message;
				}
				MessageBox.Show(message);
		}
}
```

- Si el usuario no ha iniciado sesión y hace clic en el botón "Iniciar sesión", deseamos invocar una IU de inicio de sesión y que el usuario escriba sus credenciales. Implementar el controlador del botón Inicio de sesión:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
		// TODO: Sign the user out if they clicked the "Clear Cache" button

		// If the user clicked the 'Sign-In' button, force
		// ADAL to prompt the user for credentials by specifying
		// PromptBehavior.Always.  ADAL will get a token for the
		// TodoListService and cache it for you.

		AuthenticationResult result = null;
		try
		{
				result = await authContext.AcquireTokenAsync(new string[] { clientId }, null, clientId, redirectUri, new PlatformParameters(PromptBehavior.Always, null));
				SignInButton.Content = "Clear Cache";
				GetTodoList();
		}
		catch (AdalException ex)
		{
				// If ADAL cannot get a token, it will throw an exception.
				// If the user canceled the login, it will result in the
				// error code 'authentication_canceled'.

				if (ex.ErrorCode == "authentication_canceled")
				{
						MessageBox.Show("Sign in was canceled by the user");
				}
				else
				{
						// An unexpected error occurred.
						string message = ex.Message;
						if (ex.InnerException != null)
						{
								message += "Inner Exception : " + ex.InnerException.Message;
						}

						MessageBox.Show(message);
				}

				return;
		}

}
```

- Si el usuario inicia sesión correctamente, ADAL recibirá y almacenará en caché un token por usted y podrá proceder con la llamada al método `GetTodoList()` con confianza. Lo único que queda para obtener las tareas del usuario es implementar el método `GetTodoList()`.

```C#
private async void GetTodoList()
{
		AuthenticationResult result = null;
		try
		{
				// Here, we try to get an access token to call the TodoListService
				// without invoking any UI prompt.  PromptBehavior.Never forces
				// ADAL to throw an exception if it cannot get a token silently.

				result = await authContext.AcquireTokenAsync(new string[] { clientId }, null, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never, null));
		}
		catch (AdalException ex)
		{
				// ADAL couldn't get a token silently, so show the user a message
				// and let them click the Sign-In button.

				if (ex.ErrorCode == "user_interaction_required")
				{
						MessageBox.Show("Please sign in first");
						SignInButton.Content = "Sign In";
				}
				else
				{
						// In any other case, an unexpected error occurred.

						string message = ex.Message;
						if (ex.InnerException != null)
						{
								message += "Inner Exception : " + ex.InnerException.Message;
						}
						MessageBox.Show(message);
				}

				return;
		}

		// Once the token has been returned by ADAL,
    // add it to the http authorization header,
    // before making the call to access the To Do list service.

    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

		...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null) { // If the user clicked the 'clear cache' button, // clear the ADAL token cache and show the user as signed out. // It's also necessary to clear the cookies from the browser // control so the next user has a chance to sign in.

		if (SignInButton.Content.ToString() == "Clear Cache")
		{
				TodoList.ItemsSource = string.Empty;
				authContext.TokenCache.Clear();
				ClearCookies();
				SignInButton.Content = "Sign In";
				return;
		}

		...
```

Congratulations! You now have a working .NET WPF app that has the ability to authenticate users & securely call Web APIs using OAuth 2.0.  Run your both projects, and sign in with either a personal Microsoft account or a work or school account.  Add tasks to that user's To-Do list.  Sign out, and sign back in as another user to view their To-Do list.  Close the app, and re-run it.  Notice how the user's session remains intact - that is becuase the app caches tokens in a local file.

ADAL makes it easy to incorporate common identity features into your app, using both personal and work accounts.  It takes care of all the dirty work for you - cache management, OAuth protocol support, presenting the user with a login UI, refreshing expired tokens, and more.  All you really need to know is a single API call, `authContext.AcquireTokenAsync(...)`.

For reference, the completed sample (without your configuration values) [is provided as a .zip here](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip), or you can clone it from GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## Pasos siguientes

Ahora puede pasar a temas más avanzados. También puede probar lo siguiente:

- [Proteger la API web TodoListService con el modelo de aplicaciones v2.0 >>](active-directory-v2-devquickstarts-dotnet-api.md)

Para obtener recursos adicionales, consulte: - [la vista previa del modelo de aplicaciones v2.0 >>](active-directory-appmodel-v2-overview.md) - [la etiqueta "adal" StackOverflow >>](http://stackoverflow.com/questions/tagged/adal)

<!---HONumber=August15_HO7-->