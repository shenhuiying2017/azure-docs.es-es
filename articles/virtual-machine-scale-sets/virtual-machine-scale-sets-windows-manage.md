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
	ms.date="04/26/2016"
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

    Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
    UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
    VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
    ProvisioningState     : Succeeded
    OverProvision         :
    Id                    : /subscriptions/{subscription-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                  : myvmss1
    Type                  : Microsoft.Compute/virtualMachineScaleSets
    Location              : centralus
    Tags                  :

En este comando, reemplace el *nombre del grupo de recursos* por el nombre del grupo de recursos que contiene el conjunto de escalado de máquinas virtuales, *el nombre del conjunto de escalado* por el nombre del conjunto de escalado de máquina virtual y *#* por el identificador de instancia de la máquina virtual sobre el que desea obtener información y, a continuación, ejecútelo.

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
        
Entonces, se devuelve algo parecido a lo siguiente:

    InstanceId         : 1
    Sku                : Microsoft.Azure.Management.Compute.Models.Sku
    LatestModelApplied : True
    InstanceView       :
    HardwareProfile    :
    StorageProfile     : Microsoft.Azure.Management.Compute.Models.StorageProfile
    OsProfile          : Microsoft.Azure.Management.Compute.Models.OSProfile
    NetworkProfile     : Microsoft.Azure.Management.Compute.Models.NetworkProfile
    DiagnosticsProfile :
    AvailabilitySet    :
    ProvisioningState  : Succeeded
    LicenseType        :
    Plan               :
    Resources          :
    Id                 : /subscriptions/{subscription-id}/resourceGroups/myrg1/providers/Microsoft.
                         Compute/virtualMachineScaleSets/myvmss1/virtualMachines/1
    Name               : myvmss1_1
    Type               : Microsoft.Compute/virtualMachineScaleSets/virtualMachines
    Location           : centralus
    Tags               :
        
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

<!---HONumber=AcomDC_0504_2016-->