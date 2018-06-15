---
title: 'Guía de inicio rápido: Diagnóstico de problemas al filtrar el tráfico de las máquinas virtuales con Azure PowerShell | Microsoft Docs'
description: En esta guía de inicio rápido, aprenderá a diagnosticar los problemas que surgen al filtrar el tráfico de las máquinas virtuales utilizando la funcionalidad Comprobación del flujo de IP de Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose a virtual machine (VM) network traffic filter problem that prevents communication to and from a VM.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: d98a804961defc80bebe3e3a838dd229c23044bc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32180460"
---
# <a name="quickstart-diagnose-a-virtual-machine-network-traffic-filter-problem---azure-powershell"></a>Guía de inicio rápido: Diagnóstico de problemas al filtrar el tráfico de las máquinas virtuales con Azure PowerShell

En esta guía de inicio rápido, va a implementar una máquina virtual y a comprobar las comunicaciones entre una dirección IP y una dirección URL u otra dirección IP. Además, va a determinar la causa de un error de comunicación y cómo puede resolverlo.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell de forma local, para esta guía necesitará la versión 5.4.1 del módulo de AzureRM PowerShell, o cualquier versión posterior. Ejecute ` Get-Module -ListAvailable AzureRM` para ver cuál es la versión instalada. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure.

## <a name="create-a-vm"></a>Crear una VM

Para poder crear una máquina virtual, debe crear un grupo de recursos que contenga la máquina virtual. Cree un grupo de recursos con [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Cree la máquina virtual con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Cuando se realiza este paso, se le solicitará las credenciales. Los valores que especifique se configuran como el nombre de usuario y la contraseña de la máquina virtual.

```azurepowershell-interactive
$vM = New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

La máquina virtual tarda en crearse unos minutos. No continúe con los pasos restantes hasta que la máquina virtual se haya creado y PowerShell devuelva la salida.

## <a name="test-network-communication"></a>Prueba de la comunicación de red

Para probar la comunicación de red con Network Watcher, primero debe habilitar un monitor de red en la región en la que quiere probar la máquina virtual y, a continuación, usar la funcionalidad Comprobación del flujo de IP de Network Watcher para probar la comunicación.

### <a name="enable-network-watcher"></a>Habilitación de Network Watcher

Si ya tiene un monitor de red habilitado en la región Este de EE. UU., use [Get-AzureRmNetworkWatcher](/powershell/module/azurerm.network/get-azurermnetworkwatcher) para recuperarlo. En el ejemplo siguiente, se recupera un monitor de red existente llamado *NetworkWatcher_eastus* que se encuentra en el grupo de recursos *NetworkWatcherRG*:

```azurepowershell-interactive
$networkWatcher = Get-AzureRmNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Si aún no tiene un monitor de red habilitado en la región Este de EE. UU., use [New-AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher) para crearlo:

```azurepowershell-interactive
$networkWatcher = New-AzureRmNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-ip-flow-verify"></a>Uso de la funcionalidad Comprobación del flujo de IP

Cuando se crea una máquina virtual, Azure permite y deniega el tráfico de entrada y salida de la máquina virtual conforme a unos valores predeterminados. Si lo desea, puede invalidar después los valores predeterminados de Azure para permitir o denegar otros tipos de tráfico. Para comprobar si se permite o deniega el tráfico dirigido a distintos destinos o el tráfico procedente de una dirección IP, utilice el comando [Test-AzureRmNetworkWatcherIPFlow](/powershell/module/azurerm.network/test-azurermnetworkwatcheripflow).

Pruebe la comunicación de salida entre la máquina virtual y una de las direcciones IP de www.bing.com:

```azurepowershell-interactive
Test-AzureRmNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Outbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 60000 `
  -RemoteIPAddress 13.107.21.200 `
  -RemotePort 80
```

Después de unos segundos, el resultado devuelto le informa de que una regla de seguridad llamada **AllowInternetOutbound** ha permitido el acceso.

Pruebe la comunicación de salida entre la máquina virtual y 172.31.0.100:

```azurepowershell-interactive
Test-AzureRmNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Outbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 60000 `
  -RemoteIPAddress 172.31.0.100 `
  -RemotePort 80
```

El resultado devuelto le informa de que una regla de seguridad llamada **DefaultOutboundDenyAll** ha denegado el acceso.

Pruebe la comunicación de entrada que llega a la máquina virtual desde 172.31.0.100:

```azurepowershell-interactive
Test-AzureRmNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Inbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 80 `
  -RemoteIPAddress 172.31.0.100 `
  -RemotePort 60000
```

El resultado devuelto le informa de que una regla de seguridad llamada **DefaultInboundDenyAll** ha denegado el acceso. Ahora que sabe qué reglas de seguridad están permitiendo o denegando el tráfico de entrada y salida en una máquina virtual, puede determinar cómo deben resolverse los problemas.

## <a name="view-details-of-a-security-rule"></a>Ver detalles de una regla de seguridad

Para determinar por qué las reglas de [Prueba de comunicación de red](#test-network-communication) permiten o deniegan la comunicación, revise las reglas de seguridad vigentes en la interfaz de red con [Get-AzureRmEffectiveNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermeffectivenetworksecuritygroup):

```azurepowershell-interactive
Get-AzureRmEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup
```

La salida devuelta contiene el siguiente texto de la regla **AllowInternetOutbound**, que permite el acceso de salida a www.bing.com en [Uso de la funcionalidad Comprobación del flujo de IP](#use-ip-flow-verify):

```powershell
{
  "Name":
"defaultSecurityRules/AllowInternetOutBound",
  "Protocol": "All",
  "SourcePortRange": [
    "0-65535"
  ],
  "DestinationPortRange": [
    "0-65535"
  ],
  "SourceAddressPrefix": [
    "0.0.0.0/0"
  ],
  "DestinationAddressPrefix": [
    "Internet"
  ],
  "ExpandedSourceAddressPrefix": [],
  "ExpandedDestinationAddressPrefix": [
    "1.0.0.0/8",
    "2.0.0.0/7",
    "4.0.0.0/6",
    "8.0.0.0/7",
    "11.0.0.0/8",
    "12.0.0.0/6",
    ...
    ],
    "Access": "Allow",
    "Priority": 65001,
    "Direction": "Outbound"
  },
```

Puede ver en la salida que **DestinationAddressPrefix** es **Internet**. Sin embargo, no queda claro el modo en que 13.107.21.200, la dirección que probó en [Uso de la funcionalidad Comprobación del flujo de IP](#use-ip-flow-verify), está relacionada con **Internet**. Verá que aparecen varios prefijos de direcciones bajo **ExpandedDestinationAddressPrefix**. Uno de los prefijos de la lista es **12.0.0.0/6**, que comprende el intervalo de direcciones IP de 12.0.0.1 a 15.255.255.254. Como 13.107.21.200 está dentro de ese intervalo, la regla **AllowInternetOutBound** permite el tráfico de salida. Asimismo, no aparece ninguna regla con una **prioridad**  mayor (con un número más bajo) en la salida devuelta por `Get-AzureRmEffectiveNetworkSecurityGroup` que invalide esta regla. Para denegar la comunicación de salida con 13.107.21.200, podría agregar una regla de seguridad con una prioridad mayor que denegara la salida del puerto 80 hacia la dirección IP.

Cuando ejecutó el comando `Test-AzureRmNetworkWatcherIPFlow` para probar la comunicación de salida hacia 172.131.0.100 en [Uso de la funcionalidad Comprobación del flujo de IP](#use-ip-flow-verify), la salida le informó de que la regla **DefaultOutboundDenyAll** había denegado la comunicación. La regla **DefaultOutboundDenyAll** es igual que la regla **DenyAllOutBound** que aparece en la siguiente salida del comando `Get-AzureRmEffectiveNetworkSecurityGroup`:

```powershell
{
"Name": "defaultSecurityRules/DenyAllOutBound",
"Protocol": "All",
"SourcePortRange": [
  "0-65535"
],
"DestinationPortRange": [
  "0-65535"
],
"SourceAddressPrefix": [
  "0.0.0.0/0"
],
"DestinationAddressPrefix": [
  "0.0.0.0/0"
],
"ExpandedSourceAddressPrefix": [],
"ExpandedDestinationAddressPrefix": [],
"Access": "Deny",
"Priority": 65500,
"Direction": "Outbound"
}
```

La regla muestra **0.0.0.0/0** como **DestinationAddressPrefix**. La regla deniega la comunicación de salida hacia 172.131.0.100 porque la dirección no está incluida en el valor **DestinationAddressPrefix** de ninguna otra regla de salida en los resultados del comando `Get-AzureRmEffectiveNetworkSecurityGroup`. Para permitir la comunicación de salida, podría agregar una regla de seguridad con una prioridad mayor que permitiera el tráfico hacia el puerto 80 en 172.131.0.100.

Cuando ejecutó el comando `Test-AzureRmNetworkWatcherIPFlow` para probar la comunicación de entrada procedente de 172.131.0.100 en [Uso de la funcionalidad Comprobación del flujo de IP](#use-ip-flow-verify), la salida le informó de que la regla **DefaultInboundDenyAll** había denegado la comunicación. La regla **DefaultInboundDenyAll** es igual que la regla **DenyAllInBound** que aparece en la siguiente salida del comando `Get-AzureRmEffectiveNetworkSecurityGroup`:

```powershell
{
"Name": "defaultSecurityRules/DenyAllInBound",
"Protocol": "All",
"SourcePortRange": [
  "0-65535"
],
"DestinationPortRange": [
  "0-65535"
],
"SourceAddressPrefix": [
  "0.0.0.0/0"
],
"DestinationAddressPrefix": [
  "0.0.0.0/0"
],
"ExpandedSourceAddressPrefix": [],
"ExpandedDestinationAddressPrefix": [],
"Access": "Deny",
"Priority": 65500,
"Direction": "Inbound"
},
```

La regla **DenyAllInBound** se aplica porque, tal y como puede verse en la salida, no existe ninguna otra regla con una prioridad mayor en la salida del comando `Get-AzureRmEffectiveNetworkSecurityGroup` que permita la entrada en el puerto 80 del tráfico dirigido a la máquina virtual procedente de 172.131.0.100. Para permitir la comunicación de entrada, podría agregar una regla de seguridad con una prioridad mayor que permitiera el tráfico de entrada en el puerto 80 del tráfico procedente de 172.131.0.100.

Las pruebas de esta guía de inicio rápido permiten comprobar la configuración de Azure. Si estas pruebas devuelven resultados esperados pero sigue teniendo problemas de red, asegúrese de que no hay un firewall entre la máquina virtual y el punto de conexión con el que se está comunicando y que el sistema operativo de la máquina virtual no tiene un firewall que permita o deniegue la comunicación.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no lo necesite, puede usar [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para quitar el grupo de recursos y todos los recursos que contiene:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha creado una máquina virtual y diagnosticado los filtros del tráfico de entrada y de salida. Ha aprendido que las reglas de grupo de seguridad de red permiten o deniegan el tráfico de entrada y salida de una máquina virtual. Más información acerca de las [reglas de seguridad](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) y [cómo crearlas](../virtual-network/manage-network-security-group.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-security-rule).

Incluso con la aplicación de los filtros de tráfico adecuados, la comunicación con una máquina virtual puede seguir experimentando errores debido a la configuración del enrutamiento. Para más información acerca de cómo diagnosticar los problemas de enrutamiento de red de la máquina virtual, consulte [Diagnose VM routing problems](diagnose-vm-network-routing-problem-powershell.md) (Diagnóstico de problemas de enrutamiento de máquina virtual). Para diagnosticar problemas con los filtros de tráfico, la latencia o el enrutamiento de salida mediante una herramienta, consulte [Solución de problemas de conexiones](network-watcher-connectivity-powershell.md).