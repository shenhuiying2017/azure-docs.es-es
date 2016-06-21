
###Creación de un proyecto del Servicio de mensajería en la nube de Google con clave de API

>[AZURE.NOTE] Para llevar a cabo este procedimiento, debe tener una cuenta de Google asociada a una dirección de correo electrónico comprobada. Para crear una cuenta de Google, vaya a <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Navegue a la [consola de Google Cloud](https://console.developers.google.com/project) e inicie sesión con las credenciales de su cuenta de Google.

2. Haga clic en **Go to project** (Ir al proyecto) y luego haga clic en **Create Project** (Crear proyecto).
<!--
   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project.png)

   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project-2.png)   
-->
3. Escriba un nombre para el proyecto.

4. Anote el número de proyecto que aparece bajo el cuadro de texto **Project name** (Nombre del proyecto). Lo necesitará más adelante en el tutorial para rellenar el archivo de manifiesto de Android. ![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)
5. Haga clic en **Crear**.

6. En la columna izquierda, asegúrese de que **Overview** (Información general) esté seleccionada y, en las API móviles, haga clic en **Servicio de mensajería en la nube de Google**. A continuación, en la página siguiente, haga clic en **Enable** (Habilitar).

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)
<!--
	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-gcm-2.png)
-->
7. En la siguiente página, haga clic en **Go to Credentials** (Ir a las credenciales) y en la siguiente página, seleccione **Servicio de mensajería en la nube de Google** en el primer cuadro desplegable y **Web server** (Servidor web) en el segundo. A continuación, haga clic en **What credentials do I need?** (¿Qué credenciales necesito?).

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

8. En la página **Add credentials to your project** (Agregar credenciales al proyecto), haga clic en **Create API key** (Crear clave de API).

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)
<!--
   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key6.png)
-->
9. Anote el valor de **API KEY** (Clave de API). Usará este valor de clave de API más adelante para configurar la sección de inserción nativa. Ahora, haga clic en **Done** (Listo).

<!---HONumber=AcomDC_0608_2016-->