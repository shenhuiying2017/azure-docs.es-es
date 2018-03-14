---
title: "Introducción a Azure Resource Health | Microsoft Docs"
description: "Introducción a Azure Resource Health"
services: Resource health
documentationcenter: 
author: shawntabrizi
manager: 
editor: 
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 07/01/2017
ms.author: shawn.tabrizi
ms.openlocfilehash: 50a173a3d3a10ed59492b4a1d64173913f331639
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2018
---
# <a name="azure-resource-health-overview"></a>Introducción a Azure Resource Health
 
Azure Resource Health ayuda a diagnosticar aquellos casos en los que un problema de Azure afecta a los recursos y a obtener soporte técnico para resolverlo. Informa sobre el mantenimiento actual y pasado de los recursos. Y proporciona soporte técnico para ayudar a mitigar los problemas.

Mientras que [Estado de Azure](https://status.azure.com) le informa sobre problemas de servicio que afectan a un amplio conjunto de clientes de Azure, Resource Health le proporciona un panel personalizado del mantenimiento de los recursos. Resource Health muestra todas las veces que los recursos no estuvieron disponibles en el pasado debido a problemas de servicio de Azure. Así resulta fácil comprender si se infringió un Acuerdo de Nivel de Servicio. 

## <a name="resource-definition-and-health-assessment"></a>Definición de recurso y evaluación de mantenimiento
Un recurso es una instancia específica de un servicio de Azure, por ejemplo, una máquina virtual, una aplicación web o una base de datos SQL.

Resource Health se basa en las señales que emiten los distintos servicios de Azure para evaluar si el mantenimiento de un recurso es correcto o no. Si el mantenimiento de un recurso no es correcto, Resource Health analiza información adicional para determinar el origen del problema. También identifica las acciones que lleva a cabo Microsoft para corregir el problema o las acciones que usted puede realizar para solucionar la causa del problema. 

Para más información sobre cómo se evalúa el mantenimiento, revise la lista completa de tipos de recurso y comprobaciones de mantenimiento en [Azure Resource Health](resource-health-checks-resource-types.md).

## <a name="health-status"></a>Estado de mantenimiento
El mantenimiento de un recurso se muestra con uno de los siguientes estados.

### <a name="available"></a>Disponible
Un estado de **disponible** significa que el servicio no ha detectado los eventos que afectan al mantenimiento del recurso. En los casos en los que el recurso se ha recuperado de un tiempo de inactividad no planeado durante las últimas 24 horas, verá la notificación **Resuelto recientemente**.

![Estado de "Disponible" para una máquina virtual con una notificación "Resuelto recientemente"](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>No disponible
Un estado de **no disponible** significa que el servicio ha detectado un evento en curso relacionado con la plataforma o no relacionado con la plataforma que afecta al mantenimiento del recurso.

#### <a name="platform-events"></a>Eventos de plataforma
Varios componentes de la infraestructura de Azure desencadenan estos eventos. Por ejemplo, acciones programadas (como mantenimiento planeado) e incidentes inesperados (como reinicio del host no planeado).

Resource Health proporciona detalles adicionales sobre el evento y el proceso de recuperación. También permite ponerse en contacto con soporte técnico, incluso si no tiene un contrato de soporte técnico de Microsoft activo.

![Estado de "No disponible" en una máquina virtual debido a un evento de plataforma](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Eventos no relacionados con la plataforma
Los eventos no relacionados con la plataforma se desencadenan por las acciones de los usuarios. Por ejemplo, detener una máquina virtual o alcanzar el número máximo de conexiones a una instancia de Redis Cache.

![Estado de "No disponible" en una máquina virtual debido a un evento no relacionado con la plataforma](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Desconocido
Este estado de mantenimiento indica que Resource Health no ha recibido información sobre este recurso durante más de 10 minutos. Si bien este estado no es una indicación definitiva del estado del recurso, es un punto de datos importante en el proceso de solución de problemas.

Si el recurso se ejecuta según lo esperado, el estado del recurso cambia a **Disponible** al cabo de unos minutos.

Si tiene problemas con el recurso, el estado de mantenimiento **Desconocido** podría sugerir que un evento de la plataforma afecta al recurso.

![Estado de "Desconocido" en una máquina virtual](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Degradado
Este estado de mantenimiento indica que el recurso ha detectado una pérdida de rendimiento, aunque sigue estando disponible para su uso.
Los distintos recursos tienen sus propios criterios respecto a cuándo especifican que un recurso está degradado.

![Estado de "Degradado" en una máquina virtual](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Informe de un estado incorrecto
Si cree que el estado de mantenimiento actual es incorrecto, puede hacérnoslo saber si selecciona **Report incorrect health status** (Notificar estado de mantenimiento incorrecto). En aquellos casos en que se vea afectado por un problema de Azure, se recomienda que se ponga en contacto con el soporte técnico de Resource Health. 

![Cuadro para enviar información sobre un estado incorrecto](./media/resource-health-overview/incorrect-status.png)

## <a name="historical-information"></a>Información histórica
Puede acceder al historial de mantenimiento de hasta 14 días si selecciona **View History** (Ver historial) en Resource Health. 

![Lista de eventos de Resource Health durante las últimas dos semanas](./media/resource-health-overview/history-blade.png)

## <a name="getting-started"></a>Introducción
Para abrir Resource Health para un recurso:
1.  Inicie sesión en el Portal de Azure.
2.  Vaya a su sitio.
3.  En el menú de recursos del panel izquierdo, seleccione **Resource Health**.

![Abrir Resource Health desde la vista de recursos](./media/resource-health-overview/from-resource-blade.png)

Otra manera de acceder a Resource Health es seleccionar **Todos los servicios** y escribir **resource health** en el cuadro de texto de filtro. En el panel **Ayuda y soporte técnico**, seleccione [Resource Health](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Abrir Resource Health desde "Todos los servicios"](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Pasos siguientes

Consulte estos recursos para más información sobre Resource Health:
-  [Tipos de recursos y comprobaciones de estado en Azure Resource Health](resource-health-checks-resource-types.md)
-  [Preguntas más frecuentes sobre Azure Resource Health](resource-health-faq.md)




