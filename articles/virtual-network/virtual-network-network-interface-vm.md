---
title: "Incorporación de interfaces de red a máquinas virtuales de Azure o eliminación de estas | Microsoft Docs"
description: "Aprenda a agregar interfaces de red a máquinas virtuales o a eliminarlas de ellas."
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
ms.date: 07/25/2017
ms.author: jdial
ms.openlocfilehash: 57f95b765b1b116814683a6643db16091c3041f6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="add-network-interfaces-to-or-remove-from-virtual-machines"></a>Incorporación de interfaces de red a máquinas virtuales o eliminación de estas

Aprenda a agregar una interfaz de red existente al crear una máquina virtual o a agregar interfaces de red a una máquina virtual existente detenida (desasignada) o a quitarlas de esta. Una interfaz de red permite que una máquina virtual de Azure se comunique con Internet, Azure y los recursos locales. Una máquina virtual puede tener una o varias interfaces de red. 

Si tiene que agregar, cambiar o quitar direcciones IP para una interfaz de red, consulte el artículo para [administrar las direcciones IP de interfaces de red](virtual-network-network-interface-addresses.md). Si necesita crear, cambiar o eliminar interfaces de red, lea el artículo [Manage network interfaces (Administración de interfaces de red)](virtual-network-network-interface.md).

## <a name="before"></a>Antes de empezar

Antes de llevar a cabo ningún paso de las secciones de este artículo, realice las siguientes tareas:

- Para aprender cuántas interfaces de red admite cada tamaño de máquina virtual Linux y Windows, vea los artículos sobre el tamaño de las máquinas virtuales [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Inicie sesión con una cuenta de Azure en [Azure Portal](https://portal.azure.com), la interfaz de la línea de comandos (CLI) de Azure o Azure PowerShell. Si todavía no tiene una cuenta de Azure, regístrese para obtener una [cuenta de evaluación gratuita](https://azure.microsoft.com/free).
- Si usa comandos de PowerShell para completar las tareas de este artículo, [instale y configure Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de los commandlets de Azure PowerShell. Para obtener ayuda con los comandos de PowerShell, con ejemplos, escriba `get-help <command> -full`.
- Si usa comandos de la interfaz de la línea de comandos (CLI) de Azure para completar las tareas de este artículo, [instale y configure la CLI de Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de la CLI de Azure. Para obtener ayuda con los comandos de la CLI, escriba `az <command> --help`. En lugar de instalar la CLI y sus requisitos previos, puede usar Azure Cloud Shell. Azure Cloud Shell es un shell de Bash gratuito que se puede ejecutar directamente en Azure Portal. Tiene la CLI de Azure preinstalada y configurada para utilizar con su cuenta. Para usar Cloud Shell, haga clic en el botón Cloud Shell **>_** que se encuentra en la parte superior del [portal](https://portal.azure.com).

## <a name="about"></a>Sobre interfaces de red y máquinas virtuales

Puede agregar (asociar) una interfaz de red existente a una máquina virtual al crear esta, siempre que la interfaz de red no esté conectada a otra máquina virtual. Puede agregar una interfaz de red a una máquina virtual existente o quitarla (desasociarla) de ella, siempre que la máquina virtual esté detenida (desasignada). Si crea una máquina virtual desde Azure Portal, este crea automáticamente una interfaz de red con la configuración predeterminada. El portal no permite:

- Especificar una interfaz de red existente para agregarla al crear la máquina virtual
- Creación de una máquina virtual con varias interfaces de red
- Especificar un nombre para la interfaz de red (el portal crea la interfaz de red con un nombre predeterminado)

Puede usar Azure PowerShell o la CLI para crear una máquina virtual o una interfaz de red con todos los atributos anteriores para los que no se puede usar el portal. Antes de completar las tareas de las secciones siguientes, tenga en cuenta las restricciones y los comportamientos siguientes:

- Todos los tamaños de máquina virtual admiten dos interfaces de red como mínimo, aunque algunos admiten más. Anteriormente, algunos tamaños de máquina virtual solo admitían una interfaz de red. Para aprender cuántas interfaces de red admite cada tamaño de máquina virtual, vea los artículos sobre el tamaño de las máquinas virtuales [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
- Anteriormente, solo se podían agregar interfaces de red a máquinas virtuales que admitían varias interfaces de red y que se habían creado con al menos dos. No se podía agregar una interfaz de red a una máquina virtual que se hubiera creado con una interfaz de red, aunque el tamaño de máquina virtual admitiera varias interfaces de red. Y a la inversa, solo se podían quitar interfaces de red de una máquina virtual que tuviera al menos tres, ya que las máquinas virtuales creadas con al menos dos interfaces de red siempre debían tener al menos dos. Ninguna de estas restricciones se aplican ya. Ahora puede crear una máquina virtual con cualquier cantidad de interfaces de red (hasta el máximo que admita su tamaño) y agregar o quitar las que quiera (de las máquinas virtuales detenidas [desasignadas]), siempre que la máquina virtual conserve al menos una interfaz de red.
- De forma predeterminada, la primera interfaz de red de una máquina virtual se define como la interfaz de red *principal*. Todas las demás interfaces de red de la máquina virtual son interfaces de red *secundarias*.
- Los servidores DHCP de Azure asignan una puerta de enlace predeterminada a las interfaces de red principales, pero no a las secundarias. Puesto que las interfaces de red secundarias no tienen asignada una puerta de enlace predeterminada, no se pueden comunicar con recursos ajenos a su subred de forma predeterminada. Para habilitar la comunicación de las interfaces de red secundarias de una máquina virtual Windows con recursos ajenos a su subred, agregue rutas al sistema operativo con el comando `route add` de una línea de comandos de Windows. En cuanto a las máquinas virtuales Linux, puesto que el comportamiento predeterminado es usar el enrutamiento de host no seguro, se recomienda limitar el tráfico de las interfaces de red secundarias a una sola subred. Si necesita conectividad fuera de la subred para las interfaces de red secundarias, habilite el enrutamiento basado en directivas para asegurarse de que el tráfico de entrada y salida use la misma interfaz de red.
- De forma predeterminada, todo el tráfico saliente de la máquina virtual se envía a la dirección IP asignada a la configuración de IP principal de la interfaz de red principal. El usuario controla qué dirección IP se usa para el tráfico saliente en el sistema operativo de la máquina virtual, pero, de manera predeterminada, sale de la interfaz de red principal.
- En el pasado, era un requisito que todas las máquinas virtuales situadas en el mismo conjunto de disponibilidad debían tener una o varias interfaces de red. Ahora puede haber máquinas virtuales con cualquier cantidad de interfaces de red en el mismo conjunto de disponibilidad, hasta el máximo que permita su tamaño. Por otra parte, solo se pueden agregar máquinas virtuales a un conjunto de disponibilidad al crearlas. Para más información acerca de los conjuntos de disponibilidad, lea el artículo de [administración de la disponibilidad de las máquinas virtuales en Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Aunque las interfaces de red de la misma máquina virtual se pueden conectar a subredes diferentes dentro de una red virtual, todas las interfaces de red deben estar conectadas a la misma red virtual.
- Puede agregar cualquier dirección IP para cualquier configuración de IP de cualquier interfaz de red principal o secundaria a un grupo de servidores back-end de Azure Load Balancer. En el pasado, solo la dirección IP principal de la interfaz de red principal podía agregarse a un grupo de servidores back-end. Para más información sobre las direcciones IP y las configuraciones, lea el artículo de [incorporación, cambio o eliminación de direcciones IP](virtual-network-network-interface-addresses.md).
- La eliminación de una máquina virtual no elimina las interfaces de red asociadas a ella. Cuando se elimina una máquina virtual, las interfaces de red se desasocian de la máquina virtual. Puede agregar las interfaces de red a diferentes máquinas virtuales o eliminarlas.
- Si una interfaz de red tiene una dirección IPv6 privada asignada, se puede conectar a una máquina virtual en el momento que se cree. Una vez que se haya creado la máquina virtual, no se podrá conectar a una interfaz de red con una dirección IPv6 asignada. Si se conecta una interfaz de red con una dirección IPv6 privada asignada al crear una máquina virtual, solo se podrá conectar esa interfaz de red a la máquina virtual, independientemente de cuántas interfaces de red admita el tamaño de la máquina virtual. Consulte el artículo sobre [direcciones IP de interfaz de red](virtual-network-network-interface-addresses.md) para obtener más información sobre la asignación de direcciones IP a interfaces de red.

## <a name="vm-create"></a>Incorporación de interfaces de red existentes a una nueva máquina virtual

Al crear una máquina virtual a través del portal, este crea una interfaz de red con la configuración predeterminada y la asocia a la máquina virtual automáticamente. No se pueden agregar interfaces de red existentes a una máquina virtual nueva ni crear una máquina virtual con varias interfaces de red mediante Azure Portal. Para ello, puede usar la CLI o PowerShell. Al crear una máquina virtual, le puede agregar todas las interfaces de red que su tamaño admita. Para más información sobre cuántas interfaces de red admite cada tamaño de máquina virtual, lea los artículos sobre el tamaño de las máquinas virtuales [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Actualmente, las interfaces de red que se agregan a una máquina virtual no se pueden asociar a otra. Para más información sobre la creación de interfaces de red, lea el artículo [Manage network interfaces (Administración de interfaces de red)](virtual-network-network-interface.md#create-a-network-interface).

> [!WARNING]
> Si la interfaz de red tiene una dirección IPv6 privada asignada, solo se podrá agregar esa interfaz de red a la máquina virtual cuando esta se cree. No se puede agregar más de una interfaz de red a la máquina virtual en el momento de crearla ni después, siempre y cuando la dirección IPv6 esté asignada a una interfaz de red conectada a una máquina virtual. Consulte el artículo sobre [direcciones IP de interfaz de red](virtual-network-network-interface-addresses.md) para obtener más información sobre la asignación de direcciones IP a interfaces de red.

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Incorporación de una interfaz de red existente a una máquina virtual existente

Se pueden agregar tantas interfaces de red a una máquina virtual como su tamaño admita. Para aprender cuántas interfaces de red admite cada tamaño de máquina virtual, vea los artículos sobre el tamaño de las máquinas virtuales [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). La máquina virtual a la que quiere agregar una interfaz de red debe admitir varias interfaces de red y estar detenida (desasignada). Actualmente, las interfaces de red que quiere agregar a una máquina virtual no se pueden asociar a otra. No se pueden agregar interfaces de red a una máquina virtual existente mediante Azure Portal. Para agregar interfaces de red a una máquina virtual, debe usar la CLI o PowerShell. 

> [!WARNING]
> Si una interfaz de red tiene una dirección IPv6 privada asignada, no se podrá agregar a una máquina virtual. Solo se puede agregar una interfaz de red con una dirección IPv6 privada asignada a una máquina virtual cuando esta se cree. Consulte el artículo sobre [direcciones IP de interfaz de red](virtual-network-network-interface-addresses.md) para obtener más información sobre la asignación de direcciones IP a interfaces de red.

|Herramienta|Comando|
|---|---|
|CLI|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add) (referencia) o [pasos detallados](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referencia) o [pasos detallados](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="vm-view-nic"></a> Visualización de interfaces de red para una máquina virtual

Puede ver las interfaces de red asociadas actualmente a una máquina virtual para conocer su configuración y sus direcciones IP asignadas. 

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga asignados los roles de propietario, colaborador o colaborador de red para la suscripción. Para obtener más información sobre la asignación de roles en las cuentas, consulte [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *máquinas virtuales*. Cuando **máquinas virtuales** aparezca en los resultados de búsqueda, haga clic en él.
3. En la hoja **Máquinas virtuales** que aparece, haga clic en el nombre de la máquina virtual cuyas interfaces de red quiere ver.
4. En la hoja sección **CONFIGURACIÓN** de la hoja de la máquina virtual seleccionada que aparece, haga clic en **Interfaces de red**. Para más información sobre la configuración de la interfaz de red y sobre cómo cambiarla, lea el artículo [Manage network interfaces (Administración de interfaces de red)](virtual-network-network-interface.md). Para obtener más información sobre la incorporación, el cambio o la eliminación de direcciones IP asignadas a una interfaz de red, lea el artículo [Add, change, or remove IP addresses](virtual-network-network-interface-addresses.md) (Adición, cambio o eliminación de direcciones IP).

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-remove-nic"></a> Eliminación de interfaces de red de una máquina virtual

La máquina virtual de la que quiere quitar (o desasociar) una interfaz de red debe estar detenida (desasignada) y tener al menos dos interfaces de red asociadas. Puede quitar cualquier interfaz de red siempre y cuando la máquina virtual tenga en todo momento una asociada. Si quita una interfaz de red principal, Azure asigna el atributo primary a la interfaz de red que haya estado más tiempo asociada a la máquina virtual. 

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga asignados los roles de propietario, colaborador o colaborador de red para la suscripción. Para obtener más información sobre la asignación de roles en las cuentas, consulte [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *máquinas virtuales*. Cuando **máquinas virtuales** aparezca en los resultados de búsqueda, haga clic en él.
3. En la hoja **Máquinas virtuales** que aparece, haga clic en el nombre de la VM cuyas interfaces de red quiere quitar.
4. En la hoja sección **CONFIGURACIÓN** de la hoja de la máquina virtual seleccionada que aparece, haga clic en **Interfaces de red**. Para más información sobre la configuración de la interfaz de red y sobre cómo cambiarla, lea el artículo [Manage network interfaces (Administración de interfaces de red)](virtual-network-network-interface.md). Para obtener más información sobre la incorporación, el cambio o la eliminación de direcciones IP asignadas a una interfaz de red, lea el artículo [Add, change, or remove IP addresses](virtual-network-network-interface-addresses.md) (Adición, cambio o eliminación de direcciones IP).
5. En la hoja Interfaces de red que aparece, haga clic en **...**, que se encuentra en la interfaz de red que quiere desasociar.
6. Haga clic en **Desasociar**. Si solo hay una interfaz de red conectada a la máquina virtual, la opción **Desasociar** no estará disponible. Haga clic en **Sí** en el cuadro de confirmación que aparece.

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#remove) (referencia) o [pasos detallados](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzureRMVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referencia) o [pasos detallados](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="next-steps"></a>Pasos siguientes
Para crear una máquina virtual con varias interfaces de red o direcciones IP, lea los siguientes artículos:

**Comandos**

|Tarea|Herramienta|
|---|---|
|Creación de una máquina virtual con varias NIC|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Creación de una máquina virtual con una sola interfaz de red y varias direcciones IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Creación de una máquina virtual con una sola interfaz de red y una dirección IPv6 privada (detrás de Azure Load Balancer)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Plantilla de Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
