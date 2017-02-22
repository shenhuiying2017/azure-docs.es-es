---
title: "Nueva implementación de máquinas virtuales Linux en Azure | Microsoft Docs"
description: "Cómo volver a implementar máquinas virtuales Linux en Azure para solucionar problemas de conexión SSH."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/16/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 13ae22245d105b32b9fc50d7dbc8a6d50ad4560a
ms.openlocfilehash: 85fcc919b97d4cc06f89fc1ea5dc701ff61c2b27


---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Nueva implementación de una máquina virtual Linux en un nuevo nodo de Azure
Si ha estado teniendo dificultades para solucionar los problemas de acceso de SSH o de las aplicaciones a una máquina virtual (VM) Linux en Azure, puede que lo ayude volverla a implementar. Cuando se vuelve a implementar una máquina virtual, se mueve a otro nodo dentro de la infraestructura de Azure y después se vuelve a conectar, conservando todas las opciones de configuración y los recursos asociados. En este artículo se muestra cómo volver a implementar una máquina virtual con la CLI de Azure o el Portal de Azure.

> [!NOTE]
> Después de volver a implementar una máquina virtual, se perderá el disco temporal y se actualizarán las direcciones IP dinámicas asociadas con la interfaz de red virtual. 

Puede volver a implementar una máquina virtual mediante una de las siguientes opciones. Solo tiene que elegir una opción para volver a implementar la máquina virtual:

- [CLI de Azure 1.0](#azure-cli-10)
- [Versión preliminar de la CLI de Azure 2.0](#azure-cli-20-preview)
- [Portal de Azure](#using-azure-portal)


## <a name="azure-cli-10"></a>CLI de Azure 1.0
Instale la [CLI de Azure 1.0 más reciente ](../xplat-cli-install.md), inicie sesión en una cuenta de Azure y asegúrese de que está en modo Resource Manager (`azure config mode arm`).

En el ejemplo siguiente se vuelve a implementar la máquina virtual llamada `myVM` en el grupo de recursos denominado `myResourceGroup`:

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

## <a name="azure-cli-20-preview"></a>Versión preliminar de la CLI de Azure 2.0
Instale la última versión de la [CLI de Azure 2.0 (versión preliminar)](/cli/azure/install-az-cli2) e inicie sesión en una cuenta de Azure con [az login](/cli/azure/#login).

Vuelva a implementar la máquina virtual con [az vm redeploy](/cli/azure/vm#redeploy). En el ejemplo siguiente se vuelve a implementar la máquina virtual llamada `myVM` en el grupo de recursos denominado `myResourceGroup`:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Pasos siguientes
Puede encontrar ayuda específica sobre la [solución de problemas de las conexiones SSH](virtual-machines-linux-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o [pasos detallados para solucionar problemas de SSH](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) si tiene problemas para conectarse a la máquina virtual. También puede leer sobre la [solución de problemas de aplicaciones](virtual-machines-linux-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) si no puede acceder a una aplicación que se ejecuta en la máquina virtual.




<!--HONumber=Feb17_HO3-->


