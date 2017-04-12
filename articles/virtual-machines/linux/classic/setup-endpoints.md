---
title: "Configuración de puntos de conexión en una máquina virtual Linux (modelo clásico) | Microsoft Docs"
description: "Aprenda a configurar puntos de conexión para una máquina virtual Linux en el Portal de Azure clásico para permitir la comunicación con una máquina virtual Linux en Azure."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: f3749738-1109-4a1d-8635-40e4bd220e91
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: c5fbdaacca1a946a94f2af1dada953a0d9c8e011
ms.lasthandoff: 04/03/2017


---
# <a name="how-to-set-up-endpoints-on-a-linux-classic-virtual-machine-in-azure"></a>Cómo configurar puntos de conexión en máquinas virtuales Linux clásicas en Azure
Todas las máquinas virtuales con Linux que se crean en Azure con el modelo de implementación clásico pueden comunicarse automáticamente a través de un canal de red privado con otras máquinas virtuales del mismo servicio en la nube o de la misma red virtual. Sin embargo, los equipos en Internet o en otras redes virtuales necesitan extremos para dirigir el tráfico de red entrante a una máquina virtual. Este artículo también está disponible para [máquinas virtuales Windows](../../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos.

En el modelo de implementación de **Resource Manager**, los puntos de conexión se configuran mediante **grupos de seguridad de red (NSG)**. Para más información, consulte [Apertura de puertos y puntos de conexión](../nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Cuando se crea una máquina virtual de Linux en el Portal de Azure clásico, se suelen crear automáticamente puntos de conexión comunes como de Shell seguro (SSH). Puede configurar extremos adicionales al crear la máquina virtual o posteriormente, según sea necesario.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Pasos siguientes
* También se puede crear un punto de conexión mediante la [interfaz de la línea de comandos de Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2). Ejecute el comando **azure vm endpoint create** .
* Si ha creado una máquina virtual en el modelo de implementación de Resource Manager, también puede usar la CLI de Azure en modo de Resource Manager para [crear grupos de seguridad de la red](../../../virtual-network/virtual-networks-create-nsg-arm-cli.md) con el fin de controlar el tráfico en la máquina virtual.


