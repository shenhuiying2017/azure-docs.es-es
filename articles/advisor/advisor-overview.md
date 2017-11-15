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
ms.openlocfilehash: 906450f75557820bb27762707c3328b08b23cccb
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2017
---
# <a name="introduction-to-azure-advisor"></a>Introducción a Azure Advisor

Obtenga información sobre las funcionalidades clave de Azure Advisor y obtenga respuesta a las preguntas más frecuentes.

## <a name="what-is-advisor"></a>¿Qué es Advisor?
Advisor es un consultor personalizado en la nube que lo ayuda a seguir procedimientos recomendados para optimizar las implementaciones de Azure. Analiza la configuración de recursos y la telemetría de uso, y recomienda soluciones que pueden ayudar a mejoran la rentabilidad, el rendimiento, la alta disponibilidad y la seguridad de los recursos de Azure.

Con Advisor, puede:
* Obtener sugerencias de procedimientos recomendados proactivas, prácticas y personalizadas, 
* Mejorar el rendimiento, la seguridad y la alta disponibilidad de los recursos, al mismo tiempo que identifica oportunidades para reducir el gasto general de Azure, y
* Obtener recomendaciones con acciones propuestas en línea.

Puede acceder a Advisor mediante [Azure Portal](https://aka.ms/azureadvisordashboard). Inicie sesión en el [portal](https://portal.azure.com), busque **Advisor** en el menú de navegación o búsquelo en el menú **Más servicios**.

El panel de Advisor muestra recomendaciones personalizadas para todas las suscripciones.  Puede aplicar filtros para mostrar las recomendaciones para determinadas suscripciones y tipos de recursos.  Las recomendaciones se dividen en cuatro categorías: 

* **Alta disponibilidad**: lo ayuda a garantizar y mejorar la continuidad de las aplicaciones empresariales críticas. Para obtener más información, consulte las [recomendaciones sobre alta disponibilidad de Advisor](advisor-high-availability-recommendations.md).
* **Seguridad**: lo ayuda a detectar amenazas y vulnerabilidades que podrían dar lugar a infracciones de seguridad. Para obtener más información, consulte las [recomendaciones sobre seguridad de Advisor](advisor-security-recommendations.md).
* **Rendimiento**: lo ayuda a mejorar la velocidad de las aplicaciones. Para obtener más información, consulte las [recomendaciones sobre rendimiento de Advisor](advisor-performance-recommendations.md).
* **Cost** (Costo): lo ayuda a optimizar y reducir el gasto general de Azure. Para obtener más información, consulte las [recomendaciones sobre el costo de Advisor](advisor-cost-recommendations.md).

  ![Tipos de recomendaciones de Advisor](./media/advisor-overview/advisor-dashboard.png)

> [!NOTE]
> Para utilizar Azure Advisor con una suscripción, debe iniciarse una suscripción *Propietario* en el panel de Advisor.  Esta acción registra la suscripción con Advisor.  Desde ese momento, cualquier suscripción *Propietario*, *Colaborador* o *Lector* puede acceder a las recomendaciones de Advisor para la suscripción. 

Puede hacer clic en una categoría para ver la lista de recomendaciones dentro de esa categoría y seleccionar una recomendación para obtener más información sobre ella.  También puede aprender más sobre las acciones que puede llevar a cabo para aprovechar las ventajas de una oportunidad o resolver un problema.

![Categoría de recomendaciones de Advisor](./media/advisor-overview/advisor-ha-category-example.png) 

Seleccione la acción recomendada para implementar la recomendación.  Se abrirá una interfaz sencilla que le permitirá implementar la recomendación o consultar la documentación que le ayudará con la implementación.  Una vez que implemente una recomendación, Advisor puede tardar un día en reconocerla.

Si no desea realizar de inmediato una acción basada en una recomendación, puede posponerla durante un período de tiempo o descartarla.  SI no desea recibir recomendaciones para un grupo de recursos o una suscripción específicos, puede configurar Advisor para generar solo recomendaciones para grupos de recursos y suscripciones específicos.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="how-do-i-access-advisor"></a>¿Cómo se accede a Advisor?
Puede acceder a Advisor mediante [Azure Portal](https://aka.ms/azureadvisordashboard). Inicie sesión en el [portal](https://portal.azure.com), busque **Advisor** en el menú de navegación o búsquelo en el menú **Más servicios**.

También puede ver las recomendaciones de Advisor a través de la interfaz de recursos de la máquina virtual. Seleccione una máquina virtual y después desplácese a las recomendaciones de Advisor en el menú. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>¿Qué permisos son necesarios para acceder a Advisor?

Para obtener recomendaciones de Advisor para una suscripción, primero debe registrar su suscripción en dicha solución. Una suscripción se registra cuando un *Propietario*  de una suscripción inicia el panel de Advisor. Esta operación solo se realiza una vez. Una vez registrada la suscripción, puede acceder a las recomendaciones de Advisor como *Propietario*, *Contribuidor* o *Lector* para una suscripción.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>¿Para qué recursos Advisor ofrece recomendaciones?

Advisor proporciona recomendaciones para máquinas virtuales, conjuntos de disponibilidad, puertas de enlace de aplicaciones, App Services, servidores SQL Server, bases de datos SQL y Redis Cache.

### <a name="can-i-snooze-or-dismiss-a-recommendation"></a>¿Se puede posponer o descartar una recomendación?

Para posponer o descartar una recomendación, haga clic en el **Snooze** (Posponer). Puede especificar un tiempo de posposición o seleccionar **Never** (Nunca) para descartar la recomendación.

## <a name="next-steps"></a>Pasos siguientes

Para aprender más sobre las recomendaciones de Advisor, consulte:

* [Introducción a Advisor](advisor-get-started.md)
* [Recomendaciones sobre alta disponibilidad de Advisor](advisor-high-availability-recommendations.md)
* [Recomendaciones sobre seguridad de Advisor](advisor-security-recommendations.md)
* [Recomendaciones sobre rendimiento de Advisor](advisor-performance-recommendations.md)
* [Recomendaciones sobre el costo de Advisor](advisor-cost-recommendations.md)
