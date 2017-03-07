---
title: "Ejemplo de script de la CLI de Azure: creación de una máquina virtual Windows Server 2016 con NLB | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: creación de una máquina virtual Windows Server 2016 con NLB"
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-Windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 82d40c30c92f5da090e7ec4e2f25ead3908cc603
ms.openlocfilehash: 4437ed8a1d32b1d56a48b88051189daab35f02ac
ms.lasthandoff: 03/02/2017

---

# <a name="load-balance-traffic-between-highly-available-virtual-machines"></a>Equilibrio de la carga de tráfico entre máquinas virtuales de alta disponibilidad

Este ejemplo de script crea todo lo necesario para ejecutar varias máquinas virtuales Windows Server 2016 configuradas con valores de alta disponibilidad y equilibrio de carga. El resultado de este script son tres máquinas virtuales unidas en un conjunto de disponibilidad de Azure y accesibles mediante Azure Load Balancer. Este ejemplo funciona en un shell de Bash.

Antes de ejecutar este script, asegúrese de que se haya creado una conexión con Azure mediante el comando `az login`. Además, debe cambiar la variable $AdminPassword al principio del script por una contraseña única para cumplir los requisitos de complejidad.

Este ejemplo funciona en un shell de Bash. Para ver las opciones de ejecución de scripts de la CLI de Azure en Windows, consulte [Using the Azure CLI on Windows](../virtual-machines-windows-cli-options.md) (Uso de la CLI de Azure en Windows).

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-vm-nlb/create-windows-vm-nlb.sh "Creación rápida de una máquina virtual")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un grupo de recursos, una máquina virtual, un conjunto de disponibilidad, un equilibrador de carga y todos los recursos relacionados. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create) | Crea una red virtual y una subred de Azure. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#create) | Crea una dirección IP pública con una dirección IP estática y un nombre DNS asociado. |
| [az network lb create](https://docs.microsoft.com/cli/azure/network/lb#create) | Crea un equilibrador de carga de red de Azure (NLB). |
| [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe#create) | Crea un sondeo de NLB. Se utiliza una prueba de NLB para supervisar cada máquina virtual en el conjunto de NLB. Si alguna máquina virtual deja de estar accesible, el tráfico no se enruta a la máquina virtual. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#create) | Crea una regla de NLB. En este ejemplo, se crea una regla para el puerto 80. Según va llegando el tráfico HTTP a NLB, se enruta al puerto 80 de una de las máquinas virtuales del conjunto de NLB. |
| [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#create) | Crea una regla de traducción de direcciones de red (NAT) de NLB.  Las reglas de NAT asignan un puerto de NLB a un puerto en una máquina virtual. En este ejemplo, se crea una regla NAT para el tráfico SSH para cada máquina virtual del conjunto de NLB.  |
| [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#create) | Crea un grupo de seguridad de red (NSG), que es un límite de seguridad entre Internet y la máquina virtual. |
| [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule#create) | Crea una regla de NSG para permitir el tráfico entrante. En este ejemplo, el puerto 22 está abierto al tráfico SSH. |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic#create) | Crea una tarjeta de máquina virtual y la conecta con la red virtual, la subred y el NSG. |
| [az vm availability-set create](https://docs.microsoft.com/cli/azure/network/lb/rule#create) | Crea un conjunto de disponibilidad. Los conjuntos de disponibilidad garantizan la disponibilidad de las aplicaciones al repartir las máquinas virtuales entre los recursos físicos, de forma que si se produce un error, el conjunto no se verá afectado en su totalidad. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#create) | Crea la máquina virtual y la conecta con la tarjeta de red, la red virtual, la subred y el NSG. Este comando también especifica la imagen de máquina virtual que se usará, y las credenciales administrativas.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de la CLI de máquina virtual en la [documentación sobre máquinas virtuales Windows de Azure](../virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

