<properties
   pageTitle="Habilitación del acceso externo a la máquina virtual mediante el Portal | Microsoft Azure"
   description="Obtenga información sobre cómo abrir un puerto o crear un punto de conexión que permita el acceso externo a la máquina virtual con el modelo de implementación de Resource Manager en el Portal de Azure"
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
   ms.date="05/24/2016"
   ms.author="iainfou"/>

# Habilitación del acceso externo a la máquina virtual mediante el Portal de Azure
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## Comandos rápidos
También puede [llevar a cabo estos pasos con Azure PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md).

En primer lugar, cree el grupo de seguridad de red. Seleccione un grupo de recursos en el portal, haga clic en "Agregar" y busque y seleccione "Grupo de seguridad de red":

![Agregar un grupo de seguridad de red](./media/virtual-machines-windows-nsg-quickstart-portal/add-nsg.png)

Escriba un nombre para el grupo de seguridad de red y seleccione una ubicación:

![Creación de un grupo de seguridad de red](./media/virtual-machines-windows-nsg-quickstart-portal/create-nsg.png)

Seleccione el nuevo grupo de seguridad de red. Ahora puede crear una regla de entrada:

![Agregar una regla de entrada](./media/virtual-machines-windows-nsg-quickstart-portal/add-inbound-rule.png)

Proporcione un nombre para la nueva regla. Tenga en cuenta que el puerto 80 ya está especificado de manera predeterminada. Aquí es donde podría cambiar el origen, el protocolo y el destino al agregar reglas adicionales al grupo de seguridad de red:

![Crear una regla de entrada](./media/virtual-machines-windows-nsg-quickstart-portal/create-inbound-rule.png)

El paso final es asociar el grupo de seguridad de red a una subred o una interfaz de red específica. Vamos a asociar el grupo de seguridad de red con una subred:

![Asociar un grupo de seguridad de red a una subred](./media/virtual-machines-windows-nsg-quickstart-portal/associate-subnet.png)

Seleccione la red virtual y luego seleccione la subred adecuada:

![Asociar un grupo de seguridad de red a una red virtual](./media/virtual-machines-windows-nsg-quickstart-portal/select-vnet-subnet.png)

Ahora ha creado un grupo de seguridad de red, ha creado una regla de entrada que permite el tráfico en el puerto 80 y lo ha asociado con una subred. Todas las máquinas virtuales que se conectan a esa subred estarán accesibles en el puerto 80.


## Más información sobre los grupos de seguridad de red
Los comandos rápidos que se describen aquí le permiten ponerse a trabajar con el flujo de tráfico a la máquina virtual. Los grupos de seguridad de red proporcionan muchas características excelentes y un gran nivel de detalle para controlar el acceso a sus recursos. Puede leer más sobre la [creación de un grupo de seguridad de red y las reglas de ACL aquí](../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Los grupos de seguridad de red y las reglas de ACL también se pueden definir como parte de las plantillas de Azure Resource Manager. Más información sobre la [creación de grupos de seguridad de red con plantillas](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Si necesita utilizar el enrutamiento de puerto para asignar un único puerto externo a un puerto interno de su máquina virtual, es preciso utilizar un equilibrador de carga y las reglas de traducción de direcciones de red (NAT). Por ejemplo, puede exponer el puerto TCP 8080 externamente y dirigir el tráfico al puerto TCP 80 en una máquina virtual. Puede aprender sobre la [creación de un equilibrador de carga accesible desde Internet](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## Pasos siguientes
En este ejemplo, se ha creado una regla sencilla para permitir tráfico HTTP. Puede encontrar información sobre la creación de entornos más detallados en los siguientes artículos:

- [Información general del Administrador de recursos de Azure](../resource-group-overview.md)
- [¿Qué es un grupo de seguridad de red?](../virtual-network/virtual-networks-nsg.md)
- [Información general de Azure Resource Manager para equilibradores de carga](../load-balancer/load-balancer-arm.md)

<!---HONumber=AcomDC_0608_2016-->