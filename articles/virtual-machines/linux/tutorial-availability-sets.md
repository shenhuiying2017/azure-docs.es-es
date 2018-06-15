---
title: 'Tutorial: Alta disponibilidad para máquinas virtuales Linux en Azure | Microsoft Docs'
description: En este tutorial, aprenderá a usar la CLI de Azure 2.0 para implementar máquinas virtuales de alta disponibilidad en conjuntos de disponibilidad.
documentationcenter: ''
services: virtual-machines-linux
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: tutorial
ms.date: 10/05/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: dc6fba89571515d0d2d7ed3ecc35c3065405056b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32188436"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-the-azure-cli-20"></a>Tutorial: Creación e implementación de máquinas virtuales de alta disponibilidad con la CLI de Azure 2.0

En este tutorial, obtendrá información sobre cómo aumentar la disponibilidad y confiabilidad de las soluciones de máquina virtual en Azure mediante una funcionalidad denominada "conjuntos de disponibilidad". Los conjuntos de disponibilidad garantizan que las máquinas virtuales implementadas en Azure se distribuyan entre varios clústeres de hardware aislados. De este modo, se asegura de que, si se produce un error de hardware o software en Azure, solo un subconjunto de las máquinas virtuales se verá afectado y que la solución seguirá disponible y en funcionamiento.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear un conjunto de disponibilidad
> * Crear una máquina virtual en un conjunto de disponibilidad
> * Comprobar los tamaños de máquina virtual disponibles

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, en este tutorial es preciso que ejecute la CLI de Azure de la versión 2.0.30, u otra posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="availability-set-overview"></a>Información general sobre conjuntos de disponibilidad

Un conjunto de disponibilidad es una funcionalidad de agrupación lógica que puede usar en Azure para asegurarse de que los recursos de máquina virtual que coloque en dicho conjunto de disponibilidad estén aislados entre sí cuando se implementen en un centro de datos de Azure. Azure garantiza que las máquinas virtuales colocados en un conjunto de disponibilidad se ejecuten en varios servidores físicos, grupos de proceso, unidades de almacenamiento y conmutadores de red. Si se produce un error de hardware o software de Azure, solo un subconjunto de las máquinas virtuales se ve afectado y la aplicación se mantiene actualizada y sigue estando disponible para los clientes. Los conjuntos de disponibilidad son una funcionalidad fundamental para compilar soluciones en la nube confiables.

Veamos una solución basada en máquina virtual típica en la podría haber cuatro servidores web front-end y usar dos máquinas virtuales de back-end que hospedan una base de datos. Con Azure, desea definir 2 conjuntos de disponibilidad antes de implementar las máquinas virtuales: un conjunto de disponibilidad para el nivel de "web" y otro para el nivel de "base de datos". Al crear una máquina virtual, podrá especificar el conjunto de disponibilidad como un parámetro en el comando az vm create, y Azure garantiza automáticamente que las máquinas virtuales que cree en el conjunto de disponibilidad estén aisladas en varios recursos de hardware físico. Si el hardware físico que está ejecutando una de sus máquinas virtuales del servidor web o del servidor de base de datos tiene un problema, sabe que las demás instancias de las máquinas virtuales del servidor de base de datos y del servidor web siguen ejecutándose correctamente porque están en un hardware diferente.

Use los conjuntos de disponibilidad cuando quiera implementar soluciones basadas en máquinas virtuales confiables en Azure.


## <a name="create-an-availability-set"></a>Crear un conjunto de disponibilidad

Puede crear el conjunto de disponibilidad mediante [az vm availability-set create](/cli/azure/vm/availability-set#az_vm_availability_set_create). En este ejemplo, se establece el número de dominios de actualización y de error en *2* para el conjunto de disponibilidad denominado *myAvailabilitySet* en el grupo de recursos *myResourceGroupAvailability*.

En primer lugar, cree un grupo de recursos con [az group create](/cli/azure/group#az-group-create) y luego cree el conjunto de disponibilidad.

```azurecli-interactive
az group create --name myResourceGroupAvailability --location eastus

az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Los conjuntos de disponibilidad permiten aislar los recursos en dominios de error y dominios de actualización. Un **dominio de error** representa una colección aislada de recursos de almacenamiento, red y servidor. En el ejemplo anterior, el conjunto de disponibilidad se distribuye entre al menos dos dominios de error cuando se implementan las máquinas virtuales. El conjunto de disponibilidad también se distribuye entre dos **dominios de actualización**. El uso de dos dominios de actualización garantiza que cuando Azure lleve a cabo actualizaciones de software, los recursos de máquina virtual están aislados, lo que evita que todo el software que se ejecuta en la máquina virtual se actualice al mismo tiempo.


## <a name="create-vms-inside-an-availability-set"></a>Creación de VM dentro de un conjunto de disponibilidad

Las máquinas virtuales deben crearse en el conjunto de disponibilidad para asegurarse de que se distribuyan correctamente en el hardware. No se puede agregar una máquina virtual existente a un conjunto de disponibilidad después de que se crea.

Cuando se cree una máquina virtual con [az vm create](/cli/azure/vm#az_vm_create), use el parámetro `--availability-set` para especificar el nombre del conjunto de disponibilidad.

```azurecli-interactive
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys \
     --no-wait
done
```

Ahora hay dos máquinas virtuales dentro del conjunto de disponibilidad. Como se encuentran en el mismo conjunto de disponibilidad, Azure se asegura de que las máquinas virtuales y todos sus recursos (incluidos los discos de datos) se distribuyen entre el hardware físico aislado. Esta distribución ayuda a garantizar una mayor disponibilidad de la solución general de máquina virtual.

La distribución del conjunto de disponibilidad puede verse en el portal en Grupos de recursos > myResourceGroupAvailability > myAvailabilitySet. Las máquinas virtuales se distribuyen entre los dos dominios de actualización o de error, tal como se muestra en el ejemplo siguiente:

![Conjunto de disponibilidad en el portal](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Comprobación de los tamaños de VM disponibles

Más adelante, se pueden agregar máquinas virtuales adicionales al conjunto de disponibilidad, donde los tamaños de máquina virtual están disponibles en el hardware. Use [az vm availability-set list-sizes](/cli/azure/availability-set#az_availability_set_list_sizes) para enumerar todos los tamaños disponibles en el clúster de hardware para el conjunto de disponibilidad.

```azurecli-interactive
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Crear un conjunto de disponibilidad
> * Crear una máquina virtual en un conjunto de disponibilidad
> * Comprobar los tamaños de máquina virtual disponibles

Avance al siguiente tutorial para informarse sobre los conjuntos de escalado de máquinas virtuales.

> [!div class="nextstepaction"]
> [Creación de un conjunto de escalado de máquinas virtuales](tutorial-create-vmss.md)