---
title: Introducción a los conjuntos de escalado de máquinas virtuales de Azure | Microsoft Docs
description: Información acerca de los conjuntos de escalado de máquinas virtuales de Azure y de cómo escalar automáticamente sus aplicaciones
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 03/27/2018
ms.author: negat
ms.openlocfilehash: 8ded9b20bd70d18b8a68df0c9775f3a56f8b185b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
ms.locfileid: "30247382"
---
# <a name="what-are-virtual-machine-scale-sets"></a>¿Qué son los conjuntos de escalado de máquina virtual?
Los conjuntos de escalado de máquinas virtuales de Azure permiten crear y administrar un grupo de máquinas virtuales idénticas con equilibrio de carga y ajuste automático. El número de instancias de máquina virtual puede aumentar o disminuir automáticamente según la demanda, o de acuerdo a una programación definida. Los conjuntos de escalado proporcionan una alta disponibilidad a las aplicaciones y le permiten administrar, configurar y actualizar de forma centralizada un gran número de máquinas virtuales. Con los conjuntos de escalado de máquinas virtuales, puede crear servicios a gran escala para áreas como proceso, macrodatos y cargas de trabajo de contenedor.


## <a name="why-use-virtual-machine-scale-sets"></a>¿Por qué utilizar conjuntos de escalado de máquinas virtuales?
Para proporcionar redundancia y mejorar el rendimiento las aplicaciones normalmente se distribuyen entre varias instancias. Los clientes pueden acceder a la aplicación mediante un equilibrador de carga que distribuye las solicitudes a una de las instancias de la aplicación. Si necesita realizar tareas de mantenimiento o actualizar una instancia de la aplicación, los clientes tienen que distribuirse a otra instancia de aplicación que esté disponible. Para hacer frente a la demanda adicional de los clientes, puede que tenga que aumentar el número de instancias de aplicación en las que se ejecuta la aplicación.

Los conjuntos de escalado de máquinas virtuales de Azure proporcionan las capacidades de administración para las aplicaciones que se ejecutan en varias máquinas virtuales, [el escalado automático de recursos](virtual-machine-scale-sets-autoscale-overview.md)y el equilibrio de carga de tráfico. Los conjuntos de escalado ofrecen las siguientes ventajas principales:

- **Facilitan la creación y administración de varias máquinas virtuales**
    - Cuando tiene muchas máquinas virtuales que ejecutan la aplicación es importante mantener una configuración coherente en todo el entorno. Para obtener un rendimiento confiable de la aplicación, el tamaño de máquina virtual, la configuración de disco y las instalaciones de la aplicación deben coincidir en todas las máquinas virtuales.
    - Con los conjuntos de escalado se crean todas las instancias de máquina virtual desde la misma configuración e imagen base de sistema operativo. Este enfoque le permite administrar fácilmente cientos de máquinas virtuales sin tareas adicionales de configuración o administración de red.
    - Los conjuntos de escalado admiten el uso de [Azure Load Balancer](../load-balancer/load-balancer-overview.md) para la distribución del tráfico de nivel 4 básico, y de [Azure Application Gateway](../application-gateway/application-gateway-introduction.md) para las características más avanzadas de distribución de nivel 7 y terminación SSL.

- **Proporcionan alta disponibilidad y resistencia de aplicación**
    - Los conjuntos de escalado se utilizan para ejecutar varias instancias de la aplicación. Si una de estas instancias de máquina virtual tiene un problema, los clientes siguen teniendo acceso a la aplicación a través de una de las otras instancias de máquina virtual con una interrupción mínima.
    - Con el fin de tener mayor disponibilidad, puede usar [zonas de disponibilidad](../availability-zones/az-overview.md) para distribuir automáticamente las instancias de máquina virtual en un conjunto de escalado dentro de un único centro de datos o en varios centros de datos.

- **Permiten a la aplicación escalar automáticamente a medida que cambia la demanda de recursos**
    - La demanda de la aplicación por parte de los clientes puede cambiar a lo largo del día o de la semana. Para cumplir con la demanda del cliente, los conjuntos de escalado pueden aumentar o disminuir automáticamente el número de instancias de máquina virtual a medida que aumenta o disminuye la demanda de la aplicación.
    - El escalado automático reduce el número de instancias innecesarias de máquina virtual que ejecutan la aplicación cuando la demanda es baja, mientras que cuando la demanda aumenta, los clientes continúan recibiendo un nivel aceptable de rendimiento gracias a la incorporación automática de instancias adicionales de máquina virtual. Esta capacidad le ayuda a reducir los costos y a crear de forma eficaz los recursos de Azure según sea necesario.

- **Funciona a gran escala**
    - Los conjuntos de escalado admiten hasta 1000 instancias de máquina virtual. Si crea y carga sus propias imágenes de máquina virtual personalizadas, el límite es 300 instancias.
    - Para obtener el mejor rendimiento con cargas de trabajo de producción, use [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md) y [Premium Storage](../virtual-machines/windows/premium-storage.md).


## <a name="differences-between-virtual-machines-and-scale-sets"></a>Diferencias entre las máquinas virtuales y los conjuntos de escalado
Los conjuntos de escalado se generan a partir de máquinas virtuales. Con los conjuntos de escalado las capas de administración y automatización sirven para ejecutar y escalar sus aplicaciones. En su lugar podría crear y administrar manualmente máquinas virtuales individuales, o integrar herramientas existentes para crear un nivel similar de automatización. En la tabla siguiente se describen las ventajas de los conjuntos de escalado en comparación con la administración manual de varias instancias de máquina virtual.

| Escenario                           | Grupo manual de máquinas virtuales                                                                    | Conjunto de escalado de máquina virtual |
|------------------------------------|----------------------------------------------------------------------------------------|---------------------------|
| Incorporación de instancias adicionales de máquina virtual        | Proceso manual para crear, configurar y garantizar el cumplimiento                             | Creación automática a partir de la configuración central |
| Equilibrio y distribución del tráfico | Proceso manual para crear y configurar una instancia de Azure Load Balancer o Application Gateway      | Posibilidad de crear e integrar automáticamente con Azure Load Balancer o Application Gateway |
| Alta disponibilidad y redundancia   | Creación manual de conjuntos de disponibilidad o distribución y seguimiento manual de las máquinas virtuales en las distintas zonas de disponibilidad | Distribución automática de instancias de máquina virtual a través de las zonas de disponibilidad o conjuntos de disponibilidad |
| Escalado de máquinas virtuales                     | Supervisión manual y Azure Automation                                                 | Escalado automático basado en las métricas de host, las métricas de invitado, Application Insights o programación |

No hay costo adicional por usar conjuntos de escalado. Solo se paga por los recursos de proceso subyacente como las instancias de máquina virtual, el equilibrador de carga o el almacenamiento de disco administrado. Las características de administración y automatización, como el escalado automático y la redundancia, no incurren en cargos adicionales sobre el uso de máquinas virtuales.


## <a name="next-steps"></a>Pasos siguientes
Para empezar cree su primer conjunto de escalado de máquinas virtuales en Azure Portal.

> [!div class="nextstepaction"]
> [Creación de un conjunto de escalado en Azure Portal](quick-create-portal.md)
