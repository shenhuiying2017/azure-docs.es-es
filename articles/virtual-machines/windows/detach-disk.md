---
title: "Desconexión de un disco de datos de una máquina virtual Windows: Azure | Microsoft Docs"
description: "Aprenda a desconectar un disco de datos de una máquina virtual de Azure creada mediante el modelo de implementación de Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 7013e7ff3cb14dcad8e3e9a926bcee771180259d
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/18/2017
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Desacoplamiento de un disco de datos de una máquina virtual de Windows
Cuando ya no necesite un disco de datos que se encuentra conectado a una máquina virtual, puede desconectarlo fácilmente. Esto quita el disco de la máquina virtual, pero no lo quita del almacenamiento.

> [!WARNING]
> Si se desconecta un disco, no se elimina automáticamente. Si se ha suscrito a Almacenamiento premium, seguirá acumulando cargos de almacenamiento por el disco. Para más información, consulte [Precios y facturación](premium-storage.md#pricing-and-billing)cuando se utiliza Premium Storage.
>
>

Si desea volver a usar los datos existentes en el disco, puede acoplarlo de nuevo a la misma máquina virtual (o a otra).

## <a name="detach-a-data-disk-using-the-portal"></a>Desconexión de un disco de datos mediante el portal

1. En el menú de la izquierda, seleccione **Máquinas virtuales**.
2. Seleccione la máquina virtual que tiene el disco de datos que quiere desconectar y haga clic en **Detener** para desasignar la máquina virtual.
3. En el panel de la máquina virtual, seleccione **Discos**.
4. En la parte superior del panel **Discos**, seleccione **Editar**.
5. En el panel **Discos**, en el extremo derecho del disco de datos que quiere desasociar, haga clic en el ![imagen del botón Desasociar](./media/detach-disk/detach.png) botón de desasociación.
5. Una vez que haya quitado el disco, haga clic en **Guardar**, en la parte superior del panel.
6. En el panel de la máquina virtual, haga clic en **Información general** y, luego, en el botón **Iniciar** de la parte superior del panel para reiniciar la máquina virtual.



El disco permanece en el almacenamiento pero ya no está acoplado a una máquina virtual.

## <a name="detach-a-data-disk-using-powershell"></a>Desconexión de un disco de datos con PowerShell
En este ejemplo, el primer comando obtiene la máquina virtual denominada **MyVM07** en el grupo de recursos **RG11** mediante el cmdlet [Get-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) y la almacena en la variable **$VirtualMachine**.

La segunda línea quita el disco de datos denominado DataDisk3 de la máquina virtual mediante el cmdlet [Remove-AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk).

La tercera línea actualiza el estado de la máquina virtual mediante el cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) para completar el proceso de eliminación del disco de datos.

```azurepowershell-interactive
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07"
Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
Update-AzureRmVM -ResourceGroupName "RG11" -VM $VirtualMachine
```

Para obtener más información, consulte [Remove-AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk).

## <a name="next-steps"></a>Pasos siguientes
Si desea reutilizar el disco de datos, basta con que lo [conecte a otra máquina virtual](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

