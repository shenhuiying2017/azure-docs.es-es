---
title: archivo de inclusión
description: archivo de inclusión
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 03/19/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: c1e57ea28f597293d8bb52207bbbb76892b1d5f5
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/24/2018
---
## <a name="deployment-considerations"></a>Consideraciones de la implementación

* Para ver la disponibilidad de máquinas virtuales de la serie N, consulte [Productos disponibles por región](https://azure.microsoft.com/en-us/regions/services/).

* Las máquinas virtuales de la serie N solo se pueden implementar en el modelo de implementación de Resource Manager.

* Las máquinas virtuales de serie N difieren en el tipo de Azure Storage que admiten en sus discos. Las máquinas virtuales NC y NV solo admiten discos de máquina virtual respaldados por Disk Storage (HDD) estándar. Las máquinas virtuales NCv2, ND y NCv3 solo admiten discos de máquina virtual respaldados por Disk Storage (SSD) Premium.

* Si desea implementar más de un pequeño número de máquinas virtuales de la serie N, considere la posibilidad de usar una suscripción de pago por uso u otras opciones de compra. Si usa una [cuenta gratuita de Azure](https://azure.microsoft.com/free/), solo puede usar un número limitado de núcleos de proceso de Azure.

* Es posible que necesite aumentar la cuota de núcleos (por región) de la suscripción de Azure y la cuota independiente para núcleos NC, NCv2, NCv3, ND o NV. Para solicitar un aumento de cuota, [abra una solicitud de soporte técnico al cliente en línea](../articles/azure-supportability/how-to-create-azure-support-request.md) sin cargo alguno. Los límites predeterminados pueden variar según la categoría de suscripción.




