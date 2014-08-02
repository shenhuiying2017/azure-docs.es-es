

**Nota:**

Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Google asociada a una dirección de correo electrónico verificada. Para crear una cuenta de Google, vaya a [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

1.  Diríjase al sitio web [Google Cloud Console](http://cloud.google.com/console), inicie sesión con las credenciales de su cuenta de Google y, a continuación, haga clic en **CREATE PROJECT**.

       ![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)   

    **Nota:**

    Si ya tiene un proyecto existente, se le dirigirá a la página **Projects** después de iniciar sesión. Para crear un nuevo proyecto desde el panel, expanda **API Project**, haga clic en **Create...** bajo **Other projects** y, a continuación, escriba un nombre de proyecto y haga clic en **Create project**.

2.  Escriba un nombre de proyecto, acepte los términos del servicio y haga clic en **Create**. Lleve a cabo la comprobación por SMS solicitada y haga clic de nuevo en **Create**.

3.  Tome nota del número del proyecto que aparece en la sección **Projects**.

    Más adelante en este tutorial, configurará este valor como la variable PROJECT\_ID en el cliente.

4.  En la columna de la izquierda, haga clic en **APIs & auth**, a continuación desplácese hacia abajo, haga clic en el botón de alternancia para habilitar **Google Cloud Messaging for Android** y acepte los términos del servicio.

    ![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5.  Haga clic en **Credentials** y, a continuación, en **CREATE NEW KEY**.

      ![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6.  En **Create a new key**, haga clic en **Server key**. En la siguiente ventana, haga clic en **Create**.

      ![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)

7.  Anote el valor de **API key**.

      ![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png) 


