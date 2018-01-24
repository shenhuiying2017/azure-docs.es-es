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
ms.date: 12/15/2017
ms.author: jdial
ms.openlocfilehash: abe6abb942d206330e809f3aef388b846d7d7c7f
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Incorporación de interfaces de red a máquinas virtuales o su eliminación de ellas

Aprenda a agregar una interfaz de red existente al crear una máquina virtual o a agregar interfaces de red a una máquina virtual existente detenida (desasignada) o a quitarlas de esta. Una interfaz de red permite que una máquina virtual de Azure se comunique con Internet, Azure y los recursos locales. Una máquina virtual puede tener una o varias interfaces de red. 

Si tiene que agregar, cambiar o quitar direcciones IP para una interfaz de red, consulte el artículo para [administrar las direcciones IP de interfaces de red](virtual-network-network-interface-addresses.md). Si necesita crear, cambiar o eliminar interfaces de red, lea el artículo [Manage network interfaces (Administración de interfaces de red)](virtual-network-network-interface.md).

## <a name="before"></a>Antes de empezar

Antes de llevar a cabo ningún paso de las secciones de este artículo, realice las siguientes tareas:

- Inicie sesión con una cuenta de Azure en [Azure Portal](https://portal.azure.com), la interfaz de la línea de comandos (CLI) de Azure o Azure PowerShell. Si todavía no tiene una cuenta de Azure, regístrese para obtener una [cuenta de evaluación gratuita](https://azure.microsoft.com/free).
- Si usa comandos de PowerShell para completar las tareas de este artículo, [instale y configure Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de los commandlets de Azure PowerShell. Para obtener ayuda con los comandos de PowerShell, con ejemplos, escriba `get-help <command> -full`. En lugar de instalar Azure PowerShell, puede usar Azure Cloud Shell. Azure Cloud Shell es una instancia de PowerShell gratuita que se ejecuta directamente en Azure Portal. Tiene Azure PowerShell preinstalado y configurad para usarlo con su cuenta. Para usar Cloud Shell, haga clic en el botón Cloud Shell **>_** en la parte superior del [portal](https://portal.azure.com) y seleccione PowerShell en la esquina superior izquierda cuando aparezca la ventana de Shell.
- Si usa comandos de la interfaz de la línea de comandos (CLI) de Azure para completar las tareas de este artículo, [instale y configure la CLI de Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de la CLI de Azure. Para obtener ayuda con los comandos de la CLI, escriba `az <command> --help`. En lugar de instalar la CLI y sus requisitos previos, puede usar Azure Cloud Shell. Azure Cloud Shell es un shell de Bash gratuito que se puede ejecutar directamente en Azure Portal. Tiene la CLI de Azure preinstalada y configurada para utilizar con su cuenta. Para usar Cloud Shell, haga clic en el botón Cloud Shell **>_** en la parte superior del [portal](https://portal.azure.com) y seleccione Bash en la esquina superior izquierda cuando aparezca la ventana de Shell.

## <a name="vm-create"></a>Incorporación de interfaces de red existentes a una nueva máquina virtual

Al crear una máquina virtual a través del portal, este crea una interfaz de red con la configuración predeterminada y la asocia a la máquina virtual automáticamente. No se pueden agregar interfaces de red existentes a una máquina virtual nueva ni crear una máquina virtual con varias interfaces de red mediante Azure Portal. Para ello, puede usar la CLI o PowerShell. Sin embargo, antes de usar PowerShell o la CLI para crear una máquina virtual con una interfaz de red existente, debe familiarizarse con las [restricciones](#constraints). Si crea una máquina virtual con varias interfaces de red, también debe configurar el sistema operativo para usarlas correctamente una vez que se crea la máquina virtual. Para detalles, consulte la configuración de [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) o [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) para varias interfaces de red.

**Comandos** Antes de crear la máquina virtual, cree una interfaz de red con los pasos que aparecen en [Crear una interfaz de red](virtual-network-network-interface.md#create-a-network-interface).

|Herramienta|Get-Help|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Incorporación de una interfaz de red a una máquina virtual existente

1. Inicie sesión en el Portal de Azure.
2. En el cuadro de búsqueda que se encuentra en la parte superior del portal, busque el nombre de la máquina virtual a la que desea agregar la interfaz de red o busque la máquina virtual con un clic en **Todos los servicios** y, a continuación, **Máquinas virtuales**. Cuando encuentre la máquina virtual, haga clic en ella. La máquina virtual a la que desea agregar una interfaz de red debe admitir todas las interfaces de red que desea agregar. Para aprender cuántas interfaces de red admite cada tamaño de máquina virtual, vea los artículos sobre el tamaño de las máquinas virtuales [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  
3. Haga clic en **Introducción**, en **CONFIGURACIÓN**. Haga clic en **Detener** y espere hasta que el **estado** de la máquina virtual cambie a *Detenida (desasignada)*. 
4. Haga clic en **Redes**, en **CONFIGURACIÓN**.
5. Haga clic en **Attach network interface** (Asociar interfaz de red). En la lista de interfaces de red existentes que actualmente no están asociadas a otra máquina virtual, haga clic en la interfaz de red que desea asociar. La interfaz de red que selecciona no puede tener habilitadas las redes aceleradas, no puede tener asignada una dirección IPv6 y debe existir en la misma red virtual que la red virtual en que está la interfaz de red actualmente asociada a la máquina virtual. Si no tiene una interfaz de red existente, debe crear una. Para hacerlo, haga clic en **Crear interfaz de red**. Para más información sobre cómo crear una interfaz de red, consulte [Crear una interfaz de red](virtual-network-network-interface.md#create-a-network-interface). Para más información sobre las restricciones adicionales cuando se agregan interfaces de red a las máquinas virtuales, consulte [Restricciones](#constraints).
6. Haga clic en **OK**.
7. Haga clic en **Introducción**, en **CONFIGURACIÓN**. Haga clic en **Iniciar** para iniciar la máquina virtual.
8. Configure el sistema operativo de máquina virtual para usar correctamente varias interfaces de red. Para detalles, consulte la configuración de [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) o [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) para varias interfaces de red.

|Herramienta|Get-Help|
|---|---|
|CLI|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add) (referencia) o [pasos detallados](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referencia) o [pasos detallados](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="vm-view-nic"></a> Visualización de interfaces de red para una máquina virtual

Puede ver las interfaces de red asociadas actualmente a una máquina virtual para conocer su configuración y sus direcciones IP asignadas. 

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga asignados los roles de propietario, colaborador o colaborador de red para la suscripción. Para obtener más información sobre la asignación de roles en las cuentas, consulte [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *máquinas virtuales*. Cuando **máquinas virtuales** aparezca en los resultados de búsqueda, haga clic en él.
3. Haga clic en el nombre de la máquina virtual cuyas interfaces de red desea ver.
4. En la sección **CONFIGURACIÓN** de la máquina virtual que seleccionó, haga clic en **Redes**. Para información sobre la configuración de la interfaz de red y cómo modificarla, consulte [Administración de interfaces de red](virtual-network-network-interface.md). Para obtener más información sobre la incorporación, el cambio o la eliminación de direcciones IP asignadas a una interfaz de red, lea el artículo [Add, change, or remove IP addresses](virtual-network-network-interface-addresses.md) (Adición, cambio o eliminación de direcciones IP).

**Comandos**

|Herramienta|Get-Help|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-remove-nic"></a> Eliminación de interfaces de red de una máquina virtual

1. Inicie sesión en el Portal de Azure.
2. En el cuadro de búsqueda que se encuentra en la parte superior del portal, busque el nombre de la máquina virtual de la que desea quitar (desasociar) la interfaz de red o busque la máquina virtual con un clic en **Todos los servicios** y, a continuación, **Máquinas virtuales**. Cuando encuentre la máquina virtual, haga clic en ella.
3. Haga clic en **Introducción**, en **CONFIGURACIÓN**. Haga clic en **Detener** y espere hasta que el **estado** de la máquina virtual cambie a *Detenida (desasignada)*. 
4. Haga clic en **Redes**, en **CONFIGURACIÓN**.
5. Haga clic en **Desasociar interfaz de red**. En la lista de interfaces de red actualmente asociadas a la máquina virtual, haga clic en la interfaz de red que desea desasociar. Si solo se muestra una interfaz de red, no puede desasociarla porque una máquina virtual siempre debe tener al menos una interfaz de red asociada.
6. Haga clic en **OK**.

**Comandos**

|Herramienta|Get-Help|
|---|---|
|CLI|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#remove) (referencia) o [pasos detallados](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzureRMVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referencia) o [pasos detallados](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="next-steps"></a>Pasos siguientes
Para crear una máquina virtual con varias interfaces de red o direcciones IP, lea los siguientes artículos:

**Comandos**

|Task|Herramienta|
|---|---|
|Creación de una máquina virtual con varias NIC|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Creación de una máquina virtual con una sola interfaz de red y varias direcciones IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Creación de una máquina virtual con una sola interfaz de red y una dirección IPv6 privada (detrás de Azure Load Balancer)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Plantilla de Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="constraints"></a>Restricciones

- Una VM debe tener al menos una interfaz de red asociada.
- Una VM solo puede tener tantas interfaces de red asociadas como admita el tamaño de la VM. Para más información sobre cuántas interfaces de red admite cada tamaño de máquina virtual, consulte los artículos sobre los tamaños de VM [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Todos los tamaños admiten al menos dos interfaces de red.
- Actualmente, las interfaces de red que se agregan a una máquina virtual no se pueden asociar a otra. Para más información sobre cómo crear interfaces de red, consulte [Crear una interfaz de red](virtual-network-network-interface.md#create-a-network-interface).
- Anteriormente, solo se podían agregar interfaces de red a máquinas virtuales que admitían varias interfaces de red y que se habían creado con al menos dos. No se podía agregar una interfaz de red a una máquina virtual que se hubiera creado con una interfaz de red, aunque el tamaño de máquina virtual admitiera varias interfaces de red. Y a la inversa, solo se podían quitar interfaces de red de una máquina virtual que tuviera al menos tres, ya que las máquinas virtuales creadas con al menos dos interfaces de red siempre debían tener al menos dos. Ninguna de estas restricciones se aplican ya. Ahora puede crear una máquina virtual con cualquier cantidad de interfaces de red (hasta el máximo que permita el tamaño de la máquina virtual).
- De manera predeterminada, la primera interfaz de red asociada a una máquina virtual se define como la interfaz de red *principal*. Todas las demás interfaces de red de la máquina virtual son interfaces de red *secundarias*.
- Si bien puede controlar a qué interfaz de red envía el tráfico saliente, de manera predeterminada, todo el tráfico saliente de la máquina virtual se envía a la dirección IP asignada a la configuración de IP principal de la interfaz de red principal.
- En el pasado, era un requisito que todas las máquinas virtuales situadas en el mismo conjunto de disponibilidad debían tener una o varias interfaces de red. Ahora puede haber máquinas virtuales con cualquier cantidad de interfaces de red en el mismo conjunto de disponibilidad, hasta el máximo que permita su tamaño. Por otra parte, solo se pueden agregar máquinas virtuales a un conjunto de disponibilidad al crearlas. Para más información acerca de los conjuntos de disponibilidad, lea el artículo de [administración de la disponibilidad de las máquinas virtuales en Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Aunque las interfaces de red de la misma máquina virtual se pueden conectar a subredes diferentes dentro de una red virtual, todas las interfaces de red deben estar conectadas a la misma red virtual.
- Puede agregar cualquier dirección IP para cualquier configuración de IP de cualquier interfaz de red principal o secundaria a un grupo de servidores back-end de Azure Load Balancer. En el pasado, solo la dirección IP principal de la interfaz de red principal podía agregarse a un grupo de servidores back-end. Para más información sobre las direcciones IP y las configuraciones, lea el artículo de [incorporación, cambio o eliminación de direcciones IP](virtual-network-network-interface-addresses.md).
- La eliminación de una máquina virtual no elimina las interfaces de red asociadas a ella. Cuando se elimina una máquina virtual, las interfaces de red se desasocian de la máquina virtual. Puede agregar las interfaces de red a diferentes máquinas virtuales o eliminarlas.
- Si una interfaz de red tiene una dirección IPv6 privada asignada, debe agregarla (asociarla) a una máquina virtual en el momento que se cree. Una vez que se haya agregado la máquina virtual, no se podrá conectar a una interfaz de red con una dirección IPv6 asignada. Si se agrega una interfaz de red con una dirección IPv6 privada asignada al crear una máquina virtual, solo se podrá agregar esa interfaz de red a la máquina virtual, independientemente de cuántas interfaces de red admita el tamaño de la máquina virtual. Consulte el artículo sobre [direcciones IP de interfaz de red](virtual-network-network-interface-addresses.md) para obtener más información sobre la asignación de direcciones IP a interfaces de red.
- De manera similar a IPv6, no puede asociar una interfaz de red con redes aceleradas habilitadas a una máquina virtual una vez que esta se crea. Además, para aprovechar las redes aceleradas, también debe completar pasos dentro del sistema operativo de la máquina virtual. Para más información sobre las redes aceleradas y otras restricciones cuando la use, consulte los artículos sobre las redes aceleradas para las máquinas virtuales [Windows](create-vm-accelerated-networking-powershell.md) o [Linux](create-vm-accelerated-networking-cli.md).
