---
title: "Archivo de registros de diagnóstico de Azure | Microsoft Docs"
description: "Aprenda a archivar los registros de diagnóstico de Azure para su retención a largo plazo en una cuenta de almacenamiento."
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 3a55c73f-2ef3-45f3-8956-bcf9c0cb7e05
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2016
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: 3e82377c36da1563931bc0301daa4534e6f82377
ms.openlocfilehash: e3413236445f3f6034a228dc93624d3fdc9b87ad


---
# <a name="archive-azure-diagnostic-logs"></a>Archivo de registros de diagnóstico de Azure
En este artículo, le mostraremos cómo puede usar el Portal de Azure, los cmdlets de PowerShell, la CLI o la API de REST para archivar los [registros de diagnóstico de Azure](monitoring-overview-of-diagnostic-logs.md) en una cuenta de almacenamiento. Esta opción es útil si desea conservar los registros de diagnóstico con una directiva de retención opcional para auditorías, análisis estáticos o copias de seguridad. La cuenta de almacenamiento no tiene que estar en la misma suscripción que la del recurso que emite los registros, siempre que el usuario que configura la configuración tenga acceso RBAC adecuado a ambas suscripciones.

## <a name="prerequisites"></a>Requisitos previos
Antes de comenzar, necesita [crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account) en la que poder archivar los registros de diagnóstico. Le recomendamos encarecidamente que no utilice una cuenta de almacenamiento existente que tenga otros datos sin supervisión almacenados en ella, para que pueda controlar mejor el acceso a los datos de supervisión. Sin embargo, si también va a archivar las métricas del registro de actividades y de diagnóstico en una cuenta de almacenamiento, puede que tenga sentido utilizar igualmente esa cuenta de almacenamiento para los registros de diagnóstico a fin de mantener todos los datos de supervisión en una ubicación central. La cuenta de almacenamiento que use debe ser una cuenta de almacenamiento de propósito general no una cuenta de almacenamiento de blobs.

## <a name="diagnostic-settings"></a>Configuración de diagnóstico
Para archivar los registros de diagnóstico mediante cualquiera de los métodos siguientes, establezca una **configuración de diagnóstico** para un recurso determinado. Una configuración de diagnóstico para un recurso define las categorías de registros que se almacenan o transmiten y las salidas: cuenta de almacenamiento y/o Centro de eventos. También define la directiva de retención (el número de días que deben conservarse) para los eventos almacenados de cada categoría de registro en una cuenta de almacenamiento. Si se establece en cero una directiva de retención, los eventos para esa categoría de registro se almacenan indefinidamente. De lo contrario, una directiva de retención puede ser cualquier número de días comprendido entre 1 y 2147483647. [Puede leer más acerca de estas opciones de diagnóstico aquí](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings). Las directivas de retención se aplican a diario, por lo que al final de un día (UTC) se eliminan los registros del día que quede fuera de la directiva de retención. Por ejemplo, si tuviera una directiva de retención de un día, se eliminarían los registros de anteayer al principio del día de hoy.

## <a name="archive-diagnostic-logs-using-the-portal"></a>Archivo de registros de diagnóstico mediante el portal
1. En el portal, haga clic en la hoja de recursos para el recurso en el que desea habilitar el archivo de registros de diagnóstico.
2. En la sección **Supervisión** del menú de configuración de recursos, seleccione **Diagnósticos**.
   
    ![Sección de supervisión del menú de recursos](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-sec.png)
3. Active la casilla para **Exportar a cuenta de almacenamiento**y seleccione una cuenta de almacenamiento. Además, puede establecer un número de días para conservar estos registros mediante los controles deslizantes de **Retención (días)** . Con una retención de cero días, los registros se almacenan indefinidamente.
   
    ![Hoja de registros de diagnóstico](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-blade.png)
4. Haga clic en **Save**.

Los registros de diagnóstico se archivan en esa cuenta de almacenamiento en cuanto se generan nuevos datos de evento.

## <a name="archive-diagnostic-logs-via-the-powershell-cmdlets"></a>Archivo de registros de diagnóstico mediante cmdlets de PowerShell
```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg -StorageAccountId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Categories networksecuritygroupevent,networksecuritygrouprulecounter -Enabled $true -RetentionEnabled $true -RetentionInDays 90
```

| Propiedad | Obligatorio | Description |
| --- | --- | --- |
| ResourceId |Sí |Identificador de recurso en el que desea establecer una configuración de diagnóstico. |
| StorageAccountId |No |Identificador de recurso de la cuenta de almacenamiento en la que se deben guardar los registros de diagnóstico. |
| Categorías |No |Lista de categorías de registro separadas por comas, que se van a habilitar. |
| Enabled |Sí |Valor booleano que indica si los diagnósticos están habilitados o deshabilitados en este recurso. |
| RetentionEnabled |No |Valor booleano que indica si está habilitada una directiva de retención en este recurso. |
| RetentionInDays |No |Número de días que deben retenerse los eventos, entre 1 y 2147483647. Con el valor cero, se almacenan los registros indefinidamente. |

## <a name="archive-diagnostic-logs-via-the-cross-platform-cli"></a>Archivo de registros de diagnóstico a través de la CLI multiplataforma
```
azure insights diagnostic set --resourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg --storageId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage –categories networksecuritygroupevent,networksecuritygrouprulecounter --enabled true
```

| Propiedad | Obligatorio | Description |
| --- | --- | --- |
| ResourceId |Sí |Identificador de recurso en el que desea establecer una configuración de diagnóstico. |
| storageId |No |Identificador de recurso de la cuenta de almacenamiento en la que se deben guardar los registros de diagnóstico. |
| Categorías |No |Lista de categorías de registro separadas por comas, que se van a habilitar. |
| Enabled |Sí |Valor booleano que indica si los diagnósticos están habilitados o deshabilitados en este recurso. |

## <a name="archive-diagnostic-logs-via-the-rest-api"></a>Archivo de registros de diagnóstico mediante la API de REST
[Consulte este documento](https://msdn.microsoft.com/library/azure/dn931931.aspx) para obtener información sobre cómo definir una configuración de diagnóstico mediante la API de REST de Azure Monitor.

## <a name="schema-of-diagnostic-logs-in-the-storage-account"></a>Esquema de registros de diagnóstico en la cuenta de almacenamiento
Una vez que haya configurado el archivo, se crea un contenedor de almacenamiento en la cuenta de almacenamiento en cuanto se produzca un evento en una de las categorías de registro que haya habilitado. Los blobs dentro del contenedor siguen el mismo formato en los registros de diagnóstico y en el registro de actividades. La estructura de estos blobs es:

> insights-logs-{nombre de categoría de registro}/resourceId=/SUBSCRIPTIONS/{id. de suscripción}/RESOURCEGROUPS/{nombre de grupo de recursos}/PROVIDERS/{nombre de proveedor de recursos}/{tipo de recurso}/{nombre de recurso}/y={año con cuatro dígitos}/m={mes con dos dígitos}/d={día con dos dígitos}/h={hora en formato de 24 horas con dos dígitos}/m=00/PT1H.json
> 
> 

O, sencillamente,

> insights-logs-{nombre de categoría de registro}/resourceId=/{id. de recurso}/y={año con cuatro dígitos}/m={mes con dos dígitos}/d={día con dos dígitos}/h={hora en formato de 24 horas con dos dígitos}/m=00/PT1H.json
> 
> 

Por ejemplo, un nombre de blob podría ser:

> insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
> 
> 

Cada blob PT1H.json contiene un blob JSON de eventos que se produjeron en la hora especificada en la dirección URL del blob (por ejemplo h=12). Durante la hora en cuestión, los eventos se anexan al archivo PT1H.json a medida que se producen. El valor de los minutos (m=00) siempre es 00, ya que los eventos de los registros de diagnóstico se dividen en blobs individuales por hora.

En el archivo PT1H.json, cada evento se almacena en la matriz de "registros" con este formato:

```
{
    "records": [
        {
            "time": "2016-07-01T00:00:37.2040000Z",
            "systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1",
            "category": "NetworkSecurityGroupRuleCounter",
            "resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG",
            "operationName": "NetworkSecurityGroupCounters",
            "properties": {
                "vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}",
                "subnetPrefix": "10.3.0.0/24",
                "macAddress": "000123456789",
                "ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp",
                "direction": "In",
                "type": "allow",
                "matchedConnections": 1988
            }
        }
    ]
}
```

| Nombre del elemento | Description |
| --- | --- |
| Twitter en tiempo |Marca de tiempo de cuándo el servicio de Azure generó el evento que procesó la solicitud correspondiente al evento. |
| ResourceId |Identificador de recurso del recurso afectado. |
| operationName |Nombre de la operación. |
| categoría |Categoría de registro del evento. |
| propiedades |Conjunto de pares `<Key, Value>` (es decir, diccionario) que describe los detalles del evento. |

> [!NOTE]
> Las propiedades y el uso de estas propiedades pueden variar según el recurso.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
* [Descargar blobs para el análisis](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)
* [Transmisión de registros de diagnóstico de Azure a Centros de eventos](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Información general sobre los registros de diagnóstico de Azure](monitoring-overview-of-diagnostic-logs.md)




<!--HONumber=Dec16_HO2-->


