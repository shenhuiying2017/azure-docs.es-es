<properties
    pageTitle="Extensión del Agente SQL Server para las máquinas virtuales SQL Server (clásico) | Microsoft Azure"
    description="En este tema se describe cómo administrar la extensión Agente de SQL Server, que automatiza tareas de administración específicas de SQL Server. Entre ellas se incluyen la copia de seguridad automatizada, la aplicación de revisiones automatizada y la integración del Almacén de claves de Azure. En este tema se usa el modelo de implementación clásica."
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
    ms.date="09/26/2016"
    ms.author="jroth"/>


# <a name="sql-server-agent-extension-for-sql-server-vms-(classic)"></a>Extensión del Agente SQL Server para las máquinas virtuales SQL Server (clásico)

> [AZURE.SELECTOR]
- [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
- [Clásico](virtual-machines-windows-classic-sql-server-agent-extension.md)

La extensión del agente de IaaS SQL Server (SQLIaaSAgent) se ejecuta en máquinas virtuales de Azure para automatizar las tareas de administración. En este tema se proporciona información general sobre los servicios admitidos por la extensión, así como instrucciones para la instalación, el estado y la eliminación.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] Para ver la versión de Resource Manager de este artículo, consulte [Extensión del Agente SQL Server para máquinas virtuales SQL Server (Resource Manager)](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Servicios admitidos

La extensión del Agente de IaaS SQL Server es compatible con las siguientes tareas de administración:

| Característica de administración | Descripción |
|---------------------|-------------------------------|
| **Copia de seguridad automatizada de SQL** | Automatiza la programación de copias de seguridad de todas las bases de datos para la instancia predeterminada de SQL Server en la máquina virtual. Para obtener más información, consulte [Copia de seguridad automatizada para SQL Server en máquinas virtuales de Azure (implementación clásica)](virtual-machines-windows-classic-sql-automated-backup.md).|
| **Aplicación de revisiones automatizada de SQL** | Configura una ventana de mantenimiento durante la cual tienen lugar las actualizaciones de la máquina virtual, de tal forma que puede evitar las actualizaciones durante las horas punta para la carga de trabajo. Para obtener más información, consulte [Aplicación de revisiones automatizadas para SQL Server en máquinas virtuales de Azure (implementación clásica)](virtual-machines-windows-classic-sql-automated-patching.md).|
| **Integración del Almacén de claves de Azure** | Permite instalar y configurar automáticamente el Almacén de claves de Azure en la máquina virtual SQL Server. Para obtener más información, consulte [Configuración de la integración de Almacén de claves de Azure para SQL Server en máquinas virtuales de Azure (implementación clásica)](virtual-machines-windows-classic-ps-sql-keyvault.md).|

## <a name="prerequisites"></a>Requisitos previos

Requisitos para usar la extensión del Agente de IaaS SQL Server en la máquina virtual:

### <a name="operating-system:"></a>Sistema operativo:

- Windows Server 2012
- Windows Server 2012 R2

### <a name="sql-server-versions:"></a>Versiones de SQL Server:

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

### <a name="azure-powershell:"></a>Azure PowerShell:

[Descargue y configure los comandos de Azure PowerShell más recientes](../powershell-install-configure.md).

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

>[AZURE.NOTE] Las máquinas virtuales clásicas carecen de una opción para instalar y configurar la extensión Agente de IaaS de SQL mediante el portal.

## <a name="status"></a>Estado

Una manera de comprobar que la extensión está instalada consiste en ver el estado del agente en el Portal de Azure. Seleccione **Todas las configuraciones** en la hoja de la máquina virtual y haga clic en **Extensiones**. Debería aparecer la extensión **SQLIaaSAgent** .

![Extensión del Agente de IaaS SQL Server en el Portal de Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

También puede usar el cmdlet **Get-AzureVMSqlServerExtension** de Azure PowerShell.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Eliminación   

En el Portal de Azure, puede desinstalar la extensión haciendo clic en los puntos suspensivos de la hoja **Extensiones** de las propiedades de la máquina virtual. Después, haga clic en **Eliminar**.

![Desinstalación de la extensión del Agente de IaaS SQL Server en el Portal de Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

También puede utilizar el cmdlet **Remove-AzureVMSqlServerExtension** de PowerShell.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Pasos siguientes

Empiece utilizando uno de los servicios admitidos por la extensión. Para más información, consulte los temas a los que se hace referencia en la sección [Servicios admitidos](#supported-services) de este artículo.

Para obtener más información sobre cómo ejecutar SQL Server en Máquinas virtuales de Azure, consulte [Información general sobre SQL Server en Máquinas virtuales de Azure](virtual-machines-windows-sql-server-iaas-overview.md).



<!--HONumber=Oct16_HO2-->


