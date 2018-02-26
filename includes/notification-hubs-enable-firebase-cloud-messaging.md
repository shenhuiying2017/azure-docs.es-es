

1. Inicie sesión en la [consola Firebase](https://firebase.google.com/console/). Si aún no tiene uno, cree un nuevo proyecto de Firebase.
2. Después de crear el proyecto, seleccione **Add Firebase to your Android app** (Añade Firebase a tu aplicación de Android). Siga las instrucciones que se le proporcionan.

    ![Agregar Firebase a una aplicación de Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. En la consola Firebase, seleccione el icono de la rueda dentada del proyecto. Luego, seleccione **Project Settings** (Configuración del proyecto).

    ![Seleccionar Project Settings (Configuración del proyecto)](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Seleccione la pestaña **General** en la configuración del proyecto. Después, descargue el archivo **google-services.json** que contiene la clave de la API del servidor y el identificador de cliente.
5. Seleccione la pestaña **Cloud Messaging** en la configuración del proyecto y copie el valor de **Legacy server key** (Clave del servidor heredada). Este valor se utilizará para configurar la directiva de acceso al centro de notificaciones.
