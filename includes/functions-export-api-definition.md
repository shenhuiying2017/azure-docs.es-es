## <a name="export-an-api-definition"></a>Exportar una definición de API
Ya tiene la definición de OpenAPI para su función; la creó siguiendo las instrucciones de [Create an OpenAPI definition for a function](../articles/azure-functions/functions-openapi-definition.md) (Creación de una definición de OpenAPI para una función). El siguiente paso de este proceso es exportar la definición de la API para que PowerApps y Microsoft Flow puedan usarla en una API personalizada.

> [!IMPORTANT]
> Recuerde que debe iniciar sesión en Azure con las mismas credenciales que usa para sus inquilinos de PowerApps y Microsoft Flow. Esto permite a Azure crear la API personalizada y ponerla a disposición de PowerApps y Microsoft Flow.

1. En [Azure Portal](https://portal.azure.com), haga clic en el nombre de aplicación de función (como **function-demo-energy**) > **Características de la plataforma** > **Definición de API**.

    ![Definición de API](media/functions-export-api-definition/api-definition.png)

1. Haga clic en **Exportar a PowerApps + Flow**.

    ![Origen de definición de la API](media/functions-export-api-definition/export-api-1.png)

1. En el panel derecho, use la configuración que se especifica en la tabla.

    |Configuración|Descripción|
    |--------|------------|
    |**Modo de exportación**|Seleccione **Rápido** para generar la API personalizada de forma automática. Si selecciona **Manual**, se exporta la definición de la API, pero después tendrá que importarla en PowerApps y Microsoft Flow de forma manual. Para más información, consulte, [Exportación a PowerApps y Microsoft Flow](../articles/azure-functions/app-service-export-api-to-powerapps-and-flow.md).|
    |**Entorno**|Seleccione el entorno en que se debe guardar la API personalizada. Para más información, consulte [Información general sobre los entornos (PowerApps)](https://powerapps.microsoft.com/tutorials/environments-overview/) o [Información general sobre los entornos (Microsoft Flow)](https://us.flow.microsoft.com/documentation/environments-overview-admin/).|
    |**Nombre de la API personalizada**|Escriba un nombre, por ejemplo, `Turbine Repair`.|
    |**Nombre de clave de API**|Escriba el nombre que deben ver los compiladores de aplicación y flujo en la interfaz de usuario de la API personalizada. Tenga en cuenta que en el ejemplo se incluye información útil.|
 
    ![Exportación a PowerApps y Microsoft Flow](media/functions-export-api-definition/export-api-2.png)

1. Haga clic en **Aceptar**. La API personalizada se ha compilado y agregado al entorno especificado.