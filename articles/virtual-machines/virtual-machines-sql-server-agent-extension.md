<properties 
	pageTitle="Extensión Agente de IaaS de SQL Server" 
	description="Describe la extensión del Agente SQL Server, que permite a las máquinas virtuales que ejecutan SQL Server en la nube en Azure usar las características de automatización y cómo instalar al agente si no se instala automáticamente." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="jeffgoll" 
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="06/17/2015"
	ms.author="jeffreyg"/>

# Extensión Agente de IaaS de SQL Server

Esta extensión permite que SQL Server en máquinas virtuales de Azure utilice determinados servicios, descritos en este artículo, que solo se puede usar con esta extensión instalada. Esta extensión se instala automáticamente para las imágenes de la galería de SQL Server en el Portal de vista previa de Azure. Se puede instalar en cualquier máquina virtual de SQL Server en Azure que tenga instalado el agente invitado de máquina virtual de Azure.
 
## Requisitos previos
Requisitos para usar los cmdlets de Powershell:

- El SDK de línea de comandos de Azure más reciente está [disponible aquí](http://azure.microsoft.com/downloads/)

Requisitos para usar la extensión en la máquina virtual:

- Agente invitado de máquina virtual de Azure
- Windows Server 2012, Windows Server 2012 R2 o posterior
- SQL Server 2012, SQL Server 2014 o posterior
 
## Servicios disponibles con la extensión

- **Copias de seguridad automatizadas de SQL**: este servicio automatiza la programación de copias de seguridad de todas las bases de datos para la instancia predeterminada de SQL Server en la máquina virtual. Para obtener más información acerca de este servicio, consulte [Copia de seguridad automatizada para SQL Server en máquinas virtuales de Azure](https://msdn.microsoft.com/library/azure/dn906091.aspx).
- **Aplicación de revisión automatizada de SQL**: este servicio le permite configurar una ventana de mantenimiento durante la cual tienen lugar las actualizaciones de la máquina virtual, de tal forma que puede evitar las actualizaciones durante las horas pico para la carga de trabajo. Para obtener más información acerca de este servicio, consulte [Aplicación de revisión automatizada para SQL Server en máquinas virtuales de Azure](https://msdn.microsoft.com/library/azure/dn961166.aspx).

## Adición de la extensión con Powershell
Si aprovisiona la máquina virtual de SQL Server mediante el [Portal de vista previa de Azure](https://portal.azure.com/), la extensión se instalará automáticamente. Para las máquinas virtuales de SQL Server aprovisionadas con el [Portal de administración de Azure](https://manage.windowsazure.com), o para máquinas virtuales a las que aporta su propia licencia SQL, puede agregar esta extensión a una máquina virtual existente mediante el siguiente cmdlet de PowerShell de Azure.

**Set-AzureVMSqlServerExtension**

### Sintaxis

Set-AzureVMSqlServerExtension [-VM] <IPersistentVM> [[-Version] <string>] [-AutoBackupSettings <AutoBackupSettings>] [-AutoPatchingSetttings <AutoPatchingSetttings>] [-Confirm] [-WhatIf] [<CommonParameters>]

> [AZURE.NOTE]Se recomienda la omisión del parámetro -Version. Sin él, el valor predeterminado es la versión más reciente de la extensión.

### Ejemplo
	Get-AzureVM –ServiceName serviceName –Name vmName | Set-AzureVMSqlServerExtension –AutoBackupSettings $abs | Update-AzureVM**

## Comprobación del estado de la extensión
Si desea comprobar el estado de esta extensión y los servicios asociados a ella, puede utilizar cualquier portal. En los detalles de la máquina virtual existente, busque **Extensiones** en **Configuración**.

También puede utilizar el siguiente cmdlet de Azure PowerShell:

**Get-AzureVMSqlServerExtension**

### Sintaxis

Get-AzureVMSqlServerExtension [[-VM] <IPersistentVM>] [[-Version] <string>] [<parámetrosComunes>]

> [AZURE.NOTE]Puede omitir el parámetro -Version. Sin él, el valor predeterminado es la versión más reciente de la extensión.

### Ejemplo
	Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## Eliminación de la extensión con Powershell   
Si desea quitar esta extensión de la máquina virtual, puede utilizar el siguiente cmdlet de Azure Powershell.

**Remove-AzureVMSqlServerExtension**

### Sintaxis
Remove-AzureVMSqlServerExtension -VM <IPersistentVM> [<CommonParameters>]

<!---HONumber=August15_HO6-->