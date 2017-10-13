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
ms.openlocfilehash: 040d58a81a9b41fe660e4276d698bf884f90bb6c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-resource-health-overview"></a>Información general sobre Azure Resource Health
 
Resource Health le ayuda a diagnosticar y obtener soporte técnico cuando un problema de Azure afecta a sus recursos. Le informa acerca del mantenimiento actual y pasado de los recursos y le ayuda a mitigar los problemas. Resource Health le proporciona soporte técnico si necesita ayuda con los problemas de los servicios de Azure.

Mientras que [Estado de Azure](https://status.azure.com) le informa sobre problemas de servicio que afectan a un amplio conjunto de clientes de Azure, Resource Health le proporciona un panel personalizado del estado de los recursos. Resource Health muestra todas las veces que los recursos no estuvieron disponibles en el pasado debido a problemas de servicio de Azure. Esto facilita comprender si se infringió un Acuerdo de Nivel de Servicio. 

## <a name="what-is-considered-a-resource-and-how-does-resource-health-decides-if-a-resource-is-healthy-or-not"></a>¿Qué se considera que es un recurso y cómo Resource Health decide si el estado de un recurso es correcto?
Un recurso es una instancia de un tipo de recurso que un servicio de Azure ofrece a través de Azure Resource Manager, por ejemplo: una máquina virtual, una aplicación web o una base de datos de SQL Database.

Resource Health se basa en las señales que emiten los distintos servicios de Azure para evaluar si el estado de un recurso es correcto o no. Si el estado de un recurso no es correcto, Resource Health analiza información adicional para determinar el origen del problema. También identifica las acciones que lleva a cabo Microsoft para corregir el problema o las acciones que usted puede realizar para solucionar la causa del problema. 

Revise la lista completa de tipos de recurso y comprobaciones de estado en [Azure Resource Health](resource-health-checks-resource-types.md) para detalles adicionales sobre cómo se evalúa el estado de mantenimiento.

## <a name="health-status-provided-by-resource-health"></a>Estado de mantenimiento proporcionado por Resource Health
Un recurso tiene uno de los siguientes estados:

### <a name="available"></a>Disponible
El servicio no ha detectado ningún evento que afecte el estado del recurso. En los casos en que el recurso se recuperó de un tiempo de inactividad no planeado durante las últimas 24 horas, verá la notificación de que se **recuperó recientemente**.

![Máquina virtual disponible en Resource Health](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>No disponible
El servicio detectó un evento de plataforma o no de plataforma en curso que afecta el estado del recurso.

#### <a name="platform-events"></a>Eventos de plataforma
Estos eventos los desencadenan varios componentes de la infraestructura de Azure e incluyen tanto acciones programadas como mantenimiento planeado o incidentes inesperados, como un reinicio no planeado del host.

Resource Health proporciona detalles adicionales sobre el evento y el proceso de recuperación y le permite ponerse en contacto con el soporte técnico incluso si no tiene ningún contrato de Soporte técnico de Microsoft activo.

![Máquina virtual no disponible en Resource Health debido a un evento de plataforma](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Eventos no de plataforma
Estos eventos los desencadenan las acciones que realizan los usuarios; por ejemplo, detener una máquina virtual o llegar a la cantidad máxima de conexiones a Redis Cache.

![Máquina virtual no disponible en Resource Health debido a un evento no de plataforma](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Desconocido
Este estado de mantenimiento indica que Resource Health no ha recibido información sobre este recurso durante más de 10 minutos. Si bien este estado no es una indicación definitiva del estado del recurso, es un punto de datos importante en el proceso de solución de problemas:
* Si el recurso se ejecuta según lo esperado, el estado del recurso cambiará a Disponible después de unos minutos.
* Si tiene problemas con el recurso, el estado de mantenimiento Desconocido podría sugerir que un evento de la plataforma afecta al recurso.

![Máquina virtual Desconocida de Resource Health](./media/resource-health-overview/Unknown.png)

## <a name="report-an-incorrect-status"></a>Informe de un estado incorrecto
Si en algún momento cree que el estado de mantenimiento actual es incorrecto, puede hacérnoslo saber si hace clic en **Informe de estado de mantenimiento incorrecto**. En los casos en que lo afecta un problema de Azure, se recomienda que se ponga en contacto con el soporte técnico desde la hoja Resource Health. 

![Informe de estado incorrecto en Resource Health](./media/resource-health-overview/incorrect-status.png)

## <a name="historical-information"></a>Información histórica
Puede tener acceso a los datos históricos de mantenimiento de hasta 14 días; para ello, haga clic en **Ver el historial** en la hoja Resource Health. 

![Historial de informes en Resource Health](./media/resource-health-overview/history-blade.png)

## <a name="getting-started"></a>Introducción
Para abrir Resource Health para un recurso
1.  Inicie sesión en Azure Portal.
2.  Vaya al recurso.
3.  En el menú de recursos que se encuentra en el lado izquierdo, haga clic en **Resource Health**.

![Apertura de Resource Health desde la hoja Recurso](./media/resource-health-overview/from-resource-blade.png)

También puede tener acceso a Resource Health con un clic en **Más servicios** y escribiendo **Resource Health** en el cuadro de texto de filtro para abrir la hoja **Ayuda y soporte técnico**. Por último, haga clic en [**Resource Health**](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Apertura de Resource Health desde Más servicios](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Pasos siguientes

Revise estos recursos para más información sobre Resource Health:
-  [Tipos de recurso y comprobaciones de estado en Azure Resource Health](resource-health-checks-resource-types.md)
-  [Preguntas más frecuentes de Azure Resource Health](resource-health-faq.md)




