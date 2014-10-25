Para poder autenticar usuarios, debe registrar la aplicación con un proveedor de identidades. A continuación, debe registrar el secreto de cliente generado por el proveedor con Servicios móviles.

1.  Inicie sesión en el [Portal de administración de Azure][], haga clic en **Servicios móviles** y, a continuación, haga clic en el servicio móvil.

    ![][]

2.  Haga clic en la pestaña **Panel** y anote el valor de **Mobile Service URL**.

    ![][1]

    Es posible que tenga que proporcionar ese valor al proveedor de identidades cuando registre la aplicación.

3.  Seleccione un proveedor de identidades compatible en la lista siguiente y siga los pasos para registrar la aplicación con ese proveedor:

-   [Cuenta Microsoft][]
-   [Inicio de sesión en Facebook][]
-   [Inicio de sesión en Twitter][]
-   [Inicio de sesión en Google][]
-   [Azure Active Directory][]

    Recuerde anotar los valores secretos y la identidad del cliente que genera el proveedor.

    <div class="dev-callout"><b>Nota de seguridad</b>
<p>El secreto que genera el proveedor es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicaci&oacute;n.</p>
</div>

1.  Vuelva al Portal de administración, haga clic en la pestaña **Identity**, especifique el identificador de la aplicación y los valores secretos compartidos que le proporcionó el proveedor de identidades y haga clic en **Save**.

    ![][2]

    El servicio móvil y la aplicación están ahora configurados para que funcionen con el proveedor de autenticación seleccionado.

<!-- URLs. -->

  [Portal de administración de Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-register-authentication/mobile-services-selection.png
  [1]: ./media/mobile-services-register-authentication/mobile-service-uri.png
  [Cuenta Microsoft]: /es-es/documentation/articles/mobile-services-how-to-register-microsoft-authentication/
  [Inicio de sesión en Facebook]: /es-es/documentation/articles/mobile-services-how-to-register-facebook-authentication/
  [Inicio de sesión en Twitter]: /es-es/documentation/articles/mobile-services-how-to-register-twitter-authentication/
  [Inicio de sesión en Google]: /es-es/documentation/articles/mobile-services-how-to-register-google-authentication/
  [Azure Active Directory]: /es-es/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [2]: ./media/mobile-services-register-authentication/mobile-identity-tab.png
