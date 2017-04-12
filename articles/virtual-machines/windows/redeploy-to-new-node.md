---
title: "Nueva implementación de máquinas virtuales Windows en Azure | Microsoft Docs"
description: "Cómo volver a implementar máquinas virtuales Windows en Azure para solucionar problemas de conexión RDP."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/16/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 7ca51ce26e009d18b4519a5e7ba16df64ec984ef
ms.lasthandoff: 03/31/2017


---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Nueva implementación de máquinas virtuales Windows en un nuevo nodo de Azure
Si ha tenido dificultades para solucionar problemas con la conexión a Escritorio remoto (RDP) o el acceso de aplicaciones a una maquina virtual de Azure basada en Windows, puede servir de ayuda que repita la implementación de la máquina virtual. Cuando se vuelve a implementar una máquina virtual, se mueve a otro nodo dentro de la infraestructura de Azure y después se vuelve a conectar, conservando todas las opciones de configuración y los recursos asociados. En este artículo se muestra cómo volver a implementar una máquina virtual con Azure PowerShell o el Portal de Azure.

> [!NOTE]
> Después de volver a implementar una máquina virtual, se perderá el disco temporal y se actualizarán las direcciones IP dinámicas asociadas con la interfaz de red virtual. 


## <a name="using-azure-powershell"></a>Uso de Azure PowerShell
Asegúrese de que tiene la versión más reciente de Azure PowerShell 1.x instalada en su equipo. Para más información, vea [Instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs).

En el ejemplo siguiente se implementa la máquina virtual llamada `myVM` en el grupo de recursos denominado `myResourceGroup`:

```powershell
Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```


[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Pasos siguientes
Puede encontrar ayuda específica sobre la [solución de problemas de las conexiones RDP](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) o [pasos detallados para solucionar problemas de RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) si tiene problemas para conectarse a su VM. También puede leer sobre la [solución de problemas de aplicaciones](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) si no puede acceder a una aplicación que se ejecuta en la máquina virtual.


