

1. En el equipo Mac, inicie **Acceso a llaves**. En la barra de navegación izquierda, en **Categoría**, abra **Mis certificados**. Busque el certificado SSL que descargó en la sección anterior y muestre su contenido. Seleccione solo el certificado, sin seleccionar la clave privada, y [expórtelo](https://support.apple.com/kb/PH20122?locale=en_US).
2. En [Azure Portal](https://portal.azure.com/), haga clic en **Examinar todo** > **App Services** y finalmente en el back-end de Mobile Apps. En **Configuración**, haga clic en **App Service Push** y, después, en el nombre del centro de notificaciones. Vaya a **Apple Push Notification Service** > **Cargar certificado**. Cargue el archivo .p12 y seleccione el **Modo** correcto (según si el certificado SSL de cliente es para un espacio aislado o para producción). Guarde los cambios.

El servicio ahora está configurado para trabajar con las notificaciones push en iOS.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
