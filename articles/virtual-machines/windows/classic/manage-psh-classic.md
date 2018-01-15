---
title: "Administración de máquinas virtuales con Azure PowerShell | Microsoft Docs"
description: "Obtenga información acerca de los comandos que puede usar para automatizar las tareas de administración de sus máquinas virtuales."
services: virtual-machines-windows
documentationcenter: windows
author: singhkays
manager: timlt
editor: 
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 7cdf9bd3-6578-4069-8a95-e8585f04a394
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2016
ms.author: kasing
ms.openlocfilehash: 88ab031778957f20dd42955c2c1311d731e2f588
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="manage-your-virtual-machines-by-using-azure-powershell"></a>Administración de las máquinas virtuales con Azure PowerShell
> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo de Resource Manager. Para ver los comandos de PowerShell comunes que usan el modelo de Resource Manager, consulte [aquí](../../virtual-machines-windows-ps-common-ref.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Muchas tareas que se realizan a diario para administrar las máquinas virtuales pueden automatizarse mediante cmdlets de Azure PowerShell. En este artículo se proporcionan comandos de ejemplo para las tareas más sencillas, así como vínculos a artículos que muestran los comandos para tareas más complejas.

> [!NOTE]
> Si no ha instalado y configurado todavía Azure PowerShell, obtenga instrucciones en el artículo [Cómo instalar y configurar Azure PowerShell](/powershell/azure/overview).
> 
> 

## <a name="how-to-use-the-example-commands"></a>Uso de los comandos de ejemplo
Tendrá que reemplazar parte del texto en los comandos con texto que sea adecuado para su entorno. Los símbolos < y > indican texto que se debe reemplazar. Al reemplazar el texto, quite los símbolos pero deje las comillas en su lugar.

## <a name="get-a-vm"></a>Obtención de una máquina virtual
Es una tarea básica que utilizará a menudo. Utilícelo para obtener información acerca de una máquina virtual, realizar tareas en una máquina virtual y obtener el resultado para almacenarlo en una variable.

Para obtener información acerca de la máquina virtual, ejecute este comando y reemplace todo el contenido de las comillas, incluidos los caracteres < y >:

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Para almacenar la salida en una variable $vm, ejecute:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="log-on-to-a-windows-based-vm"></a>Inicio de sesión en una máquina virtual Windows
Ejecute estos comandos:

> [!NOTE]
> Puede obtener el nombre de la máquina virtual y del servicio de nube en la presentación del comando **Get-AzureVM** .
> 
> $svcName = "<cloud service name>" $vmName = "<virtual machine name>" $localPath = "<ubicación de unidad y carpeta para almacenar el archivo RDP descargado, ejemplo: c:\temp >" $localFile = $localPath + "\" + $vmname + ".rdp" Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch
> 
> 

## <a name="stop-a-vm"></a>Detención de una máquina virtual
Ejecute este comando:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

> [!IMPORTANT]
> Utilice este parámetro para mantener la IP virtual (VIP) del servicio de nube en caso de que sea la última máquina virtual en ese servicio en la nube. <br><br> Si utiliza el parámetro StayProvisioned, se le facturará por la máquina virtual.
> 
> 

## <a name="start-a-vm"></a>Inicio de una máquina virtual
Ejecute este comando:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="attach-a-data-disk"></a>Acoplamiento de un disco de datos
Esta tarea requiere unos pocos pasos. En primer lugar, use el cmdlet ****Add-AzureDataDisk**** para agregar el disco al objeto $vm. A continuación, use el cmdlet **Update-AzureVM** para actualizar la configuración de la VM.

También tendrá que decidir si desea adjuntar un disco nuevo o uno que contenga los datos. Para un disco nuevo, el comando crea el archivo .vhd y lo adjunta.

Para adjuntar un disco nuevo, ejecute este comando:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM $vm | Update-AzureVM

Para adjuntar un disco de datos existente, ejecute este comando:

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

Para adjuntar discos de datos desde un archivo .vhd existente en el almacenamiento de blobs, ejecute este comando:

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> |
              Update-AzureVM

## <a name="create-a-windows-based-vm"></a>Creación de una máquina virtual Windows
Para crear una nueva máquina virtual basada Windows en Azure, siga las instrucciones de [Uso de Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Windows](create-powershell.md). Este tema le guiará por la creación de un conjunto de comandos de Azure PowerShell que crean una máquina virtual Windows que puede configurarse previamente:

* Con pertenencia al dominio de Active Directory;
* Con discos adicionales;
* Como miembro de un conjunto de carga equilibrada;
* Con una dirección IP estática.

