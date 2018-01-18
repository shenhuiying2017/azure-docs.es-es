---
title: "Automatizar tareas de administración en máquinas virtuales de SQL (Resource Manager) | Microsoft Docs"
description: "En este artículo se describe cómo administrar la extensión Agente de SQL Server, que automatiza tareas de administración específicas de SQL Server. Entre ellas se incluyen la copia de seguridad automatizada, la aplicación de revisiones automatizada y la integración de Azure Key Vault."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/05/2018
ms.author: jroth
ms.openlocfilehash: 1d2b681660ae6f59dec8a287baa853085c64ebeb
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>Automatizar las tareas de administración en Azure Virtual Machines con la extensión del Agente SQL Server (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Clásico](../classic/sql-server-agent-extension.md)
> 
> 

La extensión del agente de IaaS SQL Server (SQLIaaSExtension) se ejecuta en Azure Virtual Machines para automatizar las tareas de administración. En este artículo se proporciona información general sobre los servicios admitidos por la extensión, así como instrucciones para la instalación, el estado y la eliminación.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Para ver la versión clásica de este artículo, consulte [Extensión del Agente SQL Server para VM con SQL Server (clásico)](../classic/sql-server-agent-extension.md).

## <a name="supported-services"></a>Servicios admitidos
La extensión del Agente de IaaS SQL Server es compatible con las siguientes tareas de administración:

| Característica de administración | DESCRIPCIÓN |
| --- | --- |
| **Automated Backup de SQL** |Automatiza la programación de copias de seguridad de todas las bases de datos para la instancia predeterminada de SQL Server en la máquina virtual. Para más información, consulte [Copia de seguridad automatizada para SQL Server en Azure Virtual Machines (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Aplicación de revisiones automatizada de SQL** |Configura una ventana de mantenimiento durante la cual tienen lugar las actualizaciones de la máquina virtual, de tal forma que puede evitar las actualizaciones durante las horas punta para la carga de trabajo. Para más información, consulte [Aplicación de revisión automatizada para SQL Server en Azure Virtual Machines (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Integración de Azure Key Vault** |Permite instalar y configurar automáticamente Azure Key Vault en la máquina virtual SQL Server. Para más información, consulte [Configuración de la integración de Azure Key Vault para SQL Server en máquinas virtuales de Azure (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md). |

Una vez instalada y en ejecución, la extensión Agente de IaaS de SQL Server permite que estas características de administración estén disponibles en el panel de SQL Server de la máquina virtual de Azure Portal y a través de Azure PowerShell, tanto para imágenes de Marketplace de SQL Server como para instalaciones manuales de la extensión. 

## <a name="prerequisites"></a>Requisitos previos
Requisitos para usar la extensión del Agente de IaaS SQL Server en la máquina virtual:

**Sistema operativo**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Versiones de SQL Server**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Descargar y configurar los comandos de Azure PowerShell más recientes](/powershell/azure/overview)

## <a name="installation"></a>Instalación
La extensión del Agente de IaaS SQL Server se instala automáticamente cuando aprovisiona una de las imágenes de la galería de máquina virtual de SQL Server. Si tiene que volver a instalar la extensión manualmente en una de estas máquinas virtuales con SQL Server, use el siguiente comando de PowerShell:

```powershell
Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2" -Location "East US 2"
```

> [!IMPORTANT]
> Si la extensión todavía no está instalada, su instalación reiniciará el servicio de SQL Server.

También es posible instalar la extensión del agente de IaaS de SQL Server en una máquina virtual de solo sistema operativo de Windows Server. Esto solo se permite si además ha instalado manualmente SQL Server en ese equipo. Luego instale la extensión manualmente mediante el cmdlet de PowerShell **Set-AzureVMSqlServerExtension**.

> [!NOTE]
> Si instala manualmente la extensión del agente de IaaS de SQL Server en una máquina virtual solo para el sistema operativo Windows Server, no puede administrar los valores de configuración de SQL Server mediante Azure Portal. En este escenario debe realizar todos los cambios con PowerShell.

## <a name="status"></a>Status
Una manera de comprobar que la extensión está instalada consiste en ver el estado del agente en Azure Portal. Seleccione **Todas las configuraciones** en la ventana de la máquina virtual y haga clic en **Extensiones**. Debería aparecer la extensión **SQLIaaSExtension** .

![Extensión del Agente de IaaS de SQL Server en Azure Portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

También puede usar el cmdlet de Azure PowerShell **Get-AzureVMSqlServerExtension**.

    Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

El comando anterior confirma que el agente está instalado y proporciona información de estado general. También puede obtener información de estado específica sobre Automated Backup y Automated Backup con los siguientes comandos.

    $sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Eliminación
En Azure Portal, puede desinstalar la extensión haciendo clic en los puntos suspensivos de la ventana **Extensiones** de las propiedades de la máquina virtual. Después, haga clic en **Eliminar**.

![Desinstalación de la extensión Agente de IaaS de SQL Server en Azure Portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

También puede usar el cmdlet de PowerShell **Remove-AzureRmVMSqlServerExtension**.

    Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## <a name="next-steps"></a>pasos siguientes
Empiece utilizando uno de los servicios admitidos por la extensión. Para más información, consulte los artículos a los que se hace referencia en la sección [Servicios admitidos](#supported-services) de este artículo.

Para obtener más información sobre cómo ejecutar SQL Server en Azure Virtual Machines, consulte [Información general sobre SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).

