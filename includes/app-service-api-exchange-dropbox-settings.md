1. En otra ventana o pestaña del explorador, vaya al [Portal de vista previa de Azure](https://portal.azure.com).
2. Vaya a la hoja **Aplicación de API** para el conector de Dropbox. (Si aún está en la hoja **Grupo de recursos**, solo tiene que hacer clic en el conector de Dropbox en el diagrama).
3. Haga clic en **Configuración** y, en la hoja **Configuración**, haga clic en **Autenticación**.
   
    ![Hacer clic en Configuración](./media/app-service-api-exchange-dropbox-settings/clicksettings.png)
   
    ![Hacer clic en Autenticación](./media/app-service-api-exchange-dropbox-settings/clickauth.png)
4. En la hoja Autenticación, escriba el identificador y el secreto de cliente del sitio de Dropbox y, a continuación, haga clic en **Guardar**.
   
    ![Escribir los valores y hacer clic en Guardar](./media/app-service-api-exchange-dropbox-settings/authblade.png)
5. Copie el **URI de redirección** (el cuadro gris sobre el identificador y el secreto del cliente) y agregue el valor a la página que dejó abierta en el paso anterior.
   
    El URI de redirección sigue este patrón:
   
        [gatewayurl]/api/consent/redirect/[connectorname]
   
    Por ejemplo:
   
        https://dropboxrgaeb4ae60b7.azurewebsites.net/api/consent/redirect/DropboxConnector
   
    ![Obtener URI de redirección](./media/app-service-api-exchange-dropbox-settings/redirecturi.png)
   
    ![Crear aplicación de Dropbox](./media/app-service-api-exchange-dropbox-settings/dbappsettings2.png)

<!---HONumber=Oct15_HO3-->