---
title: "Automatizar tareas de administración en máquinas virtuales de SQL (implementación clásica) | Microsoft Docs"
description: "En este tema se describe cómo administrar la extensión Agente de SQL Server, que automatiza tareas de administración específicas de SQL Server. Entre ellas se incluyen la copia de seguridad automatizada, la aplicación de revisiones automatizada y la integración de Azure Key Vault. En este tema se usa el modelo de implementación clásica."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: a9bda2e7-cdba-427c-bc30-77cde4376f3a
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/05/2017
ms.author: jroth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 30fa9128cd51a7498449c991b58500ad9acdd3d4
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>Automatizar las tareas de administración en Azure Virtual Machines con la extensión del Agente SQL Server (implementación clásica)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [Clásico](../classic/sql-server-agent-extension.md)
> 
>
 
La extensión del agente de IaaS SQL Server (SQLIaaSAgent) se ejecuta en máquinas virtuales de Azure para automatizar las tareas de administración. En este tema se proporciona información general sobre los servicios admitidos por la extensión, así como instrucciones para la instalación, el estado y la eliminación.

> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../azure-resource-manager/resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos. Para ver la versión de Resource Manager de este artículo, consulte [SQL Server Agent Extension for SQL Server VMs Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md) (Extensión del Agente SQL Server para máquinas virtuales SQL Server (Resource Manager)).

## <a name="supported-services"></a>Servicios admitidos
La extensión del Agente de IaaS SQL Server es compatible con las siguientes tareas de administración:

| Característica de administración | Descripción |
| --- | --- |
| **Automated Backup de SQL** |Automatiza la programación de copias de seguridad de todas las bases de datos para la instancia predeterminada de SQL Server en la máquina virtual. Para obtener más información, consulte [Copia de seguridad automatizada para SQL Server en Azure Virtual Machines (implementación clásica)](../classic/sql-automated-backup.md). |
| **Aplicación de revisiones automatizada de SQL** |Configura una ventana de mantenimiento durante la cual tienen lugar las actualizaciones de la máquina virtual, de tal forma que puede evitar las actualizaciones durante las horas punta para la carga de trabajo. Para obtener más información, consulte [Automated Patching para SQL Server en Azure Virtual Machines (implementación clásica)](../classic/sql-automated-patching.md). |
| **Integración de Azure Key Vault** |Permite instalar y configurar automáticamente Azure Key Vault en la máquina virtual SQL Server. Para obtener más información, consulte [Configuración de la integración de Azure Key Vault para SQL Server en máquinas virtuales de Azure (implementación clásica)](../classic/ps-sql-keyvault.md). |

## <a name="prerequisites"></a>Requisitos previos
Requisitos para usar la extensión del Agente de IaaS SQL Server en la máquina virtual:

### <a name="operating-system"></a>Sistema operativo:
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>Versiones de SQL Server:
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:
[Descargue y configure los comandos de Azure PowerShell más recientes](/powershell/azure/overview).

Inicie Windows PowerShell y conéctelo con su suscripción de Azure mediante el comando **Add-AzureAccount** .

    Add-AzureAccount

Si tiene varias suscripciones, use **Select-AzureSubscription** para seleccionar la suscripción que contenga la máquina virtual clásica de destino.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

Aquí, puede obtener una lista de las máquinas virtuales clásicas y sus nombres de servicio asociados con el comando **Get-AzureVM** .

    Get-AzureVM

## <a name="installation"></a>Instalación
Para las máquinas virtuales clásicas, debe usar PowerShell para instalar la extensión Agente de IaaS de SQL Server y configurar sus servicios asociados. Use el cmdlet **Set-AzureVMSqlServerExtension** de PowerShell para instalar la extensión. Por ejemplo, el siguiente comando instala la extensión en una máquina virtual Windows Server (clásica) y le asigna el nombre "SQLIaaSExtension".

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Si actualiza a la versión más reciente de la Extensión Agente de IaaS de SQL, debe reiniciar la máquina virtual después de actualizar la extensión.

> [!NOTE]
> Las máquinas virtuales clásicas carecen de una opción para instalar y configurar la extensión Agente de IaaS de SQL mediante el portal.
> 
> 

## <a name="status"></a>Estado
Una manera de comprobar que la extensión está instalada consiste en ver el estado del agente en Azure Portal. Seleccione una máquina virtual en la hoja de la máquina virtual y haga clic en **Extensiones**. Debería aparecer la extensión **SQLIaaSAgent** .

![Extensión del Agente de IaaS SQL Server en Azure Portal](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

También puede usar el cmdlet **Get-AzureVMSqlServerExtension** de Azure PowerShell.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Eliminación
En Azure Portal, puede desinstalar la extensión haciendo clic en los puntos suspensivos de la hoja **Extensiones** de las propiedades de la máquina virtual. Hacer clic en **Desinstalar**.

![Desinstalación de la extensión del Agente de IaaS SQL Server en Azure Portal](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

También puede utilizar el cmdlet **Remove-AzureVMSqlServerExtension** de PowerShell.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Pasos siguientes
Empiece utilizando uno de los servicios admitidos por la extensión. Para más información, consulte los temas a los que se hace referencia en la sección [Servicios admitidos](#supported-services) de este artículo.

Para obtener más información sobre cómo ejecutar SQL Server en Azure Virtual Machines, consulte [Información general sobre SQL Server en Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

