---
title: Crear, cambiar o eliminar un emparejamiento de red virtual de Azure | Microsoft Docs
description: Aprenda a crear, cambiar o eliminar un emparejamiento de red virtual.
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
ms.author: jdial;anavin
ms.openlocfilehash: 8fa7496c3489a0a3e5ee6d829bbeef0f0ccdf315
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Crear, cambiar o eliminar un emparejamiento de red virtual

Aprenda a crear, cambiar o eliminar un emparejamiento de red virtual. El emparejamiento de red virtual permite conectar redes virtuales mediante la red troncal de Azure. Una vez emparejadas, las redes virtuales se siguen administrando como recursos separados. Si no está familiarizado con el emparejamiento de red virtual, se recomienda que lea la [introducción al emparejamiento de redes virtuales](virtual-network-peering-overview.md) y que complete el tutorial en el que se describe cómo [crear un emparejamiento de red virtual](tutorial-connect-virtual-networks-portal.md) antes de completar las tareas de este artículo.

El emparejamiento de redes virtuales en las mismas regiones tiene disponibilidad general. El emparejamiento de redes virtuales de diferentes regiones actualmente se encuentra en versión preliminar. Consulte las [Actualizaciones de redes virtuales](https://azure.microsoft.com/updates/?product=virtual-network) para ver las regiones disponibles. Debe [registrar una suscripción para la versión preliminar](tutorial-connect-virtual-networks-powershell.md#register).

> [!WARNING]
> Los emparejamientos de redes virtuales que se crean en este escenario no pueden tener el mismo nivel de disponibilidad y confiabilidad que los escenarios de la versión de disponibilidad general. Es posible que los emparejamientos de redes virtuales tengan funcionalidades limitadas y no estén disponibles en todas las regiones de Azure. Para las notificaciones más al día sobre disponibilidad y estado de esta característica, consulte la página de [actualizaciones de Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network) .
>

## <a name="before-you-begin"></a>Antes de empezar

Complete las tareas siguientes antes de seguir los pasos de las secciones de este artículo:

- Si todavía no tiene una cuenta de Azure, regístrese para obtener una [cuenta de evaluación gratuita](https://azure.microsoft.com/free).
- Si usa el portal, abra https://portal.azure.com e inicie sesión con la cuenta de Azure.
- Si usa comandos de PowerShell para completar las tareas de este artículo, ejecute los comandos que se encuentran en [Azure Cloud Shell](https://shell.azure.com/powershell) o ejecute PowerShell en el equipo. Azure Cloud Shell es un shell interactivo gratuito que puede usar para ejecutar los pasos de este artículo. Tiene las herramientas comunes de Azure preinstaladas y configuradas para usarlas en la cuenta. Para realizar este tutorial es necesaria la versión 5.2.0 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para buscar la versión instalada. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure.
- Si usa la interfaz de la línea de comandos (CLI) de Azure para completar las tareas de este artículo, ejecute los comandos que se encuentran en [Azure Cloud Shell](https://shell.azure.com/bash) o ejecute la CLI en el equipo. Para realizar este tutorial es necesaria la versión 2.0.26 o superior de la CLI de Azure. Ejecute `az --version` para buscar la versión instalada. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli). Si ejecuta de forma local la CLI de Azure, también debe ejecutar `az login` para crear una conexión con Azure.

## <a name="create-a-peering"></a>Creación de un emparejamiento

>[!NOTE]
>Antes de crear un emparejamiento, asegúrese de conocer los [requisitos y las restricciones](#requirements-and-constraints), así como los [permisos requeridos](#permissions).
>

1. En el cuadro de búsqueda de la parte superior del portal, escriba *redes virtuales*. Cuando aparezca la opción **Redes virtuales** en los resultados de búsqueda, selecciónela. No seleccione **Redes virtuales (clásico)** si aparece en la lista, ya que no se puede crear un emparejamiento de una red virtual implementada mediante el modelo de implementación clásica.
2. Seleccione en la lista la red virtual que quiera emparejar.
3. En la lista de redes virtuales, seleccione la red virtual que quiera emparejar.
4. En **CONFIGURACIÓN**, seleccione **Emparejamientos**.
5. Seleccione **+Agregar**. 
6. <a name="add-peering"></a>Escriba o seleccione valores para la siguiente configuración:
    - **Nombre:** el nombre del emparejamiento debe ser único dentro de la red virtual.
    - **Modelo de implementación de red virtual:** seleccione con qué modelo de implementación se implementó la red virtual con la que quiere realizar el emparejamiento.
    - **Conozco mi id. de recurso:** si tiene acceso de lectura a la red virtual con la que quiere realizar el emparejamiento, no active esta casilla. Si no tiene acceso de lectura a la red virtual o la suscripción con la que quiere realizar el emparejamiento, active esta casilla. Escriba el identificador de recurso completo de la red virtual con la que quiere realizar el emparejamiento en el cuadro **Id. de recurso** que aparece cuando se activa la casilla. El identificador de recurso que especifique debe ser para una red virtual que exista en la misma [región](https://azure.microsoft.com/regions) de Azure que esta red virtual. Si desea seleccionar una red virtual en una región distinta, [registre su suscripción para la versión preliminar.](tutorial-connect-virtual-networks-portal.md) El identificador de recurso completo es similar a /subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>. Para obtener el identificador de recurso de una red virtual, vea las propiedades de la red virtual. Para obtener información sobre cómo ver las propiedades de una red virtual, consulte [Administración de redes virtuales](manage-virtual-network.md#view-virtual-networks-and-settings).
    - **Suscripción:** seleccione la [suscripción](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) de la red virtual con la que quiere realizar el emparejamiento. Se muestran una o varias suscripciones, en función del número de suscripciones al que tenga acceso de lectura su cuenta. Si ha activado la casilla **Id. de recurso**, esta opción no está disponible.
    - **Red virtual:** seleccione la red virtual con la que quiere realizar el emparejamiento. Puede seleccionar una red virtual creada a través de cualquier modelo de implementación de Azure. Si desea seleccionar una red virtual en una región distinta, [registre su suscripción para la versión preliminar.](tutorial-connect-virtual-networks-portal.md) Debe tener acceso de lectura a la red virtual para que sea visible en la lista. Si una red virtual aparece en gris, puede deberse a que el espacio de direcciones de la red virtual se superpone con el espacio de direcciones de esta red virtual. Si los espacios de direcciones de las redes virtuales se superponen, no se pueden emparejar. Si ha activado la casilla **Id. de recurso**, esta opción no está disponible.
    - **Permitir acceso a red virtual:** seleccione **Habilitado** (valor predeterminado) si quiere habilitar la comunicación entre las dos redes virtuales. Al habilitar la comunicación entre las redes virtuales, permite que los recursos conectados a cualquier red virtual se comuniquen entre sí con el mismo ancho de banda y latencia que si estuvieran conectados a la misma red virtual. Todas las comunicaciones entre los recursos de las dos redes virtuales se realizan a través de la red privada de Azure. La etiqueta predeterminada **VirtualNetwork** para los grupos de seguridad de red abarca la red virtual y la red virtual emparejada. Para obtener más información sobre las etiquetas predeterminadas de los grupos de seguridad de red, lea el artículo de [introducción a los grupos de seguridad de red](virtual-networks-nsg.md#default-tags).  Seleccione **Deshabilitado** si no quiere que el tráfico fluya a la red virtual emparejada. Por ejemplo, podría seleccionar **Deshabilitado** si ha emparejado una red virtual con otra red virtual pero quiere deshabilitar en ocasiones el flujo de tráfico entre ambas redes. Le resultará más cómoda la opción de habilitar y deshabilitar que eliminar y volver a crear emparejamientos. Si esta opción está deshabilitada, el tráfico no fluye entre las redes virtuales emparejadas.
    - **Permitir tráfico reenviado:** seleccione esta casilla para permitir que el tráfico que *reenvió* una aplicación virtual de red desde una red virtual (este tráfico no se origina en la red virtual) fluya a esta red virtual mediante un emparejamiento. Por ejemplo, supongamos que hay tres redes virtuales llamadas Radio1, Radio2 y Concentrador. Existe un emparejamiento entre cada red virtual de radio y la red virtual de concentrador, pero no existe ninguno entre las redes virtuales de radio. Se implementa una aplicación virtual de red en la red virtual de concentrador, y las rutas definidas por el usuario se aplican a cada red virtual de radio que redirige el tráfico entre las subredes a través de la aplicación virtual de red. Si esta casilla no está seleccionada para realizar el emparejamiento entre cada red virtual de radio y la red virtual de concentrador, el tráfico no fluye entre las redes virtuales de radio porque el concentrador reenvía el tráfico entre las redes virtuales. Aunque el hecho de habilitar esta funcionalidad permite el tráfico reenviado a través del emparejamiento, no se crean rutas definidas por el usuario ni aplicaciones virtuales de red. Las rutas definidas por el usuario y las aplicaciones virtuales de red se crean por separado. Obtenga información sobre las [rutas definidas por el usuario](virtual-networks-udr-overview.md#user-defined). No es necesario comprobar esta configuración si se reenvía el tráfico entre redes virtuales a través de VPN Gateway de Azure.
    - **Permitir tránsito de puerta de enlace:** active esta casilla si tiene una puerta de enlace de red virtual asociada a esta red virtual y quiere permitir que el tráfico procedente de la red virtual emparejada fluya a través de la puerta de enlace. Por ejemplo, esta red virtual puede asociarse a una red local a través de una puerta de enlace de red virtual. La puerta de enlace puede ser una puerta de enlace de ExpressRoute o VPN. La selección de esta casilla permite que el tráfico procedente de la red virtual emparejada fluya a través de la puerta de enlace asociada a esta red virtual hacia la red local. Si activa esta casilla, la red virtual emparejada no puede tener una puerta de enlace configurada. La red virtual emparejada debe tener seleccionada la casilla **Usar puertas de enlace remotas** cuando se configura el emparejamiento de la otra red virtual a esta red virtual. Si deja esta casilla sin activar (valor predeterminado), el tráfico procedente de la red virtual emparejada seguirá fluyendo a esta red virtual, pero no podrá fluir a través de una puerta de enlace de red virtual asociada a esta red virtual. 
    
    Además de reenviar tráfico a una red local, una puerta de enlace VPN puede reenviar el tráfico de red entre redes virtuales emparejadas con la red virtual en que se encuentra la puerta de enlace, sin necesidad de que las redes virtuales se tengan que emparejar entre sí. Esto es útil cuando desea utilizar una puerta de enlace VPN en una red virtual de concentrador (vea el ejemplo de concentrador y radio descrito para **Permitir tráfico reenviado**) para redirigir el tráfico entre redes virtuales de radio que no están emparejadas entre sí. Obtenga más información sobre las [puertas de enlace de red virtual](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti). Este escenario requiere la implementación de las rutas definidas por el usuario que especifican la puerta de enlace de red virtual como el tipo de próximo salto. Obtenga información sobre las [rutas definidas por el usuario](virtual-networks-udr-overview.md#user-defined). Solo puede especificar una puerta de enlace VPN como un tipo de próximo salto en una ruta definida por el usuario; no puede especificar una puerta de enlace de ExpressRoute como el tipo de próximo salto en una ruta definida por el usuario.

        You cannot enable this option if you're peering a virtual network (Resource Manager) with a virtual network (classic). Though the traffic flows between the two virtual networks, the virtual network (classic) traffic cannot flow through a network gateway attached to the virtual network (Resource Manager). 

    - **Usar puertas de enlace remotas:** active esta casilla para permitir que el tráfico procedente de esta red virtual fluya a través de la puerta de enlace de red virtual asociada a la red virtual con la que está realizando el emparejamiento. Por ejemplo, la red virtual con la que está realizando el emparejamiento tiene una puerta de enlace de VPN asociada que permite la comunicación a una red local.  La activación de esta casilla permite que el tráfico procedente de esta red virtual fluya a través de la puerta de enlace de VPN asociada a la red virtual emparejada. Si activa esta casilla, la red virtual emparejada debe tener asociada una puerta de enlace de red virtual y debe tener activada la casilla **Permitir tránsito de puerta de enlace**. Si deja esta casilla sin activar (valor predeterminado), el tráfico procedente de la red virtual emparejada todavía puede fluir a esta red virtual, pero no podrá fluir a través de una puerta de enlace de red virtual asociada a esta red virtual. 
Esta opción puede estar habilitada solo en un emparejamiento de esta red virtual.
No puede usar esta opción si ya tiene una puerta de enlace configurada en la red virtual.
        No puede habilitar esta opción si está emparejando una red virtual (Administrador de recursos) con una red virtual (clásica). Aunque el tráfico fluye entre las dos redes virtuales, el tráfico de la red virtual (Administrador de recursos) no puede fluir a través de una puerta de enlace de red asociada a la red virtual (clásica).

7. Haga clic en el botón **Aceptar** para agregar la subred a la red virtual seleccionada.

### <a name="commands"></a>Comandos:

- CLI de Azure: [az network vnet peering create](/cli/azure/network/vnet/peering#create)
- PowerShell: [Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering)

### <a name="scenarios"></a>Escenarios

Un emparejamiento de redes virtuales se crea entre redes virtuales creadas mediante el mismo modelo de implementación o mediante modelos distintos que existen en la misma suscripción o en cualquier suscripción diferente. Realice el tutorial paso a paso para uno de los siguientes escenarios:
 
|Modelo de implementación de Azure  | La suscripción  |
|---------|---------|
|Ambas mediante Resource Manager |[La misma](tutorial-connect-virtual-networks-portal.md)|
| |[Diferente](create-peering-different-subscriptions.md)|
|Una mediante Resource Manager y la otra clásica     |[La misma](create-peering-different-deployment-models.md)|
| |[Diferente](create-peering-different-deployment-models-subscriptions.md)|

## <a name="view-or-change-peering-settings"></a>Visualización o cambio de la configuración de emparejamiento

1. En el cuadro de búsqueda de la parte superior del portal, escriba *redes virtuales*. Cuando aparezca la opción **Redes virtuales** en los resultados de búsqueda, selecciónela. No seleccione **Redes virtuales (clásico)** si aparece en la lista, ya que no se puede crear un emparejamiento de una red virtual implementada mediante el modelo de implementación clásica.
2. Seleccione en la lista la red virtual a la que quiera cambiar la configuración de emparejamiento.
3. En la lista de redes virtuales, seleccione la red virtual a la que quiera cambiar la configuración de emparejamiento.
4. En **CONFIGURACIÓN**, seleccione **Emparejamientos**.
5. Haga clic en el emparejamiento cuya configuración quiere ver o cambiar.
6. Cambie los valores pertinentes. Obtenga información sobre las opciones de cada configuración en el [paso 6](#add-peering) de la sección Crear un emparejamiento. 

    >[!NOTE]
    >Antes de crear un emparejamiento, asegúrese de conocer los [requisitos y las restricciones](#requirements-and-constraints), así como los [permisos requeridos](#permissions).
    >

7. Haga clic en **Save**(Guardar).

**Comandos**

CLI de Azure: [az network vnet peering list](/cli/azure/network/vnet/peering#az_network_vnet_peering_list) para mostrar los emparejamientos de una red virtual, [az network vnet peering show](/cli/azure/network/vnet/peering#az_network_vnet_peering_show) para mostrar la configuración de un emparejamiento específico y [az network vnet peering update](/cli/azure/network/vnet/peering#az_network_vnet_peering_update) para cambiar la configuración de emparejamiento.|
- PowerShell: [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering) para recuperar y ver la configuración de emparejamiento y [Set-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/set-azurermvirtualnetworkpeering) para cambiar la configuración.

## <a name="delete-a-peering"></a>Eliminación de un emparejamiento

Cuando se elimina un emparejamiento, el tráfico procedente de una red virtual ya no fluye a la red virtual emparejada. Cuando se emparejan redes virtuales implementadas mediante el Administrador de recursos, cada red virtual tiene un emparejamiento con la otra red virtual. Aunque el hecho de eliminar el emparejamiento de una red virtual deshabilita la comunicación entre las redes virtuales, no elimina el emparejamiento de la otra red virtual. El estado del emparejamiento que existe en la otra red virtual es **Desconectado**. Para volver a crear el emparejamiento, primero tendrá que volver a crear el emparejamiento en la primera red virtual y el estado de emparejamiento de ambas redes virtuales deberá haber cambiado a *Conectado*. 

Si quiere que las redes virtuales se comuniquen algunas veces pero no siempre, en lugar de eliminar un emparejamiento, puede establecer el valor **Permitir acceso a red virtual** en **Deshabilitado**. Para obtener información sobre cómo hacerlo, vea el paso 6 de la sección [Crear un emparejamiento](#create-peering) de este artículo. Probablemente le resulte más fácil deshabilitar y habilitar el acceso a la red que eliminar y volver a crear emparejamientos.

1. En el cuadro de búsqueda de la parte superior del portal, escriba *redes virtuales*. Cuando aparezca la opción **Redes virtuales** en los resultados de búsqueda, selecciónela. No seleccione **Redes virtuales (clásico)** si aparece en la lista, ya que no se puede crear un emparejamiento de una red virtual implementada mediante el modelo de implementación clásica.
2. Seleccione en la lista la red virtual de la cual quiera eliminar el emparejamiento.
3. En la lista de redes virtuales, seleccione la red virtual de la cual quiera eliminar el emparejamiento.
4. En **CONFIGURACIÓN**, seleccione **Emparejamientos**.
5. En el lado derecho del emparejamiento que quiere eliminar, seleccione **...**, **Eliminar** y, a continuación, seleccione **Sí** para eliminar el emparejamiento de la primera red virtual.
6. Complete los pasos anteriores para eliminar el emparejamiento de la otra red virtual del emparejamiento.

**Comandos**

- CLI de Azure: [az network vnet peering delete](/cli/azure/network/vnet/peering#az_network_vnet_peering_delete)
- PowerShell: [Remove-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/remove-azurermvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Requisitos y restricciones 

- Las redes virtuales que empareje deben tener espacios de direcciones IP que no se solapen.
- Una vez que una red virtual se ha emparejado con otra red virtual, no se pueden agregar rangos de direcciones al espacio de direcciones de una red virtual ni pueden eliminarse de este. Para agregar o quitar rangos de direcciones, elimine el emparejamiento, agregue o quite los rangos de direcciones y, a continuación, vuelva a crear el emparejamiento. Para agregar rangos de direcciones a las redes virtuales o quitarlos, consulte [Manage virtual networks](manage-virtual-network.md) (Administrar redes virtuales).
- Es posible emparejar dos redes virtuales implementadas mediante el Administrador de recursos, o bien una red virtual implementada mediante el Administrador de recursos con una red virtual implementada mediante el modelo de implementación clásica. No se pueden emparejar dos redes virtuales creadas mediante el modelo de implementación clásica. Si no está familiarizado con los modelos de implementación de Azure, vea el artículo de [descripción de los modelos de implementación de Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Puede usar [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) para conectar dos redes virtuales creadas mediante el modelo de implementación clásica.
- Cuando se emparejan dos redes virtuales creadas mediante el Administrador de recursos, debe configurarse un emparejamiento para cada red virtual en el emparejamiento. 
    - *Iniciado*: cuando se crea el emparejamiento a la segunda red virtual desde la primera red virtual, el estado de emparejamiento es *Iniciado*. 
    - *Conectado*: cuando se crea el emparejamiento desde la segunda red virtual a la primera red virtual, el estado de emparejamiento es *Conectado*. Si consulta el estado de emparejamiento de la primera red virtual, verá que ha cambiado de *Iniciado* a *Conectado*. El emparejamiento no se habrá establecido correctamente mientras el estado de ambos emparejamientos de red virtual no sea *Conectado*.
- Al emparejar una red virtual creada mediante el Administrador de recursos con una red virtual creada mediante el modelo de implementación clásica, solo se configura un emparejamiento para la red virtual implementada mediante el Administrador de recursos. No se puede configurar el emparejamiento para una red virtual (clásica), o bien entre dos redes virtuales implementadas mediante el modelo de implementación clásica. Cuando se crea el emparejamiento de la red virtual (Administrador de recursos) a la red virtual (clásica), el estado de emparejamiento es *Actualizando*, pero en breve cambia a *Conectado*.
- El emparejamiento se establece entre dos redes virtuales. Los emparejamientos no son transitivos. Si crea emparejamientos entre:
    - VirtualNetwork1 y VirtualNetwork2
    - VirtualNetwork2 y VirtualNetwork3

  No hay ningún emparejamiento entre VirtualNetwork1 y VirtualNetwork3 a través de VirtualNetwork2. Si desea crear un emparejamiento de red virtual entre VirtualNetwork1 y VirtualNetwork3, debe crear un emparejamiento entre VirtualNetwork1 y VirtualNetwork3.
- No se pueden resolver nombres en redes virtuales emparejadas mediante la resolución de nombres predeterminada de Azure. Para resolver nombres de otras redes virtuales, tiene que usar un servidor DNS personalizado. Para obtener información sobre cómo configurar el servidor DNS, vea el artículo [Resolución de nombres mediante su propio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
- Los recursos de ambas redes virtuales del emparejamiento puedan comunicarse entre sí con el mismo ancho de banda y latencia que si estuvieran en la misma red virtual. A pesar de ello, el tamaño de cada máquina virtual tiene su propio ancho de banda de red máximo. Para más información sobre el ancho de banda de red máximo para los diferentes tamaños de máquina virtual, consulte los artículos sobre los tamaños de máquina virtual [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Se pueden emparejar redes virtuales implementadas mediante el Administrador de recursos que se encuentren en la misma suscripción o en suscripciones diferentes.
- Se pueden emparejar redes virtuales implementadas mediante diferentes modelos de implementación que se encuentren en la misma suscripción o en suscripciones distintas. 
- Las suscripciones en las que se encuentran ambas redes virtuales deben estar asociadas al mismo inquilino de Azure Active Directory. Si todavía no tiene un inquilino de AD, puede [crear uno](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#start-from-scratch) rápidamente. Puede usar una instancia de [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) para conectar dos redes virtuales que existen en distintas suscripciones asociadas a diferentes inquilinos de Active Directory.
- Una red virtual puede emparejarse con otra red virtual y también conectarse a otra red virtual con una puerta de enlace de red virtual de Azure. Cuando las redes virtuales están conectadas mediante emparejamiento y una puerta de enlace, el tráfico entre las redes virtuales fluye a través de la configuración de emparejamiento, en lugar de la puerta de enlace.
- Hay un cargo nominal para el tráfico de entrada y salida que utiliza un emparejamiento de red virtual. Consulte la [página de precios](https://azure.microsoft.com/pricing/details/virtual-network)para obtener más información.

## <a name="permissions"></a>Permisos

Las cuentas que use para crear un emparejamiento de redes virtuales deben tener el rol o los permisos necesarios. Por ejemplo, si está emparejando dos redes virtuales denominadas myVnetA y myVnetB, su cuenta debe tener asignado el rol o los permisos mínimos siguientes para cada red virtual:
    
|Red virtual|Modelo de implementación|Rol|Permisos|
|---|---|---|---|
|myVnetA|Resource Manager|[Colaborador de la red](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Clásico|[Colaborador de la red clásica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/D|
|myVnetB|Resource Manager|[Colaborador de la red](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Clásico|[Colaborador de la red clásica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Obtenga más información sobre los [roles integrados](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) y la asignación de permisos específicos a [roles personalizados](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (solo Resource Manager).

## <a name="next-steps"></a>Pasos siguientes

Aprenda a crear una [topología de red de concentrador y radio](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)
