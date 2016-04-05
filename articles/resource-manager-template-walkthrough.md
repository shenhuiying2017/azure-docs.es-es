<properties
   pageTitle="Tutorial de la plantilla de Resource Manager | Microsoft Azure"
   description="Un tutorial paso a paso de la plantilla de Resource Manager que aprovisiona una arquitectura básica de Azure IaaS."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/29/2016"
   ms.author="navale;tomfitz"/>
   
# Tutorial de la plantilla de Azure Resource Manager

Este tema le guiará por los pasos para crear una plantilla de Resource Manager. Creará una plantilla basada en la [plantilla de dos máquinas virtuales con equilibrador de carga y reglas de equilibrio de carga](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules) en la [galería de inicio rápido](https://github.com/Azure/azure-quickstart-templates). Las técnicas que aprenda pueden aplicarse a cualquier plantilla que necesite crear.

Echemos un vistazo a una arquitectura común:

* Dos máquinas virtuales que usan la misma cuenta de almacenamiento, se encuentran en el mismo conjunto de disponibilidad y en la misma subred de una red virtual.
* Una sola NIC y dirección IP de máquina virtual para cada máquina virtual.
* Un equilibrador de carga con reglas de equilibrio de carga en el puerto 80

![arquitectura](./media/resource-group-overview/arm_arch.png)

Ha decidido que desea implementar esta arquitectura en Azure y desea usar plantillas de Resource Manager para volver a implementar fácilmente la arquitectura en otro momento; sin embargo, no está seguro de cómo crear esa plantilla. Este tema le ayudará a comprender qué incluir en la plantilla.

Puede usar cualquier tipo de editor para crear la plantilla. Visual Studio proporciona herramientas que simplifican el desarrollo de la plantilla, pero no es necesario Visual Studio para completar este tutorial. Para obtener un tutorial sobre cómo usar Visual Studio para crear una implementación de aplicación web y Base de datos SQL, consulte [Creación e implementación de grupos de recursos de Azure mediante Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## Creación de la plantilla de Resource Manager

La plantilla es un archivo JSON que define todos los recursos que se van a implementar. También permite definir los parámetros que se especifican durante la implementación, las variables que se construyen a partir de otros valores y expresiones, y las salidas de la implementación.

Comencemos con la plantilla más sencilla:

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {  },
      "variables": {  },
      "resources": [  ],
      "outputs": {  }
    }

Guarde este archivo como **azuredeploy.json**.

En primer lugar, nos centraremos en la sección **resources**, y veremos la información sobre **parameters** y **variables** más adelante en este tema.

## Cuenta de almacenamiento
Deberá definir la cuenta de almacenamiento que usarán las máquinas virtuales. En la sección **resources**, agregue un objeto que defina la cuenta de almacenamiento, tal y como se muestra a continuación.

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

Quizás se pregunte de dónde proceden estas propiedades y estos valores. Las propiedades **type**, **name**, **apiVersion** y **location** son elementos estándar que están disponibles para todos los tipos de recursos. Encontrará información sobre los elementos comunes en [Recursos](../resource-group-authoring-templates/#resources). Establezca **name** en un valor de parámetro que se pasa durante la implementación. Establezca **location** en la ubicación usada por el grupo de recursos. Veremos cómo se determinan **type** y **apiVersion** en las secciones siguientes.

La sección **properties** contiene todas las propiedades que son exclusivas de un tipo de recurso en particular. Los valores que especifique en esta sección coinciden exactamente con la operación PUT en la API de REST para crear ese tipo de recurso. Al crear una cuenta de almacenamiento, debe proporcionar un valor de **accountType**. En la [API de REST para crear una cuenta de almacenamiento](https://msdn.microsoft.com/library/azure/mt163564.aspx), observe que la sección properties de la operación de REST también contiene una propiedad **accountType** y los valores permitidos están documentados. En este ejemplo, el tipo de cuenta se establece en **Standard\_LRS**, pero se puede especificar otro valor o permitir que los usuarios pasen el tipo de cuenta como un parámetro.

## Conjunto de disponibilidad
Después de la definición de la cuenta de almacenamiento, agregue un conjunto de disponibilidad establecido para las máquinas virtuales. En este caso, no se necesitan propiedades adicionales, por lo que su definición es bastante simple. Consulte la [API de REST para crear un conjunto de disponibilidad](https://msdn.microsoft.com/library/azure/mt163607.aspx) para toda la sección properties si quiere definir el número de dominios de actualización y de dominios de error.

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

    PS C:\> Get-AzureRmResourceProvider -ListAvailable

O bien, si usa la CLI de Azure, puede ejecutar el comando siguiente:

    azure provider list

Dado que en este tema se está creando con cuentas de almacenamiento, máquinas virtuales y redes virtuales, deberá trabajar con:

- Microsoft.Storage
- Microsoft.Compute
- Microsoft.Network

Para ver los tipos de recursos de un proveedor determinado, ejecute el siguiente comando de PowerShell:

    PS C:\> (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute).ResourceTypes

O bien, para la CLI de Azure, el siguiente comando devolverá los tipos disponibles en formato JSON y los guardará en un archivo.

    azure provider show Microsoft.Compute --json > c:\temp.json

Debería ver **availabilitySets** como uno de los tipos en **Microsoft.Compute**. El nombre completo del tipo es **Microsoft.Compute/availabilitySets**. Puede determinar el nombre del tipo de recurso para cualquiera de los recursos de plantilla.

## Dirección IP pública
Defina una dirección IP pública. Vuelva a consultar [API de REST para direcciones IP públicas](https://msdn.microsoft.com/library/azure/mt163590.aspx) para ver las propiedades que se deben establecer.

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

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Network).ResourceTypes | Where-Object ResourceTypeName -eq publicIPAddresses).ApiVersions

Que devuelve los siguientes valores:

    2015-06-15
    2015-05-01-preview
    2014-12-01-preview

Para ver las versiones de la API con la CLI de Azure, ejecute el mismo comando **azure provider show** mostrado anteriormente.

Al crear una nueva plantilla, seleccione la versión más reciente de la API.

## Red virtual y subred
Cree una red virtual con una subred. Consulte [API de REST para redes virtuales](https://msdn.microsoft.com/library/azure/mt163661.aspx) para ver todas las propiedades que se deben establecer.

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

## Equilibrador de carga
Ahora va a crear un equilibrador de carga accesible desde el exterior. Dado que este equilibrador de carga usa la dirección IP pública, debe declarar una dependencia en la dirección IP pública en la sección **dependsOn**. Esto significa que el equilibrador de carga no se implementará hasta que finalice la implementación de la dirección IP pública. Sin definir esta dependencia, recibirá un error porque Resource Manager intentará implementar los recursos en paralelo y se intentará establecer el equilibrador de carga en la dirección IP pública que todavía no existe.

En esta definición de recursos, también creará un grupo de direcciones de back-end, un par de reglas NAT entrantes a RDP en las máquinas virtuales y una regla de equilibrio de carga con un sondeo tcp en el puerto 80. Consulte [API de REST de equilibrador de carga](https://msdn.microsoft.com/library/azure/mt163574.aspx) para ver todas las propiedades.

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

## Interfaz de red
Creará dos interfaces de red, una para cada máquina virtual. En lugar de tener que incluir entradas duplicadas para las interfaces de red, puede usar la [función copyIndex()](resource-group-create-multiple.md) para iterar por el bucle de copia (denominado nicLoop) y crear el número de interfaces de red definido en las variables `numberOfInstances`. La interfaz de red depende de la creación de la red virtual y el equilibrador de carga. Usa la subred definida en la creación de la red virtual y el identificador de equilibrador de carga para configurar el grupo de direcciones del equilibrador de carga y las reglas NAT entrantes. Consulte [API de REST para interfaces de red](https://msdn.microsoft.com/library/azure/mt163668.aspx) para ver todas las propiedades.

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

## Máquina virtual
Aprenderá a crear dos máquinas virtuales con la función copyIndex(), como hizo al crear las [interfaces de red](#network-interface). La creación de la máquina virtual depende de la cuenta de almacenamiento, la interfaz de red y el conjunto de disponibilidad. Esta máquina virtual se creará a partir de una imagen de Marketplace, tal y como se define en la propiedad `storageProfile`. `imageReference` se usa para definir el editor de la imagen, la oferta, la sku y la versión. Por último, se configura un perfil de diagnóstico para habilitar los diagnósticos para la máquina virtual.

Para buscar las propiedades pertinentes para una imagen de Marketplace, consulte los artículos [Select Linux virtual machine images](./virtual-machines/virtual-machines-linux-cli-ps-findimage.md) (Seleccionar imágenes de máquina virtual de Linux) o [Select Windows virtual machine images](./virtual-machines/virtual-machines-windows-cli-ps-findimage.md) (Seleccionar imágenes de máquina virtual de Windows). Para las imágenes publicadas por otros proveedores, deberá especificar otra propiedad llamada `plan`. Encontrará un ejemplo en [esta plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/checkpoint-single-nic) en la galería de inicio rápido.


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

Ha terminado de definir los recursos de la plantilla.

## Parámetros

En la sección parameters, defina los valores que pueden especificarse al implementar la plantilla. Defina solo los parámetros para los valores que cree deben variar durante la implementación. Puede proporcionar un valor predeterminado para un parámetro que se usa si no se proporciona durante la implementación. También puede definir los valores permitidos tal y como se muestra para el parámetro **imageSKU**.

```
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

## Variables

En la sección variables, puede definir los valores que se usan en más de un lugar en la plantilla, o los valores que se construyen a partir de otras expresiones o variables. Las variables se usan con frecuencia para simplificar la sintaxis de la plantilla.

```
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



## Pasos siguientes

Ha terminado de crear la plantilla y está listo para la implementación.

- Para más información sobre la estructura de una plantilla, consulte [Creación de plantillas de Azure Resource Manager](resource-group-authoring-templates.md).
- Para más información sobre cómo implementar una plantilla, consulte [Implementación de un grupo de recursos con la plantilla de Azure Resource Manager](resource-group-template-deploy.md)

<!---HONumber=AcomDC_0330_2016-->