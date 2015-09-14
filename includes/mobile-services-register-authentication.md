
1. Haga clic en el Portal de Azure > **Servicios móviles** > su servicio móvil > **Panel**, y anote el valor de la **Dirección URL del servicio móvil**.

2. Registre la aplicación con uno o varios de los siguientes proveedores de autenticación:
   * [Google](mobile-services-how-to-register-google-authentication.md)
   * [Facebook](mobile-services-how-to-register-facebook-authentication.md)
   * [Twitter](mobile-services-how-to-register-twitter-authentication.md)
   * [Microsoft](mobile-services-how-to-register-microsoft-authentication.md)
   * [Azure Active Directory](mobile-services-how-to-register-active-directory-authentication.md). 
   
   Anote los valores de secreto de cliente e identidad de cliente generados por el proveedor. No puede distribuir o compartir el secreto de cliente.

3. En el portal de Azure, haga clic en **Servicios móviles** > el servicio móvil > **Identidad** > la configuración del proveedor de identidades y, a continuación, escriba el Id. de cliente y el valor de secreto de su proveedor. 
 
Ahora ha configurado la aplicación y el servicio móvil para funcionar con su proveedor de autenticación. Opcionalmente puede repetir todos estos pasos para cada proveedor de identidades adicional que desee admitir.

    > [AZURE.IMPORTANT] Verify that you've set the correct redirect URI on your identity provider's developer site. As described in the linked instructions for each provider above, the redirect URI may be different for a .NET backend service vs. for a JavaScript backend service. An incorrectly configured redirect URI may result in the login screen not being displayed properly and the app malfunctioning in unexpected ways.

<!---HONumber=September15_HO1-->