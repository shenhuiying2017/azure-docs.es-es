---
title: "Implementación de recursos de proceso Linux con plantillas de Azure Resource Manager | Microsoft Docs"
description: "Tutorial de DotNet Core para máquinas virtuales de Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 1c4d419e-ba0e-45e4-a9dd-7ee9975a86f9
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2016
ms.author: nepeters
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 1fd905b30c6624a21ed90ea75633d7ca1ea474a9
ms.lasthandoff: 04/03/2017


---
# <a name="application-architecture-with-azure-resource-manager-templates-for-linux-vms"></a>Arquitectura de aplicaciones con plantillas de Azure Resource Manager para máquinas virtuales Linux

Al desarrollar una implementación de Azure Resource Manager, los requisitos de procesos deben asignarse a servicios y recursos de Azure. Si una aplicación consta de puntos de conexión HTTP, una base de datos y un servicio de almacenamiento en caché de datos, los recursos de Azure que hospedan cada uno de estos componentes deben racionalizarse. Por ejemplo, la aplicación Music Store de ejemplo incluye una aplicación web que se hospeda en una máquina virtual y una base de datos SQL que se hospeda en la base de datos SQL de Azure. 

Este documento describe cómo se configuran los recursos de procesos de Music Store en la plantilla de Azure Resource Manager de ejemplo. Se resaltan todas las dependencias y configuraciones únicas. Para obtener la mejor experiencia, realice una implementación previa de una instancia de la solución en su suscripción de Azure y trabaje con la plantilla de Azure Resource Manager. La plantilla completa se puede encontrar aquí: [Music Store Deployment on Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)(Implementación de Music Store en Ubuntu). 

## <a name="virtual-machine"></a>Máquina virtual
La aplicación Music Store incluye una aplicación web donde los clientes pueden buscar y comprar música. Aunque hay varios servicios de Azure que pueden hospedar aplicaciones web, en este ejemplo se utiliza una máquina virtual. Con la plantilla de Music Store de ejemplo, se implementa una máquina virtual, se instala un servidor web, y se instala y configura el sitio web de Music Store. Este artículo se centra únicamente en la implementación de la máquina virtual. La configuración del servidor web y la aplicación se detalla en un artículo posterior.

Se puede agregar una máquina virtual a una plantilla mediante el asistente Agregar nuevo recurso de Visual Studio o insertando un recurso JSON válido en la plantilla de implementación. Al implementar una máquina virtual, también se necesitan varios recursos relacionados. Si utiliza Visual Studio para crear la plantilla, estos recursos se crean automáticamente. Si la plantilla se crea manualmente, estos recursos deben insertarse y configurarse.

Siga este vínculo para ver el ejemplo de JSON en la plantilla de Resource Manager: [Virtual Machine JSON](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L295)(JSON de máquina virtual).

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat(variables('vmName'),copyindex())]",
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "virtualMachineLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "tags": {
    "displayName": "virtual-machine"
  },
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('vhdStorageName'))]",
    "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]",
    "nicLoop"
  ],
  "properties": {
    "availabilitySet": {
      "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
    },
      ........<truncated>  
    }
```

Una vez implementada, las propiedades de la máquina virtual pueden verse en Azure Portal.

![Máquina virtual](./media/dotnet-core-2-architecture/vm.png)

## <a name="storage-account"></a>Cuenta de almacenamiento
Las cuentas de almacenamiento tienen numerosas funcionalidades y opciones de almacenamiento. En el contexto de las máquinas virtuales de Azure, una cuenta de almacenamiento contiene los discos duros virtuales de la máquina virtual y todos los discos de datos adicionales. El ejemplo de Music Store incluye una cuenta de almacenamiento que contiene la unidad de disco duro virtual de cada máquina virtual de la implementación. 

Siga este vínculo para ver el ejemplo de JSON en la plantilla de Resource Manager: [Storage Account](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L109)(Cuenta de almacenamiento).

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[variables('vhdStorageName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "storage-account"
  },
  "properties": {
    "accountType": "[variables('vhdStorageType')]"
  }
}
```

Una cuenta de almacenamiento se asocia a una máquina virtual dentro de la declaración de plantilla de Resource Manager de la máquina virtual. 

Siga este vínculo para ver el ejemplo de JSON en la plantilla de Resource Manager: [Virtual Machine and Storage Account association](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L341)(Asociación de cuenta de almacenamiento con una máquina virtual).

```json
"osDisk": {
  "name": "osdisk",
  "vhd": {
    "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/',variables('vhdStorageName')), '2015-06-15').primaryEndpoints.blob,'vhds/osdisk', copyindex(), '.vhd')]"
  },
  "caching": "ReadWrite",
  "createOption": "FromImage"
}
```

Después de la implementación, la cuenta de almacenamiento puede verse en Azure Portal.

![Cuenta de almacenamiento](./media/dotnet-core-2-architecture/storacct.png)

Si hace clic en el contenedor de blobs de la cuenta de almacenamiento, puede ver el archivo de unidad de disco duro virtual para cada máquina virtual implementada con la plantilla.

![Unidades de disco duro virtuales](./media/dotnet-core-2-architecture/vhd.png)

Para más información sobre Azure Storage, consulte [Documentación de Almacenamiento](https://azure.microsoft.com/documentation/services/storage/).

## <a name="virtual-network"></a>Red virtual
Si una máquina virtual requiere una conexión en red interna, como la capacidad de comunicarse con otras máquinas virtuales y recursos de Azure, se requiere Azure Virtual Network.  Una red virtual no permite el acceso a la máquina virtual a través de Internet. La conectividad pública requiere una dirección IP pública, que se detalla más adelante en esta serie.

Siga este vínculo para ver el ejemplo de JSON en la plantilla de Resource Manager: [Virtual Network and Subnets](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L136)(Red virtual y subredes).

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroup'))]"
  ],
  "tags": {
    "displayName": "virtual-network"
  },
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "10.0.0.0/24"
      ]
    },
    "subnets": [
      {
        "name": "[variables('subnetName')]",
        "properties": {
          "addressPrefix": "10.0.0.0/24",
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroup'))]"
          }
        }
      }
    ]
  }
}
```

En Azure Portal, la red virtual se asemeja a la siguiente imagen. Observe que todas las máquinas virtuales que se implementan con la plantilla están conectadas a la red virtual.

![Red virtual](./media/dotnet-core-2-architecture/vnet.png)

## <a name="network-interface"></a>Interfaz de red
 Una interfaz de red conecta una máquina virtual a una red virtual, en concreto a una subred que se ha definido en la red virtual. 

 Siga este vínculo para ver el ejemplo de JSON en la plantilla de Resource Manager: [Network Interface](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L166)(Interfaz de red).

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/networkInterfaces",
  "name": "[concat(variables('networkInterfaceNamePrefix'), copyindex())]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "network-interface"
  },
  "copy": {
    "name": "nicLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
    "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIpAddressName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'), '/inboundNatRules/', 'SSH-VM', copyIndex())]"
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
              "id": "[variables('lbPoolID')]"
            }
          ],
          "loadBalancerInboundNatRules": [
            {
              "id": "[concat(variables('lbID'),'/inboundNatRules/SSH-VM', copyIndex())]"
            }
          ]
        }
      }
    ]
  }
}
```

Cada recurso de la máquina virtual incluye un perfil de red. La interfaz de red está asociada a la máquina virtual de este perfil.  

Siga este vínculo para ver el ejemplo de JSON en la plantilla de Resource Manager: [Virtual Machine Network Profile](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L350)(Perfil de red de la máquina virtual).

```json
"networkProfile": {
  "networkInterfaces": [
    {
      "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('networkInterfaceNamePrefix'), copyindex()))]"
    }
  ]
}
```

En Azure Portal, la red virtual se asemeja a la siguiente imagen. La dirección IP interna y la asociación de la máquina virtual pueden verse en el recurso de la interfaz de red.

![Interfaz de red](./media/dotnet-core-2-architecture/nic.png)

Para más información acerca de Azure Virtual Network, consulte [Documentación de Red virtual](https://azure.microsoft.com/documentation/services/virtual-network/).

## <a name="azure-sql-database"></a>Base de datos SQL de Azure
Además de la máquina virtual que hospeda el sitio web de Music Store, se implementa Azure SQL Database para hospedar la base de datos de la tienda de música. La ventaja de utilizar Azure SQL Database aquí es que no es necesario un segundo conjunto de máquinas virtuales, además de que en el servicio se integran la disponibilidad y la escala.

Se puede agregar una base de datos SQL de Azure mediante el asistente Agregar nuevo recurso de Visual Studio o insertando un recurso JSON válido en una plantilla. El recurso de SQL Server incluye un nombre de usuario y una contraseña con derechos administrativos concedidos en la instancia de SQL. Además, se agrega un recurso de firewall de base de datos SQL. De forma predeterminada, las aplicaciones hospedadas en Azure pueden conectarse con la instancia de SQL. Para permitir que una aplicación externa, como SQL Server Management Studio, se conecte a la instancia de SQL, debe configurarse el firewall. Para esta demostración de Music Store, la configuración predeterminada es correcta. 

Siga este vínculo para ver el ejemplo de JSON en la plantilla de Resource Manager: [Azure SQL DB](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L401).

```json
{
  "apiVersion": "2014-04-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "[variables('musicStoreSqlName')]",
  "location": "[resourceGroup().location]",

  "dependsOn": [],
  "tags": {
    "displayName": "sql-music-store"
  },
  "properties": {
    "administratorLogin": "[parameters('adminUsername')]",
    "administratorLoginPassword": "[parameters('sqlAdminPassword')]"
  },
  "resources": [
    {
      "apiVersion": "2014-04-01-preview",
      "type": "firewallrules",
      "name": "firewall-allow-azure",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Sql/servers/', variables('musicStoreSqlName'))]"
      ],
      "properties": {
        "startIpAddress": "0.0.0.0",
        "endIpAddress": "0.0.0.0"
      }
    }
  ]
}
```

Vista del servidor SQL y la base de datos de Music Store tal como se muestran en Azure Portal.

![SQL Server](./media/dotnet-core-2-architecture/sql.png)

Para más información sobre la implementación de Azure SQL Database, consulte [Documentación de Base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/).

## <a name="next-step"></a>Paso siguiente
<hr>

[Paso 2: acceso y seguridad en plantillas de Azure Resource Manager](dotnet-core-3-access-security.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


