
* Siga los pasos que aparecen en [Instalación de una identidad de firma SSL de cliente en el servidor](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW15) para exportar el certificado que descargó en el paso anterior a un archivo .p12.

* En el Portal de vista previa de Azure, haga clic en **Examinar** > **Aplicaciones móviles** > su aplicación > **Servicios de notificaciones de inserción** > **Servicios de notificaciones de inserción de Apple** > **Cargar certificado**. Cargue el archivo .p12 y asegúrese de que esté seleccionado el **modo** correcto (ya sea Espacio aislado o Producción, que dependerá si generó un certificado SSL de cliente de Desarrollo o Distribución). El servicio ahora está configurado para trabajar con las notificaciones push en iOS.

<!---HONumber=July15_HO3-->