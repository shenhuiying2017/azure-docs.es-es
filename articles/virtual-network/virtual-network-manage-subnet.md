---
title: "Incorporación, cambio o eliminación de una subred de red virtual de Azure | Microsoft Docs"
description: "Obtenga información sobre la incorporación, la modificación o la eliminación de una subred de red virtual en Azure."
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
ms.openlocfilehash: 85ba6ef3e51c339a77eb9b4198c4f87e2a64cf09
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Incorporación, cambio o eliminación de una subred de red virtual

Obtenga información sobre la incorporación, la modificación o la eliminación de una subred de red virtual. 

Si no está familiarizado con las redes virtuales, antes de agregar una subred, cambiarla o eliminarla, es recomendable leer la [introducción a Azure Virtual Network](virtual-networks-overview.md) y [Crear, cambiar o eliminar una red virtual](virtual-network-manage-network.md). Todos los recursos de Azure implementados en una red virtual se implementan en una subred de esa red virtual. Normalmente, se crean varias subredes en una red virtual para:
- **Filtrar el tráfico entre subredes**. Puede aplicar grupos de seguridad de red a subredes para filtrar el tráfico de red entrante y saliente de todos aquellos recursos (como máquinas virtuales) que se encuentran en la red virtual. Para obtener más información sobre cómo crear grupos de seguridad de red, consulte [Creación de grupos de seguridad de red](virtual-networks-create-nsg-arm-pportal.md).
- **Controlar el enrutamiento entre subredes**. Azure crea rutas predeterminadas para que el tráfico se enrute automáticamente entre subredes. Puede reemplazar las rutas predeterminadas de Azure creando rutas definidas por el usuario. Para obtener más información sobre las rutas definidas por el usuario, consulte [Creación de rutas definidas por el usuario](virtual-network-create-udr-arm-ps.md). 

En este artículo se explica cómo agregar, cambiar y eliminar una subred de una red virtual creada mediante el modelo de implementación de Azure Resource Manager.
 
## <a name="before"></a>Antes de empezar

Antes de comenzar las tareas que se describen en este artículo, lleve a cabo las siguientes tareas previas necesarias:

- Si no está familiarizado con el trabajo con redes virtuales, se recomienda que revise el ejercicio de [Creación de su primera red virtual](virtual-network-get-started-vnet-subnet.md). Este ejercicio puede ayudarle a familiarizarse más con las redes virtuales.
- Para obtener información sobre los límites de las redes virtuales, revise los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Inicie sesión con la cuenta de Azure en Azure Portal, en la interfaz de la línea de comandos (CLI de Azure) de Azure o en Azure PowerShell. Si no tiene una cuenta de Azure, regístrese para obtener una [cuenta de prueba gratuita](https://azure.microsoft.com/free).
- Si tiene pensado usar comandos de PowerShell para realizar las tareas descritas en este artículo, primero debe [instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de los cmdlets de Azure PowerShell. Para obtener ayuda para los comandos de PowerShell de los ejemplos, escriba `get-help <command> -full`.
- Si tiene pensado usar comandos de la CLI de Azure para realizar las tareas indicadas en este artículo, debe realizar una de estas dos acciones:
    - [Instale y configure la CLI de Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de la CLI de Azure.
    - Use Azure Cloud Shell. En lugar de instalar la CLI y sus dependencias, puede usar Azure Cloud Shell. Azure Cloud Shell es un shell de Bash gratuito que se puede ejecutar directamente en Azure Portal. Tiene la CLI de Azure preinstalada y configurada para utilizar con su cuenta. Para usar Cloud Shell, haga clic en el icono Cloud Shell (**>_**) en la parte superior de Azure Portal. 

  Para obtener ayuda con los comandos de la CLI de Azure, escriba `az <command> --help`.

## <a name="create-subnet"></a>Agregar una subred

Para agregar una subred:

1. Inicie sesión en el [portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol Colaborador de la red para la suscripción. Para obtener más información sobre la asignación de roles y permisos a las cuentas, consulte [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. En el cuadro de búsqueda del portal, escriba **redes virtuales**. En los resultados de la búsqueda, haga clic en **Redes virtuales**.
3. En la hoja **Redes virtuales** que aparece, haga clic en la red virtual a la cual quiere agregar una subred.
4. En la hoja Redes virtuales, haga clic en **Subredes**.
5. Haga clic en **+Subred**.
6. En la hoja **Agregar subred**, especifique valores para los parámetros siguientes:
    - **Nombre:** debe ser único dentro de la red virtual.
    - **Intervalo de direcciones**: debe estar dentro del espacio de direcciones de la red virtual. Este intervalo no puede superponerse con otros intervalos de direcciones de subred dentro de la red virtual. El espacio de direcciones debe especificarse mediante notación de Enrutamiento de interdominios sin clases (CIDR). Por ejemplo, en una red virtual con el espacio de direcciones 10.0.0.0/16, podría definir el espacio de direcciones de subred 10.0.0.0/24. El menor intervalo que se puede especificar es /29, lo que proporciona ocho direcciones IP de subred. De conformidad con el protocolo, Azure reserva la primera y la última dirección de cada subred. Otras tres direcciones están reservadas para el uso del servicio de Azure. Como resultado, al definir una subred con el intervalo de direcciones /29, se generan tres direcciones IP utilizables en la subred. Si tiene pensado conectar una red virtual a una VPN Gateway, debe crear una subred de puerta de enlace. Más información sobre las [consideraciones específicas del intervalo de direcciones de las subredes de puerta de enlace](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). En determinadas circunstancias se puede cambiar el intervalo de direcciones una vez agregada la subred. Para obtener información sobre cómo cambiar un intervalo de direcciones de subred, consulte [Cambio de configuración de subred](#change-subnet) en este artículo.
    - **Grupo de seguridad de red:** de forma opcional, puede asociar un grupo de seguridad de red existente a la subred para controlar el filtrado de tráfico de red entrante y saliente de la subred. El grupo de seguridad de red debe existir en la misma suscripción y la misma ubicación que la red virtual. También debe crearse con el modelo de implementación de Resource Manager. Para obtener más información sobre cómo crear grupos de seguridad de red, consulte [Grupos de seguridad de red](virtual-networks-create-nsg-arm-pportal.md).
    - **Tabla de rutas:** de forma opcional, puede asociar una tabla de rutas existente a la subred para controlar el enrutamiento de tráfico de red a otras redes. La tabla de rutas debe existir en la misma suscripción y la misma ubicación que la red virtual. También debe crearse con el modelo de implementación de Resource Manager. Para obtener más información sobre la creación de tablas de rutas, consulte [Rutas definidas por el usuario](virtual-network-create-udr-arm-ps.md).
    - **Usuarios**: puede controlar el acceso a la subred con los roles integrados u otros personalizados. Para obtener más información sobre la asignación de roles y usuarios para el acceso a la subred, consulte [Asignación de roles para administrar el acceso a los recursos de Azure](../active-directory/role-based-access-control-configure.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access).
7. Para agregar la subred a la máquina virtual seleccionada, haga clic en **Aceptar**.

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI de Azure|[az network vnet subnet create](/cli/azure/network/vnet/subnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json), [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet"></a>Cambio de configuración de subred

Puede cambiar grupos de seguridad de red, tablas de rutas y acceso de usuarios a una subred si administra los recursos de una subred. Para más información sobre esta configuración, vea el paso 6 de [Agregar una subred](#create-subnet). Si quiere cambiar el espacio de direcciones de una subred, primero debe eliminar cualquier recurso de ella. Los pasos necesarios para eliminar un recurso varían según el recurso. Para más información sobre cómo eliminar recursos de subredes, lea los documentos de cada tipo de recurso que quiera eliminar. Para cambiar el intervalo de direcciones de una subred:

1. Inicie sesión en el [portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol Colaborador de la red para la suscripción. Para obtener más información sobre la asignación de roles y permisos a las cuentas, consulte [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. En el cuadro de búsqueda del portal, escriba **redes virtuales**. En los resultados de la búsqueda, haga clic en **Redes virtuales**.
3. En la hoja **Redes virtuales**, haga clic en la red virtual para la que quiere cambiar el intervalo de direcciones de subred.
4. Haga clic en la subred cuyo intervalo de direcciones quiere cambiar.
5. Especifique el nuevo intervalo de direcciones en el cuadro **Intervalo de direcciones** de la hoja de la subred. El intervalo debe ser único dentro del espacio de direcciones de la red virtual. Este intervalo no puede superponerse con otros intervalos de direcciones de subred dentro de la red virtual. El espacio de direcciones debe especificarse con notación de CIDR. Por ejemplo, en una red virtual con el espacio de direcciones 10.0.0.0/16, podría definir el espacio de direcciones de subred 10.0.0.0/24. El menor intervalo que se puede especificar es /29, lo que proporciona ocho direcciones IP de subred. De conformidad con el protocolo, Azure reserva la primera y la última dirección de cada subred. Otras tres direcciones están reservadas para el uso del servicio de Azure. Como resultado, una subred con un intervalo de direcciones /29 tiene tres direcciones IP utilizables. Si tiene pensado conectar una red virtual a una VPN Gateway, debe crear una subred de puerta de enlace. Más información sobre las [consideraciones específicas del intervalo de direcciones de las subredes de puerta de enlace](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). En determinadas circunstancias se puede cambiar el intervalo de direcciones una vez creada la subred. Para obtener información sobre cómo cambiar un intervalo de direcciones de subred, consulte [Cambio de configuración de subred](#change-subnet) en este artículo.
6. Haga clic en **Guardar**.

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI de Azure|[az network vnet subnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-subnet"></a>Eliminación de una subred

Solo se puede eliminar una subred si no tiene recursos. Si hay recursos en la subred, debe eliminarlos para poder eliminar la subred. Los pasos necesarios para eliminar un recurso varían según el recurso. Para más información sobre cómo eliminar recursos de subredes, lea los documentos de cada tipo de recurso que quiera eliminar. Para eliminar una subred:

1. Inicie sesión en el [portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol Colaborador de la red para la suscripción. Para obtener más información sobre la asignación de roles y permisos a las cuentas, consulte [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. En el cuadro de búsqueda del portal, escriba **redes virtuales**. En los resultados de la búsqueda, haga clic en **Redes virtuales**.
3. En la hoja **Redes virtuales**, haga clic en la red virtual de la cual quiere eliminar una subred.
4. En la hoja de la red virtual, en **CONFIGURACIÓN**, haga clic en **Subredes**.
5. En la lista de subredes que aparece en la hoja de subredes, haga clic con el botón derecho en la que quiere eliminar, haga clic en **Eliminar** y, a continuación, en **Sí** para eliminar la subred.

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI de Azure|[az network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>Pasos siguientes

Para crear una máquina virtual en una subred, vea [Create a virtual network and deploy VMs in the subnet (Crear una red virtual e implementar máquinas virtuales en la subred)](virtual-network-get-started-vnet-subnet.md#create-vms).
