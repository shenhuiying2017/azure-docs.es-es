<properties
   pageTitle="Permitir el acceso externo a una máquina virtual con Linux | Microsoft Azure"
   description="Más información sobre cómo abrir un puerto o crear un punto de conexión que permita el acceso externo a la máquina virtual con Linux mediante el modelo de implementación de Resource Manager y la CLI de Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="05/24/2016"
   ms.author="iainfou"/>

# Permitir el acceso externo a la máquina virtual
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## Comandos rápidos
Para crear un grupo de seguridad de red y las reglas que necesitará [la CLI de Azure](../xplat-cli-install.md) en modo de Resource Manager (`azure config mode arm`).

Cree el grupo de seguridad de red de la manera siguiente y escriba sus propios nombres y ubicación según sea adecuado:

```
azure network nsg create --resource-group TestRG --name TestNSG --location westus
```

Agregue una regla que permita tráfico HTTP al servidor web (esta se puede ajustar para su propio escenario, por ejemplo, el acceso de SSH o la conectividad de base de datos):

```
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 80 --access allow --resource-group TestRG --nsg-name TestNSG --name AllowHTTP
```

Asociar el grupo de seguridad de red con la interfaz de red de la máquina virtual:

```
azure network nic set --resource-group TestRG --name TestNIC --network-security-group-name TestNSG
```

Como alternativa, puede asociar el grupo de seguridad de red a una subred de red virtual, en lugar de únicamente a la interfaz de red en una única máquina virtual:

```
azure network vnet subnet set --resource-group TestRG --name TestSubnet --network-security-group-name TestNSG
```

## Más información sobre los grupos de seguridad de red
Los comandos rápidos que se describen aquí le permiten ponerse a trabajar con el flujo de tráfico a la máquina virtual. Los grupos de seguridad de red proporcionan muchas características excelentes y un gran nivel de detalle para controlar el acceso a sus recursos. Puede leer más sobre la [creación de un grupo de seguridad de red y las reglas de ACL aquí](../virtual-network/virtual-networks-create-nsg-arm-cli.md).

Los grupos de seguridad de red y las reglas de ACL también se pueden definir como parte de las plantillas de Azure Resource Manager. Más información sobre la [creación de grupos de seguridad de red con plantillas](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Si necesita utilizar el enrutamiento de puerto para asignar un único puerto externo a un puerto interno de su máquina virtual, es preciso utilizar un equilibrador de carga y las reglas de traducción de direcciones de red (NAT). Por ejemplo, puede exponer el puerto TCP 8080 externamente y dirigir el tráfico al puerto TCP 80 en una máquina virtual. Puede aprender sobre la [creación de un equilibrador de carga accesible desde Internet](../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## Pasos siguientes
En este ejemplo, se ha creado una regla sencilla para permitir tráfico HTTP. Puede encontrar información sobre la creación de entornos más detallados en los siguientes artículos:

- [Información general del Administrador de recursos de Azure](../resource-group-overview.md)
- [¿Qué es un grupo de seguridad de red?](../virtual-network/virtual-networks-nsg.md)
- [Compatibilidad de Azure Resource Manager con el Equilibrador de carga](../load-balancer2 /load-balancer-arm.md)

<!---HONumber=AcomDC_0601_2016-->