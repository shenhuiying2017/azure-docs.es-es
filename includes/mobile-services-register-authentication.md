

Para autenticar usuarios, registre su aplicación con un proveedor de identidad y, a continuación, registre las credenciales del cliente generadas por el proveedor con Servicios móviles de Azure.

1. Inicie sesión en el Portal de administración de Azure, haga clic en **Servicios móviles** y, a continuación, en su servicio móvil.

2. Haga clic en la pestaña **Panel** y observe el valor de **Dirección URL del servicio móvil**. Es posible que tenga que proporcionar ese valor al proveedor de identidades cuando registre la aplicación.

3. Elija un proveedor de identidades admitidas en la lista siguiente. Siga los pasos para registrar la aplicación con ese proveedor. Recuerde anotar los valores secretos y la identidad del cliente que genera el proveedor.

 - <a href="/documentation/articles/mobile-services-how-to-register-microsoft-authentication/" target="_blank">Cuenta Microsoft</a>
 - <a href="/documentation/articles/mobile-services-how-to-register-facebook-authentication/" target="_blank">Facebook</a>
 - <a href="/documentation/articles/mobile-services-how-to-register-twitter-authentication/" target="_blank">Twitter</a>
 - <a href="/documentation/articles/mobile-services-how-to-register-google-authentication/" target="_blank">Google </a>
 - <a href="/documentation/articles/mobile-services-how-to-register-active-directory-authentication/" target="_blank">Azure Active Directory</a>

    > [AZURE.IMPORTANT]El secreto que genera el proveedor es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación.

4. Vuelva al Portal de administración, haga clic en la pestaña **Identidad**, especifique el identificador de la aplicación y los valores secretos compartidos que le proporcionó el proveedor de identidades y haga clic en **Guardar**. El servicio móvil y la aplicación están ahora configurados para que funcionen con el proveedor de autenticación seleccionado.

    > [AZURE.IMPORTANT]Compruebe que ha establecido el URI de redirección correcto en el sitio para desarrolladores del proveedor de identidades. Como se describe en las instrucciones vinculadas para cada proveedor anterior, el URI de redirección puede ser diferente para un servicio de back-end de .NET frente a un servicio de back-end de JavaScript. Es posible que un URI de redirección configurado incorrectamente en la pantalla de inicio de sesión no se muestre correctamente y que la aplicación funcione mal de maneras inesperadas.

5. (Opcional) Repita los pasos 3 y 4 para configurar cualquier otro proveedor de identidad que desee que su aplicación admita.

<!--HONumber=54-->