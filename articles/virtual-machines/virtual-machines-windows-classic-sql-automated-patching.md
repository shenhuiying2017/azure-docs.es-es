<properties
	pageTitle="Aplicación de revisiones automatizada para máquinas virtuales SQL Server (implementación clásica) | Microsoft Azure"
	description="Explica la característica Aplicación de revisión automatizada para máquinas virtuales SQL Server que se ejecutan en Azure mediante el modelo de implementación clásica."
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="07/15/2016"
	ms.author="jroth" />

# Aplicación de revisiones automatizadas para SQL Server en máquinas virtuales de Azure (implementación clásica)

> [AZURE.SELECTOR]
- [Resource Manager](virtual-machines-windows-sql-automated-patching.md)
- [Clásico](virtual-machines-windows-classic-sql-automated-patching.md)

Aplicación de revisión automatizada establece una ventana de mantenimiento para una máquina virtual de Azure con SQL Server. Actualizaciones automatizadas solo puede instalarse durante esta ventana de mantenimiento. Para SQL Server, esto garantiza que se actualiza el sistema y que cualquier reinicio asociado se producto en el mejor momento posible para la base de datos. Aplicación de revisión automatizada se basa en la [Extensión Agente de IaaS de SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 
Para ver la versión de Resource Manager de este artículo, consulte [Automated Patching for SQL Server in Azure Virtual Machines (Resource Manager)](virtual-machines-windows-sql-automated-patching.md) (Aplicación de revisión automatizada para SQL Server en máquinas virtuales de Azure [Resource Manager]).

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

>[AZURE.NOTE] Aplicación de revisión automatizada se basa en la Extensión Agente de IaaS de SQL Server. Las imágenes actuales de la galería de máquinas virtuales de SQL agregan esta extensión de manera predeterminada. Para más información, consulte [Extensión Agente de IaaS de SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

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
Use el Portal de Azure para configurar Aplicación de revisión automatizada cuando cree una nueva máquina virtual de SQL Server en el modelo de implementación clásica.

La siguiente captura del Portal de Azure muestra estas opciones en **CONFIGURACIÓN OPCIONAL** | **APLICACIÓN DE REVISIÓN AUTOMATIZADA DE SQL**.

![Aplicación de revisión automática de SQL en el Portal de Azure](./media/virtual-machines-windows-classic-sql-automated-patching/IC778484.jpg)

### Máquinas virtuales existentes
Para las máquinas virtuales de SQL Server 2012 o 2014, seleccione la configuración **Aplicación de revisión automatizada** en la sección **Configuración** de las propiedades de la máquina virtual. En la ventana **Aplicación de revisión automatizada**, puede habilitar la característica, establecer la programación de mantenimiento y la hora de inicio y elegir la duración de la ventana de mantenimiento. Esto se muestra en la siguiente captura de pantalla.

![Configuración de Aplicación de revisión automatizada en el Portal de Azure](./media/virtual-machines-windows-classic-sql-automated-patching/IC792132.jpg)

>[AZURE.NOTE] Cuando habilita Aplicación de revisión automatizada por primera vez, Azure configura el agente de Iaas de SQL Server en segundo plano. Durante este tiempo, es posible que el Portal de Azure no muestre que se ha configurado Aplicación de revisión automatizada. Espere unos minutos hasta que el agente se instale y configure. Después, el Portal de Azure mostrará la nueva configuración.

## Configuración con PowerShell

También puede usar PowerShell para configurar Aplicación de revisión automatizada.

En el ejemplo siguiente, se usa PowerShell para configurar Aplicación de revisión automatizada en una máquina virtual de SQL Server existente. El comando **New-AzureVMSqlServerAutoPatchingConfig** configura una nueva ventana de mantenimiento para actualizaciones automáticas.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Según este ejemplo, la siguiente tabla describe el efecto práctico en la máquina virtual de Azure de destino:

|Parámetro|Efecto|
|---|---|
|**DayOfWeek**|Las revisiones instaladas cada jueves.|
|**MaintenanceWindowStartingHour**|Inicia las actualizaciones a las 11:00 a.m.|
|**MaintenanceWindowsDuration**|Las revisiones deben instalarse en un plazo de 120 minutos. Según la hora de inicio, deben haberse completado a las 1:00 p.m.|
|**PatchCategory**|La única configuración posible para este parámetro es “Importante”.|

La instalación y configuración del agente de Iaas de SQL Server puede tardar algunos minutos.

Para deshabilitar Aplicación de revisión automatizada, ejecute el script sin el parámetro -Enable en New-AzureVMSqlServerAutoPatchingConfig. Al igual que la instalación, la deshabilitación de Aplicación de revisión automatizada puede tardar algunos minutos.

## Pasos siguientes

Para más información acerca de otras tareas de automatización disponibles, consulte la [extensión Agente de IaaS de SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

Para más información sobre cómo ejecutar SQL Server en Máquinas virtuales de Azure, consulte [Información general sobre SQL Server en máquinas virtuales de Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=AcomDC_0720_2016-->