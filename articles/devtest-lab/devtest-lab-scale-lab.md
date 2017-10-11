---
title: "Escalado de cuotas y límites en su laboratorio en Azure DevTest Labs | Microsoft Docs"
description: Aprenda a escalar un laboratorio en Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: ae624155-9181-45fa-bd2b-1983339b7e0e
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: tarcher
ms.openlocfilehash: f11ed42b474e4f208eac92689bfa33fb188d15a1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Escalado de cuotas y límites en DevTest Labs
Mientras trabaja en DevTest Labs, puede que observe que hay ciertos límites predeterminados para algunos recursos de Azure, lo que puede afectar al servicio DevTest Labs. Estos límites se conocen como **cuotas**.

> [!NOTE]
> El servicio DevTest Labs no impone cuotas. Las cuotas que podría encontrar son restricciones predeterminadas de la suscripción de Azure en general.

Puede usar cada recurso de Azure hasta que alcance su cuota. Cada suscripción tiene cuotas independientes y se realiza el seguimiento del uso para cada suscripción.

Por ejemplo, cada suscripción tiene una cuota predeterminada de 20 núcleos. Por lo tanto, si va a crear en el laboratorio máquinas virtuales con cuatro núcleos, solo puede crear cinco. 

En [Límites de suscripción y de servicios de Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits), se enumeran algunas de las cuotas más frecuentes para recursos de Azure. Entre los recursos que más se usan en un laboratorio y para los que puede encontrar cuotas, están los núcleos de máquina virtual, las direcciones IP públicas, la interfaz de red, los discos administrados, la asignación de roles RBAC y los circuitos ExpressRoute.

## <a name="view-your-usage-and-quotas"></a>Visualización del uso y las cuotas
Estos pasos muestran cómo ver las cuotas actuales en su suscripción para recursos específicos de Azure y comprobar qué porcentaje de cada cuota ha usado.

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Seleccione **Más servicios** y **Facturación** en la lista.
1. En la hoja Facturación, seleccione una suscripción.
4. Seleccione **Uso y cuotas**.

   ![Botón Uso y cuotas](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas.png)

   Aparece la hoja Uso y cuotas, donde se muestran los distintos recursos disponibles en esa suscripción y el porcentaje de la cuota que está usando para cada uno de ellos.

   ![Cuotas y uso](./media/devtest-lab-scale-lab/devtestlab-view-quotas.png)

## <a name="requesting-more-resources-in-your-subscription"></a>Solicitud de más recursos en la suscripción
Si alcanza un límite de cuota, se puede aumentar el límite predeterminado de un recurso en una suscripción hasta un máximo, como se describe en [Límites de suscripción y de servicios de Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits).

Estos pasos le muestran cómo solicitar un aumento de la cuota a través de [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Seleccione **Más servicios**, **Facturación**, y **Uso y cuotas**.
1. En la hoja Uso y cuotas, seleccione el botón **Solicitar aumento**.

   ![Botón Solicitar aumento](./media/devtest-lab-scale-lab/devtestlab-request-increase.png)

1. Para completar y enviar la solicitud, rellene la información necesaria en las tres pestañas del formulario **Nueva solicitud de soporte técnico**.

   ![Formulario Solicitar aumento](./media/devtest-lab-scale-lab/devtestlab-support-form.png)

[Understanding Azure Limits and Increases](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) (Información sobre los límites y los aumentos de Azure) proporciona más información sobre cómo ponerse en contacto con el soporte técnico de Azure para solicitar un aumento de la cuota.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Pasos siguientes
* Explore la [galería de plantillas de inicio rápido de Azure Resource Manager de DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
