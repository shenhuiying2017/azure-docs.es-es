---
title: "Solicitudes de aumento de cuota de núcleos de Azure Resource Manager | Microsoft Docs"
description: "Solicitudes de aumento de cuota de núcleos de Azure Resource Manager"
author: ganganarayanan
ms.author: gangan
ms.date: 1/18/2017
ms.topic: article
ms.service: microsoft-docs
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: cb6c5b3e86f126d4110d1cd29d8c9891e356e414
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="resource-manager-core-quota-increase-requests"></a>Solicitudes de aumento de cuota de núcleos de Resource Manager

Se imponen cuotas de núcleos de Resource Manager en el nivel de región y de familia de SKU.
Aprenda más sobre cómo se imponen cuotas en la página [Límites de servicios y suscripciones de Azure](http://aka.ms/quotalimits).
Para más información sobre las familias de SKU, puede comparar coste y el rendimiento de la [precios de máquinas virtuales](http://aka.ms/pricingcompute) página.

Para solicitar un aumento, cree un caso de soporte técnico de cuotas en el portal de Azure, [https://portal.azure.com](https://portal.azure.com).

> [!NOTE]
> Aprenda a [crear una solicitud de soporte técnico](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) en el portal de Azure

1. En la nueva página de solicitud de soporte técnico, seleccione el tipo de problema de "Cuota" y el tipo de cuota de "Núcleos".

    ![Hoja de aspectos básicos de Cuota](./media/resource-manager-core-quotas-request/Basics-blade.png)

2. Seleccione el modelo de implementación "Resource Manager" y seleccione una ubicación.

    ![Hoja de problema de Cuota](./media/resource-manager-core-quotas-request/Problem-step.png)

3. Seleccione las familias de SKU que requieren un aumento.

    ![Series de SKU seleccionadas](./media/resource-manager-core-quotas-request/SKU-selected.png)

4. Escriba los nuevos límites que quiere en la suscripción.

    ![Nueva solicitud de cuota de SKU](./media/resource-manager-core-quotas-request/SKU-new-quota.png)

- Para quitar una línea, desactive la SKU de la lista desplegable de familias de SKU o haga clic en el icono de descartar "x".
Después de escribir la cuota deseada para cada familia de SKU, haga clic en "Siguiente" en la página de pasos del problema para continuar con la creación de la solicitud de soporte técnico.
