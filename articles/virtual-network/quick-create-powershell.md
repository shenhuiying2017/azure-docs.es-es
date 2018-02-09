---
title: Crear una red virtual en Azure - PowerShell | Microsoft Docs
description: "Obtenga información sobre cómo crear una red virtual rápidamente mediante PowerShell. Una red virtual permite que una gran cantidad de tipos de recursos de Azure se comuniquen entre ellos de forma privada."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: 
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 091e7e6cabf325cdd9d4289e7d22e71c583d91db
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-virtual-network-using-powershell"></a>Creación de una red virtual mediante PowerShell

En este artículo aprenderá a crear una red virtual. Después de crear una red virtual, implementará dos máquinas virtuales en esa red y podrán comunicarse entre ellas de forma privada.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell de forma local, para este tutorial es necesario tener la versión 5.1.1 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute ` Get-Module -ListAvailable AzureRM` para ver cuál es la versión instalada. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos de Azure con [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure. En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*. Todos los recursos de Azure deben crearse dentro de una ubicación (o región) de Azure.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-virtual-network"></a>Crear una red virtual

Cree una red virtual con [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). En el ejemplo siguiente se crea una red virtual predeterminada denominada *myVirtualNetwork* en la ubicación *EastUS*:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/24
```

Todas las redes virtuales tienen uno o más prefijos de dirección asignado. El espacio de direcciones se especifica en la notación CIDR. El espacio de direcciones 10.0.0.0/24 abarca el intervalo de direcciones 10.0.0.0-10.0.0.254. Las redes virtuales tienen cero o más subredes. Los recursos se implementan en una subred que se encuentra en una red virtual. 

Cree una configuración de subred con [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Todas las subredes tienen un prefijo de dirección que se encuentra en el prefijo de dirección de la red virtual. En este ejemplo, se crea una configuración de subred con el mismo prefijo de dirección que el de la red virtual:

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Aunque el prefijo de dirección de la subred abarca el intervalo 10.0.0.0-10.0.0.254, solo están disponibles las direcciones 10.0.0.4-10.0.0.254, ya que Azure reserva las cuatro primeras direcciones (0-3) y la última dirección de cada subred. Puesto que el prefijo de dirección de subred es el mismo que el de la red virtual, solo puede haber una subred en esta red virtual.

Escriba la configuración de subred en la red virtual con [ASet-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), que crea la siguiente subred:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Creación de máquinas virtuales

Una red virtual permite que una gran cantidad de tipos de recursos de Azure se comuniquen entre ellos de forma privada. Un tipo de recurso que se puede implementar en una red virtual es una máquina virtual. Cree dos máquinas virtuales en la red virtual y, más adelante, tendrá la oportunidad de validar y comprender cómo funciona la comunicación entre máquinas virtuales en una red virtual.

Cree una máquina virtual con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Cuando se realiza este paso, se le solicitará las credenciales. Los valores que especifique se configuran como el nombre de usuario y la contraseña de la máquina virtual. La ubicación en la que se crea la máquina virtual debe ser la misma ubicación en la que está la red virtual. De todas formas, no es necesario que la máquina virtual esté en el mismo grupo de recursos que la red virtual, aunque lo esté en este artículo. El parámetro `-AsJob` permite que el comando se ejecute en segundo plano para que pueda continuar con la siguiente tarea.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

Se devuelve un resultado similar al siguiente resultado de ejemplo, y Azure comienza a crear la máquina virtual en segundo plano.

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

El protocolo DHCP de Azure asigna de forma automática la dirección 10.0.0.4 a la máquina virtual durante la creación, ya que esta es la primera dirección que está disponible en la subred *default*.

Cree una segunda máquina virtual. 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```
La creación de la máquina virtual tarda algunos minutos. Una vez creada, Azure devuelve el resultado acerca de la máquina virtual que se acaba de crear. Aunque no esté en el resultado devuelto, Azure asigna la dirección *10.0.0.5* a la máquina virtual *myVm2*, ya que es la siguiente dirección disponible en la subred.

## <a name="connect-to-a-virtual-machine"></a>Conexión a una máquina virtual

Use el comando [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) para devolver la dirección IP pública de una máquina virtual. De forma predeterminada, Azure asigna una dirección IP pública y enrutable mediante Internet a cada máquina virtual. La dirección IP pública se asigna a la máquina virtual desde un [grupo de direcciones asignadas a cada región de Azure](https://www.microsoft.com/download/details.aspx?id=41653). Aunque Azure sabe qué dirección IP pública tiene asignada una máquina virtual, el sistema operativo que se ejecuta en una máquina virtual no sabe si tiene asignada alguna dirección IP pública. En el siguiente ejemplo se devuelve la dirección IP pública de la máquina virtual *myVm1*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -Name myVm1 -ResourceGroupName myResourceGroup | Select IpAddress
```

Ejecute el comando siguiente en el equipo local para crear una sesión de Escritorio remoto con la máquina virtual *myVm1*. Reemplace `<publicIpAddress>` con la dirección IP que devolvió el comando anterior.

```
mstsc /v:<publicIpAddress>
```

A continuación se crea, se descarga y se abre en el equipo un archivo de Protocolo de Escritorio remoto (.rdp). Escriba el nombre de usuario y la contraseña que especificó al crear la máquina virtual y, a continuación, haga clic en **Aceptar**. Puede recibir una advertencia de certificado durante el proceso de inicio de sesión. Haga clic en **Sí** o **Conectar** para continuar con la conexión.

## <a name="validate-communication"></a>Validar la comunicación

No se puede hacer ping en una máquina virtual de Windows porque el Firewall de Windows no lo permite de forma predeterminada. Para permitir el ping en *myVm1*, escriba el siguiente comando desde el símbolo del sistema:

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Para validar la comunicación con *myVm2*, escriba el siguiente comando desde el símbolo del sistema en la máquina virtual *myVm1*. Proporcione las credenciales que usó cuando creó la máquina virtual y, a continuación, complete la conexión:

```
mstsc /v:myVm2
```

La conexión al Escritorio remoto se completará correctamente porque ambas máquinas virtuales tienen direcciones IP privadas asignadas desde la subred *default* y porque el Escritorio remoto está abierto a través del Firewall de Windows de forma predeterminada. Puede conectarse a *myVm2* según el nombre de host porque Azure proporciona automáticamente la resolución de nombres DNS de todos los hosts de una red virtual. Desde el símbolo del sistema, haga ping en *myVm1* desde *myVm2*.

```
ping myvm1
```

El ping se realizará correctamente porque en un paso anterior permitió que atravesara el Firewall de Windows en la máquina virtual *myVm1*. Utilice el siguiente comando para confirmar la comunicación saliente a Internet:

```
ping bing.com
```

Recibirá cuatro respuestas de bing.com. De forma predeterminada, una máquina virtual que se encuentre en una red virtual puede realizar comunicaciones salientes a Internet.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no lo necesite, puede usar el comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para quitar el grupo de recursos y todos los recursos que contenga. Salga de la sesión de Eascritorio remoto y, a continuación, ejecute en el equipo el siguiente comando para eliminar el grupo de recursos:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>pasos siguientes

En este artículo implementó una red virtual predeterminada con una subred y dos máquinas virtuales. Para saber cómo crear una red virtual personalizada que tenga varias subredes y que realice tareas básicas de administración de redes virtuales, consulte el tutorial que indica cómo crear y administrar una red virtual personalizada.


> [!div class="nextstepaction"]
> [Crear y administrar una red virtual personalizada](virtual-networks-create-vnet-arm-pportal.md#powershell)
