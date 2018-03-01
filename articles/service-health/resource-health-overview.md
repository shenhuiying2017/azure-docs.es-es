---
title: "Información general sobre Estado de los recursos de Azure | Microsoft Docs"
description: "Estado de los recursos de Azure. Información general"
services: Resource health
documentationcenter: 
author: BernardoAMunoz
manager: 
editor: 
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 07/01/2017
ms.author: BernardoAMunoz
ms.openlocfilehash: 9912911d6ed43a70a7a0f9316079d8f66d063f64
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="azure-resource-health-overview"></a>Información general sobre Azure Resource Health
 
Resource Health le ayuda a diagnosticar y obtener soporte técnico cuando un problema de Azure afecta a sus recursos. Le informa acerca del mantenimiento actual y pasado de los recursos y le ayuda a mitigar los problemas. Resource Health le proporciona soporte técnico si necesita ayuda con los problemas de los servicios de Azure.

Mientras que [Estado de Azure](https://status.azure.com) le informa sobre problemas de servicio que afectan a un amplio conjunto de clientes de Azure, Resource Health le proporciona un panel personalizado del estado de los recursos. Resource Health muestra todas las veces que los recursos no estaban disponibles en el pasado debido a problemas del servicio de Azure, de forma que sea sencillo saber si se infringe un SLA. 

## <a name="what-is-considered-a-resource-and-how-does-resource-health-decides-if-a-resource-is-healthy-or-not"></a>¿Qué se considera que es un recurso y cómo Resource Health decide si el estado de un recurso es correcto?
Un recurso es una instancia específica de un servicio de Azure, por ejemplo: una máquina virtual, una aplicación web o una base de datos SQL.

Resource Health se basa en las señales que emiten los distintos servicios de Azure para evaluar si el estado de un recurso es correcto o no. Si el estado de un recurso no es correcto, Resource Health analiza información adicional para determinar el origen del problema. También identifica las acciones que lleva a cabo Microsoft para corregir el problema o las acciones que usted puede realizar para solucionar la causa del problema. 

Revise la lista completa de tipos de recurso y comprobaciones de estado en [Azure Resource Health](resource-health-checks-resource-types.md) para detalles adicionales sobre cómo se evalúa el estado de mantenimiento.

## <a name="health-status-provided-by-resource-health"></a>Estado de mantenimiento proporcionado por Resource Health
Un recurso tiene uno de los siguientes estados:

### <a name="available"></a>Disponible
El servicio no ha detectado ningún evento que afecte al mantenimiento del recurso. En los casos en que el recurso se recuperó de un tiempo de inactividad no planeado durante las últimas 24 horas, verá la notificación de que se **recuperó recientemente**.

![Máquina virtual disponible en Resource Health](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>No disponible
El servicio detectó un evento de plataforma o no de plataforma en curso que afecta el estado del recurso.

#### <a name="platform-events"></a>Eventos de plataforma
Varios componentes de la infraestructura de Azure desencadenan estos eventos. Por ejemplo, acciones programadas (por ejemplo, mantenimiento planeado) e incidentes inesperados (por ejemplo, reinicio del host no planeado).

Resource Health proporciona detalles adicionales sobre el evento y el proceso de recuperación. También permite ponerse en contacto con soporte técnico, incluso si no tiene un contrato de soporte técnico de Microsoft activo.

![Máquina virtual no disponible en Resource Health debido a un evento de plataforma](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Eventos no de plataforma
Estos eventos se desencadenan debido a las acciones realizadas por los usuarios. Por ejemplo, detener una máquina virtual o alcanzar el número máximo de conexiones a una instancia de Redis Cache.

![Máquina virtual no disponible en Resource Health debido a un evento no de plataforma](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Desconocido
Este estado de mantenimiento indica que Resource Health no ha recibido información sobre este recurso durante más de 10 minutos. Si bien este estado no es una indicación definitiva del estado del recurso, es un punto de datos importante en el proceso de solución de problemas:
* Si el recurso se ejecuta según lo esperado, el estado del recurso cambiará a Disponible al cabo de unos minutos.
* Si tiene problemas con el recurso, el estado de mantenimiento Desconocido podría sugerir que un evento de la plataforma afecta al recurso.

![Máquina virtual Desconocida de Resource Health](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Degradado
Este estado de mantenimiento indica que el recurso ha detectado una pérdida de rendimiento, aunque sigue estando disponible para su uso.
Los distintos recursos tienen sus propios criterios respecto a cuándo especifican que un recurso está degradado.

![Máquina virtual degradada de Resource Health](./media/resource-health-overview/degraded.png)

## <a name="report-an-incorrect-status"></a>Informe de un estado incorrecto
Si en algún momento cree que el estado de mantenimiento actual es incorrecto, puede hacérnoslo saber si hace clic en **Informe de estado de mantenimiento incorrecto**. En aquellos casos en que se vea afectado por un problema de Azure, se recomienda que se ponga en contacto con el soporte técnico de Resource Health. 

![Informe de estado incorrecto en Resource Health](./media/resource-health-overview/incorrect-status.png)

## <a name="historical-information"></a>Información histórica
Puede acceder al historial de mantenimiento de hasta 14 días si hace clic en **Ver historial** en Resource Health. 

![Historial de informes en Resource Health](./media/resource-health-overview/history-blade.png)

## <a name="getting-started"></a>Introducción
Para abrir Resource Health para un recurso
1.  Inicie sesión en Azure Portal.
2.  Vaya al recurso.
3.  En el menú de recursos que se encuentra en el lado izquierdo, haga clic en **Resource Health**.

![Apertura de Resource Health desde la vista de recursos](./media/resource-health-overview/from-resource-blade.png)

Otra forma de acceder a Resource Health es hacer clic en **Todos los servicios** y escribir **Resource Health** en el cuadro de texto de filtro para abrir la hoja **Ayuda y soporte técnico**. Por último, haga clic en [**Resource Health**](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Apertura de Resource Health desde Todos los servicios](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>pasos siguientes

Revise estos recursos para más información sobre Resource Health:
-  [Tipos de recurso y comprobaciones de estado en Azure Resource Health](resource-health-checks-resource-types.md)
-  [Preguntas más frecuentes de Azure Resource Health](resource-health-faq.md)




