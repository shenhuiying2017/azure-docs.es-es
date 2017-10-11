---
title: Copia de seguridad automatizada para SQL Server 2014 en Azure Virtual Machines | Microsoft Docs
description: "Se explica la característica Copia de seguridad automatizada para SQL Server 2014 en máquinas virtuales que se ejecutan en Azure. Este artículo trata exactamente sobre las máquinas virtuales que usan Resource Manager."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/05/2017
ms.author: jroth
ms.openlocfilehash: 91aab896dd5f06c950ee0ed8f36cc6a953d91611
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Copia de seguridad automatizada para SQL Server 2014 en Azure Virtual Machines (Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016](virtual-machines-windows-sql-automated-backup-v2.md)

Copia de seguridad automatizada configura automáticamente [Copia de seguridad administrada para Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todas las bases de datos existentes y nuevas en una máquina virtual de Azure que ejecuta SQL Server 2014 Standard y Enterprise. Esto le permite configurar copias de seguridad de datos normales que utilizan el almacenamiento de blobs de Azure. Copia de seguridad automatizada se basa en la [Extensión Agente de IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Requisitos previos
Para utilizar Copia de seguridad automatizada, tenga en cuenta los siguientes requisitos previos:

**Sistema operativo**:

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

**Edición/versión de SQL Server**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> Copia de seguridad automatizada funciona con SQL Server 2014. Si usa SQL Server 2016, puede usar Copia de seguridad automatizada v2 para hacer copias de seguridad de las bases de datos. Para obtener más información, vea [Copia de seguridad automatizada v2 para SQL Server 2016 en Azure Virtual Machines](virtual-machines-windows-sql-automated-backup-v2.md).

**Configuración de base de datos**:

- Las bases de datos de destino deben utilizar el modelo de recuperación completa. Para obtener más información sobre el impacto del modelo de recuperación completa en las copias de seguridad, vea [Copia de seguridad en el modelo de recuperación completa](https://technet.microsoft.com/library/ms190217.aspx).
- Las bases de datos de destino deben estar en la instancia predeterminada de SQL Server. La extensión de IaaS de SQL Server no admite instancias con nombre.

**Modelo de implementación de Azure**:

- Resource Manager

**Azure PowerShell**:

- [Instale los comandos de Azure PowerShell más recientes](/powershell/azure/overview) si planea configurar Copia de seguridad automatizada con PowerShell.

> [!NOTE]
> Copia de seguridad automatizada se basa en la Extensión Agente de IaaS de SQL Server. Las imágenes actuales de la galería de máquinas virtuales de SQL agregan esta extensión de manera predeterminada. Para más información, consulte la [extensión Agente de IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Settings

En la siguiente tabla se describen las opciones que pueden configurarse para Copia de seguridad automatizada. Los pasos de configuración reales varían si usa Azure Portal o comandos de Windows PowerShell de Azure.

| Configuración | Intervalo (valor predeterminado) | Descripción |
| --- | --- | --- |
| **Copia de seguridad automatizada** | Habilitar/deshabilitar (deshabilitado) | Habilita o deshabilita la copia de seguridad automatizada para una máquina virtual de Azure que ejecuta SQL Server 2014 Standard o Enterprise. |
| **Período de retención** | 1-30 días (30 días) | El número de días para retener una copia de seguridad. |
| **Storage Account** | Cuenta de Azure Storage | Una cuenta de Azure Storage que usar para almacenar archivos de Copia de seguridad automatizada en Blob Storage. Se crea un contenedor en esta ubicación para guardar todos los archivos de copia de seguridad. La convención de nomenclatura del archivo de copia de seguridad incluye la fecha, la hora y el nombre de máquina. |
| **Cifrado** | Habilitar/deshabilitar (deshabilitado) | Habilita o deshabilita el cifrado. Cuando se habilita el cifrado, los certificados usados para restaurar la copia de seguridad se ubican en la cuenta de almacenamiento especificada en el mismo contenedor `automaticbackup` con la misma convención de nomenclatura. Si la contraseña cambia, se genera un nuevo certificado con esa contraseña, pero el certificado antiguo permanece para restaurar copias de seguridad anteriores. |
| **Password** | Texto de contraseña | Una contraseña para claves de cifrado. Esto solo es necesario si se habilita el cifrado. Para restaurar una copia de seguridad cifrada, debe disponer de la contraseña correcta y del certificado relacionado que se usó en el momento en el que se realizó la copia de seguridad. |

## <a name="configuration-in-the-portal"></a>Configuración en el Portal

Puede usar Azure Portal para configurar Copia de seguridad automatizada durante el aprovisionamiento o para las máquinas virtuales existentes de SQL Server 2014.

### <a name="new-vms"></a>Nuevas máquinas virtuales

Utilice Azure Portal para configurar la opción Copia de seguridad automatizada cuando cree una nueva máquina virtual con SQL Server 2014 en el modelo de implementación de Resource Manager.

En la hoja **Configuración de SQL Server**, seleccione **Copia de seguridad automatizada**. La siguiente captura de pantalla de Azure Portal muestra la hoja **Copia de seguridad automatizada de SQL** .

![Configuración de Copia de seguridad automatizada de SQL en Azure Portal](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

Para conocer el contexto, consulte el tema completo en [Aprovisionamiento de una máquina virtual de SQL Server en Azure Portal](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Máquinas virtuales existentes

Para las máquinas virtuales de SQL Server existentes, seleccione su máquina virtual de SQL Server. Después, seleccione la sección **Configuración de SQL Server** de la hoja **Configuración**.

![Copia de seguridad automatizada de SQL para máquinas virtuales existentes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

En la hoja **Configuración de SQL Server**, haga clic en el botón **Editar** de la sección Copia de seguridad automatizada.

![Configuración de Copia de seguridad automatizada de SQL para máquinas virtuales existentes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

Cuando termine, haga clic en el botón **Aceptar** situado en la parte inferior de la hoja **Configuración de SQL Server** para guardar los cambios.

Si habilita Copia de seguridad automatizada por primera vez, Azure configura el Agente de IaaS de SQL Server en segundo plano. Durante este tiempo, es posible que Azure Portal no muestre que se ha configurado Copia de seguridad automatizada. Espere unos minutos hasta que el agente se instale y configure. Después, Azure Portal mostrará la nueva configuración.

> [!NOTE]
> También puede usar una plantilla para configurar Copia de seguridad automatizada. Para más información, consulte [la plantilla de inicio rápido de Azure para Copia de seguridad automatizada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configuration-with-powershell"></a>Configuración con PowerShell

Puede usar PowerShell para configurar Copia de seguridad automatizada. Antes de comenzar:

- [Descargue e instale la última versión de Azure PowerShell](http://aka.ms/webpi-azps).
- Abra Windows PowerShell y asócielo con su cuenta. Puede hacerlo siguiendo los pasos descritos en la sección [Configuración de su suscripción](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-ps-sql-create#configure-your-subscription) del tema sobre aprovisionamiento.

### <a name="install-the-sql-iaas-extension"></a>Instalación de la extensión IaaS de SQL Server
Si se aprovisiona una máquina virtual con SQL Server desde Azure Portal, la extensión IaaS de SQL Server también debe estar instalada. Puede determinar si está instalada para la VM llamando al comando **Get-AzureRmVM** y examinando la propiedad **Extensiones**.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzureRmVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions
```

Si la extensión del agente IaaS de SQL Server está instalada, debe aparecer como "SqlIaaSAgent" o "SQLIaaSExtension". **ProvisioningState** para la extensión también debería aparecer como “Correcto”.

Si no está instalada o no se ha podido aprovisionar, puede instalarla con el comando siguiente. Además del grupo de recursos y del nombre de VM, también debe especificar la región (**$region**) en que se encuentra dicha VM.

```powershell
$region = “EASTUS2”
Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region
```

### <a id="verifysettings"></a> Verificación de la configuración actual

Si ha habilitado la copia de seguridad automatizada durante el aprovisionamiento, puede usar PowerShell para comprobar la configuración actual. Ejecute el comando **Get-AzureRmVMSqlServerExtension** y examine la propiedad **AutoBackupSettings**:

```powershell
(Get-AzureRmVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Debería obtener una salida similar a la siguiente:

```
Enable                      : False
EnableEncryption            : False
RetentionPeriod             : -1
StorageUrl                  : NOTSET
StorageAccessKey            : 
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : 
FullBackupFrequency         : 
FullBackupStartTime         : 
FullBackupWindowHours       : 
LogBackupFrequency          : 
```

Si la salida muestra que la opción **Habilitar** está establecida en **False**, tiene que habilitar la copia de seguridad automatizada. Lo bueno es que habilita y configura la copia de seguridad automatizada de la misma manera. Vea la sección siguiente para leer esta información.

> [!NOTE] 
> Si comprueba la configuración inmediatamente después de realizar un cambio, es posible que obtenga los valores de configuración anteriores. Espere unos minutos y compruebe la configuración de nuevo para asegurarse de que se hayan aplicado los cambios.

### <a name="configure-automated-backup"></a>Configurar Copia de seguridad automatizada
Puede usar PowerShell para habilitar la copia de seguridad automatizada, así como para modificar su configuración y comportamiento en cualquier momento.

En primer lugar, seleccione o cree una cuenta de almacenamiento para los archivos de copia de seguridad. El script siguiente selecciona una cuenta de almacenamiento o la crea si no existe.

```powershell
$storage_accountname = “yourstorageaccount”
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }
```

> [!NOTE]
> Copia de seguridad automatizada no permite almacenar las copias de seguridad en Premium Storage, pero pueden realizar copias de seguridad de discos de VM que usan Premium Storage.

Luego use el comando **New-AzureRmVMSqlServerAutoBackupConfig** para habilitar y configurar los valores de Copia de seguridad automatizada para almacenar copias de seguridad en la cuenta de Azure Storage. En este ejemplo, las copias de seguridad están configuradas para que se conserven durante 10 días. El segundo comando, **Set-AzureRmVMSqlServerExtension**, actualiza la VM de Azure especificada con esta configuración.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

La instalación y configuración del agente de Iaas de SQL Server puede tardar algunos minutos.

> [!NOTE]
> Hay otras opciones de **New-AzureRmVMSqlServerAutoBackupConfig** que solo se aplican a SQL Server 2016 y a Copia de seguridad automatizada v2. SQL Server 2014 no es compatible con las siguientes opciones: **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**, **FullBackupStartHour**, **FullBackupWindowInHours** y **LogBackupFrequencyInMinutes**. Si intenta configurar estas opciones en una máquina virtual de SQL Server 2014, no hay ningún error, pero no se aplica la configuración. Si quiere usar estas opciones en una máquina virtual de SQL Server 2016, vea [Copia de seguridad automatizada v2 para Azure Virtual Machines con SQL Server 2016 (Resource Manager)](virtual-machines-windows-sql-automated-backup-v2.md).

Para habilitar el cifrado, modifique el script anterior para pasar el parámetro **EnableEncryption** junto con una contraseña (cadena segura) para el parámetro **CertificatePassword**. El siguiente script habilita la configuración de Copia de seguridad automatizada en el ejemplo anterior y agrega cifrado.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Para confirmar que se ha aplicado la configuración, [verifique la configuración de Copia de seguridad automatizada](#verifysettings).

### <a name="disable-automated-backup"></a>Deshabilitar Copia de seguridad automatizada

Para deshabilitar Copia de seguridad automatizada, ejecute el mismo script sin el parámetro **-Enable** en el comando **New-AzureRmVMSqlServerAutoBackupConfig**. La ausencia del parámetro **-Enable** indica al comando que deshabilite la característica. Al igual que la instalación, la deshabilitación de Copia de seguridad automatizada puede tardar algunos minutos.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Script de ejemplo

El script siguiente proporciona un conjunto de variables que se pueden personalizar para habilitar y configurar Copia de seguridad automatizada para la VM. En su caso, debe personalizar el script en función de sus requisitos. Por ejemplo, debe realizar cambios si desea deshabilitar la copia de seguridad de bases de datos del sistema o habilitar el cifrado.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = “Azure region name such as EASTUS2”
$storage_accountname = “storageaccountname”
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL IaaS Extension

Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

# Apply the Automated Backup settings to the VM

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Pasos siguientes

Copia de seguridad automatizada configura Copia de seguridad administrada en máquinas virtuales de Azure. Por lo tanto, es importante [revisar la documentación de la Copia de seguridad administrada](https://msdn.microsoft.com/library/dn449496.aspx) para comprender el comportamiento y las implicaciones.

Puede encontrar directrices adicionales sobre la copia de seguridad y la restauración para SQL Server en máquinas virtuales de Azure en el siguiente tema: [Copias de seguridad y restauración para SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

Para más información acerca de otras tareas de automatización disponibles, consulte la [extensión Agente de IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Para más información sobre cómo ejecutar SQL Server en máquinas virtuales de Azure, consulte [Introducción a SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).

