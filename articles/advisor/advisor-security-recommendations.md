---
title: Recomendaciones sobre seguridad de Azure Advisor | Microsoft Docs
description: Utilice Azure Advisor para mejorar la seguridad de las implementaciones de Azure.
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
ms.openlocfilehash: dbdbae0f87d8d115407026b263c65c3b387fa178
ms.lasthandoff: 02/21/2017

---
# <a name="advisor-security-recommendations"></a>Recomendaciones sobre seguridad de Advisor

Advisor proporciona una vista coherente y consolidada de recomendaciones para todos los recursos de Azure. Se integra con Azure Security Center para proporcionarle recomendaciones sobre seguridad. Puede obtener recomendaciones sobre seguridad en la pestaña **Security** (Seguridad) en el panel de Advisor.

![Pestaña Advisor security (Seguridad de Advisor)](./media/advisor-security-recommendations/advisor-security-tab.png)

El Centro de seguridad ayuda a evitar, detectar y responder a amenazas con más visibilidad y control de la seguridad en los recursos de Azure. Analiza periódicamente el estado de seguridad de los recursos de Azure. Cuando el Centro de seguridad identifica vulnerabilidades de seguridad potenciales, crea recomendaciones. Las recomendaciones le guían en el proceso de configuración de los controles necesarios. 

![Pestaña Advisor security (Seguridad de Advisor)](./media/advisor-security-recommendations/advisor-security-recommendations.png)

Para obtener más información sobre las recomendaciones sobre seguridad, consulte sobre la [administración de recomendaciones sobre seguridad en Azure Security Center](https://azure.microsoft.com/en-us/documentation/articles/security-center-recommendations/).

## <a name="how-to-access-security-recommendations-in-azure-advisor"></a>Obtención de acceso a las recomendaciones sobre seguridad en Azure Advisor

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. En el panel de navegación de la izquierda, haga clic en **More services** (Más servicios), en el panel de menú de servicio, desplácese hacia abajo hasta **Monitoring and Management** (Supervisión y administración), y, a continuación, haga clic en **Azure Advisor**. Esto inicia el panel de Advisor. 
3. En el panel de Advisor, haga clic en la pestaña **Security** (Seguridad) y seleccione la suscripción para la que le gustaría recibir las recomendaciones.

> [!NOTE]
> Para obtener acceso a las recomendaciones de Advisor, primero debe **registrar** su suscripción en él. Una suscripción se registra cuando el **propietario de esta** inicia el panel de Advisor y hace clic en el botón **Get recommendations** (Obtener recomendaciones). Esta operación **solo se realiza una vez**. Una vez registrada la suscripción, los **propietarios**, **contribuidores**, o **lectores** de dicha suscripción, un grupo de recursos o un recursos concreto pueden acceder a las recomendaciones de Advisor.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre las recomendaciones de Advisor, consulte los recursos siguientes:
-  [Recomendaciones sobre alta disponibilidad de Advisor](advisor-high-availability-recommendations.md)
-  [Recomendaciones sobre rendimiento de Advisor](advisor-performance-recommendations.md)
-  [Recomendaciones sobre el costo de Advisor](advisor-performance-recommendations.md)
 

