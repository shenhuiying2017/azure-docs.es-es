---
title: "Administración de VM en un conjunto de escalado de máquinas virtuales | Microsoft Docs"
description: "Administración de máquinas de un conjunto de escalado de máquinas virtuales mediante Azure PowerShell."
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: adegeo
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 88541f6054df1476866d68fdc1c9690a73ada83c
ms.contentlocale: es-es
ms.lasthandoff: 04/27/2017

---
# <a name="manage-virtual-machines-in-a-virtual-machine-scale-set"></a>Administración de máquinas virtuales en un conjunto de escalado de máquinas virtuales
Use las tareas de este artículo para administrar máquinas virtuales en su conjunto de escalado de máquinas virtuales.

La mayoría de las tareas que implican administrar una máquina virtual en un conjunto de escalado requieren que conozca el identificador de instancia de la máquina que desea administrar. Puede usar el [Explorador de recursos de Azure](https://resources.azure.com) para buscar el identificador de instancia de una máquina virtual de un conjunto de escalado. El Explorador de recursos también se puede usar para comprobar el estado de las tareas que finaliza el usuario.

Consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azure/overview) para más información sobre cómo instalar la versión más reciente de Azure PowerShell, seleccionar la suscripción que desea usar e iniciar sesión en su cuenta.

## <a name="display-information-about-a-scale-set"></a>Visualización de información sobre un conjunto de escalado
Puede obtener información general sobre un conjunto de escalado, que también se conoce como la vista de instancia. O bien, puede obtener información más específica, como información sobre los recursos del conjunto de escalado.

Reemplace los valores entre comillas por el nombre del grupo de recursos y el conjunto de escalado y, a continuación, ejecute el comando:

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

Reemplace los valores entre comillas por el nombre de su grupo de recursos y conjunto de escalado. Reemplace *#* por el identificador de instancia de la máquina virtual sobre la que desea obtener información y, a continuación, ejecútelo:

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Devuelve algo parecido al siguiente ejemplo:

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

## <a name="start-a-virtual-machine-in-a-scale-set"></a>Inicio de una máquina virtual de un conjunto de escalado
Reemplace los valores entre comillas por el nombre de su grupo de recursos y conjunto de escalado. Reemplace *#* por el identificador de la máquina virtual que desea iniciar y, a continuación, ejecútelo:

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

Puede iniciar todas las máquinas virtuales del conjunto de escalado sin usar el parámetro -InstanceId.

## <a name="stop-a-virtual-machine-in-a-scale-set"></a>Detención de una máquina virtual de un conjunto de escalado
Reemplace los valores entre comillas por el nombre de su grupo de recursos y conjunto de escalado. Reemplace *#* por el identificador de la máquina virtual que desea detener y, a continuación, ejecútelo:

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

## <a name="restart-a-virtual-machine-in-a-scale-set"></a>Reinicio de una máquina virtual de un conjunto de escalado
Reemplace los valores entre comillas por el nombre de su grupo de recursos y conjunto de escalado. Reemplace *#* por el identificador de la máquina virtual que desea reiniciar y, a continuación, ejecútelo:

    Restart-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Puede reiniciar todas las máquinas virtuales del conjunto sin usar el parámetro -InstanceId.

## <a name="remove-a-virtual-machine-from-a-scale-set"></a>Eliminación de una máquina virtual de un conjunto de escalado
Reemplace los valores entre comillas por el nombre de su grupo de recursos y conjunto de escalado. Reemplace *#* por el identificador de la máquina virtual que desea quitar y, a continuación, ejecútelo:  

    Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId #

Puede quitar el conjunto de escalado de máquina virtual a la vez sin usar el parámetro -InstanceId.

## <a name="change-the-capacity-of-a-scale-set"></a>Cambio de la capacidad de un conjunto de escalado
Puede agregar o quitar máquinas virtuales cambiando la capacidad del conjunto. Obtenga el conjunto de escalado que desea cambiar, establezca la capacidad en el valor que desee y, a continuación, actualice el conjunto de escalado con la nueva capacidad. En estos comandos, reemplace los valores entre comillas por el nombre de su grupo de recursos y conjunto de escalado.

    $vmss = Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
    $vmss.sku.capacity = 5
    Update-AzureRmVmss -ResourceGroupName "resource group name" -Name "scale set name" -VirtualMachineScaleSet $vmss 

Si va a quitar máquinas virtuales del conjunto de escalado, se quitan primero las máquinas virtuales con los identificadores más altos.


