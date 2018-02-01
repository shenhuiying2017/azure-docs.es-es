---
title: "Automated Backup de máquinas virtuales SQL Server (modelo clásico) | Microsoft Docs"
description: "Explica la característica Automated Backup para SQL Server que se ejecuta en Azure Virtual Machines mediante Resource Manager. "
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 3333e830-8a60-42f5-9f44-8e02e9868d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/23/2018
ms.author: jroth
ms.openlocfilehash: 81910dd9c2743484ca65bd21e400a1a52e961a19
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Automated Backup para SQL Server en Azure Virtual Machines (implementación clásica)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [Clásico](../classic/sql-automated-backup.md)
> 
> 

Automated Backup configura automáticamente [Automated Backup para Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todas las bases de datos existentes y nuevas en una máquina virtual de Azure que ejecuta SQL Server 2014 Standard y Enterprise. Esto le permite configurar copias de seguridad de datos normales que utilizan el almacenamiento de blobs de Azure. Automated Backup se basa en la [Extensión Agente de IaaS de SQL Server](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../azure-resource-manager/resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo de Resource Manager. Para ver la versión de Resource Manager de este artículo, consulte [Automated Backup para SQL Server en Azure Virtual Machines (Resource Manager)](../sql/virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>requisitos previos
Para utilizar Automated Backup, tenga en cuenta los siguientes requisitos previos:

**Sistema operativo**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Edición/versión de SQL Server**:

* SQL Server 2014 Standard
* SQL Server 2014 Enterprise

> [!NOTE]
> Automated Backup para SQL Server 2016 es compatible con las máquinas virtuales de Resource Manager. Para más información, consulte [Automated Backup v2 para Azure Virtual Machines con SQL Server 2016 (Resource Manager)](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2).

**Configuración de base de datos**:

* Las bases de datos de destino deben utilizar el modelo de recuperación completa.

**Azure PowerShell**:

* [Instale los cmdlets más recientes de Azure PowerShell](/powershell/azure/overview).

**Extensión IaaS de SQL Server**:

* [Instale la extensión IaaS de SQL Server](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Configuración
En la siguiente tabla se describen las opciones que pueden configurarse para Automated Backup. Para las máquinas virtuales clásicas, debe usar PowerShell para configurar estas opciones.

| Configuración | Intervalo (valor predeterminado) | DESCRIPCIÓN |
| --- | --- | --- |
| **Automated Backup** |Habilitar/deshabilitar (deshabilitado) |Habilita o deshabilita Automated Backup para una máquina virtual de Azure que ejecuta SQL Server 2014 Standard o Enterprise. |
| **Período de retención** |1-30 días (30 días) |El número de días para retener una copia de seguridad. |
| **Storage Account** |Cuenta de almacenamiento de Azure (la cuenta de almacenamiento creada para la máquina virtual especificada) |Una cuenta de almacenamiento de Azure que usar para almacenar archivos de Automated Backup en el almacenamiento de blobs. Se crea un contenedor en esta ubicación para guardar todos los archivos de copia de seguridad. La convención de nomenclatura del archivo de copia de seguridad incluye la fecha, la hora y el nombre de máquina. |
| **Cifrado** |Habilitar/deshabilitar (deshabilitado) |Habilita o deshabilita el cifrado. Cuando se habilita el cifrado, los certificados usados para restaurar la copia de seguridad se ubican en la cuenta de almacenamiento especificada en el mismo contenedor de copia de seguridad automatizada con la misma convención de nomenclatura. Si la contraseña cambia, se genera un nuevo certificado con esa contraseña, pero el certificado antiguo permanece para restaurar copias de seguridad anteriores. |
| **Password** |Texto de contraseña (ninguno) |Una contraseña para claves de cifrado. Esto solo es necesario si se habilita el cifrado. Para restaurar una copia de seguridad cifrada, debe disponer de la contraseña correcta y del certificado relacionado que se usó en el momento en el que se realizó la copia de seguridad. | **Backup system databases** (Copia de seguridad de bases de datos del sistema) | Habilitar/deshabilitar (deshabilitado) | Realiza copias de seguridad completas de Master, Model y MSDB |
| **Configure backup schedule** (Configurar programación de copia de seguridad) | Manual/Automatizado (Automatizado) | Seleccione **Automatizado** para realizar copias de seguridad completas y de registros en función del crecimiento de registros. Seleccione **Manual** a fin de especificar la programación para copias de seguridad completas y de registros. |

## <a name="configuration-with-powershell"></a>Configuración con PowerShell
En el siguiente ejemplo de PowerShell, se configura Automated Backup para una máquina virtual de SQL Server 2014 existente. El comando **New-AzureVMSqlServerAutoBackupConfig** configura los valores de Copia de seguridad automatizada para almacenar copias de seguridad en la cuenta de almacenamiento de Azure especificada por la variable $storageaccount. Estas copias de seguridad se conservarán durante 10 días. El comando **Set-AzureVMSqlServerExtension** actualiza la máquina virtual de Azure especificada con esta configuración.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

La instalación y configuración del agente de Iaas de SQL Server puede tardar algunos minutos.

Para habilitar el cifrado, modifique el script anterior para pasar el parámetro EnableEncryption junto con una contraseña (cadena segura) para el parámetro CertificatePassword. El siguiente script habilita la configuración de Automated Backup en el ejemplo anterior y agrega cifrado.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Para deshabilitar la copia de seguridad automática, ejecute el script sin el parámetro **-Enable** en **New-AzureVMSqlServerAutoBackupConfig**. Al igual que la instalación, la deshabilitación de Automated Backup puede tardar algunos minutos.

> [!NOTE]
> La deshabilitación y la desinstalación del agente de Iaas de SQL Server no quita los ajustes de Copia de seguridad administrada configurados anteriormente. Debe deshabilitar Automated Backup antes de deshabilitar o desinstalar el agente de Iaas de SQL Server.
> 
> 

## <a name="next-steps"></a>pasos siguientes
Automated Backup configura Copia de seguridad administrada en Azure Virtual Machines. Por lo tanto, es importante [revisar la documentación de la Copia de seguridad administrada](https://msdn.microsoft.com/library/dn449496.aspx) para comprender el comportamiento y las implicaciones.

Puede encontrar directrices adicionales sobre la copia de seguridad y la restauración para SQL Server en Azure Virtual Machines en el siguiente tema: [Copias de seguridad y restauración para SQL Server en Azure Virtual Machines](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Para más información acerca de otras tareas de automatización disponibles, consulte la [extensión Agente de IaaS de SQL Server](../classic/sql-server-agent-extension.md).

Para más información sobre cómo ejecutar SQL Server en máquinas virtuales de Azure, consulte [Introducción a SQL Server en Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

