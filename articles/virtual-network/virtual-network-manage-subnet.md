---
title: Incorporación, cambio o eliminación de una subred de red virtual de Azure | Microsoft Docs
description: Obtenga información sobre la incorporación, la modificación o la eliminación de una subred de red virtual en Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: 68d4c54b2648dc3b40e69dcde9828d18de318796
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894466"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Incorporación, cambio o eliminación de una subred de red virtual

Obtenga información sobre la incorporación, la modificación o la eliminación de una subred de red virtual. Todos los recursos de Azure implementados en una red virtual se implementan en una subred de esa red virtual. Si no está familiarizado con las redes virtuales, puede obtener más información sobre ellas en la [información general sobre redes virtuales](virtual-networks-overview.md) o siguiendo un [tutorial](quick-create-portal.md). Para crear, cambiar o eliminar una red virtual, vea [Manage a virtual network](manage-virtual-network.md) (Administrar una red virtual).

## <a name="before-you-begin"></a>Antes de empezar

Complete las tareas siguientes antes de seguir los pasos de las secciones de este artículo:

- Si todavía no tiene una cuenta de Azure, regístrese para obtener una [cuenta de evaluación gratuita](https://azure.microsoft.com/free).
- Si usa el portal, abra https://portal.azure.com e inicie sesión con la cuenta de Azure.
- Si usa comandos de PowerShell para completar las tareas de este artículo, ejecute los comandos que se encuentran en [Azure Cloud Shell](https://shell.azure.com/powershell) o ejecute PowerShell en el equipo. Azure Cloud Shell es un shell interactivo gratuito que puede usar para ejecutar los pasos de este artículo. Tiene las herramientas comunes de Azure preinstaladas y configuradas para usarlas en la cuenta. Para seguir este tutorial se necesita la versión 5.7.0 del módulo de Azure PowerShell u otra posterior. Ejecute `Get-Module -ListAvailable AzureRM` para buscar la versión instalada. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Connect-AzureRmAccount` para crear una conexión con Azure.
- Si usa la interfaz de la línea de comandos (CLI) de Azure para completar las tareas de este artículo, ejecute los comandos que se encuentran en [Azure Cloud Shell](https://shell.azure.com/bash) o ejecute la CLI en el equipo. Para seguir este tutorial se necesita la versión 2.0.31 de la CLI de Azure o una versión posterior. Ejecute `az --version` para buscar la versión instalada. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli). Si ejecuta de forma local la CLI de Azure, también debe ejecutar `az login` para crear una conexión con Azure.

La cuenta en la que inicia sesión o con la que se conecta a Azure debe tener asignado el rol de [colaborador de red](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o un [rol personalizado](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que tenga asignadas las acciones apropiadas en [Permisos](#permissions).

## <a name="add-a-subnet"></a>Incorporación de una subred

1. En el cuadro de búsqueda de la parte superior del portal, escriba *redes virtuales*. Cuando aparezca la opción **Redes virtuales** en los resultados de la búsqueda, selecciónela.
2. En la lista de redes virtuales, seleccione la red virtual que en la que desea agregar una subred.
3. En **CONFIGURACIÓN**, seleccione **Subredes**.
4. Seleccione **+Subred**.
5. Escriba valores para estos parámetros:
    - **Nombre:** debe ser único dentro de la red virtual.
    - **Intervalo de direcciones**: debe estar dentro del espacio de direcciones de la red virtual. Este intervalo no puede superponerse con otros intervalos de direcciones de subred dentro de la red virtual. El espacio de direcciones debe especificarse mediante notación de Enrutamiento de interdominios sin clases (CIDR). Por ejemplo, en una red virtual con el espacio de direcciones 10.0.0.0/16, podría definir el espacio de direcciones de subred 10.0.0.0/24. El menor intervalo que se puede especificar es /29, lo que proporciona ocho direcciones IP de subred. De conformidad con el protocolo, Azure reserva la primera y la última dirección de cada subred. Otras tres direcciones están reservadas para el uso del servicio de Azure. Como resultado, al definir una subred con el intervalo de direcciones /29, se generan tres direcciones IP utilizables en la subred. Si planea conectar una red virtual a una puerta de enlace VPN, debe crear una subred de puerta de enlace. Más información sobre las [consideraciones específicas del intervalo de direcciones de las subredes de puerta de enlace](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). En determinadas circunstancias se puede cambiar el intervalo de direcciones una vez agregada la subred. Para información sobre cómo cambiar un intervalo de direcciones de subred, consulte [Cambio de configuración de subred](#change-subnet-settings).
    - **Grupo de seguridad de red:** puede no asociar ningún grupo de seguridad de red o asociar uno existente a una subred para filtrar el tráfico de red entrante y saliente de la subred. El grupo de seguridad de red debe existir en la misma suscripción y la misma ubicación que la red virtual. Obtenga más información sobre los [grupos de seguridad de red](security-overview.md) y [cómo crear un grupo de seguridad de red](tutorial-filter-network-traffic.md).
    - **Tabla de rutas:** puede no asociar ninguna tabla de rutas o asociar una existente a una subred para controlar el enrutamiento del tráfico de red a otras redes. La tabla de rutas debe existir en la misma suscripción y la misma ubicación que la red virtual. Obtenga más información sobre el [enrutamiento de Azure](virtual-networks-udr-overview.md) y [cómo crear una tabla de rutas](tutorial-create-route-table-portal.md).
    - **Puntos de conexión de servicio:** una subred puede tener habilitados varios puntos de conexión de servicio o ninguno. Para habilitar un punto de conexión de servicio, seleccione el servicio o los servicios para los que desea habilitar los puntos de conexión en la lista de **servicios**. La ubicación se configura automáticamente para un punto de conexión. De forma predeterminada, los puntos de conexión de servicio están configurados para la región de la red virtual. En el caso de Azure Storage, para admitir escenarios de conmutación por error regionales, los puntos de conexión se configuran automáticamente para las [regiones emparejadas de Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).

    Para quitar un punto de conexión de servicio, anule la selección del servicio para el que desea quitar el punto de conexión. Para más información sobre los puntos de conexión de servicio y sobre los servicios para los que se pueden habilitar, vea la [información general sobre los puntos de conexión de servicio de las redes virtuales](virtual-network-service-endpoints-overview.md). Una vez que habilita un punto de conexión para un servicio, también debe habilitar el acceso de red para la subred de un recurso creado con el servicio. Por ejemplo, si habilita el punto de conexión de servicio para *Microsoft.Storage*, también debe habilitar el acceso de red a todas las cuentas de Azure Storage a las que desea conceder acceso de red. Para detalles sobre cómo habilitar el acceso de red a las subredes para las que está habilitado un punto de conexión de servicio, consulte la documentación correspondiente al servicio individual para el que habilitó el punto de conexión de servicio.

    Para validar que un punto de conexión de servicio está habilitado para una subred, consulte las [rutas efectivas](virtual-network-routes-troubleshoot-portal.md#view-effective-routes-for-a-virtual-machine) para cualquier interfaz de red de la subred. Cuando se configura un punto de conexión, verá una ruta *predeterminada* con los prefijos de dirección del servicio y un nextHopType de **VirtualNetworkServiceEndpoint**. Para más información sobre el enrutamiento, vea la [introducción al enrutamiento](virtual-networks-udr-overview.md).
6. Para agregar la subred a la red virtual que seleccionó, seleccione **Aceptar**.

**Comandos**

- CLI de Azure: [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)
- PowerShell: [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>Modificación de la configuración de subred

1. En el cuadro de búsqueda de la parte superior del portal, escriba *redes virtuales*. Cuando aparezca la opción **Redes virtuales** en los resultados de la búsqueda, selecciónela.
2. En la lista de redes virtuales, seleccione la red virtual que contiene la subred para la que desea modificar la configuración.
3. En **CONFIGURACIÓN**, seleccione **Subredes**.
4. En la lista de subredes, seleccione la subred para la que desea modificar la configuración. Puede cambiar la configuración siguiente:

    - **Intervalo de direcciones:** si no hay recursos implementados dentro de la subred, es posible modificar el intervalo de direcciones. Si existe algún recurso en la subred, primero deberá moverlos a otra subred o eliminarlos. Los pasos necesarios para mover o eliminar un recurso varían según el recurso. Para información sobre cómo mover o eliminar los recursos que se encuentran en las subredes, lea la documentación de cada tipo de recurso que desea mover o eliminar. Consulte cuáles son las restricciones para **Intervalo de direcciones** en el paso 5 de [Incorporación de una subred](#add-a-subnet).
    - **Usuarios**: puede controlar el acceso a la subred con los roles integrados u otros personalizados. Para obtener más información sobre la asignación de roles y usuarios para el acceso a la subred, consulte [Asignación de roles para administrar el acceso a los recursos de Azure](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access).
    - **Grupo de seguridad de red** y **Tabla de rutas**: vea el paso 5 de [Agregar una subred](#add-a-subnet).
    - **Puntos de conexión de servicio**: vea los puntos de conexión de servicio del paso 5 de [Agregar una subred](#add-a-subnet). A la hora de habilitar un punto de conexión de servicio para una red existente, asegúrese de que no se esté ejecutando ninguna tarea crítica en ningún recurso de la subred. Los puntos de conexión de servicio cambian las rutas en cada interfaz de red de la subred: pasan de usar la ruta predeterminada con el prefijo de dirección *0.0.0.0/0* y el tipo de próximo salto *Internet* a usar una ruta nueva con los prefijos de dirección del servicio y el tipo de próximo salto *VirtualNetworkServiceEndpoint*. Durante el cambio se puede terminar cualquier conexión TCP que esté abierta. El punto de conexión de servicio no se habilita hasta que los flujos de tráfico al servicio de todas las interfaces de red se actualicen con la nueva ruta. Para más información sobre el enrutamiento, vea la [introducción al enrutamiento](virtual-networks-udr-overview.md).
5. Seleccione **Guardar**.

**Comandos**

- CLI de Azure: [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>Eliminación de una subred

Solo se puede eliminar una subred si no tiene recursos. Si hay recursos en la subred, debe eliminarlos para poder eliminar la subred. Los pasos necesarios para eliminar un recurso varían según el recurso. Para más información sobre cómo eliminar recursos de subredes, lea los documentos de cada tipo de recurso que quiera eliminar.

1. En el cuadro de búsqueda de la parte superior del portal, escriba *redes virtuales*. Cuando aparezca la opción **Redes virtuales** en los resultados de la búsqueda, selecciónela.
2. En la lista de redes virtuales, seleccione la red virtual que contiene la subred que desea eliminar.
3. En **CONFIGURACIÓN**, seleccione **Subredes**.
4. En la lista de subredes, seleccione **...**, que se encuentra a la derecha, para la subred que desea eliminar.
5. Seleccione **Eliminar** y, luego, seleccione **Sí**.

**Comandos**

- CLI de Azure: [az network vnet delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- PowerShell: [Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Permisos

Para llevar a cabo tareas en subredes, su cuenta debe estar asignada al rol de [colaborador de red](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un rol [personalizado](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que tenga asignadas las acciones adecuadas que se muestran en la tabla siguiente:

|.                                                                   |   NOMBRE                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Leer una subred de red virtual              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Crear o actualizar una subred de red virtual  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Eliminar una subred de red virtual            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Unirse a una red virtual                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Habilitar un punto de conexión de servicio para una subred     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Obtener las máquinas virtuales de una subred       |

## <a name="next-steps"></a>Pasos siguientes

- Crear una red virtual y subredes con scripts de ejemplo de [PowerShell](powershell-samples.md) o de la [CLI de Azure](cli-samples.md) o con [plantillas de Azure Resource Manager](template-samples.md)
- Crear y aplicar una [directiva de Azure](policy-samples.md) para redes virtuales