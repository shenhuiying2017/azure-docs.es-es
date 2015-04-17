

1. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/) y, a continuación, haga clic en **+NUEVO** en la parte inferior de la pantalla.

2. Haga clic en **Servicios de aplicaciones**, **Bus de servicio**, **Centro de notificaciones** y, a continuación, en **Creación rápida**.

3. Escriba un nombre para su centro de notificaciones, seleccione la región deseada y, a continuación, haga clic en **Crear una nueva base de datos central de notificaciones**.

   ![Establecer propiedades del centro de notificaciones](./media/notification-hubs-android-configure-push/notification-hub-create-from-portal2.png)

4. Haga clic en el espacio de nombres que acaba de crear (por lo general ***nombre del Centro de notificaciones*-ns**) y, a continuación, haga clic en **Configurar** en la parte superior.

5. Haga clic en la pestaña **Centros de notificaciones** en la parte superior y, a continuación, haga clic en el Centro de notificaciones que acaba de crear.

6. Haga clic en la pestaña **Configurar**, escriba el valor de **Clave de API** obtenido de GCM en la sección anterior y, a continuación, haga clic en **Guardar**.

   ![Establecer la clave de API de GCM en la pestaña Configurar](./media/notification-hubs-android-configure-push/notification-hub-configure-android.png)

7. Seleccione la pestaña **Panel** en la parte superior y, a continuación, haga clic en **Ver cadena de conexión**. Anote las dos cadenas de conexión.

El centro de notificaciones ya está configurado para funcionar con GCM y dispone de las cadenas de conexión para registrar la aplicación para que reciba notificaciones y para enviar notificaciones de inserción.

<!--HONumber=49-->