
1. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en el proyecto de la aplicación de la Tienda Windows. A continuación, seleccione **Tienda** > **Asociar aplicación con la Tienda**.

    ![Asociar aplicación con la Tienda Windows](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. En el asistente, seleccione **Siguiente**. Luego, inicie sesión con su cuenta de Microsoft. En **Reserve un nuevo nombre de aplicación**, escriba un nombre para la aplicación y seleccione **Reservar**.
3. Después de crear correctamente el registro de la aplicación, seleccione el nuevo nombre de la aplicación. Seleccione **Siguiente** y después **Asociar**. Este proceso agrega la información de registro necesaria de la Tienda Windows al manifiesto de aplicación.
4. Repita los pasos 1 y 3 para el proyecto de aplicación de la Tienda de Windows Phone con el mismo registro que creó anteriormente para la aplicación de la Tienda Windows.  
5. Vaya al [Centro de desarrollo de Windows](https://dev.windows.com/en-us/overview) e inicie sesión con su cuenta de Microsoft. En **Mis aplicaciones**, seleccione el nuevo registro de aplicación. A continuación, expanda **Servicios** > **Notificaciones push**.
6. En la página **Notificaciones push**, en **Windows Push Notification Services (WNS) y Microsoft Azure Mobile Apps**, seleccione el **sitio de Servicios Live**.  Tome nota de los valores de **SID del paquete** y el valor *actual* de **Secreto de aplicación**. 

    ![Configuración de la aplicación en el Centro para desarrolladores](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > El secreto de aplicación y el SID del paquete son credenciales de seguridad importantes. No comparta estos valores con nadie ni los distribuya con su aplicación.
   >
   >
