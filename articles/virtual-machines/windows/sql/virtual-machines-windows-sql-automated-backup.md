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
ms.date: 01/30/2017
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 253c504fa433c7ca37c0065ebf01d13dafc76231
ms.openlocfilehash: c4cf6ab29ebf5b3397017cf754ee04bf57ab1555


---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Copia de seguridad automatizada para SQL Server 2014 en Azure Virtual Machines (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-automated-backup.md)
> * [Clásico](../sqlclassic/virtual-machines-windows-classic-sql-automated-backup.md)
> 
> 

Copia de seguridad automatizada configura automáticamente [Copia de seguridad administrada para Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todas las bases de datos existentes y nuevas en una máquina virtual de Azure que ejecuta SQL Server 2014 Standard y Enterprise. Esto le permite configurar copias de seguridad de datos normales que utilizan el almacenamiento de blobs de Azure. Copia de seguridad automatizada se basa en la [Extensión Agente de IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Para ver la versión clásica de este artículo, consulte [Copia de seguridad automatizada para SQL Server en máquinas virtuales de Azure (implementación clásica)](../sqlclassic/virtual-machines-windows-classic-sql-automated-backup.md).

## <a name="prerequisites"></a>Requisitos previos
Para utilizar Copia de seguridad automatizada, tenga en cuenta los siguientes requisitos previos:

**Sistema operativo**:

- Windows Server 2012
- Windows Server 2012 R2

**Edición/versión de SQL Server**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> Copia de seguridad automatizada funciona con SQL Server 2014. Si usa SQL Server 2016, puede usar Copia de seguridad automatizada v2 para hacer copias de seguridad de las bases de datos. Para obtener más información, vea [Copia de seguridad automatizada v2 para SQL Server 2016 en Azure Virtual Machines](virtual-machines-windows-sql-automated-backup-v2.md).

**Configuración de base de datos**:

- Las bases de datos de destino deben utilizar el modelo de recuperación completa.

Para obtener más información sobre el impacto del modelo de recuperación completa en las copias de seguridad, vea [Copia de seguridad en el modelo de recuperación completa](https://technet.microsoft.com/library/ms190217.aspx).

**Modelo de implementación de Azure**:

- Resource Manager

**Azure PowerShell**:

- [Instale los comandos de Azure PowerShell más recientes](/powershell/azureps-cmdlets-docs) si planea configurar Copia de seguridad automatizada con PowerShell.

> [!NOTE]
> Copia de seguridad automatizada se basa en la Extensión Agente de IaaS de SQL Server. Las imágenes actuales de la galería de máquinas virtuales de SQL agregan esta extensión de manera predeterminada. Para más información, consulte la [extensión Agente de IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Settings
En la siguiente tabla se describen las opciones que pueden configurarse para Copia de seguridad automatizada. Los pasos de configuración reales varían si usa el Portal de Azure o comandos de Windows PowerShell de Azure.

| Configuración | Intervalo (valor predeterminado) | Descripción |
| --- | --- | --- |
| **Copia de seguridad automatizada** | Habilitar/deshabilitar (deshabilitado) | Habilita o deshabilita la copia de seguridad automatizada para una máquina virtual de Azure que ejecuta SQL Server 2014 Standard o Enterprise. |
| **Período de retención** | 1-30 días (30 días) | El número de días para retener una copia de seguridad. |
| **Storage Account** | Cuenta de almacenamiento de Azure | Una cuenta de almacenamiento de Azure que usar para almacenar archivos de Copia de seguridad automatizada en Almacenamiento de blobs. Se crea un contenedor en esta ubicación para guardar todos los archivos de copia de seguridad. La convención de nomenclatura del archivo de copia de seguridad incluye la fecha, la hora y el nombre de máquina. |
| **Cifrado** | Habilitar/deshabilitar (deshabilitado) | Habilita o deshabilita el cifrado. Cuando se habilita el cifrado, los certificados usados para restaurar la copia de seguridad se ubican en la cuenta de almacenamiento especificada en el mismo contenedor `automaticbackup` con la misma convención de nomenclatura. Si la contraseña cambia, se genera un nuevo certificado con esa contraseña, pero el certificado antiguo permanece para restaurar copias de seguridad anteriores. |
| **Password** | Texto de contraseña | Una contraseña para claves de cifrado. Esto solo es necesario si se habilita el cifrado. Para restaurar una copia de seguridad cifrada, debe disponer de la contraseña correcta y del certificado relacionado que se usó en el momento en el que se realizó la copia de seguridad. |

## <a name="configuration-in-the-portal"></a>Configuración en el Portal
Puede usar Azure Portal para configurar Copia de seguridad automatizada durante el aprovisionamiento o para las máquinas virtuales existentes de SQL Server 2014.

### <a name="new-vms"></a>Nuevas máquinas virtuales
Utilice Azure Portal para configurar la opción Copia de seguridad automatizada cuando cree una nueva máquina virtual con SQL Server 2014 en el modelo de implementación de Resource Manager.

En la hoja **Configuración de SQL Server**, seleccione **Copia de seguridad automatizada**. La siguiente captura de pantalla del Portal de Azure muestra la hoja **Copia de seguridad automatizada de SQL** .

![Configuración de Copia de seguridad automatizada de SQL en el Portal de Azure](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

Para conocer el contexto, consulte el tema completo en [Aprovisionamiento de una máquina virtual de SQL Server en Azure Portal](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Máquinas virtuales existentes
Para las máquinas virtuales de SQL Server existentes, seleccione su máquina virtual de SQL Server. Después, seleccione la sección **Configuración de SQL Server** de la hoja **Configuración**.

![Copia de seguridad automatizada de SQL para máquinas virtuales existentes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

En la hoja **Configuración de SQL Server**, haga clic en el botón **Editar** de la sección Copia de seguridad automatizada.

![Configuración de Copia de seguridad automatizada de SQL para máquinas virtuales existentes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

Cuando termine, haga clic en el botón **Aceptar** situado en la parte inferior de la hoja **Configuración de SQL Server** para guardar los cambios.

Si habilita Copia de seguridad automatizada por primera vez, Azure configura el Agente de IaaS de SQL Server en segundo plano. Durante este tiempo, es posible que el Portal de Azure no muestre que se ha configurado Copia de seguridad automatizada. Espere unos minutos hasta que el agente se instale y configure. Después, el Portal de Azure mostrará la nueva configuración.

> [!NOTE]
> También puede usar una plantilla para configurar Copia de seguridad automatizada. Para más información, consulte [la plantilla de inicio rápido de Azure para Copia de seguridad automatizada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).
> 
> 

## <a name="configuration-with-powershell"></a>Configuración con PowerShell
Tras aprovisionar la máquina virtual de SQL, use PowerShell para configurar la copia de seguridad automatizada. Antes de comenzar:

- [Descargue e instale la última versión de Azure PowerShell](http://aka.ms/webpi-azps).
- Abra Windows PowerShell y asócielo con su cuenta. Puede hacerlo siguiendo los pasos descritos en la sección [Configuración de su suscripción](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-ps-sql-create#configure-your-subscription) del tema sobre aprovisionamiento.

En el siguiente ejemplo de PowerShell, se configura Copia de seguridad automatizada para una máquina virtual de SQL Server 2014 existente. El comando **AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig** configura los valores de Copia de seguridad automatizada para almacenar copias de seguridad en la cuenta de almacenamiento de Azure asociada con la máquina virtual. Estas copias de seguridad se conservarán durante 10 días. El comando **Set-AzureRmVMSqlServerExtension** actualiza la máquina virtual de Azure especificada con esta configuración.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriodInDays 10 -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

La instalación y configuración del agente de Iaas de SQL Server puede tardar algunos minutos.

Para habilitar el cifrado, modifique el script anterior para pasar el parámetro **EnableEncryption** junto con una contraseña (cadena segura) para el parámetro **CertificatePassword**. El siguiente script habilita la configuración de Copia de seguridad automatizada en el ejemplo anterior y agrega cifrado.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

Para deshabilitar la copia de seguridad automática, ejecute el mismo script sin el parámetro **-Enable** en el comando **AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig**. La ausencia del parámetro **-Enable** indica al comando que deshabilite la característica. Al igual que la instalación, la deshabilitación de Copia de seguridad automatizada puede tardar algunos minutos.

> [!NOTE]
> La desinstalación del agente de IaaS de SQL Server no quita los ajustes de Copia de seguridad automatizada configurados anteriormente. Debe deshabilitar Copia de seguridad automatizada antes de deshabilitar o desinstalar el agente de Iaas de SQL Server.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Copia de seguridad automatizada configura Copia de seguridad administrada en máquinas virtuales de Azure. Por lo tanto, es importante [revisar la documentación de la Copia de seguridad administrada](https://msdn.microsoft.com/library/dn449496.aspx) para comprender el comportamiento y las implicaciones.

Puede encontrar directrices adicionales sobre la copia de seguridad y la restauración para SQL Server en máquinas virtuales de Azure en el siguiente tema: [Copias de seguridad y restauración para SQL Server en Máquinas virtuales de Azure](virtual-machines-windows-sql-backup-recovery.md).

Para más información acerca de otras tareas de automatización disponibles, consulte la [extensión Agente de IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Para más información sobre cómo ejecutar SQL Server en máquinas virtuales de Azure, consulte [Introducción a SQL Server en máquinas virtuales de Azure](virtual-machines-windows-sql-server-iaas-overview.md).




<!--HONumber=Jan17_HO5-->


