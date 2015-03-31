<properties
   pageTitle="administrar-VM-powershell de azure"
   description="Administrar las máquinas virtuales con PowerShell de Azure"
   services="virtual-machines"
   documentationCenter="windows"
   authors="singhkay"
   manager="timlt"
   editor=""/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="02/20/2015"
   ms.author="kasing"/>

# Administrar las máquinas virtuales con PowerShell de Azure

Antes de comenzar, necesitará asegurarse de que tiene instalado PowerShell de Azure. Para ello, visite [Cómo instalar y configurar PowerShell de Azure](../install-configure-powershell/)

## Obtener una imagen

Antes de crear una máquina virtual, necesitará decidir **qué imagen utilizará**. Puede obtener una lista de imágenes mediante el siguiente cmdlet

      Get-AzureVMImage

Este cmdlet devuelve una lista de todas las imágenes disponibles en Azure. Se trata de una lista muy larga y puede ser difícil encontrar la imagen exacta que desea utilizar. En el ejemplo siguiente, estoy usando otros cmdlets de PowerShell para reducir la lista de imágenes devueltas a sólo aquellas que contengan según **Windows Server 2012 R2 Datacenter**. Además, escogeré que solo obtenga la última imagen al especificar [-1] para la matriz de imágenes devuelta

    $img = (Get-AzureVMImage | Select -Property ImageName, Label | where {$_.Label -like '*Windows Server 2012 R2 Datacenter*'})[-1]

## Crear una VM

La creación de una máquina virtual comienza con el cmdlet **New-AzureVMConfig**. Aquí, especificará el **nombre** de la máquina virtual, el **tamaño** de la máquina virtual y la **imagen** que se usará para la máquina virtual. Este cmdlet crea un objeto de máquina virtual local **$myVM** que más adelante se modifica con otros cmdlets de PowerShell de Azure en esta guía.

      $myVM = New-AzureVMConfig -Name "testvm" -InstanceSize "Small" -ImageName $img.ImageName

A continuación, deberá elegir el **nombre de usuario** y la **contraseña** para la máquina virtual. Puede hacerlo con el cmdlet **Add-AzureProvisioningConfig**. Aquí es donde le indica a Azure cuál es el sistema operativo para la máquina virtual. No que todavía está realizando cambios en el objeto local **$myVM**.

    $user = "azureuser"
    $pass = "&Azure1^Pass@"
    $myVM = Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass

Finalmente, está listo para poner en marcha la máquina virtual en Azure. Para ello, deberá usar el cmdlet **New-AzureVM**

> [AZURE.NOTE] Deberá configurar el servicio en la nube antes de crear la máquina virtual. Existen dos formas de hacerlo.
* Crear el servicio en la nube mediante el cmdlet New-AzureService. Si elige este método, deberá asegurarse de que la ubicación especificada en el cmdlet New-AzureVM a continuación coincide con la ubicación del servicio en la nube, de lo contrario, se producirá un error de cmdlet de ejecución del cmdlet New-AzureVM.
* Permitir que el cmdlet New-AzureVM lo haga por usted. Deberá asegurarse de que el nombre del servicio es único, de lo contrario, se producirá un error de cmdlet de ejecución del cmdlet New-AzureVM.

    New-AzureVM -ServiceName "mytestserv" -VMs $myVM -Location "West US"

**OPCIONAL**

Puede utilizar otros cmdlets como **Add-AzureDataDisk**, **Add-AzureEndpoint** para configurar opciones adicionales para la máquina virtual.

## Obtener una máquina virtual
Ahora que ha creado una máquina virtual en Azure, sin duda deseará ver cómo actúa. Puede hacerlo mediante el cmdlet **Get-AzureVM**, como se muestra a continuación

    Get-AzureVM -ServiceName "mytestserv" -Name "testvm"


## Pasos siguientes
[Conectarse a una máquina virtual de Azure con RDP o SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx)<br>
[Preguntas más frecuentes sobre las máquinas virtuales de Azure](https://msdn.microsoft.com/library/azure/dn683781.aspx)

<!--HONumber=47-->
