---
title: "Restauración de un disco de máquina virtual con Azure Backup | Microsoft Docs"
description: "Obtenga información acerca de cómo restaurar un disco y crear una máquina virtual recuperada en Azure con Backup and Recovery Services."
services: backup, virtual-machines
documentationcenter: virtual-machines
author: markgalioto
manager: carmonm
editor: 
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/28/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 9bc6da13786eb9eb6186ceadf0432b3a3ec2c941
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="restore-a-disk-and-create-a-recovered-vm-in-azure"></a>Restauración de un disco y creación de una máquina virtual recuperada en Azure
Azure Backup crea puntos de recuperación que se almacenan en almacenes de recuperación con redundancia geográfica. Cuando se realiza una restauración desde un punto de recuperación, se puede restaurar toda una máquina virtual o archivos individuales. En este artículo se explica cómo restaurar una máquina virtual completa. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Enumerar y seleccionar puntos de recuperación
> * Restaurar un disco desde un punto de recuperación
> * Crear una máquina virtual a partir del disco restaurado

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tutorial es preciso que ejecute la CLI de Azure versión 2.0.18 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 


## <a name="prerequisites"></a>Requisitos previos
Para este tutorial se necesita una máquina virtual Linux protegida con Azure Backup. Para simular un proceso de recuperación y eliminación de máquina virtual accidental, cree una máquina virtual desde un disco en un punto de recuperación. Si necesita una máquina virtual Linux que esté protegida con Azure Backup, consulte [Copia de seguridad de una máquina virtual en Azure con la CLI](quick-backup-vm-cli.md).


## <a name="backup-overview"></a>Introducción a Backup
Cuando Azure inicia una copia de seguridad, la extensión de copia de seguridad en la máquina virtual toma una instantánea de un momento dado. La extensión de copia de seguridad se instala en la máquina virtual cuando se solicita la primera copia de seguridad. Azure Backup también puede tomar una instantánea del almacenamiento subyacente si la máquina virtual no se está ejecutando cuando se realiza la copia de seguridad.

De forma predeterminada, Azure Backup toma una copia de seguridad coherente del sistema de archivos. Después de que el servicio Azure Backup tome la instantánea, los datos se transfieren al almacén de Recovery Services. Para que el proceso resulte más eficaz, Azure Backup identifica y transfiere únicamente los bloques de datos que han cambiado desde la última copia de seguridad.

Cuando finaliza la transferencia de datos, se elimina la instantánea y se crea un punto de recuperación.


## <a name="list-available-recovery-points"></a>Lista de puntos de recuperación disponibles
Para restaurar un disco, debe seleccionar un punto de recuperación como el origen de los datos de recuperación. Dado que la directiva predeterminada crea un punto de recuperación cada día y lo mantiene durante 30 días, puede mantener un conjunto de puntos de recuperación que le permita seleccionar un punto concreto a tiempo para la recuperación. 

Para ver una lista de los puntos de recuperación disponibles, use [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az_backup_recoverypoint_list). El punto de recuperación **name** se usa para recuperar discos. En este tutorial, queremos usar el punto de recuperación más reciente disponible. El parámetro `--query [0].name` selecciona el nombre del punto de recuperación más reciente de la siguiente manera:

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```


## <a name="restore-a-vm-disk"></a>Restaurar un disco de máquina virtual
Para restaurar el disco desde el punto de recuperación, cree primero una cuenta de almacenamiento de Azure. Esta cuenta de almacenamiento se usa para almacenar el disco restaurado. En pasos adicionales, el disco restaurado se usa para crear una máquina virtual.

1. Para crear una cuenta de almacenamiento, use [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_create). El nombre de la cuenta de almacenamiento debe estar en minúsculas y ser único globalmente. Reemplace *mystorageaccount* por su propio nombre único:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Restaure el disco desde el punto de recuperación con [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az_backup_restore_restore_disks). Reemplace *mystorageaccount* por el nombre de la cuenta de almacenamiento que creó en el comando anterior. Reemplace *myRecoveryPointName* por el nombre del punto de recuperación que obtuvo en la salida del comando [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az_backup_recoverypoint_list) anterior:

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```


## <a name="monitor-the-restore-job"></a>Supervisión del trabajo de restauración
Para supervisar el estado de un trabajo de restauración, use [az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az_backup_job_list):

```azurecli-interactive 
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

El resultado es similar al ejemplo siguiente, que muestra que el estado del trabajo de restauración es *InProgress*:

```
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Cuando *Estado* del trabajo de restauración indica *Completado*, el disco se ha restaurado en la cuenta de almacenamiento.


## <a name="convert-the-restored-disk-to-a-managed-disk"></a>Convertir el disco restaurado en un disco administrado
El trabajo de restauración crea un disco no administrado. Para crear una máquina virtual desde el disco, debe convertirse primero en un disco administrado.

1. Obtenga la información de conexión de la cuenta de almacenamiento con [az storage account show-connection-string](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_show_connection_string). Reemplace *mystorageaccount* por el nombre de la cuenta de almacenamiento de la siguiente manera:
    
    ```azurecli-interactive
    export AZURE_STORAGE_CONNECTION_STRING=$( az storage account show-connection-string \
        --resource-group myResourceGroup \
        --output tsv \
        --name mystorageaccount )
    ```

2. El disco no administrado está protegido en la cuenta de almacenamiento. Los siguientes comandos de obtienen información acerca del disco no administrado y crean una variable denominada *uri* que se usa en el paso siguiente al crear el disco administrado.

    ```azurecli-interactive
    container=$(az storage container list --query [0].name -o tsv)
    blob=$(az storage blob list --container-name $container --query [0].name -o tsv)
    uri=$(az storage blob url --container-name $container --name $blob -o tsv)
    ```

3. Ahora puede crear un disco administrado desde el disco recuperado con [disk create](https://docs.microsoft.com/cli/azure/disk?view=azure-cli-latest#az_disk_create). La variable *uri* del paso anterior se usa como origen para el disco administrado.

    ```azurecli-interactive
    az disk create \
        --resource-group myResourceGroup \
        --name myRestoredDisk \
        --source $uri
    ```

4. Dado que ahora tiene un disco administrado obtenido del disco restaurado, limpie el disco no administrado y la cuenta de almacenamiento con [az storage account delete](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_delete). Reemplace *mystorageaccount* por el nombre de la cuenta de almacenamiento de la siguiente manera:

    ```azurecli-interactive
    az storage account delete \
        --resource-group myResourceGroup \
        --name mystorageaccount
    ```


## <a name="create-a-vm-from-the-restored-disk"></a>Crear una máquina virtual a partir del disco restaurado
El último paso es crear una máquina virtual desde el disco administrado.

1. Cree una máquina virtual desde el disco administrado con [az vm create](/cli/azure/vm?view=azure-cli-latest#az_vm_create) como se indica a continuación:

    ```azurecli-interactive
    az vm create \
        --resource-group myResourceGroup \
        --name myRestoredVM \
        --attach-os-disk myRestoredDisk \
        --os-type linux
    ```

2. Para comprobar que la máquina virtual se ha creado desde el disco recuperado, enumere las máquinas virtuales del grupo de recursos con [az vm list](/cli/azure/vm?view=azure-cli-latest#az_vm_list) como se indica a continuación:

    ```azurecli-interactive
    az vm list --resource-group myResourceGroup --output table
    ```


## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha restaurado un disco desde un punto de recuperación y, a continuación, ha creado una máquina virtual desde el disco. Ha aprendido a:

> [!div class="checklist"]
> * Enumerar y seleccionar puntos de recuperación
> * Restaurar un disco desde un punto de recuperación
> * Crear una máquina virtual a partir del disco restaurado

Avance hasta el siguiente tutorial para obtener información acerca de cómo restaurar archivos individuales desde un punto de recuperación.

> [!div class="nextstepaction"]
> [Restauración de archivos en una máquina virtual de Azure](tutorial-restore-files.md)

