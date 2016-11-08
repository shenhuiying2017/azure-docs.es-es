---
title: Acceso y seguridad en plantillas de Azure Resource Manager | Microsoft Docs
description: Tutorial de DotNet Core para máquinas virtuales de Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/21/2016
ms.author: nepeters

---
# <a name="access-and-security-in-azure-resource-manager-templates"></a>Acceso y seguridad en plantillas de Azure Resource Manager
Por lo general, para acceder a las aplicaciones hospedadas en Azure, se requiere una conexión a Internet o una conexión VPN/ExpressRoute con Azure. En el ejemplo de la aplicación Music Store, el sitio web pasa a estar disponible en Internet con una dirección IP pública. Una vez establecido el acceso, se deben proteger las conexiones a la aplicación y el acceso a los propios recursos de la máquina virtual. Esta acceso protegido se proporciona mediante un grupo de seguridad de red. 

Este documento describe cómo se protege la aplicación Music Store en la plantilla de Azure Resource Manager de ejemplo. Se resaltan todas las dependencias y configuraciones únicas. Para obtener la mejor experiencia, realice una implementación previa de una instancia de la solución en su suscripción de Azure y trabaje con la plantilla de Azure Resource Manager. La plantilla completa se puede encontrar aquí: [Music Store Deployment on Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)(Implementación de Music Store en Ubuntu).

## <a name="public-ip-address"></a>Dirección IP pública
Para proporcionar acceso público a un recurso de Azure, se puede usar un recurso de dirección IP pública. La dirección IP pública puede configurarse con una dirección IP estática o dinámica. Si se usa una dirección dinámica y la máquina virtual se detiene y se desasigna, se eliminarán las direcciones. Al volver a iniciar la máquina, se le puede asignar una dirección IP pública diferente. Para impedir que una dirección IP cambie, se puede usar una dirección IP reservada. 

Se puede agregar una dirección IP pública a una plantilla de Azure Resource Manager mediante el asistente Agregar nuevo recurso de Visual Studio o insertando un recurso JSON válido en una plantilla. 

Siga este vínculo para ver el ejemplo de JSON en la plantilla de Resource Manager: [Public IP Address](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L121)(Dirección IP pública).

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[variables('publicipaddressName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "public-ip-front"
  },
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('publicipaddressDnsName')]"
    }
  }
}
```

Una dirección IP pública puede asociarse con un adaptador de red virtual o un equilibrador de carga. En este ejemplo, dado que la carga del sitio web de Music Store se equilibra entre varias máquinas virtuales, la dirección IP pública se asocia al equilibrador de carga.

Siga este vínculo para ver el ejemplo de JSON en la plantilla de Resource Manager: [Public IP Address association with Load Balancer](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L208)(Asociación de dirección IP pública con equilibrador de carga).

```none
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicipaddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
]
```

La dirección IP pública como se muestra en Azure Portal. Observe que la dirección IP pública está asociada a un equilibrador de carga y no a una máquina virtual. En el siguiente documento de esta serie, se describen los equilibradores de carga de red.

![Dirección IP pública](./media/virtual-machines-linux-dotnet-core/pubip.png)

Para más información sobre direcciones IP públicas de Azure, consulte [Direcciones IP en Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).

## <a name="network-security-group"></a>Grupo de seguridad de red (NSG)
Cuando se haya establecido el acceso a recursos de Azure, este acceso debe limitarse. En máquinas virtuales de Azure, el acceso se protege mediante un grupo de seguridad de red. En el ejemplo de la aplicación Music Store, todo el acceso a la máquina virtual está restringido, excepto en el puerto 80 para el acceso HTTP y el puerto 22 para el acceso SSH. Se puede agregar un grupo de seguridad de red a una plantilla de Azure Resource Manager mediante el asistente Agregar nuevo recurso de Visual Studio o insertando un recurso JSON válido en una plantilla.

Siga este vínculo para ver el ejemplo de JSON en la plantilla de Resource Manager: [Network Security Group](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L68)(Grupo de seguridad de red).

```none
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Network/networkSecurityGroups",
  "name": "[variables('nsgfront')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "nsg-front"
  },
  "properties": {
    "securityRules": [
      {
        "name": "http",
        "properties": {
          "description": "http endpoint",
          "protocol": "Tcp",
          "sourcePortRange": "*",
          "destinationPortRange": "80",
          "sourceAddressPrefix": "*",
          "destinationAddressPrefix": "*",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
      ........<truncated> 
    ]
  }
}
```

En este ejemplo, el grupo de seguridad de red está asociado al objeto de subred declarado en el recurso de red virtual. 

Siga este vínculo para ver el ejemplo de JSON en la plantilla de Resource Manager: [Network Security Group association with Virtual Network](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L158)(Asociación de grupo de seguridad de red con red virtual).

```none
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
```

Este es el aspecto que presenta el grupo de seguridad de red en Azure Portal. Observe que se puede asociar un grupo de seguridad de red con una interfaz de red o subred. En este caso, el grupo de seguridad de red se asocia a una subred. En esta configuración, las reglas de entrada se aplican a todas las máquinas virtuales conectadas a la subred.

![Grupo de seguridad de red (NSG)](./media/virtual-machines-linux-dotnet-core/nsg.png)

Para más información sobre los grupos de seguridad de red, consulte [¿Qué es un grupo de seguridad de red?](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/).

## <a name="next-step"></a>Paso siguiente
<hr>

[Paso 3: disponibilidad y escala en plantillas de Azure Resource Manager](virtual-machines-linux-dotnet-core-4-avalibility-scale.md)

<!--HONumber=Oct16_HO2-->


