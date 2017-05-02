
1. Navegue a la [consola de Google Cloud](https://console.developers.google.com/project)e inicie sesión con las credenciales de su cuenta de Google+. 
2. Haga clic en **Create Project** (Crear proyecto), escriba un nombre de proyecto y luego haga clic en **Create** (Crear). Si se solicita, ejecute la comprobación de SMS y haga clic de nuevo en **Crear** .
   
    ![Creación de un proyecto](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)   
   
     Escriba el **nombre del nuevo proyecto** y haga clic en **Create project** (Crear proyecto).
3. Haga clic en el botón **Utilities and More** (Utilidades y más) y después en **Project Information** (Información del proyecto). Anote el **número de proyecto**. Lo necesitará para establecer este valor como la variable `SenderId` en la aplicación cliente.
   
    ![Utilidades y mucho más](./media/mobile-services-enable-google-cloud-messaging/notification-hubs-utilities-and-more.png)
4. En el panel de proyecto, en **Mobile APIs** (API móviles), haga clic en **Google Cloud Messaging** (Servicio de mensajería en la nube de Google) y, después, en la página siguiente, haga clic en **Enable API** (Habilitar API) y acepte los términos del servicio. 
   
    ![Habilitación de GCM](./media/mobile-services-enable-google-cloud-messaging/enable-GCM.png)
   
    ![Habilitación de GCM](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-2.png) 
5. En el panel de proyecto, haga clic en **Credentials** > **Create Credential** > **API Key** (Credenciales > Crear credencial > Clave de API). 
   
    ![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-create-server-key.png)
6. En **Create a new key** (Crear una nueva clave), haga clic en **Server key** (Clave de servidor), escriba un nombre para la clave y haga clic en **Create** (Crear).
7. Anote el valor de **API KEY** (Clave de API).
   
    Usará este valor de clave de API para permitir que Azure lleve a cabo la autenticación con GCM y envíe notificaciones de inserción en nombre de su aplicación.

