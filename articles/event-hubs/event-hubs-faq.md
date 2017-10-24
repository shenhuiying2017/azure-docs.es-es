---
title: P+F sobre Azure Event Hubs | Microsoft Docs
description: Preguntas frecuentes sobre Azure Event Hubs (P+F)
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: bfa10984-eb22-4671-861a-f377a90d9372
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: c4faa071c4f2401fe3e852e787e3b7d4da0c7d44
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="event-hubs-frequently-asked-questions"></a>Preguntas frecuentes sobre Event Hubs

## <a name="general"></a>General

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>¿Cuál es la diferencia entre los niveles Básico y Estándar de Event Hubs?

El nivel Estándar de Azure Event Hubs proporciona más características de las disponibles en el nivel Básico. Estas son las características disponibles en el nivel Estándar:
* Retención de eventos más prolongada
* Más conexiones asíncronas, con un cargo por uso por encima del límite de más del número incluido
* Más de un único grupo de consumidores
* [Capture](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)

Para más información sobre los planes de tarifa, incluido Event Hubs dedicado, vea los [detalles de los precios de Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="what-are-event-hubs-throughput-units"></a>¿Qué son las unidades de procesamiento de los Centros de eventos?

Las unidades de procesamiento de Event Hubs se seleccionan explícitamente mediante el Portal de Azure clásico o las plantillas de Resource Manager para Event Hubs. Las unidades de procesamiento se aplican a todos los centros de eventos de un espacio de nombres de Event Hubs, y cada unidad de procesamiento da derecho al espacio de nombres a las siguientes funcionalidades:

* Hasta 1 MB por segundo de eventos de entrada (eventos enviados a un centro de eventos), pero no más de 1000 eventos de entrada, operaciones de administración o llamadas a la API de control por segundo.
* Hasta 2 MB por segundo de eventos de salida (eventos consumidos en un centro de eventos).
* Hasta 84 GB de almacenamiento de eventos (suficiente para el período de retención predeterminado de 24 horas).

Las unidades de procesamiento de los Centros de eventos se facturan por horas, según el número máximo de unidades seleccionadas durante la hora en cuestión. También puede [aumentar el número de unidades de rendimiento](event-hubs-auto-inflate.md) de forma automática a medida que el uso sea más frecuente.

### <a name="how-are-event-hubs-throughput-unit-limits-enforced"></a>¿Cómo se aplican los límites de unidades de rendimiento de los Centros de eventos?
Si el procesamiento total de entrada o la tasa total de eventos de entrada en todos los centros de eventos de un espacio de nombres superan el número total de unidades de procesamiento permitidas, los remitentes quedan sujetos a limitaciones y reciben errores que indican que se superó la cuota de entrada.

Si el procesamiento total de salida o la tasa total de eventos de salida en todos los centros de eventos de un espacio de nombres superan el número total de unidades de procesamiento permitidas, los receptores quedan sujetos a limitaciones y reciben errores que indican que se superó la cuota de salida. Las cuotas de entrada y de salida se aplican por separado, por lo que ningún remitente puede provocar que se ralentice el consumo de eventos ni ningún receptor puede impedir que los eventos se envíen a un centro de eventos.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-selected"></a>¿Hay un límite en el número de unidades de procesamiento que se pueden seleccionar?
Hay una cuota predeterminada de 20 unidades de procesamiento por espacio de nombres. Puede solicitar una cuota de unidades de procesamiento mayor, si rellena una incidencia de soporte técnico. Más allá del límite de 20 unidades de procesamiento, hay disponibles paquetes de 20 y 100 unidades de procesamiento. Tenga en cuenta que al usar más de 20 unidades de procesamiento, se quita la capacidad de cambiar el número de unidades de procesamiento sin rellenar una incidencia de soporte técnico.

Mediante la característica de [inflado automático](event-hubs-auto-inflate.md) se puede aumentar el número de unidades de rendimiento de forma automática a medida que el uso sea más frecuente.

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>¿Se puede usar una sola conexión AMQP para enviar y recibir en varios centros de eventos?
Sí, siempre y cuando todos los centros de eventos se encuentren en el mismo espacio de nombres.

### <a name="what-is-the-maximum-retention-period-for-events"></a>¿Cuál es el período de retención máximo para eventos?
El nivel Standard de los Centros de eventos admite actualmente un período de retención máximo de 7 días. Tenga en cuenta que los centros de eventos no están concebidos como almacén de datos permanente. Los períodos de retención superiores a 24 horas están pensados para escenarios en los que es útil volver a reproducir un flujo de eventos en los mismos sistemas; por ejemplo, para entrenar o comprobar un nuevo modelo de aprendizaje automático con datos existentes. Si necesita conservar los mensajes más de siete días, habilite [Event Hubs Capture](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) en el centro de eventos para extraer los datos de este a la cuenta de almacenamiento o la cuenta de servicio de Azure Data Lake que prefiera. Si habilita Capture, se le cobrará un cargo en función de la unidad de procesamiento comprada.

### <a name="where-is-azure-event-hubs-available"></a>¿Dónde está disponible Azure Event Hubs?
Azure Event Hubs está disponible en todas las regiones de Azure admitidas. Para obtener una lista, visite la página [Regiones de Azure](https://azure.microsoft.com/regions/).  

## <a name="best-practices"></a>Prácticas recomendadas

### <a name="how-many-partitions-do-i-need"></a>¿Cuántas particiones necesito?
Tenga en cuenta que el número de particiones de un centro de eventos no se puede modificar después de la configuración. Por lo tanto, es importante considerar cuántas particiones necesita antes de comenzar. 

Event Hubs está diseñado para permitir un lector de partición única por grupo de consumidores. En la mayoría de los casos de uso, el valor predeterminado de cuatro particiones es suficiente. Si desea escalar el procesamiento de eventos, tal vez desee agregar particiones adicionales. No hay ningún límite de procesamiento específico en una partición; pero el número total de unidades de procesamiento limita el procesamiento agregado en el espacio de nombres. A medida que aumenta el número de unidades de procesamiento en el espacio de nombres, puede que necesite particiones adicionales para permitir que los lectores simultáneos logren su propio procesamiento máximo.

Pero si tiene un modelo en el que su aplicación tiene afinidad con una partición determinada, aumentar el número de particiones puede que no suponga ventaja alguna. Para más información al respecto, vea [Disponibilidad y coherencia](event-hubs-availability-and-consistency.md).

## <a name="pricing"></a>Precios

### <a name="where-can-i-find-more-pricing-information"></a>¿Dónde puedo encontrar más información sobre precios?
Para obtener una completa información sobre los precios de los Centros de eventos, consulte los [detalles de precios de los Centros de eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>¿Hay un cargo por retener eventos de los Centros de eventos durante más de 24 horas?
El nivel Estándar de Event Hubs permite períodos de retención de mensajes superiores a 24 horas, hasta un máximo de 7 días. Si el tamaño del número total de eventos almacenados supera la asignación de almacenamiento para el número de unidades de procesamiento seleccionadas (84 GB por unidad de procesamiento), el tamaño que supere la asignación se cargará con la tarifa publicada de almacenamiento de blobs de Azure. La asignación de almacenamiento en cada unidad de procesamiento cubre todos los costos de almacenamiento de los períodos de retención de 24 horas (valor predeterminado), incluso aunque la unidad de procesamiento se consuma hasta la asignación de entrada máxima.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>¿Cómo se calcula y se cobra el tamaño de almacenamiento de los Centros de eventos?
El tamaño total de todos los eventos almacenados, incluida la sobrecarga interna de encabezados de eventos o las estructuras de almacenamiento en disco de todos los centros de eventos, se mide a lo largo del día. Al final del día, se calcula el tamaño máximo de almacenamiento. La asignación de almacenamiento diario se calcula basándose en el número mínimo de unidades de procesamiento seleccionadas durante el día (cada unidad de procesamiento ofrece una asignación de 84 GB). Si el tamaño total supera la asignación de almacenamiento diaria calculada, el exceso de almacenamiento se factura con las tarifas de almacenamiento de blobs de Azure (con la tarifa de **almacenamiento con redundancia local** ).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>¿Cómo se calculan los eventos de entrada de los Centros de eventos?
Cada evento enviado a un centro de eventos cuenta como mensaje facturable. Un *evento de entrada* se define como una unidad de datos que es menor o igual que 64 KB. Cualquier evento que tenga un tamaño menor o igual que 64 KB se considera un evento facturable. Si el evento es mayor de 64 KB, el número de eventos facturables se calcula según el tamaño del evento, en múltiplos de 64 KB. Por ejemplo, un evento de 8 KB enviado al centro de eventos se factura como un evento, pero un mensaje de 96 KB enviado al Centro de eventos se factura como dos eventos.

Los eventos consumidos en un centro de eventos, así como las operaciones de administración y las llamadas de control (como los puntos de comprobación), no se cuentan como eventos de entrada facturables, pero se acumulan hasta llegar a la asignación de unidades de procesamiento.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>¿Los cargos por conexión desacoplada se aplican a los Centros de eventos?
Los cargos de conexión se aplican solo cuando se usa el protocolo AMQP. No hay ningún cargo de conexión por el envío de eventos mediante HTTP, independientemente del número de sistemas o dispositivos emisores. Si tiene previsto usar AMQP (por ejemplo, para conseguir un flujo de eventos más eficiente o para habilitar la comunicación bidireccional en escenarios de comando y control de IoT), vea la página de [información de precios de Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/) para obtener información sobre el número de conexiones incluidas en cada nivel de servicio.

### <a name="how-is-event-hubs-capture-billed"></a>¿Cómo se factura Event Hubs Capture?
Capture se habilita cuando algún centro de eventos del espacio de nombres tiene la opción Capture habilitada. Event Hubs Capture se factura por horas y por unidad de rendimiento comprada. A medida que el número de unidades de rendimiento aumenta o disminuye, la facturación de Event Hubs Capture refleja estos cambios en incrementos de horas completas. Para más información sobre Event Hubs Capture, vea los [detalles de los precios de Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="will-i-be-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>¿Se me cobrará la cuenta de almacenamiento que seleccione para Event Hubs Capture?
Capture usa la cuenta de almacenamiento que se especifique al habilitarlo en un servicio centro de eventos. Dado que esta es su cuenta de almacenamiento, los cambios efectuados en esta configuración se facturan en su suscripción de Azure.

## <a name="quotas"></a>Cuotas

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>¿Hay alguna cuota asociada a los centros Event Hubs?
Para obtener una lista de todas las cuotas de los centros Event Hubs, consulte [Cuotas](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Solución de problemas

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>¿Cuáles son algunas de las excepciones generadas por los centros Event Hubs y sus acciones sugeridas?
Para obtener una lista de posibles excepciones de Event Hubs, consulte [Información general sobre excepciones](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Registros de diagnóstico
Event Hubs admite dos tipos de [registros de diagnóstico](event-hubs-diagnostic-logs.md): los registros de errores de Capture y los registros operativos. Ambos, se representan en JSON y se pueden activar a través de Azure Portal.

### <a name="support-and-sla"></a>SLA y soporte técnico
El soporte técnico para los Centros de eventos está disponible a través de los [foros de la comunidad](https://social.msdn.microsoft.com/forums/azure/home). Se ofrece de forma gratuita soporte técnico para la administración de suscripciones y la facturación.

Para más información sobre nuestro SLA, consulte la página [Acuerdos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/) .

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de Event Hubs, visite los vínculos siguientes:

* [Información general de Event Hubs](event-hubs-what-is-event-hubs.md)
* [Creación de un Centro de eventos](event-hubs-create.md)
* [Inflado automático de Event Hubs](event-hubs-auto-inflate.md)
