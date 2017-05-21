---
title: "Introducción al archivado de los datos de telemetría con Azure Event Hubs Archive | Microsoft Docs"
description: "Información general de la característica Azure Event Hubs Archive | Microsoft Azure."
services: event-hubs
documentationcenter: 
author: djrosanova
manager: timlt
editor: 
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: darosa;sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 03756c15d0cd46731e66df3d81c97099f13dcd17
ms.contentlocale: es-es
ms.lasthandoff: 05/16/2017


---
# <a name="azure-event-hubs-archive"></a>Azure Event Hubs Archive
Azure Event Hubs Archive permite entregar automáticamente los datos de transmisión por secuencias de Event Hubs a la cuenta de Blob Storage que elija con mayor flexibilidad para especificar el intervalo de tiempo o de espacio que prefiera. La configuración de Archive es rápida, su ejecución no tiene costos administrativos y se escala automáticamente con las [unidades de procesamiento](event-hubs-features.md#capacity) de Event Hubs. Event Hubs Archive es la forma más sencilla de cargar datos de streaming en Azure y permite centrarse en el procesamiento de datos, en lugar de en su captura.

Event Hubs Archive permite procesar las canalizaciones en tiempo real y las basadas en lotes en la misma transmisión, lo que permite crear soluciones que pueden crecer a la par que sus necesidades. Si ya está creando sistemas basados en lotes pensando en un futuro procesamiento en tiempo real o desea agregar una ruta de acceso inactiva eficaz a una solución en tiempo real existente, Event Hubs Archive facilita el trabajo con la transmisión de datos.

## <a name="how-event-hubs-archive-works"></a>Funcionamiento de Event Hubs Archive
Event Hubs es un búfer duradero de retención temporal para la entrada de datos de telemetría, es similar a un registro distribuido. La clave para reducir horizontalmente en Event Hubs es el [modelo de consumidor con particiones](event-hubs-features.md#partitions). Cada partición es un segmento de datos independiente y se consume de forma independiente. Con el tiempo estos datos envejecen basándose en el período de retención configurable. Como consecuencia, un centro de eventos determinado nunca llega a estar "demasiado lleno".

Event Hubs Archive le permite especificar su propia cuenta de Azure Blob Storage y el contenedor que se utilizará para almacenar los datos archivados. Esta cuenta puede estar en la misma región que su centro de eventos o en otra región, algo que se agrega a la flexibilidad de la característica Event Hubs Archive.

Los datos archivados se escriben en el formato de [Apache Avro][Apache Avro], que es un formato compacto, rápido y binario que proporciona estructuras de datos enriquecidos con un esquema insertado. Este formato se usa ampliamente en el ecosistema de Hadoop, pero también lo usan Stream Analytics y Azure Data Factory. En este mismo artículo encontrará más información acerca de cómo trabajar con Avro.

### <a name="archive-windowing"></a>Ventanas de Archive
Event Hubs Archive permite configurar una ventana para controlar el almacenamiento. Esta ventana tiene una configuración mínima de tamaño y tiempo con una "directiva de que el primero gana", lo que significa que el primer desencadenador que se encuentre provocará una operación de almacenamiento. Si tiene una ventana de almacenamiento de 100 MB cada quince minutos y envía 1 MB/s, la ventana de tamaño se desencadenará antes que la ventana de tiempo. Cada partición se archiva de forma independiente y escribe un blob en bloques completado en el momento del almacenamiento, denominado como la hora en que se encontró el intervalo de almacenamiento. Esta es la convención de nomenclatura:

```
[Namespace]/[EventHub]/[Partition]/[YYYY]/[MM]/[DD]/[HH]/[mm]/[ss]
```

### <a name="scaling-to-throughput-units"></a>Escalado a unidades de procesamiento
El tráfico de los Event Hubs lo controlan las [unidades de procesamiento](event-hubs-features.md#capacity). Una sola unidad de procesamiento permite una entrada de 1 MB/s o 1000 eventos por segundo y una salida que duplica esas cifras. Los Event Hubs estándar se pueden configurar con 1-20 unidades de procesamiento, y puede adquirir más a través de una solicitud de aumento de cuota al [equipo de soporte técnico][support request]. El uso por encima de las unidades de procesamiento adquiridas está limitado. Event Hubs Archive copia los datos directamente del almacenamiento interno de Event Hubs, omite las cuotas de salida de unidades de procesamiento y guarda la salida para otros lectores de procesamiento como Stream Analytics o Spark.

Una vez configurado, Event Hubs Archive se ejecuta automáticamente en cuanto se envía el primer evento. Y no deja de ejecutarse en ningún momento. Para facilitar que el procesamiento de bajada sepa que el proceso funciona, Events Hubs escribe archivos vacíos cuando no hay datos. Esto proporciona un marcador y una cadencia predecibles que puede alimentar sus procesadores de lotes.

## <a name="setting-up-event-hubs-archive"></a>Configuración de Event Hubs Archive
Archive se puede configurar en el momento de creación de un centro de eventos a través del Portal o de Azure Resource Manager. Simplemente habilite Archive, para lo que debe hacer clic en el botón **Activado** . Configure una cuenta de almacenamiento y un contenedor, para lo que debe hacer clic en la sección **Contenedor** de la hoja. Dado que Event Hubs Archive utiliza la autenticación de servicio a servicio con el almacenamiento, no es necesario especificar una cadena de conexión de almacenamiento. El selector de recursos selecciona automáticamente el identificador URI del recurso para la cuenta de almacenamiento. Si se usa Azure Resource Manager, es preciso suministrar explícitamente dicho identificador URI como una cadena.

La ventana de tiempo predeterminada es cinco minutos. El valor mínimo es 1 y el máximo 15. La ventana **Tamaño** tiene un intervalo de 10-500 MB.

![][1]

## <a name="adding-archive-to-an-existing-event-hub"></a>Incorporación de Archive a un centro de eventos existente
Se pueden configurar Archive en los Centros de eventos existentes que se encuentran en un espacio de nombres de Event Hubs. La característica no está disponible en los anteriores espacios de nombres del tipo **Mensajería** o **Mixto**. Para habilitar Archive en un centro de eventos existente o para cambiar la configuración de Archive, haga clic en su espacio de nombres para cargar la hoja **Essentials** y, después, haga clic en el centro de eventos para el que desea habilitar o cambiar la configuración de Archive. Por último, haga clic en la sección **Propiedades** de la hoja abierta, tal como se muestra en la ilustración siguiente.

![][2]

Event Hubs Archive también se puede configurar a través de las plantillas de Azure Resource Manager. Para obtener más información, consulte [este artículo](event-hubs-resource-manager-namespace-event-hub-enable-archive.md).

## <a name="exploring-the-archive-and-working-with-avro"></a>Exploración del almacenamiento y trabajo con Avro
Una vez configurado, Event Hubs Archive crea archivos en la cuenta de Azure Storage y en el contenedor proporcionado en la ventana de tiempo configurada. Dichos archivos se pueden ver en cualquier herramienta, como el [Explorador de Azure Storage][Azure Storage Explorer]. Los archivos se pueden descargar de forma local para trabajar con ellos.

Los archivos que genera Event Hubs Archive tienen el siguiente esquema de Avro:

![][3]

Para explorar los archivos de Avro fácilmente, utilice el archivo jar [Avro Tools][Avro Tools] desde Apache. Tras descargar este archivo, para ver el esquema de un archivo específico de Avro, ejecute el comando siguiente:

```
java -jar avro-tools-1.8.1.jar getschema \<name of archive file\>
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

Apache Avro tiene guías de introducción para [Java][Java] y [Python][Python] muy completas. También puede leer el artículo de [introducción a Event Hubs Archive](event-hubs-archive-python.md) .

## <a name="how-event-hubs-archive-is-charged"></a>Cobro de Event Hubs Archive
Event Hubs Archive se mide de forma similar a las unidades de procesamiento, por hora. El cargo es directamente proporcional al número de unidades de procesamiento que se adquirieron para el espacio de nombres. A medida que las unidades de procesamiento se incrementan y reducen, Event Hubs Archive aumenta y disminuye, con el fin de proporcionar un rendimiento coincidente. Los medidores se ejecutan de manera simultánea. El costo de Event Hubs Archive es $0,10 por hora por unidad de procesamiento, que se ofrece con un descuento del 50 % durante el período de versión preliminar.

Event Hubs Archive es la manera más fácil de introducir datos en Azure. Con Azure Data Lake, Azure Data Factory y Azure HDInsight, se pueden realizar el procesamiento por lotes y cualquier otro análisis que desee con las plataformas y herramientas conocidas a la escala que necesite.

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de Event Hubs, visite los vínculos siguientes:

* Una [aplicación de ejemplo completa que usa Event Hubs][sample application that uses Event Hubs].
* El ejemplo [Scale out Event Processing with Event Hubs][Scale out Event Processing with Event Hubs] (Escalado horizontal del procesamiento de eventos con Event Hubs).
* [Información general de Event Hubs][Event Hubs overview]

[Apache Avro]: http://avro.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[1]: ./media/event-hubs-archive-overview/event-hubs-archive1.png
[2]: media/event-hubs-archive-overview/event-hubs-archive2.png
[Azure Storage Explorer]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-archive-overview/event-hubs-archive3.png
[Avro Tools]: http://www-us.apache.org/dist/avro/avro-1.8.1/java/avro-tools-1.8.1.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3

