La característica Mobile Apps de Azure App Service usa [Azure Notification Hubs] para enviar notificaciones push; por lo tanto, necesita un centro de notificaciones para su aplicación móvil.

1. En [Azure Portal], vaya a **App Services** y haga clic en el back-end de aplicación. En **Configuración**, haga clic en **Insertar**.
2. Haga clic en **Conectar** para agregar un recurso de Centro de notificaciones a la aplicación. Puede crear un centro o conectarse a uno existente.

    ![](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Ya ha conectado un centro de notificaciones al proyecto de back-end de Mobile Apps. Más adelante podrá configurar este centro de notificaciones para que se conecte a un sistema de notificación de plataforma (PNS) que envíe notificaciones push a los dispositivos.

[Azure Portal]: https://portal.azure.com/
[Azure Notification Hubs]: https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-push-notification-overview/


<!--HONumber=Dec16_HO2-->


