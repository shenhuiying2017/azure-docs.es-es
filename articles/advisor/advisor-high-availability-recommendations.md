---
title: Recomendaciones sobre alta disponibilidad de Azure Advisor | Microsoft Docs
description: Utilice Azure Advisor para mejorar la alta disponibilidad de las implementaciones de Azure.
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
ms.openlocfilehash: a6ee1d018bf33aa76d089d20f6e71318a3586aa8
ms.lasthandoff: 02/21/2017

---

# <a name="advisor-high-availability-recommendations"></a>Recomendaciones sobre alta disponibilidad de Advisor

Advisor lo ayuda a garantizar y mejorar la continuidad de las aplicaciones empresariales críticas. Puede obtener recomendaciones sobre alta disponibilidad de Advisor en la pestaña **High Availability** (Alta disponibilidad) del panel de Advisor.

![](./media/advisor-high-availability-recommendations/advisor-high-availability-tab.png)


## <a name="virtual-machines-without-an-availability-set"></a>Máquinas virtuales sin un conjunto de disponibilidad

Advisor identifica las máquinas virtuales que no forman parte de un conjunto de disponibilidad y recomienda moverlas a un conjunto de disponibilidad. Para proporcionar redundancia a la aplicación, recomendamos que agrupe dos máquinas virtuales o más en un conjunto de disponibilidad. Esta configuración garantiza que durante un evento de mantenimiento planeado o no planeado, al menos una máquina virtual esté disponible y cumpla el Acuerdo de Nivel de Servicio de la máquina virtual de Azure. Puede crear un conjunto de disponibilidad de la máquina virtual o agregar la máquina virtual a un conjunto de disponibilidad existente.

> [!NOTE]
> Si decide crear un conjunto de disponibilidad, debe agregar al menos una máquina virtual más en ese conjunto de disponibilidad después de haberlo creado. Se recomienda que agrupe dos o más máquinas virtuales en un conjunto de disponibilidad para garantizar que una de las máquinas esté disponible durante una interrupción.

![](./media/advisor-high-availability-recommendations/advisor-high-availability-create-availability-set.png)

## <a name="availability-sets-with-a-single-virtual-machine"></a>Conjuntos de disponibilidad con una sola máquina virtual 

Advisor identifica conjuntos de disponibilidad que contienen una sola máquina virtual y recomienda que agregue una o más máquinas virtuales a ella. Para proporcionar redundancia a la aplicación, recomendamos que agrupe dos máquinas virtuales o más en un conjunto de disponibilidad. Esta configuración garantiza que durante un evento de mantenimiento planeado o no planeado, al menos una máquina virtual esté disponible y cumpla el Acuerdo de Nivel de Servicio de la máquina virtual de Azure. Puede crear una máquina virtual o usar una máquina virtual existente y agregarla al conjunto de disponibilidad.  


![](./media/advisor-high-availability-recommendations/advisor-high-availability-add-vm-to-availability-set.png)

## <a name="virtual-machines-with-standard-disks"></a>Máquinas virtuales con discos estándar

Advisor identifica las máquinas virtuales con discos estándar y recomienda actualizar a discos Premium.  
El Almacenamiento premium de Azure le ofrece soporte de disco de alto rendimiento y latencia baja para máquinas virtuales con cargas de trabajo intensivas de E/S. Discos de máquinas virtuales que usan datos de almacén Premium Storage en unidades de estado sólido (SSD). Se recomienda migrar cualquier disco de máquina virtual que requiera un número elevado de operaciones de entrada/salida por segundo al Almacenamiento premium de Azure para mejorar el rendimiento de la aplicación. Si el disco no requiere E/S por segundo elevadas, puede limitar los costos mediante el mantenimiento en storage estándar. Storage estándar almacena los datos de discos de máquinas virtuales en unidades de disco duro (HDD) en lugar de SSD. Puede migrar los discos de máquinas virtuales a discos Premium. Los discos Premium son compatibles con la mayoría de las SKU de máquinas virtuales. Sin embargo, en algunos casos, si desea usar discos Premium, es posible que deba actualizar también la SKU de la máquina virtual.   

![](./media/advisor-high-availability-recommendations/advisor-high-availability-upgrade-to-premium-disks.png) 

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Obtención de acceso a las recomendaciones sobre alta disponibilidad en Advisor

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. En el panel de navegación de la izquierda, haga clic en **More services** (Más servicios), en el panel de menú de servicio, desplácese hacia abajo hasta **Monitoring and Management** (Supervisión y administración), y, a continuación, haga clic en **Azure Advisor**. Esto inicia el panel de Advisor. 
3. En el panel de Advisor, haga clic en la pestaña **High Availability** (Alta disponibilidad) y seleccione la suscripción para la que le gustaría recibir las recomendaciones.

> [!NOTE]
> Para obtener acceso a las recomendaciones de Advisor, primero debe **registrar** su suscripción en él. Una suscripción se registra cuando el **propietario de esta** inicia el panel de Advisor y hace clic en el botón **Get recommendations** (Obtener recomendaciones). Esta operación **solo se realiza una vez**. Una vez registrada la suscripción, los **propietarios**, **contribuidores**, o **lectores** de dicha suscripción, un grupo de recursos o un recursos concreto pueden acceder a las recomendaciones de Advisor.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre las recomendaciones de Advisor, consulte los recursos siguientes:
-  [Introducción a Azure Advisor](advisor-overview.md)
-  [Introducción a Advisor](advisor-get-started.md)
-  [Recomendaciones sobre seguridad de Advisor](advisor-security-recommendations.md)
-  [Recomendaciones sobre rendimiento de Advisor](advisor-performance-recommendations.md)
-  [Recomendaciones sobre el costo de Advisor](advisor-performance-recommendations.md)

