---
title: "Incorporación de interfaces de red a máquinas virtuales de Azure o eliminación de estas | Microsoft Docs"
description: "Aprenda a agregar interfaces de red (NIC) a las máquinas virtuales o a eliminarlas de estas."
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
ms.openlocfilehash: 0d609b20040572e3fb371a277603109d64a0ba37
ms.contentlocale: es-es
ms.lasthandoff: 05/12/2017


---

# <a name="add-network-interfaces-to-or-remove-from-virtual-machines"></a>Incorporación de interfaces de red a máquinas virtuales o eliminación de estas

Aprenda a agregar una interfaz de red (NIC) existente al crear una máquina virtual, o a agregarla a una máquina virtual existente detenida (desasignada) o eliminarla de esta. Una NIC permite que una máquina virtual de Azure se comunique con Internet, Azure y los recursos locales. Una máquina virtual puede tener una o varias NIC. 

Si tiene que agregar, cambiar o eliminar las direcciones IP de una NIC, lea el artículo sobre la [incorporación, el cambio o la eliminación de direcciones IP](virtual-network-network-interface-addresses.md). Si necesita crear, cambiar o eliminar NIC, lea el artículo de [configuraciones y tareas de NIC](virtual-network-network-interface.md).

## <a name="before"></a>Antes de empezar

Complete las tareas siguientes antes de seguir los pasos de las secciones de este artículo:

- Para aprender cuántas NIC admite cada tamaño de máquina virtual de Linux y Windows, consulte los artículos sobre el tamaño de las máquinas virtuales [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Inicie sesión con una cuenta de Azure en Azure Portal, la interfaz de la línea de comandos (CLI) de Azure o Azure PowerShell. Si todavía no tiene una cuenta de Azure, regístrese para obtener una [cuenta de evaluación gratuita](https://azure.microsoft.com/free).
- Si va a usar comandos de PowerShell para realizar las tareas de este artículo, instale y configure Azure PowerShell mediante los pasos del artículo de [instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de los commandlets de Azure PowerShell. Para obtener ayuda con los comandos de PowerShell, con ejemplos, escriba `get-help <command> -full`.
- Si va a usar comandos de la interfaz de la línea de comandos (CLI) de Azure para realizar las tareas de este artículo, instale y configure la CLI de Azure mediante los pasos del artículo de [instalación y configuración de la CLI de Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de la CLI de Azure. Para ayuda con los comandos de la CLI, escriba `az <command> --help`.

## <a name="about"></a>Acerca de las NIC y las máquinas virtuales

Al crear una máquina virtual, puede agregarle (asociarle) una NIC existente, siempre que la NIC no esté conectada a otra máquina virtual. Puede agregar una NIC a una máquina virtual existente o eliminarla (desasociarla) de esta, siempre que la máquina virtual esté detenida (desasignada). Al crear una máquina virtual desde Azure Portal, este crea una NIC con la configuración predeterminada. El portal no permite:

- Especificar una NIC existente para agregarla al crear la máquina virtual
- Creación de una máquina virtual con varias NIC
- Especificar un nombre para la NIC (el portal crea la NIC con un nombre predeterminado).

Puede usar Azure PowerShell o la CLI para crear una máquina virtual o la NIC con todos los atributos anteriores para los que no se puede usar el portal. Antes de completar las tareas de las secciones siguientes, tenga en cuenta las restricciones y los comportamientos siguientes:

- Todos los tamaños de máquina virtual admiten dos NIC como mínimo, algunos, incluso más. Anteriormente, algunos tamaños de máquina virtual solo admitían una NIC. Para aprender cuántas NIC admite cada tamaño de máquina virtual, consulte los artículos sobre el tamaño de las máquinas virtuales [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
- Anteriormente, solo se podían agregar NIC a VM que admitían varias NIC y que se habían creado con al menos dos NIC. No podía agregar una NIC a una VM creada con una NIC, incluso si el tamaño de la VM admitía varias NIC. Por el contrario, solo se podían eliminar NIC de una máquina virtual que tuviera al menos tres, ya que las máquinas virtuales creadas con al menos dos NIC siempre debían tener al menos dos NIC. Ninguna de estas restricciones se aplican ya. Ahora puede crear una máquina virtual con cualquier cantidad de NIC (hasta el máximo que admita su tamaño) y agregar o eliminar las que desee (de las máquinas virtuales detenidas [desasignadas]), siempre que la máquina virtual conserve al menos una NIC.
- De forma predeterminada, la primera NIC de la máquina virtual se define como *principal*. Todas las demás son *secundarias*.
- Los servidores DHCP de Azure asignan una puerta de enlace predeterminada a las NIC principales, pero no a las secundarias. Puesto que las NIC secundarias no se tienen asignada una puerta de enlace predeterminada, de manera predeterminada no se pueden comunicar con recursos ajenos a su subred. Para habilitar la comunicación de las NIC secundarias de una máquina virtual Windows con recursos ajenos a su subred, agregue rutas al sistema operativo con el comando `route add` de la línea de comandos de Windows. En cuanto a las máquinas virtuales Linux, puesto que el comportamiento predeterminado es usar el enrutamiento del host no seguro, se recomienda limitar el tráfico de las NIC secundarias a una sola subred. Si necesita conectividad fuera de la subred para las NIC secundarias, debe habilitar el enrutamiento según las directivas para asegurarse de que el tráfico de entrada y salida utiliza la misma NIC.
- De forma predeterminada, todo el tráfico saliente de la máquina virtual se envía la dirección IP asignada a la configuración de IP principal de la NIC principal. Usted controla qué dirección IP se usa para el tráfico saliente en el sistema operativo de la máquina virtual, pero, de manera predeterminada, sale de la NIC principal.
- En el pasado, era un requisito que todas las máquinas virtuales situadas en el mismo conjunto de disponibilidad debían tener una o varias NIC. Ahora puede haber máquinas virtuales con cualquier cantidad de NIC en el mismo conjunto de disponibilidad, hasta el máximo que permita su tamaño. Por otra parte, solo se pueden agregar máquinas virtuales a un conjunto de disponibilidad al crearlas. Para más información acerca de los conjuntos de disponibilidad, lea el artículo de [administración de la disponibilidad de las máquinas virtuales en Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Aunque las NIC de la misma máquina virtual se pueden conectar a subredes diferentes dentro de una red virtual, todas las NIC deben estar conectadas a la misma red virtual.
- Puede agregar cualquier dirección IP de la configuración de IP de cualquier NIC principal o secundaria a un grupo de back-end de Azure Load Balancer. En el pasado, solo la dirección IP principal para la NIC principal podía agregarse a un grupo de back-end. Para más información sobre las direcciones IP y las configuraciones, lea el artículo de [incorporación, cambio o eliminación de direcciones IP](virtual-network-network-interface-addresses.md).
- La eliminación de una máquina virtual no elimina las NIC asociadas a ella. Cuando se elimina una máquina virtual, las NIC se desasocian de la máquina virtual. Puede agregar las NIC a diferentes máquinas virtuales o eliminarlas.

## <a name="vm-create"></a>Incorporación de NIC existentes a una máquina virtual nueva
Al crear una máquina virtual a través del portal, este crea una NIC con la configuración predeterminada y la asocia a la máquina virtual. No se pueden agregar NIC existentes a una máquina virtual nueva ni crear una máquina virtual con varias NIC mediante Azure Portal. Para ello, puede usar la CLI o PowerShell. Al crear una máquina virtual, le puede agregar todas las NIC que su tamaño admita. Para obtener más información sobre cuántas NIC admite cada tamaño de máquina virtual, consulte los artículos sobre el tamaño de las máquinas virtuales [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Actualmente, las NIC que se agregan a una máquina virtual no se pueden asociar a otra. Para más información sobre la creación de NIC, consulte el artículo de [configuraciones y tareas de NIC](virtual-network-network-interface.md#create-nic).

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v2.5.0/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Incorporación de NIC existentes a una máquina virtual existente

A una máquina virtual le puede agregar todas las NIC que su tamaño permita. Para aprender cuántas NIC admite cada tamaño de máquina virtual, consulte los artículos sobre el tamaño de las máquinas virtuales [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). La máquina virtual a la que desee agregar una NIC debe admitir varias y estar detenida (desasignada). Actualmente, las NIC que se agregan a una máquina virtual no se pueden asociar a otra. No se pueden agregar NIC a una máquina virtual existente mediante Azure Portal. Para agregar NIC a una máquina virtual existente, debe utilizar la CLI o PowerShell.

|Herramienta|Comando|
|---|---|
|CLI|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add)|
|PowerShell|[Add-AzureRmVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-view-nic"></a>Visualización de las NIC de una máquina virtual

Puede ver las NIC asociadas actualmente a una máquina virtual para conocer la configuración de las NIC y sus direcciones IP asignadas. 

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga asignados los roles de propietario, colaborador o colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *máquinas virtuales*. Cuando **máquinas virtuales** aparezca en los resultados de búsqueda, haga clic en él.
3. En la hoja **Máquinas virtuales** que aparece, haga clic en el nombre de la máquina virtual de la cual desea ver las NIC.
4. En la hoja sección **CONFIGURACIÓN** de la hoja de la máquina virtual seleccionada que aparece, haga clic en **Interfaces de red**. Para más información sobre la configuración de NIC y cómo cambiarla, lea el artículo de [configuraciones y tareas de NIC](virtual-network-network-interface.md). Para más información sobre la incorporación, el cambio y la eliminación de direcciones IP asignadas una NIC, lea el artículo de [incorporación, cambio o eliminación de direcciones IP](virtual-network-network-interface-addresses.md).

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v1.3.4/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-remove-nic"></a> Eliminación de NIC de una máquina virtual

La máquina virtual de la cual desee eliminar una NIC debe estar detenida (desasignada) y tener al menos dos NIC asociadas. Puede eliminar cualquier NIC, siempre y cuando la máquina virtual tenga siempre una NIC asociada. Si elimina una NIC principal, Azure asigna el atributo primary a la NIC que haya estado más tiempo asociada a la máquina virtual. Puede elegir como principal cualquier NIC. No se pueden eliminar NIC de una máquina virtual ni establecer el atributo primary de una NIC mediante Azure Portal, aunque puede realizar ambas operaciones con la CLI o PowerShell. 

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#remove)|
|PowerShell|[Remove-AzureRMVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>Pasos siguientes
Para crear una máquina virtual con varias NIC o direcciones IP, lea los siguientes artículos:

**Comandos**

|Tarea|Herramienta|
|---|---|
|Creación de una máquina virtual con varias NIC|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
||[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Creación de una máquina virtual con una sola NIC y varias direcciones IP|[CLI](virtual-network-multiple-ip-addresses-cli.md)|
||[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

