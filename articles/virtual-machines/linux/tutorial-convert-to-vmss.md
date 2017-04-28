---
title: "Conversión de una máquina virtual de Azure en un conjunto de escalado | Microsoft Docs"
description: "Cree e implemente un conjunto de escalado de máquinas virtuales Linux de Azure con la CLI de Azure."
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/05/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 8d3376d2791b1349298db618d475ce5573083702
ms.lasthandoff: 04/07/2017

---

# <a name="convert-an-existing-azure-virtual-machine-to-a-scale-set"></a>Conversión de una máquina virtual de Azure existente en un conjunto de escalado

Este tutorial muestra cómo utilizar la CLI de Azure 2.0 para convertir una máquina virtual en un conjunto de escalado de máquinas virtuales. También aprenderá a automatizar la configuración de las máquinas virtuales en el conjunto de escalado. Para más información sobre cómo instalar la CLI de Azure 2.0, consulte [Getting Started with Azure CLI 2.0](/cli/azure/get-started-with-azure-cli.md) (Introducción a la CLI de Azure 2.0). Para más información acerca de los conjuntos de escala, consulte [Conjuntos de escala de máquinas virtuales](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

## <a name="step-1---deprovision-the-vm"></a>Paso 1: Desaprovisionamiento de la máquina virtual

Use SSH para conectarse a la máquina virtual.

Desaprovisione la máquina virtual con el agente de máquinas virtuales de Azure para eliminar archivos y datos. Para obtener una descripción detallada de desaprovisionamiento, consulte [Captura una máquina virtual Linux](capture-image.md).

```bash
sudo waagent -deprovision+user -force
exit
```

## <a name="step-2---capture-an-image-of-the-vm"></a>Paso 2: Captura de una imagen de la máquina virtual

Para obtener una descripción detallada de la captura, consulte [Captura de una máquina virtual Linux](capture-image.md).

Desasigne la VM con [az vm deallocate](/cli/azure/vm#deallocate):

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Generalice la VM con [az vm generalize](/cli/azure/vm#generalize):

```azurecli
az vm generalize --resource-group myResourceGroup --name myVM
```

Cree una imagen a partir del recurso de VM con [az image create](/cli/azure/image#create):

```azurecli
az image create --resource-group myResourceGroup --name myImage --source myVM
```

## <a name="step-3---create-the-scale-set"></a>Paso 3: Creación del conjunto de escalado

Obtenga el **identificador** de la imagen.

```azurecli
az image show --resource-group myResourceGroup --name myImage --query id
```

```json
"/subscriptions/afbdaf8b-9188-4651-bce1-9115dd57c98b/resourceGroups/vmtest/providers/Microsoft.Compute/images/myImage"
```

Cree una máquina virtual a partir de un recurso de imagen con [az vmss create](/cli/azure/vmss#create):

```azurecli
az vmss create --resource-group myResourceGroup --name myScaleSet --image /subscriptions/afbdaf8b-9188-4651-bce1-9115dd57c98b/resourceGroups/vmtest/providers/Microsoft.Compute/images/myImage --upgrade-policy-mode automatic --vm-sku Standard_DS1_v2 --data-disk-sizes-gb 10 --admin-username azureuser --generate-ssh-keys
```

Este comando también adjunta un disco de datos de 10gb. Tenga en cuenta que dependiendo del tamaño de la máquina virtual elegido (hemos usado **Standard_DS1_v2**), el número de discos de datos permitido es diferente. Para más información, revise los [tamaños de máquina virtual](sizes.md).

Una vez que finaliza el conjunto de escalado, conéctese a él. Obtenga una lista de direcciones IP para las instancias de SSH con [az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info):

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroup --name myScaleSet
```

```json
[
  "52.183.00.000:50000",
  "52.183.00.000:50003"
]
```

Ahora puede conectarse a la instancia de máquina virtual para inicializar el disco de datos.

```bash
ssh -i ~/.ssh/id_rsa.pub -p 50000 azureuser@52.183.00.000
```

## <a name="step-4---initialize-the-data-disk"></a>Paso 4: Inicialización del disco de datos

Mientras está conectado a la máquina virtual, realice una partición en el disco con `fdisk`:

```bash
(echo n; echo p; echo 1; echo  ; echo  ; echo w) | sudo fdisk /dev/sdc
```

Escriba un sistema de archivos en la partición con el comando `mkfs`:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Monte el disco nuevo para que sea accesible en el sistema operativo:

```bash
sudo mkdir /datadrive ; sudo mount /dev/sdc1 /datadrive
```

Se puede acceder ahora al disco a través del punto de montaje datadrive, que se puede comprobar con `ls /datadrive/`.

Finalice la sesión SSH.


## <a name="step-5---configure-firewall"></a>Paso 5: Configuración del firewall

Pase por el firewall al servidor web hospedado por el conjunto de escalado. Cuando se crea el conjunto de escalado, también se crea un equilibrador de carga y se utiliza **SSH** para las máquinas virtuales individuales. Para abrir un puerto, necesita dos fragmentos de información, que puede obtener mediante la CLI de Azure.

* **Grupo de direcciones IP de front-end**  
`az network lb show --resource-group myResourceGroup --name myScaleSetLB --output table --query frontendIpConfigurations[0].name`

* **Grupo de direcciones IP de back-end**  
`az network lb show --resource-group myResourceGroup --name myScaleSetLB --output table --query backendAddressPools[0].name`

Con los dos nombres, puede abrir el puerto **80**.

```azurecli
az network lb rule create --backend-pool-name myScaleSetLBBEPool --backend-port 80 --frontend-ip-name loadBalancerFrontEnd --frontend-port 80 --name webserver --protocol tcp --resource-group myResourceGroup --lb-name myScaleSetLB
```


## <a name="step-6---automate-configuration"></a>Paso 6: Automatización de la configuración

El disco de datos debe configurarse en cada instancia de máquina virtual. Se puede automatizar la configuración de la máquina virtual con la extensión **CustomScript**.

En primer lugar, cree un script *.sh* que incluya los comandos de formato de disco.

```sh
#!/bin/bash

# Setup the data disk
(echo n; echo p; echo 1; echo  ; echo  ; echo w) | fdisk /dev/sdc
fdisk /dev/sdc
mkfs -t ext4 /dev/sdc1
mkdir /datadrive
mount /dev/sdc1 /datadrive

exit 0
```

Después, cargue ese archivo de script al lugar donde la extensión **CustomScript** pueda tener acceso a él. Hay disponible una copia [aquí](https://gist.githubusercontent.com/Thraka/ab1d8b78ac4b23722f3d3c1c03ac5df4).

Cree un archivo local denominado **settings.json** e inserte el siguiente bloque JSON en ella. La propiedad `flieUris` debe establecerse en donde se cargó el archivo de script.

```json
{
  "fileUris": ["https://gist.githubusercontent.com/Thraka/ab1d8b78ac4b23722f3d3c1c03ac5df4/raw/3ac6e385010ac675e23ce583ce27b1a752f1b482/prep-vmss.sh"],
  "commandToExecute": "bash prep-vmss.sh" 
}
```

Implemente este comando en el conjunto de escalado con la extensión **CustomScript**, que hace referencia al archivo **settings.json** que se acaba de crear.

```azurecli
az vmss extension set --publisher Microsoft.Azure.Extensions --version 2.0 --name CustomScript --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

Esta extensión se ejecuta automáticamente en todas las instancias actuales y en todas las instancias creadas después ajustando la escala.

## <a name="step-7---configure-autoscale-rules"></a>Paso 7: Configuración de reglas de escalado automático

Actualmente, no se pueden establecer reglas de escalado automático en la CLI de Azure. Use [Azure Portal](https://portal.azure.com) para configurar el escalado automático.

## <a name="step-8---management-tasks"></a>Paso 8: Tareas de administración

Durante el ciclo de vida del conjunto de escalado, debe ejecutar una o varias tareas de administración. Además, puede crear scripts que automaticen diversas tareas de ciclo de vida y la CLI de Azure le ofrece una forma rápida de realizar esas tareas. A continuación, presentamos algunas tareas comunes.

### <a name="get-connection-info"></a>Obtención de información de conexión

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroup --name myScaleSet
```

### <a name="set-instance-count-manual-scale"></a>Establecimiento del número de instancias (escalado manual)

```azurecli
az vmss scale --resource-group myResourceGroup --name myScaleSet --new-capacity 4
```

### <a name="delete-resource-group"></a>Eliminación de un grupo de recursos

Al eliminar un grupo de recursos se eliminan también todos los recursos contenidos en el mismo.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes
Para aprender más sobre algunas de las características del conjunto de escalado de máquinas virtuales presentadas en este tutorial, consulte la siguiente información:

- [Introducción a los conjuntos de escalado de máquinas virtuales de Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)
- [Información general sobre Azure Load Balancer](../../load-balancer/load-balancer-overview.md)
- [Control del flujo de tráfico de red con grupos de seguridad de red](../../virtual-network/virtual-networks-nsg.md)
