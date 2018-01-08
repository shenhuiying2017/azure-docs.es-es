---
title: "Redes para conjuntos de escalado de máquinas virtuales de Azure | Microsoft Docs"
description: "Propiedades de configuración de red para el conjunto de escalado de máquinas virtuales de Azure."
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.author: negat
ms.openlocfilehash: 27f1ec18026b38d5cdb2aecfde2d01f32a86349e
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="networking-for-azure-virtual-machine-scale-sets"></a>Redes para conjuntos de escalado de máquinas virtuales de Azure

Al implementar el conjunto de escalado de máquinas virtuales de Azure a través del portal, algunas propiedades de red se establecen de forma predeterminada, por ejemplo, una instancia de Azure Load Balancer mediante reglas NAT de entrada. En este artículo se describe cómo utilizar algunas de las características más avanzadas de redes que puede configurar con conjuntos de escalado.

Puede configurar todas las características que se tratan en este artículo usando plantillas de Azure Resource Manager. También se incluyen ejemplos de la CLI de Azure y PowerShell para las características seleccionadas. Use CLI 2.10 y PowerShell 4.2.0 o una versión posterior.

## <a name="accelerated-networking"></a>Redes aceleradas
Azure Accelerated Networking mejora el rendimiento de la red al permitir la virtualización de E/S de raíz única (SR-IOV) en una máquina virtual. Para obtener más información sobre el uso de redes acelerada, consulte los temas sobre redes aceleradas para máquinas virtuales [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md) o [Linux](../virtual-network/create-vm-accelerated-networking-cli.md). Para usar las redes aceleradas con conjuntos de escalado, establezca enableAcceleratedNetworking en **true** en los valores de networkInterfaceConfigurations de su conjunto de escalado. Por ejemplo: 
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
    {
      "name": "niconfig1",
      "properties": {
        "primary": true,
        "enableAcceleratedNetworking" : true,
        "ipConfigurations": [
          ...
        ]
      }
    }
   ]
}
```

## <a name="create-a-scale-set-that-references-an-existing-azure-load-balancer"></a>Creación de un conjunto de escalado que hace referencia a una instancia de Azure Load Balancer existente
Cuando se crea un conjunto de escalado mediante Azure Portal, se crea un nuevo equilibrador de carga para la mayoría de las opciones de configuración. Si crea un conjunto de escalado, que debe hacer referencia a un equilibrador de carga existente, puede hacerlo mediante la CLI. El siguiente script de ejemplo crea un equilibrador de carga y luego un conjunto de escalado que hace referencia a él:
```bash
az network lb create -g lbtest -n mylb --vnet-name myvnet --subnet mysubnet --public-ip-address-allocation Static --backend-pool-name mybackendpool

az vmss create -g lbtest -n myvmss --image Canonical:UbuntuServer:16.04-LTS:latest --admin-username negat --ssh-key-value /home/myuser/.ssh/id_rsa.pub --upgrade-policy-mode Automatic --instance-count 3 --vnet-name myvnet --subnet mysubnet --lb mylb --backend-pool-name mybackendpool

```

## <a name="configurable-dns-settings"></a>Valores de DNS configurables
De forma predeterminada, los conjuntos de escalado adoptan los valores de DNS específicos de la red virtual y la subred en las que se crearon. No obstante, puede configurar los valores de DNS para un conjunto de escalado directamente.
~
### <a name="creating-a-scale-set-with-configurable-dns-servers"></a>Creación de un conjunto de escalado con servidores DNS configurables
Para crear un conjunto de escalado con una configuración de DNS personalizada mediante CLI 2.0, agregue el argumento **--dns-servers** al comando **vmss create**, seguido de las direcciones IP del servidor separadas por un espacio. Por ejemplo: 
```bash
--dns-servers 10.0.0.6 10.0.0.5
```
Para configurar servidores DNS personalizados en una plantilla de Azure, agregue una propiedad dnsSettings a la sección de networkInterfaceConfigurations del conjunto de escalado. Por ejemplo: 
```json
"dnsSettings":{
    "dnsServers":["10.0.0.6", "10.0.0.5"]
}
```

### <a name="creating-a-scale-set-with-configurable-virtual-machine-domain-names"></a>Creación de un conjunto de escalado con nombres de dominio de máquinas virtuales configurables
Para crear un conjunto de escalado con un nombre DNS personalizado para máquinas virtuales mediante CLI 2.0, agregue el argumento **--vm-domain-name** al comando **vmss create**, seguido de una cadena que representa el nombre de dominio.

Para establecer el nombre de dominio en una plantilla de Azure, agregue una propiedad **dnsSettings** a la sección de **networkInterfaceConfigurations** del conjunto de escalado. Por ejemplo: 

```json
"networkProfile": {
  "networkInterfaceConfigurations": [
    {
    "name": "nic1",
    "properties": {
      "primary": "true",
      "ipConfigurations": [
      {
        "name": "ip1",
        "properties": {
          "subnet": {
            "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
          },
          "publicIPAddressconfiguration": {
            "name": "publicip",
            "properties": {
            "idleTimeoutInMinutes": 10,
              "dnsSettings": {
                "domainNameLabel": "[parameters('vmssDnsName')]"
              }
            }
          }
        }
      }
    ]
    }
}
```

El resultado, para un nombre DNS de máquina virtual individual, tendría la forma siguiente: 
```
<vm><vmindex>.<specifiedVmssDomainNameLabel>
```

## <a name="public-ipv4-per-virtual-machine"></a>Dirección IPv4 pública por máquina virtual
En general, las máquinas virtuales del conjunto de escalado de Azure no requieren direcciones IP públicas propias. En la mayoría de los escenarios, resulta más económico y seguro asociar una dirección IP pública a un equilibrador de carga o a una máquina virtual individual (también conocida como jumpbox), que luego enruta las conexiones entrantes a máquinas virtuales del conjunto de escalado según sea necesario (por ejemplo, mediante reglas NAT de entrada).

Sin embargo, algunos escenarios requieren que las máquinas virtuales del conjunto de escalado tengan sus propias direcciones IP públicas. Un ejemplo son los juegos, en los que se necesita una consola para tener una conexión directa a una máquina virtual en la nube, que esté realizando el procesamiento físico de los juegos. Otro ejemplo es cuando las máquinas virtuales necesitan realizar conexiones externas entre sí a través de regiones en una base de datos distribuida.

### <a name="creating-a-scale-set-with-public-ip-per-virtual-machine"></a>Creación de un conjunto de escalado con una dirección IP pública por máquina virtual
Para crear un conjunto de escalado que asigne una dirección IP pública a cada máquina virtual con CLI 2.0, agregue el parámetro **--public-ip-per-vm** al comando **vmss create**. 

Para crear un conjunto de escalado mediante una plantilla de Azure, asegúrese de que la versión de API del recurso Microsoft.Compute/virtualMachineScaleSets sea al menos **2017-03-30** y agregue una propiedad JSON **publicIpAddressConfiguration** a la sección ipConfigurations del conjunto de escalado. Por ejemplo: 

```json
"publicIpAddressConfiguration": {
    "name": "pub1",
    "properties": {
      "idleTimeoutInMinutes": 15
    }
}
```
Ejemplo de plantilla: [201-vmss-public-ip-linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-public-ip-linux)

### <a name="querying-the-public-ip-addresses-of-the-virtual-machines-in-a-scale-set"></a>Consulta de las direcciones IP públicas de las máquinas virtuales en un conjunto de escalado
Para enumerar las direcciones IP públicas asignadas para el conjunto de escalado de máquinas virtuales mediante CLI 2.0, use el comando **az vmss list-instance-public-ips**.

Para mostrar las direcciones IP públicas del conjunto de escalado con PowerShell, use el comando _AzureRmPublicIpAddress_. Por ejemplo: 
```PowerShell
PS C:\> Get-AzureRmPublicIpAddress -ResourceGroupName myrg -VirtualMachineScaleSetName myvmss
```

También puede consultar las direcciones IP públicas haciendo referencia directamente al identificador de recurso de la configuración de la dirección IP pública. Por ejemplo: 
```PowerShell
PS C:\> Get-AzureRmPublicIpAddress -ResourceGroupName myrg -Name myvmsspip
```

Para consultar las direcciones IP públicas asignadas a las máquinas virtuales del conjunto de escalado mediante [Azure Resource Explorer](https://resources.azure.com) o la API de REST de Azure con la versión **2017-03-30** o superior.

Para ver las direcciones IP públicas para un conjunto de escalado mediante Resource Explorer, examine la sección **publicipaddresses** en su conjunto de escalado. Por ejemplo: https://resources.azure.com/subscriptions/_your_sub_id_/resourceGroups/_your_rg_/providers/Microsoft.Compute/virtualMachineScaleSets/_your_vmss_/publicipaddresses

```
GET https://management.azure.com/subscriptions/{your sub ID}/resourceGroups/{RG name}/providers/Microsoft.Compute/virtualMachineScaleSets/{scale set name}/publicipaddresses?api-version=2017-03-30
```

Salida de ejemplo:
```json
{
  "value": [
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/pipvmss/virtualMachines/0/networkInterfaces/pipvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"a64060d5-4dea-4379-a11d-b23cd49a3c8d\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "ee8cb20f-af8e-4cd6-892f-441ae2bf701f",
        "ipAddress": "13.84.190.11",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/0/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    },
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"5f6ff30c-a24c-4818-883c-61ebd5f9eee8\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "036ce266-403f-41bd-8578-d446d7397c2f",
        "ipAddress": "13.84.159.176",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    }
```

## <a name="multiple-ip-addresses-per-nic"></a>Varias direcciones IP por NIC
Cada NIC asociada a una VM en un conjunto de escalado puede tener una o varias configuraciones de IP asociadas con ella. Se asigna a cada configuración una dirección IP privada. Cada configuración también puede tener un recurso de dirección IP pública asociado con ella. Para saber cuántas direcciones IP se pueden asignar a una NIC y cuántas direcciones IP públicas se pueden usar en una suscripción de Azure, consulte los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="multiple-nics-per-virtual-machine"></a>Varias NIC por máquina virtual
Puede tener hasta 8 NIC por máquina virtual, según el tamaño de la máquina. El número máximo de NIC por máquina está disponible en el artículo sobre [Tamaños de las máquinas virtuales](../virtual-machines/windows/sizes.md). El ejemplo siguiente es un perfil de red del conjunto de escalado que muestra varias entradas NIC y varias direcciones IP públicas por máquina virtual:
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
        "name": "nic1",
        "properties": {
            "primary": "true",
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        },
        {
        "name": "nic2",
        "properties": {
            "primary": "false",
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        }
    ]
}
```

## <a name="nsg-per-scale-set"></a>NSG por conjunto de escalado
Los grupos de seguridad de red se pueden aplicar directamente a un conjunto de escalado agregando una referencia a la sección de configuración de la interfaz de red de las propiedades de máquinas virtuales del conjunto de escalado.

Por ejemplo:  
```
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            }
                "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>pasos siguientes
Para más información acerca de redes virtuales, consulte la [Introducción a Azure Virtual Network](../virtual-network/virtual-networks-overview.md).
