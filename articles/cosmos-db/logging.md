---
title: Registro de diagnóstico de Azure Cosmos DB | Microsoft Docs
description: Use este tutorial para obtener ayuda para empezar a trabajar con el registro de Azure Cosmos DB.
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
tags: azure-resource-manager
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: sngun
ms.openlocfilehash: 6bad6c7cd641a2ef5461ee37b89c0bdc6a221017
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
---
# <a name="azure-cosmos-db-diagnostic-logging"></a>Registro de diagnóstico de Azure Cosmos DB

Después de comenzar a usar una o más bases de datos de Azure Cosmos DB, puede que quiera supervisar cómo y cuándo se accede a las bases de datos. En este artículo se proporciona información general de los registros que están disponibles en la plataforma Azure. Aprenderá cómo habilitar el registro de diagnóstico con fines de supervisión para enviar registros a [Azure Storage ](https://azure.microsoft.com/services/storage/), cómo transmitir registros a [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) y cómo exportar registros a [Azure Log Analytics ](https://azure.microsoft.com/services/log-analytics/).

## <a name="logs-available-in-azure"></a>Registros disponibles en Azure

Antes de explicar cómo supervisar la cuenta de Azure Cosmos DB, aclaremos algunos aspectos del registro y la supervisión. Hay diferentes tipos de registros en la plataforma Azure. Hay [registros de actividades de Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), [registros de diagnóstico de Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), [métricas de Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics), eventos, supervisión de latidos, registros de operaciones, entre otros. Hay un sinfín de registros. Puede ver una lista completa de los registros en [Azure Log Analytics](https://azure.microsoft.com/en-us/services/log-analytics/) en Azure Portal. 

En la siguiente imagen se muestran los diferentes tipos de registros de Azure disponibles:

![Diferentes tipos de registros de Azure](./media/logging/azurelogging.png)

En la imagen anterior, los **recursos de proceso** representan los recursos de Azure a los que puede acceder el sistema operativo invitado. Por ejemplo, Azure Virtual Machines, los conjuntos de escalado de máquinas virtuales, Azure Container Service se consideran recursos de proceso. Los recursos de proceso generan registros de actividad, registros de diagnóstico y registros de aplicaciones. Para más información, consulte el artículo [Supervisión de Azure: recursos de proceso](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#azure-monitor-sources---compute-subset).

Los **recursos no de proceso** son recursos a los que no puede acceder el sistema operativo subyacente y funcionan directamente con recursos. Por ejemplo, grupos de seguridad de red o Logic Apps. Azure Cosmos DB es un recurso no de proceso. Puede ver los registros de los recursos no de proceso en el registro de actividad o si habilita la opción de registros de diagnóstico en el portal. Para más información, consulte el artículo [Supervisión de Azure: recursos no de proceso](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#azure-monitor-sources---everything-else).

El registro de actividad registra las operaciones a un nivel de suscripción para Azure Cosmos DB. Se registran operaciones como ListKeys, Write DatabaseAccounts y otras. Los registros de diagnóstico proporcionan un registro más pormenorizado y le permiten iniciar DataPlaneRequests (crear, leer, consultar...) y MongoRequests.


En este artículo, nos centramos en el registro de actividad de Azure, registros de diagnóstico de Azure y métricas de Azure. ¿Cuál es la diferencia entre estos tres registros? 

### <a name="azure-activity-log"></a>Azure Activity Log

El registro de actividad de Azure es un registro de suscripción que proporciona información sobre los eventos de nivel de suscripción que se han producido en Azure. El registro de actividad notifica los eventos de plano de control de las suscripciones en la categoría administrativa. Puede utilizar el registro de actividad para determinar los interrogantes “qué, quién y cuándo” de las operaciones de escritura (PUT, POST, DELETE) en los recursos de la suscripción. También puede conocer el estado de la operación y otras propiedades relevantes. 

El registro de actividad es distinto de los registros de diagnóstico. El registro de actividad proporciona datos sobre las operaciones en un recurso desde el exterior (el _plano de control_). En el contexto de Azure Cosmos DB, las operaciones del plano de control incluyen la creación de colecciones, enumeración de claves, eliminación de claves o enumeración de bases de datos, entre otras. Los registros de diagnóstico son emitidos por un recurso y proporcionan información sobre el funcionamiento de dicho recurso (el _plano de datos_). Algunos ejemplos de las operaciones del plano de datos en el registro de diagnóstico son Delete, Insert y ReadFeed.

Los registros de actividad (operaciones del plano de control) pueden ser mejores por naturaleza, pueden incluir la lista completa de direcciones de correo electrónico del autor de la llamada, la dirección IP de este, el nombre del recurso, el nombre de la operación y el identificador del inquilino, entre otros. El registro de actividad contiene varias [categorías](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema) de datos. Para obtener todos los detalles sobre los esquemas de estas categorías, consulte [Esquema de eventos del registro de actividad de Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema). Sin embargo, los registros de diagnóstico pueden ser restrictivos por naturaleza ya que los datos de información de identificación personal a menudo se eliminan de esos registros. Puede tener la dirección IP del autor de la llamada, pero se quitará el último octeto.

### <a name="azure-metrics"></a>Métricas de Azure

Las [métricas de Azure](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-metrics) (también denominadas _contadores de rendimiento_) tienen el tipo de telemetría de Azure más importante emitido por la mayoría de los recursos de Azure. Las métricas le permiten ver información sobre el rendimiento, almacenamiento, coherencia, disponibilidad y latencia de los recursos de Azure Cosmos DB. Para más información, consulte [Supervisión y depuración con métricas de Azure Cosmos DB](use-metrics.md).

### <a name="azure-diagnostic-logs"></a>Registros de diagnóstico de Azure

Un recurso emite registros de diagnóstico de Azure que proporcionan datos exhaustivos y frecuentes acerca del funcionamiento de ese recurso. El contenido de estos registros varía según el tipo de recurso. Los registros de diagnóstico de nivel de recursos también difieren de los registros de diagnóstico de nivel de sistema operativo invitado. Estos son los recopilados por un agente que se ejecuta dentro de una máquina virtual u otro tipo de recurso admitido. Los registros de diagnóstico de nivel de recursos no requieren ningún agente y capturan datos específicos de recursos de la propia plataforma Azure. Los registros de diagnóstico a nivel del sistema operativo invitado capturan los datos del sistema operativo y de las aplicaciones que se ejecutan en una máquina virtual.

![Registro de diagnóstico en Storage, Event Hubs o Log Analytics](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>¿Qué es lo que registran los registros de diagnóstico de Azure?

* Se registran todas las solicitudes de back-end autenticadas (TCP/REST) entre todas las API, incluidas las solicitudes con error debido a permisos de acceso, errores del sistema o solicitudes incorrectas. En la actualidad, no hay soporte técnico disponible para las solicitudes iniciadas por el usuario de Graph, Cassandra y Table API.
* Operaciones en la base de datos misma, que incluyen operaciones CRUD en todos los documentos, contenedores y bases de datos.
* Operaciones en claves de cuenta, que incluyen la creación, modificación o eliminación de estas claves.
* Solicitudes no autenticadas que dan como resultado una respuesta 401. Por ejemplo, las solicitudes que no tienen un token de portador, cuyo formato es incorrecto o está caducado o tienen un token no válido.

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Activar el registro en Azure Portal

Para habilitar el registro de diagnóstico, debe tener los siguientes recursos:

* Una cuenta existente de Azure Cosmos DB, una base de datos y un contenedor. Para obtener instrucciones sobre cómo crear estos recursos, consulte [Creación de una cuenta de base de datos mediante Azure Portal](create-sql-api-dotnet.md#create-a-database-account), [Ejemplos de la CLI de Azure](cli-samples.md) o [Ejemplos de PowerShell](powershell-samples.md).

Para habilitar el registro de diagnóstico en Azure Portal, siga estos pasos:

1. En [Azure Portal](https://portal.azure.com), en la cuenta de Azure Cosmos DB, seleccione **Registros de diagnóstico** en el panel de navegación izquierdo y luego seleccione **Activar diagnósticos**.

    ![Activar el registro de diagnóstico para Azure Cosmos DB en Azure Portal](./media/logging/turn-on-portal-logging.png)

2. En la página **Configuración de diagnóstico**, siga estos pasos: 

    * **Name** (Nombre): escriba un nombre para los registros que quiere crear.

    * **Archivar en una cuenta de almacenamiento**: para usar esta opción, necesita una cuenta de almacenamiento existente a la cual conectarse. Para crear una nueva cuenta de almacenamiento en el portal, consulte [Crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md) y siga las instrucciones para crear una cuenta de uso general de Azure Resource Manager. Luego, vuelva a esta página en el portal para seleccionar la cuenta de almacenamiento. Las cuentas de almacenamiento recién creadas pueden tardar unos minutos en aparecer en el menú desplegable.
    * **Transmitir a un centro de eventos**: para usar esta opción, necesita un espacio de nombres de Event Hubs y un centro de eventos al que conectarse. Para crear un espacio de nombres de Event Hubs, consulte [Creación de un espacio de nombres de Event Hubs y un centro de eventos con Azure Portal](../event-hubs/event-hubs-create.md). Luego, vuelva a esta página en el portal para seleccionar el espacio de nombres de Event Hubs y el nombre de la directiva.
    * **Enviar a Log Analytics**: para usar esta opción, use un área de trabajo existente o cree una nueva área de trabajo de Log Analytics siguiendo los pasos necesarios para [crear una nueva área de trabajo](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace) en el portal. Para más información sobre cómo ver los registros de Log Analytics, consulte [Visualización de los registros de Log Analytics](#view-in-loganalytics).
    * **Log DataPlaneRequests** (Registrar DataPlaneRequests): seleccione esta opción para registrar las solicitudes de back-end procedentes de la plataforma distribuida subyacente para cuentas de SQL, Graph, MongoDB, Cassandra y Table API de Azure Cosmos DB. Si va a archivar en una cuenta de almacenamiento, puede seleccionar el período de retención para los registros de diagnóstico. Los registros se eliminan automáticamente una vez expira el período de retención.
    * **Log MongoRequests** (Registrar MongoRequests): seleccione esta opción para registrar las solicitudes iniciadas por el usuario procedentes del front-end de Azure Cosmos DB que funciona con cuentas de la API de MongoDB. Si va a archivar en una cuenta de almacenamiento, puede seleccionar el período de retención para los registros de diagnóstico. Los registros se eliminan automáticamente una vez expira el período de retención.
    * **Metric Requests** (Solicitudes de métricas): seleccione esta opción para almacenar los datos detallados en [Métricas de Azure](../monitoring-and-diagnostics/monitoring-supported-metrics.md). Si va a archivar en una cuenta de almacenamiento, puede seleccionar el período de retención para los registros de diagnóstico. Los registros se eliminan automáticamente una vez expira el período de retención.

3. Seleccione **Guardar**.

    Si recibe un error que indica que "no se pudieron actualizar los diagnósticos para \<nombre de área de trabajo>. La suscripción \<Id. de suscripción > no está registrada para usar microsoft.insights, "siga las instrucciones de [solución de problemas de Azure Diagnostics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) para registrar la cuenta y, después, vuelva a intentar este procedimiento.

    Si desea cambiar el modo en que se guardan los registros de diagnóstico en algún momento, vuelva a esta página para modificar la configuración de registro de diagnóstico de su cuenta.

## <a name="turn-on-logging-by-using-azure-cli"></a>Activación del registro con la CLI de Azure

Para habilitar las métricas y los registros de diagnóstico con la CLI de Azure, use los siguientes comandos:

- Para habilitar el almacenamiento de registros de diagnóstico en una cuenta de almacenamiento, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   `resourceId` es el nombre de la cuenta de Azure Cosmos DB. `storageId` es el nombre de la cuenta de almacenamiento a la que se van a enviar los registros.

- Para habilitar el streaming de registros de diagnóstico a un centro de eventos, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   `resourceId` es el nombre de la cuenta de Azure Cosmos DB. `serviceBusRuleId` es una cadena con este formato:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Para habilitar el envío de registros de diagnóstico a un área de trabajo de Log Analytics, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Puede combinar estos parámetros para habilitar varias opciones de salida.

## <a name="turn-on-logging-by-using-powershell"></a>Activación del registro con PowerShell

Para activar el registro de diagnóstico con PowerShell, necesita Azure PowerShell, con la versión 1.0.1 como mínimo.

Para instalar Azure PowerShell y asociarlo con una suscripción de Azure, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).

Si ya instaló Azure PowerShell y no sabe la versión, en la consola de PowerShell, escriba `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Conexión a las suscripciones
Inicie una sesión de PowerShell de Azure e inicie sesión en su cuenta de Azure con el siguiente comando:  

```powershell
Connect-AzureRmAccount
```

En la ventana emergente del explorador, escriba el nombre de usuario y la contraseña de su cuenta de Azure. Azure PowerShell obtiene todas las suscripciones asociadas a esta cuenta y, de forma predeterminada, usa la primera.

Si tiene más de una suscripción, es posible que deba especificar la suscripción específica que se usó para crear su almacén de claves de Azure. Para ver las suscripciones de su cuenta, escriba el siguiente comando:

```powershell
Get-AzureRmSubscription
```

A continuación, para especificar la suscripción asociada a la cuenta de Azure Cosmos DB que registrará, escriba el siguiente comando:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Si tiene más de una suscripción asociada a su cuenta, es importante especificar la suscripción que quiere usar.
>
>

Para más información sobre cómo configurar PowerShell de Azure, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).

### <a id="storage"></a>Creación de una cuenta de almacenamiento nueva para los registros
Aunque puede usar una cuenta de almacenamiento existente para sus registros, en este tutorial crearemos una nueva cuenta de almacenamiento exclusiva para los registros de Azure Cosmos DB. Para mayor comodidad, almacenamos los detalles de la cuenta de almacenamiento en una variable denominada **sa**.

A fin de facilitar la administración, en este tutorial usamos el mismo grupo de recursos que el que contiene la base de datos de Azure Cosmos DB. Sustituya los valores por los parámetros **ContosoResourceGroup**, **contosocosmosdblogs** y **North Central US**, según corresponda:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Si decide usar una cuenta de almacenamiento existente, la cuenta debe usar la misma suscripción que la suscripción a la instancia de Azure Cosmos DB. La cuenta también debe usar el modelo de implementación de Resource Manager, en lugar del modelo de implementación clásica.
>
>

### <a id="identify"></a>Identificar la cuenta de Azure Cosmos DB para sus registros
Establezca el nombre de la cuenta de Azure Cosmos DB en una variable denominada **account**, donde **ResourceName** es el nombre de la cuenta de Azure Cosmos DB.

```powershell
$account = Get-AzureRmResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Habilitación del registro
Para habilitar el registro para Azure Cosmos DB, use el cmdlet `Set-AzureRmDiagnosticSetting` con las variables para la nueva cuenta de almacenamiento, la cuenta de Azure Cosmos DB y la categoría para la que hay que habilitar el registro. Ejecute el siguiente comando y establezca la marca **-Enabled** en **$true**:

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

La salida del comando debe ser similar a la del siguiente ejemplo:

```powershell
    StorageAccountId            : /subscriptions/<subscription-ID>/resourceGroups/ContosoResourceGroup/providers`
    /Microsoft.Storage/storageAccounts/contosocosmosdblogs
    ServiceBusRuleId            :
    EventHubAuthorizationRuleId :
    Metrics
        TimeGrain       : PT1M
        Enabled         : False
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    Logs
        Category        : DataPlaneRequests
        Enabled         : True
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    WorkspaceId                 :
    Id                          : /subscriptions/<subscription-ID>/resourcegroups/ContosoResourceGroup/providers`
    /microsoft.documentdb/databaseaccounts/contosocosmosdb/providers/microsoft.insights/diagnosticSettings/service
    Name                        : service
    Type                        :
    Location                    :
    Tags                        :
```

La salida del comando confirma que el registro está ahora habilitado para la base de datos y que la información se está guardando en la cuenta de almacenamiento.

Opcionalmente también se puede establecer una directiva de retención para los registros, de forma que los registros más antiguos se eliminen automáticamente. Por ejemplo, establezca la directiva de retención con la marca **-RetentionEnabled** establecida en **$true**. Establezca el parámetro **-RetentionInDays** en **90** para que los registros de más de 90 días se eliminen automáticamente.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Acceso a los registros
Los registros de Azure Cosmos DB para la categoría **DataPlaneRequests** se almacenan en el contenedor **insights-logs-data-plane-requests** en la cuenta de almacenamiento que proporcionó. 

En primer lugar, cree una variable para el nombre del contenedor. La variable se usa en todo el tutorial.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Para mostrar una lista de todos los blobs de este contenedor, escriba:

```powershell
Get-AzureStorageBlob -Container $container -Context $sa.Context
```

La salida del comando debe ser similar a la del siguiente ejemplo:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 10510193
ContentType       : application/octet-stream
LastModified      : 9/28/2017 7:49:04 PM +00:00
SnapshotTime      :
ContinuationToken:
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.`
                    LazyAzureStorageContext
Name              : resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS`
/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/CONTOSOCOSMOSDB/y=2017/m=09/d=28/h=19/m=00/PT1H.json
```

Como puede ver en esta salida, los blobs siguen una convención de nomenclatura: `resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Los valores de fecha y hora usan UTC.

Puesto que la misma cuenta de almacenamiento puede usarse para recopilar registros de varios recursos, puede usar el identificador de recurso completo en el nombre del blob para acceder o descargar solo los blobs específicos que necesita. Antes de hacerlo, primero vamos a explicar cómo descargar todos los blobs.

En primer lugar, cree una carpeta para descargar los blobs. Por ejemplo: 

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Después, obtenga una lista de todos los blobs:  

```powershell
$blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context
```

Canalice esta lista mediante el comando `Get-AzureStorageBlobContent` para descargar los blobs en la carpeta de destino:

```powershell
$blobs | Get-AzureStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

Al ejecutar este segundo comando, el delimitador **/** de los nombres de los blobs crea una estructura de carpeta completa en la carpeta de destino. Esta estructura de carpetas se usa para descargar y almacenar los blobs como archivos.

Para descargar blobs de forma selectiva, utilice caracteres comodín. Por ejemplo: 

* Si tiene varias bases de datos y desea descargar los registros solo para una base de datos, denominada **CONTOSOCOSMOSDB3**, utilice el comando:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Si tiene varios grupos de recursos y desea descargar los registros solo de un grupo de recursos específico, use el comando `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Si desea descargar todos los registros del mes de julio de 2017, use el comando `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

También puede ejecutar los comandos siguientes:

* Para consultar el estado de la configuración del diagnóstico del recurso de la base de datos, use el comando `Get-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`.
* Para deshabilitar el registro de la categoría **DataPlaneRequests** para el recurso de la cuenta de base de datos, use el comando `Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`.


Los blobs que se devuelven en cada una de estas consultas se almacenan como texto, con el formato de blob JSON, tal como se muestra en el código siguiente:

```json
{
    "records":
    [
        {
           "time": "Fri, 23 Jun 2017 19:29:50.266 GMT",
           "resourceId": "contosocosmosdb",
           "category": "DataPlaneRequests",
           "operationName": "Query",
           "resourceType": "Database",
           "properties": {"activityId": "05fcf607-6f64-48fe-81a5-f13ac13dd1eb",`
           "userAgent": "documentdb-dotnet-sdk/1.12.0 Host/64-bit MicrosoftWindowsNT/6.2.9200.0 AzureSearchIndexer/1.0.0",`
           "resourceType": "Database","statusCode": "200","documentResourceId": "",`
           "clientIpAddress": "13.92.241.0","requestCharge": "2.260","collectionRid": "",`
           "duration": "9250","requestLength": "72","responseLength": "209", "resourceTokenUserRid": ""}
        }
    ]
}
```

Para obtener información acerca de los datos de cada blob JSON, consulte [Interpretación de los registros de Azure Cosmos DB](#interpret).

## <a name="manage-your-logs"></a>Administración de los registros

Los registros de diagnóstico se ponen a disposición de la cuenta a las dos horas a partir del momento en que se realiza la operación de Azure Cosmos DB. Es decisión suya administrar los registros en la cuenta de almacenamiento:

* Utilice los métodos de control de acceso de Azure estándar para proteger los registros y restringir quién puede acceder a ellos.
* Elimine los registros que ya no desee mantener en la cuenta de almacenamiento.
* El período de retención para las solicitudes de plano de datos archivadas en una cuenta de almacenamiento está configurado en el portal cuando hay seleccionada la opción **Log DataPlaneRequests** (Registrar DataPlaneRequests). Para cambiar esa configuración, consulte [Activar el registro en Azure Portal](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-log-analytics"></a>Visualización de los registros de Log Analytics

Si ha seleccionado la opción **Enviar a Log Analytics** al activar el registro de diagnóstico, los datos correspondientes de la colección se reenviarán a Log Analytics en dos horas. Cuando se observa Log Analytics inmediatamente después de activar el registro, no verá ningún dato. Espere dos horas y vuelva a intentarlo. 

Antes de ver los registros, compruebe si el área de trabajo de Log Analytics para utilizar su nuevo lenguaje de consulta. Para comprobarlo, abra [Azure Portal](https://portal.azure.com), seleccione **Log Analytics** en el extremo izquierdo y, después, seleccione el nombre del área de trabajo como se muestra en la siguiente imagen. Se muestra la página **Área de trabajo de OMS**:

![Log Analytics en Azure Portal](./media/logging/azure-portal.png)

Si ve el mensaje siguiente en la página **Área de trabajo de OMS**, el área de trabajo no se ha actualizado para utilizar el nuevo lenguaje. Para más información acerca de la actualización del nuevo lenguaje de consulta, consulte [Actualización del área de trabajo de Azure Log Analytics en la nueva búsqueda de registros](../log-analytics/log-analytics-log-search-upgrade.md). 

![Mensaje de actualización de Log Analytics](./media/logging/upgrade-notification.png)

Para ver los datos de diagnóstico en Log Analytics, abra la página **Búsqueda de registros** desde el menú de la izquierda o el área **Administración** de la página, como se muestra en la siguiente imagen:

![Opciones de búsqueda de registros en Azure Portal](./media/logging/log-analytics-open-log-search.png)

Ahora que ha habilitado la recopilación de datos, ejecute el siguiente ejemplo de búsqueda de registros, utilizando el nuevo lenguaje de consulta, para ver los diez registros más recientes `AzureDiagnostics | take 10`.

![Búsqueda de registros de ejemplo para los 10 registros más recientes](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>Consultas

Estas son algunas consultas adicionales que puede escribir en el cuadro **Búsqueda de registros** para ayudarle a supervisar los contenedores de Azure Cosmos DB. Estas consultas funcionan con el [nuevo lenguaje](../log-analytics/log-analytics-log-search-upgrade.md). 

Para obtener información acerca del significado de los datos devueltos por cada búsqueda de registros, consulte [Interpretación de los registros de Azure Cosmos DB](#interpret).

* Para consultar todos los registros de diagnóstico de Azure Cosmos DB durante el período de tiempo especificado:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Para consultar los últimos 10 eventos registrados:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* Para consultar todas las operaciones, agrupadas por tipo de operación:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* Para consultar todas las operaciones, agrupadas por tipo de **recurso**:

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Para consultar toda la actividad de los usuarios, agrupada por recurso:

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```
    > [!NOTE]
    > Este comando es para un registro de actividad, no para un registro de diagnóstico.

* Para consultar qué operaciones tardan más de tres milisegundos:

    ```
    AzureDiagnostics | where toint(duration_s) > 30000 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Para consultar qué agente está ejecutando las operaciones:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* Para consultar cuándo se realizaron las operaciones de larga duración:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , toint(duration_s)/1000 | render timechart
    ```

Para más información sobre cómo utilizar el nuevo lenguaje de búsqueda de registros, consulte [Descripción de las búsquedas de registros en Log Analytics](../log-analytics/log-analytics-log-search-new.md). 

## <a id="interpret"></a>Interpretar los registros

Los datos de diagnóstico almacenados en Azure Storage y Log Analytics usan un esquema similar. 

En la tabla siguiente se describe el contenido de cada entrada del registro.

| Propiedad o campo de Azure Storage | Propiedad de Log Analytics | DESCRIPCIÓN |
| --- | --- | --- |
| **time** | **TimeGenerated** | La fecha y hora (UTC) cuando se produjo la operación. |
| **resourceId** | **Recurso** | La cuenta de Azure Cosmos DB para la cual los registros están habilitados.|
| **category** | **Categoría** | Para los registros de Azure Cosmos DB, **DataPlaneRequests** es el único valor disponible. |
| **operationName** | **OperationName** | Nombre de la operación. Este valor puede ser cualquiera de las siguientes operaciones: Create, Update, Read, ReadFeed, Delete, Replace, Execute, SqlQuery, Query, JSQuery, Head, HeadFeed o Upsert.   |
| **properties** | N/D | El contenido de este campo se describe en las filas siguientes. |
| **activityId** | **activityId_g** | GUID único para la operación registrada. |
| **userAgent** | **userAgent_s** | Una cadena que especifica el agente de usuario de cliente que realiza la solicitud. El formato es {nombre de agente de usuario}/{versión}.|
| **resourceType** | **ResourceType** | Tipo de recurso al que se accede. Este valor puede ser cualquiera de los siguientes tipos de recursos: Database, Collection, Document, Attachment, User, Permission, StoredProcedure, Trigger, UserDefinedFunction u Offer. |
| **statusCode** | **statusCode_s** | Estado de respuesta de la operación. |
| **requestResourceId** | **ResourceId** | El valor resourceId que pertenece a la solicitud. El valor puede apuntar a databaseRid, collectionRid o documentRid, según la operación realizada.|
| **clientIpAddress** | **clientIpAddress_s** | Dirección IP del cliente. |
| **requestCharge** | **requestCharge_s** | El número de RU que se utiliza en la operación |
| **collectionRid** | **collectionId_s** | Identificador único de la colección.|
| **duration** | **duration_s** | Duración de la operación, en tics. |
| **requestLength** | **requestLength_s** | Longitud de la solicitud, en bytes. |
| **responseLength** | **responseLength_s** | Longitud de la respuesta, en bytes.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Este valor no está vacío cuando se usan [tokens de recursos](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) para la autenticación. Los puntos de valor para el identificador de recurso del usuario. |

## <a name="next-steps"></a>Pasos siguientes

- Para comprender cómo habilitar el registro y también las categorías de métricas y registro que admiten los distintos servicios de Azure, lea los artículos [Información general sobre las métricas en Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) e [Información general sobre los registros de diagnóstico de Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).
- Lea estos artículos para obtener información sobre Event Hubs:
   - [¿Qué es Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Introducción a Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Consulte [Descarga de métricas y registros de diagnóstico de Azure Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
- Consulte [Descripción de las búsquedas de registros en Log Analytics](../log-analytics/log-analytics-log-search-new.md).
