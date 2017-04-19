
1. Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.

2. Haga clic en **Proceso** > **Function App**, seleccione la **Suscripción**, escriba un **Nombre de la aplicación** único, que identifica la aplicación de función, y especifique la siguiente configuración:
   
   * **[Grupo de recursos](../articles/azure-resource-manager/resource-group-overview.md)**: seleccione **Crear nuevo** y escriba un nombre para el nuevo grupo de recursos. 
   * **[Plan de hospedaje](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)**, que puede ser cualquiera de los siguientes: 
     * **Plan de consumo**: el tipo de plan predeterminado para Azure Functions. Al elegir un plan de consumo, también debe elegir la ubicación en el campo **Ubicación**.  
     * **Plan de App Service**: este plan requiere que se cree un **plan de App Service/ubicación** o se seleccione uno existente. Esta configuración determina la [ubicación, las características, el costo y los recursos de proceso](https://azure.microsoft.com/pricing/details/app-service/) asociados a la aplicación.  
   * **Cuenta de almacenamiento**: cada aplicación de función requiere una cuenta de almacenamiento. Puede elegir una cuenta de almacenamiento existente o [crear una](../articles/storage/storage-create-storage-account.md#create-a-storage-account). 
     
    ![Creación de una aplicación de función en Azure Portal](./media/functions-create-function-app-portal/function-app-create-flow.png)

3. Haga clic en **Crear** para aprovisionar e implementar la nueva función de aplicación.  
