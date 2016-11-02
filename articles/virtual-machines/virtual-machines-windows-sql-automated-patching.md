<properties
	pageTitle="Aplicación de revisión automatizada para máquinas virtuales SQL Server (Resource Manager) | Microsoft Azure"
	description="Explica la característica Aplicación de revisión automatizada para máquinas virtuales de SQL Server que se ejecutan en Azure mediante Resource Manager."
	services="virtual-machines-windows"
	documentationCenter="na"
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
	ms.date="08/19/2016"
	ms.author="jroth" />

# Aplicación de revisión automatizada para SQL Server en máquinas virtuales de Azure (Resource Manager)

> [AZURE.SELECTOR]
- [Resource Manager](virtual-machines-windows-sql-automated-patching.md)
- [Clásico](virtual-machines-windows-classic-sql-automated-patching.md)

Aplicación de revisión automatizada establece una ventana de mantenimiento para una máquina virtual de Azure con SQL Server. Actualizaciones automatizadas solo puede instalarse durante esta ventana de mantenimiento. Para SQL Server, esta restricción garantiza que se actualice el sistema y que cualquier reinicio asociado se produzca en el mejor momento posible para la base de datos. Aplicación de revisión automatizada se basa en la [Extensión Agente de IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] modelo de implementación clásica. Para ver la versión clásica de este artículo, consulte [Aplicación de revisiones automatizadas para SQL Server en máquinas virtuales de Azure implementación clásica](virtual-machines-windows-classic-sql-automated-patching.md).

## Requisitos previos

Para utilizar Aplicación de revisión automatizada, tenga en cuenta los siguientes requisitos previos:

**Sistema operativo**:

- Windows Server 2012
- Windows Server 2012 R2

**Versión de SQL Server**:

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**Azure PowerShell**:

- [Instale los comandos de Azure PowerShell más recientes](../powershell-install-configure.md) si planea configurar Aplicación de revisión automatizada con PowerShell.

>[AZURE.NOTE] Aplicación de revisión automatizada se basa en la Extensión Agente de IaaS de SQL Server. Las imágenes actuales de la galería de máquinas virtuales de SQL agregan esta extensión de manera predeterminada. Para más información, consulte [Extensión Agente de IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## Settings

En la siguiente tabla se describen las opciones que pueden configurarse para Aplicación de revisión automatizada. Los pasos de configuración reales varían si usa el Portal de Azure o comandos de Windows PowerShell de Azure.

|Configuración|Valores posibles|Descripción|
|---|---|---|
|**Aplicación de revisiones automatizada**|Habilitar/deshabilitar (deshabilitado)|Habilita o deshabilita Aplicación de revisión automatizada para una máquina virtual de Azure.|
|**Programación de mantenimiento**|Cada día, el lunes, el martes, el miércoles, el jueves, el viernes, el sábado, el domingo|La programación para descargar e instalar actualizaciones de Windows, SQL Server y Microsoft para la máquina virtual.|
|**Hora de inicio de mantenimiento**|0-24|La hora de inicio local para actualizar la máquina virtual.|
|**Duración de la ventana de mantenimiento**|30-180|El número de minutos permitido para completar la descarga y la instalación de actualizaciones.|
|**Categoría de la revisión**|Importante|La categoría de actualizaciones para descargar e instalar.|

## Configuración en el Portal
Puede usar el Portal de Azure para configurar Aplicación de revisión automatizada durante el aprovisionamiento o para las máquinas virtuales existentes.

### Nuevas máquinas virtuales
Use el Portal de Azure para configurar la opción Aplicación de revisión automatizada cuando cree una nueva máquina virtual de SQL Server en el modelo de implementación de Resource Manager.

En la hoja **Configuración de SQL Server**, seleccione **Aplicación de revisión automatizada**. La siguiente captura de pantalla del Portal de Azure muestra la hoja **Aplicación de revisión automatizada de SQL**.

![Aplicación de revisión automatizada de SQL en el Portal de Azure](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Para conocer el contexto, consulte el tema completo en [Aprovisionamiento de una máquina virtual de SQL Server en el Portal de Azure](virtual-machines-windows-portal-sql-server-provision.md).

### Máquinas virtuales existentes
Para las máquinas virtuales de SQL Server existentes, seleccione su máquina virtual de SQL Server. Después, seleccione la sección **Configuración de SQL Server** de la hoja **Configuración**.

![Aplicación de revisión automatizada de SQL para máquinas virtuales existentes](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)

En la hoja **Configuración de SQL Server**, haga clic en el botón **Editar** en la sección Aplicación de revisión automatizada.

![Configuración de Aplicación de revisión automatizada de SQL para máquinas virtuales existentes](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-configuration.png)

Cuando termine, haga clic en el botón **Aceptar** situado en la parte inferior de la hoja **Configuración de SQL Server** para guardar los cambios.

Si habilita Aplicación de revisión automatizada por primera vez, Azure configura el agente de IaaS de SQL Server en segundo plano. Durante este tiempo, es posible que el Portal de Azure no muestre que se ha configurado Aplicación de revisión automatizada. Espere unos minutos hasta que el agente se instale y configure. Después, el Portal de Azure muestra la nueva configuración.

>[AZURE.NOTE] También puede usar una plantilla para configurar Aplicación de revisión automatizada. Para más información, consulte [la plantilla de inicio rápido de Azure para Aplicación de revisión automatizada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autopatching-update).

## Configuración con PowerShell

Después de aprovisionar la máquina virtual de SQL, use PowerShell para configurar Aplicación de revisión automatizada.

En el ejemplo siguiente, se usa PowerShell para configurar Aplicación de revisión automatizada en una máquina virtual de SQL Server existente. El comando **AzureRM.Compute\\New-AzureVMSqlServerAutoPatchingConfig** configura una nueva ventana de mantenimiento para actualizaciones automáticas.

	$vmname = "vmname"
	$resourcegroupname = "resourcegroupname"
	$aps = AzureRM.Compute\New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Set-AzureRmVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

Según este ejemplo, la siguiente tabla describe el efecto práctico en la máquina virtual de Azure de destino:

|Parámetro|Efecto|
|---|---|
|**DayOfWeek**|Las revisiones instaladas cada jueves.|
|**MaintenanceWindowStartingHour**|Inicia las actualizaciones a las 11:00 a.m.|
|**MaintenanceWindowsDuration**|Las revisiones deben instalarse en un plazo de 120 minutos. Según la hora de inicio, deben haberse completado a las 1:00 p.m.|
|**PatchCategory**|La única configuración posible para este parámetro es **Importante**.|

La instalación y configuración del agente de Iaas de SQL Server puede tardar algunos minutos.

Para deshabilitar Aplicación de revisión automatizada, ejecute el mismo script sin el parámetro **-Enable** en **AzureRM.Compute\\New-AzureVMSqlServerAutoPatchingConfig**. La ausencia del parámetro **-Enable** indica al comando que deshabilite la característica.

## Pasos siguientes

Para más información acerca de otras tareas de automatización disponibles, consulte la [extensión Agente de IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Para más información sobre cómo ejecutar SQL Server en máquinas virtuales de Azure, consulte [Introducción a SQL Server en máquinas virtuales de Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=AcomDC_0824_2016-->