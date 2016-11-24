
1. En [Azure Portal](https://portal.azure.com/), haga clic en **Examinar todo** > **App Services** > su aplicación móvil de back-end. En **Configuración**, haga clic en **App Service Push** (Notificación push de App Service) y, a continuación, haga clic en el nombre del centro de notificaciones.
2. Vaya a **Windows (WNS)**, escriba la **Clave de seguridad **(secreto de cliente) y el **SID del paquete** que ha obtenido en el sitio de Servicios Live. Por último, haga clic en **Guardar**.
   
    ![Establecimiento de la clave de WNS en el portal](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

El back-end ahora está configurado para usar WNS para enviar notificaciones push.



<!--HONumber=Nov16_HO3-->


