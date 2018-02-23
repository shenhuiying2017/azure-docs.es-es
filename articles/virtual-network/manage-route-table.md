---
title: "Creación, modificación o eliminación de una tabla de rutas de Azure | Microsoft Azure"
description: "Obtenga información sobre cómo crear, modificar o eliminar una tabla de rutas."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: 7edc73f337a72c24fd24d94468ee590b75dfa7df
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/11/2018
---
# <a name="create-change-or-delete-a-route-table"></a>Creación, modificación o eliminación de una tabla de rutas

Azure enruta automáticamente el tráfico entre redes locales, las redes virtuales y las subredes de Azure. Si desea cambiar algún enrutamiento predeterminado de Azure, debe crear una tabla de rutas. Si no está familiarizado con el enrutamiento de Azure, le recomendamos leer [Enrutamiento del tráfico de redes virtuales](virtual-networks-udr-overview.md) y completar el tutorial [Creación de una ruta definida por el usuario - Azure Portal](create-user-defined-route-portal.md) antes de completar las tareas que aparecen este artículo.

## <a name="before-you-begin"></a>Antes de empezar

Complete las tareas siguientes antes de seguir los pasos de las secciones de este artículo:

- Si todavía no tiene una cuenta de Azure, regístrese para obtener una [cuenta de evaluación gratuita](https://azure.microsoft.com/free).
- Si usa el portal, abra https://portal.azure.com e inicie sesión con la cuenta de Azure.
- Si usa comandos de PowerShell para completar las tareas de este artículo, ejecute los comandos que se encuentran en [Azure Cloud Shell](https://shell.azure.com/powershell) o ejecute PowerShell en el equipo. Azure Cloud Shell es un shell interactivo gratuito que puede usar para ejecutar los pasos de este artículo. Tiene las herramientas comunes de Azure preinstaladas y configuradas para usarlas en la cuenta. Para realizar este tutorial es necesaria la versión 5.2.0 del módulo de Azure PowerShell o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para buscar la versión instalada. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure.
- Si usa la interfaz de la línea de comandos (CLI) de Azure para completar las tareas de este artículo, ejecute los comandos que se encuentran en [Azure Cloud Shell](https://shell.azure.com/bash) o ejecute la CLI en el equipo. Para realizar este tutorial es necesaria la versión 2.0.26 o superior de la CLI de Azure. Ejecute `az --version` para buscar la versión instalada. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli). Si ejecuta de forma local la CLI de Azure, también debe ejecutar `az login` para crear una conexión con Azure.

## <a name="create-a-route-table"></a>Creación de una tabla de rutas

Existe un límite para la cantidad de tablas de rutas que puede crear por suscripción y ubicación de Azure. Para más información, consulte el artículo acerca de los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. En la esquina superior izquierda del portal, seleccione **+ Nuevo**.
2. Seleccione **Redes** y, luego, **Tabla de rutas**.
3. Escriba un **nombre** para la tabla de rutas, seleccione la **suscripción**, cree un **grupo de recursos** nuevo o seleccione uno existe, seleccione una **ubicación** y, luego, seleccione **Crear**. La opción **Disable BGP route propagation** (Deshabilitar la propagación de rutas BGP) evita que las rutas locales se propaguen a una red virtual de Azure a través de BGP. Si la red virtual no está conectada a una puerta de enlace de red de Azure (VPN o ExpressRoute), deje la opción *Disabled* (Deshabilitada). 

**Comandos**

- CLI de Azure: [az network route-table create](/cli/azure/network/route-table/route#az_network_route_table_create)
- PowerShell: [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable)

## <a name="view-route-tables"></a>Vista de las tablas de rutas

En el cuadro de búsqueda que aparece en la parte superior del portal, escriba *tablas de rutas*. Cuando la opción **Tablas de rutas** aparezca en los resultados de la búsqueda, selecciónela. Aparecen las tablas de rutas que existen en la suscripción.

**Comandos**

- CLI de Azure: [az network route-table list](/cli/azure/network/route-table/route#az_network_route_table_list)
- PowerShell: [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## <a name="view-details-of-a-route-table"></a>Vista de los detalles de una tabla de rutas

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba *tablas de rutas*. Cuando la opción **Tablas de rutas** aparezca en los resultados de la búsqueda, selecciónela.
2. Seleccione en la lista la tabla de rutas cuyos detalles desea ver. En **CONFIGURACIÓN**, puede ver las **rutas** de la tabla de rutas y las **subredes** con las que está asociada la tabla de rutas.
3. Para más información sobre la configuración común de Azure, consulte la información siguiente:
    *   [Registro de actividad](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
    *   [Control de acceso (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
    *   [Etiquetas](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)
    *   [Bloqueos](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Script de Automation](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Comandos**

- CLI de Azure: [az network route-table show](/cli/azure/network/route-table/route#az_network_route_table_show)
- PowerShell: [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## <a name="change-a-route-table"></a>Modificación de una tabla de rutas

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba *tablas de rutas*. Cuando la opción **Tablas de rutas** aparezca en los resultados de la búsqueda, selecciónela.
2. Seleccione la tabla de rutas que desea modificar. Los cambios más comunes son [agregar](#create-a-route) o [quitar](#delete-a-route) rutas y [asociar](#associate-a-route-table-to-a-subnet) o [desasociar](#dissociate-a-route-table-from-a-subnet) tablas de rutas de las subredes.

**Comandos**

- CLI de Azure: [az network route-table update](/cli/azure/network/route-table/route#az_network_route_table_update)
- PowerShell: [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>Asociación de una tabla de rutas a una subred

Una subred puede tener una tabla de ruta asociada a ella o ninguna. Una tabla de rutas se puede asociar a varias subredes o a ninguna. Como las tablas de rutas no se asocian a las redes virtuales, debe asociar una tabla de rutas a cada subred con la que desea asociarla. Todo el tráfico que sale de la subred se enruta según las rutas que se crearon dentro de las tablas de rutas, las [rutas predeterminadas](virtual-networks-udr-overview.md#default) y las rutas propagadas desde una red local, si la red virtual está conectada a una puerta de enlace de red virtual de Azure (ExpressRoute, o VPN, si se usa BGP con una puerta de enlace de VPN). Solo puede asociar una tabla de rutas a las subredes de las redes virtuales que existen en la misma suscripción y ubicación de Azure de la tabla de rutas.

1. En el cuadro de búsqueda que se encuentra en la parte superior del portal, escriba *redes virtuales*. Cuando aparezca la opción **Redes virtuales** en los resultados de la búsqueda, selecciónela.
2. Seleccione la red virtual en la lista que contiene la subred a la que desea asociar una tabla de rutas.
3. Seleccione **Subredes** en **CONFIGURACIÓN**.
4. Seleccione la subred a la que desea asociar la tabla de rutas.
5. Seleccione **Tabla de rutas**, la tabla de rutas a la que desea asociar la subred y, luego, seleccione **Guardar**.

**Comandos**

- CLI de Azure: [az network vnet subnet update](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az_network_vnet_subnet_update)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>Desasociación de una tabla de rutas de una subred

Cuando desasocia una tabla de rutas de una subred, Azure enruta el tráfico según sus [rutas predeterminadas](virtual-networks-udr-overview.md#default).

1. En el cuadro de búsqueda que se encuentra en la parte superior del portal, escriba *redes virtuales*. Cuando aparezca la opción **Redes virtuales** en los resultados de la búsqueda, selecciónela.
2. Seleccione la red virtual que contiene la subred de la que desea desasociar una tabla de rutas.
3. Seleccione **Subredes** en **CONFIGURACIÓN**.
4. Seleccione la subred de la que desea desasociar la tabla de rutas.
5. Seleccione **Tabla de rutas**, **Ninguna** y, luego, seleccione **Guardar**.

**Comandos**

- CLI de Azure: [az network vnet subnet update](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az_network_vnet_subnet_update)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) 

## <a name="delete-a-route-table"></a>Eliminación de una tabla de rutas

Una tabla de rutas no se puede eliminar si está asociada a alguna subred. [Desasocie](#dissociate-a-route-table-from-a-subnet) una tabla de rutas de todas las subredes antes de intentar eliminarla.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba *tablas de rutas*. Cuando la opción **Tablas de rutas** aparezca en los resultados de la búsqueda, selecciónela.
2. Seleccione **...** en el lado derecho de la tabla de rutas que desea eliminar.
3. Seleccione **Eliminar** y, luego, seleccione **Sí**.

**Comandos**

- CLI de Azure: [az network route-table delete](/cli/azure/network/route-table/route#az_network_route_table_delete)
- PowerShell: [Delete-AzureRmRouteTable](/powershell/module/azurerm.network/delete-azurermroutetable) 

## <a name="create-a-route"></a>Creación de una ruta

Existe un límite para la cantidad de rutas por tabla de rutas que puede crear por suscripción y ubicación de Azure. Para más información, consulte el artículo acerca de los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba *tablas de rutas*. Cuando la opción **Tablas de rutas** aparezca en los resultados de la búsqueda, selecciónela.
2. Seleccione en la lista la tabla de rutas a la que desea agregar una ruta.
3. Seleccione **Rutas** en **CONFIGURACIÓN**.
4. Seleccione **+Agregar**.
5. Escriba un **nombre** único para la ruta dentro de la tabla de rutas.
6. Escriba el **prefijo de dirección**, en notación CIDR, al que desea enrutar el tráfico. El prefijo no se puede duplicar en más de una ruta dentro de la tabla de rutas, aunque puede estar dentro de otro prefijo. Por ejemplo, si definió 10.0.0.0/16 como prefijo en una ruta, de todos modos puede definir otra ruta con el prefijo de dirección 10.0.0.0/24. Azure selecciona una ruta para el tráfico en función de la coincidencia de prefijo más larga. Para más información sobre cómo Azure selecciona las rutas, consulte [Selección de rutas por parte de Azure](virtual-networks-udr-overview.md#how-azure-selects-a-route).
7. Seleccione un **tipo de próximo salto**. Para una descripción detallada de todos los tipos de próximo salto, consulte [Enrutamiento del tráfico de redes virtuales](virtual-networks-udr-overview.md).
8. Escriba una dirección IP en la **dirección del próximo salto**. Solo puede escribir una dirección si seleccionó *Aplicación virtual* en **Tipo de próximo salto**.
9. Seleccione **Aceptar**. 

**Comandos**

- CLI de Azure: [az network route-table route create](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_create)
- PowerShell: [New-AzureRmRouteConfig](/powershell/module/azurerm.network/new-azurermrouteconfig)

## <a name="view-routes"></a>Vista de las rutas

Una tabla de rutas contiene varias rutas o ninguna. Para más detalles sobre la información que aparece al ver las rutas, consulte [Enrutamiento del tráfico de redes virtuales](virtual-networks-udr-overview.md).

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba *tablas de rutas*. Cuando la opción **Tablas de rutas** aparezca en los resultados de la búsqueda, selecciónela.
2. Seleccione en la lista la tabla de rutas cuyas rutas quiere ver.
3. Seleccione **Rutas** en **CONFIGURACIÓN**.

**Comandos**

- CLI de Azure: [az network route-table route list](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_list)
- PowerShell: [Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## <a name="view-details-of-a-route"></a>Vista de los detalles de una ruta

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba *tablas de rutas*. Cuando la opción **Tablas de rutas** aparezca en los resultados de la búsqueda, selecciónela.
2. Seleccione la tabla de rutas de la que desea conocer los detalles de una ruta.
3. Seleccione **Rutas**.
4. Seleccione la ruta cuyos detalles quiere ver.

**Comandos**

- CLI de Azure: [az network route-table route show](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_show)
- PowerShell: [Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## <a name="change-a-route"></a>Modificación de una ruta

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba *tablas de rutas*. Cuando la opción **Tablas de rutas** aparezca en los resultados de la búsqueda, selecciónela.
2. Seleccione la tabla de rutas de la que desea modificar una ruta.
3. Seleccione **Rutas**.
4. Seleccione la ruta que desea modificar.
5. Cambie la configuración existente a la configuración nueva y, luego, seleccione **Guardar**.

**Comandos**

- CLI de Azure: [az network route-table route update](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_update)
- PowerShell: [Set-AzureRmRouteConfig](/powershell/module/azurerm.network/set-azurermrouteconfig)

## <a name="delete-a-route"></a>Eliminación de una ruta

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba *tablas de rutas*. Cuando la opción **Tablas de rutas** aparezca en los resultados de la búsqueda, selecciónela.
2. Seleccione la tabla de rutas de la que desea eliminar una ruta.
3. Seleccione **Rutas**.
4. En la lista de rutas, seleccione **...** en el lado derecho de la ruta que desea eliminar.
5. Seleccione **Eliminar** y, luego, seleccione **Sí**.

**Comandos**

- CLI de Azure: [az network route-table route delete](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_delete)
- PowerShell: [Remove-AzureRmRouteConfig](/powershell/module/azurerm.network/remove-azurermrouteconfig)

## <a name="view-effective-routes"></a>Vista de las rutas eficaces

Las rutas eficaces de cada interfaz de red conectada a una máquina virtual son una combinación de las tablas de rutas que creó, las rutas predeterminadas de Azure y cualquier ruta propagada desde las redes locales vía BGP a través de una puerta de enlace de red virtual de Azure. Conocer las rutas eficaces de una interfaz de red resulta útil para solucionar los problemas con el enrutamiento. Puede ver las rutas eficaces de cualquier interfaz de red conectada a una máquina virtual en ejecución.

1. En el cuadro de búsqueda que se encuentra en la parte superior del portal, escriba el nombre de una máquina virtual cuyas rutas eficaces desea ver. Si no conoce el nombre de una máquina virtual, escriba *máquinas virtuales* en el cuadro de búsqueda. Cuando la opción **Máquinas virtuales** aparezca en los resultados de la búsqueda, selecciónela y seleccione una máquina virtual en la lista.
2. Seleccione **Redes** en **CONFIGURACIÓN**.
3. Seleccione el nombre de una interfaz de red.
4. Seleccione **Rutas eficaces** en **SOPORTE Y SOLUCIÓN DE PROBLEMAS**.
5. Revise la lista de las rutas eficaces para determinar si existe la ruta correcta a la que desea enrutar el tráfico. Obtenga más información sobre los tipos de próximo salto que ve en esta lista en [Enrutamiento del tráfico de redes virtuales](virtual-networks-udr-overview.md).

**Comandos**

- CLI de Azure: [az network nic show-effective-route-table](/cli/azure/network/nic?view=azure-cli-latest#az_network_nic_show_effective_route_table)
- PowerShell: [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/remove-azurermrouteconfig) 

## <a name="validate-routing-between-two-endpoints"></a>Validación del enrutamiento entre dos puntos de conexión

Puede determinar el tipo de próximo paso entre una máquina virtual y la dirección IP de otro recurso de Azure, un recurso local o un recurso de Internet. Determinar el enrutamiento de Azure resulta útil para solucionar los problemas con el enrutamiento. Para completar esta tarea, debe haber una instancia de Network Watcher. Si no tiene una instancia de Network Watcher, complete los pasos que aparecen en [Creación de una instancia de Azure Network Watcher](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para crear una.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba *network watcher*. Cuando aparezca la opción **Network Watcher** en los resultados de búsqueda, selecciónela.
2. Seleccione **Próximo paso** en **HERRAMIENTAS DE DIAGNÓSTICO DE RED**.
3. Seleccione la **suscripción** y el **grupo de recursos** de la máquina virtual de origen desde la que desea validar el enrutamiento.
4. Seleccione la **máquina virtual**, la **interfaz de red** conectada a la máquina virtual y la **dirección IP de origen** asignada a la interfaz de red desde la que desea validar el enrutamiento.
5. Escriba la **dirección IP de destino** a la que desea validar el enrutamiento.
6. Seleccione **Próximo paso**.
7. Tras una breve espera, se devuelve información que le indica el tipo de próximo paso y el identificador de la ruta que enrutó el tráfico. Obtenga más información sobre los tipos de próximo salto que aquí se devuelven en [Enrutamiento del tráfico de redes virtuales](virtual-networks-udr-overview.md).

**Comandos**

- CLI de Azure: [az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az_network_watcher_show_next_hop)
- PowerShell: [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) 
 
## <a name="permissions"></a>Permisos

Para realizar tareas en rutas y en tablas de rutas, la cuenta debe estar asignada al rol de [colaborador de red](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un rol [personalizado](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que tenga asignados los permisos adecuados que se muestran en la tabla siguiente:

|Operación                                                       |   Nombre de la operación                               |
|--------------------------------------------------------------  |   -------------------------------------------  |
|Microsoft.Network/routeTables/read                              |   Obtención de una tabla de rutas                              |
|Microsoft.Network/routeTables/write                             |   Creación o actualización de una tabla de rutas                 |
|Microsoft.Network/routeTables/delete                            |   Eliminación de una tabla de rutas                           |
|Microsoft.Network/routeTables/join/action                       |   Unión a una tabla de rutas                             |
|Microsoft.Network/routeTables/routes/read                       |   Obtención de una ruta                                    |
|Microsoft.Network/routeTables/routes/write                      |   Creación o actualización de una ruta                       |
|Microsoft.Network/routeTables/routes/delete                     |   Eliminación de una ruta                                 |
|Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Obtención de una tabla de rutas eficaces de una interfaz de red  | 
|Microsoft.Network/networkWatchers/nextHop/action                |   Obtención del próximo salto de una VM                  |

La operación *Unión a una tabla de rutas* es necesaria para asociar una tabla de rutas a una subred.