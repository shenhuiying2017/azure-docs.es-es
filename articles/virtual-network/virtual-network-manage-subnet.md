---
title: "Creación, cambio o eliminación de subredes de redes virtuales de Azure | Microsoft Docs"
description: Aprenda a crear, cambiar o eliminar subredes de redes virtuales.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 38dcdf2f313c236a507e6a418c39812da16c6345
ms.contentlocale: es-es
ms.lasthandoff: 05/16/2017


---
# <a name="create-change-or-delete-virtual-network-subnets"></a>Creación, cambio o eliminación de subredes de redes virtuales

Aprenda a crear, cambiar o eliminar subredes de redes virtuales. Si no está familiarizado con las redes virtuales, es recomendable leer los artículos de [introducción a las redes virtuales](virtual-networks-overview.md) y [Create, change, or delete virtual networks](virtual-network-manage-network.md) (Creación, cambio o eliminación de redes virtuales) antes de crear, cambiar o eliminar subredes. Los recursos de Azure que se puedan conectar a una red virtual están conectados a una subred dentro de una red virtual. En una red virtual se suelen crear varias subredes para:
- **Filtrar el tráfico entre subredes:** los grupos de seguridad de red (NSG) se pueden aplicar a subredes para filtrar el tráfico de red entrante y saliente de todos los recursos (por ejemplo, las máquinas virtuales) conectados a la red virtual. Para más información sobre cómo crear grupos de seguridad de red, consulte el artículo sobre la [creación de grupos de seguridad de red](virtual-networks-create-nsg-arm-pportal.md).
- **Controlar el enrutamiento entre subredes:** Azure crea rutas predeterminadas para que el tráfico se enrute automáticamente entre subredes. Puede invalidar las rutas predeterminadas de Azure mediante la creación de rutas definidas por el usuario (UDR). Para más información sobre las rutas definidas por el usuario, lea el artículo sobre las [rutas definidas por el usuario](virtual-network-create-udr-arm-ps.md). 

En este artículo se describe cómo crear, cambiar y eliminar subredes de redes virtuales creadas con el modelo de implementación de Azure Resource Manager.
 
## <a name="before"></a>Antes de empezar

Complete las tareas siguientes antes de seguir los pasos de las secciones de este artículo:

- Si no está familiarizado con las redes virtuales y las subredes de Azure, se recomienda completar el ejercicio que encontrará en [Creación de su primera red virtual de Azure](virtual-network-get-started-vnet-subnet.md) antes de leer este artículo. El ejercicio le ayudará a familiarizarse con las redes virtuales y las subredes.
- Revise el artículo sobre los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para información sobre los límites de las subredes y las redes virtuales.
- Inicie sesión con una cuenta de Azure en Azure Portal, la interfaz de la línea de comandos (CLI) de Azure o Azure PowerShell. Si todavía no tiene una cuenta de Azure, regístrese para obtener una [cuenta de evaluación gratuita](https://azure.microsoft.com/free).
- Si usa los comandos de Azure PowerShell para completar las tareas de este artículo, primero debe [instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de los cmdlets de Azure PowerShell. Para obtener ayuda con los comandos de PowerShell, con ejemplos, escriba `get-help <command> -full`.
- Si usa los comandos de la interfaz de la línea de comandos (CLI) de Azure para completar las tareas de este artículo, primero debe [instalar y configurar la CLI de Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de la CLI de Azure. Para obtener ayuda con los comandos de la CLI, escriba `az <command> --help`.


## <a name="create-subnet"></a>Creación de una subred

1. Inicie sesión en el [portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *redes virtuales*. Cuando **redes virtuales** aparezca en los resultados de búsqueda, haga clic en él.
3. En la hoja **Redes virtuales** que aparece, haga clic en la red virtual a la cual desea agregar una subred.
4. En el panel que aparece para la red virtual seleccionada, haga clic en **Subredes**.
5. Haga clic en **+ Subred**.
6. En la hoja **Agregar subred**, especifique valores para los parámetros siguientes:
    - **Nombre:** debe ser único dentro de la red virtual.
    - **Intervalo de direcciones:** debe ser único dentro del espacio de direcciones para la red virtual y no superponerse con otros intervalos de direcciones de subredes de la red virtual. El espacio de direcciones debe especificarse en notación CIDR. Por ejemplo, en una red virtual con el espacio de direcciones 10.0.0.0/16, podría definir el espacio de direcciones de subred 10.0.0.0/24. El menor intervalo que se puede especificar es /29, lo que proporciona ocho direcciones IP de subred. De conformidad con el protocolo, Azure reserva la primera y la última dirección de cada subred. Otras tres direcciones están reservadas para el uso del servicio de Azure. Como resultado, al definir una subred con el intervalo de direcciones /29, se generan tres direcciones IP utilizables en la subred. Si planea conectar una red virtual a una instancia de VPN Gateway, debe crear una subred de puerta de enlace. Más información sobre las [consideraciones específicas del intervalo de direcciones de las subredes de puerta de enlace](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namegwsubagateway-subnet). En determinadas circunstancias se puede cambiar el intervalo de direcciones una vez creada la subred. Para información sobre cómo cambiar un intervalo de direcciones de subred, lea la sección sobre el [cambio de subred](#change-subnet) de este artículo.
    - **Grupo de seguridad de red (NSG):** opcionalmente, puede asociar un NSG existente a la subred para controlar el filtrado de tráfico de red entrante y saliente de la subred. El NSG debe existir en la misma suscripción y ubicación de la red virtual, y haberse creado mediante el modelo de implementación de Resource Manager. Para más información sobre cómo crear grupos de seguridad de red, consulte el artículo sobre los [grupos de seguridad de red](virtual-networks-create-nsg-arm-pportal.md).
    - **Tabla de rutas:** opcionalmente, puede asociar una tabla de rutas existente a la subred para controlar el enrutamiento de tráfico de red a otras redes. La tabla de rutas debe existir en la misma suscripción y ubicación de la red virtual, y haberse creado mediante el modelo de implementación de Resource Manager. Para más información sobre la creación de tablas de rutas, lea el artículo sobre las [rutas definidas por el usuario](virtual-network-create-udr-arm-ps.md).
    - **Usuarios**: puede controlar el acceso a la subred con los roles integrados u otros personalizados. Para más información sobre la asignación de roles y usuarios para acceder a la subred, lea el artículo sobre la [asignación de roles para administrar el acceso a los recursos de Azure](../active-directory/role-based-access-control-configure.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access).
7. Haga clic en el botón **Aceptar** para agregar la subred a la red virtual seleccionada.

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|[az network vnet subnet create](/cli/azure/network/vnet/subnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json), [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet"></a>Cambio de configuración de subred

Con recursos conectados a una subred, puede cambiar el grupo de seguridad de red, las tablas de rutas y el acceso de los usuarios a esta. Para más información acerca de estas opciones, lea el paso 6 de la sección [Creación de una subred](#create-subnet) de este artículo. Para cambiar el espacio de direcciones de una subred, no se pueden conectar recursos a la subred. Si hay recursos conectados a la subred, para cambiar el intervalo de direcciones, primero debe eliminarlos. Las instrucciones sobre cómo eliminar un recurso varían según el recurso. Para más información sobre cómo eliminar los recursos conectados a una subred, lea la documentación de cada tipo de recurso que desee eliminar. Para cambiar el intervalo de direcciones de una subred, complete los pasos siguientes:

1. Inicie sesión en el [portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior del portal, escriba *redes virtuales*. Cuando **redes virtuales** aparezca en los resultados de búsqueda, haga clic en él.
3. En la hoja **Redes virtuales** que aparece, haga clic en la red virtual a la cual desea cambiar el intervalo de direcciones de subred.
4. Haga clic en la subred de la cual desea cambiar el intervalo de direcciones.
5. En la hoja que aparece para la subred seleccionada, escriba el nuevo intervalo de direcciones en el cuadro **Intervalo de direcciones**. Debe ser único dentro del espacio de direcciones para la red virtual y no superponerse con otros intervalos de direcciones de subredes de la red virtual. El espacio de direcciones debe especificarse en notación CIDR. Por ejemplo, en una red virtual con el espacio de direcciones 10.0.0.0/16, podría definir el espacio de direcciones de subred 10.0.0.0/24. El menor intervalo que se puede especificar es /29, lo que proporciona ocho direcciones IP de subred. De conformidad con el protocolo, Azure reserva la primera y la última dirección de cada subred. Otras tres direcciones están reservadas para el uso del servicio de Azure. Como resultado, una subred con un intervalo de direcciones /29 tiene tres direcciones IP utilizables. Si planea conectar una red virtual a una instancia de VPN Gateway, debe crear una subred de puerta de enlace. Más información sobre las [consideraciones específicas del intervalo de direcciones de las subredes de puerta de enlace](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namegwsubagateway-subnet). En determinadas circunstancias se puede cambiar el intervalo de direcciones una vez creada la subred. Para información sobre cómo cambiar un intervalo de direcciones de subred, lea la sección sobre el [cambio de subred](#change-subnet) de este artículo.
6. Haga clic en **Guardar**.

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|[az network vnet subnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-subnet"></a>Eliminación de una subred

Solo se puede eliminar una subred si no tiene recursos conectados. Si hay recursos conectados a la subred, primero debe eliminarlos. Las instrucciones sobre cómo eliminar un recurso varían según el recurso. Para más información sobre cómo eliminar los recursos conectados a una subred, lea la documentación de cada tipo de recurso que desee eliminar.

1. Inicie sesión en el [portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *redes virtuales*. Cuando **redes virtuales** aparezca en los resultados de búsqueda, haga clic en él.
3. En la hoja **Redes virtuales** que aparece, haga clic en la red virtual de la cual desea eliminar la subred.
4. En la hoja que aparece para la red virtual seleccionada, en **Subredes**, haga clic en **Opciones**.
5. En la lista de subredes que aparece en la hoja de subredes, haga clic con el botón derecho en la que desea eliminar, haga clic en **Eliminar** y en **Sí** para eliminar la subred.

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|[az network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>Pasos siguientes

Para crear una máquina virtual y conectarla a una subred, lea el artículo sobre la [creación de una red virtual y la conexión de máquinas virtuales](virtual-network-get-started-vnet-subnet.md#a-namecreate-vmsacreate-virtual-machines).
