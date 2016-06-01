<properties
	pageTitle="Extensión del Agente SQL Server para las máquinas virtuales SQL Server (Resource Manager) | Microsoft Azure"
	description="En este tema se describe cómo administrar la extensión del Agente SQL Server, que automatiza tareas de administración específicas de SQL Server. Entre ellas se incluyen la copia de seguridad automatizada, la aplicación de revisiones automatizada y la integración del Almacén de claves de Azure. En este tema se usa el modelo de implementación de Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="05/16/2016"
	ms.author="jroth"/>

# Extensión del Agente SQL Server para las máquinas virtuales SQL Server (Resource Manager)

> [AZURE.SELECTOR]
- [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
- [Clásico](virtual-machines-windows-classic-sql-server-agent-extension.md)

La extensión del agente de IaaS SQL Server (SQLIaaSExtension) se ejecuta en máquinas virtuales de Azure para automatizar las tareas de administración. En este tema se proporciona información general sobre los servicios admitidos por la extensión, así como instrucciones para la instalación, el estado y la eliminación.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]
modelo de implementación clásica. Para ver la versión clásica de este artículo, consulte [Extensión del Agente SQL Server para las máquinas virtuales SQL Server (clásico)](virtual-machines-windows-classic-sql-server-agent-extension.md).

## Servicios admitidos

La extensión del Agente de IaaS SQL Server es compatible con las siguientes tareas de administración:

| Característica de administración | Descripción |
|---------------------|-------------------------------|
| **Copia de seguridad automatizada de SQL** | Automatiza la programación de copias de seguridad de todas las bases de datos para la instancia predeterminada de SQL Server en la máquina virtual.|
| **Aplicación de revisiones automatizada de SQL** | Configura una ventana de mantenimiento durante la cual tienen lugar las actualizaciones de la máquina virtual, de tal forma que puede evitar las actualizaciones durante las horas punta para la carga de trabajo.|
| **Integración del Almacén de claves de Azure** | Permite instalar y configurar automáticamente el Almacén de claves de Azure en la máquina virtual SQL Server. Para obtener más información, consulte [Configuración de la integración de Almacén de claves de Azure para SQL Server en máquinas virtuales de Azure (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md).|

## Requisitos previos

Requisitos para usar la extensión del Agente de IaaS SQL Server en la máquina virtual:

- Windows Server 2012, Windows Server 2012 R2 o posterior
- SQL Server 2012, SQL Server 2014 o posterior

Requisitos para usar los cmdlets de Powershell:

- La versión más reciente de Azure PowerShell está [disponible aquí](../powershell-install-configure.md).

## Instalación

La extensión del Agente de IaaS SQL Server se instala automáticamente cuando aprovisiona una de las imágenes de la galería de máquina virtual de SQL Server.

Si crea una máquina virtual solo para el sistema operativo Windows Server, puede instalar la extensión manualmente usando el cmdlet de PowerShell **Set-AzureVMSqlServerExtension**. Por ejemplo, el siguiente comando instala la extensión en una máquina virtual solo para el sistema operativo Windows Server y le asigna el nombre "SQLIaaSExtension".

	Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2"

## Estado

Una manera de comprobar que la extensión está instalada consiste en ver el estado del agente en el Portal de Azure. Seleccione **Todas las opciones** en la hoja de la máquina virtual y, después, haga clic en **Extensiones**. Debería aparecer la extensión **SQLIaaSExtension**.

![Extensión del Agente de IaaS SQL Server en el Portal de Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

También puede utilizar el cmdlet de Azure PowerShell **Get-AzureVMSqlServerExtension**.

	Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

El comando anterior confirma que el agente está instalado y proporciona información de estado general. También puede obtener información de estado específica sobre la copia de seguridad automatizada y la aplicación de revisiones con los siguientes comandos.

	$sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
	$sqlext.AutoPatchingSettings
	$sqlext.AutoBackupSettings

## Eliminación   

En el Portal de Azure, puede desinstalar la extensión haciendo clic en los puntos suspensivos de la hoja **Extensiones** de las propiedades de la máquina virtual. Después, haga clic en **Eliminar**.

![Desinstalación de la extensión del Agente de IaaS SQL Server en el Portal de Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

También puede utilizar el cmdlet de PowerShell **Remove-AzureRmVMSqlServerExtension**.

	Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## Pasos siguientes

Empiece utilizando uno de los servicios admitidos por la extensión. Para obtener más información, consulte los temas a los que se hace referencia en la sección [Servicios admitidos](#supported-services) de este artículo.

Para obtener más información sobre cómo ejecutar SQL Server en Máquinas virtuales de Azure, consulte [Información general sobre SQL Server en Máquinas virtuales de Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!----HONumber=AcomDC_0518_2016-->