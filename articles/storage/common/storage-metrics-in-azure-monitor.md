---
title: "Métricas de Azure Storage en Azure Monitor | Microsoft Docs"
description: "Obtenga información acerca de las nuevas métricas que ofrece Azure Monitor."
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: 
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 09/05/2017
ms.author: fryu
ms.openlocfilehash: d30a99044e335723e5d2c4bbd71fab7e4fd51145
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-storage-metrics-in-azure-monitor-preview"></a>Métricas de Azure Storage en Azure Monitor (versión preliminar)

Con las métricas de Azure Storage puede analizar tendencias de uso, hacer un seguimiento de las solicitudes y diagnosticar los problemas de su cuenta de almacenamiento.

Azure Monitor proporciona interfaces de usuario unificadas para la supervisión de distintos servicios de Azure. Para más información, consulte [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md). Azure Storage integra Azure Monitor mediante el envío de datos de métricas a la plataforma de Azure Monitor.

## <a name="access-metrics"></a>Acceso a la métrica

Azure Monitor proporciona varias maneras de tener acceso a las métricas. Se puede acceder a ellas desde [Azure Portal](https://portal.azure.com), las API de Azure Monitor (REST y .NET) y soluciones de análisis como Operation Management Suite y Event Hub. Para más información, consulte [Información general sobre las métricas en Microsoft Azure](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

De forma predeterminada, las métricas están habilitadas y se puede acceder a datos de los últimos 30 días. Si es necesario conservar los datos durante un periodo mayor, se pueden archivar en una cuenta de Azure Storage. Esto se configura en la [configuración de diagnóstico](../../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) de Azure Monitor.

### <a name="access-metrics-in-the-azure-portal"></a>Métricas de Azure en Azure Portal

Una vez transcurrido un tiempo, las métricas se pueden supervisar en Azure Portal. A continuación puede ver un ejemplo en el que se muestra cómo ver **UsedCapacity** a nivel de cuenta.

![captura de pantalla de acceso a métricas desde Azure Portal](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

Para ver métricas que admitan las dimensiones, debe filtrar por el valor de la dimensión que desee. A continuación puede ver un ejemplo en el que se muestra cómo ver **transacciones** a nivel de cuenta con el tipo de respuesta **Correcto**.

![captura de pantalla de acceso a métricas con dimensión desde Azure Portal](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Acceso a métricas con la API de REST

Azure Monitor proporciona [API de REST](/rest/api/monitor/) para leer la definición y los valores de las métricas. En esta sección se muestra cómo leer las métricas de almacenamiento. El identificador de recurso se usa en todas las API de REST. Para más información, lea [Descripción de identificador de recurso para los servicios de Storage](#understanding-resource-id-for-services-in-storage).

En el ejemplo siguiente se muestra cómo usar [ArmClient](https://github.com/projectkudu/ARMClient) en la línea de comandos para simplificar la realización de pruebas con la API de REST.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Lista de definición de métricas a nivel de cuenta con la API de REST

En el ejemplo siguiente se muestra cómo enumerar la definición de métricas a nivel de cuenta:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2017-05-01-preview

```

Si desea enumerar las definiciones de métricas de un blob, una tabla, un archivo o una cola, debe especificar identificadores de recursos diferentes para cada servicio con la API.

La respuesta contiene la definición de métricas en formato JSON:

```Json
{
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions/UsedCapacity",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
      "category": "Capacity",
      "name": {
        "value": "UsedCapacity",
        "localizedValue": "Used capacity"
      },
      "isDimensionRequired": false,
      "unit": "Bytes",
      "primaryAggregationType": "Average",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D"
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D"
        }
      ]
    },
    ... next metric definition
  ]
}

```

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Lectura de valores de métricas a nivel de cuenta con la API de REST

En el ejemplo siguiente se muestra cómo leer los datos de métricas a nivel de cuenta:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metric=Availability&api-version=2017-05-01-preview&aggregation=Average&interval=PT1H"

```

En el ejemplo anterior, si desea leer los valores de métricas de un blob, una tabla, un archivo o una cola, debe especificar identificadores de recursos diferente para cada servicio con la API.

La respuesta siguiente contiene valores de métricas en formato JSON:

```Json
{
  "cost": 0,
  "timespan": "2017-09-07T17:27:41Z/2017-09-07T18:27:41Z",
  "interval": "PT1H",
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/Microsoft.Insights/metrics/Availability",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Availability",
        "localizedValue": "Availability"
      },
      "unit": "Percent",
      "timeseries": [
        {
          "metadatavalues": [],
          "data": [
            {
              "timeStamp": "2017-09-07T17:27:00Z",
              "average": 100.0
            }
          ]
        }
      ]
    }
  ]
}

```

## <a name="billing-for-metrics"></a>Facturación de métricas

Actualmente, el uso de métricas en Azure Monitor es gratuito. Sin embargo, si usa otras soluciones que ingieren datos de métricas, puede que se le facturen dichas soluciones. Por ejemplo, se le facturará por Azure Storage si archiva datos de métricas en una cuenta de Azure Storage. O se le facturará por Operation Management Suite (OMS) si transmite en secuencias datos de métricas a OMS para realizar análisis avanzados.

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Descripción de identificador de recurso para los servicios de Azure Storage

El identificador de recurso es un identificador único de un recurso de Azure. Cuando use la API de REST de Azure Monitor para leer las definiciones o los valores de las métricas, debe usar el identificador del recurso en el que va a operar. Este es el formato de la plantilla del identificador de recurso:

`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
`

Storage proporciona métricas tanto a nivel de la cuenta de almacenamiento como al nivel de servicio con Azure Monitor. Por ejemplo, puede recuperar métricas solo de Blob Storage. Cada nivel tiene su propio identificador de recurso, que se utiliza para recuperar las métricas solo de ese nivel.

### <a name="resource-id-for-a-storage-account"></a>Identificador de recurso de una cuenta de almacenamiento

A continuación se muestra el formato para especificar el identificador de recurso de una cuenta de almacenamiento.

`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
`

### <a name="resource-id-for-the-storage-services"></a>Identificador de recurso de los servicios de almacenamiento

A continuación se muestra el formato para especificar el identificador de recurso de los servicios de almacenamiento.

* Identificador de recurso de Blob service `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
`
* Identificador de recurso de Table service `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
`
* Identificador de recurso de Queue service `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
`
* Identificador de recurso de File service `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
`

### <a name="resource-id-in-azure-monitor-rest-api"></a>Identificador de recurso en la API de REST de Azure Monitor

A continuación se muestra el patrón que se utiliza cuando se llama a la API de REST de Azure Monitor.

`
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
`

## <a name="capacity-metrics"></a>Métricas de capacidad

Los valores de las métricas de capacidad se envían a Azure Monitor cada hora. El valor se actualiza a diario. El intervalo de agregación define el intervalo de tiempo para el que se presentan los valores de las métricas. El intervalo de agregación que se admite en las métricas de capacidad es una hora (PT1H).

Azure Storage proporciona las siguientes métricas de capacidad en Azure Monitor.

### <a name="account-level"></a>Nivel de cuenta

| Nombre de métrica | Descripción |
| ------------------- | ----------------- |
| UsedCapacity | La cantidad de almacenamiento que utiliza la cuenta de almacenamiento. En las cuentas de almacenamiento estándar, es la suma de la capacidad usada por un blob, una tabla, un archivo y una cola. Tanto en las cuentas de almacenamiento Premium como en las cuentas de Blob Storage, coincide con BlobCapacity. <br/><br/> Unidad: bytes <br/> Tipo de agregación: promedio <br/> Ejemplo de valor: 1024 |

### <a name="blob-storage"></a>Almacenamiento de blobs

| Nombre de métrica | Descripción |
| ------------------- | ----------------- |
| BlobCapacity | El total de almacenamiento de blobs que se utiliza en la cuenta de almacenamiento. <br/><br/> Unidad: bytes <br/> Tipo de agregación: promedio <br/> Ejemplo de valor: 1024 <br/> Dimensión: BlobType ([definición](#metrics-dimensions)) |
| BlobCount    | El número de objetos BLOB almacenados en la cuenta de almacenamiento. <br/><br/> Unidad: recuento <br/> Tipo de agregación: promedio <br/> Ejemplo de valor: 1024 <br/> Dimensión: BlobType ([definición](#metrics-dimensions)) |
| ContainerCount    | El número de contenedores que hay en la cuenta de almacenamiento. <br/><br/> Unidad: recuento <br/> Tipo de agregación: promedio <br/> Ejemplo de valor: 1024 |

### <a name="table-storage"></a>Almacenamiento de tablas

| Nombre de métrica | Descripción |
| ------------------- | ----------------- |
| TableCapacity | La cantidad de almacenamiento de tablas que utiliza la cuenta de almacenamiento. <br/><br/> Unidad: bytes <br/> Tipo de agregación: promedio <br/> Ejemplo de valor: 1024 |
| TableCount   | El número de tablas de la cuenta de almacenamiento. <br/><br/> Unidad: recuento <br/> Tipo de agregación: promedio <br/> Ejemplo de valor: 1024 |
| TableEntityCount | El número de entidades de tabla que hay en la cuenta de almacenamiento. <br/><br/> Unidad: recuento <br/> Tipo de agregación: promedio <br/> Ejemplo de valor: 1024 |

### <a name="queue-storage"></a>Queue Storage

| Nombre de métrica | Descripción |
| ------------------- | ----------------- |
| QueueCapacity | La cantidad de almacenamiento de colas que utiliza la cuenta de almacenamiento. <br/><br/> Unidad: bytes <br/> Tipo de agregación: promedio <br/> Ejemplo de valor: 1024 |
| QueueCount   | El número de colas que hay en la cuenta de almacenamiento. <br/><br/> Unidad: recuento <br/> Tipo de agregación: promedio <br/> Ejemplo de valor: 1024 |
| QueueMessageCount | El número de mensajes de la colas no expirados que hay en la cuenta de almacenamiento. <br/><br/>Unidad: recuento <br/> Tipo de agregación: promedio <br/> Ejemplo de valor: 1024 |

### <a name="file-storage"></a>File Storage

| Nombre de métrica | Descripción |
| ------------------- | ----------------- |
| FileCapacity | La cantidad de almacenamiento de archivos que utiliza la cuenta de almacenamiento. <br/><br/> Unidad: bytes <br/> Tipo de agregación: promedio <br/> Ejemplo de valor: 1024 |
| FileCount   | El número de archivos que hay en la cuenta de almacenamiento. <br/><br/> Unidad: recuento <br/> Tipo de agregación: promedio <br/> Ejemplo de valor: 1024 |
| FileShareCount | El número de recursos compartidos de archivo que hay en la cuenta de almacenamiento. <br/><br/> Unidad: recuento <br/> Tipo de agregación: promedio <br/> Ejemplo de valor: 1024 |

## <a name="transaction-metrics"></a>Métricas de transacciones

Las métricas de transacciones se envían de Azure Storage a Azure Monitor cada minuto. Todas las métricas de transacciones están disponibles a nivel tanto de cuenta como de servicio (Blob Storage, Table Storage, Azure Files y Queue Storage). El intervalo de agregación define el intervalo de tiempo en el que se presentan los valores de las métricas. Los intervalos de agregación compatible para todas las métricas de transacciones son PT1H y PT1M.

Azure Storage proporciona las siguientes métricas de transacciones en Azure Monitor.

| Nombre de métrica | Descripción |
| ------------------- | ----------------- |
| Transacciones | El número de solicitudes realizadas a un servicio de almacenamiento o la operación de API especificada. Este número incluye solicitudes correctas y con errores, así como las solicitudes que generaron errores. <br/><br/> Unidad: recuento <br/> Tipo de agregación: total <br/> Dimensiones aplicables: ResponseType, GeoType, ApiName ([definición](#metrics-dimensions))<br/> Ejemplo de valor: 1024 |
| Ingress | La cantidad de datos de entrada. Este número incluye la entradas desde un cliente externo en Azure Storage, así como la entrada dentro de Azure. <br/><br/> Unidad: bytes <br/> Tipo de agregación: total <br/> Dimensiones aplicables: GeoType, ApiName ([definición](#metrics-dimensions)) <br/> Ejemplo de valor: 1024 |
| Egress | La cantidad de datos de salida. Este número incluye la salida de un cliente externo en Azure Storage, así como la salida dentro de Azure. En consecuencia, este número no refleja las salidas facturables. <br/><br/> Unidad: bytes <br/> Tipo de agregación: total <br/> Dimensiones aplicables: GeoType, ApiName ([definición](#metrics-dimensions)) <br/> Ejemplo de valor: 1024 |
| SuccessServerLatency | El tiempo medio que se usa para que Azure Storage procese una solicitud correcta . Este valor no incluye la latencia de red especificada en SuccessE2ELatency. <br/><br/> Unidad: milisegundos <br/> Tipo de agregación: promedio <br/> Dimensiones aplicables: GeoType, ApiName ([definición](#metrics-dimensions)) <br/> Ejemplo de valor: 1024 |
| SuccessE2ELatency | La latencia media de un extremo a otro de las solicitudes correctas realizadas a un servicio de almacenamiento o a la operación de API especificada. Este valor incluye el tiempo de procesamiento requerido dentro de Azure Storage para leer la solicitud, enviar la respuesta y recibir la confirmación de la respuesta. <br/><br/> Unidad: milisegundos <br/> Tipo de agregación: promedio <br/> Dimensiones aplicables: GeoType, ApiName ([definición](#metrics-dimensions)) <br/> Ejemplo de valor: 1024 |
| Disponibilidad | El porcentaje de disponibilidad para el servicio de almacenamiento o la operación de API especificada. Para calcular la disponibilidad hay que tomar el número total de solicitudes facturables y dividirlo por el número de solicitudes aplicables, incluidas las solicitudes que generaron errores inesperados. Todos los errores inesperados reducen la disponibilidad del servicio de almacenamiento o de la operación de API especificada. <br/><br/> Unidad: porcentaje <br/> Tipo de agregación: promedio <br/> Dimensiones aplicables: GeoType, ApiName ([definición](#metrics-dimensions)) <br/> Ejemplo de valor: 99,99 |

## <a name="metrics-dimensions"></a>Dimensiones de métricas

Azure Storage admite los siguientes dimensiones para las métricas en Azure Monitor.

| Nombre de dimensión | Descripción |
| ------------------- | ----------------- |
| BlobType | Solo el tipo de blob de las métricas de Blob. Los valores que se admiten son **BlockBlob** y **PageBlob**. Anexar Blob se incluye en BlockBlob. |
| ResponseType | Tipo de respuesta de la transacción. Los valores disponibles son: <br/><br/> <li>ServerOtherError: todos los errores del server, excepto los descritos </li> <li> ServerBusyError: solicitud autenticada que devolvió un código de estado HTTP 503 (aún no se admite). </li> <li> ServerTimeoutError: solicitud autenticada que ha superado el tiempo de espera y que devolvió un código de estado HTTP 500. El tiempo de espera se superó debido a un error del servidor. </li> <li> ThrottlingError: suma de error de limitación del lado del cliente y del lado del servidor (se quitará una vez que se admitan ServerBusyError y ClientThrottlingError) </li> <li> AuthorizationError: solicitud autenticada errónea debido a un acceso no autorizado de los datos o a un error de autorización. </li> <li> NetworkError: solicitud autenticada errónea debido a errores de red. Normalmente se produce cuando un cliente cierra prematuramente una conexión antes de que se haya superado el tiempo de expiración. </li> <li>  ClientThrottlingError: error de limitación del lado de cliente (aún no se admite) </li> <li> ClientTimeoutError: solicitud autenticada que ha superado el tiempo de espera y que devolvió un código de estado HTTP 500. Si el tiempo de expiración de la red del cliente o el tiempo de expiración de la solicitud se establece en un valor menor de lo que espera el servicio de almacenamiento, es un tiempo de expiración esperado. De lo contrario, se notifica como un error ServerTimeoutError. </li> <li> ClientOtherError: todos los errores del cliente, excepto los descritos. </li> <li> Success: solicitud correcta|
| GeoType | Transacción de clúster principal o secundario. Los valores disponibles incluyen Principal y Secundario. Se aplica al Almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS) al leer objetos de un inquilino secundario. |
| ApiName | El nombre de la operación. Por ejemplo: <br/> <li>CreateContainer</li> <li>DeleteBlob</li> <li>GetBlob</li> Para ver los nombres de todas las operaciones, consulte este [documento](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages#logged-operations.md). |

En el caso de las dimensiones compatibles con métricas, es preciso especificar el valor de la dimensión para ver los valores correspondientes de las métricas. Por ejemplo, si observa el valor **Transactions** en las respuestas correctas, es preciso filtrar la dimensión **ResponseType** por **Success**. O bien si examina el valor **BlobCount** de Blob en bloques, es preciso filtrar la dimensión **BlobType** por **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>Continuidad en el servicio de métricas heredadas

Las métricas heredades están disponibles en paralelo con las métricas de Azure Monitor. La compatibilidad sigue siendo la misma hasta que Azure Storage finaliza el servicio en las métricas heredadas. El plan final se anunciará una vez que se publiquen oficialmente las métricas administradas de Azure Monitor.

## <a name="see-also"></a>Otras referencias

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
