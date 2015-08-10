
* Haga clic en el Portal de Azure > **Servicios móviles** > su servicio móvil > **Panel**, y anote el valor de la **Dirección URL del servicio móvil**.

* Registre la aplicación con [Google](mobile-services-how-to-register-google-authentication.md), [Facebook](mobile-services-how-to-register-facebook-authentication.md), [Twitter](mobile-services-how-to-register-twitter-authentication.md), [Microsoft](mobile-services-how-to-register-microsoft-authentication.md) o [Azure Active Directory](mobile-services-how-to-register-active-directory-authentication.md). Anote los valores de secreto de cliente e identidad de cliente generados por el proveedor. No puede distribuir o compartir el secreto de cliente.

* Haga clic en Portal de Azure > **Servicios móviles** > el servicio móvil > **Identidad** > la configuración del proveedor de identidades. Escriba el identificador de la aplicación y el valor de secreto de su proveedor. Ahora ha configurado la aplicación y el servicio móvil para funcionar con su proveedor de autenticación. Opcionalmente puede repetir todos estos pasos para cada proveedor de identidades adicional que desee admitir.

    > [AZURE.IMPORTANT]Compruebe que ha establecido el URI de redirección correcto en el sitio para desarrolladores del proveedor de identidades. Como se describe en las instrucciones vinculadas para cada proveedor anterior, el URI de redirección puede ser diferente para un servicio de back-end de .NET frente a un servicio de back-end de JavaScript. Es posible que un URI de redirección configurado incorrectamente en la pantalla de inicio de sesión no se muestre correctamente y que la aplicación funcione mal de maneras inesperadas.

<!---HONumber=July15_HO5-->