App Service Mobile Apps usa [Notification Hubs] para enviar notificaciones push; por lo tanto, necesita un centro de notificaciones para su aplicación móvil.

1. En [Azure Portal], vaya a **Examinar** > **App Services** y haga clic en el back-end de aplicación. En **Configuración**, haga clic en **App Service Push** (Notificación push de App Service).
2. Haga clic en **Configurar** para configurar un centro de notificaciones. Puede crear un centro o conectarse a uno existente.
   
    ![](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Ya ha conectado un centro de notificaciones al back-end de la aplicación móvil. Más adelante podrá configurar este centro de notificaciones para que se conecte a un sistema de notificación de plataforma (PNS) que envíe notificaciones push a los dispositivos.

[Azure Portal]: https://portal.azure.com/
[Notification Hubs]: https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-push-notification-overview/

<!--HONumber=Nov16_HO3-->


