
###Creación de un proyecto del Servicio de mensajería en la nube de Google con clave de API

>[AZURE.NOTE] Para llevar a cabo este procedimiento, debe tener una cuenta de Google asociada a una dirección de correo electrónico comprobada. Para crear una cuenta de Google, vaya a <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Navegue a la [consola de Google Cloud](https://console.developers.google.com/project) e inicie sesión con las credenciales de su cuenta de Google.

2. Haga clic en **All projects** (Todos los proyectos) y luego haga clic en **Create Project** (Crear proyecto).

3. Escriba un **nombre de proyecto** y haga clic en **Create** (Crear)

4. Una vez creado el proyecto, asegúrese de anotar el **número de proyecto** ya que será un valor numérico largo. Puede encontrarlo en **la sección IAM & Admin** de la **configuración** del proyecto ya que lo necesitará más adelante.
 
	![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)

5. Ahora crearemos una clave para la plataforma del Servicio de mensajería en la nube de Google que utilizará nuestra plataforma para enviar notificaciones a los dispositivos Android. Vaya a la sección **API Manager** (Administrador de API) y haga clic en **Servicio de mensajería en la nube de Google** en **Mobile API** (API de Mobile).

	![](./media/mobile-engagement-enable-google-cloud-messaging/gcm.png)

6. En la página siguiente, haga clic en el botón **Enable** (Habilitar). El panel le pedirá que cree unas credenciales. Haga clic en el botón **Go to Credentials** (Ir a credenciales).

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)

6. Seleccione **Servicio de mensajería en la nube de Google** en el primer cuadro desplegable y **Web server** (Servidor web) en el segundo. A continuación, haga clic en **What credentials do I need?** (¿Qué credenciales necesito?).

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

7. En la página **Add credentials to your project** (Agregar credenciales al proyecto), haga clic en **Create API key** (Crear clave de API).

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)

8. Anote el valor de **API KEY** (Clave de API). Usará este valor de clave de API más adelante para configurar la sección de inserción nativa. Ahora, haga clic en **Done** (Listo).

<!---HONumber=AcomDC_0622_2016-->