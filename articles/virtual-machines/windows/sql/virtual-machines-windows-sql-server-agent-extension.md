---
title: "Automatizar tareas de administración en máquinas virtuales de SQL (Resource Manager) | Microsoft Docs"
description: "En este tema se describe cómo administrar la extensión Agente de SQL Server, que automatiza tareas de administración específicas de SQL Server. Entre ellas se incluyen la copia de seguridad automatizada, la aplicación de revisiones automatizada y la integración del Almacén de claves de Azure. En este tema se usa el modelo de implementación de Resource Manager."
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
ms.date: 01/09/2017
ms.author: jroth
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 00832fcabfb3d1f6d9b7b9745335d4154a2caae3
ms.lasthandoff: 03/07/2017

---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>Automatizar las tareas de administración en máquinas de virtuales de Azure con la extensión del Agente SQL Server (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Clásico](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)
> 
> 

La extensión del agente de IaaS SQL Server (SQLIaaSExtension) se ejecuta en máquinas virtuales de Azure para automatizar las tareas de administración. En este tema se proporciona información general sobre los servicios admitidos por la extensión, así como instrucciones para la instalación, el estado y la eliminación.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Para ver la versión clásica de este artículo, consulte [Extensión del Agente SQL Server para VM con SQL Server (clásico)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="supported-services"></a>Servicios admitidos
La extensión del Agente de IaaS SQL Server es compatible con las siguientes tareas de administración:

| Característica de administración | Descripción |
| --- | --- |
| **Copia de seguridad automatizada de SQL** |Automatiza la programación de copias de seguridad de todas las bases de datos para la instancia predeterminada de SQL Server en la máquina virtual. Para más información, consulte [Copia de seguridad automatizada para SQL Server en Azure Virtual Machines (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Aplicación de revisiones automatizada de SQL** |Configura una ventana de mantenimiento durante la cual tienen lugar las actualizaciones de la máquina virtual, de tal forma que puede evitar las actualizaciones durante las horas punta para la carga de trabajo. Para más información, consulte [Aplicación de revisión automatizada para SQL Server en Azure Virtual Machines (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Integración del Almacén de claves de Azure** |Permite instalar y configurar automáticamente el Almacén de claves de Azure en la máquina virtual SQL Server. Para más información, consulte [Configuración de la integración de Azure Key Vault para SQL Server en Azure Virtual Machines (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md). |

## <a name="prerequisites"></a>Requisitos previos
Requisitos para usar la extensión del Agente de IaaS SQL Server en la máquina virtual:

**Sistema operativo**:

* Windows Server 2012
* Windows Server 2012 R2

**Versiones de SQL Server**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Descargar y configurar los comandos de Azure PowerShell más recientes](/powershell/azureps-cmdlets-docs)

## <a name="installation"></a>Instalación
La extensión del Agente de IaaS SQL Server se instala automáticamente cuando aprovisiona una de las imágenes de la galería de máquina virtual de SQL Server.

Si crea una máquina virtual solo para el sistema operativo Windows Server, puede instalar la extensión manualmente usando el cmdlet de PowerShell **Set-AzureVMSqlServerExtension** . Por ejemplo, el siguiente comando instala la extensión en una máquina virtual solo para el sistema operativo Windows Server y le asigna el nombre "SQLIaaSExtension".

    Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2" -Location "East US 2"

Si actualiza a la versión más reciente de la Extensión Agente de IaaS de SQL, debe reiniciar la máquina virtual después de actualizar la extensión.

> [!NOTE]
> Si instala la extensión Agente de IaaS de SQL Server manualmente en una máquina virtual de Windows Server, debe utilizar y administrar sus características con comandos de PowerShell. La interfaz del portal está disponible solo para las imágenes de la galería de SQL Server.
> 
> 

## <a name="status"></a>Status
Una manera de comprobar que la extensión está instalada consiste en ver el estado del agente en el Portal de Azure. Seleccione **Todas las configuraciones** en la hoja de la máquina virtual y haga clic en **Extensiones**. Debería aparecer la extensión **SQLIaaSExtension** .

![Extensión del Agente de IaaS SQL Server en el Portal de Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

También puede utilizar el cmdlet de Azure PowerShell **Get-AzureVMSqlServerExtension** .

    Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

El comando anterior confirma que el agente está instalado y proporciona información de estado general. También puede obtener información de estado específica sobre la copia de seguridad automatizada y la aplicación de revisiones con los siguientes comandos.

    $sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Eliminación
En el Portal de Azure, puede desinstalar la extensión haciendo clic en los puntos suspensivos de la hoja **Extensiones** de las propiedades de la máquina virtual. Después, haga clic en **Eliminar**.

![Desinstalación de la extensión del Agente de IaaS SQL Server en el Portal de Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

También puede utilizar el cmdlet de PowerShell **Remove-AzureRmVMSqlServerExtension** .

    Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## <a name="next-steps"></a>Pasos siguientes
Empiece utilizando uno de los servicios admitidos por la extensión. Para más información, consulte los temas a los que se hace referencia en la sección [Servicios admitidos](#supported-services) de este artículo.

Para obtener más información sobre cómo ejecutar SQL Server en Máquinas virtuales de Azure, consulte [Información general sobre SQL Server en Máquinas virtuales de Azure](virtual-machines-windows-sql-server-iaas-overview.md).


