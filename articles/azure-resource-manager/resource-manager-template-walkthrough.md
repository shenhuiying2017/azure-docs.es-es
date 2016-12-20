---
title: Tutorial de la plantilla de Resource Manager | Microsoft Docs
description: "Un tutorial paso a paso de la plantilla de Resource Manager que aprovisiona una arquitectura básica de Azure IaaS."
services: azure-resource-manager
documentationcenter: na
author: navalev
manager: timlt
editor: 
ms.assetid: f1cfd704-f6e1-47d5-8094-b439c279c13f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2016
ms.author: navale;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 40687046c832ca39e460f1d7f55f7c188b6242b3
ms.openlocfilehash: 0479a970e2c3b8abaa9305de37a79e2b4128932e


---
# <a name="resource-manager-template-walkthrough"></a>Tutorial de la plantilla de Resource Manager
Una de las primeras preguntas que se plantean al crear una plantilla es "¿Cómo empezar?". Se puede empezar con una plantilla en blanco, siguiendo la estructura básica descrita en el [artículo sobre creación de plantillas](resource-group-authoring-templates.md#template-format), y agregar los recursos, además de los parámetros y las variables adecuados. Una buena alternativa sería empezar explorando la [galería de inicio rápido](https://github.com/Azure/azure-quickstart-templates) y buscar escenarios similares al que está intentando crear. Puede combinar varias plantillas o modificar una existente para adaptarla a su propio escenario específico. 

Echemos un vistazo a una infraestructura común:

* Dos máquinas virtuales que usan la misma cuenta de almacenamiento, se encuentran en el mismo conjunto de disponibilidad y en la misma subred de una red virtual.
* Una sola NIC y dirección IP de máquina virtual para cada máquina virtual.
* Un equilibrador de carga con reglas de equilibrio de carga en el puerto 80

![arquitectura](./media/resource-manager-template-walkthrough/arm_arch.png)

Este tema lo guiará por los pasos para crear una plantilla de Resource Manager para esa infraestructura. La plantilla final que cree se basa en una plantilla de inicio rápido llamada [2 VMs in a Load Balancer and load balancing rules](https://azure.microsoft.com/documentation/templates/201-2-vms-loadbalancer-lbrules/)(Dos máquinas virtuales en un equilibrador de carga y reglas de equilibrio de carga).

Pero hacer todo eso a la vez es mucho, así que empecemos creando una cuenta de almacenamiento e implementándola. Cuando domine la creación de la cuenta de almacenamiento, agregará los otros recursos y volverá a implementar la plantilla para completar la infraestructura.

> [!NOTE]
> Puede usar cualquier tipo de editor para crear la plantilla. Visual Studio proporciona herramientas que simplifican el desarrollo de la plantilla, pero no es necesario Visual Studio para completar este tutorial. Para ver un tutorial sobre cómo usar Visual Studio para crear una implementación de aplicación web y Base de datos SQL, consulte [Creación e implementación de grupos de recursos de Azure mediante Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). 
> 
> 

## <a name="create-the-resource-manager-template"></a>Creación de la plantilla de Resource Manager
La plantilla es un archivo JSON que define todos los recursos que se van a implementar. También permite definir los parámetros que se especifican durante la implementación, las variables que se construyen a partir de otros valores y expresiones, y las salidas de la implementación. 

Comencemos con la plantilla más sencilla:

```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {  },
      "variables": {  },
      "resources": [  ],
      "outputs": {  }
    }
 ```

Guarde este archivo como **azuredeploy.json** (tenga en cuenta que la plantilla se puede llamar como desee, aunque debe ser un archivo json).

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento
En la sección **resources** , agregue un objeto que defina la cuenta de almacenamiento, como se muestra a continuación. 

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "accountType": "Standard_LRS"
    }
  }
]
```

Quizás se pregunte de dónde proceden estas propiedades y estos valores. Las propiedades **type**, **name**, **apiVersion** y **location** son elementos estándar que están disponibles para todos los tipos de recursos. Puede aprender sobre los elementos comunes en [Recursos](resource-group-authoring-templates.md#resources). **name** se establece en un valor de parámetro que se pasa durante la implementación y **location** como la ubicación que el grupo de recursos usa. Veremos cómo se determinan **type** y **apiVersion** en las secciones siguientes.

La sección **properties** contiene todas las propiedades que son exclusivas de un tipo de recurso en particular. Los valores que especifique en esta sección coinciden exactamente con la operación PUT en la API de REST para crear ese tipo de recurso. Al crear una cuenta de almacenamiento, debe proporcionar un valor para **accountType**. En la [API de REST para crear una cuenta de almacenamiento](https://msdn.microsoft.com/library/azure/mt163564.aspx) , observe que la sección properties de la operación de REST también contiene una propiedad **accountType** y los valores permitidos están documentados. En este ejemplo, el tipo de cuenta se establece en **Standard_LRS**, pero se puede especificar otro valor o permitir que los usuarios pasen el tipo de cuenta como parámetro.

Ahora volvamos a la sección **parameters** y veamos cómo se define el nombre de la cuenta de almacenamiento. Puede aprender más sobre el uso de parámetros en [Parámetros](resource-group-authoring-templates.md#parameters). 

```json
"parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
}
```
Aquí se ha definido un parámetro de tipo string que va a contener el nombre de la cuenta de almacenamiento. Se proporcionará el valor para este parámetro durante la implementación de la plantilla.

## <a name="deploying-the-template"></a>Implementación de la plantilla
Tenemos una plantilla completa para crear una cuenta de almacenamiento. Como recordará, la plantilla se guardó en un archivo **azuredeploy.json**:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
  },  
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('storageAccountName')]",
      "apiVersion": "2015-06-15",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    }
  ]
}
```

Hay bastantes maneras de implementar una plantilla, como puede ver en el [artículo Implementación de recursos con plantillas de Azure Resource Manager](resource-group-template-deploy.md). Para implementar la plantilla mediante Azure PowerShell, use:

```powershell
# create a new resource group
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West Europe"

# deploy the template to the resource group
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile azuredeploy.json
```

O bien, para implementarla mediante la CLI de Azure, use:

```azurecli
azure group create -n ExampleResourceGroup -l "West Europe"

azure group deployment create -f azuredeploy.json -g ExampleResourceGroup -n ExampleDeployment
```

Ahora ya dispone de una cuenta de almacenamiento.

Los pasos siguientes consistirán en agregar todos los recursos necesarios para implementar la arquitectura descrita al comienzo del tutorial. Estos recursos se agregarán a la plantilla en la que ha estado trabajando.

## <a name="availability-set"></a>Conjunto de disponibilidad
Después de la definición de la cuenta de almacenamiento, agregue un conjunto de disponibilidad establecido para las máquinas virtuales. En este caso, no se necesitan propiedades adicionales, por lo que su definición es bastante simple. Consulte la sección properties completa en la [API de REST para crear un conjunto de disponibilidad](https://msdn.microsoft.com/library/azure/mt163607.aspx) si quiere definir el número de dominios de actualización y de dominios de error.

```json
{
   "type": "Microsoft.Compute/availabilitySets",
   "name": "[variables('availabilitySetName')]",
   "apiVersion": "2015-06-15",
   "location": "[resourceGroup().location]",
   "properties": {}
}
```

Observe que **name** se establece en el valor de una variable. Esta plantilla, el nombre del conjunto de disponibilidad se necesita en diferentes lugares. Para mantener más fácilmente la plantilla, defina ese valor una vez y úselo en diversos lugares.

El valor especificado para **type** contiene el proveedor de recursos y el tipo de recurso. En el caso de los conjuntos de disponibilidad, el proveedor de recursos es **Microsoft.Compute** y el tipo de recurso es **availabilitySets**. Para obtener la lista de proveedores de recursos disponible, ejecute el siguiente comando de PowerShell:

```powershell
    Get-AzureRmResourceProvider -ListAvailable
```

O bien, si usa la CLI de Azure, puede ejecutar el comando siguiente:

```azurecli
    azure provider list
```
Dado que en este tema se está creando con cuentas de almacenamiento, máquinas virtuales y redes virtuales, deberá trabajar con:

* Microsoft.Storage
* Microsoft.Compute
* Microsoft.Network

Para ver los tipos de recursos de un proveedor determinado, ejecute el siguiente comando de PowerShell:

```powershell
    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute).ResourceTypes
```

O bien, para la CLI de Azure, el siguiente comando devolverá los tipos disponibles en formato JSON y los guardará en un archivo.

```azurecli
    azure provider show Microsoft.Compute --json > c:\temp.json
```

Debería ver **availabilitySets** como uno de los tipos en **Microsoft.Compute**. El nombre completo del tipo es **Microsoft.Compute/availabilitySets**. Puede determinar el nombre del tipo de recurso para cualquiera de los recursos de plantilla.

## <a name="public-ip"></a>Dirección IP pública
Defina una dirección IP pública. Vuelva a consultar la [API de REST para direcciones IP públicas](https://msdn.microsoft.com/library/azure/mt163590.aspx) para ver las propiedades que se deben establecer.

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[parameters('publicIPAddressName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('dnsNameforLBIP')]"
    }
  }
}
```

El método de asignación se establece en **Dynamic** pero puede establecerlo en el valor que necesite o configurarlo para que acepte un valor de parámetro. Se permite que los usuarios de la plantilla pasen un valor para la etiqueta de nombre de dominio.

Ahora, echemos un vistazo para ver cómo determinar el valor de **apiVersion**. El valor que especifique coincide con la versión de la API de REST que se usará al crear el recurso. Por lo tanto, puede consultar la documentación de la API de REST de ese tipo de recurso. O bien, puede ejecutar el siguiente comando de PowerShell para un tipo determinado.

```powershell
    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Network).ResourceTypes | Where-Object ResourceTypeName -eq publicIPAddresses).ApiVersions
```
Que devuelve los siguientes valores:

    2015-06-15
    2015-05-01-preview
    2014-12-01-preview

Para ver las versiones de la API con la CLI de Azure, ejecute el mismo comando **azure provider show** mostrado antes.

Al crear una nueva plantilla, seleccione la versión más reciente de la API.

## <a name="virtual-network-and-subnet"></a>Red virtual y subred
Cree una red virtual con una subred. Consulte la [API de REST para redes virtuales](https://msdn.microsoft.com/library/azure/mt163661.aspx) para ver todas las propiedades que se deben establecer.

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/virtualNetworks",
   "name": "[parameters('vnetName')]",
   "location": "[resourceGroup().location]",
   "properties": {
     "addressSpace": {
       "addressPrefixes": [
         "10.0.0.0/16"
       ]
     },
     "subnets": [
       {
         "name": "[variables('subnetName')]",
         "properties": {
           "addressPrefix": "10.0.0.0/24"
         }
       }
     ]
   }
}
```

## <a name="load-balancer"></a>Equilibrador de carga
Ahora va a crear un equilibrador de carga accesible desde el exterior. Dado que este equilibrador de carga usa la dirección IP pública, debe declarar una dependencia de la dirección IP pública en la sección **dependsOn** . Esto significa que el equilibrador de carga no se implementará hasta que finalice la implementación de la dirección IP pública. Sin definir esta dependencia, recibirá un error porque Resource Manager intentará implementar los recursos en paralelo y se intentará establecer el equilibrador de carga en la dirección IP pública que todavía no existe. 

En esta definición de recursos, también creará un grupo de direcciones de back-end, un par de reglas NAT entrantes a RDP en las máquinas virtuales y una regla de equilibrio de carga con un sondeo tcp en el puerto 80. Consulte la [API de REST de equilibrador de carga](https://msdn.microsoft.com/library/azure/mt163574.aspx) para ver todas las propiedades.

```json
{
   "apiVersion": "2015-06-15",
   "name": "[parameters('lbName')]",
   "type": "Microsoft.Network/loadBalancers",
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
   ],
   "properties": {
     "frontendIPConfigurations": [
       {
         "name": "LoadBalancerFrontEnd",
         "properties": {
           "publicIPAddress": {
             "id": "[variables('publicIPAddressID')]"
           }
         }
       }
     ],
     "backendAddressPools": [
       {
         "name": "BackendPool1"
       }
     ],
     "inboundNatRules": [
       {
         "name": "RDP-VM0",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50001,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       },
       {
         "name": "RDP-VM1",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50002,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       }
     ],
     "loadBalancingRules": [
       {
         "name": "LBRule",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "backendAddressPool": {
             "id": "[variables('lbPoolID')]"
           },
           "protocol": "tcp",
           "frontendPort": 80,
           "backendPort": 80,
           "enableFloatingIP": false,
           "idleTimeoutInMinutes": 5,
           "probe": {
             "id": "[variables('lbProbeID')]"
           }
         }
       }
     ],
     "probes": [
       {
         "name": "tcpProbe",
         "properties": {
           "protocol": "tcp",
           "port": 80,
           "intervalInSeconds": 5,
           "numberOfProbes": 2
         }
       }
     ]
   }
}
```

## <a name="network-interface"></a>Interfaz de red
Creará dos interfaces de red, una para cada máquina virtual. En lugar de tener que incluir entradas duplicadas para las interfaces de red, puede usar la [función copyIndex()](resource-group-create-multiple.md) para iterar por el bucle de copia (denominado nicLoop) y crear el número de interfaces de red definido en las variables `numberOfInstances`. La interfaz de red depende de la creación de la red virtual y el equilibrador de carga. Usa la subred definida en la creación de la red virtual y el identificador de equilibrador de carga para configurar el grupo de direcciones del equilibrador de carga y las reglas NAT entrantes.
Consulte la [API de REST para interfaces de red](https://msdn.microsoft.com/library/azure/mt163668.aspx) para ver todas las propiedades.

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/networkInterfaces",
   "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
   "location": "[resourceGroup().location]",
   "copy": {
     "name": "nicLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "dependsOn": [
     "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
     "[concat('Microsoft.Network/loadBalancers/', parameters('lbName'))]"
   ],
   "properties": {
     "ipConfigurations": [
       {
         "name": "ipconfig1",
         "properties": {
           "privateIPAllocationMethod": "Dynamic",
           "subnet": {
             "id": "[variables('subnetRef')]"
           },
           "loadBalancerBackendAddressPools": [
             {
               "id": "[concat(variables('lbID'), '/backendAddressPools/BackendPool1')]"
             }
           ],
           "loadBalancerInboundNatRules": [
             {
               "id": "[concat(variables('lbID'),'/inboundNatRules/RDP-VM', copyindex())]"
             }
           ]
         }
       }
     ]
   }
}
```

## <a name="virtual-machine"></a>Máquina virtual
Aprenderá a crear dos máquinas virtuales con la función copyIndex(), como hizo al crear las [interfaces de red](#network-interface).
La creación de la máquina virtual depende de la cuenta de almacenamiento, la interfaz de red y el conjunto de disponibilidad. Esta máquina virtual se creará a partir de una imagen de Marketplace, tal y como se define en la propiedad `storageProfile`. `imageReference` se usa para definir el editor de la imagen, la oferta, la SKU y la versión. Por último, se configura un perfil de diagnóstico para habilitar los diagnósticos para la máquina virtual. 

Para buscar las propiedades pertinentes para una imagen de Marketplace, consulte los artículos [Navegación y selección de las imágenes de máquina virtual Linux en Azure con CLI o PowerShell](../virtual-machines/virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o [Navegación y selección de las imágenes de máquina virtual Windows en Azure con PowerShell o CLI](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Compute/virtualMachines",
   "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
   "copy": {
     "name": "virtualMachineLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
     "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
     "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
   ],
   "properties": {
     "availabilitySet": {
       "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
     },
     "hardwareProfile": {
       "vmSize": "[parameters('vmSize')]"
     },
     "osProfile": {
       "computerName": "[concat(parameters('vmNamePrefix'), copyIndex())]",
       "adminUsername": "[parameters('adminUsername')]",
       "adminPassword": "[parameters('adminPassword')]"
     },
     "storageProfile": {
       "imageReference": {
         "publisher": "[parameters('imagePublisher')]",
         "offer": "[parameters('imageOffer')]",
         "sku": "[parameters('imageSKU')]",
         "version": "latest"
       },
       "osDisk": {
         "name": "osdisk",
         "vhd": {
           "uri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/vhds/','osdisk', copyindex(), '.vhd')]"
         },
         "caching": "ReadWrite",
         "createOption": "FromImage"
       }
     },
     "networkProfile": {
       "networkInterfaces": [
         {
           "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
         }
       ]
     },
     "diagnosticsProfile": {
       "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net')]"
       }
     }
}
```

> [!NOTE]
> Para las imágenes publicadas por **otros proveedores**, deberá especificar otra propiedad llamada `plan`. Encontrará un ejemplo en [esta plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/checkpoint-single-nic) en la galería de inicio rápido. 
> 
> 

Ha terminado de definir los recursos de la plantilla.

## <a name="parameters"></a>parameters
En la sección parameters, defina los valores que pueden especificarse al implementar la plantilla. Defina solo los parámetros para los valores que cree deben variar durante la implementación. Puede proporcionar un valor predeterminado para un parámetro que se usa si no se proporciona durante la implementación. También puede definir los valores permitidos tal y como se muestra para el parámetro **imageSKU** .

```json
"parameters": {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of storage account"
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Admin username"
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Admin password"
      }
    },
    "dnsNameforLBIP": {
      "type": "string",
      "metadata": {
        "description": "DNS for Load Balancer IP"
      }
    },
    "vmNamePrefix": {
      "type": "string",
      "defaultValue": "myVM",
      "metadata": {
        "description": "Prefix to use for VM names"
      }
    },
    "imagePublisher": {
      "type": "string",
      "defaultValue": "MicrosoftWindowsServer",
      "metadata": {
        "description": "Image Publisher"
      }
    },
    "imageOffer": {
      "type": "string",
      "defaultValue": "WindowsServer",
      "metadata": {
        "description": "Image Offer"
      }
    },
    "imageSKU": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter"
      ],
      "metadata": {
        "description": "Image SKU"
      }
    },
    "lbName": {
      "type": "string",
      "defaultValue": "myLB",
      "metadata": {
        "description": "Load Balancer name"
      }
    },
    "nicNamePrefix": {
      "type": "string",
      "defaultValue": "nic",
      "metadata": {
        "description": "Network Interface name prefix"
      }
    },
    "publicIPAddressName": {
      "type": "string",
      "defaultValue": "myPublicIP",
      "metadata": {
        "description": "Public IP Name"
      }
    },
    "vnetName": {
      "type": "string",
      "defaultValue": "myVNET",
      "metadata": {
        "description": "VNET name"
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D1",
      "metadata": {
        "description": "Size of the VM"
      }
    }
  }
```

## <a name="variables"></a>Variables
En la sección variables, puede definir los valores que se usan en más de un lugar en la plantilla, o los valores que se construyen a partir de otras expresiones o variables. Las variables se usan con frecuencia para simplificar la sintaxis de la plantilla.

```json
"variables": {
    "availabilitySetName": "myAvSet",
    "subnetName": "Subnet-1",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
    "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
    "numberOfInstances": 2,
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LoadBalancerFrontEnd')]",
    "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/BackendPool1')]",
    "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
  }
```

Ha completado la plantilla. Puede comparar su plantilla con la completa en la [galería de inicio rápido](https://github.com/Azure/azure-quickstart-templates) en la [plantilla para dos máquinas virtuales con equilibrador de carga y reglas de equilibrador de carga](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules). Su plantilla puede variar ligeramente porque se usen números de versión diferentes. 

Para volver a implementar la plantilla, use los mismos comandos que utilizó al implementar la cuenta de almacenamiento. No es necesario eliminar la cuenta de almacenamiento antes de volver a implementar porque Resource Manager no volverá a crear los recursos que ya existan y que no hayan cambiado.

## <a name="next-steps"></a>Pasos siguientes
* [Azure Resource Manager Template Visualizer](http://armviz.io/#/) (Visualizador de plantillas de Azure Resource Manager) es una excelente herramienta para visualizar las plantillas de Resource Manager cuando crezcan tanto que sea complicado comprenderlas solamente con la lectura del archivo json.
* Para aprender más sobre la estructura de una plantilla, consulte [Creación de plantillas de Azure Resource Manager](resource-group-authoring-templates.md).
* Para aprender a implementar una plantilla, consulte [Implementación de recursos con plantillas de Azure Resource Manager](resource-group-template-deploy.md)
* Para obtener una serie de cuatro partes acerca de cómo automatizar la implementación, vea [Automatización de implementaciones de aplicaciones en Azure Virtual Machines](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Esta serie cubre la arquitectura de la aplicación, el acceso y la seguridad, la disponibilidad y la escala, y la implementación de aplicaciones.




<!--HONumber=Dec16_HO1-->


