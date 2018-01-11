---
title: "Conexión de datos: entradas de flujo de datos desde una transmisión de eventos | Microsoft Docs"
description: "Obtenga información sobre cómo configurar una conexión de datos a Stream Analytics que se denomina \"entradas\". Entre las entradas se incluyen una transmisión de datos de los eventos y también datos de referencia."
keywords: "transmisión de datos, conexión de datos, transmisión de eventos"
services: stream-analytics
documentationcenter: 
author: SnehaGunda
manager: kfile
editor: cgronlun
ms.assetid: 8155823c-9dd8-4a6b-8393-34452d299b68
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 12/11/2017
ms.author: sngun
ms.openlocfilehash: e8b55269e861dc010c911491d52973b674dd50ca
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2017
---
# <a name="data-connection-learn-about-data-stream-inputs-from-events-to-stream-analytics"></a>Conexión de datos: obtenga información sobre las entradas de transmisiones de datos desde eventos para el Stream Analytics
La conexión de datos a un trabajo de Stream Analytics es un flujo de eventos procedente de un origen de datos, que se denomina *entrada* del trabajo. Stream Analytics cuenta con integración de primera clase con orígenes de flujo de datos de Azure, como, por ejemplo, [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) y [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/). Estos orígenes de entrada pueden proceder de la misma suscripción de Azure que el trabajo de análisis o de otra suscripción.

## <a name="data-input-types-data-stream-and-reference-data"></a>Tipos de entrada de datos: datos de referencia y de transmisión de datos
A medida que los datos se insertan en un origen de datos, el trabajo de Stream Analytics los consume y los procesa en tiempo real. Las entradas se dividen en dos tipos distintos: entradas de flujo de datos y entradas de datos de referencia.

### <a name="data-stream-inputs"></a>Entradas de secuencia de datos
Un flujo de datos es una secuencia ilimitada de eventos a lo largo del tiempo. Los trabajos de Stream Analytics deben incluir, como mínimo, una entrada de flujo de datos. Event Hubs, IoT Hub y and Blob Storage se admiten como orígenes de entrada de flujo de datos. Los centros de eventos sirven para recopilar flujos de eventos desde varios dispositivos y servicios. Es posible que estos flujos incluyan fuentes de actividades de redes sociales, información bursátil o datos de sensores. IoT Hubs está optimizado para recopilar datos de dispositivos conectados en escenarios de Internet de las cosas (IoT).  Blob Storage puede usarse como origen de entrada para la ingesta de conjuntos masivos de datos en forma de flujo, como, por ejemplo, archivos de registro.  

### <a name="reference-data"></a>Datos de referencia
Stream Analytics también admite la entrada de *datos de referencia*. Se trata de datos auxiliares que son estáticos o cambian lentamente. Suelen usarse para realizar correlaciones y búsquedas. Por ejemplo, es posible combinar datos de la entrada de flujo de datos con datos de los datos de referencia, de la misma forma que ejecutaría una instrucción SQL JOIN para buscar valores estáticos. Almacenamiento de blobs de Azure es el único origen de entrada admitido actualmente para los datos de referencia. El tamaño de los blobs de origen de datos de referencia se limita a 100 MB.

Para aprender a crear entradas de datos de referencia, vea [Uso de datos de referencia](stream-analytics-use-reference-data.md).  

## <a name="compression"></a>Compresión

Azure Stream Analytics admite la compresión en todos los orígenes de entrada del flujo de datos (Event Hubs, IoT Hub y Blob Storage). Esta característica agrega una opción desplegable nueva en la hoja **Nueva entrada** en Azure Portal, lo que le permite elegir, de manera opcional, si comprimir o no el flujo de datos. Los tipos de referencia compatibles actualmente son None, GZip y compresión Deflate. La compatibilidad con la compresión no está disponible para los datos de referencia.

No es necesario especificar el tipo de compresión con la serialización de Avro. Si se comprimen los datos de entrada de Avro, se controlan de forma transparente. 

## <a name="create-data-stream-input-from-event-hubs"></a>Creación de una entrada de flujo de datos desde Event Hubs

Azure Event Hubs ofrece consumidores de eventos de publicación y suscripción muy escalables. Un centro de eventos puede recopilar millones de eventos por segundo, por lo se pueden procesar y analizar las grandes cantidades de datos que generan las aplicaciones y los dispositivos conectados. Event Hubs y Stream Analytics juntos ofrecen una solución de extremo a extremo para el análisis en tiempo real: Event Hubs permite introducir eventos en Azure en tiempo real y los trabajos de Stream Analytics pueden procesarlos en tiempo real. Por ejemplo, se pueden enviar clics de web, lecturas de sensor o eventos de registro en línea a Event Hubs. Luego se pueden crear trabajos de Stream Analytics que usen Event Hubs como los flujos de datos de entrada para su filtrado, agregación y correlación en tiempo real.

La marca de tiempo predeterminada de los eventos procedentes de Event Hubs en Stream Analytics es la marca de tiempo correspondiente al momento en que el evento llega al centro de eventos, que es `EventEnqueuedUtcTime`. Para procesar los datos como un flujo con una marca de tiempo en la carga del evento, se debe usar la palabra clave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

### <a name="consumer-groups"></a>Grupos de consumidores
Se debe configurar cada entrada de centro de eventos de Stream Analytics para que tenga su propio grupo de consumidores. Cuando un trabajo contiene una autocombinación o tiene varias entradas, es posible que alguna entrada pueda leerla más de un lector de bajada. Esta situación afecta al número de lectores de un solo grupo de consumidores. El procedimiento recomendado para evitar superar el límite de cinco lectores por grupo de consumidores por cada partición de Event Hubs consiste en designar un grupo de consumidores para cada trabajo de Stream Analytics. Además, hay un límite de 20 grupos de consumidores por centro de eventos. Para más información, vea la [Guía de programación de Event Hubs](../event-hubs/event-hubs-programming-guide.md).

### <a name="configure-an-event-hub-as-a-data-stream-input"></a>Configuración de un centro de eventos como entrada de flujo de datos
En la siguiente tabla se explica cada propiedad de la hoja **Nueva entrada** de Azure Portal cuando se configura un centro de eventos como entrada.

| Propiedad | Descripción |
| --- | --- |
| **Alias de entrada** |Nombre descriptivo que se usará en la consulta del trabajo para hacer referencia a esta entrada. |
| **Espacio de nombres de Service Bus** |Espacio de nombres de Azure Service Bus, que es un contenedor para un conjunto de entidades de mensajería. Al crear un centro de eventos, también se crea un espacio de nombres de Service Bus. |
| **Nombre del centro de eventos** |Nombre del centro de eventos que se usa como entrada. |
| **Nombre de directiva de centro de eventos** |Directiva de acceso compartido que proporciona acceso al centro de eventos. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso. |
| **Grupo de consumidores del centro de eventos** (opcional) |Grupo de consumidores que se usa para la ingesta de datos desde el centro de eventos. Si no se especifica ningún grupo de consumidores, el trabajo de Stream Analytics usa el grupo de consumidores predeterminado. Se recomienda usar un grupo de consumidores distinto para cada trabajo de Stream Analytics. |
| **Formato de serialización de eventos** |Formato de serialización (JSON, CSV o Avro) del flujo de datos entrantes. |
| **Encoding** | Por el momento, UTF-8 es el único formato de codificación compatible. |
| **Compresión** (opcional) | El tipo de compresión (None, GZip o Deflate) del flujo de datos entrante. |

Cuando los datos proceden de un centro de eventos, puede acceder a los siguientes campos de metadatos en la consulta de Stream Analytics:

| Propiedad | Descripción |
| --- | --- |
| **EventProcessedUtcTime** |Fecha y hora en que se procesó el evento por Stream Analytics. |
| **EventEnqueuedUtcTime** |Fecha y la hora en que el Centro de eventos recibió el evento. |
| **PartitionId** |Identificador de partición de base cero para el adaptador de entrada. |

Por ejemplo, si usa estos campos, puede escribir una consulta similar al ejemplo siguiente:

````
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
````

> [!NOTE]
> Si se usa Event Hubs como punto de conexión para las rutas de IoT Hub, puede acceder a los metadatos de IoT Hub mediante la [función GetMetadataPropertyValue](https://msdn.microsoft.com/en-us/library/azure/mt793845.aspx).
> 

## <a name="create-data-stream-input-from-iot-hub"></a>Creación de una entrada de flujo de datos desde IoT Hub
El centro de Iot de Azure es un servicio de introducción de eventos de suscripción-publicación altamente escalable optimizado para escenarios de IoT.

La marca de tiempo predeterminada de los eventos procedentes de IoT Hub en Stream Analytics es la marca de tiempo correspondiente al momento en que el evento llega a IoT Hub, que es `EventEnqueuedUtcTime`. Para procesar los datos como un flujo con una marca de tiempo en la carga del evento, se debe usar la palabra clave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

> [!NOTE]
> Solo pueden procesarse los mensajes enviados con una propiedad `DeviceClient`.
> 
> 

### <a name="consumer-groups"></a>Grupos de consumidores
Se debe configurar cada entrada de IoT Hub de Stream Analytics para que tenga su propio grupo de consumidores. Cuando un trabajo contiene una autocombinación o tiene varias entradas, es posible que alguna entrada pueda leerla más de un lector de bajada. Esta situación afecta al número de lectores de un solo grupo de consumidores. El procedimiento recomendado para evitar superar el límite de cinco lectores por grupo de consumidores por cada partición de Azure IoT Hub consiste en designar un grupo de consumidores para cada trabajo de Stream Analytics.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Configuración de una instancia de IoT Hub como entrada de flujo de datos
En la siguiente tabla se explica cada propiedad de la hoja **Nueva entrada** de Azure Portal cuando se configura IoT Hub como entrada.

| Propiedad | Descripción |
| --- | --- |
| **Alias de entrada** |Nombre descriptivo que se usará en la consulta del trabajo para hacer referencia a esta entrada.|
| **IoT Hub** |Nombre de la instancia de IoT Hub que se usa como entrada. |
| **Punto de conexión** |Punto de conexión de IoT Hub.|
| **Nombre de directiva de acceso compartido** |Directiva de acceso compartido que proporciona acceso a IoT Hub. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso. |
| **Clave de directiva de acceso compartido** |Clave de la directiva de acceso compartido que se usa para autorizar el acceso a IoT Hub. |
| **Grupo de consumidores** (opcional) |Grupo de consumidores que se usa para la ingesta de datos desde IoT Hub. Si no se especifica ningún grupo de consumidores, el trabajo de Stream Analytics usa el grupo de consumidores predeterminado. Se recomienda usar un grupo de consumidores distinto para cada trabajo de Stream Analytics. |
| **Formato de serialización de eventos** |Formato de serialización (JSON, CSV o Avro) del flujo de datos entrantes. |
| **Encoding** |Por el momento, UTF-8 es el único formato de codificación compatible. |
| **Compresión** (opcional) | El tipo de compresión (None, GZip o Deflate) del flujo de datos entrante. |

Cuando los datos proceden de IoT Hub, puede acceder a los siguientes campos de metadatos en la consulta de Stream Analytics:

| Propiedad | Descripción |
| --- | --- |
| **EventProcessedUtcTime** |Fecha y la hora en que se produjo el evento. |
| **EventEnqueuedUtcTime** |Fecha y hora en que el evento se recibió en IoT Hub. |
| **PartitionId** |Identificador de partición de base cero para el adaptador de entrada. |
| **IoTHub.MessageId** | Identificador que sirve para correlacionar la comunicación bidireccional en IoT Hub. |
| **IoTHub.CorrelationId** |Identificador que se usa en las respuestas a mensajes y en los comentarios en IoT Hub. |
| **IoTHub.ConnectionDeviceId** |Identificador de autenticación que se usa para enviar este mensaje. Este valor lo marca IoT Hub en los mensajes servicebound. |
| **IoTHub.ConnectionDeviceGenerationId** |Identificador de generación del dispositivo autenticado que se ha usado para enviar este mensaje. Este valor lo marca IoT Hub en los mensajes servicebound. |
| **IoTHub.EnqueuedTime** |Hora en la que IoT Hub recibió el mensaje. |
| **IoTHub.StreamId** |Propiedad de evento personalizado que ha agregado el dispositivo remitente. |


## <a name="create-data-stream-input-from-blob-storage"></a>Creación de una entrada de flujo de datos desde Blob Storage
Azure Blob Storage ofrece una solución rentable y escalable para aquellos escenarios con grandes cantidades de datos no estructurados para almacenar en la nube. Los datos de Blob Storage suelen considerarse datos en reposo. Pero, Stream Analytics puede procesarlos como flujo de datos. Un escenario típico de entradas de Blob Storage con Stream Analytics es el procesamiento de registros. En este escenario, se han capturado datos de telemetría de un sistema y es preciso analizarlos y procesarlos para extraer datos significativos.

La marca de tiempo predeterminada de los eventos de Blob Storage en Stream Analytics es la marca de tiempo correspondiente al momento en que el blob se modificó por última vez, que es `BlobLastModifiedUtcTime`. Para procesar los datos como un flujo con una marca de tiempo en la carga del evento, se debe usar la palabra clave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

Las entradas con formato CSV *requieren* una fila de encabezado para definir los campos del conjunto de datos. Además, todos los campos de fila de encabezado deben ser únicos.

> [!NOTE]
> Stream Analytics no permite agregar contenido a un archivo de blob existente. Stream Analytics solo verá cada archivo una vez y los cambios que se produzcan en él después de que el trabajo lea los datos no se procesan. Se recomienda cargar todos los datos de un archivo de blob a la vez y, a continuación, agregar los eventos más recientes a un archivo de blob diferente y nuevo.
> 

### <a name="configure-blob-storage-as-a-data-stream-input"></a>Configuración de Blob Storage como entrada de flujo de datos

En la siguiente tabla se explica cada propiedad de la hoja **Nueva entrada** de Azure Portal cuando se configura Blob Storage como entrada.

| Propiedad | Descripción |
| --- | --- |
| **Alias de entrada** | Nombre descriptivo que se usará en la consulta del trabajo para hacer referencia a esta entrada. |
| **Cuenta de almacenamiento** | Nombre de la cuenta de almacenamiento donde se encuentran los archivos de blob. |
| **Clave de cuenta de almacenamiento** | La clave secreta asociada con la cuenta de almacenamiento. |
| **Contenedor** | Contenedor para la entrada de blob. Los contenedores proporcionan una agrupación lógica de los blobs almacenados en Microsoft Azure Blob service. Cuando se carga un blob en el servicio Azure Blob Storage, hay que especificar un contenedor para ese blob. |
| **Patrón de ruta de acceso** (opcional) | Ruta de acceso de archivo que sirve para ubicar los blobs dentro del contenedor especificado. Dentro de la ruta, puede especificar una o más instancias de las tres variables siguientes: `{date}`, `{time}` o `{partition}`.<br/><br/>Ejemplo 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Ejemplo 2: `cluster1/logs/{date}`<br/><br/>El carácter `*` no es un valor permitido para el prefijo de ruta de acceso. Solo se permiten <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">caracteres de Blob de Azure</a>. |
| **Formato de fecha** (opcional) | Si usa la variable de fecha en la ruta, formato de fecha por el que se organizan los archivos. Ejemplo: `YYYY/MM/DD` |
| **Formato de hora** (opcional) |  Si usa la variable de hora en la ruta, formato de hora por el que se organizan los archivos. Actualmente, el único valor admitido es `HH`. |
| **Formato de serialización de eventos** | Formato de serialización (JSON, CSV o Avro) del flujo de datos entrantes. |
| **Encoding** | Por el momento, UTF-8 es el único formato de codificación compatible para CSV y JSON. |
| **Compresión** (opcional) | El tipo de compresión (None, GZip o Deflate) del flujo de datos entrante. |

Cuando los datos proceden de un origen de Blob Storage, puede acceder a los siguientes campos de metadatos en la consulta de Stream Analytics:

| Propiedad | Descripción |
| --- | --- |
| **BlobName** |Nombre del blob de entrada de donde procede el evento. |
| **EventProcessedUtcTime** |Fecha y hora en que se procesó el evento por Stream Analytics. |
| **BlobLastModifiedUtcTime** |Fecha y la hora en que se modificó por última vez el blob. |
| **PartitionId** |Identificador de partición de base cero para el adaptador de entrada. |

Por ejemplo, si usa estos campos, puede escribir una consulta similar al ejemplo siguiente:

````
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
````

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Pasos siguientes
Ha obtenido información sobre las opciones de conexión de datos de Azure para sus trabajos de Stream Analytics. Para obtener más información sobre Stream Analytics, vea:

* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
