

Para poder autenticar usuarios, debe registrar la aplicación con un proveedor de identidades. A continuación, debe registrar el secreto de cliente generado por el proveedor con el servicio de la aplicación.

1. Inicie sesión en el [Portal de vista previa de Azure], haga clic en **Examinar**, **Grupo de recursos** y luego seleccione el grupo de recursos de su aplicación móvil.

2. Seleccione la puerta de enlace y tome nota del valor **URL** en **Propiedades**. Es posible que tenga que proporcionar ese valor al proveedor de identidades cuando registre la aplicación.

   	![](./media/app-service-mobile-register-authentication/gateway-uri.png)

3. Seleccione un proveedor de identidades compatible en la lista siguiente y siga los pasos para configurar la aplicación con ese proveedor:

 - <a href="/es-es/documentation/articles/app-service-mobile-how-to-configure-active-directory-authentication-preview/" target="_blank">Azure Active Directory</a>
 - <a href="/es-es/documentation/articles/app-service-mobile-how-to-configure-facebook-authentication-preview/" target="_blank">Inicio de sesión en Facebook</a>
 - <a href="/es-es/documentation/articles/app-service-mobile-how-to-configure-google-authentication-preview/" target="_blank">Inicio de sesión en Google</a>
 - <a href="/es-es/documentation/articles/app-service-mobile-how-to-configure-microsoft-authentication-preview/" target="_blank">Cuenta Microsoft</a>
 - <a href="/es-es/documentation/articles/app-service-mobile-how-to-configure-twitter-authentication-preview/" target="_blank">Inicio de sesión en Twitter</a>

	Ahora, la aplicación está configurada para trabajar con el proveedor de autenticación seleccionado.

4. (Opcional) Repita el paso anterior para configurar cualquier otro proveedor de identidad que desee que su aplicación admita. 

<!-- URLs. -->
[Portal de vista previa de Azure]: https://portal.azure.com/

<!---HONumber=July15_HO3-->