---
title: "Creación, cambio o eliminación de interfaces de red de Azure | Microsoft Docs"
description: "Aprenda lo que son las interfaces de red (NIC) y a crearlas, cambiar la configuración y eliminarlas."
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
ms.date: 05/04/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: f1fb0f6348b579121be64bff4411952026f8528d
ms.contentlocale: es-es
ms.lasthandoff: 05/12/2017


---

# <a name="create-change-or-delete-network-interfaces"></a>Creación, cambio o eliminación de interfaces de red

Aprenda a crear, cambiar la configuración y eliminar interfaces de red (NIC). Una NIC permite que una máquina virtual de Azure se comunique con Internet, Azure y los recursos locales. Al crear una máquina virtual desde Azure Portal, este crea una NIC con la configuración predeterminada. En su lugar, puede elegir crear NIC con opciones personalizadas y, al crearlas, agregar una o más a las máquinas virtuales. También puede cambiar la configuración de NIC predeterminada para las NIC existentes. En este artículo se explica cómo crear NIC con opciones personalizadas, cambiar la configuración de NIC existente (como la asignación de filtros de red: grupos de seguridad de red), asignar subredes, configurar el servidor DNS, reenviar direcciones IP y eliminar NIC. 

Si tiene que agregar, cambiar o eliminar las direcciones IP de una NIC, lea el artículo sobre la [incorporación, el cambio o la eliminación de direcciones IP](virtual-network-network-interface-addresses.md). Si necesita agregar NIC a máquinas virtuales o eliminarlas, lea el artículo sobre la [incorporación o eliminación de NIC](virtual-network-network-interface-vm.md). 


## <a name="before"></a>Antes de empezar

Complete las tareas siguientes antes de seguir los pasos de las secciones de este artículo:

- Revise el artículo sobre los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para información sobre los límites de NIC.
- Inicie sesión con una cuenta de Azure en Azure Portal, la interfaz de la línea de comandos (CLI) de Azure o Azure PowerShell. Si todavía no tiene una cuenta de Azure, regístrese para obtener una [cuenta de evaluación gratuita](https://azure.microsoft.com/free).
- Si va a usar comandos de PowerShell para realizar las tareas de este artículo, instale y configure Azure PowerShell mediante los pasos del artículo de [instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de los commandlets de Azure PowerShell. Para obtener ayuda con los comandos de PowerShell, con ejemplos, escriba `get-help <command> -full`.
- Si va a usar comandos de la interfaz de la línea de comandos (CLI) de Azure para realizar las tareas de este artículo, instale y configure la CLI de Azure mediante los pasos del artículo de [instalación y configuración de la CLI de Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de la CLI de Azure. Para obtener ayuda con los comandos de la CLI, escriba `az <command> --help`.

## <a name="create-nic"></a>Creación de una NIC
Al crear una máquina virtual desde Azure Portal, este crea una NIC con la configuración predeterminada. Si prefiere especificar toda la configuración de NIC, puede crear una NIC con opciones personalizadas y conectarla a una máquina virtual al crear esta última. También puede crear una NIC y agregarla a una máquina virtual existente. Para aprender a crear una máquina virtual con una NIC existente, o a agregar o eliminar NIC de las máquinas virtuales existentes, lea el artículo de [incorporación o eliminación de NIC](virtual-network-network-interface-vm.md). En la misma ubicación y suscripción donde vaya a crear la NIC, debe tener previamente una red virtual. Para aprender a crear una red virtual, lea el artículo de [creación de redes virtuales](virtual-networks-create-vnet-arm-pportal.md).

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *interfaces de red*. Cuando **interfaces de red** aparezca en los resultados de búsqueda, haga clic en él.
3. En la hoja **Interfaces de red** que aparece, haga clic en **+Agregar**.
4. En la hoja **Crear interfaz de red** que aparece, escriba o seleccione los valores de las siguientes opciones y haga clic en **Crear**:

    |Configuración|¿Necesario?|Detalles|
    |---|---|---|
    |Nombre|Sí|El nombre debe ser único dentro del grupo de recursos que seleccione. Con el tiempo, probablemente tendrá varias NIC en la suscripción de Azure. Lea el artículo de [convenciones de nomenclatura](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions) para sugerencias sobre la creación de convenciones de nomenclatura para facilitar la administración de NIC. El nombre no se puede cambiar una vez creada la NIC.|
    |Red virtual|Sí|Seleccione una red virtual a la cual conectar la NIC. Una NIC solo puede conectarse a una red virtual que exista en la misma ubicación y suscripción en la que se encuentre la NIC. Una vez creada una NIC, no se puede cambiar la red virtual a la que está conectada. La máquina virtual a la cual se agrega la NIC debe existir en la misma ubicación y suscripción que la NIC.|
    |Subred|Sí|Seleccione una subred dentro de la red virtual que seleccionó. Puede cambiar la subred a la que está conectada la NIC después de crearla.|
    |Asignación de la dirección IP privada|Sí| Elija entre los siguientes métodos de asignación: **Dinámico:** al seleccionar esta opción, Azure asigna automáticamente una dirección disponible del espacio de direcciones de la subred seleccionada. Azure puede asignar una dirección diferente a una NIC cuando su máquina virtual se inicia después de haberse detenido (haberse desasignado). La dirección sigue siendo la misma si la máquina virtual se reinicia y no estuvo en estado detenido (desasignado). **Estático:** al seleccionar esta opción, debe asignar manualmente una dirección IP disponible del espacio de direcciones de la subred seleccionada. Las direcciones estáticas no cambian hasta que se cambian o se elimina la NIC. Una vez creada la NIC, puede cambiar el método de asignación. El servidor DHCP de Azure asigna esta dirección a la NIC en el sistema operativo de la máquina virtual.|
    |Grupo de seguridad de red|No| Déjelo establecido en **Ninguno**, seleccione un grupo de seguridad de red (NSG) existente o cree uno. Los NSG permiten filtrar el tráfico de red entrante y saliente de la NIC. Consulte el artículo [Grupos de seguridad de red](virtual-networks-nsg.md) para más información sobre los NSG. Para crear un grupo de seguridad de red, lea el artículo de [creación de grupos de seguridad de red](virtual-networks-create-nsg-arm-pportal.md). A una NIC le puede aplicar un grupo de seguridad de red o ninguno. También puede aplicar un NSG, o ninguno, a la subred a la que está conectada la NIC. Cuando se aplica un grupo de seguridad de red a una NIC y a la subred a la cual está conectada, a veces se producen resultados inesperados. Para solucionar los problemas de los grupos de seguridad de red aplicados a las NIC, lea el artículo de [solución de problemas de los grupos de seguridad de red](virtual-network-nsg-troubleshoot-portal.md#view-effective-security-rules-for-a-network-interface).|
    |Subscription|Sí|Seleccione una de las [suscripciones](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) de Azure. La máquina virtual a la que se asocia una NIC y la red virtual a la que se conecta deben encontrarse en la misma suscripción.|
    |Grupos de recursos|Sí|Seleccione un [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) existente o cree uno. La NIC puede existir en el mismo grupo de recursos que la máquina virtual a la que está asociada o conectada, o en otro.|
    |Ubicación|Sí|La máquina virtual a la que se asocia una NIC y la red virtual a la que se conecta deben encontrarse en la misma [ubicación](https://azure.microsoft.com/regions), que se conoce también como región.|

El portal no ofrece la opción de asignar una dirección IP pública a la NIC al crearla, aunque asigne una dirección IP pública a la NIC cuando se crea una máquina virtual a través del portal. Para aprender a agregar una dirección IP pública a la NIC después de crearla, lea el artículo de [incorporación, cambio o eliminación de direcciones IP](virtual-network-network-interface-addresses.md). Si desea crear una NIC con una dirección IP pública, debe utilizar la CLI o PowerShell para crearla.

>[!Note]
> Azure asigna una dirección MAC a la NIC solo después de asociar la NIC a una máquina virtual y después de iniciar la máquina virtual por primera vez. No se puede especificar la dirección MAC que Azure asigna a la NIC. La dirección MAC permanece asignada a la NIC hasta que la NIC se elimina o se cambia la dirección IP privada asignada a la configuración de IP principal de la NIC principal. Para más información sobre las direcciones IP y las configuraciones de IP, lea el artículo de [incorporación, cambio o eliminación de direcciones IP](virtual-network-network-interface-addresses.md).

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/new-azurermnetworkinterface/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|

## <a name="view-nics"></a>Visualización de la configuración de la NIC

En la NIC puede ver y modificar la mayoría de opciones.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *interfaces de red*. Cuando **interfaces de red** aparezca en los resultados de búsqueda, haga clic en él.
3. En la hoja **Interfaces de red** que aparece, haga clic en la NIC que desea ver o cuya configuración desea cambiar.
4. Las siguientes opciones se muestran en la hoja que aparece para la NIC seleccionada:
    - **Overview** (Información general): proporciona información acerca de la NIC, como las direcciones IP asignadas, la red virtual o subred a la que la NIC está conectada y la máquina virtual a la que la NIC está asociada (si está asociada a una). La siguiente imagen muestra la configuración de información general de una NIC denominada **mywebserver256**:   ![Información general de interfaz de red](./media/virtual-network-network-interface/nic-overview.png) Puede mover una NIC a un grupo de recursos o suscripción distintos; para ello, haga clic en (**cambiar**) junto al **Grupo de recursos** o el **Nombre de la suscripción**. Si traslada la NIC, debe mover con ella todos sus recursos. Si la NIC está asociada a una máquina virtual, por ejemplo, también debe trasladar la máquina virtual y los demás recursos relacionados con ella. Para trasladar una NIC, consulte cómo [trasladar recursos a un nuevo grupo de recursos o suscripción](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal). En el artículo se enumeran los requisitos previos y se indica cómo trasladar recursos mediante Azure Portal, PowerShell y la CLI de Azure.
    - **Configuraciones de IP:** las direcciones IP públicas y privadas se asignan a una o varias configuraciones de IP para una NIC. Para más información sobre el número máximo de configuraciones de IP que admite una NIC, consulte los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Cada configuración de IP tiene asignada una dirección IP privada, y puede tener asociada una dirección IP pública. Para agregar, cambiar o eliminar configuraciones de IP de la NIC, complete los pasos de las secciones de [incorporación de una configuración de IP secundaria a una NIC](virtual-network-network-interface-addresses.md#create-ip-config), [cambio de una configuración de IP](virtual-network-network-interface-addresses.md#change-ip-config) o [eliminación de una configuración de IP](virtual-network-network-interface-addresses.md#delete-ip-config) del artículo de [incorporación, cambio o eliminación de direcciones IP](virtual-network-network-interface-addresses.md). El reenvío de IP y la asignación de subred también se configuran en esta sección. Para más información acerca de estas opciones, lea las secciones [Habilitación y deshabilitación del reenvío de IP](#ip-forwarding) y [Cambio de la asignación de subred](#subnet) de este artículo.
    - **Servidores DNS:** puede especificar a qué servidor DNS asignarán una NIC los servidores DHCP de Azure. La NIC puede heredar la configuración de la red virtual a la cual está conectada o utilizar una configuración personalizada que la invalide. Para modificar lo que se muestra, complete los pasos de la sección [Cambio de los servidores DNS](#dns) de este artículo.
    - **Grupo de seguridad de red (NSG):** muestra el grupo de seguridad de red asociado a la NIC (en caso de haberlos). Un grupo de seguridad de red contiene reglas de entrada y salida para filtrar el tráfico de red de la NIC. Si hay un NSG asociado a la NIC, se muestra el nombre del NSG asociado. Para modificar lo que se muestra, complete los pasos de la sección [Asociación o desasociación de un NSG de una interfaz de red](#associate-nsg), en este artículo.
    - **Propiedades:** muestra las opciones de configuración clave de la NIC, incluida la dirección MAC (en blanco si la NIC no está conectada a una red virtual) y la suscripción donde se encuentra.
    - **Reglas de seguridad eficaces:** las reglas de seguridad se muestran si la NIC está asociada a una máquina virtual en ejecución, y hay un NSG asociado a la NIC, a la subred a la que está conectada, o ambas. Para más información acerca de lo que se muestra, lea el artículo de [solución de problemas con los grupos de seguridad de red](virtual-network-nsg-troubleshoot-portal.md#view-effective-security-rules-for-a-network-interface). Consulte el artículo [Grupos de seguridad de red](virtual-networks-nsg.md) para más información sobre los NSG.
    - **Rutas eficaces:** las rutas se muestran si la NIC está conectada a una máquina virtual en ejecución. Las rutas son una combinación de las rutas predeterminadas de Azure, las rutas definidas por el usuario (UDR) y las rutas BGP que pueda haber en la subred a la cual está conectada la NIC. Para más información acerca de lo que se muestra, lea el artículo [Solución de problemas de rutas](virtual-network-routes-troubleshoot-portal.md#view-effective-routes-for-a-network-interface). Más información sobre las rutas predeterminadas de Azure y las definidas por el usuario, lea el artículo de [rutas definidas por el usuario](virtual-networks-udr-overview.md).
    - **Opciones comunes de Azure Resource Manager:** para más información acerca de las opciones de configuración comunes de Azure Resource Manager, lea los artículos [Registro de actividad](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs), [Control de acceso (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control), [Etiquetas](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags), [Bloqueos](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) y [Script de Automation](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group).

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|[az network nic list](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#list) para ver las NIC en la suscripción; [az network nic show](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#show) para ver la configuración de una NIC|
|PowerShell|[Get-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/get-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) para ver las NIC de la suscripción o la configuración de una NIC|

## <a name="dns"></a>Cambio de los servidores DNS

El servidor DNS lo asigna el servidor DHCP de Azure a la NIC en el sistema operativo de la máquina virtual. El servidor DNS asignado tiene la configuración de servidor DNS de las NIC. Para más información sobre las opciones de resolución de nombres de una NIC, lea el artículo [Resolución de nombres de máquinas virtuales](virtual-networks-name-resolution-for-vms-and-role-instances.md). La NIC puede heredar la configuración de la red virtual o utilizar sus propias opciones únicas que invaliden las de la red virtual.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *interfaces de red*. Cuando **interfaces de red** aparezca en los resultados de búsqueda, haga clic en él.
3. En la hoja **Interfaces de red** que aparece, haga clic en la NIC que desea ver o cuya configuración desea cambiar.
4. En **CONFIGURACIÓN** en la hoja de la NIC seleccionada, haga clic en **Servidores DNS**.
5. Haga clic en:
    - **Heredar desde la red virtual (valor predeterminado)**: elija esta opción para heredar la configuración del servidor DNS definida para la red virtual a la que la NIC está conectada. En el nivel de red virtual, se define un servidor DNS personalizado o el servidor DNS proporcionado por Azure. El servidor DNS proporcionado por Azure puede resolver los nombres de host de los recursos conectados a la misma red virtual. Para resolver los recursos conectados a otras redes virtuales, debe utilizarse FQDN.
    - **Personalizado**: puede configurar su propio servidor DNS para resolver nombres en diferentes redes virtuales. Escriba la dirección IP del servidor que desea usar como servidor DNS. La dirección del servidor DNS que especifique se asigna solo a esta NIC e invalida la configuración DNS de la red virtual a la que la NIC está conectada.
6. Haga clic en **Guardar**.

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="ip-forwarding"></a>Habilitación y deshabilitación del reenvío de IP

El reenvío IP permite que la máquina virtual a la que una NIC está conectada:
- Recibir tráfico de red no destinado a una de las direcciones IP asignadas a cualquiera de las configuraciones de IP asignadas a la NIC.
- Enviar tráfico de red con una dirección IP de origen diferente de la asignada a una de las configuraciones de IP de la NIC.

La configuración debe habilitarse para cada NIC asociada a la máquina virtual que recibe el tráfico que la máquina virtual debe reenviar. Una máquina virtual puede reenviar el tráfico tanto si tiene varias como una sola NIC. Aunque el reenvío IP es una configuración de Azure, la máquina virtual también debe ejecutar una aplicación que pueda reenviar el tráfico, como aplicaciones de firewall, de optimización de WAN o de equilibrio de carga. Cuando una máquina virtual está ejecutando aplicaciones de red, la máquina virtual suele denominarse dispositivo virtual de red (NVA). Puede ver una lista de NVA listas para su implementación en [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). El reenvío IP normalmente se usa con rutas definidas por el usuario. Para aprender más sobre las rutas definidas por el usuario, lea el artículo [Rutas definidas por el usuario](virtual-networks-udr-overview.md).

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *interfaces de red*. Cuando **interfaces de red** aparezca en los resultados de búsqueda, haga clic en él.
3. En la hoja **Interfaces de red** que aparece, haga clic en la NIC para la cual desee habilitar o deshabilitar el reenvío de IP.
4. Haga clic en **Configuraciones de IP** en sección **CONFIGURACIÓN** de la hoja de la NIC seleccionada.
5. Haga clic en **Habilitado** o **Deshabilitado** (valor predeterminado) para cambiar la configuración.
6. Haga clic en **Guardar**.

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="subnet"></a>Cambio de la asignación de subred

Puede cambiar la subred a la que está conectada la NIC, pero no la red virtual.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *interfaces de red*. Cuando **interfaces de red** aparezca en los resultados de búsqueda, haga clic en él.
3. En la hoja **Interfaces de red** que aparece, haga clic en la NIC que desea ver o cuya configuración desea cambiar.
4. Haga clic en **Configuraciones de IP** de **CONFIGURACIÓN** en la hoja de la NIC seleccionada. Si junto a las direcciones IP privadas de las configuraciones de IP que se enumeran aquí aparece **(Estático)**, debe seguir los pasos a continuación y así cambiar al método dinámico de asignación de direcciones IP. Para cambiar la asignación de subred para la NIC de todas las direcciones IP privadas, es obligatorio el método dinámico de asignación. Si las direcciones se han asignado con el método dinámico, vaya al paso cinco. Si se asignó alguna dirección con el método estático, complete los pasos siguientes para cambiar al método de asignación dinámico:
    - Haga clic en la configuración de IP cuyo método de asignación de dirección IP desea cambiar de la lista de configuraciones de IP.
    - En la hoja que aparece para la configuración de IP, haga clic en **Dinámica** para el método **Asignación**.
    - Haga clic en **Guardar**.
5. Seleccione la subred a la que desea conectar la NIC en la lista desplegable **Subred**.
6. Haga clic en **Guardar**. Las nuevas direcciones dinámicas se asignan a partir del intervalo de direcciones de la subred nueva. Una vez asignada la NIC a una nueva subred, si lo prefiere, podrá asignar direcciones IP estáticas del nuevo intervalo de direcciones de subred. Para más información sobre la incorporación, el cambio o la eliminación de direcciones IP de una NIC, lea el artículo de [incorporación, cambio o eliminación de direcciones IP](virtual-network-network-interface-addresses.md).

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-nic"></a>Eliminación de una NIC

Puede eliminar una NIC siempre que no esté asociada a una máquina virtual. Si está conectada a una máquina virtual, primero deberá detener (desasignar) la máquina virtual y después desasociar la NIC para poder eliminarla. Para desasociar una NIC de una máquina virtual, complete los pasos de la sección de [desconexión de una NIC de una máquina virtual](virtual-network-network-interface-vm.md#vm-remove-nic) del artículo de [incorporación o eliminación de interfaces de red](virtual-network-network-interface-vm.md). La eliminación de una máquina virtual desasocia todas las NIC, pero no las elimina.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *interfaces de red*. Cuando **interfaces de red** aparezca en los resultados de búsqueda, haga clic en él.
3. Haga clic con el botón derecho en la NIC que desea eliminar y haga clic en **Eliminar**.
4. Haga clic en **Sí** para confirmar la eliminación de la NIC.

Cuando se elimina una NIC, se liberan las direcciones MAC o IP asignadas a ella.

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|[az network nic delete](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.1.0/remove-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>Pasos siguientes
Para crear una máquina virtual con varias NIC o direcciones IP, lea los siguientes artículos:

**Comandos**

|Tarea|Herramienta|
|---|---|
|Creación de una máquina virtual con varias NIC|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
||[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Creación de una máquina virtual con una sola NIC y varias direcciones IP|[CLI](virtual-network-multiple-ip-addresses-cli.md)|
||[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

