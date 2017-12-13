---
title: "Uso de cloud-init para establecer el nombre de host para una máquina virtual Linux en Azure | Microsoft Docs"
description: "Procedimiento para usar cloud-init con el fin de personalizar una máquina virtual Linux con la CLI de Azure 2.0"
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
ms.openlocfilehash: 7455748b2e08104dadfed8cfe41581a400539d4f
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2017
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>Uso de cloud-init para establecer el nombre de host para una máquina virtual Linux en Azure
En este artículo se muestra el uso de [cloud-init](https://cloudinit.readthedocs.io) para configurar un nombre de host específico en una máquina virtual o en conjuntos de escalado de máquinas virtuales en el momento del aprovisionamiento en Azure. Estos scripts cloud-init se ejecutan durante el primer arranque una vez que Azure ha aprovisionado los recursos. Para más información acerca del funcionamiento nativo de cloud-init en Azure y las distribuciones de Linux compatibles, consulte la [introducción a cloud-init](using-cloud-init.md).

## <a name="set-the-hostname-with-cloud-init"></a>Establecimiento del nombre de host con cloud-init
De forma predeterminada, el nombre de host es el mismo que el de la máquina virtual al crear una en Azure.  Para ejecutar un script cloud-init para cambiar el nombre de host predeterminado al crear una máquina virtual en Azure con [az vm create](/cli/azure/vm#create), especifique el archivo cloud-init con el modificador `--custom-data`.  

Para ver la actualización en proceso, cree un archivo en el shell actual denominado *cloud_init_hostname.txt* y pegue la configuración siguiente. Para este ejemplo, cree el archivo en Cloud Shell, no en la máquina local. Puede utilizar el editor que prefiera. Escriba `sensible-editor cloud_init_hostname.txt` para crear el archivo y ver una lista de editores disponibles. Elija el número 1 para utilizar el editor **nano**. Asegúrese de que todo el archivo cloud-init se copia correctamente, especialmente la primera línea.  

```yaml
#cloud-config
hostname: myhostname
```

Antes de implementar esta imagen, debe crear un grupo de recursos con el comando [az group create](/cli/azure/group#create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Ahora, cree una máquina virtual con [az vm create](/cli/azure/vm#create) y especifique el archivo cloud-init con `--custom-data cloud_init_hostname.txt` como se indica a continuación:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

Una vez que la crea, la CLI de Azure muestra información sobre la máquina virtual. Use `publicIpAddress` para usar un cliente SSH a la máquina virtual. Escriba su propia dirección, como se indica a continuación:

```bash
ssh <publicIpAddress>
```

Para ver el nombre de la máquina virtual, use el comando `hostname` de la siguiente manera:

```bash
hostname
```

La máquina virtual debe informar el nombre de host como el valor establecido en el archivo cloud-init, como se muestra en la salida de ejemplo siguiente:

```bash
myhostname
```

## <a name="next-steps"></a>Pasos siguientes
Para ejemplos de cloud-init de cambios de configuración adicionales, vea lo siguiente:
 
- [Incorporación de otro usuario de Linux a una máquina virtual](cloudinit-add-user.md)
- [Ejecución de un administrador de paquetes para actualizar los existentes durante el primer arranque](cloudinit-update-vm.md)
- [Cambio del nombre de host de la máquina virtual local](cloudinit-update-vm-hostname.md) 
- [Instalación de un paquete de aplicación, actualización de los archivos de configuración e inserción de claves](tutorial-automate-vm-deployment.md)