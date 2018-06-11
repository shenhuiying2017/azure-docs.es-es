---
title: Datos de flujo como entrada en Azure Stream Analytics
description: Obtenga información sobre cómo configurar una conexión de datos en Azure Stream Analytics. Entre las entradas se incluyen una transmisión de datos de los eventos y también datos de referencia.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: d993a29f5a7224c2346469b42309c11e55317756
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808832"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Datos de flujo como entrada en Stream Analytics

Stream Analytics presenta una integración de primera clase con flujos de datos de Azure como entradas de tres tipos de recursos:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Almacenamiento de blobs de Azure](https://azure.microsoft.com/services/storage/blobs/) 

Estos recursos de entrada pueden proceder de la misma suscripción de Azure que el trabajo de Stream Analytics o de otra suscripción.

### <a name="compression"></a>Compresión
Stream Analytics admite la compresión en todos los orígenes de entrada del flujo de datos. Los tipos de referencia compatibles actualmente son None, GZip y compresión Deflate. La compatibilidad con la compresión no está disponible para los datos de referencia. Si se comprimen los datos de entrada de Avro, se controlan de forma transparente. No es necesario especificar el tipo de compresión con la serialización de Avro. 

## <a name="create-edit-or-test-inputs"></a>Creación, edición o prueba de entradas
Puede usar [Azure Portal](https://portal.azure.com) para [crear nuevas entradas](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-quick-create-portal#configure-input-to-the-job) y para ver o editar las entradas existentes en el trabajo de streaming. También puede probar las conexiones de entrada y [probar las consultas](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-manage-job#test-your-query) con datos de ejemplo. Al escribir una consulta, se mostrará una lista de las entradas en la cláusula FROM. Puede obtener la lista de las entradas disponibles en la página **Consulta** del portal. Si desea usar varias entradas, puede utilizar `JOIN` o escribir varias consultas `SELECT`.


## <a name="stream-data-from-event-hubs"></a>Datos de flujo de Event Hubs

Azure Event Hubs ofrece consumidores de eventos de publicación y suscripción muy escalables. Un centro de eventos puede recopilar millones de eventos por segundo, por lo se pueden procesar y analizar las grandes cantidades de datos que generan las aplicaciones y los dispositivos conectados. Juntos, Event Hubs y Stream Analytics proporcionan una solución de un extremo a otro para realizar análisis en tiempo real. Event Hubs le permite suministrar eventos a Azure en tiempo real y los trabajos de Stream Analytics pueden procesarlos también en tiempo real. Por ejemplo, se pueden enviar clics de web, lecturas de sensor o eventos de registro en línea a Event Hubs. Luego se pueden crear trabajos de Stream Analytics que usen Event Hubs como los flujos de datos de entrada para su filtrado, agregación y correlación en tiempo real.

`EventEnqueuedUtcTime` es la marca de tiempo de la llegada de un evento a un centro de eventos y es la marca de tiempo predeterminada de los eventos procedentes de Event Hubs hacia Stream Analytics. Para procesar los datos como un flujo con una marca de tiempo en la carga del evento, se debe usar la palabra clave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

### <a name="consumer-groups"></a>Grupos de consumidores
Se debe configurar cada entrada de centro de eventos de Stream Analytics para que tenga su propio grupo de consumidores. Cuando un trabajo contiene una autocombinación o tiene varias entradas, es posible que algunas entradas sean leídas por más de un lector de un nivel inferior. Esta situación afecta al número de lectores de un solo grupo de consumidores. El procedimiento recomendado para evitar superar el límite de cinco lectores por grupo de consumidores por cada partición de Event Hubs consiste en designar un grupo de consumidores para cada trabajo de Stream Analytics. Además, hay un límite de 20 grupos de consumidores por centro de eventos. Para obtener más información, consulte [Troubleshoot Azure Stream Analytics with Event Hub receivers](stream-analytics-event-hub-consumer-groups.md) (Solución de problemas de Azure Stream Analytics con los receptores de Event Hubs).

### <a name="stream-data-from-event-hubs"></a>Datos de flujo de Event Hubs
En la siguiente tabla se explica cada propiedad de la página **Nueva entrada** de Azure Portal para transmitir la entrada de datos desde un centro de eventos:

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| **Alias de entrada** |Nombre descriptivo que se usará en la consulta del trabajo para hacer referencia a esta entrada. |
| **Suscripción** | Elija la suscripción en el que existe el recurso del centro de eventos. | 
| **Espacio de nombres del centro de eventos** | El espacio de nombres del centro de eventos es un contenedor para un conjunto de entidades de mensajería. Al crear un centro de eventos, también se crea el espacio de nombres. |
| **Nombre del centro de eventos** | Nombre del centro de eventos que se usa como entrada. |
| **Nombre de la directiva del centro de eventos** | Directiva de acceso compartido que proporciona acceso al centro de eventos. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso. Esta opción se rellena automáticamente, a menos que elija proporcionar la configuración del centro de eventos manualmente.|
| **Grupo de consumidores del centro de eventos** (recomendado) | Se recomienda encarecidamente usar un grupo de consumidores distinto para cada trabajo de Stream Analytics. Esta cadena identifica el grupo de consumidores que se usa para la ingesta de datos desde el centro de eventos. Si no se especifica ningún grupo de consumidores, el trabajo de Stream Analytics usa el grupo de consumidores predeterminado.  |
| **Formato de serialización de eventos** | Formato de serialización (JSON, CSV o Avro) del flujo de datos entrantes.  Asegúrese de que el formato JSON responde a la especificación y no incluye un 0 inicial para números decimales. |
| **Encoding** | Por el momento, UTF-8 es el único formato de codificación compatible. |
| **Tipo de compresión de eventos** | El tipo de compresión utilizado para leer el flujo de datos entrante, como None (valor predeterminado), GZip o Deflate. |

Cuando los datos proceden de una entrada de flujo de un centro de eventos, puede acceder a los siguientes campos de metadatos en la consulta de Stream Analytics:

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| **EventProcessedUtcTime** |Fecha y hora en que se procesó el evento por Stream Analytics. |
| **EventEnqueuedUtcTime** |Fecha y la hora en que el Centro de eventos recibió el evento. |
| **PartitionId** |Identificador de partición de base cero para el adaptador de entrada. |

Por ejemplo, si usa estos campos, puede escribir una consulta similar al ejemplo siguiente:

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> Si se usa Event Hubs como punto de conexión para las rutas de IoT Hub, puede acceder a los metadatos de IoT Hub mediante la [función GetMetadataPropertyValue](https://msdn.microsoft.com/library/azure/mt793845.aspx).
> 

## <a name="stream-data-from-iot-hub"></a>Datos de flujo de IoT Hub
El centro de Iot de Azure es un servicio de introducción de eventos de suscripción-publicación altamente escalable optimizado para escenarios de IoT.

La marca de tiempo predeterminada de los eventos procedentes de IoT Hub en Stream Analytics es la marca de tiempo correspondiente al momento en que el evento llega a IoT Hub, que es `EventEnqueuedUtcTime`. Para procesar los datos como un flujo con una marca de tiempo en la carga del evento, se debe usar la palabra clave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

### <a name="consumer-groups"></a>Grupos de consumidores
Se debe configurar cada entrada de IoT Hub de Stream Analytics para que tenga su propio grupo de consumidores. Cuando un trabajo contiene una autocombinación o tiene varias entradas, es posible que alguna entrada pueda leerla más de un lector de bajada. Esta situación afecta al número de lectores de un solo grupo de consumidores. El procedimiento recomendado para evitar superar el límite de cinco lectores por grupo de consumidores por cada partición de Azure IoT Hub consiste en designar un grupo de consumidores para cada trabajo de Stream Analytics.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Configuración de una instancia de IoT Hub como entrada de flujo de datos
En la siguiente tabla se explica cada propiedad de la página **Nueva entrada** de Azure Portal cuando se configura IoT Hub como entrada de flujo.

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| **Alias de entrada** | Nombre descriptivo que se usará en la consulta del trabajo para hacer referencia a esta entrada.|
| **Suscripción** | Elija la suscripción en la que se encuentra el recurso de IoT Hub. | 
| **IoT Hub** | Nombre de la instancia de IoT Hub que se usa como entrada. |
| **Punto de conexión** | Punto de conexión de IoT Hub.|
| **Nombre de directiva de acceso compartido** | Directiva de acceso compartido que proporciona acceso a IoT Hub. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso. |
| **Clave de directiva de acceso compartido** | Clave de la directiva de acceso compartido que se usa para autorizar el acceso a IoT Hub.  Esta opción se rellena automáticamente, a menos que elija proporcionar la configuración de IoT Hub manualmente. |
| **Grupo de consumidores** | Se recomienda encarecidamente usar un grupo de consumidores distinto para cada trabajo de Stream Analytics. El grupo de consumidores para ingerir datos desde Azure IoT Hub. Stream Analytics usa el grupo de consumidores $Default, a menos que se especifique lo contrario.  |
| **Formato de serialización de eventos** | Formato de serialización (JSON, CSV o Avro) del flujo de datos entrantes.  Asegúrese de que el formato JSON responde a la especificación y no incluye un 0 inicial para números decimales. |
| **Encoding** | Por el momento, UTF-8 es el único formato de codificación compatible. |
| **Tipo de compresión de eventos** | El tipo de compresión utilizado para leer el flujo de datos entrante, como None (valor predeterminado), GZip o Deflate. |


Cuando usa datos de flujo de IoT Hub, puede acceder a los siguientes campos de metadatos en la consulta de Stream Analytics:

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| **EventProcessedUtcTime** | Fecha y la hora en que se produjo el evento. |
| **EventEnqueuedUtcTime** | La fecha y la hora en que el evento se recibió por Azure IoT Hub. |
| **PartitionId** | Identificador de partición de base cero para el adaptador de entrada. |
| **IoTHub.MessageId** | Identificador que sirve para correlacionar la comunicación bidireccional en IoT Hub. |
| **IoTHub.CorrelationId** | Identificador que se usa en las respuestas a mensajes y en los comentarios en IoT Hub. |
| **IoTHub.ConnectionDeviceId** | Identificador de autenticación que se usa para enviar este mensaje. Este valor lo marca IoT Hub en los mensajes servicebound. |
| **IoTHub.ConnectionDeviceGenerationId** | Identificador de generación del dispositivo autenticado que se ha usado para enviar este mensaje. Este valor lo marca IoT Hub en los mensajes servicebound. |
| **IoTHub.EnqueuedTime** | Hora en la que IoT Hub recibió el mensaje. |
| **IoTHub.StreamId** | Propiedad de evento personalizado que ha agregado el dispositivo remitente. |


## <a name="stream-data-from-blob-storage"></a>Datos de flujo de Blob Storage
Azure Blob Storage ofrece una solución rentable y escalable para aquellos escenarios con grandes cantidades de datos no estructurados para almacenar en la nube. Los datos de Blob Storage generalmente se consideran como datos en reposo, pero Stream Analytics puede procesarlos como un flujo de datos. 

Un escenario típico de entradas de Blob Storage con Stream Analytics es el procesamiento de registros. En este escenario, se han capturado archivos de datos de telemetría de un sistema y es preciso analizarlos y procesarlos para extraer datos significativos.

La marca de tiempo predeterminada de los eventos de Blob Storage en Stream Analytics es la marca de tiempo correspondiente al momento en que el blob se modificó por última vez, que es `BlobLastModifiedUtcTime`. Para procesar los datos como un flujo con una marca de tiempo en la carga del evento, se debe usar la palabra clave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

Las entradas con formato CSV *requieren* una fila de encabezado para definir los campos del conjunto de datos, y todos los campos de fila de encabezado deben ser únicos.

Stream Analytics no puede actualmente deserializar mensajes AVRO generados mediante acciones de captura de Event Hubs o el punto de conexión personalizado del contenedor de Azure Storage.

> [!NOTE]
> Stream Analytics no permite agregar contenido a un archivo de blob existente. Stream Analytics solo verá cada archivo una vez y los cambios que se produzcan en él después de que el trabajo lea los datos no se procesan. Se recomienda cargar todos los datos de un archivo de blob a la vez y, a continuación, agregar los eventos más recientes a un archivo de blob diferente y nuevo.
> 

### <a name="configure-blob-storage-as-a-stream-input"></a>Configuración de Blob Storage como entrada de flujo 

En la siguiente tabla se explica cada propiedad de la página **Nueva entrada** de Azure Portal cuando se configura Blob Storage como entrada de flujo.

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| **Alias de entrada** | Nombre descriptivo que se usará en la consulta del trabajo para hacer referencia a esta entrada. |
| **Suscripción** | Elija la suscripción en la que se encuentra el recurso de IoT Hub. | 
| **Cuenta de almacenamiento** | Nombre de la cuenta de almacenamiento donde se encuentran los archivos de blob. |
| **Clave de cuenta de almacenamiento** | La clave secreta asociada con la cuenta de almacenamiento. Esta opción se rellena automáticamente, a menos que elija proporcionar la configuración de Blob Storage manualmente. |
| **Contenedor** | Contenedor para la entrada de blob. Los contenedores proporcionan una agrupación lógica de los blobs almacenados en Microsoft Azure Blob service. Cuando se carga un blob en el servicio Azure Blob Storage, hay que especificar un contenedor para ese blob. Puede elegir el contenedor **Usar existente** contenedor o **Crear nuevo** para crear un contenedor.|
| **Patrón de ruta de acceso** (opcional) | Ruta de acceso de archivo que sirve para ubicar los blobs dentro del contenedor especificado. Dentro de la ruta, puede especificar una o más instancias de las tres variables siguientes: `{date}`, `{time}` o `{partition}`.<br/><br/>Ejemplo 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Ejemplo 2: `cluster1/logs/{date}`<br/><br/>El carácter `*` no es un valor permitido para el prefijo de ruta de acceso. Solo se permiten <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">caracteres de Blob de Azure</a>. |
| **Formato de fecha** (opcional) | Si usa la variable de fecha en la ruta, formato de fecha por el que se organizan los archivos. Ejemplo: `YYYY/MM/DD` |
| **Formato de hora** (opcional) |  Si usa la variable de hora en la ruta, formato de hora por el que se organizan los archivos. Actualmente, el único valor admitido es `HH` para las horas. |
| **Formato de serialización de eventos** | Formato de serialización (JSON, CSV o Avro) del flujo de datos entrantes.  Asegúrese de que el formato JSON responde a la especificación y no incluye un 0 inicial para números decimales. |
| **Encoding** | Por el momento, UTF-8 es el único formato de codificación compatible para CSV y JSON. |
| **Compresión** | El tipo de compresión utilizado para leer el flujo de datos entrante, como None (valor predeterminado), GZip o Deflate. |

Cuando los datos proceden de un origen de Blob Storage, puede acceder a los siguientes campos de metadatos en la consulta de Stream Analytics:

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| **BlobName** |Nombre del blob de entrada de donde procede el evento. |
| **EventProcessedUtcTime** |Fecha y hora en que se procesó el evento por Stream Analytics. |
| **BlobLastModifiedUtcTime** |Fecha y la hora en que se modificó por última vez el blob. |
| **PartitionId** |Identificador de partición de base cero para el adaptador de entrada. |

Por ejemplo, si usa estos campos, puede escribir una consulta similar al ejemplo siguiente:

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Guía de inicio rápido: Creación de un trabajo de Stream Analytics mediante Azure Portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
