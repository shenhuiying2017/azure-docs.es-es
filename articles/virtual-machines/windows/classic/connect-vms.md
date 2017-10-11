---
title: "Conexión de máquinas virtuales Windows en un servicio en la nube | Microsoft Docs"
description: "Conexión de máquinas virtuales Windows creadas con el modelo de implementación clásico con una red virtual o un servicio en la nube de Azure."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: c1cbc802-4352-4d2e-9e49-4ccbd955324b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: cynthn
ms.openlocfilehash: 0c7a21461e5bb111c4359df8e949d48382b591c1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="connect-windows-virtual-machines-created-with-the-classic-deployment-model-with-a-virtual-network-or-cloud-service"></a>Conexión de máquinas virtuales Windows creadas con el modelo de implementación clásico con una red virtual o un servicio en la nube
> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos.

Las máquinas virtuales Windows creadas con el modelo de implementación clásico siempre se colocan en un servicio en la nube. El servicio en la nube actúa como contenedor y proporciona un nombre DNS público único, una dirección IP pública y un conjunto de extremos para acceder a la máquina virtual a través de Internet. El servicio en la nube puede estar en una red virtual, pero esto no es un requisito. También puede [conectar máquinas virtuales creadas con el modelo de implementación clásico con un servicio en la nube o red virtual](../../linux/classic/connect-vms.md).

Si no es un servicio en la nube en una red virtual, se denomina un servicio en la nube *independiente* . Las máquinas virtuales de un servicio en la nube independiente solo pueden comunicarse con otras máquinas virtuales mediante los nombres DNS públicos de otras máquinas virtuales, y el tráfico viaja a través de Internet. Si un servicio en la nube está en una red virtual, las máquinas virtuales de ese servicio en la nube pueden comunicarse con todas las otras máquinas virtuales de la red virtual sin enviar tráfico a través de Internet.

Si coloca las máquinas virtuales en el mismo servicio en la nube independiente, aún puede usar los equilibrios de cargas y los conjuntos de disponibilidad. Para más información, consulte [Máquinas virtuales de equilibrio de carga](../../virtual-machines-windows-load-balance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) y [Administración de la disponibilidad de las máquinas virtuales](../../virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Sin embargo, no se pueden organizar las máquinas virtuales en subredes ni conectar un servicio en la nube independiente a la red local. Este es un ejemplo:

[!INCLUDE [virtual-machines-common-classic-connect-vms](../../../../includes/virtual-machines-common-classic-connect-vms.md)]

## <a name="next-steps"></a>Pasos siguientes
Después de haber creado una máquina virtual, es conveniente [agregar un disco de datos](attach-disk.md) para que los servicios y las cargas de trabajo tengan una ubicación donde almacenar los datos.
