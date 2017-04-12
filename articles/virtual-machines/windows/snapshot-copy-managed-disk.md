---
title: "Creación de una copia de un disco administrado de Azure para copia de seguridad | Microsoft Docs"
description: "Obtenga información sobre cómo crear una copia de un disco administrado de Azure que se usará para copias de seguridad o solucionar problemas del disco."
documentationcenter: 
author: cwatsonMSFT
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 2/9/2017
ms.author: cwatson
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 620c9223106c06cf6ea2b80fce87d81ea44d02ca
ms.lasthandoff: 03/31/2017


---
# <a name="create-a-copy-of-a-vhd-stored-as-an-azure-managed-disk-by-using-managed-snapshots"></a>Creación de una copia de un VHD almacenado como un disco administrado de Azure mediante instantáneas administradas
Cree una instantánea de un disco administrado para copias de seguridad o cree un disco administrado a partir de la instantánea y conéctelo a una máquina virtual de prueba para fines de solución de problemas. Una instantánea administrada es una copia completa a partir de un momento específico de un disco administrado de VM. Crea una copia de solo lectura del VHD y, de forma predeterminada, se almacena como un Disco administrado estándar. Para más información sobre Managed Disks, consulte [Azure Managed Disks overview](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Introducción a Azure Managed Disks).

Para información sobre los precios, consulte [Precios de Azure Storage](https://azure.microsoft.com/pricing/details/managed-disks/). 

## <a name="before-you-begin"></a>Antes de empezar
Si usa PowerShell, asegúrese de que tiene la versión más reciente del módulo de PowerShell AzureRM.Compute. Ejecute el siguiente comando para instalarla.

```
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Para más información, consulte [Azure PowerShell Versioning](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning) (Control de versiones de Azure PowerShell).

## <a name="copy-the-vhd-with-a-snapshot"></a>Copia del VHD con una instantánea
Use Azure Portal o PowerShell para crear una instantánea del disco administrado.

### <a name="use-azure-portal-to-take-a-snapshot"></a>Uso de Azure Portal para crear una instantánea 

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En la parte superior izquierda, haga clic en **Nuevo** y busque **instantánea**.
3. En la hoja Instantánea, haga clic en **Crear**.
4. Escriba un **nombre** para la instantánea.
5. Seleccione un valor de [Grupo de recursos](../../azure-resource-manager/resource-group-overview.md#resource-groups) existente o escriba el nombre para crear uno. 
6. Seleccione una ubicación del centro de datos de Azure.  
7. Como **disco de origen**, seleccione el disco administrado para la instantánea.
8. Seleccione el **tipo de cuenta** que se usará para almacenar la instantánea. Se recomienda **Standard_LRS**, a menos que necesite almacenarla en un disco de alto rendimiento.
9. Haga clic en **Crear**.

### <a name="use-powershell-to-take-a-snapshot"></a>Uso de PowerShell para crear una instantánea
Los pasos siguientes le muestran cómo obtener el disco VHD que se copiará, crear las configuraciones de instantánea y crear una instantánea del disco con el cmdlet New-AzureRmSnapshot<!--Add link to cmdlet when available-->. 

1. Configure algunos parámetros. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$location = 'southeastasia' 
$dataDiskName = 'ContosoMD_datadisk1' 
$snapshotName = 'ContosoMD_datadisk1_snapshot1'  
```
  Reemplace los valores de parámetro:
  -  "myResourceGroup", con el grupo de recursos de la VM.
  -  "southeastasia", con la ubicación geográfica en la que desea almacenar la instantánea administrada. <!---How do you look these up? -->
  -  "ContosoMD_datadisk1", con el nombre del disco VHD que desea copiar.
  -  "ContosoMD_datadisk1_snapshot1", con el nombre que desea usar para la instantánea nueva.

2. Obtenga el disco VHD que se copiará.

 ```powershell
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName -DiskName $dataDiskName 
```
3. Cree las configuraciones de la instantánea. 

 ```powershell
$snapshot =  New-AzureRmSnapshotConfig -SourceUri $disk.Id -CreateOption Copy -Location $location 
```
4. Tome la instantánea.

 ```powershell
New-AzureRmSnapshot -Snapshot $snapshot -SnapshotName $snapshotName -ResourceGroupName $resourceGroupName 
```
Si tiene previsto utilizar la instantánea para crear un disco administrado y conectarle una VM que precisa de un alto rendimiento, use el parámetro `-AccountType Premium_LRS` con el comando New-AzureRmSnapshot. El parámetro crea la instantánea para que se almacene como disco administrado Premium. Managed Disks Premium son más costosos que los Estándar. Por lo tanto, asegúrese de que realmente necesita discos Premium antes de usar el parámetro.



