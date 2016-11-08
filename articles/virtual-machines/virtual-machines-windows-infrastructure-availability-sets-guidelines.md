---
title: Directrices de conjuntos de disponibilidad | Microsoft Docs
description: Obtenga información sobre las directrices clave de diseño e implementación para implementar conjuntos de disponibilidad en los servicios de infraestructura de Azure.
documentationcenter: ''
services: virtual-machines-windows
author: iainfoulds
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/08/2016
ms.author: iainfou

---
# Availability sets guidelines (Directrices de conjuntos de disponibilidad)
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

Este artículo se centra en describir los pasos de planeación necesarios para que los conjuntos de disponibilidad garanticen que sus aplicaciones permanezcan accesibles durante eventos planeados o no planeados.

## Directrices de implementación para los conjuntos de disponibilidad
Decisiones:

* ¿Cuántos conjuntos de disponibilidad necesita para los distintos roles y niveles de la infraestructura de la aplicación?

Tareas:

* Defina el número de máquinas virtuales en cada nivel de aplicación que necesite.
* Determine si necesita ajustar el número de dominios de error o actualización que se va a usar para la aplicación.
* Defina los conjuntos de disponibilidad necesarios usando su convención de nomenclatura y las máquinas virtuales que residirán en ellos. Una máquina virtual solo puede residir en un conjunto de disponibilidad.

## Conjuntos de disponibilidad
En Azure, las máquinas virtuales (VM) se pueden colocar en una agrupación lógica llamada conjunto de disponibilidad. Al crear máquinas virtuales dentro de un conjunto de disponibilidad, la plataforma de Azure distribuirá la ubicación de esas máquinas virtuales a través de la infraestructura subyacente. Si hubiera un evento de mantenimiento planeado para la plataforma de Azure o un error de hardware o infraestructura subyacente, el uso de conjuntos de disponibilidad garantiza que al menos una máquina virtual siguiera ejecutándose.

Como procedimiento recomendado, las aplicaciones no deben residir en una sola máquina virtual. Un conjunto de disponibilidad que contiene una sola máquina virtual no obtiene ninguna protección por parte de eventos planeados o no planeados dentro de la plataforma de Azure. El [SLA de Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines) requiere dos o más máquinas virtuales dentro de un conjunto de disponibilidad para permitir la distribución de máquinas virtuales a través de la infraestructura subyacente.

La infraestructura subyacente de Azure se divide en dominios de actualización y dominios de error. Estos dominios se definen teniendo en cuenta qué hosts compartirán un ciclo de actualización común o una infraestructura física similar como la energía y redes. Azure distribuirá automáticamente las máquinas virtuales dentro de un conjunto de disponibilidad en los dominios para mantener la disponibilidad y tolerancia a errores. Dependiendo del tamaño de la aplicación y el número de máquinas virtuales dentro de un conjunto de disponibilidad, puede ajustar el número de dominios que desea usar. Puede leer más sobre la [administración de la disponibilidad y uso de los dominios de actualización y error](virtual-machines-windows-manage-availability.md).

Al diseñar la infraestructura de la aplicación, también debe planear los niveles de aplicación que usará. Agrupe las máquinas virtuales que prestan el mismo servicio a conjuntos de disponibilidad, tales como un conjunto de disponibilidad para las máquinas virtuales de front-end que ejecutan IIS. Cree un conjunto de disponibilidad independiente para las máquinas virtuales de back-end que ejecutan SQL Server. El objetivo es garantizar que un conjunto de disponibilidad proteja cada uno de los componentes de la aplicación y que la instancia siga ejecutándose al menos una vez.

Los equilibradores de carga pueden usarse delante de cada nivel de aplicación junto con un conjunto de disponibilidad para garantizar que el tráfico pueda enrutarse siempre a una instancia en ejecución. Sin un equilibrador de carga, las máquinas virtuales pueden seguir ejecutándose durante los eventos de mantenimiento planeados y no planeados, pero es posible que los usuarios finales no puedan resolverlos si la máquina virtual principal no está disponible.

## Pasos siguientes
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]

<!---HONumber=AcomDC_0914_2016-->