Los clientes de Azure pueden desbloquear 25.000 correos electrónicos gratuitos cada mes. Estos 25.000 correos electrónicos mensuales y gratuitos le darán acceso a avanzadas funciones de informes y análisis y a todas las API (Web, SMTP, Event, Parse, Sub-User). Para obtener información acerca de los servicios adicionales proporcionados por SendGrid, consulte la página de [características de SendGrid][].

### Para registrarse y obtener una cuenta SendGrid

1. Inicie sesión en el [Portal de administración de Azure][].

2. En el panel inferior del portal de administración, haga clic en **New**.

    ![command-bar-new][]

3. Haga clic en **Store**.

    ![sendgrid-store][]

4. En el cuadro de diálogo **Choose an Add-on**, seleccione **SendGrid** y haga clic en la flecha derecha.

5. En el cuadro de diálogo **Personalize Add-on**, seleccione el plan **SendGrid** para el que desea registrarse.

6. Introduzca un nombre para identificar el servicio **SendGrid** en la configuración de Azure o utilice el valor predeterminado de **SendGrid**. Los nombres deben tener de 1 a 100 caracteres y contener únicamente caracteres alfanuméricos, guiones, puntos y caracteres de subrayado. El nombre debe ser único en la lista de elementos de la Tienda de Azure.

    ![store-screen-2][]

7. Elija un valor para la región; por ejemplo, Oeste de EE. UU.

8. Haga clic en la flecha derecha.

9. En la pestaña **Review Purchase**, revise la información del plan y de precios, y consulte los términos legales. Si acepta los términos, haga clic en la marca de verificación. Después de hacer clic en la marca de verificación, la cuenta SendGrid comenzará el proceso de aprovisionamiento.

    ![store-screen-3][]

10. Después de confirmar la compra, se le redirigirá al panel de complementos, donde verá el mensaje **Purchasing SendGrid**.

    ![sendgrid-purchasing-message][]

    Su cuenta SendGrid se aprovisiona inmediatamente y verá el mensaje **Successfully purchased Add-On SendGrid**. Ya están creadas su cuenta y sus credenciales. Y está preparado para enviar correos electrónicos en este momento.

    Para modificar el plan de suscripción o ver la configuración de contacto de SendGrid, haga clic en el nombre de su servicio SendGrid para abrir el panel de complementos de SendGrid.

    ![sendgrid-add-on-dashboard][]

    Para enviar un correo electrónico usando SendGrid, debe proporcionar las credenciales de la cuenta (nombre de usuario y contraseña).

### Para encontrar las credenciales de SendGrid

1.  Haga clic en **Connection Info**.

    ![sendgrid-connection-info-button][]

2.  En el cuadro de diálogo *Connection Info*, copie la **contraseña** y el nombre de usuario para utilizarlos más adelante en este tutorial.

    ![sendgrid-connection-info][]

    Para establecer la configuración de entrega de correos electrónicos, haga clic en el botón **Manage**. Se abrirá la interfaz web de Sendgrid.com, donde podrá iniciar sesión y abrir el panel de control de SendGrid.

    ![sendgrid-control-panel][]

    Para obtener más información acerca de cómo comenzar con SendGrid, consulte [SendGrid Getting Started][].

<!--images-->

  [command-bar-new]: ./media/sendgrid-sign-up/sendgrid_BAR_NEW.PNG
  [sendgrid-store]: ./media/sendgrid-sign-up/sendgrid_offerings_store.png
  [store-screen-2]: ./media/sendgrid-sign-up/sendgrid_store_scrn2.png
  [store-screen-3]: ./media/sendgrid-sign-up/sendgrid_store_scrn3.png
  [sendgrid-purchasing-message]: ./media/sendgrid-sign-up/sendgrid_purchasing_message.png
  [sendgrid-add-on-dashboard]: ./media/sendgrid-sign-up/sendgrid_add-on_dashboard.png
  [sendgrid-connection-info-button]: ./media/sendgrid-sign-up/sendgrid_connection_info_button.png
  [sendgrid-connection-info]: ./media/sendgrid-sign-up/sendgrid_connection_info.png
  [sendgrid-control-panel]: ./media/sendgrid-sign-up/sendgrid_control_panel.png

<!--Links-->

  [SendGrid Getting Started]: http://sendgrid.com/docs
  [características de SendGrid]: http://sendgrid.com/features
  [Portal de administración de Azure]: https://manage.windowsazure.com
