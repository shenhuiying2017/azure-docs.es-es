

Para poder autenticar usuarios, debe registrar la aplicación con un proveedor de identidades. A continuación, debe registrar el secreto de cliente generado por el proveedor con Servicios móviles.

1. Inicie sesión en el [Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, haga clic en el servicio móvil.

   	![](./media/mobile-services-register-authentication/mobile-services-selection.png)

2. Haga clic en la pestaña **Panel** y anote el valor de **Dirección URL del servicio móvil**.

   	![](./media/mobile-services-register-authentication/mobile-service-uri.png)

    Es posible que tenga que proporcionar ese valor al proveedor de identidades cuando registre la aplicación.

3. Seleccione un proveedor de identidades compatible en la lista siguiente y siga los pasos para registrar la aplicación con ese proveedor:

 - <a href="/es-es/documentation/articles/mobile-services-how-to-register-microsoft-authentication/" target="_blank">Cuenta Microsoft</a>
 - <a href="/es-es/documentation/articles/mobile-services-how-to-register-facebook-authentication/" target="_blank">Inicio de sesión en Facebook</a>
 - <a href="/es-es/documentation/articles/mobile-services-how-to-register-twitter-authentication/" target="_blank">Inicio de sesión en Twitter</a>
 - <a href="/es-es/documentation/articles/mobile-services-how-to-register-google-authentication/" target="_blank">Inicio de sesión en Google</a>
 - <a href="/es-es/documentation/articles/mobile-services-how-to-register-active-directory-authentication/" target="_blank">Azure Active Directory</a>


    Recuerde anotar los valores secretos y la identidad del cliente que genera el proveedor.

    <div class="dev-callout"><b>Nota de seguridad</b>
	<p>El secreto que genera el proveedor es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación.</p>
    </div>

4. Vuelva al Portal de administración, haga clic en la pestaña **Identidad**, especifique el identificador de la aplicación y los valores secretos compartidos que le proporcionó el proveedor de identidades y haga clic en **Guardar**.

   	![](./media/mobile-services-register-authentication/mobile-identity-tab.png)

	El servicio móvil y la aplicación están ahora configurados para que funcionen con el proveedor de autenticación seleccionado.

<!-- URLs. -->
[Portal de administración de Azure]: https://manage.windowsazure.com/
