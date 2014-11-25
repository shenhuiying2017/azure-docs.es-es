1. Diríjase al sitio web [Google Cloud Console][Google Cloud Console], inicie sesión con las credenciales de su cuenta de Google y, a continuación, haga clic en **CREATE PROJECT**.

    ![][0]

	> [WACOM.NOTE]Si dispone ya de un proyecto existente, se le dirigirá a la página **Projects** después de iniciar sesión. Para crear un nuevo proyecto desde el panel, expanda **API Project**, haga clic en **Create...** bajo **Other projects** y, a continuación, escriba un nombre de proyecto y haga clic en **Create project**.

2. Escriba un nombre de proyecto, acepte los términos del servicio y haga clic en **Create**. Lleve a cabo la comprobación por SMS solicitada y haga clic de nuevo en **Create**.

3. Tome nota del número del proyecto que aparece en la sección **Projects**.

	Más adelante en este tutorial, configurará este valor como la variable PROJECT\_ID en el cliente.

4. En la columna de la izquierda, haga clic en **APIs & auth**, a continuación desplácese hacia abajo, haga clic en el botón de alternancia para habilitar **Google Cloud Messaging for Android** y acepte los términos del servicio.

    ![][1]

5. Haga clic en **Credentials** y, a continuación, en **CREATE NEW KEY**.

    ![][2]

6. En **Create a new key**, haga clic en **Server key**. En la siguiente ventana, haga clic en **Create**.

    ![][3]

7. Anote el valor de **API key**.

    ![][4]

	Utilizará este valor de clave de API para permitir que Servicios móviles se autentique con GCM y envíe notificaciones de inserción en nombre de su aplicación.

  [Google Cloud Console]: http://cloud.google.com/console
  [0]: ./media/notification-hubs-android-get-started/mobile-services-google-new-project.png
  [1]: ./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png
  [2]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png
  [3]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png
  [4]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png
