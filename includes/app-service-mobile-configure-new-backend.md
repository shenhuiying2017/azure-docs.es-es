
1. De nuevo en la configuración del back-end de aplicación móvil, desplácese hacia abajo hasta **Móvil**, haga clic en **Introducción** > la plataforma cliente. 

2. En **Crear una API de tabla**, seleccione su **Lenguaje back-end**, ya sea **C#** o **Node.js**:

	+ **Back-end de .NET (C#)**: haga clic en **Descargar**, extraiga los archivos de proyecto comprimidos en el equipo local, abra la solución en Visual Studio, compile el proyecto para restaurar los paquetes de NuGet y después implemente el proyecto en Azure. Para obtener información sobre cómo implementar un proyecto de servidor back-end de .NET en Azure, consulte la sección *Implementar el proyecto en la aplicación web* de [Crear una aplicación web ASP.NET en el Servicio de aplicaciones de Azure](../articles/app-service-web/web-sites-dotnet-get-started.md#deploy-the-project-to-the-web-app). En el Servicio de aplicaciones, un back-end de aplicación móvil es equivalente a una aplicación web.
	 
	+ **Back-end de Node.js**: acepte la confirmación y haga clic en **Crear tabla TodoItem**. Esto crea una nueva tabla *TodoItem* en la base de datos.
	 
		>[AZURE.IMPORTANT]Al cambiar un back-end de aplicación existente a Node.js, se sobrescribirá todo el contenido del sitio.

Ahora el back-end de aplicación móvil está listo para usarse con la aplicación cliente.

<!---HONumber=Nov15_HO4-->