


1. Diríjase al sitio web <a href="http://cloud.google.com/console" target="_blank">Google Cloud Console</a>, inicie sesión con las credenciales de su cuenta de Google y, a continuación, haga clic en **CREATE PROJECT**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)   

	>[AZURE.NOTE] Si ya tiene un proyecto existente, se le dirigirá a la página <strong>Projects</strong> después de iniciar sesión. Para crear un nuevo proyecto desde el panel, expanda <strong>API Project</strong>, haga clic en <strong>Create...</strong> bajo <strong>Other projects</strong> y, a continuación, escriba un nombre de proyecto y haga clic en <strong>Create project</strong>.

2. Escriba un nombre de proyecto, acepte los términos del servicio y haga clic en **Create**. Si se solicita, ejecute la comprobación de SMS y haga clic de nuevo en **Crear**.

3. Tome nota del número del proyecto que aparece en la sección **Projects**. 

	Más adelante en este tutorial, configurará este valor como la variable PROJECT_ID en el cliente.

4. En la columna de la izquierda, haga clic en **APIs & auth**, a continuación desplácese hacia abajo, haga clic en el botón de alternancia para habilitar **Google Cloud Messaging for Android** y acepte los términos del servicio. 

	![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5. Haga clic en **Credentials** y, a continuación, en **CREATE NEW KEY**. 

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6. En **Create a new key**, haga clic en **Server key**. En la siguiente ventana, haga clic en **Create**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)

7. Anote el valor de la **clave de API**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png) 

	A continuación, usará este valor de clave de API para permitir que los Servicios móviles lleven a cabo la autenticación con GCM y envíen notificaciones de inserción en nombre de su aplicación.

<!--HONumber=42-->
