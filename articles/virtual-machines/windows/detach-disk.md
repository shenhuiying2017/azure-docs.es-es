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
ms.date: 03/21/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 36cc060273ec4e402a4eecc877ed41889d9ce8ce
ms.lasthandoff: 03/31/2017


---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Desacoplamiento de un disco de datos de una máquina virtual de Windows
Cuando ya no necesite un disco de datos que se encuentra conectado a una máquina virtual, puede desconectarlo fácilmente. Esto quita el disco de la máquina virtual, pero no lo quita del almacenamiento. 

> [!WARNING]
> Si se desconecta un disco, no se elimina automáticamente. Si se ha suscrito a Almacenamiento premium, seguirá acumulando cargos de almacenamiento por el disco. Para más información, consulte [Precios y facturación](../../storage/storage-premium-storage.md#pricing-and-billing)cuando se utiliza Almacenamiento premium. 
> 
> 

Si desea volver a usar los datos existentes en el disco, puede acoplarlo de nuevo a la misma máquina virtual (o a otra).  

## <a name="detach-a-data-disk-using-the-portal"></a>Desconexión de un disco de datos mediante el portal
1. En el centro del portal, seleccione **Máquinas virtuales**.
2. Seleccione la máquina virtual que tiene el disco de datos que quiere desconectar y haga clic en **Detener** para desasignar la máquina virtual.
3. En la hoja de la máquina virtual, seleccione **Discos**.
4. En la parte superior de la hoja **Discos**, seleccione **Editar**.
5. En la hoja **Discos**, en el extremo derecho del disco de datos que desea desconectar, haga clic en el botón de desconexión ![imagen del botón de desconexión](./media/detach-disk/detach.png).
5. Después de quitar el disco, haga clic en Guardar en la parte superior de la hoja.
6. En la hoja de la máquina virtual, haga clic en **Información general** y, luego, haga clic en el botón **Iniciar** de la parte superior de la hoja para reiniciar la máquina virtual.



El disco permanece en el almacenamiento pero ya no está acoplado a una máquina virtual.

## <a name="detach-a-data-disk-using-powershell"></a>Desconexión de un disco de datos con PowerShell
En este ejemplo, el primer comando obtiene la máquina virtual denominada **MyVM07** en el grupo de recursos **RG11** con el cmdlet Get-AzureRmVM. El comando almacena la máquina virtual en la variable **$VirtualMachine** . 

El segundo comando quita el disco de datos denominado DataDisk3 de la máquina virtual. 

El último comando actualiza el estado de la máquina virtual para completar el proceso de quitar el disco de datos.

```powershell
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" 
Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
Update-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" -VM $VirtualMachine
```

Para obtener más información, consulte [Remove-AzureRmVMDataDisk](/powershell/remove-azurermvmdatadisk).

## <a name="next-steps"></a>Pasos siguientes
Si desea reutilizar el disco de datos, basta con que lo [conecte a otra máquina virtual](attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


