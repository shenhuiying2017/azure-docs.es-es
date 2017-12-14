---
title: "Uso de cloud-init para configurar un archivo de intercambio en una máquina virtual Linux | Microsoft Docs"
description: "Procedimiento para usar cloud-init con el fin de configurar un archivo de intercambio en una máquina virtual Linux con la CLI de Azure 2.0"
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: a8ccec0dc8ff100c5d067cd50f2a6fa8cb4871fb
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2017
---
# <a name="use-cloud-init-to-configure-a-swapfile-on-a-linux-vm"></a>Uso de cloud-init para configurar un archivo de intercambio en una máquina virtual Linux
Este artículo explica cómo usar [cloud-init](https://cloudinit.readthedocs.io) para configurar el archivo de intercambio en diversas distribuciones de Linux. El archivo de intercambio tradicionalmente se configuró mediante el agente Linux (WALA) en función de qué distribuciones se necesitaban.  Este documento detalla el proceso para crear el archivo de intercambio a petición durante el tiempo de aprovisionamiento mediante cloud-init.  Para obtener más información acerca del funcionamiento nativo de cloud-init en Azure y las distribuciones de Linux compatibles, consulte la [introducción a cloud-init](using-cloud-init.md).

## <a name="create-swapfile-for-ubuntu-based-images"></a>Creación del archivo de intercambio para imágenes basadas en Ubuntu
De forma predeterminada en Azure, las imágenes de la galería de Ubuntu no crean archivos de intercambio. Para habilitar la configuración del archivo de intercambio durante el aprovisionamiento de la máquina virtual mediante cloud-init: vea el [documento de AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions) en la wiki de Ubuntu.

## <a name="create-swapfile-for-redhat-and-centos-based-images"></a>Creación del archivo de intercambio para imágenes basadas en CentOS y RedHat

En el shell actual, cree un archivo denominado *cloud_init_swapfile.txt* y pegue la configuración siguiente. Para este ejemplo, cree el archivo en Cloud Shell, no en la máquina local. Puede utilizar el editor que prefiera. Escriba `sensible-editor cloud_init_swapfile.txt` para crear el archivo y ver una lista de editores disponibles. Elija el número 1 para utilizar el editor **nano**. Asegúrese de que todo el archivo cloud-init se copia correctamente, especialmente la primera línea.  

```yaml
#cloud-config
disk_setup:
ephemeral0:
table_type: gpt
layout: [66, [33,82]]
overwrite: True
fs_setup:
- device: ephemeral0.1
filesystem: ext4
- device: ephemeral0.2
filesystem: swap
mounts:
- ["ephemeral0.1", "/mnt"]
- ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
```

Antes de implementar esta imagen, debe crear un grupo de recursos con el comando [az group create](/cli/azure/group#create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Ahora, cree una máquina virtual con [az vm create](/cli/azure/vm#create) y especifique el archivo cloud-init con `--custom-data cloud_init_swapfile.txt` como se indica a continuación:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swapfile.txt \
  --generate-ssh-keys 
```

## <a name="verify-swapfile-was-created"></a>Compruebe que se creó el archivo de intercambio.
SSH a la dirección IP pública de la máquina virtual que se muestra en la salida del comando anterior. Escriba su propia **dirección IP pública**, como se indica a continuación:

```bash
ssh <publicIpAddress>
```

Una vez haya accedido mediante SSH a la máquina virtual, compruebe si se ha creado el archivo de intercambio.

```bash
swapon -s
```

La salida de este comando debe tener este aspecto:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Si tiene una imagen de Azure existente con un archivo de intercambio configurado y desea cambiar la configuración del archivo de intercambio para incluir nuevas imágenes, debe quitar el archivo de intercambio existente. Consulte el documento sobre cómo personalizar imágenes para aprovisionar mediante cloud-init para obtener más información.

## <a name="next-steps"></a>Pasos siguientes
Para ejemplos de cloud-init de cambios de configuración adicionales, vea lo siguiente:
 
- [Incorporación de otro usuario de Linux a una máquina virtual](cloudinit-add-user.md)
- [Ejecución de un administrador de paquetes para actualizar los existentes durante el primer arranque](cloudinit-update-vm.md)
- [Cambio del nombre de host de la máquina virtual local](cloudinit-update-vm-hostname.md) 
- [Instalación de un paquete de aplicación, actualización de los archivos de configuración e inserción de claves](tutorial-automate-vm-deployment.md)