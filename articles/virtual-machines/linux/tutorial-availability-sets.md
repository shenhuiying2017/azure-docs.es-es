---
title: "Tutorial de conjuntos de disponibilidad para máquinas virtuales de Linux en Azure | Microsoft Docs"
description: "Obtenga información sobre los conjuntos de disponibilidad para máquinas virtuales de Linux en Azure."
documentationcenter: 
services: virtual-machines-linux
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: tutorial
ms.date: 05/22/2017
ms.author: cynthn
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 63fe3f165864f06228604cac56d06cc061ab25f5
ms.contentlocale: es-es
ms.lasthandoff: 08/28/2017

---

# <a name="how-to-use-availability-sets"></a>Cómo usar conjuntos de disponibilidad


En este tutorial, obtendrá información sobre cómo aumentar la disponibilidad y confiabilidad de las soluciones de máquina virtual en Azure mediante una funcionalidad denominada "conjuntos de disponibilidad". Los conjuntos de disponibilidad garantizan que las máquinas virtuales implementadas en Azure se distribuyan entre varios clústeres de hardware aislados. De este modo, se asegura de que, si se produce un error de hardware o software en Azure, solo un subconjunto de las máquinas virtuales se verá afectado, y que la solución estará disponible y en funcionamiento para los clientes.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear un conjunto de disponibilidad
> * Crear una máquina virtual en un conjunto de disponibilidad
> * Comprobar los tamaños de máquina virtual disponibles


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tutorial es preciso que ejecute la CLI de Azure versión 2.0.4 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="availability-set-overview"></a>Información general sobre conjuntos de disponibilidad

Un conjunto de disponibilidad es una funcionalidad de agrupación lógica que puede usar en Azure para asegurarse de que los recursos de máquina virtual que coloque en dicho conjunto de disponibilidad estén aislados entre sí cuando se implementen en un centro de datos de Azure. Azure garantiza que las máquinas virtuales colocados en un conjunto de disponibilidad se ejecuten en varios servidores físicos, grupos de proceso, unidades de almacenamiento y conmutadores de red. De este modo, se garantiza que si se produce un error de hardware o software de Azure, solo un subconjunto de las máquinas virtuales se verá afectado, y que la aplicación se mantendrá actualizada y seguirá estando disponible para los clientes. Usar conjuntos de disponibilidad es una funcionalidad fundamental que debe tener en cuenta para crear soluciones en la nube confiables.

Veamos una solución basada en máquina virtual típica en la podría haber 4 servidores web front-end y usar 2 máquinas virtuales de back-end que hospedan una base de datos. Con Azure, desea definir 2 conjuntos de disponibilidad antes de implementar las máquinas virtuales: un conjunto de disponibilidad para el nivel de "web" y otro para el nivel de "base de datos". Al crear una máquina virtual, podrá especificar el conjunto de disponibilidad como un parámetro en el comando az vm create, y Azure garantizará automáticamente que las máquinas virtuales que cree en el conjunto de disponibilidad estén aisladas en varios recursos de hardware físico. Esto significa que si el hardware físico que está ejecutando una de sus máquinas virtuales del servidor web o del servidor de base de datos tiene un problema, sabrá que las demás instancias de las máquinas virtuales del servidor de base de datos y del servidor web seguirán ejecutándose correctamente porque están en un hardware diferente.

Siempre debe utilizar los conjuntos de disponibilidad cuando quiera implementar soluciones basadas en máquinas virtuales confiables en Azure.


## <a name="create-an-availability-set"></a>Crear un conjunto de disponibilidad

Puede crear el conjunto de disponibilidad mediante [az vm availability-set create](/cli/azure/vm/availability-set#create). En este ejemplo, se establece el número de dominios de actualización y error en *2* para el conjunto de disponibilidad denominado *myAvailabilitySet* en el grupo de recursos *myResourceGroupAvailability*.

Cree un grupo de recursos.

```azurecli-interactive 
az group create --name myResourceGroupAvailability --location eastus
```


```azurecli-interactive 
az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Los conjuntos de disponibilidad permiten aislar los recursos en dominios de error y dominios de actualización. Un **dominio de error** representa una colección aislada de recursos de almacenamiento, red y servidor. En el ejemplo anterior, indicamos que deseamos que nuestro conjunto de disponibilidad se distribuya entre, al menos, dos dominios de error cuando se implementen nuestro máquinas virtuales. También especificamos que queremos que nuestro conjunto de disponibilidad se distribuya entre dos **dominios de actualización**.  Dos dominios de actualización garantizan que cuando Azure lleve a cabo actualizaciones de software nuestros recursos de máquina virtual estén aislados, lo que evita que todo el software que se ejecuta en nuestra máquina virtual se actualice al mismo tiempo.

## <a name="configure-virtual-network"></a>Configuración de una red virtual
Antes de implementar algunas máquinas virtuales y poder probar el equilibrador, cree los recursos de red virtual auxiliares. Para más información sobre las redes virtuales, consulte el tutorial [Administración de Azure Virtual Networks](tutorial-virtual-network.md).

### <a name="create-network-resources"></a>Crear recursos de red
Cree la red virtual con el comando [az network vnet create](/cli/azure/network/vnet#create). En el ejemplo siguiente se crea una red virtual denominada *myVnet* con una subred *mySubnet*:

```azurecli-interactive 
az network vnet create \
    --resource-group myResourceGroupAvailability \
    --name myVnet \
    --subnet-name mySubnet
```
Las NIC virtuales se crean con el comando [az network nic create](/cli/azure/network/nic#create). En el ejemplo siguiente se crean tres NIC. (Una NIC virtual para cada máquina virtual que cree para la aplicación en los pasos siguientes). Puede crear NIC virtuales y máquinas virtuales adicionales en cualquier momento y agregarlas al equilibrador de carga:

```bash
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupAvailability \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```

## <a name="create-vms-inside-an-availability-set"></a>Creación de VM dentro de un conjunto de disponibilidad

Las máquinas virtuales deben crearse en el conjunto de disponibilidad para asegurarse de que se distribuyan correctamente en el hardware. No se puede agregar una máquina virtual existente a un conjunto de disponibilidad después de crearla. 

Cuando se crea una máquina virtual mediante [az vm create](/cli/azure/vm#create), se especifica el conjunto de disponibilidad mediante el parámetro `--availability-set` para especificar el nombre del conjunto de disponibilidad.

```azurecli-interactive 
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --nics myNic$i \
     --size Standard_DS1_v2  \
     --image Canonical:UbuntuServer:14.04.4-LTS:latest \
     --admin-username azureuser \
     --generate-ssh-keys \
     --no-wait
done 
```

Ahora tenemos dos máquinas virtuales en nuestro conjunto de disponibilidad recién creado. Como se encuentran en el mismo conjunto de disponibilidad, Azure se asegurará de que las máquinas virtuales y todos sus recursos (incluidos los discos de datos) se distribuyan entre el hardware físico aislado. Esta distribución ayuda a garantizar una mayor disponibilidad de nuestra solución de máquina virtual.

Un problema que puede encontrarse a medida que agregue las máquinas virtuales es que un determinado tamaño de máquina virtual ya no esté disponible para utilizarse en el conjunto de disponibilidad. Esto puede ocurrir si no hay suficiente capacidad para agregarlo al tiempo que conserva las reglas de aislamiento que aplica el conjunto de disponibilidad. Puede comprobar qué tamaños de máquina virtual están disponibles para usarse en un conjunto de disponibilidad a través del parámetro `--availability-set list-sizes`.

## <a name="check-for-available-vm-sizes"></a>Comprobación de los tamaños de VM disponibles 

Se pueden agregar más máquinas virtuales al conjunto de disponibilidad posteriormente, pero debe saber qué tamaños de máquina virtual están disponibles en el hardware. Use [az vm availability-set list-sizes](/cli/azure/availability-set#list-sizes) para enumerar todos los tamaños disponibles en el clúster de hardware para el conjunto de disponibilidad.

```azurecli-interactive 
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table  
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Crear un conjunto de disponibilidad
> * Crear una máquina virtual en un conjunto de disponibilidad
> * Comprobar los tamaños de máquina virtual disponibles

Avance al siguiente tutorial para informarse sobre los conjuntos de escalado de máquinas virtuales.

> [!div class="nextstepaction"]
> [Creación de un conjunto de escalado de máquinas virtuales](tutorial-create-vmss.md)


