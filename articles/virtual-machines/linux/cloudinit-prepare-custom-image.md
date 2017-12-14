---
title: "Preparación de la imagen de máquina virtual de Azure para su uso con cloud-init | Microsoft Docs"
description: "Preparación de una imagen de máquina virtual de Azure que ya existía previamente para implementarse con cloud-init"
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
ms.openlocfilehash: 2eb7510d4e76e4996e83f351a62c0b025b487df2
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2017
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>Preparación de la imagen de máquina virtual Linux en Azure para usarse con cloud-init
En este artículo se explica cómo utilizar una máquina virtual de Azure y prepararla para volver a implementarse y poder usar cloud-init. La imagen resultante se puede utilizar para implementar una nueva máquina virtual o conjuntos de escalado de máquinas virtuales (luego se pueden personaliza más aún mediante cloud-init durante la implementación).  Estos scripts de cloud-init se ejecutan durante el primer arranque una vez que Azure ha aprovisionado los recursos. Para obtener más información acerca del funcionamiento nativo de cloud-init en Azure y las distribuciones de Linux compatibles, consulte la [introducción a cloud-init](using-cloud-init.md).

## <a name="prerequisites"></a>Requisitos previos
En este documento se da por hecho que ya tiene una máquina virtual Azure en ejecución que ejecute una versión compatible del sistema operativo Linux. Ya ha configurado la máquina para satisfacer sus necesidades, ha instalado todos los módulos necesarios, ha procesado todas las actualizaciones necesarias y ha probado la máquina para asegurarse de que cumple los requisitos. 

## <a name="preparing-rhel-74--centos-74"></a>Preparación de RHEL 7.4 y CentOS 7.4
Necesita SSH en la máquina virtual Linux y ejecutar los comandos siguientes para instalar cloud-init.

```bash
sudo yum install -y cloud-init gdisk
sudo yum check-update cloud-init -y
sudo yum install cloud-init -y
```

Actualice la sección `cloud_init_modules` de `/etc/cloud/cloud.cfg` para incluir los siguientes módulos:
```bash
- disk_setup
- mounts
```

Este es un ejemplo de cómo se vería una sección `cloud_init_modules` de uso similar.
```bash
 cloud_config_modules:
 - mounts
 - locale
 - set-passwords
 - rh_subscription
 - yum-add-repo
 - package-update-upgrade-install
 - timezone
 - puppet
 - chef
 - salt-minion
 - mcollective
 - disable-ec2-metadata
 - runcmd
 - disk_setup
 - mounts
```
Hay que actualizar numerosas tareas relacionadas con el aprovisionamiento y el control de discos efímeros en `/etc/waagent.conf`. Ejecute los comandos siguientes para actualizar la configuración apropiada. 
```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
```
Permita solo Azure como un origen de datos para el agente Linux de Azure en creando un archivo `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` con el editor que prefiera a través de las líneas siguientes:

```bash
# This configuration file is provided by the WALinuxAgent package.
datasource_list: [ Azure ]
```

Si la imagen de Azure existente tiene un archivo de intercambio configurado y desea cambiar la configuración del archivo de intercambio para incluir nuevas imágenes con cloud-init, debe quitar el archivo de intercambio existente.

Para las imágenes basadas en RedHat, siga las instrucciones que aparecen en el siguiente documento de RedHat que explica cómo [quitar el archivo de intercambio](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/5/html/Deployment_Guide/s2-swap-removing-file.html).

Para las imágenes de CentOS con el archivo de intercambio habilitado, puede ejecutar el siguiente comando para desactivar el archivo de intercambio:
```bash
sudo swapoff /mnt/resource/swapfile
```

Asegúrese de quitar la referencia del archivo de intercambio de `/etc/fstab`; debe ser similar a lo siguiente:
```text
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

Para ahorrar espacio y quitar el archivo de intercambio, puede ejecutar el comando siguiente:
```bash
rm /mnt/resource/swapfile
```
## <a name="extra-step-for-cloud-init-prepared-image"></a>Paso adicional para la imagen preparada con cloud-init
> [!NOTE]
> Si la imagen se preparó y configuró anteriormente con **cloud-init**, debe realizar los pasos siguientes.

Los tres comandos siguientes solo se usan si la máquina virtual que va a personalizar para ser una nueva imagen de origen especializada se aprovisionó anteriormente con cloud-init.  NO es necesario ejecutar estos comandos si la imagen se ha configurado mediante el agente Linux de Azure.

```bash
sudo rm -rf /var/lib/cloud/instances/* 
sudo rm -rf /var/log/cloud-init*
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>Finalización de la configuración del agente Linux 
Todas las imágenes de la plataforma Azure tienen el agente Linux de Azure instalado, independientemente de si configuró cloud-init.  Ejecute el comando siguiente para terminar de desaprovisionar el usuario desde el equipo Linux. 

```bash
sudo waagent -deprovision+user -force
```

Para obtener más información sobre los comandos de desaprovisionamiento del agente Linux de Azure, consulte el artículo sobre el [agente Linux de Azure](agent-user-guide.md).

Salga de la sesión SSH y, luego, en el shell de Bash, ejecute los siguientes comandos de la CLI de Azure para desasignar, generalizar y crear una nueva imagen de máquina virtual de Azure.  Reemplace `myResourceGroup` y `sourceVmName` con la información adecuada que refleja la máquina virtual de origen.

```bash
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>Pasos siguientes
Para ejemplos de cloud-init de cambios de configuración adicionales, vea lo siguiente:
 
- [Incorporación de otro usuario de Linux a una máquina virtual](cloudinit-add-user.md)
- [Ejecución de un administrador de paquetes para actualizar los existentes durante el primer arranque](cloudinit-update-vm.md)
- [Cambio del nombre de host de la máquina virtual local](cloudinit-update-vm-hostname.md) 
- [Instalación de un paquete de aplicación, actualización de los archivos de configuración e inserción de claves](tutorial-automate-vm-deployment.md)