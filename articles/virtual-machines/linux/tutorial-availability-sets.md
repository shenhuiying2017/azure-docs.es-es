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
ms.topic: article
ms.date: 04/20/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: d082b37a2e070136178259c54ada8dc141f81e13
ms.contentlocale: es-es
ms.lasthandoff: 05/03/2017


---

# <a name="how-to-use-availability-sets"></a>Cómo usar conjuntos de disponibilidad

En este tutorial, aprenderá a aumentar la disponibilidad de sus máquinas virtuales al colocarlas en un grupo lógico denominado conjunto de disponibilidad. Al crear máquinas virtuales dentro de un conjunto de disponibilidad, la plataforma de Azure las distribuye por la infraestructura subyacente. Si se produce un error de hardware o se realiza un mantenimiento planeado en la plataforma, el uso de conjuntos de disponibilidad garantiza que al menos una máquina virtual siga ejecutándose.

Se pueden completar los pasos de este tutorial con la versión más reciente de la [CLI 2.0 de Azure](/cli/azure/install-azure-cli).

## <a name="availability-set-overview"></a>Información general sobre conjuntos de disponibilidad

Las máquinas virtuales se pueden crear en agrupaciones lógicas de hardware en el centro de datos de Azure subyacente. Cuando se crean dos o más máquinas virtuales, los recursos de cálculo y almacenamiento se distribuyen por el hardware, por ejemplo en servidores, conmutadores de red y almacenamiento. Esta distribución mantiene la disponibilidad de la aplicación en el caso de que un componente de hardware deba someterse a mantenimiento. Los conjuntos de disponibilidad permiten definir esta agrupación lógica.

Los conjuntos de disponibilidad proporcionan alta disponibilidad a las máquinas virtuales. Además, debe asegurarse de que las aplicaciones también están diseñadas para tolerar interrupciones o eventos de mantenimiento.

## <a name="create-an-availability-set"></a>Crear un conjunto de disponibilidad

Puede crear el conjunto de disponibilidad mediante [az vm availability-set create](/cli/azure/availability-set#create). En este ejemplo, se establece el número de dominios de actualización y error en *2* para el conjunto de disponibilidad denominado *myAvailabilitySet* en el grupo de recursos *myResourceGroupAvailability*.

```azurecli
az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

## <a name="create-vms-inside-an-availability-set"></a>Crear máquinas virtuales dentro de un conjunto de disponibilidad

Las máquinas virtuales deben crearse en el conjunto de disponibilidad para asegurarse de que se distribuyan correctamente en el hardware. No se puede agregar una máquina virtual existente a un conjunto de disponibilidad después de crearla. 

El hardware de una ubicación está dividido en varios dominios de actualización y de error. Un **dominio de actualización** es un grupo de máquinas virtuales y hardware físico subyacente que se pueden reiniciar al mismo tiempo. Las máquinas virtuales en el mismo **dominio de error** comparten un almacenamiento común, así como una fuente de alimentación y un conmutador de red comunes. 

Cuando se crea una máquina virtual mediante [az vm create](/cli/azure/vm#create), se especifica el conjunto de disponibilidad mediante el parámetro `--availability-set` para especificar el nombre del conjunto de disponibilidad.

```azurecli
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --image Canonical:UbuntuServer:14.04.4-LTS:latest \
     --admin-username azureuser \
     --generate-ssh-keys \
     --no-wait
done 
```

Ahora tenemos dos máquinas virtuales distribuidas en el hardware subyacente. 

## <a name="check-for-available-vm-sizes"></a>Comprobar los tamaños de máquina virtual disponibles 

Se pueden agregar más máquinas virtuales al conjunto de disponibilidad posteriormente, pero debe saber qué tamaños de máquina virtual están disponibles en el hardware. Use [az vm availability-set list-sizes](/cli/azure/availability-set#list-sizes) para enumerar todos los tamaños disponibles en el clúster de hardware para el conjunto de disponibilidad.

```azurecli
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table  
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha obtenido información sobre el uso de conjuntos de disponibilidad. Avance al siguiente tutorial para informarse sobre los conjuntos de escalado de máquinas virtuales.

[Creación de un conjunto de escalado de máquinas virtuales](tutorial-create-vmss.md)


