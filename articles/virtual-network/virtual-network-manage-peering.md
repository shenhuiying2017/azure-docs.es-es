---
title: Crear, cambiar o eliminar un emparejamiento de red virtual de Azure | Microsoft Docs
description: Aprenda a crear, cambiar o eliminar un emparejamiento de red virtual.
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
ms.date: 06/06/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 74f34bdbf5707510c682814716aa0b95c19a5503
ms.openlocfilehash: 1e524f13a70b1d08b064d9ff70db66b2b509abe3
ms.contentlocale: es-es
ms.lasthandoff: 06/09/2017


---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Crear, cambiar o eliminar un emparejamiento de red virtual

Aprenda a crear, cambiar o eliminar un emparejamiento de red virtual. El emparejamiento de red virtual permite conectar dos redes virtuales de la misma ubicación de Azure mediante la red troncal de Azure. Una vez que dos redes virtuales se han emparejado, se siguen administrando como recursos separados. Los recursos conectados a cualquiera de las dos redes virtuales se comunican con la misma latencia y ancho de banda que se registraría si estuvieran conectados a la misma red virtual. Si no está familiarizado con el emparejamiento de red virtual, se recomienda que lea la introducción al [Emparejamiento de redes virtuales](virtual-network-peering-overview.md) y que complete el tutorial en el que se describe cómo [crear un emparejamiento de red virtual](virtual-network-create-peering.md) antes de completar las tareas de este artículo.

## <a name="before"></a>Antes de empezar

Complete las tareas siguientes antes de seguir los pasos de las secciones de este artículo:

- Si no está familiarizado con el emparejamiento de red virtual en Azure, se recomienda que complete el tutorial sobre la [creación de un emparejamiento de red virtual](virtual-network-create-peering.md) antes de leer este artículo.
- Revise el artículo sobre los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para obtener información sobre el emparejamiento.
- Inicie sesión con una cuenta de Azure en Azure Portal, la interfaz de la línea de comandos (CLI) de Azure o Azure PowerShell. Si todavía no tiene una cuenta de Azure, regístrese para obtener una [cuenta de evaluación gratuita](https://azure.microsoft.com/free).
- Si usa comandos de PowerShell para completar las tareas de este artículo, [instale y configure Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de los cmdlets de Azure PowerShell. Para obtener ayuda con los comandos de PowerShell, con ejemplos, escriba `get-help <command> -full`.
- Si usa comandos de la interfaz de la línea de comandos (CLI) de Azure para completar las tareas de este artículo, [instale y configure la CLI de Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de la CLI de Azure. Para obtener ayuda con los comandos de la CLI, escriba `az <command> --help`.

## <a name="about-peering"></a>Acerca del emparejamiento 

Tenga en cuenta las siguientes restricciones antes de emparejar redes virtuales:
- Las redes virtuales que empareje deben tener espacios de direcciones IP que no se solapen. 
- Una vez que una red virtual se ha emparejado con otra red virtual, no se pueden agregar espacios de direcciones a una red virtual ni eliminarse de esta. Para agregar o quitar espacios de direcciones, elimine el emparejamiento, agregue o quite los espacios de direcciones y, después, vuelva a crear el emparejamiento. Para agregar espacios de direcciones a redes virtuales o quitarlos de estas, lea el artículo [Creación, cambio o eliminación de redes virtuales](virtual-network-manage-network.md#add-address-spaces). 
- Es posible emparejar dos redes virtuales implementadas mediante el Administrador de recursos, o bien una red virtual implementada mediante el Administrador de recursos con una red virtual implementada mediante el modelo de implementación clásica. No se pueden emparejar dos redes virtuales creadas mediante el modelo de implementación clásica. Si no está familiarizado con los modelos de implementación de Azure, vea el artículo de [descripción de los modelos de implementación de Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Cuando se emparejan dos redes virtuales creadas mediante el Administrador de recursos, debe configurarse un emparejamiento para cada red virtual en el emparejamiento. Cuando se crea el emparejamiento a la segunda red virtual desde la primera red virtual, el estado de emparejamiento es *Iniciado*.  Cuando se crea el emparejamiento desde la segunda red virtual a la primera red virtual, el estado de emparejamiento es *Conectado*. Si consulta el estado de emparejamiento de la primera red virtual, verá que ha cambiado de *Iniciado* a *Conectado*. El emparejamiento no se habrá establecido correctamente mientras el estado de ambos emparejamientos de red virtual no sea *Conectado*. 
- Al emparejar una red virtual creada mediante el Administrador de recursos con una red virtual creada mediante el modelo de implementación clásica, solo se configura un emparejamiento para la red virtual implementada mediante el Administrador de recursos. No se puede configurar el emparejamiento para una red virtual (clásica), o bien entre dos redes virtuales implementadas mediante el modelo de implementación clásica. Cuando se crea el emparejamiento de la red virtual (Administrador de recursos) a la red virtual (clásica), el estado de emparejamiento es *Actualizando*, pero en breve cambia a *Conectado*.   
- El emparejamiento se establece entre dos redes virtuales. Los emparejamientos no son transitivos. Si crea emparejamientos entre:
    - La red virtual 1 y la red virtual 2
    - La red virtual 2 y la red virtual 3

  No hay ningún emparejamiento entre la red virtual 1 y la red virtual 3 a través de la red virtual 2. Si quiere configurar un emparejamiento entre la red virtual 1 y la red virtual 3, tendrá que crear un emparejamiento entre la red virtual 1 y la red virtual 3.
- No se pueden resolver nombres en redes virtuales emparejadas mediante la resolución de nombres predeterminada de Azure. Para resolver nombres de otras redes virtuales, tiene que usar un servidor DNS personalizado. Para obtener información sobre cómo configurar el servidor DNS, vea el artículo [Resolución de nombres mediante su propio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
- Los recursos de ambas redes virtuales del emparejamiento puedan comunicarse entre sí con el mismo ancho de banda y latencia que si estuvieran en la misma red virtual. A pesar de ello, el tamaño de cada máquina virtual tiene su propio ancho de banda de red máximo. Para más información sobre el ancho de banda de red máximo para los diferentes tamaños de máquina virtual, consulte los artículos sobre los tamaños de máquina virtual [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Se pueden emparejar redes virtuales implementadas mediante el Administrador de recursos que se encuentren en la misma suscripción o en suscripciones diferentes.
- Se pueden emparejar redes virtuales implementadas mediante diferentes modelos de implementación que se encuentren en la misma suscripción. La funcionalidad para emparejar redes virtuales implementadas mediante diferentes modelos de implementación en suscripciones diferentes está en versión preliminar.
- Las suscripciones en las que se encuentran ambas redes virtuales deben estar asociadas al mismo inquilino de Azure Active Directory. Si todavía no tiene un inquilino de AD, puede [crear uno](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#start-from-scratch) rápidamente. 
- <a name="roles-permissions"></a>La cuenta debe tener el rol o los permisos necesarios para crear un emparejamiento. Por ejemplo, si está emparejando dos redes virtuales denominadas *Vnet1* y *VNet2*, su cuenta debe tener asignado el rol o los permisos mínimos siguientes para cada red virtual:
    
    |Red virtual|Modelo de implementación|Rol|Permisos|
    |---|---|---|---|
    |VNet1|Resource Manager|[Colaborador de la red](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
    | |Clásico|[Colaborador de la red clásica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/D|
    |VNet2|Resource Manager|[Colaborador de la red](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
    ||Clásico|[Colaborador de la red clásica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

  Obtenga más información sobre los [roles integrados](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) y la asignación de permisos específicos a [roles personalizados](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (solo el Administrador de recursos).
- Una red virtual puede emparejarse con otra red virtual y también conectarse a otra red virtual con una puerta de enlace de red virtual de Azure. Cuando las redes virtuales están conectadas mediante emparejamiento y una puerta de enlace, el tráfico entre las redes virtuales fluye a través de la configuración de emparejamiento, en lugar de la puerta de enlace.
- Hay un cargo nominal para el tráfico de entrada y salida que utiliza un emparejamiento de red virtual. Consulte la [página de precios](https://azure.microsoft.com/pricing/details/virtual-network)para obtener más información.

## <a name="create-peering"></a>Crear un emparejamiento

>[!NOTE]
>Es necesario tener en cuenta varios requisitos, restricciones y consideraciones para crear correctamente un emparejamiento. Antes de crear un emparejamiento, asegúrese de que se ha familiarizado con la lista de consideraciones de la sección [Acerca del emparejamiento](#about-peering) de este artículo.
>

1. Inicie sesión en el [portal](https://portal.azure.com) con una cuenta que disponga de los [roles y permisos](#roles-permissions) necesarios.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *redes virtuales*. Cuando **redes virtuales** aparezca en los resultados de búsqueda, haga clic en él. No seleccione **Redes virtuales (clásico)** si aparece en la lista, ya que no se puede crear un emparejamiento de una red virtual implementada mediante el modelo de implementación clásica.
3. En la hoja **Redes virtuales** que aparece, haga clic en la red virtual para la que quiere crear un emparejamiento.
4. En el panel que aparece para la red virtual seleccionada, haga clic en **Emparejamientos** en la sección **CONFIGURACIÓN**.
5. Haga clic en **+ Agregar**. 
6. <a name="add-peering"></a>En la hoja **Agregar emparejamiento**, escriba o seleccione valores para las siguientes opciones:
    - **Nombre:** el nombre del emparejamiento debe ser único dentro de la red virtual.
    - **Modelo de implementación de red virtual:** seleccione con qué modelo de implementación se implementó la red virtual con la que quiere realizar el emparejamiento.
    - **Conozco mi id. de recurso:** si tiene acceso de lectura a la red virtual con la que quiere realizar el emparejamiento, no active esta casilla. Si no tiene acceso de lectura a la red virtual o la suscripción con la que quiere realizar el emparejamiento, active esta casilla. Escriba el identificador de recurso completo de la red virtual con la que quiere realizar el emparejamiento en el cuadro **Id. de recurso** que aparece cuando se activa la casilla. El identificador de recurso que especifique debe ser para una red virtual que exista en la misma [ubicación](https://azure.microsoft.com/regions) de Azure que esta red virtual. El identificador de recurso completo es similar a /subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>. Para obtener el identificador de recurso de una red virtual, vea las propiedades de la red virtual. Para obtener información sobre cómo ver las propiedades de una red virtual, lea el artículo [Creación, cambio o eliminación de redes virtuales](virtual-network-manage-network.md#view-vnet).
    - **Suscripción:** seleccione la [suscripción](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) de la red virtual con la que quiere realizar el emparejamiento. Se muestran una o varias suscripciones, en función del número de suscripciones al que tenga acceso de lectura su cuenta. Si ha activado la casilla **Id. de recurso**, esta opción no está disponible. Puede emparejar redes virtuales en suscripciones diferentes siempre y cuando ambas redes virtuales se hayan creado mediante el Administrador de recursos. La funcionalidad para realizar el emparejamiento en suscripciones creadas mediante modelos de implementación diferentes está en versión preliminar. Regístrese para obtener la versión preliminar antes de crear un emparejamiento entre redes virtuales implementadas mediante modelos de implementación diferentes que existen en suscripciones diferentes. Obtenga más información sobre cómo registrarse para obtener la versión preliminar y [emparejar redes virtuales creadas mediante modelos de implementación diferentes en suscripciones diferentes](virtual-network-create-peering.md#different-subscriptions-different-deployment-models).
    - **Red virtual:** seleccione la red virtual con la que quiere realizar el emparejamiento. Puede seleccionar una red virtual creada mediante cualquier modelo de implementación de Azure, pero la red virtual debe estar en la misma ubicación que la red virtual desde la que ha iniciado el emparejamiento. Debe tener acceso de lectura a la red virtual para que sea visible en la lista. Si una red virtual aparece en gris, puede deberse a que el espacio de direcciones de la red virtual se superpone con el espacio de direcciones de esta red virtual. Si los espacios de direcciones de las redes virtuales se superponen, no se pueden emparejar. Si ha activado la casilla **Id. de recurso**, esta opción no está disponible.
    - **Permitir acceso a red virtual:** seleccione **Habilitado** (valor predeterminado) si quiere habilitar la comunicación entre las dos redes virtuales. Al habilitar la comunicación entre las redes virtuales, permite que los recursos conectados a cualquier red virtual se comuniquen entre sí con el mismo ancho de banda y latencia que si estuvieran conectados a la misma red virtual. Todas las comunicaciones entre los recursos de las dos redes virtuales se realizan a través de la red privada de Azure. La etiqueta predeterminada **VirtualNetwork** para los grupos de seguridad de red abarca la red virtual y la red virtual emparejada. Para obtener más información sobre las etiquetas predeterminadas de los grupos de seguridad de red, lea el artículo de [introducción a los grupos de seguridad de red](virtual-networks-nsg.md#default-tags).  Seleccione **Deshabilitado** si no quiere que el tráfico fluya a la red virtual emparejada. Por ejemplo, podría seleccionar **Deshabilitado** si ha emparejado una red virtual con otra red virtual pero quiere deshabilitar en ocasiones el flujo de tráfico entre ambas redes. Le resultará más cómoda la opción de habilitar y deshabilitar que eliminar y volver a crear emparejamientos. Si esta opción está deshabilitada, el tráfico no fluye entre las redes virtuales emparejadas.
    - **Permitir tráfico reenviado:** active esta casilla para permitir que el tráfico reenviado a la red virtual emparejada (el tráfico que no se origina en la red virtual emparejada) fluya a esta red virtual. El reenvío de tráfico es habitual cuando se ha implementado una aplicación virtual de red en la red virtual con la que se está realizando el emparejamiento y cuando se han creado rutas definidas por el usuario para reenviar el tráfico a través de la aplicación virtual de red. Si deja esta casilla sin activar (valor predeterminado), el tráfico reenviado desde la red virtual emparejada no puede fluir a esta red virtual. Aunque el hecho de habilitar esta funcionalidad permite el tráfico reenviado a través del emparejamiento, no se crean rutas definidas por el usuario ni aplicaciones virtuales de red. Las rutas definidas por el usuario y las aplicaciones virtuales de red se crean por separado. Obtenga información sobre las [rutas definidas por el usuario](virtual-networks-udr-overview.md).
    - **Permitir tránsito de puerta de enlace:** active esta casilla si tiene una puerta de enlace de red virtual asociada a esta red virtual y quiere permitir que el tráfico procedente de la red virtual emparejada fluya a través de la puerta de enlace. Por ejemplo, esta red virtual puede asociarse a una red local a través de una puerta de enlace de red virtual. La activación de esta casilla permite que el tráfico procedente de la red virtual emparejada fluya a través de la puerta de enlace asociada a esta red virtual. Si activa esta casilla, la red virtual emparejada no puede tener una puerta de enlace configurada. La red virtual emparejada debe tener activada la casilla **Usar puertas de enlace remotas** cuando se configura el emparejamiento de la otra red virtual a esta red virtual. Si deja esta casilla sin activar (valor predeterminado), el tráfico procedente de la red virtual emparejada seguirá fluyendo a esta red virtual, pero no podrá fluir a través de una puerta de enlace de red virtual asociada a esta red virtual. Obtenga más información sobre las [puertas de enlace de red virtual](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#site-to-site-and-multi-site-ipsecike-vpn-tunnel). 
    
    No puede habilitar esta opción si está emparejando una red virtual (Administrador de recursos) con una red virtual (clásica). Aunque el tráfico fluye entre las dos redes virtuales, el tráfico de la red virtual (clásica) no puede fluir a través de una puerta de enlace de red asociada a la red virtual (Administrador de recursos).
    - **Usar puertas de enlace remotas:** active esta casilla para permitir que el tráfico procedente de esta red virtual fluya a través de la puerta de enlace de red virtual asociada a la red virtual con la que está realizando el emparejamiento. Por ejemplo, la red virtual con la que está realizando el emparejamiento tiene una puerta de enlace de VPN asociada que permite la comunicación a una red local.  La activación de esta casilla permite que el tráfico procedente de esta red virtual fluya a través de la puerta de enlace de VPN asociada a la red virtual emparejada. Si activa esta casilla, la red virtual emparejada debe tener asociada una puerta de enlace de red virtual y debe tener activada la casilla **Permitir tránsito de puerta de enlace**. Si deja esta casilla sin activar (valor predeterminado), el tráfico procedente de la red virtual emparejada todavía puede fluir a esta red virtual, pero no podrá fluir a través de una puerta de enlace de red virtual asociada a esta red virtual. 
    
    No puede habilitar esta opción si está emparejando una red virtual (Administrador de recursos) con una red virtual (clásica). Aunque el tráfico fluye entre las dos redes virtuales, el tráfico de la red virtual (Administrador de recursos) no puede fluir a través de una puerta de enlace de red asociada a la red virtual (clásica).
7. Haga clic en el botón **Aceptar** para agregar la subred a la red virtual seleccionada.

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|[az network vnet peering create](/cli/azure/network/vnet/peering#create?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet"></a>Ver o cambiar la configuración de emparejamiento

1. Inicie sesión en el [portal](https://portal.azure.com) con una cuenta que disponga de los [roles y permisos](#roles-permissions) necesarios.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *redes virtuales*. Cuando **redes virtuales** aparezca en los resultados de búsqueda, haga clic en él.
3. En la hoja **Redes virtuales** que aparece, haga clic en la red virtual para la que quiere crear un emparejamiento.
4. En el panel que aparece para la red virtual seleccionada, haga clic en **Emparejamientos** en la sección **CONFIGURACIÓN**.
5. Haga clic en el emparejamiento cuya configuración quiere ver o cambiar.
6. Cambie los valores pertinentes. Obtenga información sobre las opciones de cada valor en el [paso 6](#add-peering) de la sección Crear un emparejamiento de este artículo. 

    >[!NOTE]
    >Es necesario tener en cuenta varios requisitos, restricciones y consideraciones para crear correctamente un emparejamiento. Antes de crear un emparejamiento, asegúrese de que se ha familiarizado con la lista de consideraciones de la sección [Acerca del emparejamiento](#about-peering) de este artículo.
    >

7. Haga clic en **Guardar**.

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|[az network vnet peering list](/cli/azure/network/vnet/peering?toc=%2fazure%2fvirtual-network%2ftoc.json#list) para mostrar los emparejamientos de una red virtual, [az network vnet peering show](/cli/azure/network/vnet/peering?toc=%2fazure%2fvirtual-network%2ftoc.json#show) para mostrar la configuración de un emparejamiento específico y [az network vnet peering update](/cli/azure/network/vnet/peering?toc=%2fazure%2fvirtual-network%2ftoc.json#update) para cambiar la configuración de emparejamiento.|
|PowerShell|[Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?toc=%2fazure%2fvirtual-network%2ftoc.json) para recuperar y ver la configuración de emparejamiento y [Set-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/set-azurermvirtualnetworkpeering?toc=%2fazure%2fvirtual-network%2ftoc.json) para cambiar la configuración.|

## <a name="delete-subnet"></a>Eliminar un emparejamiento
Cuando se elimina un emparejamiento, el tráfico procedente de una red virtual ya no fluye a la red virtual emparejada. Cuando se emparejan redes virtuales implementadas mediante el Administrador de recursos, cada red virtual tiene un emparejamiento con la otra red virtual. Aunque el hecho de eliminar el emparejamiento de una red virtual deshabilita la comunicación entre las redes virtuales, no elimina el emparejamiento de la otra red virtual. El estado del emparejamiento que existe en la otra red virtual es **Desconectado**. Para volver a crear el emparejamiento, primero tendrá que volver a crear el emparejamiento en la primera red virtual y el estado de emparejamiento de ambas redes virtuales deberá haber cambiado a *Conectado*. 

Si quiere que las redes virtuales se comuniquen algunas veces pero no siempre, en lugar de eliminar un emparejamiento, puede establecer el valor **Permitir acceso a red virtual** en **Deshabilitado**. Para obtener información sobre cómo hacerlo, vea el paso 6 de la sección [Crear un emparejamiento](#create-peering) de este artículo. Probablemente le resulte más fácil deshabilitar y habilitar el acceso a la red que eliminar y volver a crear emparejamientos.

1. Inicie sesión en el [portal](https://portal.azure.com) con una cuenta que disponga de los [roles y permisos](#roles-permissions) necesarios.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *redes virtuales*. Cuando **redes virtuales** aparezca en los resultados de búsqueda, haga clic en él.
3. En la hoja **Redes virtuales** que aparece, haga clic en la red virtual de la que quiere eliminar un emparejamiento.
4. En la hoja que aparece para la red virtual seleccionada, haga clic en **Emparejamientos** en la sección **Configuración**.
5. En la lista de emparejamientos que aparece en la hoja de emparejamientos, haga clic con el botón derecho en el emparejamiento que quiere eliminar y, después, haga clic en **Eliminar** y en **Sí** para eliminar el emparejamiento de la primera red virtual.
6. Complete los pasos anteriores para eliminar el emparejamiento de la otra red virtual del emparejamiento.

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|[az network vnet peering delete](/cli/azure/network/vnet/peering?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/remove-azurermvirtualnetworkpeering?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>Pasos siguientes

- Realice el [tutorial de emparejamiento de red virtual](virtual-network-create-peering.md)
- Crear una topología de red de concentrador y radio|[Varios](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)|




