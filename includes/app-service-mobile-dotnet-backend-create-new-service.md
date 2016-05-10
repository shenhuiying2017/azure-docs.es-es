1. Inicie sesión en el [Portal de Azure].

2. Haga clic en **+NUEVO** > **Web y móvil** > **Aplicación móvil** y, a continuación, proporcione un nombre para el back-end de la aplicación móvil.

3. Para el **grupo de recursos**, seleccione un grupo de recursos existente o cree uno nuevo (con el mismo nombre que su aplicación).
 
4. Seleccione **Plan del Servicio de aplicaciones**, seleccione un plan ya existente o [cree uno nuevo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan). Haga clic en **Crear**. Esto crea el back-end de aplicación móvil La configuración del plan del Servicio de aplicaciones determina la [ubicación, características, costo y recursos de procesos](https://azure.microsoft.com/pricing/details/app-service/) asociados a la aplicación.
	
6. En la hoja **Configuración** del nuevo back.end de Aplicaciones móviles, haga clic en **Inicio rápido** > la plataforma de aplicaciones cliente > **Conectar a una base de datos**.

	![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)

7. En la hoja **Agregar conexión de datos**, haga clic en **Base de datos SQL** > **Crear una nueva base de datos**, escriba el **Nombre** de la base de datos, seleccione un plan de tarifa y haga clic en **Servidor**. Puede reutilizar esta nueva base de datos. Si ya tiene una base de datos en la misma región, puede elegir en su lugar **usar una base de datos existente**. No se recomienda el uso de una base de datos en una ubicación diferente debido a los costos de ancho de banda y a una mayor latencia.
 
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)

8. En la hoja **Nuevo servidor**, escriba un nombre de servidor único en el campo **Nombre del servidor**, proporcione un inicio de sesión y una contraseña, seleccione **Permitir que los servicios de Azure accedan al servidor** y haga clic en **Aceptar**. Esto creará la nueva base de datos.

9. De nuevo en la hoja **Agregar conexión de datos**, haga clic en **Cadena de conexión**, escriba los valores de inicio de sesión y una contraseña para la base de datos, y haga clic en **Aceptar**. Espere unos minutos para que se implemente la base de datos correctamente antes de continuar.

<!-- URLs. -->
[Portal de Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->