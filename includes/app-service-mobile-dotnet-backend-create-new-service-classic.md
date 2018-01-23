1. Inicie sesión en [Azure Portal].
2. Haga clic en **+NUEVO** > **Web y móvil** > **Aplicación móvil** y, después, proporcione un nombre para el back-end de la aplicación móvil.
3. En **Grupo de recursos**, seleccione un grupo de recursos existente o cree uno nuevo (con el mismo nombre que su aplicación). 
   
    Puede seleccionar un plan de App Service ya existente o crear uno nuevo. Para más información acerca de los planes de App Service y cómo crear un nuevo plan en un plan de tarifa diferente en la ubicación deseada, consulte [Introducción detallada sobre los planes de Azure App Service](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
4. Para **plan de App Service**, se selecciona el plan predeterminado (en el [nivel estándar](https://azure.microsoft.com/pricing/details/app-service/)). También puede seleccionar otro plan o [crear uno](../articles/app-service/app-service-plan-manage.md#create-an-app-service-plan). La configuración del plan de App Service determina la [ubicación, las características, el costo y los recursos de proceso](https://azure.microsoft.com/pricing/details/app-service/) asociados a la aplicación. 
   
    Después de decidir el plan, haga clic en **Crear**. Esto crea el back-end de aplicación móvil 
5. En la hoja **Configuración** del nuevo back.end de Aplicaciones móviles, haga clic en **Inicio rápido** > la plataforma de aplicaciones cliente > **Conectar a una base de datos**. 
   
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
6. En la hoja **Agregar conexión de datos**, haga clic en **SQL Database** > **Crear una nueva base de datos**, escriba el **nombre** de la base de datos, seleccione un plan de tarifa y haga clic en **Servidor**.  Puede reutilizar esta nueva base de datos. Si ya tiene una base de datos en la misma ubicación, puede elegir **Usar base de datos existente**. No se recomienda el uso de una base de datos en una ubicación diferente debido a los costos de ancho de banda y a una mayor latencia.
   
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
7. En la hoja **Nuevo servidor**, escriba un nombre de servidor único en el campo **Nombre del servidor**, proporcione un inicio de sesión y una contraseña, active **Permitir que los servicios de Azure accedan al servidor** y haga clic en **Aceptar**. Esto creará la nueva base de datos.
8. De nuevo en la hoja **Agregar conexión de datos**, haga clic en **Cadena de conexión**, escriba los valores de inicio de sesión y contraseña para la base de datos y haga clic en **Aceptar**. Espere unos minutos para que se implemente la base de datos correctamente antes de continuar.

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/
