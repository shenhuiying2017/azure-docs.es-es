---
title: Administración de la disponibilidad de las máquinas virtuales Linux | Microsoft Docs
description: Aprenda a utilizar varias máquinas virtuales para garantizar la alta disponibilidad de su aplicación Linux en Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/25/2016
ms.author: cynthn

---
# Administración de la disponibilidad de las máquinas virtuales
Aprenda a configurar y administrar varias máquinas virtuales para garantizar una alta disponibilidad de la aplicación Linux en Azure. También puede [administrar la disponibilidad de las máquinas virtuales Windows](virtual-machines-windows-manage-availability.md).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Para obtener instrucciones sobre cómo crear un conjunto de disponibilidad mediante la CLI en el modelo de implementación de Resource Manager, consulte [azure availset: comandos para administrar los conjuntos de disponibilidad](azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets).

[!INCLUDE [virtual-machines-common-manage-availability](../../includes/virtual-machines-common-manage-availability.md)]

## Pasos siguientes
Para más información sobre el equilibrio de carga de las máquinas virtuales, consulte [Equilibrio de carga para servicios de infraestructura de Azure](virtual-machines-linux-load-balance.md).

<!---HONumber=AcomDC_0601_2016-->