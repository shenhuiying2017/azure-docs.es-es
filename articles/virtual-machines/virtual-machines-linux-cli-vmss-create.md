---
title: "¿Qué son los conjuntos de escalado de máquina virtual? | Microsoft Docs"
description: "Obtenga información sobre los conjuntos de escalado de máquina virtual."
keywords: "máquina virtual Linux,conjuntos de escalado de máquina virtual"
services: virtual-machines-linux
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba1aedb6-49cb-4546-8b8b-da97aba8e42d
ms.service: virtual-machine-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/24/2016
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 9ee2d1d4e50a5bf4c146c48024aae5f10cea32ef


---
# <a name="what-are-virtual-machine-scale-sets"></a>¿Qué son los conjuntos de escalado de máquina virtual?
Gracias a los conjuntos de escalado de máquina virtual, podrá administrar varias máquinas virtuales como un conjunto. A nivel general, los conjuntos de escalado presentan las siguientes ventajas y desventajas:

Ventajas:

1. Alta disponibilidad. Cada conjunto de escalado coloca sus máquinas virtuales en un conjunto de disponibilidad con 5 dominios de error (FD) y 5 dominios de actualización (UD) para garantizar la disponibilidad (para más información sobre los dominios de error y de actualización, consulte la sección sobre [disponibilidad de las máquinas virtuales](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). 
2. Integración sencilla con el Equilibrador de carga y la Puerta de enlace de aplicaciones de Azure.
3. Integración de aplicaciones con el escalado automático de Azure.
4. Implementación, administración y limpieza de máquinas virtuales simplificadas.
5. Compatibilidad con los tipos comunes de Windows y Linux, así como con imágenes personalizadas.

Desventajas:

1. No se puede adjuntar discos de datos a instancias de máquina virtual en un conjunto de escala. En su lugar, debe utilizar Almacenamiento de blobs, Archivos de Azure, tablas de Azure u otra solución de almacenamiento.

## <a name="quick-create-using-azure-cli"></a>Creación rápida mediante CLI de Azure
[!INCLUDE [cli-vmss-quick-create](../../includes/virtual-machines-linux-cli-vmss-quick-create-include.md)]

## <a name="next-steps"></a>Pasos siguientes
Para obtener información general, consulte la [página de aterrizaje principal de los conjuntos de escalado](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

Para consultar más documentación, consulte la [página de documentación principal de los conjuntos de escalado](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

Para las plantillas de Azure Resource Manager de ejemplo que usan conjuntos de escalado, busque "vmss" en el [repositorio de GitHub de plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates).




<!--HONumber=Nov16_HO3-->


