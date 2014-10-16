## <a name="register-mobile-service-aad"></a>Registro de su servicio móvil en Azure Active Directory

En esta sección registrará su servicio móvil en Azure Active Directory y configurará permisos para permitir una suplantación de inicio de sesión único.

1.  Registre su aplicación en Azure Active Directory siguiendo el tema [Registro en Azure Active Directory][].

2.  En el [Portal de administración de Azure][], vuelva a la extensión de Azure Active Directory y haga clic en su Active Directory.

3.  Haga clic en la pestaña **Aplicaciones** y, a continuación, haga clic en su aplicación.

4.  Haga clic en **Administrar manifiesto**. A continuación, haga clic en **Descargar manifiesto** y guarde el manifiesto de la aplicación en un directorio local.

	![][ ]

1.  Abra el archivo del manifiesto de la aplicación con Visual Studio. En la parte superior del archivo, busque la línea de permisos de la aplicación que tiene el siguiente aspecto:

        "appPermissions": [],

    Reemplace esa línea por los siguientes permisos de la aplicación y guarde el archivo.

        "appPermissions": [
            {
                "claimValue": "user_impersonation",
                "description": "Allow the application access to the mobile service",
                "directAccessGrantTypes": [],
                "displayName": "Have full access to the mobile service",
                "impersonationAccessGrantTypes": [
                    {
                        "impersonated": "User",
                        "impersonator": "Application"
                    }
                ],
                "isDisabled": false,
                "origin": "Application",
                "permissionId": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
                "resourceScopeType": "Personal",
                "userConsentDescription": "Allow the application full access to the mobile service on your behalf",
                "userConsentDisplayName": "Have full access to the mobile service"
            }
        ],

2.  En el Portal de administración de Azure, haga clic en **Administrar manifiesto** de nuevo para la aplicación y haga clic en **Cargar manifiesto**. Vaya a la ubicación del manifiesto de la aplicación que acaba de actualizar y cárguelo.

<!-- URLs. -->

  [Registro en Azure Active Directory]: /es-es/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [ ]: ./media/mobile-services-dotnet-adal-register-service/mobile-services-aad-app-manage-manifest.png
