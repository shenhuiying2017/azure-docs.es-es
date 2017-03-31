---
title: "Creación de una máquina virtual Linux mediante la CLI de Azure 1.0 | Microsoft Docs"
description: "Aprenda a crear una máquina virtual Linux con la CLI de Azure 1.0 mediante el modelo de implementación clásica"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: f8071a2e-ed91-4f77-87d9-519a37e5364f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: c4d29c453d22a2765f0884c11d98d84676b3bf32
ms.lasthandoff: 03/27/2017


---
# <a name="how-to-create-a-classic-linux-vm-with-the-azure-cli-10"></a>Creación de una máquina virtual Linux con la CLI de Azure 1.0
> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos. Para la versión de Resource Manager, consulte [aquí](../../virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

En este tema se describe cómo crear una máquina virtual (VM) Linux con la CLI de Azure 1.0 mediante el modelo de implementación clásica. Vamos a usar una imagen de Linux de una de las **IMÁGENES** disponibles en Azure. Los comandos de la CLI de Azure 1.0 ofrecen, entre otras, las siguientes opciones de configuración:

* Conexión de la VM a una red virtual
* Adición de la VM a un servicio en la nube existente
* Incorporación de la máquina virtual a una cuenta de almacenamiento existente
* Incorporación de la máquina virtual a un conjunto de disponibilidad o una ubicación

> [!IMPORTANT]
> Si desea que la máquina virtual use una red virtual, con el fin de poder conectarse a ella directamente mediante un nombre de host o configurar conexiones entre locales, asegúrese de que especifica la red virtual al crear la máquina virtual. Puede configurarse una máquina virtual para que se una a una red virtual solo cuando se cree la máquina virtual. Para obtener detalles acerca de las redes virtuales, consulte [Información general sobre redes virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).
> 
> 

## <a name="how-to-create-a-linux-vm-using-the-classic-deployment-model"></a>Creación de una máquina virtual Linux mediante el modelo de implementación clásica
[!INCLUDE [virtual-machines-create-LinuxVM](../../../../includes/virtual-machines-create-linuxvm.md)]


