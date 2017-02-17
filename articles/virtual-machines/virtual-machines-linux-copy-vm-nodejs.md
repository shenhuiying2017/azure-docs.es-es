---
title: "Creación de una copia de una máquina virtual Linux de Azure | Microsoft Docs"
description: "Sepa cómo crear una copia de una máquina virtual Linux en el modelo de implementación de Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: fc5d84768213f1c9358bfcffd77868c25b6c24a4
ms.openlocfilehash: 9920e86a00793928bdfae5bc4e9dac161ed56ad0


---
# <a name="create-a-copy-of-a-linux-virtual-machine-running-on-azure"></a>Creación de una copia de una máquina virtual Linux que se ejecuta en Azure
En este artículo se muestra cómo crear una copia de una máquina virtual de Azure con Linux con el modelo de implementación de Resource Manager. Primero copie a través del sistema operativo y los discos de datos en un nuevo contenedor y, después, configure los recursos de red para crear la nueva máquina virtual.

También puede [cargar y crear una máquina virtual a partir de una imagen de disco personalizada](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions-to-complete-the-task"></a>Versiones de la CLI para completar la tarea
Puede completar la tarea mediante una de las siguientes versiones de la CLI:

- CLI de Azure 1.0: la CLI para los modelos de implementación clásico y de Resource Manager (este artículo)
- [CLI de Azure 2.0 (versión preliminar)](virtual-machines-linux-copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): la CLI de última generación para el modelo de implementación de Resource Manager

## <a name="before-you-begin"></a>Antes de empezar
Asegúrese de que se cumplen los siguientes requisitos previos antes de comenzar los pasos:

* Tiene la [CLI de Azure](../xplat-cli-install.md) descargada e instalada en su equipo. 
* También necesitará cierta información acerca de la máquina virtual Linux de Azure existente:

| Información de la máquina virtual de origen | Dónde obtenerla |
| --- | --- |
| Nombre de la máquina virtual |`azure vm list` |
| Nombre del grupo de recursos |`azure vm list` |
| Ubicación |`azure vm list` |
| Nombre de la cuenta de almacenamiento |`azure storage account list -g <resourceGroup>` |
| Nombre del contenedor |`azure storage container list -a <sourcestorageaccountname>` |
| Nombre del archivo del VHD de la máquina virtual de origen |`azure storage blob list --container <containerName>` |

* Deberá tomar algunas decisiones acerca de la nueva máquina virtual:   <br> -Nombre del contenedor    <br> -Nombre de la máquina virtual    <br> -Tamaño de la máquina virtual    <br> -Nombre de red virtual    <br> -Nombre de subred    <br> -Nombre de IP    <br> -nombre de NIC

## <a name="login-and-set-your-subscription"></a>Inicio de sesión y establecimiento de la suscripción
1. Inicie sesión en la CLI.

    ```azurecli
    azure login
    ```
2. Asegúrese de que está en modo de Resource Manager.

    ```azurecli
    azure config mode arm
    ```
3. Establezca la suscripción correcta. Puede usar 'azure account list' para ver todas las suscripciones.

    ```azurecli
    azure account set mySubscriptionID
    ```

## <a name="stop-the-vm"></a>Parada de la máquina virtual
Detenga y desasigne la máquina virtual de origen. Puede usar 'azure vm list' para obtener una lista de todas las máquinas virtuales de su suscripción y sus nombres del grupo de recursos.

```azurecli
azure vm stop myResourceGroup myVM
azure vm deallocate myResourceGroup MyVM
```


## <a name="copy-the-vhd"></a>Copia del VHD
Puede copiar el VHD desde el almacenamiento de origen al de destino con `azure storage blob copy start`. En este ejemplo, vamos a copiar el VHD a la misma cuenta de almacenamiento, pero en un contenedor diferente.

Para copiar el VHD a otro contenedor de la misma cuenta de almacenamiento, escriba:

```azurecli
azure storage blob copy start \
        https://mystorageaccountname.blob.core.windows.net:8080/mycontainername/myVHD.vhd \
        myNewContainerName
```

## <a name="set-up-the-virtual-network-for-your-new-vm"></a>Configuración de una red virtual para la nueva máquina virtual
Configure una red virtual y una NIC para la nueva máquina virtual. 

```azurecli
azure network vnet create myResourceGroup myVnet -l myLocation

azure network vnet subnet create -a <address.prefix.in.CIDR/format> myResourceGroup myVnet mySubnet

azure network public-ip create myResourceGroup myPublicIP -l myLocation

azure network nic create myResourceGroup myNic -k mySubnet -m myVnet -p myPublicIP -l myLocation
```


## <a name="create-the-new-vm"></a>Creación de la máquina virtual
Ahora puede crear una máquina virtual desde el disco virtual cargado [mediante una plantilla de Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) o a través de la CLI especificando el identificador URI en el disco copiado escribiendo lo siguiente:

```azurecli
azure vm create -n myVM -l myLocation -g myResourceGroup -f myNic \
        -z Standard_DS1_v2 -y Linux \
        https://mystorageaccountname.blob.core.windows.net:8080/mycontainername/myVHD.vhd 
```



## <a name="next-steps"></a>Pasos siguientes
Para más información sobre cómo utilizar la CLI de Azure para administrar una máquina virtual nueva, consulte [Comandos de la CLI de Azure en el modo de Resource Manager](azure-cli-arm-commands.md).




<!--HONumber=Feb17_HO2-->


