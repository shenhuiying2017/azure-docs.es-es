---
title: "¿Qué es una lista de control de acceso de red de Azure?"
description: "Más información sobre las listas de control de acceso de Azure"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 83d66c84-8f6b-4388-8767-cd2de3e72d76
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.openlocfilehash: 9a0c85367968c9b38104012d75b1f3975be82cc1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="what-is-an-endpoint-access-control-list"></a>¿Qué es una lista de control de acceso de puntos de conexión?

> [!IMPORTANT]
> Azure tiene dos [modelos de implementación](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) diferentes para crear recursos y trabajar con ellos: Resource Manager y el clásico. Este artículo trata del modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo de implementación Resource Manager. 

Una lista de control de acceso (ACL) de puntos de conexión es una mejora de seguridad disponible para la implementación de Azure. Una ACL proporciona la capacidad de permitir o denegar tráfico a un extremo de la máquina virtual de forma selectiva. Esta capacidad de filtro de paquetes proporciona un nivel adicional de seguridad. Puede especificar ACL de red solo para extremos. No se puede especificar una ACL para una red virtual o para una subred específica contenida en una red virtual. Siempre que sea posible, se recomienda utilizar grupos de seguridad de red (NSG) en lugar de ACL. Para obtener más información sobre los grupos de seguridad de red, consulte [Información general de los grupos de seguridad de red](virtual-networks-nsg.md).

Las listas de control de acceso se pueden configurar mediante PowerShell o Azure Portal. Para configurar una ACL de red con PowerShell, consulte [Administración de listas de control de acceso para puntos de conexión mediante PowerShell](virtual-networks-acl-powershell.md). Para configurar una ACL de red con Azure Portal, vea [Configuración de puntos de conexión en una máquina virtual](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Con las ACL de red, puede hacer lo siguiente:

* Permitir o denegar selectivamente el tráfico entrante según el intervalo de direcciones IPv4 de subred remota a un extremo de entrada de la máquina virtual
* Incluir en una lista negra direcciones IP
* Crear varias reglas por extremo de máquina virtual
* Usar el orden de reglas para garantizar que se aplica el conjunto correcto de reglas en un extremo de máquina virtual determinado (de menor a mayor)
* Especificar una ACL para una dirección IPv4 de la subred remota específica.

Para más información sobre los límites de las listas de control de acceso, consulte el artículo [Límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).

## <a name="how-acls-work"></a>Funcionamiento de las ACL
Una ACL es un objeto que contiene una lista de reglas. Al crear una ACL y al aplicarla a un extremo de máquina virtual, el filtrado de paquetes tiene lugar en el nodo de host de la máquina virtual. Esto significa que el tráfico de direcciones IP remotas lo filtra el nodo de host para hacer coincidir las reglas de ACL en lugar de en su máquina virtual. De esta forma se evita que la máquina virtual gaste preciosos ciclos de CPU en el filtrado de paquetes.

Cuando se crea una máquina virtual, se coloca una ACL predeterminada para bloquear todo el tráfico entrante. Sin embargo, si se crea un extremo para (puerto 3389), la ACL predeterminada se modifica para permitir todo el tráfico entrante para ese extremo. Después se permite el tráfico entrante desde cualquier subred remota a ese extremo y no se requiere ningún aprovisionamiento de firewall. Todos los demás puertos se bloquean para el tráfico entrante a menos que se creen extremos para esos puertos. De forma predeterminada, se permite el tráfico saliente.

**Ejemplo de tabla de ACL predeterminada**

| **Nº de regla** | **Subred remota** | **Extremo** | **Permitir o denegar** |
| --- | --- | --- | --- |
| 100 |0.0.0.0/0 |3389 |Permitir |

## <a name="permit-and-deny"></a>Permitir y denegar
Selectivamente puede permitir o denegar el tráfico de red para un extremo de entrada de la máquina virtual mediante la creación de reglas que especifiquen "permitir" o "denegar". Es importante tener en cuenta que, de manera predeterminada, cuando se crea un punto de conexión, se permite todo el tráfico a él. Por ese motivo, es importante entender cómo se crean reglas de permiso y denegación y colocarlas en el orden de prioridad correcto si desea un control granular sobre el tráfico de red que ha elegido dar permiso para que llegue al extremo de la máquina virtual.

Puntos que se deben tener en cuenta:

1. **Sin ACL** : de forma predeterminada, cuando se crea un extremo, se permite todo en este extremo.
2. **Permitir** : al agregar uno o más intervalos "permitidos", se deniegan todos los demás intervalos de forma predeterminada. Solo los paquetes del intervalo de IP permitidas podrán comunicarse con el extremo de la máquina virtual.
3. **Denegar** : al agregar uno o más intervalos "denegados", se permiten todos los demás intervalos del tráfico de forma predeterminada.
4. **Combinación de permitir y denegar** : puede utilizar una combinación de "permitir" y "denegar" si desea definir un intervalo de direcciones IP específico para permitir o denegar.

## <a name="rules-and-rule-precedence"></a>Reglas y precedencia de reglas
Las ACL de red se pueden configurar en los extremos de la máquina virtual específica. Por ejemplo, puede especificar una ACL de red para un extremo RDP creado en una máquina virtual que bloquee el acceso a determinadas direcciones IP. La tabla siguiente muestra una manera de conceder acceso a direcciones IP virtuales públicas (VIP) de un determinado intervalo para permitir el acceso para RDP. Se deniegan todas las direcciones IP remotas. Seguimos un orden de regla de la *inferior tiene prioridad* .

### <a name="multiple-rules"></a>Varias reglas
En el ejemplo siguiente, si desea permitir el acceso al extremo RDP solo desde dos intervalos de direcciones IPv4 públicas (65.0.0.0/8 y 159.0.0.0/8), puede lograrlo especificando dos reglas *Permitir* . En este caso, como RDP se crea de forma predeterminada para una máquina virtual, puede que desee bloquear el acceso al puerto RDP según una subred remota. El ejemplo siguiente muestra una manera de conceder acceso a direcciones IP virtuales públicas (VIP) de un determinado intervalo para permitir el acceso para RDP. Se deniegan todas las direcciones IP remotas. Esto funciona porque las ACL de red se puede configurar para un extremo de máquina virtual específica y se deniega el acceso de forma predeterminada.

**Ejemplo: varias reglas**

| **Nº de regla** | **Subred remota** | **Extremo** | **Permitir o denegar** |
| --- | --- | --- | --- |
| 100 |65.0.0.0/8 |3389 |Permitir |
| 200 |159.0.0.0/8 |3389 |Permitir |

### <a name="rule-order"></a>Orden de reglas
Dado que se pueden especificar varias reglas para un extremo, debe haber una manera de organizar las reglas para determinar qué regla tiene prioridad. El orden de reglas especifica la prioridad. Las ACL de red siguen el orden de regla de la *inferior tiene prioridad* . En el ejemplo siguiente, se le ha concedido acceso de manera selectiva al extremo del puerto 80 a solo determinados intervalos de direcciones IP. Para configurar esto, tenemos una regla de denegación (regla \# 100) para las direcciones del espacio 175.1.0.1/24. A continuación, se especifica una segunda regla con prioridad 200 que permite el acceso a todas las demás direcciones bajo 175.0.0.0/8.

**Ejemplo: precedencia de reglas**

| **Nº de regla** | **Subred remota** | **Extremo** | **Permitir o denegar** |
| --- | --- | --- | --- |
| 100 |175.1.0.1/24 |80 |Denegar |
| 200 |175.0.0.0/8 |80 |Permitir |

## <a name="network-acls-and-load-balanced-sets"></a>ACL de red y conjuntos de carga equilibrada
Las ACL de red pueden especificarse en un punto de conexión del conjunto de carga equilibrada. Si se especifica una ACL para un conjunto de carga equilibrada, la ACL de red se aplica a todas las máquinas virtuales de ese conjunto. Por ejemplo, si se ha creado un conjunto de carga equilibrada con "Puerto 80" y ese conjunto contiene tres máquinas virtuales, la ACL de red creada en el punto de conexión "Puerto 80" de una máquina virtual se aplicará automáticamente a las demás.

![ACL de red y conjuntos de carga equilibrada](./media/virtual-networks-acl/IC674733.png)

## <a name="next-steps"></a>Pasos siguientes
[Administración de listas de control de acceso para puntos de conexión mediante PowerShell](virtual-networks-acl-powershell.md)

