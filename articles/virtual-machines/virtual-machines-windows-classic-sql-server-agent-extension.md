<properties
	pageTitle="Extensión Agente de IaaS de SQL Server (implementación clásica) | Microsoft Azure"
	description="En este tema se describe la extensión del agente SQL Server, que permite que una máquina virtual con SQL Server en Azure use las características de automatización. Usa el modelo de implementación clásica."
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
	ms.date="04/08/2016"
	ms.author="jroth"/>

# Extensión Agente de IaaS de SQL Server (implementación clásica)

Esta extensión permite que SQL Server en máquinas virtuales de Azure utilice determinados servicios, descritos en este artículo, que solo se puede usar con esta extensión instalada. Esta extensión se instala automáticamente para las imágenes de la galería de SQL Server en el Portal de Azure. Se puede instalar en cualquier máquina virtual de SQL Server en Azure que tenga instalado el agente invitado de máquina virtual de Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


## Requisitos previos
Requisitos para usar los cmdlets de Powershell:

- Versión más reciente de Azure PowerShell [disponible aquí](../powershell-install-configure.md)

Requisitos para usar la extensión en la máquina virtual:

- Agente invitado de máquina virtual de Azure
- Windows Server 2012, Windows Server 2012 R2 o posterior
- SQL Server 2012, SQL Server 2014 o posterior

## Servicios disponibles con la extensión

- **Copias de seguridad automatizadas de SQL**: este servicio automatiza la programación de copias de seguridad de todas las bases de datos para la instancia predeterminada de SQL Server en la máquina virtual. Para obtener más información, vea [Copia de seguridad automatizada para SQL Server en máquinas virtuales de Azure (implementación clásica)](virtual-machines-windows-classic-sql-automated-backup.md).
- **Aplicación de revisión automatizada de SQL**: este servicio le permite configurar una ventana de mantenimiento durante la cual tienen lugar las actualizaciones de la máquina virtual, de tal forma que puede evitar las actualizaciones durante las horas pico para la carga de trabajo. Para obtener más información, consulte [Aplicación de revisiones automatizadas para SQL Server en máquinas virtuales de Azure (implementación clásica)](virtual-machines-windows-classic-sql-automated-patching.md).
- **Integración del Almacén de claves de Azure**: este servicio permite instalar y configurar el Almacén de claves de Azure en la máquina virtual de SQL Server automáticamente. Para obtener más información, consulte [Configuración de la integración de Almacén de claves de Azure para SQL Server en máquinas virtuales de Azure (implementación clásica)](virtual-machines-windows-classic-ps-sql-keyvault.md).

## Adición de la extensión con Powershell
Si aprovisiona la máquina virtual de SQL Server mediante el [Portal de Azure](virtual-machines-windows-portal-sql-server-provision.md), la extensión se instalará automáticamente. Para las máquinas virtuales de SQL Server aprovisionadas mediante el Portal de Azure clásico, o para las máquinas virtuales a las que usted proporciona su propia licencia SQL, puede agregar esta extensión a una máquina virtual existente mediante el cmdlet **Set-AzureVMSqlServerExtension** de Azure PowerShell.

### Sintaxis

Set-AzureVMSqlServerExtension [[-ReferenceName] [String]] [-VM] IPersistentVM [[-Version] [String]] [[-AutoPatchingSettings] [AutoPatchingSettings]] [-AutoBackupSettings[AutoBackupSettings]] [-Profile [AzureProfile]] [CommonParameters]

> [AZURE.NOTE] Se recomienda la omisión del parámetro -Version. Sin él, el valor predeterminado es la versión más reciente de la extensión.

### Ejemplo
El ejemplo siguiente se configura la configuración de copia de seguridad automática mediante una configuración definida en $abs (que no se muestra aquí). serviceName es el nombre del servicio en la nube que hospeda la máquina virtual. Para obtener más información, vea [Copia de seguridad automatizada para SQL Server en máquinas virtuales de Azure (implementación clásica)](virtual-machines-windows-classic-sql-automated-backup.md).

	Get-AzureVM –ServiceName "serviceName" –Name "vmName" | Set-AzureVMSqlServerExtension –AutoBackupSettings $abs | Update-AzureVM**

## Comprobación del estado de la extensión
Si desea comprobar el estado de esta extensión y los servicios asociados a ella, puede utilizar cualquier portal. En los detalles de la máquina virtual existente, busque **Extensiones** en **Configuración**.

También puede utilizar el cmdlet de Azure PowerShell **Get-AzureVMSqlServerExtension**.

### Sintaxis

Get-AzureVMSqlServerExtension [[-VM] [IPersistentVM]] [-Profile [AzureProfile]] [CommonParameters]

### Ejemplo
	Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## Eliminación de la extensión con Powershell   
Si desea quitar esta extensión de la máquina virtual, puede utilizar el cmdlet de Azure PowerShell **Remove-AzureVMSqlServerExtension**.

### Sintaxis

Remove-AzureVMSqlServerExtension [-Profile [AzureProfile]] -VM IPersistentVM [CommonParameters]

<!---HONumber=AcomDC_0413_2016-->