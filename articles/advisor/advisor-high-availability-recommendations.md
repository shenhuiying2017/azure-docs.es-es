---
title: Recomendaciones sobre alta disponibilidad de Azure Advisor | Microsoft Docs
description: Utilice Azure Advisor para mejorar la alta disponibilidad de las implementaciones de Azure.
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
ms.openlocfilehash: 7b63de2453180e562596c211d40cebe85b95bd54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="advisor-high-availability-recommendations"></a>Recomendaciones sobre alta disponibilidad de Advisor

Azure Advisor lo ayuda a garantizar y mejorar la continuidad de las aplicaciones empresariales críticas. Puede obtener recomendaciones sobre alta disponibilidad de Advisor en la pestaña **High Availability** (Alta disponibilidad) del panel de Advisor.

![Botón Alta disponibilidad en el panel de Advisor](./media/advisor-high-availability-recommendations/advisor-high-availability-tab.png)


## <a name="ensure-virtual-machine-fault-tolerance"></a>Garantía de la tolerancia a errores en máquinas virtuales

Advisor identifica las máquinas virtuales que no forman parte de un conjunto de disponibilidad y recomienda moverlas a un conjunto de disponibilidad. Para proporcionar redundancia a la aplicación, recomendamos que agrupe dos máquinas virtuales o más en un conjunto de disponibilidad. Esta configuración garantiza que, durante un evento de mantenimiento planeado o no planeado, al menos una máquina virtual esté disponible y cumpla el Acuerdo de Nivel de Servicio de máquina virtual de Azure. Puede elegir entre crear un conjunto de disponibilidad de la máquina virtual o agregar la máquina virtual a uno existente.

> [!NOTE]
> Si decide crear un conjunto de disponibilidad, debe agregar al menos una máquina virtual más a él. Se recomienda que agrupe dos o más máquinas virtuales en un conjunto de disponibilidad para garantizar que al menos una de ellas esté disponible durante una interrupción.

![Recomendación de Advisor: para la redundancia de las máquinas virtuales, utilice conjuntos de disponibilidad](./media/advisor-high-availability-recommendations/advisor-high-availability-create-availability-set.png)

## <a name="ensure-availability-set-fault-tolerance"></a>Garantía de la tolerancia a errores del conjunto de disponibilidad 

Advisor identifica conjuntos de disponibilidad que contienen una sola máquina virtual y recomienda que se agregue una o más máquinas virtuales a él. Para proporcionar redundancia a la aplicación, recomendamos que agrupe dos máquinas virtuales o más en un conjunto de disponibilidad. Esta configuración garantiza que, durante un evento de mantenimiento planeado o no planeado, al menos una máquina virtual esté disponible y cumpla el Acuerdo de Nivel de Servicio de máquina virtual de Azure. Puede elegir entre crear una máquina virtual o usar una existente y agregarla al conjunto de disponibilidad.  

![Recomendación de Advisor: agregue una o más máquinas virtuales a este conjunto de disponibilidad](./media/advisor-high-availability-recommendations/advisor-high-availability-add-vm-to-availability-set.png)


## <a name="ensure-application-gateway-fault-tolerance"></a>Garantía de la tolerancia a errores de Application Gateway
Para garantizar la continuidad empresarial de aplicaciones críticas que se basan en Application Gateway, Advisor identifica instancias de Application Gateway que no están configuradas para la tolerancia a errores y recomienda acciones correctoras posibles. Advisor identifica instancias únicas de Application Gateway medianas o grandes, y recomienda que agregue al menos una instancia más. También identifica instancias múltiples de Application Gateway pequeñas y recomienda migrar a SKU medianas o grandes. Advisor recomienda estas acciones para asegurarse de que las instancias de Application Gateway estén configuradas para satisfacer los requisitos actuales del Acuerdo de Nivel de Servicio para estos recursos.

![Recomendación de Advisor: implemente dos o más instancias de Application Gateway medianas o grandes](./media/advisor-high-availability-recommendations/advisor-high-availability-application-gateway.png)

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>Mejora del rendimiento y la confiabilidad de los discos de máquina virtual

Advisor identifica las máquinas virtuales con discos estándar y recomienda actualizarlos a discos premium.
 
Azure Premium Storage ofrece compatibilidad con discos de alto rendimiento y latencia baja para máquinas virtuales que ejecutan cargas de trabajo intensivas de E/S. Los discos de máquinas virtuales que usan cuentas de Premium Storage almacenan datos en unidades de estado sólido (SSD). Para lograr un rendimiento óptimo en la aplicación, se recomienda migrar todos los discos de máquinas virtuales que requieran E/S por segundo elevados a Premium Storage. 

Si los discos no requieren E/S por segundo elevadas, puede limitar los costos si los mantiene en una cuenta de almacenamiento Estándar. Con la cuenta de almacenamiento Estándar, se almacenan los datos de discos de máquinas virtuales en unidades de disco duro (HDD) en lugar de SSD. Puede migrar los discos de máquinas virtuales a discos premium. Los discos premium son compatibles con la mayoría de las SKU de máquinas virtuales. Sin embargo, en algunos casos, si desea usar discos premium, es posible que deba actualizar también las SKU de máquina virtual.

![Recomendación de Advisor: mejore la confiabilidad de los discos de máquina virtual actualizándolos a discos premium](./media/advisor-high-availability-recommendations/advisor-high-availability-upgrade-to-premium-disks.png)

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Protección de los datos de máquinas virtuales contra la eliminación accidental
Advisor identifica las máquinas virtuales que no tienen la copia de seguridad habilitada y recomienda habilitarla. La configuración de la copia de seguridad de máquinas virtuales garantiza la disponibilidad de los datos críticos y ofrece protección contra la eliminación accidental o los daños.

![Recomendación de Advisor: configure la copia de seguridad de máquinas virtuales para proteger los datos críticos](./media/advisor-high-availability-recommendations/advisor-high-availability-virtual-machine-backup.png)

## <a name="access-high-availability-recommendations-in-advisor"></a>Acceso a las recomendaciones sobre alta disponibilidad en Advisor

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el panel izquierdo, haga clic en **Más servicios**.

3. En el panel de menú de servicio, en **Supervisión y administración**, haga clic en **Azure Advisor**.  
 Se muestra el panel de Advisor.

4. En el panel de Advisor, haga clic en la pestaña **Alta disponibilidad** y seleccione la suscripción para la que desea recibir recomendaciones.

> [!NOTE]
> Para acceder a las recomendaciones de Advisor, primero debe *registrar su suscripción* en dicha solución. Una suscripción se registra cuando el *propietario de esta* inicia el panel de Advisor y hace clic en el botón **Obtener recomendaciones**. Esta operación *solo se realiza una vez*. Una vez registrada la suscripción, puede acceder a las recomendaciones de Advisor como *Propietario*, *Contribuidor* o *Lector* para una suscripción, un grupo de recursos o un recurso concreto.

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de las recomendaciones de Advisor, consulte:
* [Introducción a Azure Advisor](advisor-overview.md)
* [Introducción a Advisor](advisor-get-started.md)
* [Recomendaciones sobre el costo de Advisor](advisor-performance-recommendations.md)
* [Recomendaciones sobre rendimiento de Advisor](advisor-performance-recommendations.md)
* [Recomendaciones sobre seguridad de Advisor](advisor-security-recommendations.md)

