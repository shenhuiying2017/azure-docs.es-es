---
title: archivo de inclusión
description: archivo de inclusión
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: ee32886ddb74bdbbe0f240310629c8ef26230a68
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
## <a name="deployment-considerations"></a>Consideraciones de la implementación
* **Suscripción de Azure**: para implementar más que algunas instancias de proceso intensivo, considere la posibilidad de usar una suscripción de pago por uso u otras opciones de compra. Si usa una [cuenta gratuita de Azure](https://azure.microsoft.com/free/), solo puede usar un número limitado de núcleos de proceso de Azure.

* **Disponibilidad y precios**: los tamaños de máquina virtual se ofrecen solo en el plan de tarifa estándar. Consulte [Productos disponibles por región] https://azure.microsoft.com/regions/services/) para ver la disponibilidad en las regiones de Azure. 
* **Cuota de núcleos**: quizás tenga que aumentar la cuota de núcleos de su suscripción de Azure partiendo del valor predeterminado. La suscripción también podría limitar el número de núcleos que se pueden implementar en ciertas familias de tamaño de máquina virtual, como la serie H. Para solicitar un aumento de cuota, [abra una solicitud de soporte técnico al cliente en línea](../articles/azure-supportability/how-to-create-azure-support-request.md) sin cargo alguno. (Los límites predeterminados pueden variar según la categoría de suscripción).
  
  > [!NOTE]
  > Si tiene necesidades de capacidad a gran escala, póngase en contacto con el soporte técnico de Azure. Las cuotas de Azure son límites de crédito, no garantías de capacidad. Independientemente de la cuota, solamente se le cobrarán los núcleos que use.
  > 
  > 
* **Red virtual** : no se necesita una [red virtual](https://azure.microsoft.com/documentation/services/virtual-network/) de Azure para usar instancias de proceso intensivo. Sin embargo, para muchas implementaciones necesita al menos una red virtual de Azure basada en la nube o una conexión de sitio a sitio si necesita acceder a recursos locales. Si es necesario, cree una red virtual para implementar las instancias. No se admite la adición de máquinas virtuales de proceso intensivo a las redes virtuales de grupos de afinidad.
* **Cambio de tamaño**: debido a su hardware especializado, solo se puede cambiar el tamaño de las instancias de proceso intensivo dentro de la misma familia de tamaño (serie H o serie A de proceso intensivo). Por ejemplo, una máquina virtual de la serie H solo se puede cambiar de un tamaño de serie H a otro. Además, no se admite el cambio de tamaño de un tamaño que no sea de proceso intensivo a un tamaño que sí lo sea.  

## <a name="rdma-capable-instances"></a>Instancias compatibles con RDMA
Un subconjunto de las instancias de proceso intensivo (H16r, H16mr, A8 y A9) incluye una interfaz de red para la conectividad de acceso directo a memoria remota (RDMA). (Los tamaños seleccionados de la serie N designados con "r", como NC24r, también son compatibles con RDMA). Esta interfaz se añade a la interfaz de red estándar de Azure disponible para otros tamaños de máquina virtual. 
  
Esta interfaz permite que las instancias compatibles con RDMA se comuniquen través de una red InfiniBand (IB), que funciona a velocidades FDR en máquinas virtuales H16r, H16mr y de la serie N compatibles con RDMA, y a velocidades QDR en máquinas virtuales A8 y A9. Estas funcionalidades RDMA pueden mejorar la escalabilidad y el rendimiento de determinadas aplicaciones de la Interfaz de paso de mensajes (MPI).

> [!NOTE]
> En Azure, no se admite IP sobre IB. Solo se admite RDMA sobre IB.
>

Implemente las máquinas virtuales HPC compatibles con RDMA en el mismo conjunto de disponibilidad o conjunto de escalado de máquinas virtuales (si usa el modelo de implementación de Azure Resource Manager) o en el mismo servicio en la nube (si usa el modelo de implementación clásica). A continuación se indican los requisitos adicionales para máquinas virtuales HPC compatibles con RDMA para acceder a la red RDMA de Azure.