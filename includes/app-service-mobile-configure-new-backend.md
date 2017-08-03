
1. Haga clic en **App Services** > seleccione su back-end de aplicación móvil > haga clic en **Inicio rápido** > la plataforma de cliente (iOS, Android, Xamarin, Cordova).

![Portal de Azure con inicio rápido de Aplicaciones móviles resaltado][quickstart]

2. Si no está configurada la conexión a la base de datos, debe crear una conexión de datos.

![Conexión de Azure Portal con Mobile Apps: conexión a la base de datos][connect]

  * Cree una nueva instancia y un nuevo servidor de SQL Database.

  ![Azure Portal con Mobile Apps: creación de una nueva base de datos y un nuevo servidor][server]

  * Espere hasta que se haya creado correctamente la conexión de datos.

  ![Azure Portal con Mobile Apps: notificación sobre la creación de una conexión de datos][notification]

  * La conexión de datos debe haberse creado correctamente.

  ![Azure Portal con Mobile Apps: notificación sobre la creación de una conexión de datos][already-connection]

3. En **2. Crear una API de tabla**, seleccione Node.js para **Lenguaje de back-end**. Acepte la confirmación y haga clic en **Crear tabla TodoItem**. Esto crea una nueva tabla *TodoItem* en la base de datos. Recuerde que al cambiar un back-end existente a Node.js, se sobrescribirá todo el contenido. Para crear en su lugar un back-end de .NET, [siga estas instrucciones][instructions].

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
