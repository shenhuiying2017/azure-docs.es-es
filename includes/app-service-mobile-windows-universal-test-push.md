
1. Haga clic con el botón derecho en el proyecto de la Tienda Windows, haga clic en **Establecer como proyecto de inicio**y luego presione la tecla F5 para ejecutar la aplicación de la Tienda Windows.
   
    Cuando la aplicación se inicia, el dispositivo se registra para recibir notificaciones push.
2. Detenga la aplicación de la Tienda Windows y repita el paso anterior para ejecutar la aplicación de la Tienda de Windows Phone.
   
    En este momento, ambos dispositivos están registrados para recibir notificaciones push.
3. Vuelva a ejecutar la aplicación de la Tienda Windows, escriba texto en **Insertar un TodoItem** y haga clic en **Guardar**.
   
       Note that after the insert completes, both the Windows Store and the Windows Phone apps receive a push notification from WNS. The notification is displayed on Windows Phone even when the app isn't running.
   
![](./media/app-service-mobile-windows-universal-test-push/mobile-quickstart-push5-wp8.png)

