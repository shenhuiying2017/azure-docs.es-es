Tras probar la aplicación cliente con la aplicación móvil local, el paso final de este tutorial es publicar el back-end de la aplicación móvil en Azure y ejecutar la aplicación en el servicio en directo.

> [AZURE.NOTE] En este procedimiento se explica cómo publicar un back-end de aplicación móvil con las herramientas de Visual Studio. También se puede publicar un back-end de .NET mediante el control de código fuente.

1. En el Explorador de soluciones, haga clic con el botón secundario en el proyecto de código de la aplicación móvil (es el nombre de la aplicación con el término "Servicio" anexado) y seleccione **Publicar**. 

	![Select publish on the app code project](./media/app-service-mobile-dotnet-backend-publish-service-preview/dotnet-project-publish.png)

2. En el cuadro de diálogo **Publicación web**, seleccione **Aplicación móvil de Azure** como destino de publicación. En el cuadro de diálogo que aparece, seleccione una aplicación móvil existente, que será el nombre de la aplicación móvil con "code" anexado.

    ![Select an existing web app to publish to](./media/app-service-mobile-dotnet-backend-publish-service-preview/mobile-quickstart-publish-select-service.png)

3. Haga clic en **Validar conexión** para verificar que la publicación está bien configurada y, a continuación, haga clic en **Publicar**.

	![Publish settings wizard final page](./media/app-service-mobile-dotnet-backend-publish-service-preview/dotnet-publish-settings.png)

   Una vez realizada la publicación, verá la página que confirma que el back-end de aplicación móvil está en funcionamiento en Azure. La ventana de salida de Visual Studio también mostrará que la operación se ha realizado correctamente.

<!--HONumber=49-->