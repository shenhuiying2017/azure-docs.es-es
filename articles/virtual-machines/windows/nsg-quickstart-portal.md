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
ms.date: 08/21/2017
ms.author: iainfou
ms.openlocfilehash: 33bc0be0aeae6d0276fd8999b9ac0a010e3067ba
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Apertura de puertos en una máquina virtual con Azure Portal
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Comandos rápidos
También puede [llevar a cabo estos pasos con Azure PowerShell](nsg-quickstart-powershell.md).

En primer lugar, cree el grupo de seguridad de red. Seleccione un grupo de recursos en el portal, elija **Agregar** y busque y seleccione **Grupo de seguridad de red**:

![Agregar un grupo de seguridad de red](./media/nsg-quickstart-portal/add-nsg.png)

Escriba el nombre del grupo de seguridad de red, seleccione un grupo de recursos, o créelo, y seleccione una ubicación. Cuando haya terminado, seleccione **Crear**:

![Creación de un grupo de seguridad de red](./media/nsg-quickstart-portal/create-nsg.png)

Seleccione el nuevo grupo de seguridad de red. Seleccione "Reglas de seguridad de entrada" y, después, seleccione el botón **Agregar** para crear una regla:

![Agregar una regla de entrada](./media/nsg-quickstart-portal/add-inbound-rule.png)

Elija un **servicio** común en el menú desplegable, como *HTTP*. También puede seleccionar *Personalizado* a fin de proporcionar un puerto específico para su uso. Si lo desea, cambie la prioridad o el nombre. La prioridad afecta al orden en que se aplican las reglas (cuanto más bajo es el valor numérico, antes se aplica la regla). También puede seleccionar **Avanzado** en la parte superior de esta pantalla para especificar un intervalo de puertos o un bloque de direcciones IP de origen, por ejemplo. Cuando esté listo, seleccione **Aceptar** para crear la regla:

![Crear una regla de entrada](./media/nsg-quickstart-portal/create-inbound-rule.png)

El paso final es asociar el grupo de seguridad de red a una subred o una interfaz de red específica. Vamos a asociar el grupo de seguridad de red con una subred. Seleccione **Subredes** y elija **Asociar**:

![Asociar un grupo de seguridad de red a una subred](./media/nsg-quickstart-portal/associate-subnet.png)

Seleccione la red virtual y luego seleccione la subred adecuada:

![Asociar un grupo de seguridad de red a una red virtual](./media/nsg-quickstart-portal/select-vnet-subnet.png)

Ahora ha creado un grupo de seguridad de red, ha creado una regla de entrada que permite el tráfico en el puerto 80 y lo ha asociado con una subred. A través del puerto 80 se accede a las máquinas virtuales que se conectan a esa subred.

## <a name="more-information-on-network-security-groups"></a>Más información sobre los grupos de seguridad de red
Los comandos rápidos que se describen aquí le permiten ponerse a trabajar con el flujo de tráfico a la máquina virtual. Los grupos de seguridad de red proporcionan muchas características excelentes y un gran nivel de detalle para controlar el acceso a sus recursos. Puede leer más sobre la [creación de un grupo de seguridad de red y las reglas de ACL aquí](../../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Para las aplicaciones web de alta disponibilidad, debe colocar las máquinas virtuales detrás de una instancia de Azure Load Balancer. El equilibrador de carga distribuye el tráfico a las máquinas virtuales, con un grupo de seguridad de red que proporciona el filtrado del tráfico. Para más información, consulte [Equilibrio de la carga de máquinas virtuales Linux en Azure para crear una aplicación de alta disponibilidad](tutorial-load-balancer.md).

## <a name="next-steps"></a>Pasos siguientes
En este ejemplo, se ha creado una regla sencilla para permitir tráfico HTTP. Puede encontrar información sobre la creación de entornos más detallados en los siguientes artículos:

* [Información general sobre Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [¿Qué es un grupo de seguridad de red?](../../virtual-network/virtual-networks-nsg.md)