---
title: "Información general de Azure Service Health | Microsoft Docs"
description: "Información personalizada sobre cómo las aplicaciones de Azure se ven afectadas por el mantenimiento y los problemas de servicios de Azure actuales y futuros."
services: Resource health
documentationcenter: 
author: rboucher
manager: 
editor: 
ms.assetid: 
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 07/07/2017
ms.author: robb
ms.openlocfilehash: c463479b7eaee5a0548c8891dd3a20ef070dd39b
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="azure-service-health"></a>Azure Service Health
Azure Service Health proporciona información oportuna y personalizada cuando los problemas en los servicios de Azure afectan a los servicios.  También le ayuda a prepararse para el próximo mantenimiento planeado.

## <a name="service-health-events"></a>Eventos de Service Health
Service Health realiza un seguimiento de tres tipos de eventos de estado que pueden afectar a los recursos:
1. **Problemas de servicios**: problemas en los servicios de Azure que le afectan ahora mismo. 
2. **Mantenimiento planeado**: próximas acciones de mantenimiento que pueden afectar a la disponibilidad de los servicios en el futuro.  
3. **Avisos de estado**: cambios en los servicios de Azure que requieren su atención. Por ejemplo, cuando están en desuso características de Azure o si se supera una cuota de uso.

    ![Eventos de Service Health](./media/service-health-overview/azure-service-health-overview-7.png)

## <a name="get-started-with-service-health"></a>Introducción a Service Health
Para iniciar el panel de Service Health, seleccione el icono de Service Health en el panel del portal. Si previamente ha quitado el icono o si está usando el panel personalizado, busque el servicio Service Health en "Más servicios" (parte inferior izquierda del panel).
![Introducción a Service Health](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Consulta de los problemas actuales que afectan a los servicios
La vista **Problemas del servicio** muestra los problemas en curso en servicios de Azure que afectan a los recursos. Puede saber cuándo se inició el problema y qué servicios y regiones se ven afectados. También puede leer la actualización más reciente para entender lo que está haciendo Azure para resolver el problema. 
![Administrar problemas de servicios](./media/service-health-overview/azure-service-health-overview-2.png)

Elija la pestaña **Posible impacto** para ver la lista específica de sus propios recursos a los que podría afectar el problema. Puede descargar una lista CSV de estos recursos para compartirla con su equipo.
![Administrar problemas de servicios - Impacto](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>Obtención de vínculos y explicaciones descargables 
Puede obtener un vínculo para el problema para que se use el su sistema de administración de problemas. También puede descargar el archivo PDF y a veces archivos CSV para compartir con personas que no tienen acceso a Azure Portal.   
![Administrar problemas de servicios - Administración de problemas](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>Obtención de soporte técnico de Microsoft
Si el recurso se deja en mal estado, incluso después de que se resuelve el problema, póngase en contacto con el soporte técnico.  Use los vínculos de soporte técnico de la derecha de la página.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Anclaje de un mapa de estado personalizado al panel
Filtre Service Health para mostrar las suscripciones, regiones y tipos de recursos críticos para la empresa. Guardar el filtro y ancle un mapa del mundo de estado personalizado al panel del portal. 
![Mapa de estado personalizado de filtro](./media/service-health-overview/azure-service-health-overview-6a.png)
![Anclar un mapa de estado personalizado](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Configuración de alertas de Service Health
Azure Service Health se integra con Azure Monitor para enviarle alertas al usuario por correo electrónico, mensajes de texto y notificaciones webhook cuando se vean afectados recursos críticos. Configure una alerta de registro de actividad para el evento de Service Health adecuado. Enrute esa alerta a las personas adecuadas de la organización mediante grupos de acciones. Para más información, consulte [Creación de alertas de registro de actividad en notificaciones del servicio](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md).

 
