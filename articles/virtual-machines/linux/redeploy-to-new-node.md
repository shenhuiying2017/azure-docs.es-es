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
ms.devlang: azurecli
ms.topic: support-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/23/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 7a8653a82775e718c38f65f246d997ba61f99d58
ms.contentlocale: es-es
ms.lasthandoff: 06/26/2017


---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Nueva implementación de una máquina virtual Linux en un nuevo nodo de Azure
Si encuentra dificultades para solucionar los problemas de acceso de SSH o de las aplicaciones a una máquina virtual (VM) Linux en Azure, puede que lo ayude volverla a implementar. Cuando se vuelve a implementar una máquina virtual, se mueve a otro nodo dentro de la infraestructura de Azure y después se vuelve a conectar. Se conservan todas las opciones de configuración y los recursos asociados. En este artículo se muestra cómo volver a implementar una máquina virtual con la CLI de Azure o el Portal de Azure.

> [!NOTE]
> Después de volver a implementar una máquina virtual, se perderá el disco temporal y se actualizarán las direcciones IP dinámicas asociadas con la interfaz de red virtual. 

Puede volver a implementar una máquina virtual mediante una de las siguientes opciones. Solo tiene que elegir una opción para volver a implementar la máquina virtual:

- [CLI de Azure 2.0](#azure-cli-20)
- [CLI de Azure 1.0](#azure-cli-10)
- [Portal de Azure](#using-azure-portal)

## <a name="use-the-azure-cli-20"></a>Uso de la CLI de Azure 2.0
Instale la última versión de la [CLI de Azure 2.0](/cli/azure/install-az-cli2) e inicie sesión en una cuenta de Azure con [az login](/cli/azure/#login).

Vuelva a implementar la máquina virtual con [az vm redeploy](/cli/azure/vm#redeploy). En el ejemplo siguiente se reimplementa la máquina virtual llamada *myVM* en el grupo de recursos denominado *myResourceGroup*:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-cli-10"></a>Uso de la CLI de Azure 1.0
Instale la [CLI de Azure 1.0 más reciente ](../../cli-install-nodejs.md), inicie sesión en una cuenta de Azure y asegúrese de que está en modo Resource Manager (`azure config mode arm`).

En el ejemplo siguiente se reimplementa la máquina virtual llamada *myVM* en el grupo de recursos denominado *myResourceGroup*:

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Pasos siguientes
Puede encontrar ayuda específica sobre la [solución de problemas de las conexiones SSH](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o [pasos detallados para solucionar problemas de SSH](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) si tiene problemas para conectarse a la máquina virtual. También puede leer sobre la [solución de problemas de aplicaciones](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) si no puede acceder a una aplicación que se ejecuta en la máquina virtual.


