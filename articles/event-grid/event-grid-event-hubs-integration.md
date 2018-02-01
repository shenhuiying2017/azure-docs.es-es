---
title: "Integración de Azure Event Grid y Azure Event Hubs"
description: "Se explica cómo usar Azure Event Grid y Azure Event Hubs para migrar datos a un SQL Data Warehouse"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/19/2018
ms.author: tomfitz
ms.openlocfilehash: b315bd77a47a6f106c5768da56828a5169de5fe9
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/20/2018
---
# <a name="stream-big-data-into-a-data-warehouse"></a>Transmitir macrodatos a un almacenamiento de datos

Azure [Event Grid](overview.md) es un servicio inteligente de enrutamiento de eventos que permite reaccionar ante las notificaciones procedentes de aplicaciones y servicios. En este [ejemplo de la función de captura de Event Hubs y Event Grid](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) se indica cómo usar la función de captura de Azure Event Hubs con Azure Event Grid para migrar datos desde un centro de eventos a un SQL Data Warehouse sin ningún tipo de problema.

![Información general de la aplicación](media/event-grid-event-hubs-integration/overview.png)

Mientras se envían datos al centro de eventos, la función de captura extrae datos de la transmisión y genera blobs de almacenamiento con los datos en formato Avro. Cuando la función de captura genera el blob, desencadena un evento. Event Grid distribuye información sobre el evento a los suscriptores. En este caso, los datos de evento se envían al punto de conexión de Azure Functions. Los datos de evento incluyen la ruta de acceso del blob generado. La función usa esa dirección URL para recuperar el archivo y enviarlo al almacenamiento de datos.

En este artículo:

* Implementaremos la siguiente infraestructura:
  * Un centro de eventos con la función de captura habilitada
  * Una cuenta de almacenamiento para los archivos de la función de captura
  * Un plan de Azure App Service para hospedar la aplicación de función
  * Una aplicación de función para procesar el evento
  * Un servidor SQL Server para hospedar el almacenamiento de datos
  * Un SQL Data Warehouse para almacenar los datos migrados
* Crearemos una tabla en el almacenamiento de datos.
* Agregaremos código a la aplicación de función.
* Nos suscribiremos al evento.
* Ejecutaremos la aplicación que envía datos al centro de eventos.
* Veremos los datos migrados en el almacenamiento de datos.

## <a name="about-the-event-data"></a>Acerca de los datos del evento

Event Grid distribuye datos del evento a los suscriptores. En el siguiente ejemplo se muestran datos de evento para crear un archivo de la función de captura. Fíjese concretamente en la propiedad `fileUrl` del objeto `data`. La aplicación de función obtiene este valor y lo usa para recuperar el archivo de la función de captura.

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        }
    }
]
```

## <a name="prerequisites"></a>requisitos previos

Para realizar este tutorial, necesitará lo siguiente:

* Una suscripción de Azure. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.
* [Visual Studio 2017, versión 15.3.2 o superior](https://www.visualstudio.com/vs/) con cargas de trabajo para: desarrollo de escritorio de .NET, desarrollo de Azure, desarrollo web y de ASP.NET, desarrollo de Node.js y desarrollo de Python.
* El [proyecto de ejemplo EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) descargado en el equipo.

## <a name="deploy-the-infrastructure"></a>Implementar la infraestructura

Para hacer este artículo más sencillo, implementaremos la infraestructura que necesitamos con una plantilla de Resource Manager. Para ver los recursos que se van a implementar, vea la [plantilla](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). En la versión preliminar, Event Grid admite las regiones **westus2** y **westcentralus**. Use una de estas regiones como ubicación del grupo de recursos.

Para la CLI de Azure, utilice:

```azurecli-interactive
az group create -l westcentralus -n rgDataMigrationSample

az group deployment create \
  --resource-group rgDataMigrationSample \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

Para PowerShell, use:

```powershell
New-AzureRmResourceGroup -Name rgDataMigration -Location westcentralus

New-AzureRmResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```

Proporcione un valor de contraseña cuando se le solicite.

## <a name="create-a-table-in-sql-data-warehouse"></a>Crear una tabla en SQL Data Warehouse

Agregue una tabla al almacenamiento de datos, ejecutando para ello el script [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql). Para ejecutar el script, use Visual Studio o el Editor de consultas en el portal.

El script que hay que ejecutar es este:

```sql
CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
    [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
    [MeasureTime] datetime NULL, 
    [GeneratedPower] float NULL, 
    [WindSpeed] float NULL, 
    [TurbineSpeed] float NULL
)
WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
```

## <a name="publish-the-azure-functions-app"></a>Publicar la aplicación de Azure Functions

1. Abra el [proyecto de ejemplo EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) en Visual Studio 2017 (15.3.2 o superior).

2. En el Explorador de soluciones, haga clic con el botón derecho en **FunctionDWDumper** y seleccione **Publicar**.

   ![Publicar la aplicación de función](media/event-grid-event-hubs-integration/publish-function-app.png)

3. Seleccione **Azure Function App** y **Seleccionar existente**. Seleccione **Aceptar**.

   ![Establecer la aplicación de función como destino](media/event-grid-event-hubs-integration/pick-target.png)

4. Seleccione la aplicación de función que implementó con la plantilla. Seleccione **Aceptar**.

   ![Seleccionar la aplicación de función](media/event-grid-event-hubs-integration/select-function-app.png)

5. Cuando Visual Studio haya configurado el perfil, seleccione **Publicar**.

   ![Select publish](media/event-grid-event-hubs-integration/select-publish.png)

6. Tras publicar la función, vaya a [Azure Portal](https://portal.azure.com/). Seleccione el grupo de recursos y la aplicación de función.

   ![Ver la aplicación de función](media/event-grid-event-hubs-integration/view-function-app.png)

7. Seleccione la función.

   ![Seleccionar la función](media/event-grid-event-hubs-integration/select-function.png)

8. Obtenga la dirección URL de la función. Necesitará esta dirección URL al crear la suscripción a evento.

   ![Obtención de dirección URL de la función](media/event-grid-event-hubs-integration/get-function-url.png)

9. Copie el valor.

   ![Copiar la dirección URL](media/event-grid-event-hubs-integration/copy-url.png)

## <a name="subscribe-to-the-event"></a>Nos suscribiremos al evento.

Puede usar la CLI de Azure o el portal para suscribirse al evento. En este artículo se contemplan ambos métodos.

### <a name="portal"></a>Portal

1. En el espacio de nombres de Event Hubs, seleccione **Event Grid** a la izquierda.

   ![Seleccionar Event Grid](media/event-grid-event-hubs-integration/select-event-grid.png)

2. Agregue una suscripción a evento.

   ![Agregar una suscripción a evento](media/event-grid-event-hubs-integration/add-event-subscription.png)

3. Proporcione los valores de la suscripción a evento. Use la dirección URL de Azure Functions que copió. Seleccione **Crear**.

   ![Proporcionar valores de suscripción](media/event-grid-event-hubs-integration/provide-values.png)

### <a name="azure-cli"></a>Azure CLI

Para suscribirse al evento, ejecute los siguientes comandos, que requieren la versión 2.0.24 o una versión posterior de la CLI de Azure:

```azurecli-interactive
namespaceid=$(az resource show --namespace Microsoft.EventHub --resource-type namespaces --name <your-EventHubs-namespace> --resource-group rgDataMigrationSample --query id --output tsv)
az eventgrid event-subscription create \
  --resource-id $namespaceid \
  --name captureEventSub \
  --endpoint <your-function-endpoint>
```

## <a name="run-the-app-to-generate-data"></a>Ejecutar la aplicación para generar datos

Ya ha terminado de configurar el centro de eventos, el SQL Data Warehouse, la aplicación de Azure Functions y la suscripción a evento. La solución está lista para migrar datos desde el centro de eventos al almacenamiento de datos. Hay que configurar algunos valores antes de ejecutar una aplicación que genere los datos del centro de eventos.

1. En el portal, seleccione el espacio de nombres del centro de eventos. Seleccione **Cadenas de conexión**.

   ![Seleccionar Cadenas de conexión](media/event-grid-event-hubs-integration/event-hub-connection.png)

2. Seleccione **RootManageSharedAccessKey**.

   ![Seleccionar la clave](media/event-grid-event-hubs-integration/show-root-key.png)

3. Copie **Cadena de conexión: clave principal**.

   ![Copiar clave](media/event-grid-event-hubs-integration/copy-key.png)

4. Vuelva al proyecto de Visual Studio. En el proyecto WindTurbineDataGenerator, abra **program.cs**.

5. Reemplace los dos valores constantes. Use el valor copiado en **EventHubConnectionString**. Use el nombre del centro de eventos en **EventHubName**.

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://tfdatamigratens.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Compile la solución. Ejecute la aplicación WindTurbineGenerator.exe. Transcurridos unos minutos, realice una consulta en la tabla del almacenamiento de datos relativa a los datos migrados.

## <a name="next-steps"></a>pasos siguientes

* Para obtener una introducción a Event Grid, vea [Acerca de Event Grid](overview.md).
* Para obtener una introducción a la función de captura de Event Hubs, vea [Habilitación de la funcionalidad de captura de Event Hubs mediante Azure Portal](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Para más información sobre cómo configurar y ejecutar el ejemplo, vea el [ejemplo de la función de captura de Event Hubs y Event Grid](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).