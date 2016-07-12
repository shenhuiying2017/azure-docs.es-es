<properties
	pageTitle="Extensión del Agente SQL Server para las máquinas virtuales SQL Server (clásico) | Microsoft Azure"
	description="En este tema se describe cómo administrar la extensión del Agente SQL Server, que automatiza tareas de administración específicas de SQL Server. Entre ellas se incluyen la copia de seguridad automatizada, la aplicación de revisiones automatizada y la integración del Almacén de claves de Azure. En este tema se usa el modelo de implementación clásica."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="05/16/2016"
	ms.author="jroth"/>

# Extensión del Agente SQL Server para las máquinas virtuales SQL Server (clásico)

> [AZURE.SELECTOR]
- [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
- [Clásico](virtual-machines-windows-classic-sql-server-agent-extension.md)

La extensión del agente de IaaS SQL Server (SQLIaaSAgent) se ejecuta en máquinas virtuales de Azure para automatizar las tareas de administración. En este tema se proporciona información general sobre los servicios admitidos por la extensión, así como instrucciones para la instalación, el estado y la eliminación.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 

Para ver la versión de Resource Manager de este artículo, consulte [Extensión del Agente SQL Server para máquinas virtuales SQL Server (Resource Manager)](virtual-machines-windows-sql-server-agent-extension.md).

## Servicios admitidos

La extensión del Agente de IaaS SQL Server es compatible con las siguientes tareas de administración:

| Característica de administración | Descripción |
|---------------------|-------------------------------|
| **Copia de seguridad automatizada de SQL** | Automatiza la programación de copias de seguridad de todas las bases de datos para la instancia predeterminada de SQL Server en la máquina virtual. Para obtener más información, consulte [Copia de seguridad automatizada para SQL Server en máquinas virtuales de Azure (implementación clásica)](virtual-machines-windows-classic-sql-automated-backup.md).|
| **Aplicación de revisiones automatizada de SQL** | Configura una ventana de mantenimiento durante la cual tienen lugar las actualizaciones de la máquina virtual, de tal forma que puede evitar las actualizaciones durante las horas punta para la carga de trabajo. Para obtener más información, consulte [Aplicación de revisiones automatizadas para SQL Server en máquinas virtuales de Azure (implementación clásica)](virtual-machines-windows-classic-sql-automated-patching.md).|
| **Integración del Almacén de claves de Azure** | Permite instalar y configurar automáticamente el Almacén de claves de Azure en la máquina virtual SQL Server. Para obtener más información, consulte [Configuración de la integración de Almacén de claves de Azure para SQL Server en máquinas virtuales de Azure (implementación clásica)](virtual-machines-windows-classic-ps-sql-keyvault.md).|

## Requisitos previos

Requisitos para usar la extensión del Agente de IaaS SQL Server en la máquina virtual:

**Sistema operativo**:

- Windows Server 2012
- Windows Server 2012 R2

**Versiones de SQL Server**:

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**Azure PowerShell**:

- [Descargar y configurar los comandos de Azure PowerShell más recientes](../powershell-install-configure.md)

**Agente invitado de la máquina Virtual**:

- La extensión BGInfo se instala automáticamente en nuevas máquinas virtuales de Azure.

## Instalación

La extensión del Agente de IaaS SQL Server se instala automáticamente cuando aprovisiona una de las imágenes de la galería de máquina virtual de SQL Server.

Si crea una máquina virtual solo para el sistema operativo Windows Server, puede instalar la extensión manualmente usando el cmdlet de PowerShell **Set-AzureVMSqlServerExtension**. Use el comando para configurar uno de los servicios del agente, como la aplicación de revisiones automatizada. La máquina virtual instala el agente si no está instalado. Para obtener instrucciones sobre el uso del cmdlet de PowerShell **Set-AzureVMSqlServerExtension**, consulte los temas individuales de la sección [Supported services](#supported-services) (Servicios admitidos) de este artículo.

Si actualiza a la versión más reciente de la Extensión Agente de IaaS de SQL, debe reiniciar la máquina virtual después de actualizar la extensión.

>[AZURE.NOTE] Si instala la Extensión Agente de IaaS de SQL Server manualmente en una máquina virtual, debe utilizar y administrar sus características con comandos de PowerShell. La interfaz del portal no está disponible en esta situación.

## Estado

Una manera de comprobar que la extensión está instalada consiste en ver el estado del agente en el Portal de Azure. Seleccione **Todas las configuraciones** en la hoja de la máquina virtual y, después, haga clic en **Extensiones**. Debería aparecer la extensión **SQLIaaSAgent**.

![Extensión del Agente de IaaS SQL Server en el Portal de Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

También puede utilizar el cmdlet de Azure PowerShell **Get-AzureVMSqlServerExtension**.

	Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## Eliminación   

En el Portal de Azure, puede desinstalar la extensión haciendo clic en los puntos suspensivos de la hoja **Extensiones** de las propiedades de la máquina virtual. Después, haga clic en **Eliminar**.

![Desinstalación de la extensión del Agente de IaaS SQL Server en el Portal de Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

También puede utilizar el cmdlet de PowerShell **Remove-AzureVMSqlServerExtension**.

	Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## Pasos siguientes

Empiece utilizando uno de los servicios admitidos por la extensión. Para más información, consulte los temas a los que se hace referencia en la sección [Supported services](#supported-services) (Servicios admitidos) de este artículo.

Para obtener más información sobre cómo ejecutar SQL Server en Máquinas virtuales de Azure, consulte [Información general sobre SQL Server en Máquinas virtuales de Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!----HONumber=AcomDC_0629_2016-->