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
	ms.date="06/07/2016"
	ms.author="davidmu"/>

# Administración de máquinas virtuales de Azure con Resource Manager y PowerShell

## Azure PowerShell
 
Consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para más información sobre cómo instalar la versión más reciente de Azure PowerShell, seleccionar la suscripción que desea usar e iniciar sesión en su cuenta de Azure.

## Configuración de variables

Todos los comandos el artículo requieren el nombre del grupo de recursos donde se encuentra la máquina virtual y el nombre de la máquina virtual que administrar. Reemplace el valor de **$rgName** por el nombre del grupo de recursos que contiene la máquina virtual. Reemplace el valor de **$vmName** por el nombre de la máquina virtual. Cree las variables.

    $rgName = "resource-group-name"
    $vmName = "VM-name"

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

    Restart-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Entonces, se devuelve algo parecido a lo siguiente:

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

## Eliminación de una máquina virtual

Elimine la máquina virtual.

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

## Cambio de tamaño de una máquina virtual

Este ejemplo muestra cómo actualizar el tamaño de la máquina virtual.
        
    $vmSize = "Standard_A1"
    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    $vm.HardwareProfile.vmSize = $vmSize
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
    
Entonces, se devuelve algo parecido a lo siguiente:

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK
                              
Consulte [Tamaños de las máquinas virtuales Linux en Azure](virtual-machines-windows-sizes.md) para ver una lista de los tamaños disponibles para una máquina virtual.

## Adición de disco de datos a una máquina virtual

En este ejemplo se muestra cómo agregar un disco de datos a una máquina virtual existente.

    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    Add-AzureRmVMDataDisk -VM $vm -Name "disk-name" -VhdUri "https://mystore1.blob.core.windows.net/vhds/datadisk1.vhd" -LUN 0 -Caching ReadWrite -DiskSizeinGB 1 -CreateOption Empty
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm

No se inicializó el disco que agregó. Para hacerlo, puede iniciar sesión en él y utilizar la característica de administración de discos. Si instaló WinRM y un certificado cuando lo creó, puede usar PowerShell en remoto para inicializar el disco. También puede utilizar una extensión de script personalizada:

    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"

El archivo de script puede tener un contenido parecido a lo siguiente para inicializar los discos:

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

## Pasos siguientes

Si se produjeron problemas con la implementación, le recomendamos echar un vistazo a [Solución de problemas de implementaciones de grupo de recursos con el Portal de Azure](../resource-manager-troubleshoot-deployments-portal.md).

<!---HONumber=AcomDC_0907_2016-->