---
title: Disponibilidad de regiones y cuotas en Azure Container Instances
description: La disponibilidad de regiones y cuotas predeterminadas del servicio Azure Container Instances.
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: overview
ms.date: 01/11/2018
ms.author: marsma
ms.openlocfilehash: baf93d4a2a4ba1e05bbf558d0c056fa3aa833fef
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="quotas-and-region-availability-for-azure-container-instances"></a>Disponibilidad de cuotas y regiones en Azure Container Instances

Todos los servicios de Azure incluyen ciertas cuotas y límites predeterminados para los recursos y las características. En las secciones siguientes se detallan los límites de recursos predeterminados para varios recursos de Azure Container Instances (ACI), así como la disponibilidad del servicio ACI en regiones de Azure.

## <a name="service-quotas-and-limits"></a>Límites y cuotas del servicio

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="region-availability"></a>Disponibilidad en regiones

Azure Container Instances está disponible en las siguientes regiones con los límites de memoria y de CPU especificados.

| La ubicación | SO | CPU | Memoria (GB) |
| -------- | -- | :---: | :-----------: |
| Europa Occidental, Oeste de EE. UU., Este de EE. UU., Sudeste Asiático | Linux | 4 | 14 |
| Europa Occidental, Oeste de EE. UU., Este de EE. UU., Sudeste Asiático  | Windows | 4 | 14 |

Las instancias de contenedor creadas dentro de estos límites de recursos están sujetas a disponibilidad dentro de la región de implementación. Cuando una región está sometida a mucha carga, puede experimentar un error al implementar instancias. Para mitigar este tipo de error de implementación, intente implementar las instancias con una configuración inferior de memoria y de CPU, o intente realizar la implementación en un momento posterior.

Para más información sobre cómo solucionar problemas de la implementación de instancias de contenedor, consulte [Solución de problemas en las implementaciones en Azure Container Instances](container-instances-troubleshooting.md).

## <a name="next-steps"></a>pasos siguientes

Pueden aumentarse ciertas cuotas y límites predeterminados. Para solicitar un aumento de uno o varios recursos que admiten un aumento de este tipo, envíe una [solicitud de soporte técnico de Azure] [azure-support] (seleccione "Cuota" en **Tipo de emisión**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
