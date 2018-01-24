---
title: "Recuperación ante desastres con localización geográfica de Azure Event Hubs | Microsoft Docs"
description: "Cómo usar regiones geográficas para conmutar por error y llevar a cabo una recuperación ante desastres en Azure Event Hubs"
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: sethm
ms.openlocfilehash: 237b0639be75e12cff56f40ac76426aba7a8a701
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/16/2017
---
# <a name="azure-event-hubs-geo-disaster-recovery"></a>Recuperación ante desastres con localización geográfica de Azure Event Hubs

Cuando las regiones o los centros de datos enteros de Azure (si no se usan [zonas de disponibilidad](../availability-zones/az-overview.md)) experimentan tiempo de inactividad, es muy importante que el procesamiento de datos siga funcionando en una región o un centro de datos diferente. De esta forma, la *recuperación ante desastres con localización geográfica* y la *replicación geográfica* son características importantes para cualquier empresa. Azure Event Hubs admite tanto la recuperación ante desastres con localización geográfica como la replicación geográfica, en el nivel de espacio de nombres. 

La característica de recuperación ante desastres con localización geográfica está disponible globalmente para el SKU estándar de Event Hubs.

## <a name="outages-and-disasters"></a>Interrupciones y desastres

Es importante advertir la distinción entre "interrupciones" y "desastres". Una *interrupción* es la falta de disponibilidad temporal de Azure Event Hubs y puede afectar a algunos componentes del servicio, como un almacén de mensajes o incluso todo el centro de datos. Sin embargo, después de corregir el problema, Event Hubs está de nuevo disponible. Normalmente, una interrupción no provoca la pérdida de mensajes ni otros datos. Un ejemplo de una interrupción de este tipo podría ser un error de corriente en el centro de datos. Algunas interrupciones son solo pérdidas de conexión de corta duración debidas a problemas de red o transitorios. 

Un *desastre* se define como la pérdida permanente o a largo plazo de un clúster de Event Hubs, una región de Azure o un centro de datos. Puede que la región o el centro de datos no vuelvan a estar disponibles, o que estén inactivos durante horas o días. Algunos ejemplos de esos desastres son los incendios, las inundaciones o los terremotos. Un desastre que se convierte en permanente podría provocar la pérdida de algunos mensajes, eventos u otros datos. Sin embargo, en la mayoría de los casos, no debe producirse una pérdida de datos y se pueden recuperar los mensajes una vez que se realiza la copia de seguridad del centro de datos.

La característica de recuperación ante desastres con localización geográfica de Azure Event Hubs es una solución de recuperación ante desastres. Los conceptos y el flujo de trabajo descritos en este artículo se aplican a situaciones catastróficas y no a interrupciones transitorias o temporales. Para obtener una explicación detallada de la recuperación ante desastres en Microsoft Azure, consulte [este artículo](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Términos y conceptos básicos

La característica de recuperación ante desastres implementa la recuperación ante desastres de metadatos y depende de espacios de nombres de recuperación ante desastres principales y secundarios. Tenga en cuenta que la característica de recuperación ante desastres con localización geográfica solo está disponible para las [SKU estándar](https://azure.microsoft.com/pricing/details/event-hubs/). No es necesario realizar ningún cambio de la cadena de conexión, ya que la conexión se realiza a través de un alias.

Los siguientes términos se utilizan en este artículo:

-  *Alias*: el nombre de una configuración de recuperación ante desastres que ha configurado. El alias proporciona una sola cadena de conexión estable de nombre de dominio completo (FQDN). Las aplicaciones usan esta cadena de conexión de alias para conectarse a un espacio de nombres. 

-  *Espacio de nombres principal o secundario*: los espacios de nombres que corresponden al alias. El espacio de nombres principal es "activo" y recibe los mensajes (puede ser un espacio de nombres nuevo o existente). El espacio de nombres secundario es "pasivo" y no recibe mensajes. Los metadatos entre ambos están sincronizados, por lo que ambos pueden aceptar sin problemas mensajes sin ningún cambio en el código de la aplicación o la cadena de conexión. Para asegurarse de que solo el espacio de nombres activo recibe mensajes, se debe usar el alias. 

-  *Metadatos*: entidades como centros de eventos y grupos de consumidores; y sus propiedades del servicio que están asociadas con el espacio de nombres. Tenga en cuenta que solo las entidades y sus valores de configuración se replican automáticamente. No se replican los mensajes ni los eventos. 

-  *Conmutación por error*: El proceso de activación del espacio de nombres secundario.

## <a name="setup-and-failover-flow"></a>Configuración y flujo de conmutación por error

La siguiente sección contiene información general del proceso de conmutación por error y en ella se explica cómo configurar la conmutación por error inicial. 

![1][]

### <a name="setup"></a>Configuración

Primero creará y usará un espacio de nombres principal existente y un nuevo espacio de nombres secundario, y luego emparejará los dos. Este emparejamiento le proporciona un alias que puede usar para conectarse. Al usar un alias, no es necesario que cambie las cadenas de conexión. Solo pueden agregarse nuevos espacios de nombres al emparejamiento de la conmutación por error. Por último, debe agregar alguna supervisión para detectar si se necesita una conmutación por error. En la mayoría de los casos, el servicio forma parte de un gran ecosistema, así que las conmutaciones por error automáticas rara vez son posibles, dado que con mucha frecuencia las conmutaciones por error se deben realizar en sincronización con el resto del subsistema o de la infraestructura.

### <a name="example"></a>Ejemplo

En un ejemplo de este escenario, se considere una solución de punto de venta (POS) que emite mensajes o eventos. Event Hubs pasa esos eventos a alguna solución de asignación o formato, que reenvía los datos asignados a otros sistema para continuar el procesamiento. En ese momento, todos estos sistemas podrían estar hospedados en la misma región de Azure. La decisión sobre cuándo y qué parte conmutar por error depende del flujo de datos de su infraestructura. 

Puede automatizar la conmutación por error con sistemas de supervisión o con soluciones de supervisión creadas de forma personalizada. Sin embargo, dicha automatización implica planeamiento y trabajo adicionales, lo cual escapa del ámbito de este artículo.

### <a name="failover-flow"></a>Flujo de conmutación por error

Si inicia la conmutación por error, se requieren dos pasos:

1. En caso de otra interrupción, querrá poder realizar de nuevo la conmutación por error. Por lo tanto, configure un segundo espacio de nombres pasivo y actualice el emparejamiento. 

2. Extraiga mensajes del espacio de nombres principal anterior una vez que vuelva a estar disponible. A continuación, use ese espacio de nombres para la mensajería normal fuera de la configuración de la recuperación con localización geográfica, o bien elimine el espacio de nombres principal antiguo.

> [!NOTE]
> Solo se admite semántica de conmutación hacia delante. En este escenario, realizará una conmutación por error y volverá a realizar el emparejamiento con un nuevo espacio de nombres. No se admite la conmutación por recuperación, por ejemplo, en un clúster de SQL. 

![2][]

## <a name="management"></a>Administración

Si cometió algún error; por ejemplo, emparejó las regiones incorrectas durante la configuración inicial, puede interrumpir el emparejamiento de los dos espacios de nombres en cualquier momento. Si quiere usar los espacios de nombres emparejados como espacios de nombres normales, elimine el alias.

## <a name="samples"></a>Muestras

En el [ejemplo de GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient) se muestra cómo configurar e iniciar una conmutación por error. En este ejemplo se demuestran los siguientes conceptos:

- La configuración necesaria en Azure Active Directory para usar Azure Resource Manager con Event Hubs. 
- Los pasos necesarios para ejecutar el código de ejemplo. 
- Envío y recepción desde el espacio de nombres principal actual. 

## <a name="considerations"></a>Consideraciones

Tenga en cuenta los siguientes aspectos con esta versión:

1. En el planeamiento de conmutación por error, también debe considerar el factor tiempo. Por ejemplo, si perdió conectividad durante más de 15 o 20 minutos, podría decidir iniciar la conmutación por error. 
 
2. El hecho de que no se replique ningún dato significa que las sesiones actualmente activas no se replican. Además, es posible que la detección de duplicados y los mensajes programados no funcionen. Funcionarán las nuevas sesiones, los nuevos mensajes programados y los nuevos duplicados. 

3. La conmutación por error de una infraestructura distribuida compleja se debe [ensayar](/azure/architecture/resiliency/disaster-recovery-azure-applications#disaster-simulation) al menos una vez. 

4. La sincronización de entidades puede llevar algún tiempo, unas 50-100 entidades por minuto.

## <a name="next-steps"></a>pasos siguientes

* El [ejemplo en GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient) le guía a través de un flujo de trabajo simple que crea un emparejamiento geográfico e inicia una conmutación por error para un escenario de recuperación ante desastres.
* La [referencia de la API de REST](/rest/api/eventhub/disasterrecoveryconfigs) describe las API para llevar a cabo la configuración de recuperación de desastres con localización geográfica.

Para obtener más información acerca de Event Hubs, visite los vínculos siguientes:

* Empiece por un [tutorial de Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Preguntas más frecuentes sobre Event Hubs](event-hubs-faq.md)
* [Aplicaciones de ejemplo que usan Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png