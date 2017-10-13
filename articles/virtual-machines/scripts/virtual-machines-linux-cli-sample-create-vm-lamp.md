---
title: "Ejemplo de script de la CLI de Azure: implementación de la pila de LAMP en un conjunto de escalado de máquinas virtuales de carga equilibrada | Microsoft Docs"
description: "Use una extensión de script personalizado para implementar la pila de LAMP en un conjunto de escalado de máquinas virtuales de carga equilibrada en Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2017
ms.author: allclark
ms.custom: mvc
ms.openlocfilehash: 23170923d7c05c9b7230cf331725250b2a3c0f09
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-the-lamp-stack-in-a-load-balanced-virtual-machine-scale-set"></a>Implementación de la pila de LAMP en un conjunto de escalado de máquinas virtuales de carga equilibrada

En este ejemplo se crea un conjunto de escalado de máquinas virtuales y se aplica una extensión que ejecuta un script personalizado para implementar la pila de LAMP en cada máquina virtual del conjunto de escalado.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/build-stack.sh "Create virtual machine scale set with LAMP stack")]

## <a name="connect"></a>Conectar

Use este código para ver cómo conectarse a las máquinas virtuales y al conjunto de escalado.

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/how-to-access.sh "Access the virtual machine scale set")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Ejecute el siguiente comando para quitar el grupo de recursos, el conjunto de escalado y las máquinas virtuales, y todos los recursos relacionados.

```azurecli-interactive 
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un grupo de recursos, una máquina virtual, un conjunto de disponibilidad, un equilibrador de carga y todos los recursos relacionados. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az vmss create](https://docs.microsoft.com/cli/azure/vmss#az_vmss_create) | Crea un conjunto de escalado de máquinas virtuales. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Agrega un punto de conexión de carga equilibrada. |
| [az vmss extension set](https://docs.microsoft.com/cli/azure/vmss/extension#az_vmss_extension_set) | Crea la extensión que ejecuta el script personalizado en la implementación de una máquina virtual. |
| [az vmss update-instances](https://docs.microsoft.com/cli/azure/vmss#az_vmss_update_instances) | Ejecuta el script personalizado en las instancias de máquinas virtuales que se implementaron antes de aplicar la extensión al conjunto de escalado. |
| [az vmss scale](https://docs.microsoft.com/cli/azure/vmss#az_vmss_scale) | Permite escalar verticalmente el conjunto de escalado agregando más instancias de máquinas virtuales. El script personalizado se ejecuta en esas instancias cuando se implementa. |
| [az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_list) | Permite obtener las direcciones IP de las máquinas virtuales creadas por el ejemplo. |
| [az network lb show](https://docs.microsoft.com/cli/azure/network/lb#az_network_lb_show) | Permite obtener los puertos de front-end y back-end utilizados por el equilibrador de carga. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de la CLI de máquina virtual en la [documentación sobre máquinas virtuales Linux de Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
