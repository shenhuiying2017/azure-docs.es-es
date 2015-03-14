Una vez que haya registrado su aplicación con APNS y haya configurado su proyecto, debe configurar su servicio móvil para la integración con APNS.

1. En Acceso a llaves, haga clic con el botón secundario en el nuevo certificado de la aplicación de inicio rápido en **Llaves** o **Mis certificados**, haga clic en **Exportar**, asigne como nombre de archivo QuickstartPusher, seleccione el formato **.p12** y haga clic en **Guardar**.

   	![](./media/mobile-services-apns-configure-push/mobile-services-ios-push-step18.png)

  Anote el nombre de archivo y la ubicación del certificado exportado.

>[AZURE.NOTE]  Con este tutorial se crea un archivo QuickstartPusher.p12. El nombre del archivo y la ubicación pueden ser diferentes.

2. Inicie sesión en el [Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, haga clic en su aplicación.

   	![](./media/mobile-services-apns-configure-push/mobile-services-selection.png)

3. Haga clic en la pestaña **Insertar** y, a continuación, en **Cargar**.

   	![](./media/mobile-services-apns-configure-push/mobile-push-tab-ios.png)

	Se mostrará el cuadro de diálogo Upload Certificate.

4. Haga clic en **Archivo**, seleccione el archivo QuickstartPusher.p12 del certificado exportado, escriba la **Contraseña**, asegúrese de que está seleccionado el **Modo** correcto (Desarrollo/Espacio aislado o Prod/Producción), haga clic en el icono de marca de verificación y, a continuación, haga clic en **Guardar**.

   	![](./media/mobile-services-apns-configure-push/mobile-push-tab-ios-upload.png)

    > [AZURE.NOTE] Este tutorial usa certificados de desarrollo.

El servicio móvil está configurado ahora para que funcione con APNS.

<!-- URLs. -->
[Portal de administración de Azure]: https://manage.windowsazure.com/
<!--HONumber=42-->
