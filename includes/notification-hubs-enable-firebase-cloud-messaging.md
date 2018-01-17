

1. Inicie sesión en la [consola Firebase](https://firebase.google.com/console/). Si aún no tiene uno, cree un nuevo proyecto de Firebase.
2. Una vez creado el proyecto, haga clic en **Add Firebase to your Android app** (Agregar Firebase a la aplicación Android) y siga las instrucciones proporcionadas.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. En la consola Firebase, haga clic en el engranaje de su proyecto y, después, haga clic en **Project Settings**(Configuración del proyecto).

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Haga clic en la pestaña **General** de la configuración del proyecto y, después, descargue el archivo **google-services.json** que contiene la clave de la API del servidor y el identificador de cliente.
5. Haga clic en la pestaña **Cloud Messaging** en la configuración del proyecto y copie el valor de **Legacy server key** (Clave del servidor heredada). Este valor se utilizará para configurar la directiva de acceso central de notificaciones.
