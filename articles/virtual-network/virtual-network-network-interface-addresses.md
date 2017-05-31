---
title: "Configuración de direcciones IP para las interfaces de red de Azure | Microsoft Docs"
description: "Aprenda a agregar direcciones IP públicas y privadas a las NIC, así como a cambiarlas y eliminarlas."
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
ms.openlocfilehash: cf7dbc648136897772bbd068687313eec16bed75
ms.contentlocale: es-es
ms.lasthandoff: 05/12/2017


---

# <a name="add-change-or-remove-ip-addresses-for-azure-network-interfaces"></a>Incorporación, cambio o eliminación de direcciones IP para interfaces de red de Azure

Aprenda a agregar, cambiar y eliminar direcciones IP públicas y privadas para interfaces de red (NIC). Las direcciones IP privadas asignadas a una NIC permiten a una máquina virtual comunicarse con Internet y con otros recursos conectados a una red virtual de Azure. Las direcciones IP públicas asignadas a una NIC permiten la comunicación entrante en una máquina virtual desde Internet. 

Si necesita crear, cambiar o eliminar NIC, lea el artículo de [configuraciones y tareas de NIC](virtual-network-network-interface.md). Si necesita agregar NIC a máquinas virtuales o eliminarlas, lea el artículo de [incorporación o eliminación de NIC](virtual-network-network-interface-vm.md). 


## <a name="before"></a>Antes de empezar

Complete las tareas siguientes antes de seguir los pasos de las secciones de este artículo:

- Revise el artículo sobre los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para información sobre los límites de las direcciones IP públicas y privadas.
- Inicie sesión con una cuenta de Azure en Azure Portal, la interfaz de la línea de comandos (CLI) de Azure o Azure PowerShell. Si todavía no tiene una cuenta de Azure, regístrese para obtener una [cuenta de evaluación gratuita](https://azure.microsoft.com/free).
- Si va a usar comandos de PowerShell para realizar las tareas de este artículo, instale y configure Azure PowerShell mediante los pasos del artículo de [instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de los commandlets de Azure PowerShell. Para obtener ayuda con los comandos de PowerShell, con ejemplos, escriba `get-help <command> -full`.
- Si va a usar comandos de la interfaz de la línea de comandos (CLI) de Azure para realizar las tareas de este artículo, instale y configure la CLI de Azure mediante los pasos del artículo de [instalación y configuración de la CLI de Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de la CLI de Azure. Para obtener ayuda con los comandos de la CLI, escriba `az <command> --help`.

## <a name="about"></a>Acerca de las NIC y las direcciones IP

A las NIC se les pueden asignar varias direcciones IP públicas y privadas, dentro de los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Asignar varias direcciones IP a una NIC resulta útil en escenarios como:
- Hospede varios sitios web o servicios con direcciones IP y certificados SSL diferentes en un único servidor.
- Una máquina virtual que actúa como una aplicación virtual de red, por ejemplo, un firewall o un equilibrador de carga.
- La capacidad de agregar cualquier dirección IP privada para cualquiera de las NIC a un grupo de servidores de back-end de Azure Load Balancer. En el pasado, solo la dirección IP principal para la NIC principal podía agregarse a un grupo de back-end. Para más información sobre cómo equilibrar la carga de varias configuraciones de IP, lea el artículo [Load balancing multiple IP configurations](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Equilibrio de carga de varias configuraciones de IP).

Las direcciones IP se asignan a la configuración de IP. A las NIC siempre se les asigna una configuración de IP **principal**, pero también pueden tener varias configuraciones **secundarias** asignadas. A cada configuración de IP se le asigna uno de los siguientes tipos de direcciones (o a ambos):
- **Privada:** las direcciones IP privadas permiten a las máquinas virtuales comunicarse con otros recursos conectados a la red virtual de la NIC. Cada configuración de IP debe tener asignada una dirección IP privada. Los servidores DHCP de Azure asignan la dirección IP privada para la configuración de IP principal de la NIC a la NIC dentro del sistema operativo de la máquina virtual. Las direcciones IP privadas también permiten a las máquinas virtuales la comunicación de salida a Internet. Las conexiones de salida son las direcciones de red de origen traducidas (SNAT). Para más información acerca de la conectividad de salida a Internet de Azure, consulte el artículo de [Comprender las conexiones salientes en Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). La comunicación entrante a una dirección IP privada de máquina virtual desde Internet no está permitida.
- **Pública:** las direcciones IP públicas permiten la conexión entrante a una máquina virtual desde Internet. Las conexiones salientes a Internet no tienen SNAT. Puede asignar una dirección IP pública a una configuración de IP, pero no es obligatorio. Para más información sobre las direcciones IP públicas, lea el artículo sobre las [direcciones IP publicas](virtual-network-public-ip-address.md).

El número de direcciones IP públicas y privadas que se pueden asignar a una NIC es limitado. Para más información, lea el artículo acerca de los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

Las direcciones IP públicas y privadas se pueden asignar con los métodos de asignación siguientes:
- **Dinámico:** de manera predeterminada se asignan direcciones IP públicas y privadas dinámicas. Las direcciones dinámicas pueden cambiar si la máquina virtual se detiene (desasigna) y después se reinicia. Si no quiere que la dirección IP cambie durante la vida de la máquina virtual, use una dirección estática.
- **Estático:** las direcciones estáticas no cambian hasta que se elimina la máquina virtual. Las direcciones IP privadas estáticas se asignan desde el espacio de direcciones para la subred conectada a la NIC. Para más información sobre la asignación de direcciones IP públicas estáticas de Azure, lea el artículo sobre las [direcciones IP publicas](virtual-network-public-ip-address.md).

## <a name="create-ip-config"></a>Incorporación de direcciones IP

Puede agregar a una NIC tantas direcciones IP como sea necesario, dentro de los límites indicados en el artículo sobre los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *interfaces de red*. Cuando **interfaces de red** aparezca en los resultados de búsqueda, haga clic en él.
3. En la hoja **Interfaces de red** que aparece, haga clic en la NIC a la cual desee agregar una dirección IP.
4. En la sección **CONFIGURACIÓN** de la hoja de la NIC seleccionada, haga clic en **Configuraciones de IP**.
5. Haga clic en **+Agregar** en la hoja que se abre para las configuraciones de IP.
6. Especifique lo siguiente y haga clic en **Aceptar** para cerrar la hoja **Agregar configuración de IP**:

    |Configuración|¿Necesario?|Detalles|
    |---|---|---|
    |Nombre|Sí|Debe ser único para la NIC|
    |Escriba|Sí|Puesto que va a agregar una configuración de IP a una NIC existente y cada NIC deben tener una configuración de IP principal, la única opción es **Secundaria**.|
    |Método de asignación de direcciones IP privadas|Sí|Una dirección **Dinámica** puede cambiar si la máquina virtual se reinicia después de estar en estado detenido (desasignado). Azure asigna una dirección disponible del espacio de direcciones de la subred conectada a la NIC. Una dirección **Estática** no se libera hasta que se elimina la NIC. Especifique una dirección IP del intervalo de direcciones de subred que no se esté usando actualmente en otra configuración de IP.|
    |Dirección IP pública|No|**Deshabilitado:** ningún recurso de dirección IP pública está asociado actualmente a la configuración de IP. **Habilitada:** seleccione una dirección IP pública existente o cree una nueva. Para más información sobre cómo crear una dirección IP pública, lea el artículo [Direcciones IP públicas](virtual-network-public-ip-address.md#create).|
7. Agregue manualmente las direcciones IP privadas secundarias al sistema operativo de la máquina virtual siguiendo las instrucciones del artículo [Asignación de varias direcciones IP a máquinas virtuales](virtual-network-multiple-ip-addresses-portal.md#os-config). No agregue direcciones IP públicas al sistema operativo de la máquina virtual.

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|[az network nic ip-config create](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/add-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-ip-config"></a>Cambio de configuración de las direcciones IP

Puede que necesite cambiar el método de asignación de una dirección IP, la dirección IP estática o la dirección IP pública asignada a una NIC. Si va a modificar la dirección IP privada de una configuración IP secundaria asociada a una NIC secundaria de una máquina virtual (más información acerca de las [NIC principales y secundarias](virtual-network-network-interface-vm.md#about)), detenga (desasigne) la máquina virtual antes de completar los pasos siguientes: 

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *interfaces de red*. Cuando **interfaces de red** aparezca en los resultados de búsqueda, haga clic en él.
3. En la hoja **Interfaces de red** que aparece, haga clic en la NIC que desea ver o cuya configuración de las direcciones IP desea cambiar.
4. En la sección **CONFIGURACIÓN** de la hoja de la NIC seleccionada, haga clic en **Configuraciones de IP**.
5. Haga clic en la configuración de IP que desea modificar, en la lista de la hoja que se abre para las configuraciones de IP.
6. Cambie la configuración según la información que se proporciona en el paso 6 de la sección sobre la [Incorporación de direcciones IP](#create-ip-config) de este artículo. Haga clic en **Guardar** para cerrar la hoja de la configuración de IP que ha modificado.

>[!NOTE]
>Si la NIC principal tiene varias configuraciones de IP y cambia la dirección IP privada de la configuración de IP principal, debe reasignar manualmente todas las direcciones IP secundarias a la NIC en Windows (no es necesario para Linux). Para asignar manualmente las direcciones IP a una NIC en un sistema operativo, lea el artículo [Asignación de varias direcciones IP a máquinas virtuales](virtual-network-multiple-ip-addresses-portal.md#os-config). No agregue direcciones IP públicas al sistema operativo de la máquina virtual.

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="delete-ip-config"></a>Eliminación de direcciones IP

Puede eliminar de una NIC direcciones IP públicas y privadas, pero una NIC siempre debe tener al menos una dirección IP privada asignada.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *interfaces de red*. Cuando **interfaces de red** aparezca en los resultados de búsqueda, haga clic en él.
3. En la hoja **Interfaces de red** que aparece, haga clic en la NIC de la cual desee eliminar direcciones IP.
4. En la sección **CONFIGURACIÓN** de la hoja de la NIC seleccionada, haga clic en **Configuraciones de IP**.
5. Haga clic con el botón derecho en una configuración de IP secundaria (no se puede eliminar la principal) que desee eliminar, haga clic en **Eliminar** y en **Sí** para confirmar la eliminación. Si la configuración tenía asociado un recurso de dirección IP pública, el recurso se desasocia de la configuración de IP, pero no se elimina.
6. Cierre la hoja **Configuraciones de IP**.

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|[az network nic ip-config delete](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/remove-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>Pasos siguientes
Para crear una máquina virtual con varias NIC o direcciones IP, lea los siguientes artículos:

**Comandos**

|Tarea|Herramienta|
|---|---|
|Creación de una máquina virtual con varias NIC|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
||[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Creación de una máquina virtual con una sola NIC y varias direcciones IP|[CLI](virtual-network-multiple-ip-addresses-cli.md)|
||[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

