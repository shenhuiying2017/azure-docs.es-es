---
title: "Introducción a Azure Advisor | Microsoft Docs"
description: Utilice Azure Advisor para optimizar las implementaciones de Azure.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 96925f251cf4984a11516a962740e19a7b9589dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-advisor"></a>Introducción a Azure Advisor

Obtenga información sobre Azure Advisor y sus funcionalidades clave, y obtenga respuesta a las preguntas más frecuentes.

## <a name="what-is-advisor"></a>¿Qué es Advisor?
Advisor es un consultor personalizado en la nube que lo ayuda a seguir procedimientos recomendados para optimizar las implementaciones de Azure. Analiza la configuración de recursos y la telemetría de uso, y recomienda soluciones que pueden ayudar a mejoran la rentabilidad, el rendimiento, la alta disponibilidad y la seguridad de los recursos de Azure.

Con Advisor, puede:
* Obtener sugerencias de procedimientos recomendados proactivas, prácticas y personalizadas, 
* Mejorar el rendimiento, la seguridad y la alta disponibilidad de los recursos, al mismo tiempo que identifica oportunidades para reducir el gasto general de Azure, y
* Obtener recomendaciones con acciones propuestas en línea.

Puede acceder a Advisor mediante [Azure Portal](https://aka.ms/azureadvisordashboard). Inicie sesión en [Azure Portal](https://portal.azure.com), seleccione **Examinar** y desplácese a **Azure Advisor**. El panel de Advisor muestra recomendaciones personalizadas de la suscripción seleccionada. 

Las recomendaciones se dividen en cuatro categorías: 

* **Alta disponibilidad**: lo ayuda a garantizar y mejorar la continuidad de las aplicaciones empresariales críticas. Para obtener más información, consulte las [recomendaciones sobre alta disponibilidad de Advisor](advisor-high-availability-recommendations.md).

* **Seguridad**: lo ayuda a detectar amenazas y vulnerabilidades que podrían dar lugar a infracciones de seguridad. Para obtener más información, consulte las [recomendaciones sobre seguridad de Advisor](advisor-security-recommendations.md).

* **Rendimiento**: lo ayuda a mejorar la velocidad de las aplicaciones. Para obtener más información, consulte las [recomendaciones sobre rendimiento de Advisor](advisor-performance-recommendations.md).

* **Costo**: lo ayuda a optimizar y reducir el gasto general de Azure. Para obtener más información, consulte las [recomendaciones sobre el costo de Advisor](advisor-cost-recommendations.md).

  ![Tipos de recomendaciones de Advisor](./media/advisor-overview/advisor-all-tab-examples.png)

> [!NOTE]
> Para acceder a las recomendaciones de Advisor, primero debe *registrar su suscripción* en dicha solución. Una suscripción se registra cuando el *propietario de esta* inicia el panel de Advisor y hace clic en el botón **Obtener recomendaciones**. Esta operación *solo se realiza una vez*. Una vez registrada la suscripción, puede acceder a las recomendaciones de Advisor como *Propietario*, *Contribuidor* o *Lector* para una suscripción, un grupo de recursos o un recurso concreto.

Puede hacer clic en una recomendación para saber más sobre ella. También puede aprender más sobre las acciones que puede llevar a cabo para aprovechar las ventajas de una oportunidad o resolver un problema. 

Advisor ofrece recomendaciones con acciones insertadas o vínculos de documentación. Al hacer clic en una acción insertada, se inicia un "viaje de usuario guiado" para implementarla. Al hacer clic en un vínculo de documentación, se le remite a documentación que describe cómo llevar a cabo la acción de forma manual. 

Advisor actualiza las recomendaciones cada hora. Si no desea realizar de inmediato una acción basada en una recomendación, puede posponerla durante un período de tiempo o descartarla. 

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="how-do-i-access-advisor"></a>¿Cómo se accede a Advisor?
Puede acceder a Advisor mediante [Azure Portal](https://aka.ms/azureadvisordashboard). Inicie sesión en [Azure Portal](https://portal.azure.com), seleccione **Examinar** y desplácese a **Azure Advisor**. El panel de Advisor muestra recomendaciones personalizadas de la suscripción seleccionada. 

También puede ver las recomendaciones de Advisor a través de la hoja de recursos de la máquina virtual. Seleccione una máquina virtual y después desplácese a las recomendaciones de Advisor en el menú. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>¿Qué permisos son necesarios para acceder a Advisor?

Para acceder a las recomendaciones de Advisor, primero debe *registrar su suscripción* en dicha solución. Una suscripción se registra cuando el *propietario de esta* inicia el panel de Advisor y hace clic en el botón **Obtener recomendaciones**. Esta operación *solo se realiza una vez*. Una vez registrada la suscripción, puede acceder a las recomendaciones de Advisor como *Propietario*, *Contribuidor* o *Lector* para una suscripción, un grupo de recursos o un recurso concreto.

### <a name="how-often-are-advisor-recommendations-updated"></a>¿Con qué frecuencia se actualizan las recomendaciones de Advisor?

Las recomendaciones de Advisor se actualizan cada hora.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>¿Para qué recursos Advisor ofrece recomendaciones?

Advisor proporciona recomendaciones para máquinas virtuales, conjuntos de disponibilidad, puertas de enlace de aplicaciones, App Services, servidores SQL Server, bases de datos SQL y Redis Cache.

### <a name="can-i-snooze-or-dismiss-a-recommendation"></a>¿Se puede posponer o descartar una recomendación?

Para posponer o descartar una recomendación, haga clic en el botón o vínculo **Snooze** (Posponer). Puede especificar un tiempo de posposición o seleccionar **Never** (Nunca) para descartar la recomendación.

## <a name="next-steps"></a>Pasos siguientes

Para aprender más sobre las recomendaciones de Advisor, consulte:

* [Introducción a Advisor](advisor-get-started.md)
* [Recomendaciones sobre alta disponibilidad de Advisor](advisor-high-availability-recommendations.md)
* [Recomendaciones sobre seguridad de Advisor](advisor-security-recommendations.md)
* [Recomendaciones sobre rendimiento de Advisor](advisor-performance-recommendations.md)
* [Recomendaciones sobre el costo de Advisor](advisor-cost-recommendations.md)
