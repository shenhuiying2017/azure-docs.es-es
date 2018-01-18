---
title: "Creación, cambio o eliminación de una interfaz de red de Azure | Microsoft Docs"
description: "Obtenga más información sobre qué es una interfaz de red y cómo crearla, cambiar la configuración y eliminarla."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: jdial
ms.openlocfilehash: 9f1cf113f75bc5a96af8c33d4b83d1bd0f5c6efd
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="create-change-or-delete-a-network-interface"></a>Creación, cambio o eliminación de una interfaz de red

Aprenda a crear, cambiar la configuración y eliminar una interfaz de red. Una interfaz de red permite que una máquina virtual de Azure se comunique con Internet, Azure y los recursos locales. Al crear una máquina virtual desde Azure Portal, este crea una interfaz de red con la configuración predeterminada. En su lugar, puede crear interfaces de red con una configuración personalizada y agregar una o varias interfaces de red a una máquina virtual al crearla. También puede cambiar la configuración predeterminada de la interfaz de red en una interfaz de red existente. En este artículo se explica cómo crear una interfaz de red con opciones personalizadas, cambiar la configuración existente, como la asignación de filtros de red (grupo de seguridad de red), la asignación de subredes, la configuración del servidor DNS y el reenvío IP, y, finalmente, cómo eliminar una interfaz de red.

Si tiene que agregar, cambiar o quitar direcciones IP para una interfaz de red, consulte el artículo para [administrar las direcciones IP](virtual-network-network-interface-addresses.md). Si necesita agregar a interfaces de red, o quitar interfaces de red de máquinas virtuales, consulte el artículo [Incorporación de interfaces de red a máquinas virtuales o eliminación de estas](virtual-network-network-interface-vm.md).


## <a name="before-you-begin"></a>Antes de empezar

Antes de llevar a cabo ningún paso de las secciones de este artículo, realice las siguientes tareas:

- Revise el artículo sobre los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para más información sobre los límites de las interfaces de red.
- Inicie sesión con una cuenta de Azure en [Azure Portal](https://portal.azure.com), la interfaz de la línea de comandos (CLI) de Azure o Azure PowerShell. Si todavía no tiene una cuenta de Azure, regístrese para obtener una [cuenta de evaluación gratuita](https://azure.microsoft.com/free).
- Si usa comandos de PowerShell para completar las tareas de este artículo, [instale y configure Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de los commandlets de Azure PowerShell. Para obtener ayuda con los comandos de PowerShell, con ejemplos, escriba `get-help <command> -full`.
- Si usa comandos de la interfaz de la línea de comandos (CLI) de Azure para completar las tareas de este artículo, [instale y configure la CLI de Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de la CLI de Azure. Para obtener ayuda con los comandos de la CLI, escriba `az <command> --help`. En lugar de instalar la CLI y sus requisitos previos, puede usar Azure Cloud Shell. Azure Cloud Shell es un shell de Bash gratuito que se puede ejecutar directamente en Azure Portal. Tiene la CLI de Azure preinstalada y configurada para utilizar con su cuenta. Para usar Cloud Shell, haga clic en el botón Cloud Shell **> _** situado en la parte superior del [Portal](https://portal.azure.com).

## <a name="create-a-network-interface"></a>Crear una interfaz de red

Al crear una máquina virtual desde Azure Portal, este crea una interfaz de red con la configuración predeterminada. Si prefiere especificar toda la configuración de la interfaz de red, puede crear una interfaz de red con una configuración personalizada y asociar la interfaz de red a una máquina virtual al crear esta última (con PowerShell o la CLI de Azure). También puede crear una interfaz de red y agregarla a una máquina virtual existente (con PowerShell o la CLI de Azure). Para obtener información sobre cómo crear una máquina virtual con una interfaz de red existente, o cómo agregar interfaces de red a máquinas virtuales existentes, o quitarlas, consulte el artículo [Adición o eliminación de interfaces de red](virtual-network-network-interface-vm.md). En la misma ubicación y suscripción donde vaya a crear la interfaz de red, debe tener previamente una [red virtual](virtual-networks-create-vnet-arm-pportal.md).

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *interfaces de red*. Cuando **interfaces de red** aparezca en los resultados de búsqueda, haga clic en él.
3. En la hoja **Interfaces de red** que aparece, haga clic en **+Agregar**.
4. En la hoja **Crear interfaz de red** que aparece, escriba o seleccione los valores de las siguientes opciones y haga clic en **Crear**:

    |Configuración|¿Necesario?|Detalles|
    |---|---|---|
    |NOMBRE|Sí|El nombre debe ser único dentro del grupo de recursos que seleccione. Con el tiempo, probablemente tendrá varias interfaces de red en la suscripción de Azure. Lea el artículo de [convenciones de nomenclatura](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions) para obtener sugerencias sobre la creación de convenciones de nomenclatura para facilitar la administración de la interfaz de red. El nombre no se puede cambiar una vez creada la interfaz de red.|
    |Red virtual|Sí|Seleccione la red virtual para la interfaz de red. Solo se puede asignar una interfaz de red a una red virtual que exista en la misma suscripción y ubicación que la interfaz de red. Una vez creada la interfaz de red, no se puede cambiar la red virtual a la que está asignada. La máquina virtual que se agrega a la interfaz de red también debe existir en la misma ubicación y suscripción que la interfaz de red.|
    |Subred|Sí|Seleccione una subred dentro de la red virtual que seleccionó. Puede cambiar la subred a la que está asignada la interfaz de red después de crearla.|
    |Asignación de la dirección IP privada|Sí| En esta configuración, va a elegir el método de asignación para la dirección IPv4. Elija entre los siguientes métodos de asignación: **Dinámico:** al seleccionar esta opción, Azure asigna automáticamente la siguiente dirección disponible del espacio de direcciones de la subred seleccionada. **Estático:** al seleccionar esta opción, debe asignar manualmente una dirección IP disponible del espacio de direcciones de la subred seleccionada. Las direcciones estática y dinámica no cambian hasta que usted realice algún cambio elimine la interfaz de red. Una vez creada la interfaz de red, puede cambiar el método de asignación. El servidor DHCP de Azure asigna esta dirección a la interfaz de red en el sistema operativo de la máquina virtual.|
    |Grupo de seguridad de red|Sin | Déjelo establecido en **Ninguno**, seleccione un [grupo de seguridad de red](virtual-networks-nsg.md) existente o [cree uno](virtual-networks-create-nsg-arm-pportal.md). Los grupos de seguridad de red permiten filtrar el tráfico de red hacia y desde una interfaz de red. Puede aplicar un grupo de seguridad de red, o ninguno, a una interfaz de red. También puede aplicar un grupo de seguridad de red, o ninguno, a la subred a la que está asignada la interfaz de red. Cuando un grupo de seguridad de red se aplica a una interfaz de red y a la subred a la que está asignada la interfaz de red, en ocasiones se producen resultados inesperados. Para solucionar problemas de los grupos de seguridad de red aplicados a interfaces de red y a subredes, consulte el artículo [Solución de problemas de grupos de seguridad de red](virtual-network-nsg-troubleshoot-portal.md#nsg).|
    |La suscripción|Sí|Seleccione una de las [suscripciones](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) de Azure. La máquina virtual asociada a una interfaz de red y la red virtual a la que está conectada deben existir en la misma suscripción.|
    |Dirección IP privada (IPv6)|Sin | Si activa esta casilla, se asigna una dirección IPv6 a la interfaz de red, además de la dirección IPv4 ya asignada. Consulte la sección [IPv6](#IPv6) de este artículo para obtener información importante sobre el uso de IPv6 con interfaces de red. No se puede seleccionar un método de asignación a la dirección IPv6. Si desea asignar una dirección IPv6, hay que asignarla con el método dinámico.
    |Nombre de IPv6 (solo aparece cuando la casilla **Dirección IP privada (IPv6)** está activada) |Sí, si la casilla **Dirección IP privada (IPv6)** está activada.| Este nombre se asigna a una configuración IP secundaria de la interfaz de red. Obtenga más información sobre las configuraciones de IP en la sección [Visualización de la configuración de la interfaz de red](#view-network-interface-settings) en este artículo.|
    |Grupos de recursos|Sí|Seleccione un [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) existente o cree uno. La interfaz de red puede existir en el mismo grupo de recursos que la máquina virtual a la que está asociada o conectada, o en otro.|
    |La ubicación|Sí|La máquina virtual asociada a una interfaz de red y la red virtual a la que está conectada deben existir en la misma [ubicación](https://azure.microsoft.com/regions), que también se conoce como región.|

El portal no proporciona la opción de asignar una dirección IP pública a la interfaz de red al crearla, aunque el portal crea una dirección IP pública y la asigna a una interfaz de red cuando se crea una máquina virtual mediante el portal. Para aprender a agregar una dirección IP pública a la interfaz de red después de crearla, consulte el artículo sobre la [administración de direcciones IP](virtual-network-network-interface-addresses.md). Si desea crear una interfaz de red con una dirección IP pública, debe utilizar la CLI o PowerShell para crearla.

>[!Note]
> Azure asigna una dirección MAC a la interfaz de red solo después de asociar la interfaz de red a una máquina virtual y que esta se inicie la primera vez. No se puede especificar la dirección MAC que Azure asigna a la interfaz de red. La dirección MAC permanece asignada a la interfaz de red hasta que esta se elimina o se cambia la dirección IP privada asignada a la configuración de IP principal de la interfaz de red principal. Para más información sobre las direcciones IP y las configuraciones de IP, consulte sobre la [administración de direcciones IP](virtual-network-network-interface-addresses.md).

**Comandos**

|Herramienta|Get-Help|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|

## <a name="view-network-interface-settings"></a>Visualización de la configuración de la interfaz de red

Puede ver y cambiar la mayoría de las opciones de una interfaz de red después de crearla.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *interfaces de red*. Cuando **interfaces de red** aparezca en los resultados de búsqueda, haga clic en él.
3. En la hoja **Interfaces de red** que aparece, haga clic en la interfaz de red que desea ver o cuya configuración desea cambiar.
4. Las siguientes opciones se muestran en la hoja que aparece para la interfaz de red seleccionada:
    - **Información general:** proporciona información acerca de la interfaz de red, como las direcciones IP asignadas a ella, la red o subred virtual a la que está asignada la interfaz de red y la máquina virtual a la que está asociada la interfaz de red (si está asociada a una). La siguiente imagen muestra la configuración de información general de una interfaz de red denominada **mywebserver256**: ![Información general de interfaz de red](./media/virtual-network-network-interface/nic-overview.png) Puede mover una interfaz de red a un grupo de recursos o suscripción distintos; para ello, haga clic en (**cambiar**) junto al **Grupo de recursos** o el **Nombre de la suscripción**. Si mueve la interfaz de red, debe mover con ella todos sus recursos relacionados. Por ejemplo, si la interfaz de red está asociada a una máquina virtual, también debe mover la máquina virtual y otros recursos relacionados con ella. Para trasladar una interfaz de red, consulte cómo [trasladar recursos a un nuevo grupo de recursos o suscripción](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal). En el artículo se enumeran los requisitos previos y se indica cómo trasladar recursos mediante Azure Portal, PowerShell y la CLI de Azure.
    - **Configuraciones de IP:** las direcciones IPv4 e IPv6 públicas y privadas se asignan a las configuraciones de IP que se muestran a continuación. Si se asigna una dirección IPv6 a una configuración de IP, la dirección no se muestra. Obtenga más información sobre las configuraciones de IP y cómo agregar y quitar IP direcciones en el artículo sobre la [configuración de direcciones IP para una interfaz de red de Azure](virtual-network-network-interface-addresses.md). El reenvío de IP y la asignación de subred también se configuran en esta sección. Para más información acerca de estas opciones, consulte las secciones [Habilitación o deshabilitación del reenvío IP](#enable-or-disable-ip-forwarding) y [Cambio de la asignación de subred](#change-subnet-assignment) de este artículo.
    - **Servidores DNS:** puede especificar a qué servidor DNS asignarán una interfaz de red los servidores DHCP de Azure. La configuración de la red virtual puede heredar la configuración de la red virtual a la que está asignada la interfaz de red, o tener una configuración personalizada que reemplaza la configuración de la red virtual a la que está asignada. Para modificar lo que se muestra, complete los pasos de la sección [Cambio de los servidores DNS](#change-dns-servers) de este artículo.
    - **Grupo de seguridad de red (NSG):** muestra el grupo de seguridad de red asociado a la interfaz de red (en caso de haberlos). Un grupo de seguridad de red contiene reglas de entrada y salida para filtrar el tráfico de red de la interfaz de red. Si hay un grupo de seguridad de red asociado a la interfaz de red, se muestra el nombre del grupo de seguridad de red asociado. Para modificar lo que se muestra, complete los pasos de la sección [Administrar las asociaciones](virtual-network-manage-nsg-arm-portal.md#manage-associations) del artículo Administración de grupos de seguridad de red mediante el portal.
    - **Propiedades:** muestra las opciones de configuración clave de la interfaz de red, incluida la dirección MAC (en blanco si la interfaz de red no está asociada a una máquina virtual) y la suscripción donde se encuentra.
    - **Reglas de seguridad eficaces:** las reglas de seguridad se muestran si la interfaz de red está asociada a una máquina virtual en ejecución, y hay un grupo de seguridad de red asociado a la interfaz de red, a la subred a la que está asignada, o ambas. Para más información acerca de lo que se muestra, lea el artículo de [solución de problemas con los grupos de seguridad de red](virtual-network-nsg-troubleshoot-portal.md#nsg). Consulte el artículo [Grupos de seguridad de red](virtual-networks-nsg.md) para más información sobre los NSG.
    - **Rutas eficaces:** las rutas se muestran si la interfaz de red está asociada a una máquina virtual en ejecución. Las rutas son una combinación de las rutas predeterminadas de Azure, las rutas definidas por el usuario (UDR) y las rutas BGP que pueda haber en la subred a la cual está asignada la interfaz de red. Para más información acerca de lo que se muestra, lea el artículo [Solución de problemas de rutas](virtual-network-routes-troubleshoot-portal.md#view-effective-routes-for-a-network-interface). Más información sobre las rutas predeterminadas de Azure y las definidas por el usuario, lea el artículo de [rutas definidas por el usuario](virtual-networks-udr-overview.md).
    - **Opciones comunes de Azure Resource Manager:** para más información acerca de las opciones de configuración comunes de Azure Resource Manager, lea los artículos [Registro de actividad](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs), [Control de acceso (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control), [Etiquetas](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags), [Bloqueos](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) y [Script de Automation](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group).

**Comandos**

Si se asigna una dirección IPv6 a una interfaz de red, la salida de PowerShell devuelve el hecho de que la dirección está asignada, pero no devuelve la dirección asignada. De forma similar, la CLI devuelve el hecho de que la dirección está asignada, pero devuelve *null* en su salida para la dirección.

|Herramienta|Get-Help|
|---|---|
|CLI|[az network nic list](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#list) para ver las interfaces de red en la suscripción; [az network nic show](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#show) para ver la configuración de una interfaz de red|
|PowerShell|[Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) para ver las interfaces de red en la suscripción o ver la configuración de una interfaz de red|

## <a name="change-dns-servers"></a>Cambio de los servidores DNS

El servidor DHCP de Azure asigna el servidor DNS a la interfaz de red en el sistema operativo de la máquina virtual. El servidor DNS asignado tiene la configuración de servidor DNS de la interfaz de red. Para más información sobre las opciones de resolución de nombres de una interfaz de red, consulte el artículo [Resolución de nombres de máquinas virtuales](virtual-networks-name-resolution-for-vms-and-role-instances.md). La interfaz de red puede heredar la configuración de la red virtual o utilizar sus propias opciones que reemplacen las de la red virtual.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *interfaces de red*. Cuando **interfaces de red** aparezca en los resultados de búsqueda, haga clic en él.
3. En la hoja **Interfaces de red** que aparece, haga clic en la interfaz de red que desea ver o cuya configuración desea cambiar.
4. En **CONFIGURACIÓN** en la hoja de la interfaz de red seleccionada, haga clic en **Servidores DNS**.
5. Haga clic en:
    - **Heredar desde la red virtual (valor predeterminado)**: elija esta opción para heredar la configuración del servidor DNS definida para la red virtual a la que la interfaz de red está conectada. En el nivel de red virtual, se define un servidor DNS personalizado o el servidor DNS proporcionado por Azure. El servidor DNS proporcionado por Azure puede resolver los nombres de host de los recursos asignados a la misma red virtual. Para resolver los recursos asignados a otras redes virtuales, debe utilizarse FQDN.
    - **Personalizado**: puede configurar su propio servidor DNS para resolver nombres en diferentes redes virtuales. Escriba la dirección IP del servidor que desea usar como servidor DNS. La dirección del servidor DNS especificado se asigna solo a esta interfaz de red y reemplaza cualquier configuración DNS de la red virtual a la que está asignada la interfaz de red.
6. Haga clic en **Save**(Guardar).

**Comandos**

|Herramienta|Get-Help|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="enable-or-disable-ip-forwarding"></a>Habilitación o deshabilitación del reenvío IP

El reenvío IP permite a la máquina virtual a la que está asociada la interfaz de red:
- Recibir tráfico de red no destinado a una de las direcciones IP asignadas a cualquiera de las configuraciones de IP asignadas a la interfaz de red.
- Enviar tráfico de red con una dirección IP de origen diferente de la asignada a una de las configuraciones de IP de la interfaz de red.

La configuración debe estar habilitada para cada interfaz de red que esté asociada a la máquina virtual que recibe el tráfico y que esta debe reenviar. Una máquina virtual puede reenviar el tráfico si tiene varias interfaces de red o una sola interfaz de red asociada a él. Aunque el reenvío IP es una configuración de Azure, la máquina virtual también debe ejecutar una aplicación que pueda reenviar el tráfico, como aplicaciones de firewall, de optimización de WAN o de equilibrio de carga. Cuando una máquina virtual está ejecutando aplicaciones de red, la máquina virtual suele denominarse aplicación virtual de red. Puede ver una lista de las aplicaciones virtuales de red listas para su implementación en [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). El reenvío IP normalmente se usa con rutas definidas por el usuario. Para aprender más sobre las rutas definidas por el usuario, lea el artículo [Rutas definidas por el usuario](virtual-networks-udr-overview.md).

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *interfaces de red*. Cuando **interfaces de red** aparezca en los resultados de búsqueda, haga clic en él.
3. En la hoja **Interfaces de red** que aparece, haga clic en la interfaz de red para la cual desee habilitar o deshabilitar el reenvío IP.
4. Haga clic en **Configuraciones de IP** en sección **CONFIGURACIÓN** de la hoja de la interfaz de red seleccionada.
5. Haga clic en **Habilitado** o **Deshabilitado** (valor predeterminado) para cambiar la configuración.
6. Haga clic en **Save**(Guardar).

**Comandos**

|Herramienta|Get-Help|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet-assignment"></a>Cambio de la asignación de subred

Puede cambiar la subred, pero no la red virtual, a la que está asignada una interfaz de red.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *interfaces de red*. Cuando **interfaces de red** aparezca en los resultados de búsqueda, haga clic en él.
3. En la hoja **Interfaces de red** que aparece, haga clic en la interfaz de red que desea ver o cuya configuración desea cambiar.
4. Haga clic en **Configuraciones de IP** de **CONFIGURACIÓN** en la hoja de la interfaz de red seleccionada. Si junto a las direcciones IP privadas de las configuraciones de IP que se enumeran aquí aparece **(Estático)**, debe seguir los pasos a continuación y así cambiar al método dinámico de asignación de direcciones IP. Para cambiar la asignación de subred para la interfaz de red de todas las direcciones IP privadas, es obligatorio el método dinámico de asignación. Si las direcciones se han asignado con el método dinámico, vaya al paso cinco. Si se asignó alguna dirección IPv4 con el método estático, complete los pasos siguientes para cambiar al método de asignación dinámico:
    - Haga clic en la configuración de IP cuyo método de asignación de dirección IPv4 desea cambiar de la lista de configuraciones de IP.
    - En la hoja que aparece para la configuración de IP, haga clic en **Dinámica** para el método **Asignación**. No se puede asignar una dirección IPv6 con el método de asignación estático.
    - Haga clic en **Save**(Guardar).
5. Seleccione la subred a la que desea conectar la interfaz de red en la lista desplegable **Subred**.
6. Haga clic en **Save**(Guardar). Las nuevas direcciones dinámicas se asignan a partir del intervalo de direcciones de la subred nueva. Una vez asignada la interfaz de red a una nueva subred, si lo prefiere, podrá asignar direcciones IPv4 estáticas del nuevo intervalo de direcciones de subred. Para más información sobre la incorporación, el cambio o la eliminación de direcciones IP de una interfaz de red, lea el artículo sobre la [administración de direcciones IP](virtual-network-network-interface-addresses.md).

**Comandos**

|Herramienta|Get-Help|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-a-network-interface"></a>Eliminar una interfaz de red

Puede eliminar una interfaz de red siempre y cuando no esté asociada a una máquina virtual. Si está se está asociado a una máquina virtual, primero debe colocar la máquina virtual en estado detenido (desasignado) y, después, desasociar la interfaz de red de la máquina virtual, antes de poder eliminar la interfaz de red. Para desasociar una interfaz de red de una máquina virtual, complete los pasos de la sección de [desconexión de una interfaz de red de una máquina virtual](virtual-network-network-interface-vm.md#vm-remove-nic) del artículo sobre [incorporación o eliminación de interfaces de red](virtual-network-network-interface-vm.md). Al eliminar una máquina virtual, se desasocian todas las interfaces de red asociadas a ella, pero no se eliminan las interfaces de red.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *interfaces de red*. Cuando **interfaces de red** aparezca en los resultados de búsqueda, haga clic en él.
3. Haga clic con el botón derecho en la interfaz de red que desea eliminar y haga clic en **Eliminar**.
4. Haga clic en **Sí** para confirmar la eliminación de la interfaz de red.

Cuando se elimina una interfaz de red, se liberan las direcciones MAC o IP asignadas a ella.

**Comandos**

|Herramienta|Get-Help|
|---|---|
|CLI|[az network nic delete](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmNetworkInterface](/powershell/module/azurerm.network/remove-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>pasos siguientes
Para crear una máquina virtual con varias interfaces de red o direcciones IP, lea los siguientes artículos:

**Comandos**

|Task|Herramienta|
|---|---|
|Creación de una máquina virtual con varias NIC|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Creación de una máquina virtual con una sola interfaz de red y varias direcciones IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Creación de una máquina virtual con una sola interfaz de red y una dirección IPv6 privada (detrás de Azure Load Balancer)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Plantilla de Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
