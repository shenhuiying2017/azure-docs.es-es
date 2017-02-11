---
title: "Administración de VM con Resource Manager y con PowerShell | Microsoft Docs"
description: "Administre máquinas virtuales con Azure Resource Manager y PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 48930854-7888-4e4c-9efb-7d1971d4cc14
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: 63e822de6ae50be33590048140e06e89526282ee


---
# <a name="manage-azure-virtual-machines-using-resource-manager-and-powershell"></a>Administración de máquinas virtuales de Azure con Resource Manager y PowerShell
## <a name="install-azure-powershell"></a>Instalar Azure Powershell
Consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs) para más información sobre cómo instalar la versión más reciente de Azure PowerShell, seleccionar la suscripción que desea usar e iniciar sesión en su cuenta.

## <a name="set-variables"></a>Configuración de variables
Todos los comandos el artículo requieren el nombre del grupo de recursos donde se encuentra la máquina virtual y el nombre de la máquina virtual que administrar. Reemplace el valor de **$rgName** por el nombre del grupo de recursos que contiene la máquina virtual. Reemplace el valor de **$vmName** por el nombre de la máquina virtual. Cree las variables.

    $rgName = "resource-group-name"
    $vmName = "VM-name"

## <a name="display-information-about-a-virtual-machine"></a>Visualización de información acerca de una máquina virtual
Obtenga la información de la máquina virtual.

    Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Devuelve algo parecido al siguiente ejemplo:

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

## <a name="stop-a-virtual-machine"></a>Detención de una máquina virtual
Detenga una máquina virtual en ejecución.

    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Se te pedirá una confirmación:

    Virtual machine stopping operation
    This cmdlet will stop the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Escriba **Y** para detener la máquina virtual.

Después de unos minutos, devuelve algo parecido a este ejemplo:

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:11:57 PM
    EndTime    : 9/13/2016 12:14:40 PM

## <a name="start-a-virtual-machine"></a>Inicio de una máquina virtual
Inicie la máquina virtual si está detenida.

    Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Después de unos minutos, devuelve algo parecido a este ejemplo:

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:32:55 PM
    EndTime    : 9/13/2016 12:35:09 PM

Si desea reiniciar una máquina virtual que ya se está ejecutando, use el comando **Restart-AzureRmVM** que se describe a continuación.

## <a name="restart-a-virtual-machine"></a>Reinicio de una máquina virtual
Reinicie la máquina virtual en ejecución.

    Restart-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Devuelve algo parecido al siguiente ejemplo:

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:54:40 PM
    EndTime    : 9/13/2016 12:55:54 PM

## <a name="delete-a-virtual-machine"></a>Eliminación de una máquina virtual
Elimine la máquina virtual.  

    Remove-AzureRmVM -ResourceGroupName $rgName –Name $vmName

> [!NOTE]
> Puede usar el parámetro **–Force** para omitir la solicitud de confirmación.
> 
> 

Si no usa el parámetro -Force, se le pedirá confirmación:

    Virtual machine removal operation
    This cmdlet will remove the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Devuelve algo parecido al siguiente ejemplo:

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

## <a name="update-a-virtual-machine"></a>Actualización de una máquina virtual
Este ejemplo muestra cómo actualizar el tamaño de la máquina virtual.

    $vmSize = "Standard_A1"
    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    $vm.HardwareProfile.vmSize = $vmSize
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm

Devuelve algo parecido al siguiente ejemplo:

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

Consulte [Tamaños de las máquinas virtuales Linux en Azure](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ver una lista de los tamaños disponibles para una máquina virtual.

## <a name="add-a-data-disk-to-a-virtual-machine"></a>Adición de disco de datos a una máquina virtual
En este ejemplo se muestra cómo agregar un disco de datos a una máquina virtual existente.

    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    Add-AzureRmVMDataDisk -VM $vm -Name "disk-name" -VhdUri "https://mystore1.blob.core.windows.net/vhds/datadisk1.vhd" -LUN 0 -Caching ReadWrite -DiskSizeinGB 1 -CreateOption Empty
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm

No se inicializó el disco que agregó. Para hacerlo, puede iniciar sesión en él y utilizar la característica de administración de discos. Si instaló WinRM y un certificado cuando lo creó, puede usar PowerShell en remoto para inicializar el disco. También puede utilizar una extensión de script personalizada: 

    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"

El archivo de script puede tener un contenido parecido a este código para inicializar los discos:

    $disks = Get-Disk |   Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { ([char]$_) }
    $count = 0
    $labels = @("data1","data2")

    foreach($d in $disks) {
        $driveLetter = $letters[$count].ToString()
        $d | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] `
            -Confirm:$false -Force 
        $count++
    }

## <a name="next-steps"></a>Pasos siguientes
Si se produjeron problemas con la implementación, le recomendamos echar un vistazo a [Solución de problemas de implementaciones de grupo de recursos con Azure Portal](../resource-manager-troubleshoot-deployments-portal.md).




<!--HONumber=Dec16_HO2-->


