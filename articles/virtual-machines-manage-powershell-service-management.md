<properties
   pageTitle="Tareas comunes de máquinas virtuales mediante el modo Administración de servicios de Azure PowerShell"
   description="Proporciona comandos para las tareas comunes de la máquina virtual con el modo Administración de servicios de Azure PowerShell"
   services="virtual-machines"
   documentationCenter="windows"
   authors="KBDAzure"
   manager="timlt"
   editor=""/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="04/22/2015"
   ms.author="kathydav;singhkay"/>

# Tareas comunes de máquinas virtuales con Azure PowerShell en modo Administración de servicio

 > [AZURE.SELECTOR]
    - [Resource Manager](virtual-machines-manage-powershell-resource-management.md)
    - [Service Management](virtual-machines-manage-powershell-service-management.md)

Muchas tareas que se realizan cada día para administrar las máquinas virtuales pueden automatizarse mediante cmdlets de Azure PowerShell en modo Administrador de servicios. En este artículo se proporcionan comandos de ejemplo para las tareas más sencillas, así como vínculos a artículos que muestran los comandos para tareas más complejas.

>[AZURE.NOTE] Si no ha instalado y configurado todavía Azure PowerShell, obtenga instrucciones [aquí](install-configure-powershell.md).

## Utilización de los comandos de ejemplo
Tendrá que reemplazar parte del texto en los comandos con texto que sea adecuado para su entorno. Los símbolos < and > indican texto que se debe reemplazar. Al reemplazar el texto, quite los símbolos pero deje las comillas en su lugar.

## Obtención de una máquina virtual
Es una tarea básica que utilizará a menudo. Utilícelo para obtener información acerca de una máquina virtual, realizar tareas en una máquina virtual y obtener el resultado para almacenarlo en una variable.

Para obtener información acerca de la máquina virtual, ejecute este comando y reemplace todo el contenido de las comillas, incluidos los caracteres < and >:

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Para almacenar la salida en una variable $vm, ejecute:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Inicio de sesión en una máquina virtual Windows

Ejecute estos comandos:

>[AZURE.NOTE]Puede obtener el nombre de la máquina virtual y del servicio de nube en la presentación del comando **Get-AzureVM**.
>
	$svcName="<cloud service name>"
	$vmName="<virtual machine name>"
	$localPath="<drive and folder location to store the downloaded RDP file, example: c:\temp >"
	$localFile=$localPath + "\" + $vmname + ".rdp"
	Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch

## Detención de una máquina virtual

Ejecute este comando:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT]Utilice el parámetro **StayProvisioned** para mantener la IP virtual \(VIP\) del servicio de nube en caso de que sea la última máquina virtual en ese servicio de nube. Si usa este parámetro, se le facturará por la máquina virtual.

## Inicio de una máquina virtual

Ejecute este comando:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Anexión de un disco de datos
Esta tarea requiere unos pocos pasos. En primer lugar, utilice el cmdlet \*\*\* Add-AzureDataDisk \*\*\* para agregar el disco al objeto $vm y, a continuación, utilice el cmdlet Update-AzureVM para actualizar la configuración de la máquina virtual.

También tendrá que decidir si desea adjuntar un disco nuevo o uno que contenga los datos. Para un disco nuevo, el comando crea el archivo .vhd y lo adjunta en el mismo comando.

Para adjuntar un disco nuevo, ejecute este comando:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM <$vm> `
              | Update-AzureVM

Para adjuntar un disco de datos existente, ejecute este comando:

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> `
              | Update-AzureVM

Para adjuntar discos de datos desde un archivo .vhd existente en el almacenamiento de blobs, ejecute este comando:

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> `
              | Update-AzureVM

## Creación de una máquina virtual Windows

Para crear una nueva máquina virtual de Windows en Azure, siga las instrucciones de [Uso de Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Windows](virtual-machines-ps-create-preconfigure-windows-vms.md). Este tema le guiará por la creación de un conjunto de comandos de PowerShell que crea una máquina virtual de Windows que puede configurarse previamente con:

- Pertenencia al dominio de Active Directory
- Discos adicionales
- Como miembro de un conjunto con equilibrio de carga
- Una dirección IP estática


<!--HONumber=52-->
