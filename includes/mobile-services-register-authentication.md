
En primer lugar, deberá registrar la aplicación con un proveedor de identidades en su sitio; a continuación, establezca las credenciales en el servicio móvil.

1. Haga clic en el [Portal de administración de Azure], desplácese al servicio móvil, haga clic en **Panel** y anote el valor de la **Dirección URL del servicio móvil**.

2. Registre la aplicación con uno de los siguientes proveedores de identidades admitido.

	* [Google](mobile-services-how-to-register-google-authentication.md)
	* [Facebook](mobile-services-how-to-register-facebook-authentication.md)
	* [Twitter](mobile-services-how-to-register-twitter-authentication.md)
	* [Microsoft](mobile-services-how-to-register-microsoft-authentication.md)
	* [Azure Active Directory](mobile-services-how-to-register-active-directory-authentication.md).  
	
    >[AZURE.IMPORTANT]Asegúrese de establecer correctamente el URI de redirección para el servicio móvil en el sitio para desarrolladores del proveedor de identidades. Como se describe en las instrucciones vinculadas para cada proveedor anterior, la ruta de acceso de la URL de redirección es diferente para un servicio móvil de back-end de .NET (`/signin-<provider>`) frente a un servicio móvil de back-end de JavaScript (`/login/<provider>`). Una URI de redirección configurada incorrectamente impide que al cliente pueda iniciar sesión en su aplicación. <br/>No distribuya ni comparta el secreto de cliente.

3. Vuelva al servicio móvil en el [Portal de administración de Azure], haga clic en la pestaña **Identidad** y escriba el identificador de la aplicación y los valores secretos que obtenido del proveedor de identidades.

Ahora que ha configurado la aplicación y el servicio móvil para admitir un proveedor de identidades para la autenticación, puede repetir estos pasos para agregar compatibilidad con proveedores de identidades adicionales.

[Portal de administración de Azure]: https://manage.windowsazure.com/

<!---HONumber=July15_HO4-->