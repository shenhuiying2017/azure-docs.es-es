---
title: "Registro de diagnóstico de Azure Cosmos DB | Microsoft Docs"
description: Use este tutorial para obtener ayuda para empezar a trabajar con el registro de Azure Cosmos DB.
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
tags: azure-resource-manager
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mimig
ms.openlocfilehash: 34c46fb282ad154225f5ee8ef544bc8da1c50016
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-diagnostic-logging"></a>Registro de diagnóstico de Azure Cosmos DB

Una vez que haya comenzado a usar una o más bases de datos de Azure Cosmos DB, puede que quiera supervisar cómo y cuándo se accede a las bases de datos. El registro de diagnóstico en Azure Cosmos DB le permite llevar a cabo esta supervisión. Al habilitar el registro de diagnóstico, puede enviar registros a [Azure Storage](https://azure.microsoft.com/services/storage/), transmitirlos a [Azure Event Hubs](https://azure.microsoft.com/en-us/services/event-hubs/) o exportarlos a un área de trabajo de [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite) a través de [Log Analytics](https://azure.microsoft.com/services/log-analytics/).

![Registro de diagnóstico a Storage, Event Hubs u Operations Management Suite a través de Log Analytics](./media/logging/azure-cosmos-db-logging-overview.png)

Use este tutorial para empezar a trabajar con los registros de Azure Cosmos DB a través de Azure Portal, CLI o PowerShell.

## <a name="what-is-logged"></a>¿Qué se registra?

* Se registran todas las solicitudes de API de REST DocumentDB (SQL) autenticadas, incluidas las solicitudes con error debido a permisos de acceso, errores del sistema o solicitudes incorrectas. Actualmente, no está disponible la compatibilidad con las API MongoDB, Graph y Table.
* Operaciones en la base de datos misma, que incluye operaciones CRUD en todos los documentos, contenedores y bases de datos.
* Operaciones en claves de cuenta, que incluye la creación, modificación o eliminación de estas claves.
* Solicitudes no autenticadas que dan como resultado una respuesta 401. Por ejemplo, las solicitudes que no tienen un token de portador, cuyo formato es incorrecto o está caducado o tienen un token no válido.

## <a name="prerequisites"></a>Requisitos previos
Para realizar este tutorial, necesitará los recursos siguientes:

* Una cuenta existente de Azure Cosmos DB, una base de datos y un contenedor. Para obtener instrucciones sobre cómo crear estos recursos, consulte [Creación de una cuenta de base de datos mediante Azure Portal](create-documentdb-dotnet.md#create-a-database-account), [Ejemplos de la CLI](cli-samples.md) o [Ejemplos de PowerShell](powershell-samples.md).


## <a name="turn-on-logging-in-the-azure-portal"></a>Activar el registro en Azure Portal

1. En [Azure Portal](https://portal.azure.com), en la cuenta de Azure Cosmos DB, haga clic en **Registros de diagnóstico** en el panel de navegación izquierdo y luego en **Activar diagnósticos**.

    ![Activar el registro de diagnóstico para Azure Cosmos DB en Azure Portal](./media/logging/turn-on-portal-logging.png)

2. En la página **Configuración de diagnóstico**, haga lo siguiente: 

    * **Nombre**. Escriba un nombre para los registros que quiere crear.

    * **Archivar en una cuenta de almacenamiento**. Para usar esta opción, necesita una cuenta de almacenamiento existente a la cual conectarse. Para crear una nueva cuenta de almacenamiento en el portal, consulte [Crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md) y siga las instrucciones para crear una cuenta de uso general de Resource Manager. Luego, vuelva a esta página en el portal para seleccionar la cuenta de almacenamiento. Las cuentas de almacenamiento recién creadas pueden tardar unos minutos en aparecer en el menú desplegable.
    * **Transmitir a un centro de eventos**. Para usar esta opción, necesita un espacio de nombres de Event Hubs y un centro de eventos al que conectarse. Para crear un espacio de nombres de Event Hubs, consulte [Creación de un espacio de nombres de Event Hubs y un centro de eventos con Azure Portal](../event-hubs/event-hubs-create.md). Luego, vuelva a esta página en el portal para seleccionar el espacio de nombres de Event Hubs y el nombre de la directiva.
    * **Enviar a Log Analytics**. Para usar esta opción, use una de las áreas de trabajo existentes o cree una nueva área de trabajo de Operations Management Suite siguiendo las indicaciones en el portal.
    * **Registrar DataPlaneRequests**. Si va a archivar en una cuenta de almacenamiento, puede seleccionar el período de retención para los registros de diagnóstico. Para ello, seleccione **DataPlaneRequests** y elija el número de días que conservará los registros. Los registros se eliminan automáticamente una vez expira el período de retención. 

3. Haga clic en **Guardar**.

    Puede volver a esta página en cualquier momento para modificar la configuración del registro de diagnóstico para su cuenta.

## <a name="turn-on-logging-using-cli"></a>Activar el registro con CLI

Para habilitar las métricas y los registros de diagnóstico con CLI de Azure, use los siguientes comandos:

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

## <a name="turn-on-logging-using-powershell"></a>Activar el registro con PowerShell

Para activar el registro con PowerShell, necesita Azure PowerShell, con la versión 1.0.1 como mínimo.

Para instalar Azure PowerShell y asociarlo con una suscripción de Azure, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).

Si ya instaló Azure PowerShell y no sabe la versión, en la consola de PowerShell, escriba `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Conexión a las suscripciones
Inicie una sesión de PowerShell de Azure e inicie sesión en su cuenta de Azure con el siguiente comando:  

```powershell
Login-AzureRmAccount
```

En la ventana emergente del explorador, escriba el nombre de usuario y la contraseña de su cuenta de Azure. Azure PowerShell obtiene todas las suscripciones asociadas a esta cuenta y, de forma predeterminada, usa la primera.

Si tiene varias suscripciones, es posible que deba especificar la que se usó para crear su instancia de Azure Key Vault. Escriba lo siguiente para ver las suscripciones de su cuenta:

```powershell
Get-AzureRmSubscription
```

A continuación, para especificar la suscripción asociada a la cuenta de Azure Cosmos DB que registrará, escriba:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Si tiene varias suscripciones asociadas a su cuenta, es importante especificar la suscripción.
>
>

Para obtener más información sobre cómo configurar PowerShell de Azure, consulte [Instalación y configuración de PowerShell de Azure](/powershell/azure/overview).

### <a id="storage"></a>Creación de una cuenta de almacenamiento nueva para los registros
Aunque puede usar una cuenta de almacenamiento existente para sus registros, en este tutorial crearemos una nueva cuenta de almacenamiento exclusiva para los registros de Azure Cosmos DB. Para mayor comodidad, almacenamos los detalles de la cuenta de almacenamiento en una variable denominada **sa**.

A fin de facilitar la administración, en este tutorial usamos el mismo grupo de recursos que el que contiene nuestra base de datos de Azure Cosmos DB. Sustituya los valores de ContosoResourceGroup, contosocosmosdblogs y “North Central US” por sus propios valores, según corresponda:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Si decide usar una cuenta de almacenamiento existente, esta debe usar la misma suscripción que su suscripción a Azure Cosmos DB y debe usar el modelo de implementación de Resource Manager, en lugar del modelo de implementación Clásico.
>
>

### <a id="identify"></a>Identificar la cuenta de Azure Cosmos DB para sus registros
Establezca el nombre de la cuenta de Azure Cosmos DB en una variable denominada **account**, donde ResourceName es el nombre de la cuenta de Azure Cosmos DB.

```powershell
$account = Get-AzureRmResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Habilitación del registro
Para habilitar el registro para Azure Cosmos DB, use el cmdlet Set-AzureRmDiagnosticSetting, junto con las variables para la nueva cuenta de almacenamiento, la cuenta de Azure Cosmos DB y la categoría para la que desea habilitar los registros. Ejecute el siguiente comando y establezca la marca **-Enabled** en **$true**:

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

La salida del comando debe ser similar a la siguiente:

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

Esto confirma que el registro está habilitado ahora para la base de datos y guarda la información en su cuenta de almacenamiento.

Opcionalmente también se puede establecer una directiva de retención para los registros, de forma que los registros más antiguos se eliminen automáticamente. Por ejemplo, establezca la directiva de retención mediante la marca **-RetentionEnabled** en **$true** y establezca el parámetro **-RetentionInDays** en **90**, con el fin de que los registros que tengan más de 90 días se eliminen automáticamente.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Acceso a los registros
Los registros de Azure Cosmos DB para la categoría **DataPlaneRequests** se almacenan en el contenedor **insights-logs-data-plane-requests** en la cuenta de almacenamiento que proporcionó. 

En primer lugar, cree una variable para el nombre del contenedor. Se usará en el resto del tutorial.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Para mostrar una lista de todos los blobs de este contenedor, escriba:

```powershell
Get-AzureStorageBlob -Container $container -Context $sa.Context
```

El resultado será similar al siguiente.

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

Puesto que la misma cuenta de almacenamiento puede usarse para recopilar registros de varios recursos, el identificador de recurso completo en el nombre del blob es muy útil para acceder o descargar solo los blobs que necesita. Pero antes de hacerlo, primero explicaremos cómo descargar todos los blobs.

En primer lugar, cree una carpeta para descargar los blobs. Por ejemplo:

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

A continuación, obtenga una lista de todos los blobs:  

```powershell
$blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context
```

Canalice esta lista mediante 'Get-AzureStorageBlobContent' para descargar los blobs en la carpeta de destino:

```powershell
$blobs | Get-AzureStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

Al ejecutar este segundo comando, el delimitador **/** de los nombres de los blobs crea una estructura de carpeta completa en la carpeta de destino. Esta estructura de carpetas se usa para descargar y almacenar los blobs como archivos.

Para descargar blobs de forma selectiva, utilice caracteres comodín. Por ejemplo:

* Si tiene varias bases de datos y desea descargar los registros solo para una base de datos, denominada CONTOSOCOSMOSDB3:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Si tiene varios grupos de recursos y desea descargar los registros solo de un grupo de recursos específico, use `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Si desea descargar todos los registros del mes de julio de 2017, use `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

Ahora está listo para comenzar a ver lo que está en los registros. Pero antes de pasar a eso, hay otros dos parámetros de `Get-AzureRmDiagnosticSetting` que puede ser necesario conocer:

* Para consultar el estado de la configuración del diagnóstico del recurso de la base de datos: `Get-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
* Para deshabilitar el registro de la categoría **DataPlaneRequests** para el recurso de la cuenta de base de datos: `Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`

## <a id="interpret"></a>Interpretación de los registros de Azure Cosmos DB
Los blobs individuales se almacenan como texto, con formato de blob JSON. Este JSON es una entrada de registro de ejemplo:

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


En la tabla siguiente se muestran los nombres y las descripciones de los campos.

| Nombre del campo | Description |
| --- | --- |
| Twitter en tiempo |La fecha y hora (UTC) cuando se produjo la operación. |
| resourceId |La cuenta de Azure Cosmos DB para la cual los registros están habilitados.|
| categoría |Para los registros de Azure Cosmos DB, DataPlaneRequests es el único valor disponible. |
| operationName |Nombre de la operación. Este valor puede ser cualquiera de las siguientes operaciones: Create, Update, Read, ReadFeed, Delete, Replace, Execute, SqlQuery, Query, JSQuery, Head, HeadFeed o Upsert.   |
| propiedades |El contenido de este campo se describe en la tabla siguiente. |

En la tabla siguiente se enumeran los campos que se registran en el campo de propiedades.

| Nombre del campo de propiedad | Descripción |
| --- | --- |
| activityId | GUID único para la operación registrada. |
| userAgent |Una cadena que especifica el agente de usuario de cliente que realiza la solicitud. El formato es {nombre de agente de usuario}/{versión}.|
| resourceType | Tipo de recurso al que se accede. Este valor puede ser cualquiera de los siguientes tipos de recursos: Database, Collection, Document, Attachment, User, Permission, StoredProcedure, Trigger, UserDefinedFunction u Offer. |
| statusCode |Estado de respuesta de la operación. |
| requestResourceId | El valor resourceId perteneciente a la solicitud, puede apuntar a databaseRid, collectionRid o documentRid según la operación realizada.|
| clientIpAddress |Dirección IP del cliente. |
| requestCharge | Número de RU utilizado por la operación |
| collectionRid | Identificador único de la colección.|
| duration | Duración de la operación, en tics. |
| requestLength |Longitud de la solicitud, en bytes. |
| responseLength | Longitud de la respuesta, en bytes.|
| resourceTokenUserRid | No está vacío cuando se usan [tokens de recursos](https://docs.microsoft.com/en-us/azure/cosmos-db/secure-access-to-data#resource-tokens) para la autenticación, y apunta al id. de recurso del usuario. |

## <a name="managing-your-logs"></a>Administración de los registros

Los registros se ponen a disposición de la cuenta a las dos horas a partir del momento en que se realiza la operación de Azure Cosmos DB. Es decisión suya administrar los registros en la cuenta de almacenamiento:

* Utilice los métodos de control de acceso de Azure estándar para proteger los registros mediante la restricción de quién puede tener acceso a ellos.
* Elimine los registros que ya no desee mantener en la cuenta de almacenamiento.
* El período de retención para las solicitudes de plano de datos archivadas en una cuenta de almacenamiento está configurado en el portal cuando hay seleccionada la opción **Log DataPlaneRequests** (Registrar DataPlaneRequests). Para cambiar esa configuración, consulte [Activar el registro en Azure Portal](#turn-on-logging-in-the-azure-portal).

## <a name="next-steps"></a>Pasos siguientes

- Para comprender no solo cómo habilitar los registros, sino también las categorías de métricas y registro que admiten los distintos servicios de Azure, lea los artículos [Información general sobre las métricas en Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) e [Información general sobre los registros de diagnóstico de Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).
- Lea estos artículos para obtener información sobre Event Hubs:
   - [¿Qué es Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Introducción a Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Vea [Download metrics and diagnostic logs from Azure Storage](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs) (Descargar métricas y registros de diagnóstico de Azure Storage).
