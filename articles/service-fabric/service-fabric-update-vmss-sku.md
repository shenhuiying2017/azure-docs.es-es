---
title: Procedimiento para actualizar o migrar la SKU del tipo de nodo principal a SKU superiores | Microsoft Docs
description: Aprenda a actualizar o a migrar la SKU del tipo de nodo principal a SKU superiores mediante comandos de PowerShell y comandos de la CLI.
services: service-fabric
documentationcenter: .net
author: v-rachiw
manager: navya
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/08/2018
ms.author: v-rachiw
ms.openlocfilehash: 8d5b560068a9e0bc0169bfdb98c5e939e34a3b8c
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
ms.locfileid: "34274034"
---
# <a name="upgrademigrate-the-sku-for-primary-node-type-to-higher-sku"></a>Actualización o migración de la SKU del tipo de nodo principal a SKU superiores

Este artículo describe cómo actualizar o migrar la SKU del tipo de nodo principal del clúster de Service Fabric a SKU superiores con Azure PowerShell

## <a name="add-a-new-virtual-machine-scale-set"></a>Adición de un nuevo conjunto de escalado de máquinas virtuales 

Implementación de un nuevo conjunto de escalado de máquinas virtuales y de Load Balancer. La configuración de la extensión de Service Fabric (especialmente el tipo de nodo) del nuevo conjunto de escalado de máquinas virtuales debe ser la misma que la del antiguo conjunto de escalado que está intentando actualizar. Compruebe en Service Fabric Explorer que los nuevos nodos están disponibles. 

### <a name="azure-powershell"></a>Azure PowerShell
El ejemplo siguiente usa Azure PowerShell para implementar la plantilla actualizada de Resource Manager *template.json* mediante el grupo de recursos llamado *myResourceGroup*:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName myResourceGroup -TemplateFile \template\template.json 
```

Consulte el siguiente ejemplo para modificar la plantilla json para agregar un nuevo recurso de conjunto de escalado de máquinas virtuales con el tipo de nodo principal en el clúster existente.

```json
        {
            "apiVersion": "[variables('vmssApiVersion')]",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[parameters('vmNodeType2Name')]",
            "location": "[parameters('computeLocation')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
                "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType2Name')))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('applicationDiagnosticsStorageAccountName'))]"
            ],
            "properties": {
                "overprovision": "[parameters('overProvision')]",
                "upgradePolicy": {
                    "mode": "Automatic"
                },
                "virtualMachineProfile": {
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "[concat(parameters('vmNodeType2Name'),'_ServiceFabricNode')]",
                                "properties": {
                                    "type": "ServiceFabricNode",
                                    "autoUpgradeMinorVersion": true,
                                    "protectedSettings": {
                                        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                                        "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                                    },
                                    "publisher": "Microsoft.Azure.ServiceFabric",
                                    "settings": {
                                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                                        "dataPath": "D:\\\\SvcFab",
                                        "durabilityLevel": "Bronze",
                                        "enableParallelJobs": true,
                                        "nicPrefixOverride": "[parameters('subnet0Prefix')]",
                                        "certificate": {
                                            "thumbprint": "[parameters('certificateThumbprint')]",
                                            "x509StoreName": "[parameters('certificateStoreValue')]"
                                        }
                                    },
                                    "typeHandlerVersion": "1.0"
                                }
                            },
                            {
                                "name": "[concat('VMDiagnosticsVmExt','_vmNodeType2Name')]",
                                "properties": {
                                    "type": "IaaSDiagnostics",
                                    "autoUpgradeMinorVersion": true,
                                    "protectedSettings": {
                                        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                                        "storageAccountEndPoint": "https://core.windows.net/"
                                    },
                                    "publisher": "Microsoft.Azure.Diagnostics",
                                    "settings": {
                                        "WadCfg": {
                                            "DiagnosticMonitorConfiguration": {
                                                "overallQuotaInMB": "50000",
                                                "EtwProviders": {
                                                    "EtwEventSourceProviderConfiguration": [
                                                        {
                                                            "provider": "Microsoft-ServiceFabric-Actors",
                                                            "scheduledTransferKeywordFilter": "1",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricReliableActorEventTable"
                                                            }
                                                        },
                                                        {
                                                            "provider": "Microsoft-ServiceFabric-Services",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricReliableServiceEventTable"
                                                            }
                                                        }
                                                    ],
                                                    "EtwManifestProviderConfiguration": [
                                                        {
                                                            "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                                            "scheduledTransferLogLevelFilter": "Information",
                                                            "scheduledTransferKeywordFilter": "4611686018427387904",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricSystemEventTable"
                                                            }
                                                        }
                                                    ]
                                                }
                                            }
                                        },
                                        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                                    },
                                    "typeHandlerVersion": "1.5"
                                }
                            }
                        ]
                    },
                    "networkProfile": {
                        "networkInterfaceConfigurations": [
                            {
                                "name": "[concat(parameters('nicName'), '-2')]",
                                "properties": {
                                    "ipConfigurations": [
                                        {
                                            "name": "[concat(parameters('nicName'),'-',2)]",
                                            "properties": {
                                                "loadBalancerBackendAddressPools": [
                                                    {
                                                        "id": "[variables('lbPoolID2')]"
                                                    }
                                                ],
                                                "loadBalancerInboundNatPools": [
                                                    {
                                                        "id": "[variables('lbNatPoolID2')]"
                                                    }
                                                ],
                                                "subnet": {
                                                    "id": "[variables('subnet0Ref')]"
                                                }
                                            }
                                        }
                                    ],
                                    "primary": true
                                }
                            }
                        ]
                    },
                    "osProfile": {
                        "adminPassword": "[parameters('adminPassword')]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "computernamePrefix": "[parameters('vmNodeType2Name')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[parameters('sourceVaultValue')]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateStore": "[parameters('certificateStoreValue')]",
                                        "certificateUrl": "[parameters('certificateUrlValue')]"
                                    }
                                ]
                            }
                        ]
                    },
                    "storageProfile": {
                        "imageReference": {
                            "publisher": "[parameters('vmImagePublisher')]",
                            "offer": "[parameters('vmImageOffer')]",
                            "sku": "[parameters('vmImageSku')]",
                            "version": "[parameters('vmImageVersion')]"
                        },
                        "osDisk": {
                            "caching": "ReadOnly",
                            "createOption": "FromImage",
                            "managedDisk": {
                                "storageAccountType": "[parameters('storageAccountType')]"
                            }
                        }
                    }
                }
            },
            "sku": {
                "name": "[parameters('vmNodeType2Size')]",
                "capacity": "[parameters('nt2InstanceCount')]",
                "tier": "Standard"
            },
            "tags": {
                "resourceType": "Service Fabric",
                "clusterName": "[parameters('clusterName')]"
            }
        },
```


## <a name="remove-old-virtual-machine-scale-set"></a>Eliminación de un conjunto de escalado de máquinas virtuales antiguo

Deshabilite las instancias de máquinas virtuales de un conjunto de escalado de máquinas virtuales antiguo con intención de eliminar el nodo. Esta operación puede tardar mucho tiempo en completarse. Puede deshabilitar todos los nodos a la vez y se pondrán en cola. Espere hasta que todos los nodos estén deshabilitados. 

### <a name="azure-powershell"></a>Azure PowerShell
En el ejemplo siguiente se usa Azure Service Fabric PowerShell para deshabilitar la instancia del nodo denominada *NTvm1_0* del antiguo conjunto de escalado de máquinas virtuales llamado *NTvm1*:

```powershell
Disable-ServiceFabricNode -NodeName NTvm1_0 -Intent RemoveNode 
```

Elimine todo el conjunto de escalado. Espere a que el estado de aprovisionamiento del conjunto de escalado sea Correcto 

### <a name="azure-powershell"></a>Azure PowerShell
En el ejemplo siguiente se usa Azure PowerShell para eliminar todo el conjunto de escalado llamado *NTvm1* del grupo de recursos denominado *myResourceGroup*:

```powershell
Remove-AzureRmVmss -ResourceGroupName myResourceGroup -VMScaleSetName NTvm1
```

## <a name="remove-load-balancer-related-to-old-scale-set"></a>Eliminación de la instancia de Load Balancer relacionada con el conjunto de escalado antiguo

Elimine la instancia de Load Balancer relacionada con el conjunto de escalado antiguo. Este paso provocará un breve período de tiempo de inactividad del clúster

### <a name="azure-powershell"></a>Azure PowerShell
En el ejemplo siguiente se usa Azure PowerShell para eliminar la instancia de Load Balancer llamada *LB-myCluster-NTvm1* relacionada con el antiguo conjunto de escalado del grupo de recursos denominado *myResourceGroup*:

```powershell
Remove-AzureRmLoadBalancer -Name LB-myCluster-NTvm1 -ResourceGroupName myResourceGroup
```

## <a name="remove-public-ip-related-to-old-scale-set"></a>Eliminación de la dirección IP pública relacionada con el antiguo conjunto de escalado

Almacene la configuración DNS de la dirección IP pública relacionada con el antiguo conjunto de escalado en la variable y, a continuación, elimine la dirección IP pública.

### <a name="azure-powershell"></a>Azure PowerShell
En el ejemplo siguiente se usa Azure PowerShell para almacenar la configuración DNS de la dirección IP pública denominada *LBIP-myCluster-NTvm1* en la variable y quitar la dirección IP:

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1  -ResourceGroupName myResourceGroup
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn
Remove-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1 -ResourceGroupName myResourceGroup
```

## <a name="update-public-ip-address-related-to-new-scale-set"></a>Actualización de la dirección IP pública relacionada con el nuevo conjunto de escalado

Actualice la configuración DNS de la dirección IP pública relacionada con el nuevo conjunto de escalado con la misma configuración de la dirección IP pública del conjunto antiguo.

### <a name="azure-powershell"></a>Azure PowerShell
En el ejemplo siguiente se usa Azure PowerShell para actualizar la configuración DNS de la dirección IP pública denominada *LBIP-myCluster-NTvm3* con la configuración DNS almacenada en variables en el paso anterior:

```powershell
$PublicIP = Get-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1  -ResourceGroupName myResourceGroup
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP 
```

## <a name="remove-knowledge-of-service-fabric-node-from-fm"></a>Eliminación de conocimientos del nodo de Service Fabric desde FM

Notifica a Service Fabric que los nodos que están fuera de servicio se han eliminado del clúster. (Ejecute este comando para todas las instancias de máquinas virtuales del antiguo conjunto de escalado de máquinas virtuales) (Si el nivel de durabilidad del antiguo conjunto de escalado de máquinas virtuales era Silver o Gold, puede que este paso no sea necesario, ya que este paso lo realiza el sistema automáticamente).

### <a name="azure-powershell"></a>Azure PowerShell
En el ejemplo siguiente se usa Azure Service Fabric PowerShell para notificar a Service Fabric que el nodo denominado *NTvm1_0* se ha eliminado:

```powershell
Remove-ServiceFabricNodeState -NodeName NTvm1_0
```