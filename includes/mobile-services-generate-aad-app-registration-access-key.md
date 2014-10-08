1.  Haga clic en la pestaña **Aplicaciones** de su página de directorios en el [Portal de administración de Azure][].

2.  Haga clic en su registro de aplicación integrada.

3.  Haga clic en **Configurar** en la página de la aplicación y desplácese hacia abajo hasta la sección **claves** de la página.
4.  Haga clic en la duración **1 año** para la nueva clave. A continuación, haga clic en **Guardar** y el portal mostrará su nuevo valor de clave.
5.  Copie los valores **Id. de cliente** y **Clave** que aparecen después de guardar. Tenga en cuenta que el valor de clave se mostrará una sola vez después de haber guardado.

    ![][]

6.  Desplácese hasta la parte inferior de la página de configuración de la aplicación integrada y habilite el permiso **Leer los datos del directorio** de la aplicación y haga clic en **Guardar**.

    ![][1]

7.  En el [Portal de administración de Azure][], vuelva a su servicio móvil y haga clic en la pestaña **Configurar**. Desplácese hasta la sección **configuración de aplicación** y agregue la siguiente configuración de aplicación; por último, haga clic en **Guardar**.

    | Nombre de configuración de aplicación | Descripción                                                                                  |
    |---------------------------------------|----------------------------------------------------------------------------------------------|
    | AAD\_CLIENT\_ID                       | El identificador de cliente que copió de su aplicación integrada en los pasos anteriores.    |
    | AAD\_CLIENT\_KEY                      | La clave de aplicación que generó en su aplicación integrada de AAD en los pasos anteriores. |
    | AAD\_TENANT\_DOMAIN                   | El nombre de su dominio AAD. Debería ser similar a "mydomain.onmicrosoft.com".               |

 
    ![][2]

  [Portal de administración de Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-generate-aad-app-registration-access-key/client-id-and-key.png
  [1]: ./media/mobile-services-generate-aad-app-registration-access-key/app-perms.png
  [2]: ./media/mobile-services-generate-aad-app-registration-access-key/aad-app-settings.png
