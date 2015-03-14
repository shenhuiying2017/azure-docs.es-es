
1. En el Explorador de soluciones de Visual Studio, haga clic con el botón secundario en el proyecto de servicio y haga clic en **Iniciar nueva instancia**, que está en el menú contextual **Depurar**.

    ![start mobile service project locally](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/vs-start-debug-service-project.png)

    Visual Studio abre la página web predeterminada del servicio. De forma predeterminada, Visual Studio hospeda el servicio móvil de manera local en IIS Express.

2. Haga clic con el botón secundario en el icono de bandeja de IIS Express en la barra de tareas de Windows y compruebe que el servicio móvil se haya iniciado.

	 ![verify the mobile service in the taskbar](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/iis-express-tray.png)

3. En la página de inicio de su servicio móvil, haga clic en **probarlo**.

    ![mobile service start up page](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-welcome-page.png)

    Esto muestra la página de documentación de la API, que puede usar para probar el servicio móvil.

	>[AZURE.NOTE] No se requiere autenticación para obtener acceso a esta página cuando se ejecuta de forma local. Al ejecutarse en Azure, debe suministrar la clave de la aplicación como contraseña (sin nombre de usuario) para obtener acceso a la página.

4. Haga clic en el vínculo **GET tables/TodoItem**.

	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-api-documentation-page.png)
   	
	Esto muestra la página de respuesta GET de la API.

5. Haga clic en **probarlo** y luego haga clic en **enviar**.
 
	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-todoitems.png)

	Esto envía una solicitud GET al servicio móvil para devolver todas las filas de la tabla TodoItem. Como la tabla la arranca el inicializador, se devuelven dos objetos TodoItem en el cuerpo del mensaje de respuesta. Para más información sobre los inicializadores, vea [Modificación del modelo de datos de un servicio móvil back-end .NET](./es-es/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations/).

	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-response.png)

<!--HONumber=42-->
