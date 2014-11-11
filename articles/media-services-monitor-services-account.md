<properties linkid="manage-services-mediaservices-monitor-a-media-services-account" urlDisplayName="How to monitor" pageTitle="Monitor a Media Services Account - Azure" metaKeywords="" description="Describes how to configure monitoring for your Media Services account in Azure." metaCanonical="" services="media-services" documentationCenter="" title="How to Monitor a Media Services Account" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree" />

# <span id="monitormediaservicesaccount"></span></a>Supervisión de una cuenta de Servicios multimedia

El panel Servicios multimedia de Azure presenta las métricas de uso y la información de la cuenta que se pueden utilizar para administrar la cuenta de Servicios multimedia.

Puede supervisar el número de trabajos de codificación en cola, tareas de codificación con error, trabajos de codificación activos representados por los datos de entrada y salida del codificador, así como el uso de almacenamiento de blobs asociados a la cuenta de Servicios multimedia. Además, si está realizando streaming de contenido a los clientes, puede recuperar también varias métricas de streaming. Puede elegir supervisar los datos durante las últimas 6 horas, 24 horas o 7 días.

**Nota:** existen costes adicionales asociados a la supervisión de los datos de almacenamiento en el Portal de administración de Azure. Para obtener más información, consulte [Facturación del análisis de almacenamiento][Facturación del análisis de almacenamiento].

## <span id="configuremonitoring"></span></a>Supervisión de una cuenta de Servicios multimedia

1.  En el [Portal de administración][Portal de administración], haga clic en **Servicios multimedia** y, a continuación, haga clic en el nombre de la cuenta de Servicios multimedia para abrir el panel.

    ![Panel\_ServiciosMultimedia][Panel\_ServiciosMultimedia]

2.  Para supervisar los datos o trabajos de codificación, comience a enviar los trabajos de codificación a Servicios multimedia, o comience a realizar streaming de contenido a los clientes a través de la transmisión por secuencias a petición de Servicios multimedia de Azure. Debería empezar a ver los datos de supervisión en el panel al cabo de una hora aproximadamente.

## <span id="configuringstorage"></span></a>Supervisión del uso de almacenamiento de blobs (opcional)

1.  Haga clic en el nombre de la **cuenta de almacenamiento** en la sección de **vista rápida**.
2.  En la página de la cuenta de almacenamiento, haga clic en el vínculo **página de configuración** y desplácese hacia abajo hasta la configuración de **supervisión** para los servicios Blob, Tabla y Cola, mostrados a continuación.

    **Nota:** los blobs son el único tipo de almacenamiento admitido en Servicios multimedia.

    ![OpcionesAlmacenamiento][OpcionesAlmacenamiento]

3.  En **supervisión**, configure el nivel de supervisión y la directiva de retención de datos para los blobs:

-   Para configurar el nivel de supervisión, seleccione una de las opciones siguientes:

    **Minimal**: Recopila métricas como entrada/salida, disponibilidad, latencia y porcentajes de éxito, que se agregan a los servicios Blob, Tabla y Cola.

    **Verbose**: Además de las métricas mínimas, recopila el mismo conjunto de métricas para cada operación de almacenamiento en la API del Servicio de almacenamiento de Azure. Las métricas detalladas facilitan el análisis preciso de los problemas que se producen durante las operaciones de las aplicaciones.

    **Off**: Desactiva la supervisión. Los datos de supervisión existentes permanecen disponibles hasta el final del periodo de retención.

-   Para configurar la directiva de retención de datos, en **Retención (en días)**, escriba el número de días que se deben retener los datos (entre 1 y 365). Si no desea configurar una directiva de retención, escriba un cero. Si no existe una directiva de retención, es posible eliminar los datos de supervisión. Recomendamos que configure una directiva de retención basada en el tiempo que desee retener los datos de análisis de almacenamiento de su cuenta, de modo que el sistema pueda eliminar sin coste los datos de análisis antiguos o no usados.

1.  Al finalizar la configuración de la supervisión, haga clic en **Save**.
    De manera similar a las métricas de Servicios multimedia, debería empezar a ver los datos de supervisión en el panel al cabo de una hora aproximadamente.
    Las métricas se almacenan en la cuenta de almacenamiento en cuatro tablas denominadas $MetricsTransactionsBlob, $MetricsTransactionsTable, $MetricsTransactionsQueue y $MetricsCapacityBlob. Para obtener más información, consulte [Storage Analytics Metrics][Storage Analytics Metrics].

<!-- Images -->

  [Facturación del análisis de almacenamiento]: http://go.microsoft.com/fwlink/?LinkId=256667
  [Portal de administración]: http://go.microsoft.com/fwlink/?LinkID=256666
  [OpcionesAlmacenamiento]: ./media/media-services-monitor-services-account/storagemonitoringoptions_scoped.png
  [Storage Analytics Metrics]: http://go.microsoft.com/fwlink/?LinkId=256668
