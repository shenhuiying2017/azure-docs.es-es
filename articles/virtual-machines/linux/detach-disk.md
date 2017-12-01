---
title: "Desconexión de un disco de una máquina virtual Linux en Azure | Microsoft Docs"
description: "Obtenga información sobre cómo desconectar un disco de datos de una máquina virtual de Azure creada mediante la CLI 2.0 o Azure Portal."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: c589dd8c9d597145fd87a00d9a2ba040988cd8ec
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/18/2017
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Desconexión de un disco de datos de una máquina virtual Linux

Cuando ya no necesite un disco de datos que se encuentra conectado a una máquina virtual, puede desconectarlo fácilmente. Esto quita el disco de la máquina virtual, pero no lo quita del almacenamiento. 

> [!WARNING]
> Si se desconecta un disco, no se elimina automáticamente. Si se ha suscrito a Almacenamiento premium, seguirá acumulando cargos de almacenamiento por el disco. Para más información, consulte [Precios y facturación](../windows/premium-storage.md#pricing-and-billing)cuando se utiliza Premium Storage. 
> 
> 

Si desea volver a usar los datos existentes en el disco, puede acoplarlo de nuevo a la misma máquina virtual (o a otra).  

## <a name="detach-a-data-disk-using-cli-20"></a>Desconexión de un disco de datos con la CLI de 2.0

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

El disco permanece en el almacenamiento pero ya no está acoplado a una máquina virtual.


## <a name="detach-a-data-disk-using-the-portal"></a>Desconexión de un disco de datos mediante el portal
1. En el menú de la izquierda, seleccione **Máquinas virtuales**.
2. Seleccione la máquina virtual que tiene el disco de datos que quiere desconectar y haga clic en **Detener** para desasignar la máquina virtual.
3. En el panel de la máquina virtual, seleccione **Discos**.
4. En la parte superior del panel **Discos**, seleccione **Editar**.
5. En el panel **Discos**, en el extremo derecho del disco de datos que quiere desasociar, haga clic en el ![imagen del botón Desasociar](./media/detach-disk/detach.png) botón de desasociación.
5. Una vez que haya quitado el disco, haga clic en Guardar, en la parte superior del panel.
6. En el panel de la máquina virtual, haga clic en **Información general** y, luego, en el botón **Iniciar** de la parte superior del panel para reiniciar la máquina virtual.

El disco permanece en el almacenamiento pero ya no está acoplado a una máquina virtual.


## <a name="next-steps"></a>Pasos siguientes
Si desea reutilizar el disco de datos, basta con que lo [conecte a otra máquina virtual](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

