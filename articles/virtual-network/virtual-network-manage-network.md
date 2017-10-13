---
title: Crear, cambiar o eliminar una red virtual de Azure | Microsoft Docs
description: Aprenda a crear, cambiar o eliminar una red virtual en Azure.
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
ms.openlocfilehash: 74aace2136136c25bc56327d38cfbab168265401
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-change-or-delete-a-virtual-network"></a>Crear, cambiar o eliminar una red virtual

Aprenda a crear y eliminar una red virtual, y a cambiar ajustes de configuración, como los servidores DNS y los espacios de direcciones IP, en una red virtual existente.

Una red virtual es una representación de su propia red en la nube. Una red virtual es un aislamiento lógico de la nube de Azure dedicada a su suscripción de Azure. Para cada red virtual que cree, puede:
- Elegir un espacio de direcciones para asignar. Un espacio de direcciones se compone de uno o más intervalos de direcciones que se definen mediante la notación de Enrutamiento de interdominios sin clases (CIDR), como 10.0.0.0/16.
- Elegir el uso del servidor DNS proporcionado por Azure o de su propio servidor DNS. Todos los recursos conectados a la red virtual se asignan a este servidor DNS para resolver los nombres dentro de la red virtual.
- Segmentar la red virtual en subredes, cada una con su propio intervalo de direcciones dentro del espacio de direcciones de la red virtual. Para aprender a crear, cambiar y eliminar subredes, vea [Agregar, cambiar o eliminar subredes](virtual-network-manage-subnet.md).

En este artículo se describe cómo crear, cambiar y eliminar redes virtuales mediante el modelo de implementación de Azure Resource Manager.

## <a name="before"></a>Antes de empezar

Antes de comenzar las tareas que se describen en este artículo, lleve a cabo las siguientes tareas previas necesarias:

- Si no está familiarizado con el trabajo con redes virtuales, se recomienda que revise el ejercicio de [Creación de su primera red virtual](virtual-network-get-started-vnet-subnet.md). Este ejercicio puede ayudarle a familiarizarse más con las redes virtuales.
- Para obtener información sobre los límites de las redes virtuales, revise los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Inicie sesión con la cuenta de Azure en Azure Portal, en la interfaz de la línea de comandos (CLI de Azure) de Azure o en Azure PowerShell. Si no tiene una cuenta de Azure, regístrese para obtener una [cuenta de prueba gratuita](https://azure.microsoft.com/free).
- Si tiene pensado usar comandos de PowerShell para realizar las tareas descritas en este artículo, primero debe [instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de los cmdlets de Azure PowerShell. Para obtener ayuda para los comandos de PowerShell de los ejemplos, escriba `get-help <command> -full`.
- Si tiene pensado usar comandos de la CLI de Azure para completar las tareas descritas en este artículo, primero debe [instalar y configurar la CLI de Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de la CLI de Azure. Para obtener ayuda con los comandos de la CLI de Azure, escriba `az <command> --help`.


## <a name="create-vnet"></a>Creación de una red virtual

Para crear una red virtual:

1. Inicie sesión en el [portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol Colaborador de la red para la suscripción. Para obtener más información sobre la asignación de roles y permisos a las cuentas, vea [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Haga clic en **Nuevo** > **Redes** > **Red virtual**.
3. En la hoja **Red virtual**, deje seleccionado **Administrador de recursos** en el cuadro **Seleccionar un modelo de implementación** y haga clic en **Crear**.
4. En la hoja **Crear red virtual**, escriba o seleccione los valores para los siguientes ajustes y haga clic en **Crear**:
    - **Nombre**: tiene que ser único en el [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) dentro del que seleccione crear la red virtual. No se puede cambiar el nombre una vez creada la red virtual. Puede crear varias redes virtuales con el tiempo. Vea [Convenciones de nomenclatura](/azure/architecture/best-practices/naming-conventions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions) para obtener sugerencias de nombres. El uso de una convención de nomenclatura puede facilitar la administración de varias redes virtuales.
    - **Espacio de direcciones**: especifique el espacio de direcciones con la notación CIDR. El espacio de direcciones que defina puede ser público o privado (RFC 1918). Tanto si se define el espacio de direcciones como público o como privado, el espacio de direcciones es accesible solo desde dentro de la red virtual, desde redes virtuales conectadas entre sí y desde las redes locales que se hayan conectado a la red virtual. No se pueden agregar los siguientes espacios de direcciones:
        - 224.0.0.0/4 (multidifusión)
        - 255.255.255.255/32 (difusión)
        - 127.0.0.0/8 (bucle invertido)
        - 169.254.0.0/16 (local de vínculo)
        - 168.63.129.16/32 (DNS interno)

      Aunque solo se puede definir un espacio de direcciones cuando se crea la red virtual, puede agregar varios espacios de direcciones una vez creada la red virtual. Para obtener información sobre cómo agregar un espacio de direcciones a una red virtual existente, vea [Agregar o quitar un espacio de direcciones](#add-address-spaces) en este artículo.

      >[!WARNING]
      >Si una red virtual tiene espacios de direcciones que se superponen con otra red virtual o red local, las dos redes no se pueden conectar. Antes de definir un espacio de direcciones, tenga en cuenta si es posible que en el futuro quiera conectar la red virtual a otras redes virtuales o redes locales.
      >
      >

    - **Nombre de subred**: el nombre de la subred debe ser único dentro de la red virtual. El nombre de subred no se puede cambiar después de crear la subred. El portal requiere que se defina una subred al crear una red virtual, aunque una red virtual no necesita tener ninguna subred. En el portal, solo se puede definir una subred cuando se crea una red virtual. Puede agregar más subredes a la red virtual más adelante, una vez creada la red virtual. Para agregar una subred a una red virtual, vea [Crear una subred](virtual-network-manage-subnet.md#create-subnet) en [Crear, cambiar o eliminar subredes](virtual-network-manage-subnet.md). Puede crear una red virtual que tenga varias subredes mediante la CLI de Azure o PowerShell.

      >[!TIP]
      >A veces, los administradores crean diferentes subredes para filtrar o controlar el enrutamiento de tráfico entre ellas. Antes de definir subredes, considere cómo quiere filtrar y enrutar el tráfico entre ellas. Para obtener más información sobre el filtrado de tráfico entre subredes, vea [Grupos de seguridad de red](virtual-networks-nsg.md). Azure realiza el enrutamiento de tráfico entre subredes automáticamente, pero puede invalidar las rutas predeterminadas de Azure. Para obtener información sobre cómo invalidar el enrutamiento de tráfico de subred predeterminado de Azure, vea [Rutas definidas por el usuario](virtual-networks-udr-overview.md).
      >

    - **Intervalo de direcciones de subred**: tiene que estar dentro del espacio de direcciones que especificó para la red virtual. El menor intervalo que se puede especificar es /29, lo que proporciona ocho direcciones IP de subred. De conformidad con el protocolo, Azure reserva la primera y la última dirección de cada subred. Otras tres direcciones están reservadas para el uso del servicio de Azure. Como resultado, una red virtual con un intervalo de direcciones de subred de /29 tiene solo tres direcciones IP utilizables. Si planea conectar una red virtual a una puerta de enlace VPN, debe crear una subred de puerta de enlace. Más información sobre las [consideraciones específicas del intervalo de direcciones de las subredes de puerta de enlace](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). En determinadas circunstancias se puede cambiar el intervalo de direcciones una vez creada la subred. Para obtener información sobre cómo cambiar un intervalo de direcciones de subred, vea [Cambiar la configuración de subred](#change-subnet) en [Agregar, cambiar o eliminar subredes](virtual-network-manage-subnet.md).
    - **Suscripción**: seleccione una [suscripción](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). No se puede usar la misma red virtual en más de una suscripción de Azure. Pero se puede conectar una red virtual de una suscripción a las redes virtuales de otras suscripciones. Para conectar redes virtuales en distintas suscripciones, use Azure VPN Gateway o emparejamiento de red virtual. Cualquier recurso de Azure que se conecte a la red virtual debe estar en la misma suscripción que la red virtual.
    - **Grupo de recursos**: seleccione un [grupo de recursos existente](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) o cree uno nuevo. Un recurso de Azure que se conecta a la red virtual puede estar en el mismo grupo de recursos que la red virtual o en otro diferente.
    - **Ubicación**: seleccione una [ubicación](https://azure.microsoft.com/regions/) de Azure, también conocida como región. Una red virtual solo puede estar en una ubicación de Azure. Pero se puede conectar una red virtual en una ubicación a una red virtual en otra ubicación mediante el uso de VPN Gateway. Cualquier recurso de Azure que se conecte a la red virtual debe estar en la misma ubicación que la red virtual.

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI de Azure|[az network vnet create](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name = "view-vnet"></a>Visualización de las redes virtuales y su configuración

Para ver las redes virtuales y la configuración:

1. Inicie sesión en el [portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol Colaborador de la red para la suscripción. Para obtener más información sobre la asignación de roles y permisos a las cuentas, consulte [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. En el cuadro de búsqueda del portal, escriba **redes virtuales**. En los resultados de la búsqueda, haga clic en **Redes virtuales**.
3. En la hoja **Redes virtuales**, haga clic en la red virtual para la que quiere ver la configuración.
4. Los siguientes ajustes se muestran en la hoja de la red virtual seleccionada:
    - **Información general**: proporciona información sobre la red virtual, incluido el espacio de direcciones y los servidores DNS. En la siguiente captura de pantalla se muestra la configuración de información general de una red virtual denominada **MyVNet**:

        ![Información general de interfaz de red](./media/virtual-network-manage-network/vnet-overview.png)

      En la hoja **Información general**, puede mover una red virtual a una suscripción o grupo de recursos diferente. Para obtener información sobre cómo mover una red virtual, vea [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json). En el artículo se enumeran los requisitos previos y se indica cómo trasladar recursos mediante Azure Portal, PowerShell y la CLI de Azure. Todos los recursos que están conectados a la red virtual se deben mover con la red virtual.
    - **Espacio de direcciones**: se enumeran los espacios de direcciones asignados a la red virtual. Para obtener información sobre cómo agregar y quitar un espacio de direcciones, complete los pasos en la sección [Agregar o quitar un espacio de direcciones](#address-spaces) de este artículo.
    - **Dispositivos conectados**: se muestran todos los recursos que están conectados a la red virtual. En la captura de pantalla anterior, hay tres interfaces de red y un equilibrador de carga conectados a la red virtual. Se muestran todos los nuevos recursos que se creen y conecten a la red virtual. Si elimina un recurso que estaba conectado a la red virtual, ya no aparecerá en la lista.
    - **Subredes**: se muestra una lista de las subredes que existen dentro de la red virtual. Para obtener información sobre cómo agregar y quitar una subred, consulte [Crear una subred](virtual-network-manage-subnet.md#create-subnet) y [Eliminar una subred](virtual-network-manage-subnet.md#delete-subnet) en [Agregar, cambiar o eliminar subredes](virtual-network-manage-subnet.md).
    - **Servidores DNS**: puede especificar si el servidor DNS interno de Azure o un servidor DNS personalizado proporciona resolución de nombres para los dispositivos que están conectados a la red virtual. Al crear una red virtual mediante Azure Portal, los servidores DNS de Azure se usan para la resolución de nombres dentro de una red virtual, de forma predeterminada. Para modificar los servidores DNS, complete los pasos de la sección [Agregar, cambiar o eliminar un servidor DNS](#dns-servers) de este artículo.
    - **Emparejamientos**: si hay emparejamientos existentes en la suscripción, estos aparecerán aquí. Puede ver la configuración de emparejamientos existentes, o crear, cambiar o eliminar emparejamientos. Para obtener más información sobre emparejamiento, vea [Emparejamiento de redes virtuales de Azure](virtual-network-peering-overview.md).
    - **Propiedades**: muestra la configuración de la red virtual, incluidos el identificador de recurso de la red virtual y la suscripción de Azure en la que se encuentra.
    - **Diagrama**: el diagrama proporciona una representación visual de todos los dispositivos conectados a la red virtual. El diagrama muestra alguna información clave sobre los dispositivos. Para administrar un dispositivo en esta vista, en el diagrama, haga clic en el dispositivo.
    - **Configuración común de Azure**: para obtener más información sobre la configuración común de Azure, vea la siguiente información:
        *   [Registro de actividad](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
        *   [Control de acceso (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
        *   [Etiquetas](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)
        *   [Bloqueos](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        *   [Script de automatización](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI de Azure|[az network vnet show](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork/?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="add-address-spaces"></a>Agregar o quitar un espacio de direcciones

Puede agregar y quitar espacios de direcciones de una red virtual. Un espacio de direcciones tiene que especificarse en notación CIDR y no puede superponerse con otros espacios de direcciones dentro de la misma red virtual. Los espacios de direcciones que defina pueden ser públicos o privados (RFC 1918). Tanto si se define el espacio de direcciones como público o como privado, el espacio de direcciones es accesible solo desde dentro de la red virtual, desde redes virtuales conectadas entre sí y desde las redes locales que se hayan conectado a la red virtual. No se pueden agregar los siguientes espacios de direcciones:

- 224.0.0.0/4 (multidifusión)
- 255.255.255.255/32 (difusión)
- 127.0.0.0/8 (bucle invertido)
- 169.254.0.0/16 (local de vínculo)
- 168.63.129.16/32 (DNS interno)

Para agregar o quitar un espacio de direcciones:

1. Inicie sesión en el [portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol Colaborador de la red para la suscripción. Para obtener más información sobre la asignación de roles y permisos a las cuentas, consulte [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. En el cuadro de búsqueda del portal, escriba **redes virtuales**. En los resultados de la búsqueda, seleccione **Redes virtuales**.
3. En la hoja **Redes virtuales**, haga clic en la red virtual para la que quiere agregar o quitar un espacio de direcciones.
4. En la hoja de la red virtual, en **CONFIGURACIÓN**, haga clic en **Espacio de direcciones**.
5. En la hoja que aparece para el espacio de direcciones, realice una de las siguientes opciones:
    - **Agregar un espacio de direcciones**: escriba el nuevo espacio de direcciones. El espacio de direcciones no puede superponerse a un espacio de direcciones existente definido para la red virtual.
    - **Quitar un espacio de direcciones**: haga clic con el botón derecho en un espacio de direcciones y, después, haga clic en **Quitar**. Si existe una subred en el espacio de direcciones, no se puede quitar el espacio de direcciones. Para quitar un espacio de direcciones, es necesario eliminar primero las subredes que existen en ese espacio (y todos los recursos conectados a las subredes).
6. Haga clic en **Guardar**.

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI de Azure|Solo Resource Manager|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="dns-servers"></a>Agregar, cambiar o quitar un servidor DNS

Todas las máquinas virtuales que están conectadas a la red virtual se registran con los servidores DNS que especifique para la red virtual. También usan el servidor DNS especificado para la resolución de nombres. Cada interfaz de red (NIC) en una máquina virtual puede tener su propia configuración de servidor DNS. Si una NIC tiene su propia configuración de servidor DNS, esta invalida la configuración del servidor DNS para la red virtual. Para obtener más información sobre la configuración de DNS de NIC, vea [Configuración y tareas de la interfaz de red](virtual-network-network-interface.md#change-dns-servers). Para obtener más información sobre la resolución de nombres para las máquinas virtuales y las instancias de rol de Azure Cloud Services, vea [Resolución de nombres para las máquinas virtuales e instancias de rol](virtual-networks-name-resolution-for-vms-and-role-instances.md). Para agregar, cambiar o quitar un servidor DNS:

1. Inicie sesión en el [portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol Colaborador de la red para la suscripción. Para obtener más información sobre la asignación de roles y permisos a las cuentas, vea [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. En el cuadro de búsqueda del portal, escriba **redes virtuales**. En los resultados de la búsqueda, seleccione **Redes virtuales**.
3. En la hoja **Redes virtuales**, haga clic en la red virtual en la que quiere cambiar la configuración DNS.
4. En la hoja de la red virtual, en **CONFIGURACIÓN**, haga clic en **Servidores DNS**.
5. Seleccione una de las opciones siguientes en la hoja en la que se enumeran los servidores DNS:
    - **Predeterminado (proporcionado por Azure)**: todos los nombres de los recursos y las direcciones IP privadas se registran automáticamente en los servidores DNS de Azure. Puede resolver nombres entre los recursos conectados a la misma red virtual. No se puede usar esta opción para resolver nombres entre redes virtuales. Para resolver nombres de otras redes virtuales, tiene que usar un servidor DNS personalizado.
    - **Personalizado**: puede agregar uno o varios servidores, hasta el límite de Azure para una red virtual. Para obtener más información sobre los límites de servidor DNS, vea [Límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Tiene las siguientes opciones:
        - **Agregar una dirección**: agrega el servidor a la lista de servidores DNS de la red virtual. Esta opción también registra el servidor DNS con Azure. Si ya ha registrado un servidor DNS con Azure, puede seleccionarlo en la lista.
        - **Quitar una dirección**: haga clic en la **X** junto al servidor que quiere quitar. Eliminar el servidor solo lo quita de esta lista de redes virtuales. El servidor DNS seguirá registrado en Azure para que lo usen otras redes virtuales.
        - **Reordenar direcciones de servidor DNS**: es importante comprobar que se enumeran los servidores DNS en el orden correcto para su entorno. Las listas de servidores DNS se usan en el orden en que se especifican. No funcionan como una instalación round robin. Si se puede acceder al primer servidor DNS de la lista, el cliente usa ese servidor DNS, con independencia de si el servidor DNS funciona correctamente. Quite todos los servidores DNS que aparecen y vuelva a agregarlos en el orden que desee.
        - **Cambiar una dirección**: resalte el servidor DNS en la lista y después escriba el nuevo nombre.
6. Haga clic en **Guardar**.
7. Reinicie las máquinas virtuales conectadas a la red virtual para que se les asigne la nueva configuración de servidor DNS. Hasta que se reinicien, las máquinas virtuales seguirán usando la configuración de DNS que consideran actual.

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI de Azure|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="delete-vnet"></a>Eliminación de una red virtual

Solo se puede eliminar una red virtual si no tiene recursos conectados. Si hay recursos conectados a cualquier subred dentro de la red virtual, primero tiene que eliminarlos. Los pasos necesarios para eliminar un recurso varían según el recurso. Para obtener más información sobre cómo eliminar los recursos conectados a una subred, lea la documentación de cada tipo de recurso que quiera eliminar. Para eliminar una red virtual:

1. Inicie sesión en el [portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol Colaborador de la red para la suscripción. Para obtener más información sobre la asignación de roles y permisos a las cuentas, vea [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. En el cuadro de búsqueda del portal, escriba **redes virtuales**. En los resultados de la búsqueda, haga clic en **Redes virtuales**.
3. En la hoja **Redes virtuales**, seleccione la red virtual que quiere eliminar.
4. En la hoja de la red virtual, para confirmar que no hay ningún dispositivo conectado a la red virtual, en **CONFIGURACIÓN**, haga clic en **Dispositivos conectados**. Si hay dispositivos conectados, primero debe eliminarlos antes de poder eliminar la red virtual. Si no hay ningún dispositivo conectado, haga clic en **Información general**.
5. En la parte superior de la hoja, haga clic en el icono **Eliminar**.
6. Para confirmar la eliminación de la red virtual, haga clic en **Sí**.


**Comandos**

|Herramienta|Comando|
|---|---|
|CLI de Azure|[azure network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetwork](/powershell/module/azurerm.network/remove-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>Pasos siguientes

- Para crear una máquina virtual y después conectarla a una red virtual, vea [Crear una red virtual y conectar máquinas virtuales](virtual-network-get-started-vnet-subnet.md#create-vms).
- Para filtrar el tráfico de red entre subredes dentro de una red virtual, vea [Creación de grupos de seguridad de red con Azure Portal](virtual-networks-create-nsg-arm-pportal.md).
- Para emparejar una red virtual con otra, vea [Crear un emparejamiento de red virtual](virtual-network-create-peering.md#portal).
- Para obtener información sobre las opciones para conectar una red virtual a una red local, vea [Acerca de VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams).
