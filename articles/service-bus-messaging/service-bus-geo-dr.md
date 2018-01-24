---
title: "Recuperación ante desastres con localización geográfica de Azure Service Bus | Microsoft Docs"
description: "Cómo usar regiones geográficas para conmutar por error y llevar a cabo una recuperación ante desastres en Azure Service Bus"
services: service-bus-messaging
documentationcenter: 
author: christianwolf42
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: sethm
ms.openlocfilehash: fdeb9ba55fc8eade95f6fca88f47dd12aa18a480
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/16/2017
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Recuperación ante desastres con localización geográfica de Azure Service Bus

Cuando hay regiones de Azure completas o centros de datos (si no se utilizan [zonas de disponibilidad](../availability-zones/az-overview.md)) que experimentan un tiempo de inactividad, es crucial que el procesamiento de datos siga funcionando en otra región o centro de datos. De esta forma, la *recuperación ante desastres con localización geográfica* y la *replicación geográfica* son características importantes para cualquier empresa. Azure Service Bus admite tanto la recuperación ante desastres con localización geográfica como la replicación geográfica, en el nivel de espacio de nombres. 

La característica de recuperación ante desastres con localización geográfica está disponible globalmente para la SKU Premium de Service Bus. 

## <a name="outages-and-disasters"></a>Interrupciones y desastres

Es importante tener en cuenta la distinción entre "interrupciones" y "desastres". Una *interrupción* es la falta de disponibilidad temporal de Azure Service Bus y puede afectar a algunos componentes del servicio, como un almacén de mensajes o incluso todo el centro de datos. Sin embargo, una vez corregido el problema, Service Bus vuelve a estar disponible. Normalmente, una interrupción no provoca la pérdida de mensajes ni otros datos. Un ejemplo de una interrupción de este tipo podría ser un error de corriente en el centro de datos. Algunas interrupciones son solo breves pérdidas de conexión debido a problemas transitorios o de red. 

Un *desastre* se define como la pérdida permanente o a largo plazo de un clúster, una región de Azure o un centro de datos de Service Bus. La región o el centro de datos no volverá necesariamente a estar disponible, o puede que esté fuera de servicio durante horas o días. Algunos ejemplos de esos desastres son los incendios, las inundaciones o los terremotos. Un desastre que se convierte en permanente podría provocar la pérdida de algunos mensajes, eventos u otros datos. Sin embargo, en la mayoría de los casos, no debe producirse una pérdida de datos y se pueden recuperar los mensajes una vez que se realiza la copia de seguridad del centro de datos.

La característica de recuperación ante desastres con localización geográfica de Azure Service Bus es una solución de recuperación ante desastres. Los conceptos y el flujo de trabajo descritos en este artículo se aplican a situaciones catastróficas y no a interrupciones transitorias o temporales. Para obtener una explicación detallada de la recuperación ante desastres en Microsoft Azure, consulte [este artículo](/azure/architecture/resiliency/disaster-recovery-azure-applications).   

## <a name="basic-concepts-and-terms"></a>Términos y conceptos básicos

La característica de recuperación ante desastres implementa la recuperación ante desastres de metadatos y depende de espacios de nombres de recuperación ante desastres principales y secundarios. Tenga en cuenta que la característica de recuperación ante desastres con localización geográfica solo está disponible para [SKU Premium](service-bus-premium-messaging.md). No es necesario realizar ningún cambio de la cadena de conexión, ya que la conexión se realiza a través de un alias.

Los siguientes términos se utilizan en este artículo:

-  *Alias*: el nombre para una configuración de recuperación ante desastres que ha configurado. El alias proporciona una sola cadena de conexión estable de nombre de dominio completo (FQDN). Las aplicaciones usan esta cadena de conexión de alias para conectarse a un espacio de nombres. 

-  *Espacio de nombres principal o secundario*: los espacios de nombres que corresponden al alias. El espacio de nombres principal es "activo" y recibe mensajes (puede ser un espacio de nombres ya existente o uno nuevo). El espacio de nombres secundario es "pasivo" y no recibe mensajes. Los metadatos entre ambos están sincronizados, por lo que ambos pueden aceptar sin problemas mensajes sin ningún cambio de código de la aplicación o cadena de conexión. Para asegurarse de que solo el espacio de nombres activo recibe mensajes, tiene que utilizar el alias. 

-  *Metadatos*: entidades como colas, temas y suscripciones, y sus propiedades del servicio que están asociados con el espacio de nombres. Tenga en cuenta que solo las entidades y sus valores se replican automáticamente. Los mensajes no se replican. 

-  *Conmutación por error*: El proceso de activación del espacio de nombres secundario.

## <a name="setup-and-failover-flow"></a>Flujo de conmutación por error y configuración

La siguiente sección contiene información general del proceso de conmutación por error y explica cómo configurar la conmutación por error inicial. 

![1][]

### <a name="setup"></a>Configuración

En primer lugar cree un espacio de nombres principal o use uno ya existente, y un nuevo espacio de nombres secundario, luego emparéjelos. Este emparejamiento le proporciona un alias que puede usar para conectarse. Al usar un alias, no es necesario que cambie las cadenas de conexión. Solo pueden agregarse nuevos espacios de nombres al emparejamiento de la conmutación por error. Por último, debe agregar alguna supervisión para detectar si es necesario realizar una conmutación por error. En la mayoría de los casos, el servicio forma parte de un ecosistema mayor, por lo tanto las conmutaciones por error automáticas raramente son posibles, ya que a menudo las conmutaciones por error tienen que realizarse en sincronía con el subsistema o infraestructura restantes.

### <a name="example"></a>Ejemplo

En un ejemplo de este escenario, se considera una solución de punto de venta (POS) que emite mensajes o eventos. Service Bus pasa esos eventos a alguna solución de asignación o de repetición de formateo, que a continuación reenvía los datos asignados a otro sistema para un procesamiento adicional. En ese momento, todos estos sistemas se pueden hospedar en la misma región de Azure. La decisión sobre cuándo y en qué parte se realizará la conmutación por error depende del flujo de datos en su infraestructura. 

Puede automatizar la conmutación por error con la supervisión de sistemas, o con soluciones de supervisión personalizadas. Sin embargo, dicha automatización necesita planeamiento y trabajo extra que se encuentran fuera del ámbito de este artículo.

### <a name="failover-flow"></a>Flujo de conmutación por error

Si inicia la conmutación por error, se requieren dos pasos:

1. En caso de otra interrupción, tiene que poder volver a realizar la conmutación por error. Por lo tanto, configure un segundo espacio de nombres pasivo y actualice el emparejamiento. 

2. Extraiga mensajes del espacio de nombres anteriormente principal una vez que vuelva a estar disponible. Después de eso, utilice ese espacio de nombres para la mensajería regular fuera de la configuración de recuperación con localización geográfica, o elimine el espacio de nombres principal antiguo.

> [!NOTE]
> Se admite solo la semántica de conmutación de reenvío. En este escenario, se realiza la conmutación por error y, a continuación, se vuelve a emparejar con un nuevo espacio de nombres. No se admite la conmutación por recuperación, por ejemplo en un clúster de SQL. 

![2][]

## <a name="management"></a>Administración

Si ha cometido algún error; por ejemplo, ha emparejado regiones incorrectas durante la configuración inicial, puede interrumpir el emparejamiento de los dos espacios de nombres en cualquier momento. Si desea usar los espacios de nombres emparejados como espacios de nombres normales, elimine el alias.

## <a name="use-existing-namespace-as-alias"></a>Uso de espacio de nombres existente como alias

Si tiene un escenario en el que no se pueden cambiar las conexiones de productores y consumidores, puede reutilizar el nombre del espacio de nombres como nombre de alias. Consulte el [código de ejemplo en GitHub aquí](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>Muestras

Los [ejemplos en GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2) muestran cómo configurar e iniciar una conmutación por error. Estos ejemplos demuestran los conceptos siguientes:

- Configuración necesaria en Azure Active Directory para usar Azure Resource Manager con Service Bus. 
- Pasos necesarios para ejecutar el código de ejemplo. 
- Envío y recepción desde el espacio de nombres principal actual. 
- Uso de un espacio de nombres existente como alias.

## <a name="considerations"></a>Consideraciones

Tenga en cuenta y recuerde las siguientes consideraciones para esta versión:

1. En el planeamiento de la conmutación por error, también debe considerar el factor de tiempo. Por ejemplo, si se pierde la conectividad durante más de 15 a 20 minutos, puede decidir iniciar la conmutación por error. 
 
2. El hecho de que no se replican datos significa que las sesiones activas en la actualidad no se replican. Además, la detección de duplicados y mensajes programados puede no funcionar. Funcionarán las nuevas sesiones, los mensajes programados y los duplicados nuevos. 

3. Conmutar por error una compleja infraestructura distribuida debe [ensayarse](/azure/architecture/resiliency/disaster-recovery-azure-applications#disaster-simulation) al menos una vez. 

4. La sincronización de entidades puede tardar algún tiempo, aproximadamente 50-100 entidades por minuto. Las suscripciones y reglas también cuentan como entidades. 

## <a name="next-steps"></a>pasos siguientes

- Consulte la [referencia de la API de REST](/rest/api/servicebus/disasterrecoveryconfigs) de la recuperación ante desastres con localización geográfica aquí.
- Ejecute el [ejemplo de recuperación](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2) ante desastres con localización geográfica en GitHub.
- Consulte el [ejemplo en el que se envían mensajes a un alias](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) de la recuperación ante desastres con localización geográfica.

Para más información sobre la mensajería de Service Bus, consulte los siguientes artículos:

* [Elementos fundamentales de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Colas, temas y suscripciones de Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introducción a las colas de Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Uso de temas y suscripciones de Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [API DE REST](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geo1.png
[2]: ./media/service-bus-geo-dr/geo2.png
