---
title: Automated Backup v2 para Azure Virtual Machines con SQL Server 2016 | Microsoft Docs
description: "Se explica la característica Automated Backup para SQL Server 2016 en máquinas virtuales que se ejecutan en Azure. Este artículo trata exactamente sobre las máquinas virtuales que usan Resource Manager."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: ebd23868-821c-475b-b867-06d4a2e310c7
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/05/2017
ms.author: jroth
ms.openlocfilehash: e7e14b0243f82c672392d5ab4bb6aca01156465b
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="automated-backup-v2-for-sql-server-2016-azure-virtual-machines-resource-manager"></a>Automated Backup v2 para Azure Virtual Machines con SQL Server 2016 (Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016](virtual-machines-windows-sql-automated-backup-v2.md)

Automated Backup v2 configura automáticamente [Automated Backup para Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todas las bases de datos existentes y nuevas en una VM de Azure que ejecuta las versiones SQL Server 2016 Standard, Enterprise o Developer. Esto le permite configurar copias de seguridad de datos normales que utilizan el almacenamiento de blobs de Azure. Automated Backup v2 depende de la [Extensión del agente de IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Requisitos previos
Para utilizar Automated Backup v2, revise los siguientes requisitos previos:

**Sistema operativo**:

- Windows Server 2012 R2
- Windows Server 2016

**Edición/versión de SQL Server**:

- SQL Server 2016 Standard
- SQL Server 2016 Enterprise
- SQL Server 2016 Developer

> [!IMPORTANT]
> Automated Backup v2 funciona con SQL Server 2016. Si usa SQL Server 2014, puede usar Automated Backup v1 para hacer copias de seguridad de las bases de datos. Para obtener más información, vea [Automated Backup para SQL Server 2014 en Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).

**Configuración de base de datos**:

- Las bases de datos de destino deben utilizar el modelo de recuperación completa. Para obtener más información sobre el impacto del modelo de recuperación completa en las copias de seguridad, vea [Copia de seguridad en el modelo de recuperación completa](https://technet.microsoft.com/library/ms190217.aspx).
- Las bases de datos del sistema no tienen que usar el modelo de recuperación completa. Sin embargo, sí debe usar el modelo de recuperación completa si necesita realizar copias de seguridad de registros para el modelo o para MSDB.
- Las bases de datos de destino deben estar en la instancia predeterminada de SQL Server. La extensión de IaaS de SQL Server no admite instancias con nombre.

**Modelo de implementación de Azure**:

- Resource Manager

> [!NOTE]
> Automated Backup se basa en la **Extensión Agente de IaaS de SQL Server**. Las imágenes actuales de la galería de máquinas virtuales de SQL agregan esta extensión de manera predeterminada. Para más información, consulte la [extensión Agente de IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Configuración
En la siguiente tabla se describen las opciones que pueden configurarse para Automated Backup v2. Los pasos de configuración reales varían si usa el Portal de Azure o comandos de Windows PowerShell de Azure.

### <a name="basic-settings"></a>Configuración básica

| Configuración | Intervalo (valor predeterminado) | Descripción |
| --- | --- | --- |
| **Automated Backup** | Habilitar/deshabilitar (deshabilitado) | Habilita o deshabilita Automated Backup para una VM de Azure que ejecuta SQL Server 2016 Standard o Enterprise. |
| **Período de retención** | 1-30 días (30 días) | El número de días para retener copias de seguridad. |
| **Storage Account** | Cuenta de Azure Storage | Una cuenta de almacenamiento de Azure que usar para almacenar archivos de Automated Backup en el almacenamiento de blobs. Se crea un contenedor en esta ubicación para guardar todos los archivos de copia de seguridad. La convención de nomenclatura de los archivos de copia de seguridad agrega la fecha, la hora y el GUID de base de datos. |
| **Cifrado** |Habilitar/deshabilitar (deshabilitado) | Habilita o deshabilita el cifrado. Cuando se habilita el cifrado, los certificados usados para restaurar la copia de seguridad se ubican en la cuenta de almacenamiento especificada en el mismo contenedor de **copia de seguridad automatizada** con la misma convención de nomenclatura. Si la contraseña cambia, se genera un nuevo certificado con esa contraseña, pero el certificado antiguo permanece para restaurar copias de seguridad anteriores. |
| **Password** |Texto de contraseña | Una contraseña para claves de cifrado. Esto solo es necesario si se habilita el cifrado. Para restaurar una copia de seguridad cifrada, debe disponer de la contraseña correcta y del certificado relacionado que se usó en el momento en el que se realizó la copia de seguridad. |

### <a name="advanced-settings"></a>Configuración avanzada

| Configuración | Intervalo (valor predeterminado) | Descripción |
| --- | --- | --- |
| **Copias de seguridad de bases de datos del sistema** | Habilitar/deshabilitar (deshabilitado) | Cuando se habilita, esta característica realiza también una copia de seguridad de las bases de datos del sistema: maestra, MSDB y modelo. Para las bases de datos MSDB y modelo, verifique que están en modo de recuperación completa si desea que se realicen copias de seguridad de registros. Nunca se realizan copias de seguridad de registros para las bases de datos maestras. Tampoco se realizan copias de seguridad para TempDB. |
| **Programación de copia de seguridad** | Manual/Automatizado (Automatizado) | De forma predeterminada, la programación de copias de seguridad se determina automáticamente en función del crecimiento de registros. La programación manual de copias de seguridad permite al usuario especificar el período de tiempo para las copias de seguridad. En este caso, las copias de seguridad siempre se realizarán con la frecuencia especificada y durante el período de tiempo especificado de un día determinado. |
| **Frecuencia de copia de seguridad completa** | Diariamente/semanalmente | Frecuencia de las copias de seguridad completas. En ambos casos, las copias de seguridad completas se inician durante el siguiente período de tiempo programado. Cuando se selecciona semanalmente, las copias de seguridad pueden extenderse varios días hasta que se realizan correctamente las copias de seguridad de todas las bases de datos. |
| **Hora de inicio de copia de seguridad completa** | 00:00 – 23:00 (01:00) | Hora de inicio de un día determinado durante el cual se pueden realizar copias de seguridad completas. |
| **Período de tiempo de copia de seguridad completa** | 1-23 horas (1 hora) | Duración del período de tiempo de un día determinado durante el cual se pueden realizar copias de seguridad completas. |
| **Frecuencia de copia de seguridad de registros** | 5-60 minutos (60 minutos) | Frecuencia de las copias de seguridad de registros. |

## <a name="understanding-full-backup-frequency"></a>Información de la frecuencia de copia de seguridad completa
Es importante conocer la diferencia entre las copias de seguridad completas diarias y semanales. Para ello, se le guiará a través de dos escenarios de ejemplo.

### <a name="scenario-1-weekly-backups"></a>Escenario 1: Copias de seguridad semanales
Tiene una VM con SQL Server que contiene un número de bases de datos muy grandes.

El lunes, se habilita Automated Backup v2 con las siguientes opciones:

- Programación de copia de seguridad: **Manual**
- Frecuencia de copia de seguridad completa: **Semanal**
- Hora de inicio de copia de seguridad completa: **01:00**
- Período de tiempo de copia de seguridad completa: **1 hora**

Esto significa que la siguiente ventana de copia de seguridad disponible es el martes a la 1:00 durante una hora. En ese momento, la copia de seguridad empieza a realizar copias de seguridad de las bases de datos una a una. En este escenario, las bases de datos son lo suficientemente grandes como para que primero se realicen las copias de seguridad completas de las bases de datos pares. No obstante, después de una hora no se realiza la copia de seguridad de todas las bases de datos.

Cuando esto ocurre, Automated Backup comienza la copia de seguridad de las demás bases de datos al día siguiente, es decir, el miércoles a la 1:00 durante una hora. Si no se realiza la copia de seguridad de todas las bases de datos a esa hora, vuelve a intentarlo al día siguiente a la misma hora. Se aplica este mismo comportamiento hasta que se realiza correctamente la copia de seguridad de todas las bases de datos.

Cuando vuelve a llegar el martes, Automated Backup empieza de nuevo a realizar la copia de seguridad de todas las bases de datos.

En este escenario se muestra que Automated Backup solo funciona durante el período de tiempo especificado y que la copia de seguridad de cada base de datos se realiza una vez a la semana. También se muestra que las copias de seguridad se pueden extender varios días en caso de que no sea posible completar todas las copias de seguridad en un solo día.

### <a name="scenario-2-daily-backups"></a>Escenario 2: Copias de seguridad diarias
Tiene una VM con SQL Server que contiene un número de bases de datos muy grandes.

El lunes, se habilita Automated Backup v2 con las siguientes opciones:

- Programación de copia de seguridad: Manual
- Frecuencia de copia de seguridad completa: Diaria
- Hora de inicio de copia de seguridad completa: 22:00
- Período de tiempo de copia de seguridad completa: 6 horas

Esto significa que la siguiente ventana de copia de seguridad disponible es el lunes a la 22:00 durante seis horas. En ese momento, la copia de seguridad empieza a realizar copias de seguridad de las bases de datos una a una.

Posteriormente, se vuelve a realizar la copia de seguridad completa de todas las bases de datos el martes a las 22:00 horas durante seis horas.

> [!IMPORTANT]
> Al programar copias de seguridad diarias, se recomienda programar un período de tiempo amplio para asegurarse de se puede realizar la copia de seguridad de todas las bases de datos durante ese período. Esto es especialmente importante en el caso de que tenga una gran cantidad de datos de los que realizar una copia de seguridad.

## <a name="configuration-in-the-portal"></a>Configuración en el Portal

Puede usar Azure Portal para configurar Automated Backup v2 durante el aprovisionamiento o para las máquinas virtuales existentes de SQL Server 2016.

### <a name="new-vms"></a>Nuevas máquinas virtuales

Utilice Azure Portal para configurar la opción Automated Backup v2 cuando cree una nueva máquina virtual con SQL Server 2016 en el modelo de implementación de Resource Manager. 

En la hoja **Configuración de SQL Server**, seleccione **Copia de seguridad automatizada**. La siguiente captura de pantalla de Azure Portal muestra la hoja **Automated Backup de SQL**.

![Configuración de Automated Backup de SQL en Azure Portal](./media/virtual-machines-windows-sql-automated-backup-v2/automated-backup-blade.png)

> [!NOTE]
> Automated Backup v2 está deshabilitado de forma predeterminada.

Para conocer el contexto, consulte el tema completo en [Aprovisionamiento de una máquina virtual de SQL Server en Azure Portal](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Máquinas virtuales existentes

Para las máquinas virtuales de SQL Server existentes, seleccione su máquina virtual de SQL Server. Después, seleccione la sección **Configuración de SQL Server** de la hoja **Configuración**.

![Automated Backup de SQL para máquinas virtuales existentes](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration.png)

En la hoja **Configuración de SQL Server**, haga clic en el botón **Editar** de la sección Copia de seguridad automatizada.

![Configuración de Automated Backup de SQL para máquinas virtuales existentes](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration-edit.png)

Cuando termine, haga clic en el botón **Aceptar** situado en la parte inferior de la hoja **Configuración de SQL Server** para guardar los cambios.

Si habilita Automated Backup por primera vez, Azure configura el Agente de IaaS de SQL Server en segundo plano. Durante este tiempo, es posible que Azure Portal no muestre que se ha configurado Automated Backup. Espere unos minutos hasta que el agente se instale y configure. Después, el Portal de Azure mostrará la nueva configuración.

## <a name="configuration-with-powershell"></a>Configuración con PowerShell

Puede usar PowerShell para configurar Automated Backup v2. Antes de comenzar:

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
Enable                      : True
EnableEncryption            : False
RetentionPeriod             : 30
StorageUrl                  : https://test.blob.core.windows.net/
StorageAccessKey            :  
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : Manual
FullBackupFrequency         : WEEKLY
FullBackupStartTime         : 2
FullBackupWindowHours       : 2
LogBackupFrequency          : 60
```

Si la salida muestra que la opción **Habilitar** está establecida en **False**, tiene que habilitar la copia de seguridad automatizada. Lo bueno es que habilita y configura Automated Backup de la misma manera. Vea la sección siguiente para leer esta información.

> [!NOTE] 
> Si comprueba la configuración inmediatamente después de realizar un cambio, es posible que obtenga los valores de configuración anteriores. Espere unos minutos y compruebe la configuración de nuevo para asegurarse de que se hayan aplicado los cambios.

### <a name="configure-automated-backup-v2"></a>Configuración de Automated Backup v2
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
> Automated Backup no permite almacenar las copias de seguridad en Premium Storage, pero pueden realizar copias de seguridad de discos de VM que usan Premium Storage.

Luego use el comando **New-AzureRmVMSqlServerAutoBackupConfig** para habilitar y configurar los valores de Copia de seguridad automatizada v2 para almacenar copias de seguridad en la cuenta de Azure Storage. En este ejemplo, las copias de seguridad están configuradas para que se conserven durante 10 días. Las copias de seguridad de las bases de datos del sistema están habilitadas. Las copias de seguridad completas están programadas semanalmente con un período de tiempo que empieza a las 20:00 horas y dura dos horas. Las copias de seguridad de registros están programadas para que se realicen cada 30 minutos. El segundo comando, **Set-AzureRmVMSqlServerExtension**, actualiza la VM de Azure especificada con esta configuración.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname 
```

La instalación y configuración del agente de Iaas de SQL Server puede tardar algunos minutos. 

Para habilitar el cifrado, modifique el script anterior para pasar el parámetro **EnableEncryption** junto con una contraseña (cadena segura) para el parámetro **CertificatePassword**. El siguiente script habilita la configuración de Automated Backup en el ejemplo anterior y agrega cifrado.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Para confirmar que se ha aplicado la configuración, [verifique la configuración de Automated Backup](#verifysettings).

### <a name="disable-automated-backup"></a>Deshabilitar Automated Backup
Para deshabilitar Copia de seguridad automatizada, ejecute el mismo script sin el parámetro **-Enable** en el comando **New-AzureRmVMSqlServerAutoBackupConfig**. La ausencia del parámetro **-Enable** indica al comando que deshabilite la característica. Al igual que la instalación, la deshabilitación de Automated Backup puede tardar algunos minutos.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Script de ejemplo
El script siguiente proporciona un conjunto de variables que se pueden personalizar para habilitar y configurar Automated Backup para la VM. En su caso, debe personalizar el script en función de sus requisitos. Por ejemplo, debe realizar cambios si desea deshabilitar la copia de seguridad de bases de datos del sistema o habilitar el cifrado.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = “Azure region name such as EASTUS2”
$storage_accountname = “storageaccountname”
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10
$backupscheduletype = "Manual"
$fullbackupfrequency = "Weekly"
$fullbackupstarthour = "20"
$fullbackupwindow = "2"
$logbackupfrequency = "30"

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
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType $backupscheduletype -FullBackupFrequency $fullbackupfrequency `
    -FullBackupStartHour $fullbackupstarthour -FullBackupWindowInHours $fullbackupwindow `
    -LogBackupFrequencyInMinutes $logbackupfrequency

# Apply the Automated Backup settings to the VM

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Pasos siguientes
Automated Backup v2 configura Managed Backup en Azure Virtual Machines. Por lo tanto, es importante [revisar la documentación de la Copia de seguridad administrada](https://msdn.microsoft.com/library/dn449496.aspx) para comprender el comportamiento y las implicaciones.

Puede encontrar directrices adicionales sobre la copia de seguridad y la restauración para SQL Server en Azure Virtual Machines en el siguiente tema: [Copias de seguridad y restauración para SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

Para más información acerca de otras tareas de automatización disponibles, consulte la [extensión Agente de IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Para más información sobre cómo ejecutar SQL Server en Azure Virtual Machines, consulte [Introducción a SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).

