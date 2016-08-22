<properties
   pageTitle="Habilitación del acceso externo a la máquina virtual mediante PowerShell | Microsoft Azure"
   description="Obtenga información sobre cómo abrir un puerto o crear un punto de conexión que permita el acceso externo a la máquina virtual de Windows con el modo de implementación de Resource Manager y Azure PowerShell"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/08/2016"
   ms.author="iainfou"/>

# Habilitación del acceso externo a la máquina virtual mediante PowerShell
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## Comandos rápidos
Para crear reglas de los grupos de seguridad de red y ACL necesitará [que esté instalada la versión más reciente de Azure PowerShell](../powershell-install-configure.md). También puede [llevar a cabo estos pasos con el Portal de Azure](virtual-machines-windows-nsg-quickstart-portal.md).

En primer lugar, debe crear una regla para permitir el tráfico HTTP en el puerto TCP 80, escribiendo un nombre y una descripción propios:

```
$httprule = New-AzureRmNetworkSecurityRuleConfig -Name http-rule -Description "Allow HTTP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 80
```

A continuación, cree el grupo de seguridad de red y asigne la regla HTTP que acaba de crear como sigue, escribiendo el nombre y la ubicación de su propio grupo de recursos:

```
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName TestRG -Location westus `
    -Name "TestNSG" -SecurityRules $httprule
```

Ahora hay que asignar el grupo de seguridad de red a una subred. En primer lugar, seleccione la red virtual:

```
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
```

Asocie el grupo de seguridad de red a la subred:

```
Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name TestSubnet `
    -NetworkSecurityGroup $nsg
```

Por último, actualice la red virtual para que los cambios surtan efecto:

```
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## Más información sobre los grupos de seguridad de red
Los comandos rápidos que se describen aquí le permiten ponerse a trabajar con el flujo de tráfico a la máquina virtual. Los grupos de seguridad de red proporcionan muchas características excelentes y un gran nivel de detalle para controlar el acceso a sus recursos. Puede leer más sobre la [creación de un grupo de seguridad de red y las reglas de ACL aquí](../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Los grupos de seguridad de red y las reglas de ACL también se pueden definir como parte de las plantillas de Azure Resource Manager. Más información sobre la [creación de grupos de seguridad de red con plantillas](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Si necesita utilizar el enrutamiento de puerto para asignar un único puerto externo a un puerto interno de su máquina virtual, es preciso utilizar un equilibrador de carga y las reglas de traducción de direcciones de red (NAT). Por ejemplo, puede exponer el puerto TCP 8080 externamente y dirigir el tráfico al puerto TCP 80 en una máquina virtual. Puede aprender sobre la [creación de un equilibrador de carga accesible desde Internet](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## Pasos siguientes
En este ejemplo, se ha creado una regla sencilla para permitir tráfico HTTP. Puede encontrar información sobre la creación de entornos más detallados en los siguientes artículos:

- [Información general del Administrador de recursos de Azure](../resource-group-overview.md)
- [¿Qué es un grupo de seguridad de red?](../virtual-network/virtual-networks-nsg.md)
- [Información general de Azure Resource Manager para equilibradores de carga](../load-balancer/load-balancer-arm.md)

<!---HONumber=AcomDC_0810_2016-->