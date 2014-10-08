## <a name="register-app-aad"></a>Registro de su aplicación cliente en Azure Active Directory

1.  Diríjase a **Active Directory** en el [Portal de administración de Azure][] y haga clic en el directorio.

	![][ ]

1.  Haga clic en la pestaña **Aplicaciones** que aparece en la parte superior y, a continuación, haga clic en **ADD** para agregar una aplicación.

	![][1]

1.  Haga clic en **Agregar una aplicación que mi organización está desarrollando**.

2.  En el asistente para agregar aplicaciones, escriba el valor de **Nombre** para la aplicación y haga clic en el tipo **Aplicación de cliente nativo**. A continuación, haga clic para continuar.

	![][2]

1.  En el cuadro **URI de redirección**, escriba el extremo /login/done de su servicio móvil. Este valor debería ser similar a <https://todolist.azure-mobile.net/login/done>.

	![][3]

1.  Haga clic en la pestaña **Configurar** de la aplicación nativa y copie el **Id. de cliente**. Lo necesitará más adelante.

	![][4]

1.  Desplácese hacia abajo, hasta la sección **permisos a otras aplicaciones**, y conceda acceso total a la aplicación de servicio móvil que registró anteriormente. A continuación, haga clic en **Guardar**.

	![][5]

El servicio móvil está ahora configurado en AAD para recibir inicios de sesión únicos desde su aplicación.

  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [ ]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-select-aad.png
  [1]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-aad-applications-tab.png
  [2]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-selection.png
  [3]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-redirect-uri.png
  [4]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-client-id.png
  [5]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-add-permissions.png
