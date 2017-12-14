---
title: "Uso de cloud-init para instalar paquetes en una máquina virtual Linux en Azure | Microsoft Docs"
description: "Procedimiento para usar cloud-init con el fin de actualizar e instalar paquetes en una máquina virtual Linux con la CLI de Azure 2.0"
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
ms.openlocfilehash: 4209bc270a6d255c8512dd6ccd5551b556da5a6b
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2017
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Usar Uso de cloud-init para instalar paquetes en una máquina virtual Linux en Azure
En este artículo se muestra el uso de [cloud-init](https://cloudinit.readthedocs.io) para actualizar paquetes en una máquina virtual Linux (VM) o en conjuntos de escalado de máquinas virtuales (VMSS) en el momento del aprovisionamiento en Azure. Estos scripts de cloud-init se ejecutan durante el primer arranque una vez que Azure ha aprovisionado los recursos. Para obtener más información acerca del funcionamiento nativo de cloud-init en Azure y las distribuciones de Linux compatibles, consulte la [introducción a cloud-init](using-cloud-init.md).

## <a name="update-a-vm-with-cloud-init"></a>Actualización de una máquina virtual con cloud-init
Por motivos de seguridad, es posible que desee configurar una máquina virtual para aplicar las actualizaciones más recientes en el primer arranque. Como cloud-init funciona en distintas distribuciones de Linux, no es necesario especificar `apt` ni `yum` para el administrador de paquetes. En lugar de eso, se define `package_upgrade` para permitir que el proceso cloud-init determine el mecanismo adecuado para la distribución en uso. Este flujo de trabajo permite usar los mismos scripts de cloud-init en distintas distribuciones.

Para ver la actualización en proceso, cree un archivo en el shell actual denominado *cloud_init_upgrade.txt* y pegue la configuración siguiente. Para este ejemplo, cree el archivo en Cloud Shell, no en la máquina local. Puede utilizar el editor que prefiera. Escriba `sensible-editor cloud_init_upgrade.txt` para crear el archivo y ver una lista de editores disponibles. Elija el número 1 para utilizar el editor **nano**. Asegúrese de que todo el archivo cloud-init se copia correctamente, especialmente la primera línea.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Antes de implementar esta imagen, debe crear un grupo de recursos con el comando [az group create](/cli/azure/group#create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Ahora, cree una máquina virtual con [az vm create](/cli/azure/vm#create) y especifique el archivo cloud-init con `--custom-data cloud_init_upgrade.txt` como se indica a continuación:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

SSH a la dirección IP pública de la máquina virtual que se muestra en la salida del comando anterior. Escriba su propia **dirección IP pública**, como se indica a continuación:

```bash
ssh <publicIpAddress>
```

Ejecute la herramienta de administración de paquetes y compruebe si hay actualizaciones. En el ejemplo siguiente se usa `apt-get` en una máquina virtual Ubuntu:

```bash
sudo apt-get upgrade
```

Como cloud-init comprobó e instaló las actualizaciones en el arranque, no hay ninguna actualización que aplicar, tal como se muestra en la salida de ejemplo siguiente:

```bash
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

También puede ver que `httpd` se instaló mediante la ejecución de `yum history` y revisar la salida que hacen referencia a `httpd`. 

## <a name="next-steps"></a>Pasos siguientes
Para ejemplos de cloud-init de cambios de configuración adicionales, vea lo siguiente:
 
- [Incorporación de otro usuario de Linux a una máquina virtual](cloudinit-add-user.md)
- [Ejecución de un administrador de paquetes para actualizar los existentes durante el primer arranque](cloudinit-update-vm.md)
- [Cambio del nombre de host de la máquina virtual local](cloudinit-update-vm-hostname.md) 
- [Instalación de un paquete de aplicación, actualización de los archivos de configuración e inserción de claves](tutorial-automate-vm-deployment.md)