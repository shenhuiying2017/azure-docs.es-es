---
title: "Creación, cambio o eliminación de redes virtuales de Azure | Microsoft Docs"
description: Aprenda a crear, cambiar o eliminar redes virtuales.
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
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: b577891afc52013b712634dd51e7e28efcfc4482
ms.contentlocale: es-es
ms.lasthandoff: 05/16/2017


---
# <a name="create-change-or-delete-virtual-networks"></a>Creación, cambio o eliminación de redes virtuales

Aprenda a crear y eliminar redes virtuales (VNet) y a cambiar ajustes de configuración, como los servidores DNS y los espacios de direcciones IP, en redes virtuales existentes. Una red virtual es una representación de su propia red en la nube. Una red virtual es un aislamiento lógico de la nube de Azure dedicada a su suscripción. Para cada red virtual, puede:
- Elegir qué espacio de direcciones le desea asignar. Un espacio de direcciones se compone de uno o más intervalos de direcciones definidos mediante la notación CIDR, como 10.0.0.0/16.
- Elegir el uso del servidor DNS proporcionado por Azure o de su propio servidor DNS para cada red virtual. Todos los recursos conectados a la red virtual se asignan a este servidor DNS para resolver nombres dentro de la red virtual.
- Segmentar la red virtual en subredes, cada una con su propio intervalo de direcciones dentro del espacio de direcciones de la red virtual. Para aprender a crear, cambiar y eliminar subredes consulte el artículo [Creación, cambio o eliminación de subredes de redes virtuales](virtual-network-manage-subnet.md).

En este artículo se describe cómo crear, cambiar y eliminar redes virtuales creadas con el modelo de implementación de Azure Resource Manager.
 
## <a name="before"></a>Antes de empezar

Complete las tareas siguientes antes de seguir los pasos de las secciones de este artículo:

- Si no está familiarizado con las redes virtuales, se recomienda completar el ejercicio que encontrará en [Creación de su primera red virtual de Azure](virtual-network-get-started-vnet-subnet.md) antes de leer este artículo. El ejercicio le ayudará a familiarizarse con las redes virtuales.
- Revise el artículo sobre los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para conocer los límites para las redes virtuales.
- Inicie sesión con una cuenta de Azure en Azure Portal, la interfaz de la línea de comandos (CLI) de Azure o Azure PowerShell. Si todavía no tiene una cuenta de Azure, regístrese para obtener una [cuenta de evaluación gratuita](https://azure.microsoft.com/free).
- Si usa los comandos de Azure PowerShell para completar las tareas de este artículo, primero debe [instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de los cmdlets de Azure PowerShell. Para obtener ayuda con los comandos de PowerShell, con ejemplos, escriba `get-help <command> -full`.
- Si usa los comandos de la interfaz de la línea de comandos (CLI) de Azure para completar las tareas de este artículo, primero debe [instalar y configurar la CLI de Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de la CLI de Azure. Para obtener ayuda con los comandos de la CLI, escriba `az <command> --help`.


## <a name="create-vnet"></a>Creación de una red virtual

1. Inicie sesión en el [portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En Azure Portal, haga clic **+ Nuevo**. En la hoja **Nuevo** que aparece, haga clic en **Redes**. En la hoja **Redes**, haga clic en **Red virtual**.
3. En la hoja **Red virtual**, deje seleccionado *Resource Manager* en el cuadro **Seleccionar un modelo de implementación** y haga clic en **Crear**.
4. En la hoja **Crear red virtual** que aparece, escriba o seleccione los valores para los siguientes ajustes y haga clic en **Crear**:
    - **Nombre**: tiene que ser único en el [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) dentro del que seleccione crear la red virtual. El nombre no se puede cambiar una vez creada la red virtual. Con el tiempo puede crear varias redes virtuales. Lea el artículo [Convenciones de nomenclatura](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions) para informarse sobre sugerencias para crear nombres que faciliten la administración de varias redes virtuales.
    - **Espacio de direcciones**: se especifica con la notación CIDR. El espacio de direcciones que defina puede ser público o privado (RFC 1918). Tanto si se define un espacio de direcciones público como privado, este solo es accesible desde dentro de la red virtual, desde las redes virtuales conectadas entre sí y las redes locales que se hayan conectado a la red virtual. No se pueden agregar los siguientes espacios de direcciones:
        - 224.0.0.0/4 (multidifusión)
        - 255.255.255.255/32 (difusión)
        - 127.0.0.0/8 (bucle invertido)
        - 169.254.0.0/16 (local de vínculo)
        - 168.63.129.16/32 (DNS interno)
    
      Aunque al crear la red virtual solo se puede definir un espacio de direcciones, después de la creación se pueden agregar espacios de direcciones adicionales. Para aprender cómo se hace, complete los pasos en la sección [Incorporación y eliminación de espacios de direcciones](#add-address-spaces) de este artículo.

      >[!WARNING]
      >Si las redes virtuales tienen espacios de direcciones que se superponen con los de otras redes virtuales o redes locales, no se pueden conectar entre sí. Antes de definir el espacio de direcciones, considere otras redes virtuales o redes locales que puede que desee conectar a la red virtual en el futuro.
      >
      >
    
    - **Nombre de subred:** tiene que ser único dentro de la red virtual. El nombre no se puede cambiar una vez creada la subred. El portal requiere que se defina una subred al crear una red virtual, aunque una red virtual no necesita tener ninguna subred. En el momento de crear la red virtual, el portal solo permite definir una subred, pero una vez que se haya creado la red virtual se pueden agregar subredes adicionales. Para agregar una subred a una red virtual, consulte la sección [Creación de una subred](virtual-network-manage-subnet.md#create-subnet) del artículo [Creación, cambio o eliminación de subredes](virtual-network-manage-subnet.md). Puede crear una red virtual con varias subredes usando la CLI o PowerShell.

      >[!TIP]
      >A menudo se crean diferentes subredes para filtrar o controlar el enrutamiento de tráfico entre ellas. Antes de definir subredes, considere cómo desea filtrar y enrutar el tráfico entre ellas. Para más información acerca del filtrado de tráfico entre subredes, consulte el artículo sobre [grupos de seguridad de red](virtual-networks-nsg.md). Azure realiza el enrutamiento entre subredes automáticamente, pero el usuario puede invalidar las rutas predeterminadas de Azure. Para obtener información sobre cómo hacerlo, lea el artículo [Rutas definidas por el usuario](virtual-networks-udr-overview.md).
      >

    - **Intervalo de direcciones de subred:** tiene que estar dentro del **espacio de direcciones** que especificó para la red virtual. El menor intervalo que se puede especificar es /29, lo que proporciona ocho direcciones IP de subred. De conformidad con el protocolo, Azure reserva la primera y la última dirección de cada subred. Otras tres direcciones están reservadas para el uso del servicio de Azure. Como resultado, una rede virtual con un intervalo de direcciones de subred de /29 tiene solo tres direcciones IP utilizables. Si planea conectar una red virtual a una instancia de VPN Gateway, debe crear una subred de puerta de enlace. Más información sobre las [consideraciones específicas del intervalo de direcciones de las subredes de puerta de enlace](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namegwsubagateway-subnet). En determinadas circunstancias se puede cambiar el intervalo de direcciones una vez creada la subred. Para información sobre cómo cambiar un intervalo de direcciones de subred, lea la sección sobre el [cambio de subred](#change-subnet) del artículo [Creación, cambio o eliminación de subredes de redes virtuales](virtual-network-manage-subnet.md).
    - **Suscripción:** seleccione una [suscripción](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Una red virtual no puede abarcar varias suscripciones, aunque sí puede estar conectada a las redes virtuales de otras suscripciones utilizando una instancia de Azure VPN Gateway o un emparejamiento de VNet. Los recursos de Azure que se conectan a la red virtual tienen que encontrarse dentro de la misma suscripción.
    - **Grupo de recursos:** seleccione un [grupo de recursos](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) existente o cree uno nuevo. Los recursos de Azure que se conectan a la red virtual pueden encontrarse en el mismo grupo de recursos o en grupos diferentes.
    - **Ubicación:** seleccione una [ubicación](https://azure.microsoft.com/regions/) de Azure, también conocida como región. Una red virtual no puede abarcar varias ubicaciones de Azure, aunque sí puede estar conectada a una red virtual en otra ubicación utilizando una instancia de Azure VPN Gateway. Los recursos de Azure que se conectan a la red virtual tienen que encontrarse dentro de la misma ubicación.

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|[az network vnet create](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name = "view-vnet"></a>Visualización de las redes virtuales y su configuración

1. Inicie sesión en el [portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior del portal, escriba *redes virtuales*. Cuando **redes virtuales** aparezca en los resultados de búsqueda, haga clic en él.
3. En la hoja **Redes virtuales** que aparece, haga clic en la red virtual para la que desea ver la configuración.
4. Los siguientes ajustes se muestran en la hoja que aparece para la red virtual seleccionada:
    - **Información general:** proporciona información acerca de la red virtual, como su espacio de direcciones y servidores DNS. La siguiente imagen muestra la configuración de información general de una red virtual denominada **MyVNet**:
    
        ![Información general de interfaz de red](./media/virtual-network-manage-network/vnet-overview.png)

      En esta hoja puede mover una red virtual a una suscripción o un grupo de recurso diferente. Para más información sobre cómo mover una red virtual, lea el artículo [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json). En el artículo se enumeran los requisitos previos y se indica cómo trasladar recursos mediante Azure Portal, PowerShell y la CLI de Azure. Todos los recursos conectados a la red virtual tienen que moverse con ella. 
    - **Espacio de direcciones:** se enumeran los espacios de direcciones asignados a la red virtual. Para aprender cómo agregar y quitar los espacios de direcciones, complete los pasos en la sección [Incorporación y eliminación de espacios de direcciones](#address-spaces) de este artículo.
    - **Dispositivos conectados:** se muestran los recursos conectados a la red virtual. En el ejemplo que se muestra en la imagen anterior, hay tres interfaces de red y un equilibrador de carga conectados a la red virtual. Se muestran todos los nuevos recursos que se creen y conecten a la red virtual. Si elimina un recurso conectado a la red virtual, ya no aparecerá en la lista.
    - **Subredes:** una lista de subredes que existen dentro de la red virtual. Para obtener información sobre cómo agregar y quitar subredes, lea las secciones sobre [agregar subred](virtual-network-manage-subnet.md#create-subnet) y [eliminar subred](virtual-network-manage-subnet.md#delete-subnet) del artículo [Creación, cambio o eliminación de subredes de redes virtuales](virtual-network-manage-subnet.md).
    - **Servidores DNS:** puede especificar si el servidor DNS interno de Azure o el servidor DNS personalizado proporciona resolución de nombres para los dispositivos conectados a la red virtual. Al crear una red virtual mediante Azure Portal, los servidores DNS de Azure se utilizan para la resolución de nombres dentro de una red virtual, de forma predeterminada. Para modificar los servidores DNS, complete los pasos de la sección [Incorporación, cambio y eliminación de servidores DNS](#dns-servers) de este artículo. 
    - **Emparejamientos:** si hay emparejamientos existentes en la suscripción, estos aparecerán aquí. Puede ver la configuración de emparejamientos existentes, o crear, cambiar o eliminar emparejamientos. Para aprender más sobre emparejamientos, consulte el artículo sobre [introducción al emparejamiento de redes virtuales](virtual-network-peering-overview.md).
    - **Propiedades:** muestra la configuración de la red virtual, incluidos el identificador de recurso de la red virtual y la suscripción en la que se encuentra.
    - **Diagrama:** el diagrama proporciona una representación visual de todos los dispositivos conectados a la red virtual con alguna información clave acerca de los mismos. Puede hacer clic en cualquiera de los dispositivos para administrar directamente a través de esta vista.
    - **Opciones comunes de Azure:** para más información acerca de los ajustes de configuración comunes de Azure, lea los artículos [Registro de actividad](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs), [Control de acceso (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control), [Etiquetas](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags), [Bloqueos](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) y [Script de Automation](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group).

**Comandos**

|**Herramienta**|**Comando**|
|---|---|
|CLI|[az network vnet show](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVirtualNetwork](/powershell/resourcemanager/azurerm.network/v3.8.0/get-azurermvirtualnetwork/?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="address-spaces"></a>Incorporación y eliminación de espacios de direcciones

Puede agregar y quitar espacios de direcciones en una red virtual. Los espacios de direcciones tienen que especificarse en notación CIDR y no pueden superponerse dentro de la misma red virtual. Los espacios de direcciones que defina pueden ser públicos o privados (RFC 1918). Tanto si se definen espacios de direcciones públicos como privados, los espacios de direcciones de una red virtual solo son accesible desde dentro de la misma, desde las redes virtuales conectadas entre sí y las redes locales que se hayan conectado a la red virtual. No se pueden agregar los siguientes espacios de direcciones:
    - 224.0.0.0/4 (multidifusión)
    - 255.255.255.255/32 (difusión)
    - 127.0.0.0/8 (bucle invertido)
    - 169.254.0.0/16 (local de vínculo)
    - 168.63.129.16/32 (DNS interno)

1. Inicie sesión en el [portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *redes virtuales*. Cuando **redes virtuales** aparezca en los resultados de búsqueda, haga clic en él.
3. En la hoja **Redes virtuales** que aparece, haga clic en la red virtual en la que desea agregar o quitar un espacio de direcciones.
4. En la hoja que aparece para la red virtual seleccionada, haga clic en **Espacio de direcciones** en la sección **CONFIGURACIÓN**.
5. Realice una de las siguientes opciones en la hoja que aparece con los espacios de direcciones:
    - **Agregar un espacio de direcciones:** escriba el nuevo espacio de direcciones en el cuadro. El espacio de direcciones no puede superponerse a un espacio de direcciones existente definido para la red virtual.
    - **Quitar un espacio de direcciones:**haga clic con el botón derecho en un espacio de direcciones, a continuación, haga clic en **Quitar**. Si existe una subred en el espacio de direcciones, no se puede quitar el espacio de direcciones. Para poder quitar un espacio de direcciones, es necesario eliminar primero las subredes que existen en ese espacio (y todos los recursos conectados a las subredes).
6. Haga clic en **Guardar**.

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|Solo Resource Manager|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="dns-servers"></a>Incorporación, cambio y eliminación de servidores DNS

Todas las máquinas virtuales conectadas a la red virtual se registran con los servidores DNS especificados para la red virtual y usan el servidor DNS para la resolución de nombre. Cada interfaz de red (NIC) en una máquina virtual puede tener su propia configuración de servidor DNS. Si una NIC tiene su propia configuración de servidor DNS, esta invalida la configuración del servidor DNS para la red virtual. Para más información acerca de la configuración de DNS de NIC, consulte el artículo sobre las [los ajustes y tareas de la interfaz de red](virtual-network-network-interface.md#dns). Para más información sobre la resolución de nombres para las máquinas virtuales y las instancias de rol de los servicios en la nube, lea el artículo [Resolución de nombres para las máquinas virtuales e instancias de rol](virtual-networks-name-resolution-for-vms-and-role-instances.md).

1. Inicie sesión en el [portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *redes virtuales*. Cuando **redes virtuales** aparezca en los resultados de búsqueda, haga clic en él. 
3. En la hoja **Redes virtuales** que aparece, haga clic en la red virtual en la que desea cambiar la configuración DNS.
4. En la hoja que aparece para la red virtual seleccionada, haga clic en **Servidores DNS** en la sección **CONFIGURACIÓN**.
5. Seleccione una de las siguientes opciones en la hoja que aparece con los servidores DNS:
    - **Predeterminado (viene con Azure):** todos los nombres de los recursos y las direcciones IP privadas se registran automáticamente en los servidores DNS de Azure. Puede resolver nombres entre los recursos conectados a la misma red virtual. No se puede utilizar esta opción para resolver nombres entre redes virtuales. Para resolver nombres entre redes virtuales, tiene que usar un servidor DNS personalizado.
    - **Personalizado:** puede agregar uno o varios servidores, hasta el límite de Azure para una red virtual. Para más información sobre los límites de servidor DNS, lea el artículo sobre los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Tiene las siguientes opciones:
        - **Agregar una dirección:** agrega el servidor a la lista de servidores DNS de la red virtual y también registra el servidor DNS con Azure. Si se registró un servidor DNS anteriormente con Azure, puede seleccionarlo en la lista que aparece. 
        - **Quitar una dirección:** haga clic en la **X** junto al servidor que desea quitar. Al eliminar el servidor solo se quita de esta lista de red virtual. El servidor DNS seguirá registrado en Azure para que lo usen otras redes virtuales. 
        - **Reordenar direcciones de servidor DNS**: es importante comprobar que se enumeran los servidores DNS en el orden correcto para su entorno. Las listas de servidores DNS no funcionan con Round Robin. Se utilizan en el orden en que se especifican. Si se puede acceder al primer servidor DNS de la lista, el cliente utilizará ese servidor DNS, con independencia de si el servidor DNS funciona correctamente o no. Quite todos los servidores DNS que aparecen y vuelva a agregarlos en el orden que desee.
        - **Cambiar una dirección:** resalte el servidor DNS en la lista y después escriba el nuevo nombre.
6. Haga clic en **Guardar**.
7. Reinicie las máquinas virtuales conectadas a la red virtual para que se les asigne la nueva configuración de servidor DNS. Hasta que se reinicien, las máquinas virtuales seguirán usando la configuración de DNS que consideran actual.

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="delete-vnet"></a>Eliminación de una red virtual

Solo se puede eliminar una red virtual si no tiene recursos conectados. Si hay recursos conectados a cualquier subred dentro de la red virtual, primero tiene que eliminarlos. Las instrucciones sobre cómo eliminar un recurso varían según el recurso. Para más información sobre cómo eliminar los recursos conectados a una subred, lea la documentación de cada tipo de recurso que desee eliminar. Para eliminar una red virtual, complete los pasos siguientes:

1. Inicie sesión en el [portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *redes virtuales*. Cuando **redes virtuales** aparezca en los resultados de búsqueda, haga clic en él.
3. En la hoja **Redes virtuales** que aparece, haga clic en la red virtual que desea eliminar.
4. Confirme que no hay ningún dispositivo conectado a la red virtual haciendo clic en **Dispositivos conectados** en la sección **CONFIGURACIÓN** de la hoja que apareció para la red virtual que seleccionó. Si hay dispositivos conectados, primero debe eliminarlos antes de poder eliminar la red virtual.  Si no hay ningún dispositivo conectado, haga clic en **Introducción** en la hoja.
5. Haga clic en el icono **Eliminar** en la parte superior de la hoja. 
6. Haga clic en **Sí** para confirmar la eliminación de la red virtual.


**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|[azure network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetwork](/powershell/module/azurerm.network/remove-azurermvirtualnetwork?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>Pasos siguientes

- Para crear una máquina virtual y conectarla a una red virtual, lea el artículo sobre la [creación de una red virtual y la conexión de máquinas virtuales](virtual-network-get-started-vnet-subnet.md#a-namecreate-vmsacreate-virtual-machines).
- Para filtrar el tráfico de red entre subredes dentro de una red virtual, lea el artículo sobre la [creación de grupos de seguridad de red](virtual-networks-create-nsg-arm-pportal.md).
- Para emparejar una red virtual con otra red virtual, lea el artículo acerca de la [creación de un emparejamiento de red virtual](virtual-networks-create-vnetpeering-arm-portal.md#peering-vnets-in-the-same-subscription).
- Para más información acerca de las opciones para conectar una red virtual a una red local, lea el artículo [acerca de la puerta de enlace de red](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namediagramsaconnection-topology-diagrams).

