---
title: "Montaje de Azure File Storage en máquinas virtuales Linux con SMB | Microsoft Docs"
description: "Cómo montar Azure File Storage en máquinas virtuales Linux con SMB."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/07/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 6a3c4b85642a7b6eb4dbd0efc3b046a89328b3f0
ms.openlocfilehash: 711d217841690ee78321a1ae1a56571c49ef74cc


---

# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Montaje de Azure File Storage en máquinas virtuales Linux con SMB

Este artículo muestra cómo utilizar el servicio de Azure File Storage en una máquina virtual Linux con un montaje de SMB.  El almacenamiento de archivos de Azure ofrece recursos compartidos de archivos en la nube mediante el protocolo SMB estándar.  Los requisitos son:

- [una cuenta de Azure](https://azure.microsoft.com/pricing/free-trial/);

- [archivos de clave SSH pública y privada](virtual-machines-linux-mac-create-ssh-keys.md).

## <a name="quick-commands"></a>Comandos rápidos

Si necesita realizar rápidamente la tarea, en la siguiente sección se detallan los comandos necesarios. Se puede encontrar información más detallada y contexto para cada paso en el resto del documento, [comenzando aquí](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough).

Requisitos previos: grupo de recursos, VNet, NSG con entrada de SSH, subred, cuenta de Azure Storage, claves de cuenta de Azure Storage, recurso de Azure File Storage y una máquina virtual Linux. Reemplace los ejemplos por su propia configuración.

Creación de un directorio para el montaje local

```bash
mkdir -p /mnt/mymountpoint
```

Montaje del recurso compartido de SMB de Azure File Storage en el punto de montaje

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

Para conservar el montaje tras un reinicio, agregue una línea a `/etc/fstab`

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>Tutorial detallado

El almacenamiento de archivos de Azure ofrece recursos compartidos de archivos en la nube mediante el protocolo SMB estándar.  Y con la última versión de File Storage, es posible también montar un recurso compartido de archivos desde cualquier sistema operativo que sea compatible con SMB 3.0.  El uso de un montaje de SMB en Linux permite que se realicen copias de seguridad fáciles en una ubicación de almacenamiento de archivado permanente y sólida que se proporciona mediante un Acuerdo de Nivel de Servicio.  

El movimiento de los archivos de una máquina virtual a un montaje de SMB hospedado en Azure File Storage es una buena forma de depurar registros a medida que el mismo recurso de SMB puede montarse localmente en la estación de trabajo de Mac, Linux o Windows.  SMB no sería la mejor solución para transmitir registros de aplicación o Linux en tiempo real, puesto que el protocolo SMB no está creado para el registro de controles tan pesados.  Una herramienta de nivel de registro unificado dedicada como Fluentd sería una opción mejor a través de SMB para recopilar la salida de registro de la aplicación y Linux.

Para este tutorial detallado, creamos los requisitos previos necesarios para crear primero el recurso compartido de Azure File Storage y, a continuación, montarlo a través de SMB en una máquina virtual Linux.

## <a name="create-the-azure-storage-account"></a>Creación de una cuenta de Azure Storage

```azurecli
azure storage account create myStorageAccount \
--sku-name lrs \
--kind storage \
-l westus \
-g myResourceGroup
```

## <a name="show-the-storage-account-keys"></a>Visualización de las claves de cuenta de almacenamiento

Las claves de la cuenta de Azure Storage se crean en parejas, cuando se crea la cuenta de almacenamiento.  Las claves de cuenta de almacenamiento se crean en pares de modo que las claves se pueden girar sin ninguna interrupción del servicio.  Una vez que gira las claves a la segunda clave en el par, cree un nuevo par de claves.  Las nuevas claves de cuenta de almacenamiento se crean siempre en pares, lo que garantiza que siempre tiene al menos una clave de almacenamiento sin usar lista para girar.

```azurecli
azure storage account keys list myStorageAccount \
--resource-group myResourceGroup
```

## <a name="create-the-azure-file-storage-share"></a>Creación del recurso compartido de Azure File Storage

Cree el recurso compartido de File Storage, que contiene el recurso compartido de SMB.  La cuota es siempre en GigaBytes (GB).

```azurecli
azure storage share create mystorageshare \
--quota 10 \
--account-name myStorageAccount \
--account-key nPOgPR<--snip-->4Q==
```

## <a name="create-the-mount-point-directory"></a>Crear el directorio de punto de montaje

Se necesita un directorio local en el sistema de archivos de Linux en el que montar el recurso compartido de SMB.  No se envía nada escrito o leído desde el directorio de montaje local al recurso compartido de SMB hospedado en Azure File Storage.

```bash
sudo mkdir -p /mnt/mymountdirectory
```

## <a name="mount-the-smb-share"></a>Montaje del recurso compartido de SMB

```azurecli
sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=myStorageAccount,password=myStorageAccountkey,dir_mode=0777,file_mode=0777
```

## <a name="persist-the-smb-mount-through-reboots"></a>Conservación del montaje de SMB a través de reinicios

Una vez que se reinicia la máquina virtual de Linux, el recurso compartido de SMB montado se desmonta durante el cierre.  Para volver a montar el recurso compartido SMB en el arranque, debe agregar una línea a `/etc/fstab` de Linux.  Linux utiliza el archivo `fstab` para enumerar los sistemas de archivos que necesita montar durante el arranque.  Agregue el recurso compartido de SMB para garantizar que el recurso compartido de Azure File Storage es un sistema de archivos montado de manera permanente para la máquina virtual Linux.  La adición del recurso compartido de SMB de Azure File Storage a una nueva máquina virtual es posible con `cloud-init`.

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="next-steps"></a>Pasos siguientes

- [Uso de cloud-init para personalizar una VM de Linux durante la creación](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Adición de un disco a una máquina virtual de Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Cifrado de discos en una máquina virtual Linux mediante la CLI de Azure](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Dec16_HO3-->


