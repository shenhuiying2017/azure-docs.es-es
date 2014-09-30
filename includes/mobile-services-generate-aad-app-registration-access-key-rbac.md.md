1.  Haga clic en la pestaña **Aplicaciones** en la página del directorio del [Portal de administración de Azure][].

2.  Haga clic en el registro de la aplicación integrado.

3.  Haga clic en **Configurar** en la página de la aplicación y desplácese hacia la sección **claves** de la página.
4.  Haga clic en la duración **1 año** para una nueva clave. A continuación, haga clic en **Guardar** y el portal mostrará el nuevo valor de la clave.
5.  Copie los valores de **Id. de cliente** y **Clave** mostrados después de guardar. Tenga en cuenta que el valor de la clave solo se mostrará una única vez después de que lo haya guardado.

    ![][]

6.  Desplácese hacia la parte inferior de la página de configuración de la aplicación integrada y habilite el permiso **Leer datos de directorio** para la aplicación y haga clic en **Guardar**.

    ![][1]

7.  En el [Portal de administración de Azure][], desplácese al servicio móvil y, a continuación, haga clic en la pestaña **Configurar**. Desplácese a la sección de **configuración de la aplicación** y agregue las siguientes configuraciones y haga clic en **Guardar**.

    | Nombre de la configuración de la aplicación | Descripción                                                                                     |
    |---------------------------------------------|-------------------------------------------------------------------------------------------------|
    | AAD\_CLIENT\_ID                             | El identificador del cliente que ha copiado de la aplicación integrada en los pasos anteriores. |
    | AAD\_CLIENT\_KEY                            | La clave de la aplicación generada en la aplicación integrada AAD en los pasos anteriores.      |
    | AAD\_TENANT\_DOMAIN                         | El nombre de dominio de AAD. Debe ser similar a "midominio.onmicrosoft.com"                     |
    | AAD\_GROUP\_ID                              | El identificador del grupo que ha anotado para el grupo de ventas de la sección anterior.       |

    ![][2]

  [Portal de administración de Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-generate-aad-app-registration-access-key-rbac/client-id-and-key.png
  [1]: ./media/mobile-services-generate-aad-app-registration-access-key-rbac/app-perms.png
  [2]: ./media/mobile-services-generate-aad-app-registration-access-key-rbac/aad-app-settings.png
