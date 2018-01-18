---
title: "Introducción a Azure Event Hubs Capture | Microsoft Docs"
description: "Captura de datos de telemetría con Event Hubs Capture"
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: sethm;darosa
ms.openlocfilehash: fbd4aef62891341ad3760b74cd8aaee7abf7b827
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="azure-event-hubs-capture"></a>Azure Event Hubs Capture

Azure Event Hubs Capture permite entregar automáticamente los datos de transmisión de Event Hubs a una cuenta de [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) o [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) que elija con la flexibilidad añadida de poder especificar un intervalo de tiempo o de tamaño. La configuración de Capture es rápida, su ejecución no tiene costes administrativos y se escala automáticamente con las [unidades de procesamiento](event-hubs-features.md#capacity) de Event Hubs. El uso de Event Hubs Capture constituye la forma más sencilla de cargar datos de streaming en Azure y permite centrarse en el procesamiento de datos, en lugar de en su captura.

Event Hubs Capture permite procesar las canalizaciones en tiempo real y las basadas en lotes en la misma transmisión, lo que permite crear soluciones que crecen a la par que sus necesidades. Si ya está creando sistemas basados en lotes pensando en un futuro procesamiento en tiempo real o desea agregar una ruta de acceso inactiva eficaz a una solución en tiempo real existente, Event Hubs Capture facilita el trabajo con datos de streaming.

## <a name="how-event-hubs-capture-works"></a>Cómo funciona Event Hubs Capture

Event Hubs es un búfer duradero de retención temporal para la entrada de datos de telemetría, es similar a un registro distribuido. La clave para reducir horizontalmente en Event Hubs es el [modelo de consumidor con particiones](event-hubs-features.md#partitions). Cada partición es un segmento de datos independiente y se consume de forma independiente. Con el tiempo estos datos envejecen basándose en el período de retención configurable. Como consecuencia, un centro de eventos determinado nunca llega a estar "demasiado lleno".

Event Hubs Capture le permite especificar su propia cuenta y contenedor de Azure Blob Storage, o cuenta de Azure Data Lake Store, que se usan para almacenar los datos capturados. Estas cuentas pueden estar en la misma región que el centro de eventos o en otra, lo que se suma a la flexibilidad de la característica Event Hubs Capture.

Los datos capturados se escriben en el formato de [Apache Avro][Apache Avro], que es un formato compacto, rápido y binario que proporciona estructuras de datos enriquecidos con un esquema insertado. Este formato se usa ampliamente en el ecosistema de Hadoop, en Stream Analytics y en Azure Data Factory. En este mismo artículo encontrará más información acerca de cómo trabajar con Avro.

### <a name="capture-windowing"></a>Ventanas de captura

Event Hubs Capture permite configurar una ventana para controlar la captura. Esta ventana es una configuración mínima de tamaño y tiempo con una "directiva de que el primero gana", lo que significa que el primer desencadenador que se encuentre provocará una operación de captura. Si tiene una ventana de captura de 100 MB cada quince minutos y envía 1 MB/s, la ventana de tamaño se desencadenará antes que la ventana de tiempo. Cada partición se captura de forma independiente y escribe un blob en bloques completado en el momento de la captura, denominado como la hora en que se encontró el intervalo de captura. Esta es la convención de nomenclatura de almacenamiento:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Tenga en cuenta que los valores de fecha se rellenan con ceros; un nombre de archivo de ejemplo podría ser:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

### <a name="scaling-to-throughput-units"></a>Escalado a unidades de procesamiento

El tráfico de los Event Hubs lo controlan las [unidades de procesamiento](event-hubs-features.md#capacity). Una sola unidad de procesamiento permite una entrada de 1 MB/s o 1000 eventos por segundo y una salida que duplica esas cifras. Event Hubs estándar se puede configurar con entre 1 y 20 unidades de procesamiento, y puede adquirir más a través de una solicitud de aumento de cuota al [equipo de soporte técnico][support request]. El uso por encima de las unidades de procesamiento adquiridas está limitado. Event Hubs Capture copia los datos directamente del almacenamiento interno de Event Hubs, omite las cuotas de salida de unidades de procesamiento y guarda la salida para otros lectores de procesamiento como Stream Analytics o Spark.

Una vez configurado, Event Hubs Capture se ejecuta automáticamente cuando se envía el primer evento y continúa ejecutándose. Para facilitar que el procesamiento de bajada sepa que el proceso funciona, Event Hubs escribe archivos vacíos cuando no hay datos. Este proceso proporciona un marcador y una cadencia predecibles que puede alimentar sus procesadores de lotes.

## <a name="setting-up-event-hubs-capture"></a>Configuración de Event Hubs Capture

Puede configurar la funcionalidad de captura en el momento de creación del centro de eventos mediante [Azure Portal](https://portal.azure.com) o con las plantillas de Azure Resource Manager. Para más información, consulte los siguientes artículos.

- [Habilitación de Event Hubs Capture mediante Azure Portal](event-hubs-capture-enable-through-portal.md)
- [Creación de un espacio de nombres de Event Hubs con un centro de eventos y habilitación de la característica Capture mediante una plantilla de Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)

## <a name="exploring-the-captured-files-and-working-with-avro"></a>Exploración de los archivos capturados y trabajo con Avro

Event Hubs Capture crea archivos en formato Avro, como se especifica en la ventana de tiempo configurada. Dichos archivos se pueden ver en cualquier herramienta, como el [Explorador de Azure Storage][Azure Storage Explorer]. Los archivos se pueden descargar de forma local para trabajar con ellos.

Los archivos que genera Event Hubs Capture tienen el siguiente esquema de Avro:

![][3]

Para explorar los archivos de Avro fácilmente, utilice el archivo jar [Avro Tools][Avro Tools] desde Apache. Tras descargar este archivo, para ver el esquema de un archivo específico de Avro, ejecute el comando siguiente:

```
java -jar avro-tools-1.8.2.jar getschema <name of capture file>
```

Este comando devuelve

```
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

Avro Tools también puede utilizarse para convertir el archivo al formato JSON y realizar otro procesamiento.

Para realizar un procesamiento más avanzado, descargue e instale Avro para la plataforma que desee. En el momento de redactar este artículo, existen implementaciones para C, C++, C\#, Java, NodeJS, Perl, PHP, Python y Ruby.

Apache Avro tiene guías de introducción para [Java][Java] y [Python][Python] muy completas. También puede leer el artículo [Getting started with Event Hubs Capture (Introducción a Event Hubs Capture)](event-hubs-capture-python.md).

## <a name="how-event-hubs-capture-is-charged"></a>Cómo se factura Event Hubs Capture

Event Hubs Capture se mide de forma similar a las unidades de procesamiento, por hora. El cargo es directamente proporcional al número de unidades de procesamiento que se adquirieron para el espacio de nombres. A medida que las unidades de procesamiento se incrementan y reducen, las mediciones de Event Hubs Capture aumentan y disminuyen, con el fin de proporcionar un rendimiento coincidente. Los medidores se ejecutan de manera simultánea. Para conocer los precios detallados, consulte [Precios de Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/). 

## <a name="next-steps"></a>pasos siguientes

Event Hubs Capture es el modo más sencillo de obtener datos en Azure. Con Azure Data Lake, Azure Data Factory y Azure HDInsight, se puede realizar el procesamiento por lotes y cualquier otro análisis mediante las plataformas y herramientas conocidas a la escala que necesite.

Para más información acerca de Event Hubs, visite los vínculos siguientes:

* [Get started sending and receiving events (Introducción al envío y la recepción de eventos)](event-hubs-dotnet-framework-getstarted-send.md)
* [Información general de Event Hubs][Event Hubs overview]

[Apache Avro]: http://avro.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: http://www-us.apache.org/dist/avro/avro-1.8.2/java/avro-tools-1.8.2.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
