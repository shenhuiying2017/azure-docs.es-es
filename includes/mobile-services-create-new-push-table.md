1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, en su aplicación.

    ![][0]

2.  Haga clic en la pestaña **Data** y, a continuación, en **Create**.

    ![][1]

    Esto muestra el cuadro de diálogo **Create new table**.

3.  Con la configuración predeterminada **Anybody with the application key** para todos los permisos, escriba *Registrations* en **Table name** y, a continuación, haga clic en el botón de comprobación.

    ![][2]

Esto crea la tabla **Registrations**, la cual almacena los URI de canal que se usan para enviar notificaciones de inserción.

Después, podrá modificar su aplicación para habilitar las notificaciones de inserción.

<!-- URLs -->

  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-create-new-push-table/mobile-services-selection.png
  [1]: ./media/mobile-services-create-new-push-table/mobile-create-table.png
  [2]: ./media/mobile-services-create-new-push-table/mobile-create-registrations-table.png
