---
title: "Introducción a Azure Advisor | Microsoft Docs"
description: Utilice Azure Advisor para optimizar las implementaciones de Azure.
services: advisor
documentationcenter: NA
author: kumudd
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
translationtype: Human Translation
ms.sourcegitcommit: e7fc6cb4ee4752bb7ed90a8f64edcc7c82ec3a88
ms.openlocfilehash: 3c4f6d13dd43ae557b177883e14044e637bd803b
ms.lasthandoff: 02/21/2017

---

# <a name="introduction-to-azure-advisor"></a>Introducción a Azure Advisor

Obtenga información sobre Azure Advisor, sus funciones clave y las preguntas más frecuentes.

## <a name="what-is-azure-advisor"></a>¿Qué es Azure Advisor?
Asistente de Azure es un consultor en la nube personalizado que le ayudará a realizar procedimientos recomendadas para optimizar las implementaciones de Azure. Se analiza la configuración de recursos y la telemetría de utilización y se recomiendan soluciones que pueden ayudar a mejoran la rentabilidad, el rendimiento, la alta disponibilidad y la seguridad de los recursos de Azure.

Con Azure Advisor puede:
-    obtener sugerencias de procedimientos recomendados proactivas, accionables y personalizadas, 
-    mejorar el rendimiento, la seguridad y la alta disponibilidad de los recursos, al mismo tiempo que busca oportunidades para reducir el gasto general de Azure, y
-    obtener recomendaciones con acciones insertadas.

Puede acceder a Advisor mediante [Azure Portal](https://aka.ms/azureadvisordashboard). Inicie sesión en el [portal](https://portal.azure.com), seleccione **Browse** (Examinar) y luego desplácese a **Azure Advisor**. El panel de Advisor muestra recomendaciones personalizadas de la suscripción seleccionada. Las recomendaciones se dividen en cuatro categorías. 

-   **High Availability** (Alta disponibilidad): lo ayuda a garantizar y mejorar la continuidad de las aplicaciones empresariales críticas. Para obtener más información, consulte las [recomendaciones sobre alta disponibilidad de Advisor](advisor-high-availability-recommendations.md).

-   **Security** (Seguridad): lo ayuda a detectar amenazas y vulnerabilidades que podrían dar lugar a posibles infracciones de seguridad. Para obtener más información, consulte las [recomendaciones sobre seguridad de Advisor](advisor-security-recommendations.md).

-   **Performance** (Rendimiento): lo ayuda a mejorar la velocidad de las aplicaciones. Para obtener más información, consulte las [recomendaciones sobre rendimiento de Advisor](advisor-performance-recommendations.md).

-   **Cost** (Costo): lo ayuda a optimizar y reducir el gasto general de Azure. Para obtener más información, consulte las [recomendaciones sobre el costo de Advisor](advisor-cost-recommendations.md).

  ![Tipos de recomendaciones de Advisor](./media/advisor-overview/advisor-all-tab-examples.png)

> [!NOTE]
> Para obtener acceso a las recomendaciones de Advisor, primero debe **registrar** su suscripción en él. Una suscripción se registra cuando el **propietario de esta** inicia el panel de Advisor y hace clic en el botón **Get recommendations** (Obtener recomendaciones). Esta operación **solo se realiza una vez**. Una vez registrada la suscripción, los **propietarios**, **contribuidores**, o **lectores** de dicha suscripción, un grupo de recursos o un recursos concreto pueden acceder a las recomendaciones de Advisor.

Puede hacer clic en una recomendación para obtener información adicional sobre ella. También puede obtener información sobre las acciones que puede llevar a cabo para aprovechar las ventajas de una oportunidad o resolver un problema. Advisor ofrece recomendaciones con acciones insertadas o vínculos de documentación. Al hacer clic en una acción insertada, se inicia un "viaje de usuario guiado" para implementarla. Al hacer clic en un vínculo de documentación, se remite a la documentación que describe cómo puede implementar la acción de forma manual. 

Advisor actualiza las recomendaciones cada hora. Si no desea realizar una acción inmediata debido a una recomendación, puede posponerla durante un período de tiempo o descartarla. 

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="how-do-i-access-advisor"></a>¿Cómo se accede a Advisor?
Puede acceder a Advisor mediante Azure Portal. Inicie sesión en el portal, seleccione **Browse** (Examinar) y luego desplácese a **Azure Advisor**. También puede ver las recomendaciones de Advisor a través de la hoja de recursos de la máquina virtual. Seleccione una máquina virtual y después desplácese a las recomendaciones de Advisor en el menú. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>¿Qué permisos son necesarios para acceder a Advisor?

Para obtener acceso a las recomendaciones de Advisor, primero debe **registrar** su suscripción en él. Una suscripción se registra cuando el propietario de esta inicia el panel de Advisor y hace clic en el botón **Get recommendations** (Obtener recomendaciones). Esta operación **solo se realiza una vez**. Una vez registrada la suscripción, los **propietarios**, **contribuidores**, o **lectores** de dicha suscripción, un grupo de recursos o un recursos concreto pueden acceder a las recomendaciones de Advisor.

### <a name="how-often-are-advisor-recommendations-updated"></a>¿Con qué frecuencia se actualizan las recomendaciones de Advisor?

Las recomendaciones de Advisor se actualizan cada hora.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>¿Para qué recursos Advisor ofrece recomendaciones?

Advisor proporciona recomendaciones para máquinas virtuales, conjuntos de disponibilidad, instancias de Application Gateway, App Services, servidores SQL Server, bases de datos SQL Database y Redis Cache.

### <a name="can-i-snooze-or-dismiss-a-recommendation"></a>¿Se puede posponer o descartar una recomendación?

Para posponer o descartar una recomendación, haga clic en el botón o vínculo **Snooze** (Posponer). Puede especificar un tiempo de posposición o seleccionar **Never** (Nunca) para descartar la recomendación.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre las recomendaciones de Advisor, consulte los recursos siguientes:

-  [Introducción a Advisor](advisor-get-started.md)
-  [Recomendaciones sobre alta disponibilidad de Advisor](advisor-high-availability-recommendations.md)
-  [Recomendaciones sobre seguridad de Advisor](advisor-security-recommendations.md)
-  [Recomendaciones sobre rendimiento de Advisor](advisor-performance-recommendations.md)
-  [Recomendaciones sobre el costo de Advisor](advisor-cost-recommendations.md)

