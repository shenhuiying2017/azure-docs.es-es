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
	ms.date="03/22/2016"
	ms.author="davidmu"/>

# Administración de máquinas de un conjunto de escalado de máquinas virtuales

Azure PowerShell proporciona mucha eficacia y flexibilidad al administrar recursos en Microsoft Azure. Use las tareas de este artículo para administrar recursos de máquinas virtuales del conjunto de escalado de máquinas virtuales.

- [Visualización de información sobre un conjunto de escalado de máquinas virtuales](#displayvm)
- [Inicio de una máquina virtual de un conjunto de escalado](#start)
- [Detención de una máquina virtual de un conjunto de escalado](#stop)
- [Reinicio de una máquina virtual de un conjunto de escalado](#restart)
- [Eliminación de una máquina virtual de un conjunto de escalado](#delete)

Todas las tareas que implican administrar una máquina virtual de un conjunto de escalado requieren que conozca el identificador de instancia de la máquina que desea administrar. Puede usar el [Explorador de recursos de Azure](https://resources.azure.com) para buscar el identificador de instancia de una máquina virtual de un conjunto de escalado. El Explorador de recursos también se puede usar para comprobar el estado de las tareas que finaliza el usuario.

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## <a id="displayvm"></a>Visualización de información sobre un conjunto de escalado de máquinas virtuales

Puede obtener información general sobre un conjunto de escalado, que también se conoce como la vista de instancia. O bien, puede obtener información más específica, como información sobre los recursos del conjunto.

En el comando siguiente, reemplace el *nombre del grupo de recursos* por el nombre del grupo de recursos que contiene el conjunto de escalado de máquinas virtuales, y el *nombre del conjunto de escalado* por el nombre del conjunto de escalado de máquinas virtuales. Por último, ejecútelo:

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

Entonces, se devuelve algo parecido a lo siguiente:

    Sku                      :  {
                                  "name": "Standard_A0",
                                  "tier": "Standard",
                                  "capacity": 4
                                }
    UpgradePolicy            :  {
                                  "mode": "Manual"
                                }
    VirtualMachineProfile    :  {
                                  "osProfile": {
                                    "computerNamePrefix": "myvmss1",
                                    "adminUserName": "user1",
                                    "adminPassword": null,
                                    "customData": null,
                                    "windowsConfiguration": {
                                      "provisionVMAgent": true,
                                      "enableAutomaticUpdates": true,
                                      "timeZone": null,
                                      "additionalUnattendContent": null,
                                      "winRM": null
                                    }
                                    "linuxConfiguration": null,
                                    "secrets": []
                                  },
                                  "storageProfile": {
                                    "imageReference": {
                                      "publisher": "MicrosoftWindowsServer",
                                      "offer": "WindowsServer",
                                      "sku": "2012-R2-Datacenter",
                                      "version": "latest"
                                    },
                                    "osDisk": {
                                      "name": "vmssosdisk",
                                      "caching": "ReadOnly",
                                      "createOption": "FromImage",
                                      "osType": null,
                                      "image": null,
                                      "vhdContainers": [
                                        "https://amyst1.blob.core.windows.net/vmss",
                                        "https://gmyst1.blob.core.windows.net/vmss",
                                        "https://mmyst1.blob.core.windows.net/vmss",
                                        "https://smyst1.blob.core.windows.net/vmss",
                                        "https://ymyst1.blob.core.windows.net/vmss"
                                      ]
                                    }
                                  },
                                  "networkProfile": {
                                    "networkInterfaceConfigurations": [
                                      {
                                        "name": "myresnc2",
                                        "properties.primary": true,
                                        "properties.ipConfigurations": [
                                          {
                                            "name": "ip1",
                                            "properties.subnet": {
                                              "id": "/subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Network/virtualNetworks/myresvn1/subnets/myressn1"
                                            },
                                            "properties.loadBalancerBackendAddressPools": [
                                              {
                                                "id": "/subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/myreslb1/backendAddressPools/bepool1"
                                              }
                                            ],
                                            "properties.loadBalancerInboundNatPools": [
                                              {
                                                "id": "/subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/myreslb1/inboundNatPools/natpool1"
                                              }
                                            ],
                                            "id": null
                                          }
                                        ],
                                        "id": null
                                      }
                                    ]
                                  },
                                  "extensionProfile": {
                                    "extensions": [
                                      {
                                        "name": "Microsoft.Insights.VMDiagnosticsSettings",
                                        "properties.publisher": "Microsoft.Azure.Diagnostics",
                                        "properties.type": "IaaSDiagnostics",
                                        "properties.typeHandlerVersion": "1.5",
                                        "properties.autoUpgradeMinorVersion": true,
                                        "properties.settings": {
                                          "xmlCfg": "{encoded configuration}",
                                          "storageAccount": "amyst1"
                                        },
                                        "properties.protectedSettings": null,
                                        "properties.provisioningState": null,
                                        "id": null
                                      }
                                    ]
                                  }
                                }
    ProvisioningState           : Succeeded
    Id                          : /subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                        : myvmss1
	Type                        : Microsoft.Compute/virtualMachineScaleSets
	Location                    : westus
	Tags.Count                  : 0
	Tags                        :

Para obtener información general, reemplace el *nombre del grupo de recursos* por el nombre del grupo de recursos que contiene el conjunto de escalado de máquinas virtuales, y el *nombre del conjunto de escalado* por el nombre del conjunto de escalado de máquinas virtuales. Por último, ejecútelo:

	Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceView

Entonces, se devuelve algo parecido a lo siguiente:

    VirtualMachine   :  {
                          "statusesSummary": [
                            {
                              "code": "ProvisioningState/succeeded",
                              "count": 4
                            }
                          ]
                        }
    Extensions.Count :  1
    Extensions       :  {
                          "name": "Microsoft.Insights.VMDiagnosticsSettings",
                          "statusesSummary": [
                            {
                              "code": "ProvisioningState/succeeded",
                              "count": 4
                            }
                          ]
                        }
	Statuses.Count   :  1
	Statuses         :  {
                          "code": "ProvisioningState/succeeded",
                          "level": "Info",
                          "displayStatus": "Provisioning succeeded",
                          "message": null,
                          "time": "2016-03-14T20:29:37.170809Z"
                        }

## <a id="start"></a>Inicio de una máquina virtual de un conjunto de escalado

En este comando, reemplace el *nombre del grupo de recursos* por el nombre del grupo de recursos que contiene el conjunto de escalado de máquinas virtuales, reemplace el *nombre del conjunto de escalado de VM* por el nombre del conjunto de escalado, reemplace el *identificador de instancia* por el identificador de la máquina virtual que desea reiniciar. Por último, ejecútelo:

    Start-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId "instance id"

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

## <a id="stop"></a>Detención de una máquina virtual de un conjunto de escalado

En este comando, reemplace el *nombre del grupo de recursos* por el nombre del grupo de recursos que contiene el conjunto de escalado de máquinas virtuales, reemplace el *nombre del conjunto de escalado* por el nombre del conjunto de escalado, reemplace el *identificador de instancia* por el identificador de la máquina virtual que desea detener. Por último, ejecútelo:

	Stop-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId "instance id"

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

## <a id="restart"></a>Reinicio de una máquina virtual de un conjunto de escalado

En este comando, reemplace el *nombre del grupo de recursos* por el nombre del grupo de recursos que contiene el conjunto de escalado de máquinas virtuales, reemplace el *nombre del conjunto de escalado* por el nombre del conjunto de escalado, reemplace el *identificador de instancia* por el identificador de la máquina virtual que desea reiniciar. Por último, ejecútelo:

	Restart-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId "instance id"

## <a id="delete"></a>Eliminación de una máquina virtual de un conjunto de escalado

En este comando, reemplace el *nombre del grupo de recursos* por el nombre del grupo de recursos que contiene el conjunto de escalado de máquinas virtuales, reemplace el *nombre del conjunto de escalado* por el nombre del conjunto de escalado, reemplace el *identificador de instancia* por el identificador de la máquina virtual que desea mover desde el conjunto de escalado. Por último, ejecútelo:

	Remove-AzureRmVmssVM -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId "instance id"

<!---HONumber=AcomDC_0427_2016-->