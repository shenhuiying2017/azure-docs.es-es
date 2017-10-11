---
title: "Creación de una máquina virtual de SQL Server en Azure PowerShell (implementación clásica)| Microsoft Docs"
description: "Ofrece pasos y scripts de PowerShell para crear una máquina virtual de Azure con imágenes de la galería de máquinas virtuales de SQL Server. En este tema se usa el modelo de implementación clásica."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
tags: azure-service-management
ms.assetid: b73be387-9323-4e08-be53-6e5928e3786e
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/07/2017
ms.author: jroth
ms.openlocfilehash: c3bd4329e8a22ce8503d6593560d29c2a3135e83
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Aprovisionamiento de una máquina virtual de SQL mediante Azure PowerShell (clásico)

En este artículo se ofrecen los pasos para crear una máquina virtual de SQL Server en Azure mediante los cmdlets de PowerShell.

> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../azure-resource-manager/resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos.

Para ver la versión de Resource Manager de este tema, consulte [Aprovisionamiento de una máquina virtual de SQL mediante Azure PowerShell (Resource Manager)](../sql/virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>Instalación y configuración de PowerShell:
1. Si no tiene una cuenta de Azure, visite [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
2. [Descargue e instale los comandos más recientes de Azure PowerShell](/powershell/azure/overview).
3. Inicie Windows PowerShell y conéctelo con su suscripción de Azure mediante el comando **Add-AzureAccount** .

   ```powershell
   Add-AzureAccount
   ```

## <a name="determine-your-target-azure-region"></a>Determinar su región de Azure de destino

La máquina virtual de SQL Server se hospedará en un servicio en la nube que reside en una región de Azure específica. Los siguientes pasos le ayudan a determinar la región, la cuenta de almacenamiento y servicio en la nube que se usará para el resto del tutorial.

1. Determine el centro de datos que quiere usar para hospedar su máquina virtual de SQL Server. El siguiente comando de PowerShell muestra una lista de nombres de las regiones disponibles.

   ```powershell
   (Get-AzureLocation).Name
   ```

2. Cuando haya identificado la ubicación que prefiera, establezca una variable denominada **$dcLocation** en dicha región. Por ejemplo, el siguiente comando establece la región en "este de EE. UU.":

   ```powershell
   $dcLocation = "East US"
   ```

## <a name="set-your-subscription-and-storage-account"></a>Establecimiento de la cuenta de suscripción y almacenamiento

1. Determinar la suscripción de Azure que usará para la nueva máquina virtual.

   ```powershell
   (Get-AzureSubscription).SubscriptionName
   ```

2. Asignar su suscripción de Azure de destino a la variable **$subscr** . Establézcala como su suscripción de Azure actual.

   ```powershell
   $subscr="<subscription name>"
   Select-AzureSubscription -SubscriptionName $subscr –Current
   ```

3. Luego busque las cuentas de almacenamiento existentes. El siguiente script muestra todas las cuentas de almacenamiento que existen en la región que elija:

   ```powershell
   (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName
   ```

   > [!NOTE]
   > Si necesita una nueva cuenta de almacenamiento, cree primero un nombre de cuenta de almacenamiento en minúsculas con el comando New-AzureStorageAccount, tal como se muestra en el siguiente ejemplo: `New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation`

4. Asigne el nombre de la cuenta de almacenamiento de destino en **$staccount**. A continuación, use **Set-AzureSubscription** para establecer la suscripción y la cuenta de almacenamiento actual.

   ```powershell
   $staccount="<storage account name>"
   Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
   ```

## <a name="select-a-sql-server-virtual-machine-image"></a>Seleccionar una imagen de máquina virtual de SQL Server

1. Averigüe la lista de imágenes de máquinas virtuales de SQL Server disponibles de la galería. Todas estas imágenes tendrán una propiedad **ImageFamily** que empieza por "SQL". La consulta siguiente muestra la familia de imágenes que tiene a su disposición que tiene SQL Server preinstalado.

   ```powershell
   Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily
   ```

2. Cuando encuentre la familia de imágenes de la máquina virtual, podría haber varias imágenes publicadas en esta familia. Use el siguiente script para buscar el nombre de la imagen máquina virtual publicada más reciente para su familia de imágenes seleccionada (como **SQL Server 2016 RTM Enterprise en Windows Server 2012 R2**):

   ```powershell
   $family="<ImageFamily value>"
   $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

   echo "Selected SQL Server image name:"
   echo "   $image"
   ```

## <a name="create-the-virtual-machine"></a>Creación de la máquina virtual

Por último, cree la máquina virtual con la PowerShell:

1. Cree un servicio en la nube para hospedar la nueva máquina virtual. Tenga en cuenta que también es posible utilizar un servicio en la nube existente en su lugar. Cree una nueva variable **$svcname** con el nombre corto del servicio en la nube.

   ```powershell
   $svcname = "<cloud service name>"
   New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
   ```

2. Especifique el nombre de la máquina virtual y un tamaño. Para obtener información sobre los tamaños de máquina virtual, consulte [Tamaños de máquina virtual para Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

   ```powershell
   $vmname="<machine name>"
   $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
   $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image
   ```

3. Especifique la cuenta de administrador local y la contraseña.

   ```powershell
   $cred=Get-Credential -Message "Type the name and password of the local administrator account."
   $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
   ```

4. Ejecute el script siguiente para crear la máquina virtual.

   ```powershell
   New-AzureVM –ServiceName $svcname -VMs $vm1
   ```

> [!NOTE]
> Para obtener una explicación adicional y opciones de configuración, vea la sección **Generar su conjunto de comandos** en [Usar Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Windows](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="example-powershell-script"></a>Script de PowerShell de ejemplo

El siguiente script ofrece un ejemplo de un script completo que crea una máquina virtual de **SQL Server 2016 RTM Enterprise en Windows Server 2012 R2**. Si usa este script, debe personalizar las variables iniciales basadas en los pasos anteriores de este tema.

```powershell
# Customize these variables based on your settings and requirements:
$dcLocation = "East US"
$subscr="mysubscription"
$staccount="mystorageaccount"
$family="SQL Server 2016 RTM Enterprise on Windows Server 2012 R2"
$svcname = "mycloudservice"
$vmname="myvirtualmachine"
$vmsize="A5"

# Set the current subscription and storage account
# Comment out the New-AzureStorageAccount line if the account already exists
Select-AzureSubscription -SubscriptionName $subscr –Current
New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

# Select the most recent VM image in this image family:
$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

# Create the new cloud service; comment out this line if cloud service exists already:
New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

# Create the VM config:
$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

# Set administrator credentials:
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

# Create the SQL Server VM:
New-AzureVM –ServiceName $svcname -VMs $vm1
```

## <a name="connect-with-remote-desktop"></a>Conexión con el Escritorio remoto

1. Cree los archivos .RDP en la carpeta de documentos del usuario actual para iniciar estas máquinas virtuales para completar la instalación:

   ```powershell
   $documentspath = [environment]::getfolderpath("mydocuments")
   Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"
   ```

2. En el directorio de documentos, inicie el archivo RDP. Póngase en contacto con el nombre de usuario de administrador y la contraseña que se ofreció anteriormente (por ejemplo, si su nombre de usuario era VMAdmin, especifique "\VMAdmin" como el usuario y ofrezca la contraseña).

   ```powershell
   cd $documentspath
   .\vm1.rdp
   ```

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Completar la configuración de la máquina de SQL Server para acceso remoto

Después de iniciar sesión en el equipo con Escritorio remoto, configure SQL Server según las instrucciones de los [Pasos para configurar la conectividad de SQL Server en una máquina virtual de Azure](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## <a name="next-steps"></a>Pasos siguientes

Puede encontrar instrucciones adicionales para el aprovisionamiento de máquinas virtuales con PowerShell en la [documentación de máquinas virtuales](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

En muchos casos, el siguiente paso es migrar las bases de datos a esta nueva VM de SQL Server. Para obtener instrucciones sobre la migración de bases de datos, consulte [Migración de una base de datos a SQL Server en una máquina virtual de Azure](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Si también le interesa utilizar Azure Portal para crear máquinas virtuales de SQL, consulte [Provisioning a SQL Server Virtual Machine on Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md)(Aprovisionamiento de una máquina virtual de SQL Server en Azure Portal). Tenga en cuenta que el tutorial que le guiará a través del portal crea máquinas virtuales con el modelo recomendado del Administrador de recursos, en lugar de con el modelo clásico usado en este tema de PowerShell.

Además de estos recursos, le recomendamos que revise [otros temas relacionados con la ejecución de SQL Server en Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
