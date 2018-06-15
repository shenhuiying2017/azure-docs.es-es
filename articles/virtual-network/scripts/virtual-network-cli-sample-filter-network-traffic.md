---
title: 'Ejemplo de script de la CLI de Azure: filtrar el tráfico de red de VM | Microsoft Docs'
description: 'Ejemplo de script de la CLI de Azure: filtrar el tráfico de red de VM entrante y saliente.'
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: 8e667b2ffd8820b78cbba560724527ba2c2b3bc4
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
ms.locfileid: "30840334"
---
# <a name="filter-inbound-and-outbound-vm-network-traffic-script-sample"></a>Ejemplo de script para filtrar el tráfico de red entrante y saliente de una máquina virtual

Este ejemplo de script crea una red virtual con subredes de front-end y back-end. El tráfico de red entrante hacia la subred de front-end se limita a HTTP, HTTPS y SSH, mientras que el tráfico saliente hacia Internet desde la subred back-end no se permite. Después de ejecutar el script, tendrá una máquina virtual con dos NIC. Cada NIC se conecta a una subred diferente.

Puede ejecutar el script desde Azure [Cloud Shell](https://shell.azure.com/bash) o desde una instalación de CLI de Azure local. Si usa la CLI localmente, este script requiere que ejecute la versión 2.0.28 o posterior. Ejecute `az --version` para ver cuál es la versión instalada. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli). Si ejecuta la CLI localmente, también debe ejecutar `az login` para crear una conexión con Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/filter-network-traffic/filter-network-traffic.sh  "Filter VM network traffic")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Ejecute el siguiente comando para quitar el grupo de recursos, la VM y todos los recursos relacionados:

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un grupo de recursos, una red virtual y grupos de seguridad de red. Cada comando de la tabla siguiente crea un vínculo a documentación específica del comando:

| Get-Help | Notas |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Crea una subred de front-end y una red virtual de Azure. |
| [az network subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Crea una subred de back-end. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) | Asocia NSG a subredes. |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | Crea una dirección IP pública para acceder a la VM desde Internet. |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | Crea interfaces de red virtual y las asocia a subredes de front-end y back-end de la red virtual. |
| [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) | Crea grupos de seguridad de red (NSG) que están asociados a las subredes de front-end y back-end. |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) |Crea reglas NSG que permiten o bloquean puertos específicos para subredes concretas. |
| [az vm create](/cli/azure/vm#az_vm_create) | Crea máquinas virtuales se crea y asocia una NIC a cada VM. Este comando también especifica la imagen de máquina virtual que se usará y las credenciales administrativas. |
| [az group delete](/cli/azure/group#az_group_delete) | Elimina un grupo de recursos y todos los recursos que contiene. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](/cli/azure).

Puede encontrar más ejemplos de script de CLI de red virtual en [ejemplos de CLI de red virtual](../cli-samples.md).
