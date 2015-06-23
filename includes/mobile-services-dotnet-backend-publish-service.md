

Tras probar la aplicación cliente con el servicio móvil local, el paso final de este tutorial consiste en publicar el servicio móvil en Azure y ejecutar la aplicación en el servicio en directo.

>[AZURE.NOTE]En este procedimiento se explica cómo publicar el servicio móvil con las herramientas de Visual Studio. También puede publicar el servicio móvil back-end de .NET con la utilización del control de origen. Para obtener más información, consulte [Almacenamiento de códigos de proyectos en control de código fuente](../articles/mobile-services/mobile-services-dotnet-backend-store-code-source-control.md).

1. En el Explorador de soluciones, haga clic con el botón secundario en el proyecto de servicio móvil, haga clic en **Publicar** y, a continuación, en el cuadro de diálogo **Publicación web**, haga clic en **Servicios móviles de Azure**.

2. Inicie sesión con las credenciales de la cuenta de Azure, seleccione el servicio en **Servicios móviles existentes** y haga clic en **Aceptar**. Visual Studio descarga la configuración de publicación directamente desde Azure.

	![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-select-service.png)

3. Haga clic en **Validar conexión** para verificar que la publicación está bien configurada y, a continuación, haga clic en **Publicar**.

	![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-2.png)

	Una vez realizada la publicación, volverá a ver la página de confirmación de que el servicio móvil está en funcionamiento, aunque esta vez en Azure.

<!--HONumber=54-->