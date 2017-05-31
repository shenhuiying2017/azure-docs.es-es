---
title: Apertura de puertos a una VM mediante Azure Portal | Microsoft Docs
description: "Aprenda a abrir un puerto o crear un punto de conexión a la máquina virtual Windows con el modelo de implementación de Resource Manager en Azure Portal"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 20aa8247a16195310a8cb03c13c34186456fd7af
ms.contentlocale: es-es
ms.lasthandoff: 05/11/2017


---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Apertura de puertos en una máquina virtual con Azure Portal
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Comandos rápidos
También puede [llevar a cabo estos pasos con Azure PowerShell](nsg-quickstart-powershell.md).

En primer lugar, cree el grupo de seguridad de red. Seleccione un grupo de recursos en el portal, haga clic en **Agregar** y busque y seleccione "Grupo de seguridad de red":

![Agregar un grupo de seguridad de red](./media/nsg-quickstart-portal/add-nsg.png)

Escriba el nombre del grupo de seguridad de red, seleccione un grupo de recursos, o créelo, y seleccione una ubicación. Cuando haya terminado, haga clic en **Crear**:

![Creación de un grupo de seguridad de red](./media/nsg-quickstart-portal/create-nsg.png)

Seleccione el nuevo grupo de seguridad de red. Seleccione 'Reglas de seguridad de entrada' y, después, haga clic en el botón **Agregar** para crear una regla:

![Agregar una regla de entrada](./media/nsg-quickstart-portal/add-inbound-rule.png)

Proporcione un nombre para la nueva regla. El puerto 80 aparece de forma predeterminada. En esta hoja es donde se cambia el origen, protocolo y destino cuando se agregan reglas al grupo de seguridad de red. Haga clic en **Aceptar** para crear la regla:

![Crear una regla de entrada](./media/nsg-quickstart-portal/create-inbound-rule.png)

El paso final es asociar el grupo de seguridad de red a una subred o una interfaz de red específica. Vamos a asociar el grupo de seguridad de red con una subred. Seleccione 'Subredes' y haga clic en **Asociar**:

![Asociar un grupo de seguridad de red a una subred](./media/nsg-quickstart-portal/associate-subnet.png)

Seleccione la red virtual y luego seleccione la subred adecuada:

![Asociar un grupo de seguridad de red a una red virtual](./media/nsg-quickstart-portal/select-vnet-subnet.png)

Ahora ha creado un grupo de seguridad de red, ha creado una regla de entrada que permite el tráfico en el puerto 80 y lo ha asociado con una subred. A través del puerto 80 se accede a las máquinas virtuales que se conectan a esa subred.

## <a name="more-information-on-network-security-groups"></a>Más información sobre los grupos de seguridad de red
Los comandos rápidos que se describen aquí le permiten ponerse a trabajar con el flujo de tráfico a la máquina virtual. Los grupos de seguridad de red proporcionan muchas características excelentes y un gran nivel de detalle para controlar el acceso a sus recursos. Puede leer más sobre la [creación de un grupo de seguridad de red y las reglas de ACL aquí](../../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Los grupos de seguridad de red y las reglas de ACL también se pueden definir como parte de las plantillas de Azure Resource Manager. Más información sobre la [creación de grupos de seguridad de red con plantillas](../../virtual-network/virtual-networks-create-nsg-arm-template.md).

Si necesita utilizar el enrutamiento de puerto para asignar un único puerto externo a un puerto interno de su máquina virtual, use un equilibrador de carga y las reglas de traducción de direcciones de red (NAT). Por ejemplo, puede exponer el puerto TCP 8080 externamente y dirigir el tráfico al puerto TCP 80 en una máquina virtual. Puede aprender sobre la [creación de un equilibrador de carga accesible desde Internet](../../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Pasos siguientes
En este ejemplo, se ha creado una regla sencilla para permitir tráfico HTTP. Puede encontrar información sobre la creación de entornos más detallados en los siguientes artículos:

* [Información general sobre Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [¿Qué es un grupo de seguridad de red?](../../virtual-network/virtual-networks-nsg.md)
* [Información general de Azure Resource Manager para equilibradores de carga](../../load-balancer/load-balancer-arm.md)


