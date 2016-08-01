<properties
	pageTitle="Administración de VM de un conjunto de escalado de máquinas virtuales | Microsoft Azure"
	description="Administración de máquinas de un conjunto de escalado de máquinas virtuales mediante Azure PowerShell."
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/14/2016"
	ms.author="davidmu"/>

# Administración de máquinas de un conjunto de escalado de máquinas virtuales

Use las tareas de este artículo para administrar recursos de máquinas virtuales del conjunto de escalado de máquinas virtuales.

Todas las tareas que implican administrar una máquina virtual de un conjunto de escalado requieren que conozca el identificador de instancia de la máquina que desea administrar. Puede usar el [Explorador de recursos de Azure](https://resources.azure.com) para buscar el identificador de instancia de una máquina virtual de un conjunto de escalado. El Explorador de recursos también se puede usar para comprobar el estado de las tareas que finaliza el usuario.

Consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md) para más información sobre cómo instalar la versión más reciente de Azure PowerShell, seleccionar la suscripción que quiere usar e iniciar sesión en su cuenta de Azure.

## Visualización de información sobre un conjunto de escalado de máquinas virtuales

Puede obtener información general sobre un conjunto de escalado, que también se conoce como la vista de instancia. O bien, puede obtener información más específica, como información sobre los recursos del conjunto.

En el comando siguiente, reemplace el *nombre del grupo de recursos* por el nombre del grupo de recursos que contiene el conjunto de escalado de máquinas virtuales, y el *nombre del conjunto de escalado* por el nombre del conjunto de escalado de máquinas virtuales. Por último, ejecútelo:

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

Entonces, se devuelve algo parecido a lo siguiente:

    Id                                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                                        : myvmss1
    Type                                        : Microsoft.Compute/virtualMachineScaleSets
    Location                                    : centralus
    Sku                                         :
      Name                                      : Standard_A0
      Tier                                      : Standard
      Capacity                                  : 3
    UpgradePolicy                               :
      Mode                                      : Manual
    VirtualMachineProfile                       :
      OsProfile                                 :
        ComputerNamePrefix                      : vmss1
        AdminUsername                           : admin1
        WindowsConfiguration                    :
          ProvisionVMAgent                      : True
          EnableAutomaticUpdates                : True
    StorageProfile                              :
      ImageReference                            :
        Publisher                               : MicrosoftWindowsServer
        Offer                                   : WindowsServer
        Sku                                     : 2012-R2-Datacenter
        Version                                 : latest
      OsDisk                                    :
        Name                                    : vmssosdisk
        Caching                                 : ReadOnly
        CreateOption                            : FromImage
        VhdContainers[0]                        : https://astore.blob.core.windows.net/vmss
        VhdContainers[1]                        : https://gstore.blob.core.windows.net/vmss
        VhdContainers[2]                        : https://mstore.blob.core.windows.net/vmss
        VhdContainers[3]                        : https://sstore.blob.core.windows.net/vmss
        VhdContainers[4]                        : https://ystore.blob.core.windows.net/vmss
    NetworkProfile                              :
      NetworkInterfaceConfigurations[0]         :
        Name                                    : mync1
        Primary                                 : True
        IpConfigurations[0]                     :
          Name                                  : ip1
          Subnet                                :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/virtualNetworks/myvn1/subnets/mysn1
          LoadBalancerBackendAddressPools[0]    :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/backendAddressPools/bepool1
        LoadBalancerInboundNatPools[0]          :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/inboundNatPools/natpool1
    ExtensionProfile                            :
      Extensions[0]                             :
        Name                                    : Microsoft.Insights.VMDiagnosticsSettings
        Publisher                               : Microsoft.Azure.Diagnostics
        Type                                    : IaaSDiagnostics
        TypeHandlerVersion                      : 1.5
        AutoUpgradeMinorVersion                 : True
        Settings                                : {"xmlCfg":"...","storageAccount":"astore"}
    ProvisioningState                           : Succeeded
    
En este comando, reemplace el *nombre del grupo de recursos* por el nombre del grupo de recursos que contiene el conjunto de escalado de máquinas virtuales, *el nombre del conjunto de escalado* por el nombre del conjunto de escalado de máquina virtual y *#* por el identificador de instancia de la máquina virtual sobre el que desea obtener información y, a continuación, ejecútelo.

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
        
Entonces, se devuelve algo parecido a lo siguiente:

    Id                            : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/
                                    virtualMachineScaleSets/myvmss1/virtualMachines/0
    Name                          : myvmss1_0
    Type                          : Microsoft.Compute/virtualMachineScaleSets/virtualMachines
    Location                      : centralus
    InstanceId                    : 0
    Sku                           :
      Name                        : Standard_A0
      Tier                        : Standard
    LatestModelApplied            : True
    StorageProfile                :
      ImageReference              :
        Publisher                 : MicrosoftWindowsServer
        Offer                     : WindowsServer
        Sku                       : 2012-R2-Datacenter
        Version                   : 4.0.20160617
      OsDisk                      :
        OsType                    : Windows
        Name                      : vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8
        Vhd                       :
          Uri                     : https://astore.blob.core.windows.net/vmss/vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8.vhd
        Caching                   : ReadOnly
        CreateOption              : FromImage
    OsProfile                     :
      ComputerName                : myvmss1-0
      AdminUsername               : admin1
      WindowsConfiguration        :
        ProvisionVMAgent          : True
        EnableAutomaticUpdates    : True
    NetworkProfile                :
      NetworkInterfaces[0]        :
        Id                        : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/
                                    myvmss1/virtualMachines/0/networkInterfaces/mync1
    ProvisioningState             : Succeeded
    Resources[0]                  :
      Id                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachines/
                                    myvmss1_0/extensions/Microsoft.Insights.VMDiagnosticsSettings
      Name                        : Microsoft.Insights.VMDiagnosticsSettings
      Type                        : Microsoft.Compute/virtualMachines/extensions
      Location                    : centralus
      Publisher                   : Microsoft.Azure.Diagnostics
      VirtualMachineExtensionType : IaaSDiagnostics
      TypeHandlerVersion          : 1.5
      AutoUpgradeMinorVersion     : True
      Settings                    : {"xmlCfg":"...","storageAccount":"astore"}
      ProvisioningState           : Succeeded
        
## Inicio de una máquina virtual de un conjunto de escalado

En este comando, reemplace el *nombre del grupo de recursos* por el nombre del grupo de recursos que contiene el conjunto de escalado de máquinas virtuales, *nombre del conjunto de escalado* por el nombre del conjunto de escalado y *#* por el identificador de la máquina virtual que desee iniciar. Por último, ejecútelo:

    Start-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

En el Explorador de recursos, podemos ver que el estado de la instancia es **En ejecución**:

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T02:10:08.0730839+00:00"
      },
      {
        "code": "PowerState/running",
        "level": "Info",
        "displayStatus": "VM running"
      }
    ]

Puede iniciar todas las máquinas virtuales en el conjunto sin usar el parámetro -InstanceId.
    
## Detención de una máquina virtual de un conjunto de escalado

En este comando, reemplace el *nombre del grupo de recursos* por el nombre del grupo de recursos que contiene el conjunto de escalado de máquinas virtuales, *nombre del conjunto de escalado* por el nombre del conjunto de escalado y *#* por el identificador de la máquina virtual que desee detener. Por último, ejecútelo:

	Stop-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

En el Explorador de recursos, podemos ver que el estado de la instancia es **Desasignado**:

	"statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T01:25:17.8792929+00:00"
      },
      {
        "code": "PowerState/deallocated",
        "level": "Info",
        "displayStatus": "VM deallocated"
      }
    ]
    
Para detener una máquina virtual y no desasignarla, utilice el parámetro - StayProvisioned. Puede detener todas las máquinas virtuales en el conjunto sin usar el parámetro -InstanceId.
    
## Reinicio de una máquina virtual de un conjunto de escalado

En este comando, reemplace el *nombre del grupo de recursos* por el nombre del grupo de recursos que contiene el conjunto de escalado de máquinas virtuales, *nombre del conjunto de escalado* por el nombre del conjunto de escalado y *#* por el identificador de la máquina virtual que desee iniciar. Por último, ejecútelo:

	Restart-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
    
Puede reiniciar todas las máquinas virtuales en el conjunto sin usar el parámetro -InstanceId.

## Eliminación de una máquina virtual de un conjunto de escalado

En este comando, reemplace el *nombre del grupo de recursos* por el nombre del grupo de recursos que contiene el conjunto de escalado de máquinas virtuales, *nombre del conjunto de escalado* por el nombre del conjunto de escalado y *#* por el identificador de la máquina virtual que desee quitar del conjunto de escalado. Por último, ejecútelo:

	Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId #

Puede quitar el conjunto de escalado de máquina virtual a la vez sin usar el parámetro -InstanceId.

<!---HONumber=AcomDC_0720_2016-->