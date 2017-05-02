
1. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en el proyecto de la aplicación de la Tienda Windows y haga clic en **Tienda** > **Asociar aplicación con la Tienda**.

    ![Asociar aplicación con la Tienda Windows](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. En el asistente, haga clic en **Siguiente** y, después, inicie sesión con su cuenta Microsoft. Escriba el nombre de la aplicación en **Reserve un nuevo nombre de aplicación** y después haga clic en **Reservar**.
3. Después de crear correctamente el registro de la aplicación, seleccione el nuevo nombre de la aplicación, haga clic en **Siguiente** y, después, en **Asociar**. Se agrega la información de registro necesaria de la Tienda Windows al manifiesto de aplicación.
4. Repita los pasos 1 y 3 para el proyecto de aplicación de la Tienda de Windows Phone con el mismo registro que creó anteriormente para la aplicación de la Tienda Windows.  
5. Vaya al [Centro de desarrollo de Windows](https://dev.windows.com/en-us/overview) e inicie sesión con su cuenta Microsoft. Haga clic en el nuevo registro de aplicación en **Mis aplicaciones** y, después, expanda **Servicios** > **Notificaciones push**.
6. En la página **Notificaciones push**, haga clic en **Sitio de Servicios Live** en **Windows Push Notification Services (WNS) y Microsoft Azure Mobile Apps**. Tome nota de los valores de **SID del paquete** y el valor *actual* de **Secreto de aplicación**. 

    ![Configuración de la aplicación en el Centro para desarrolladores](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > El secreto de aplicación y el SID del paquete son credenciales de seguridad importantes. No los comparta con nadie ni los distribuya con su aplicación.
   >
   >
