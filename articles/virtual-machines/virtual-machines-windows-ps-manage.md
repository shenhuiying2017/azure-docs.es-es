<properties
	pageTitle="Administración de máquinas virtuales con Resource Manager y con PowerShell | Microsoft Azure"
	description="Administre máquinas virtuales con Azure Resource Manager y PowerShell."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/18/2016"
	ms.author="davidmu"/>

# Administración de máquinas virtuales de Azure con Resource Manager y PowerShell

## Azure PowerShell
 
Consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md) para obtener más información sobre cómo instalar la versión más reciente de Azure PowerShell, seleccionar la suscripción que quiere usar e iniciar sesión en su cuenta de Azure.

## Configuración de variables

Todos los comandos el artículo requieren el nombre del grupo de recursos donde se encuentra la máquina virtual y el nombre de la máquina virtual que administrar. Reemplace el valor de **$rgName** por el nombre del grupo de recursos que contiene la máquina virtual. Reemplace el valor de **$vmName** por el nombre de la máquina virtual. Cree las variables.

        $rgName = "resource group name"
        $vmName = "VM name"

## Visualización de información acerca de una máquina virtual

Obtenga la información de la máquina virtual.
  
        Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Entonces, se devuelve algo parecido a lo siguiente:

        ResourceGroupName        : rg1
        Id                       : /subscriptions/{subscription-id}/resourceGroups/
                                    rg1/providers/Microsoft.Compute/virtualMachines/vm1
        Name                     : vm1
        Type                     : Microsoft.Compute/virtualMachines
        Location                 : centralus
        Tags                     : {}
        AvailabilitySetReference : {
                                     "id": "/subscriptions/{subscription-id}/resourceGroups/
                                       rg1/providers/Microsoft.Compute/availabilitySets/av1"
                                   }
        Extensions               : []
        HardwareProfile          : {
                                     "vmSize": "Standard_A0"
                                   }
        InstanceView             : null
        NetworkProfile           : {
                                     "networkInterfaces": [
                                       {
                                         "properties.primary": null,
                                         "id": "/subscriptions/{subscription-id}/resourceGroups/
                                           rg1/providers/Microsoft.Network/networkInterfaces/nc1"
                                       }
                                     ]
                                   }
        OSProfile                : {
                                     "computerName": "vm1",
                                     "adminUsername": "myaccount1",
                                     "adminPassword": null,
                                     "customData": null,
                                     "windowsConfiguration": {
                                       "provisionVMAgent": true,
                                       "enableAutomaticUpdates": true,
                                       "timeZone": null,
                                       "additionalUnattendContents": [],
                                       "winRM": null
                                     },
                                     "linuxConfiguration": null,
                                     "secrets": []
                                   }
        Plan                     : null
        ProvisioningState        : Succeeded
        StorageProfile           : {
                                     "imageReference": {
                                       "publisher": "MicrosoftWindowsServer",
                                       "offer": "WindowsServer",
                                       "sku": "2012-R2-Datacenter",
                                       "version": "latest"
                                     },
                                     "osDisk": {
                                       "osType": "Windows",
                                       "encryptionSettings": null,
                                       "name": "osdisk",
                                       "vhd": {
                                         "Uri": "http://sa1.blob.core.windows.net/vhds/osdisk1.vhd"
                                       }
                                       "image": null,
                                       "caching": "ReadWrite",
                                       "createOption": "FromImage"
                                     }
                                     "dataDisks": [],
                                   }
        DataDiskNames            : {}
        NetworkInterfaceIDs      : {/subscriptions/{subscription-id}/resourceGroups/
                                     rg1/providers/Microsoft.Network/networkInterfaces/nc1}

## Inicio de una máquina virtual

Inicie la máquina virtual.

        Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Después de unos minutos, devuelve algo parecido a lo siguiente:

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK

## Detención de una máquina virtual

Detenga la máquina virtual.

	    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Se te pedirá una confirmación:

        Virtual machine stopping operation
        This cmdlet will stop the specified virtual machine. Do you want to continue?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
        
Escriba **Y** para detener la máquina virtual.

Después de unos minutos, devuelve algo parecido a lo siguiente:

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK

## Reinicio de una máquina virtual

Reinicie la máquina virtual.

        $rgName = "resource group name"
        $vmName = "VM name"
        Restart-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Entonces, se devuelve algo parecido a lo siguiente:

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK

## Eliminación de una máquina virtual

Elimine la máquina virtual.

        $rgName = "resource group name"
        $vmName = "VM name"
	    Remove-AzureRmVM -ResourceGroupName $rgName –Name $vmName

> [AZURE.NOTE] Puede usar el parámetro **–Force** para omitir la solicitud de confirmación.

Se te pedirá una confirmación si no usaste el parámetro -Force:

	    Virtual machine removal operation
	    This cmdlet will remove the specified virtual machine. Do you want to continue?
	    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Entonces, se devuelve algo parecido a lo siguiente:

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK

## Actualización de una máquina virtual

Este ejemplo muestra cómo actualizar el tamaño de la máquina virtual.
        
        $vmSize = "Standard_A1"
        $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
        $vm.HardwareProfile.vmSize = $vmSize
        Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
    
    See [Sizes for virtual machines in Azure](virtual-machines-windows-sizes.md) for a list of available sizes for a virtual machine.

Entonces, se devuelve algo parecido a lo siguiente:

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK

## Pasos siguientes

Si se produjeron problemas con la implementación, debería echar un vistazo a [Solución de problemas de implementaciones de grupo de recursos con el Portal de Azure](../resource-manager-troubleshoot-deployments-portal.md).

<!---HONumber=AcomDC_0420_2016-->