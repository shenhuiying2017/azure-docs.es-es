

1. En el equipo Mac, inicie **Acceso a llaves**. Abra **Mis certificados** en **Categoría**, en la barra de navegación izquierda. Busque el certificado SSL que descargó en la sección anterior y muestre su contenido. Seleccione solo el certificado, sin seleccionar la clave privada, y [expórtelo](https://support.apple.com/kb/PH20122?locale=en_US).
2. En [Azure Portal](https://portal.azure.com/), haga clic en **Examinar todo** > **App Services** > su aplicación móvil de back-end. En **Configuración**, haga clic en **App Service Push** (Notificación push de App Service) y, a continuación, haga clic en el nombre del centro de notificaciones. Vaya a **Apple Push Notification Service** > **Cargar certificado**. Cargue el archivo .p12 y seleccione el **Modo** correcto (según si el certificado SSL de cliente es para un espacio aislado o para producción). Guarde los cambios.

El servicio ahora está configurado para trabajar con las notificaciones push en iOS.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png


<!--HONumber=Nov16_HO3-->


