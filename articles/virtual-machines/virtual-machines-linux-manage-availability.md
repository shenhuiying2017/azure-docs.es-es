---
title: "Administración de la disponibilidad de máquinas virtuales Linux en Azure | Microsoft Docs"
description: "Aprenda a utilizar varias máquinas virtuales para garantizar la alta disponibilidad de su aplicación Linux en Azure."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 891c852a-84c0-4940-a61e-ada6e185bf37
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/14/2016
ms.author: cynthn
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 73829ac6b0fecfcb524738ecde36b1b524ccb120
ms.lasthandoff: 03/03/2017


---

# <a name="manage-the-availability-of-linux-virtual-machines"></a>Manage the availability of Linux virtual machines (Administración de la disponibilidad de las máquinas virtuales con Linux)

Aprenda a configurar y administrar varias máquinas virtuales para garantizar una alta disponibilidad de la aplicación Linux en Azure. También puede [administrar la disponibilidad de máquinas virtuales Windows](virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Para obtener instrucciones sobre cómo crear un conjunto de disponibilidad mediante la CLI en el modelo de implementación de Resource Manager, consulte [azure availset: comandos para administrar los conjuntos de disponibilidad](azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets).

[!INCLUDE [virtual-machines-common-manage-availability](../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre el equilibrio de carga de las máquinas virtuales, consulte [Equilibrio de carga para servicios de infraestructura de Azure](virtual-machines-linux-load-balance.md).


