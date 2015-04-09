Una vez que haya registrado la aplicación en APNS y haya configurado el proyecto, debe configurar la aplicación móvil para que se integre con APNS.

1. En Acceso a llaves, haga clic con el botón secundario en el nuevo certificado de la aplicación de inicio rápido en **Llaves** o **Mis certificados**, haga clic en **Exportar**, asigne como nombre de archivo QuickstartPusher, seleccione el formato **.p12** y haga clic en **Guardar**.

   	![](./media/mobile-services-apns-configure-push/mobile-services-ios-push-step18.png)

    Anote el nombre de archivo y la ubicación del certificado exportado.

>[AZURE.NOTE] Con este tutorial se crea un archivo QuickstartPusher.p12. El nombre del archivo y la ubicación pueden ser diferentes.

2. Inicie sesión en el [Portal de vista previa de Azure], seleccione **Examinar**, **Aplicaciones móviles** y, a continuación, haga clic en la aplicación. Haga clic en los servicios de notificaciones de inserción.

3. En el Servicio de notificaciones push de Apple, cargue el certificado con el archivo **.p12** y la contraseña asociada con él, y seleccione el modo deseado.

La aplicación móvil del Servicio de aplicaciones ya está configurada para poder funcionar con APNS.

<!-- URLs. -->
[Portal de vista previa de Azure]: https://portal.azure.com/

<!--HONumber=49-->