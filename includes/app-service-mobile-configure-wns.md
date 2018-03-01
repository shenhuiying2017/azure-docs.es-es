
1. En [Azure Portal](https://portal.azure.com/), seleccione **Examinar todo** > **App Services**. A continuación, seleccione el back-end de Mobile Apps. En **Configuración**, seleccione **App Service Push**. A continuación, seleccione el nombre del centro de notificaciones.
2. Vaya a **Windows (WNS)**. Escriba la **Clave de seguridad** (secreto de cliente) y el **SID del paquete** que ha obtenido en el sitio de Servicios Live. Luego, seleccione **Guardar**.

    ![Establecimiento de la clave de WNS en el portal](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

El back-end ahora está configurado para usar WNS para enviar notificaciones push.
