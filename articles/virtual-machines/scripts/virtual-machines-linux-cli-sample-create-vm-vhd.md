---
title: "Ejemplo de script de la CLI de Azure: crear una máquina virtual con un disco duro virtual | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: crear una máquina virtual mediante un disco duro virtual."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/09/2017
ms.author: allclark
ms.custom: mvc
ms.openlocfilehash: 6234473d9f7f0eb18ea85e52273eb82a9ce04da5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-with-a-virtual-hard-disk"></a>Crear una máquina virtual con un disco duro virtual

Este ejemplo crea una máquina virtual con un disco duro virtual (VHD).
Crea un grupo de recursos, una cuenta de almacenamiento y un contenedor, y después carga el VHD en el contenedor para crear una máquina virtual.
Reemplaza la clave pública SSH con su clave pública para que tenga acceso a la máquina virtual.

Necesitará un VHD de arranque.
Puede usar su propio VHD o descargar el que hemos usado desde https://azclisamples.blob.core.windows.net/vhds/sample.vhd. El script busca `~/sample.vhd`.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-vhd/create-vm-vhd.sh "Create VM using a VHD")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Ejecute el siguiente comando para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```azurecli-interactive 
az group delete -n az-cli-vhd
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un grupo de recursos, una máquina virtual, un conjunto de disponibilidad, un equilibrador de carga y todos los recursos relacionados. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az storage account list](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_list) | Enumera las cuentas de almacenamiento |
| [az storage account check-name](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_check_name) | Comprueba que un nombre de cuenta de almacenamiento es válido y que no existe ya |
| [az storage account keys list](https://docs.microsoft.com/cli/azure/storage/account/keys#az_storage_account_keys_list) | Enumera las claves de las cuentas de almacenamiento |
| [az storage blob exists](https://docs.microsoft.com/cli/azure/storage/blob#az_storage_blob_exists) | Comprueba si existe el blob |
| [az storage container create](https://docs.microsoft.com/cli/azure/storage/container#az_storage_container_create) | Crea un contenedor en una cuenta de almacenamiento. |
| [az storage blob upload](https://docs.microsoft.com/cli/azure/storage/blob#az_storage_blob_upload) | Crea un blob en el contenedor cargando el VHD. |
| [az vm list](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Se usa con `--query` para comprobar si el nombre de la máquina virtual está en uso. | 
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#az_vm_availability_set_create) | Crea las máquinas virtuales. |
| [az vm access set-linux-user](https://docs.microsoft.com/cli/azure/vm/access#az_vm_access_set_linux_user) | Restablece la clave SSH para permitir el acceso del usuario actual a la máquina virtual. |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm#az_vm_list-ip-addresses) | Obtiene la dirección IP de la máquina virtual que se creó. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de la CLI de máquina virtual en la [documentación sobre máquinas virtuales Linux de Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
