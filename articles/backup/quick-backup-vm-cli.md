---
title: "Guía de inicio rápido de Azure: copia de seguridad una máquina virtual con la CLI de Azure | Microsoft Docs"
description: "Aprenda a realizar copias de seguridad de sus máquinas virtuales con la CLI de Azure"
services: backup, virtual-machines-linux
documentationcenter: virtual-machines
author: markgalioto
manager: carmonm
editor: 
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: backup, virtual-machines-linux
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: storage-backup-recovery
ms.date: 1/12/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 675ebb56496846c5d00ecb5af0e4a69c092b74c6
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/13/2018
---
# <a name="back-up-a-virtual-machine-in-azure-with-the-cli"></a>Copia de seguridad de una máquina virtual en Azure con la CLI
La CLI de Azure se usa para crear y administrar recursos de Azure desde la línea de comandos o en scripts. Para proteger sus datos realice copias de seguridad a intervalos regulares. Azure Backup crea puntos de recuperación que se guardan en almacenes de recuperación con redundancia geográfica. En este artículo se explica cómo realizar una copia de seguridad de una máquina virtual (VM) en Azure con la CLI de Azure. Estos pasos también se pueden llevar a cabo con [Azure PowerShell](quick-backup-vm-powershell.md) o en [Azure Portal](quick-backup-vm-portal.md).

Este inicio rápido permite realizar copias de seguridad en una máquina virtual de Azure existente. Si necesita crear una máquina virtual, puede [crearla con la CLI de Azure](../virtual-machines/linux/quick-create-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Para instalar y usar la CLI localmente, debe ejecutar la versión 2.0.18 de la CLI de Azure o una posterior. Para averiguar la versión de la CLI, ejecute `az --version`. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli). 


## <a name="create-a-recovery-services-vault"></a>Creación de un almacén de Servicios de recuperación
Un almacén de Recovery Services es un contenedor lógico que almacena los datos de copia de seguridad de los recursos protegidos, como las máquinas virtuales de Azure. Cuando se ejecuta el trabajo de copia de seguridad para un recurso protegido, crea un punto de recuperación en el almacén de Recovery Services. Posteriormente, se puede usar uno de estos puntos de recuperación para restaurar los datos a un momento dado en el tiempo.

Cree un almacén de Recovery Services con [az backup vault create](https://docs.microsoft.com/cli/azure/backup/vault#az_backup_vault_create). Especifique el mismo grupo de recursos y ubicación que tenga la máquina virtual que desea proteger. Si ha usado el [inicio rápido de la máquina virtual](../virtual-machines/linux/quick-create-cli.md), habrá creado lo siguiente:

- un grupo de recursos denominado *myResourceGroup*;
- una máquina virtual denominada *myVM*;
- recursos en la ubicación *eastus*.

```azurecli-interactive 
az backup vault create --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
    --location eastus
```

De forma predeterminada, el almacén de Recovery Services se establece para el almacenamiento con redundancia geográfica. El almacenamiento con redundancia geográfica garantiza que se repliquen los datos de copia de seguridad en una región de Azure secundaria que se encuentra a cientos de kilómetros de distancia de la región primaria.


## <a name="enable-backup-for-an-azure-vm"></a>Habilitación de la copia de seguridad de una máquina virtual de Azure
Cree y use directivas para definir cuándo se ejecuta un trabajo de copia de seguridad y durante cuánto tiempo se almacenan los puntos de recuperación. La directiva de protección predeterminada ejecuta un trabajo de copia de seguridad cada día y conserva los puntos de seguridad durante 30 días. Estos valores de la directiva predeterminada se pueden usar para proteger rápidamente la máquina virtual. Para habilitar la protección mediante copia de seguridad de una máquina virtual, use [az backup protection enable-for-vm](https://docs.microsoft.com/cli/azure/backup/protection#az_backup_protection_enable_for_vm). Especifique el grupo de recursos y la máquina virtual que se van a proteger, y la directiva que se va a usar:

```azurecli-interactive 
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```


## <a name="start-a-backup-job"></a>Inicio de un trabajo de copia de seguridad
Para iniciar una copia de seguridad ahora, en lugar de esperar a que la directiva predeterminada ejecute el trabajo en el momento programado, utilice [az backup protection backup-now](https://docs.microsoft.com/cli/azure/backup/protection#az_backup_protection_backup_now). El primer trabajo de copia de seguridad crea un punto de recuperación completa. Cada uno de los trabajo de copia de seguridad posteriores a esta copia de seguridad inicial crea puntos de recuperación incremental. Los puntos de recuperación incremental ahorran tiempo y espacio de almacenamiento, ya que solo transfieren los cambios realizados desde la última copia de seguridad.

Los siguientes parámetros se utilizan para hacer una copia de seguridad de la máquina virtual:

- `--container-name` es el nombre de la máquina virtual
- `--item-name` es el nombre de la máquina virtual
- El valor `--retain-until` se debe establecerse en la última fecha disponible, en formato de hora UTC (**dd-mm-aaaa**), en que desea que el punto de recuperación esté disponible

En el ejemplo siguiente se realiza una copia de la máquina virtual denominada *myVM* y se establece la expiración del punto de recuperación para el 18 de octubre de 2017:

```azurecli-interactive 
az backup protection backup-now \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --retain-until 18-10-2017
```


## <a name="monitor-the-backup-job"></a>Supervisión del trabajo de copia de seguridad
Para supervisar el estado de los trabajos de copia de seguridad, use [az backup job list](https://docs.microsoft.com/cli/azure/backup/job#az_backup_job_list):

```azurecli-interactive 
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

La salida es similar al ejemplo siguiente, que muestra que el estado del trabajo de copia de seguridad es *InProgress*:

```
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
a0a8e5e6  Backup           InProgress  myvm         2017-09-19T03:09:21  0:00:48.718366
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Cuando el apartado *Status* (Estado) del trabajo de copia de seguridad muestre *Completed* (Completado), la máquina virtual estará protegida con Recovery Services y tendrá almacenado un punto de recuperación completa.


## <a name="clean-up-deployment"></a>Limpieza de la implementación
Cuando deje de ser necesaria, puede deshabilitar la protección en la máquina virtual, quitar los puntos de restauración y el almacén de Recovery Services, y eliminar tanto el grupo de recursos como los recursos de la máquina virtual asociados. Si ha usado una máquina virtual existente, puede omitir el última comando [az group delete](/cli/azure/group?view=azure-cli-latest#az_group_delete) para dejar tanto el grupo de recursos como la máquina virtual en su lugar.

Si desea seguir un tutorial de Backup en el que se explique cómo restaurar los datos en una máquina virtual, vaya a [Pasos siguientes](#next-steps). 

```azurecli-interactive 
az backup protection disable \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --delete-backup-data true
az backup vault delete \
    --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>pasos siguientes
En esta guía de inicio rápido, ha creado un almacén de Recovery Services, habilitado la protección en una máquina virtual y creado el punto de recuperación inicial. Para más información acerca de Azure Backup, y Recovery Services, continúe con los tutoriales.

> [!div class="nextstepaction"]
> [Copia de seguridad de varias máquinas virtuales de Azure](./tutorial-backup-vm-at-scale.md)
