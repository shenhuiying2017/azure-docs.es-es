---
title: "Ejemplo de script de la CLI Azure: equilibrio de carga del tráfico a las máquinas virtuales para lograr una alta disponibilidad | Microsoft Docs"
description: "Ejemplo de script de la CLI Azure: equilibrio de carga del tráfico a las máquinas virtuales para lograr una alta disponibilidad"
services: load-balancer
documentationcenter: load-balancer
author: KumudD
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 04/21/2017
ms.author: kumud
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 07c16b63ff6af172ea68eb60cc7bf8109d2cd2d7
ms.contentlocale: es-es
ms.lasthandoff: 06/07/2017

---

# <a name="load-balance-traffic-to-vms-for-high-availability"></a>Equilibrio de carga del tráfico a las máquinas virtuales para lograr una alta disponibilidad

Este ejemplo de script crea todo lo necesario para ejecutar varias máquinas virtuales Ubuntu configuradas con valores de alta disponibilidad y equilibrio de carga. Después de ejecutar el script, tendrá tres máquinas virtuales unidas en un conjunto de disponibilidad de Azure y accesibles mediante Azure Load Balancer. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.sh "Creación rápida de una máquina virtual")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Ejecute el siguiente comando para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un grupo de recursos, una máquina virtual, un conjunto de disponibilidad, un equilibrador de carga y todos los recursos relacionados. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create) | Crea una red virtual y una subred de Azure. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#create) | Crea una dirección IP pública con una dirección IP estática y un nombre DNS asociado. |
| [az network lb create](https://docs.microsoft.com/cli/azure/network/lb#create) | Crea una instancia de Azure Load Balancer. |
| [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe#create) | Crea un sondeo de Load Balancer. Un sondeo de Load Balancer se usa para supervisar cada máquina virtual en el conjunto de Load Balancer. Si alguna máquina virtual deja de estar accesible, el tráfico no se enruta a la máquina virtual. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#create) | Crea una regla de Load Balancer. En este ejemplo, se crea una regla para el puerto 80. Según va llegando el tráfico HTTP a Load Balancer, se enruta al puerto 80 de una de las máquinas virtuales del conjunto de Load Balancer. |
| [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#create) | Crea una regla de traducción de direcciones de red (NAT) de Load Balancer.  Las reglas de NAT asignan un puerto de Load Balancer a un puerto en una máquina virtual. En este ejemplo, se crea una regla NAT para el tráfico SSH para cada máquina virtual del conjunto de Load Balancer.  |
| [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#create) | Crea un grupo de seguridad de red (NSG), que es un límite de seguridad entre Internet y la máquina virtual. |
| [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule#create) | Crea una regla de NSG para permitir el tráfico entrante. En este ejemplo, el puerto 22 está abierto al tráfico SSH. |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic#create) | Crea una tarjeta de máquina virtual y la conecta con la red virtual, la subred y el NSG. |
| [az vm availability-set create](https://docs.microsoft.com/cli/azure/network/lb/rule#create) | Crea un conjunto de disponibilidad. Los conjuntos de disponibilidad garantizan la disponibilidad de las aplicaciones al repartir las máquinas virtuales entre los recursos físicos, de forma que si se produce un error, el conjunto no se verá afectado en su totalidad. |
| [az vm create](/cli/azure/vm#create) | Crea la máquina virtual y la conecta con la tarjeta de red, la red virtual, la subred y el NSG. Este comando también especifica la imagen de máquina virtual que se usará, y las credenciales administrativas.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de la CLI de Redes de Azure en la [documentación de Redis de Azure](../cli-samples.md).
