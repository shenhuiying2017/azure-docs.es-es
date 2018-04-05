---
title: 'Restricción del acceso a la red a los recursos de PaaS: Azure PowerShell | Microsoft Docs'
description: Aprenda a limitar y restringir el acceso a la red a los recursos de Azure, como Azure Storage y Azure SQL Database, con puntos de conexión de servicio de red virtual mediante PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 7e402af74babda2ce32d4a1597c61d71aba89b9e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-powershell"></a>Restricción del acceso a la red a los recursos de PaaS mediante puntos de conexión de servicio de red virtual mediante PowerShell

Los puntos de conexión de servicio de red virtual permiten limitar el acceso a la red en algunos recursos de servicio de Azure a una subred de red virtual. También puede quitar el acceso a Internet a los recursos. Los puntos de conexión de servicio proporcionan conexión directa a los servicios de Azure compatibles desde la red virtual, de modo que puede usar el espacio de direcciones privadas de la red virtual para acceder a los servicios de Azure. El tráfico destinado a los recursos de Azure a través de puntos de conexión de servicio siempre se mantiene en la red troncal de Microsoft Azure. En este artículo, aprenderá a:

> [!div class="checklist"]
> * Crear una red virtual con una subred
> * Agregar una subred y habilitar un punto de conexión de servicio
> * Crear un recurso de Azure y permitir que tenga acceso a la red solamente desde una subred
> * Implementar una máquina virtual (VM) en cada subred
> * Confirmar el acceso a un recurso desde una subred
> * Confirmar que se ha denegado el acceso a un recurso desde una subred e Internet

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell de forma local, para realizar los pasos de este artículo necesita la versión 5.4.1 del módulo de Azure PowerShell o cualquier versión posterior. Ejecute ` Get-Module -ListAvailable AzureRM` para buscar la versión instalada. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure.

## <a name="create-a-virtual-network"></a>Crear una red virtual

Antes de crear una red virtual, cree un grupo de recursos para ella y los demás recursos que se crearon en este artículo. Cree un grupo de recursos con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En el ejemplo siguiente se crea un grupo de recursos denominado *myResourceGroup*: 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Cree una red virtual con [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). En el ejemplo siguiente se crea una red virtual denominada *myVirtualNetwork* con el prefijo de dirección *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Cree una configuración de subred con [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). En el ejemplo siguiente se crea una configuración de subred para una subred denominada *Public*:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Cree la subred en la red virtual escribiendo la configuración de dicha subred en la red virtual con [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork):

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="enable-a-service-endpoint"></a>Habilitación de un punto de conexión de servicio 

Puede habilitar puntos de conexión de servicio únicamente para los servicios que admiten estos puntos de conexión. Vea los servicios habilitados para puntos de conexión de servicio disponibles en una ubicación de Azure con [AzureRmVirtualNetworkAvailableEndpointService Get](/powershell/module/azurerm.network/get-azurermvirtualnetworkavailableendpointservice). En el ejemplo siguiente se devuelve una lista de servicios habilitados para puntos de conexión de servicio disponibles en la región *eastus*. La lista de servicios devuelta crecerá con el tiempo a medida que más servicios de Azure pasan a estar habilitados para puntos de conexión de servicio.

```azurepowershell-interactive
Get-AzureRmVirtualNetworkAvailableEndpointService -Location eastus | Select Name
``` 

Cree una subred adicional en la red virtual. En este ejemplo, se crea una subred denominada *Private* con un punto de conexión de servicio para *Microsoft.Storage*: 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="restrict-network-access-to-and-from-a-subnet"></a>Restricción del acceso entre una red y una subred

Cree reglas de seguridad de grupo de seguridad de red con [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). La siguiente regla permite el acceso de salida a las direcciones IP públicas asignadas al servicio Azure Storage: 

```azurepowershell-interactive
$rule1 = New-AzureRmNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 `
  -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

La siguiente regla deniega el acceso a todas las direcciones IP públicas. La regla anterior invalida esta regla porque su prioridad es más alta, lo que permite el acceso a las direcciones IP públicas de Azure Storage.

```azurepowershell-interactive
$rule2 = New-AzureRmNetworkSecurityRuleConfig `
  -Name Deny-internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 `
  -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

La siguiente regla permite que el tráfico de Protocolo de escritorio remoto (RDP) pueda entrar en la subred desde cualquier lugar. Se permiten las conexiones de Escritorio remoto a la subred, por lo que puede confirmar acceso a la red a un recurso en un paso posterior.

```azurepowershell-interactive
$rule3 = New-AzureRmNetworkSecurityRuleConfig `
  -Name Allow-RDP-All `
  -Access Allow `
  -DestinationAddressPrefix VirtualNetwork `
  -DestinationPortRange 3389 `
  -Direction Inbound `
  -Priority 120 `
  -Protocol * `
  -SourceAddressPrefix * `
  -SourcePortRange *
```

Cree un grupo de seguridad de red con [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). En el ejemplo siguiente se crea un grupo de seguridad de red denominado *myNsgPrivate*.

```azurepowershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

Asocie el grupo de seguridad de red a la subred *Private* con [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) y luego escriba la configuración de la subred en la red virtual. En el ejemplo siguiente se asocia el grupo de seguridad de red *myNsgPrivate* a la subred *Private*:

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="restrict-network-access-to-a-resource"></a>Restricción del acceso a la red a un recurso

Los pasos que deben seguirse para restringir el acceso a la red a los recursos creados a través de servicios de Azure habilitados para puntos de conexión de servicio varían en función de los servicios. Consulte en la documentación de cada servicio concreto los pasos necesarios para dicho servicio. Como ejemplo, de aquí en adelante se explican los pasos necesarios para restringir el acceso a la red en una cuenta de Azure Storage.

### <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Cree una cuenta de almacenamiento de Azure con [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount). Remplace `<replace-with-your-unique-storage-account-name>` por un nombre que sea único en todas las ubicaciones de Azure, que tenga entre 3 y 24 caracteres de longitud y que esté compuesto exclusivamente de números y letras en minúscula.

```azurepowershell-interactive
$storageAcctName = '<replace-with-your-unique-storage-account-name>'

New-AzureRmStorageAccount `
  -Location EastUS `
  -Name $storageAcctName `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Después de crear la cuenta de almacenamiento, recupere la clave para dicha cuenta en una variable con [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey):

```azurepowershell-interactive
$storageAcctKey = (Get-AzureRmStorageAccountKey `
  -ResourceGroupName myResourceGroup `
  -AccountName $storageAcctName).Value[0]
```

La clave se utiliza para crear un recurso compartido de archivos en un paso posterior. Especifique `$storageAcctKey` y anote el valor, ya que tendrá que escribirlo manualmente más adelante, cuando asigne el recurso compartido de archivos a una unidad de una máquina virtual.

### <a name="create-a-file-share-in-the-storage-account"></a>Creación de un recurso compartido de archivos en la cuenta de almacenamiento

Cree un contexto para la clave y la cuenta de almacenamiento con [New-AzureStorageContext](/powershell/module/azure.storage/new-azurestoragecontext). El contexto encapsula el nombre y la clave de la cuenta de almacenamiento:

```azurepowershell-interactive
$storageContext = New-AzureStorageContext $storageAcctName $storageAcctKey
```

Cree un recurso compartido de archivos con [New-AzureStorageShare](/powershell/module/azure.storage/new-azurestorageshare):

$share = New-AzureStorageShare my-file-share -Context $storageContext

### <a name="deny-all-network-access-to-a-storage-account"></a>Denegación de todo el acceso a la red a una cuenta de almacenamiento

De forma predeterminada, las cuentas de almacenamiento aceptan conexiones de red procedentes de clientes de cualquier red. Para limitar el acceso a redes seleccionadas, cambie la acción predeterminada a *Deny* con [Update-AzureRmStorageAccountNetworkRuleSet](/powershell/module/azurerm.storage/update-azurermstorageaccountnetworkruleset). Una vez que se deniega el acceso a la red, no se puede acceder a la cuenta de almacenamiento desde ninguna red.

```azurepowershell-interactive
Update-AzureRmStorageAccountNetworkRuleSet  `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -DefaultAction Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Habilitación del acceso a la red desde una subred

Recupere la red virtual creada con [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork) y luego recupere el objeto de subred privada en una variable con [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig):

```azurepowershell-interactive
$privateSubnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Name "myVirtualNetwork" `
  | Get-AzureRmVirtualNetworkSubnetConfig `
  -Name "Private"
```

Permita el acceso a la red a la cuenta de almacenamiento desde la subred *Private* con [Add-AzureRmStorageAccountNetworkRule](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig).

```azurepowershell-interactive
Add-AzureRmStorageAccountNetworkRule `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="create-virtual-machines"></a>Creación de máquinas virtuales

Para probar el acceso a la red para una cuenta de almacenamiento, implemente una máquina virtual en cada subred.

### <a name="create-the-first-virtual-machine"></a>Creación de la primera máquina virtual

Cree una máquina virtual en la subred *Pública* con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Cuando ejecute el comando siguiente, se le solicitarán las credenciales. Los valores que especifique se configuran como el nombre de usuario y la contraseña de la máquina virtual. La opción `-AsJob` crea la máquina virtual en segundo plano, así que puede continuar con el siguiente paso.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -Name "myVmPublic" `
    -AsJob
```

Se devuelve una salida similar a la del siguiente ejemplo:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

### <a name="create-the-second-virtual-machine"></a>Creación de la segunda máquina virtual

Cree una máquina virtual en la subred *Private*:

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -Name "myVmPrivate"
```

La operación de creación de la máquina virtual para Azure tarda unos minutos. No continúe con el paso siguiente hasta que Azure termine de crear la máquina virtual y devuelva la salida a PowerShell. 

## <a name="confirm-access-to-storage-account"></a>Confirmación del acceso a la cuenta de almacenamiento

Use [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) para devolver la dirección IP pública de una máquina virtual. En el siguiente ejemplo se devuelve la dirección IP pública de la máquina virtual *myVmPrivate*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Reemplace `<publicIpAddress>` en el siguiente comando, por la dirección IP pública devuelta por el comando anterior y, a continuación, escriba el siguiente comando: 

```powershell
mstsc /v:<publicIpAddress>
```

Se crea un archivo de Protocolo de Escritorio remoto (.rdp) y se descarga en su equipo. Abra el archivo .rdp descargado. Cuando se le pida, seleccione **Conectar**. Escriba el nombre de usuario y la contraseña que especificó al crear la máquina virtual. Puede que deba seleccionar **More choices** (Más opciones) y, luego, **Use a different account** (Usar una cuenta diferente), para especificar las credenciales que escribió al crear la máquina virtual. Seleccione **Aceptar**. Puede recibir una advertencia de certificado durante el proceso de inicio de sesión. Si recibe la advertencia, seleccione **Sí** o **Continuar** para continuar con la conexión.

En la máquina virtual *myVmPrivate*, asigne el recurso compartido de archivos de Azure a la unidad Z con PowerShell. Antes de ejecutar los comandos que siguen, reemplace `<storage-account-key>` y `<storage-account-name>` por valores que proporcionó o recuperó en [Crear una cuenta de almacenamiento](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```
PowerShell devuelve una salida similar a la del ejemplo siguiente:

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
```

El recurso compartido de archivos de Azure se ha asignado correctamente a la unidad Z.

Asegúrese de que la máquina virtual no tiene conectividad de salida con otras direcciones IP públicas:

```powershell
ping bing.com
```

Dado que el grupo de seguridad de red asociado a la subred *Private* no permite el acceso de salida a direcciones IP públicas que no sean las direcciones asignadas al servicio Azure Storage, no recibirá ninguna respuesta.

Cierre la sesión de Escritorio remoto a la máquina virtual *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Confirmación de la denegación del acceso a la cuenta de almacenamiento

Obtenga la dirección IP pública de la máquina virtual *myVmPublic*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPublic `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Reemplace `<publicIpAddress>` en el siguiente comando, por la dirección IP pública devuelta por el comando anterior y, a continuación, escriba el siguiente comando: 

```powershell
mstsc /v:<publicIpAddress>
```

En la máquina virtual *myVmPublic*, intente asignar el recurso compartido de archivos de Azure a la unidad Z. Antes de ejecutar los comandos que siguen, reemplace `<storage-account-key>` y `<storage-account-name>` por valores que proporcionó o recuperó en [Crear una cuenta de almacenamiento](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

El acceso al recurso compartido se deniega y recibe el error `New-PSDrive : Access is denied`. El acceso se deniega porque la máquina virtual *myVmPublic* está implementada en la subred *Public*. La subred *Public* no tiene ningún punto de conexión de servicio habilitado para Azure Storage y la cuenta de almacenamiento solo permite el acceso de red desde la subred *Private*, no desde la subred *Public*.

Cierre la sesión de Escritorio remoto a la máquina virtual *myVmPublic*.

Desde su equipo, intente ver los recursos compartidos de archivos en la cuenta de almacenamiento con el siguiente comando:

```powershell-interactive
Get-AzureStorageFile `
  -ShareName my-file-share `
  -Context $storageContext
```

Se deniega el acceso y se recibe el error *Get-AzureStorageFile : The remote server returned an error: (403) Forbidden. Código de estado HTTP: 403 - mensaje de Error de HTTP: esta solicitud no está autorizada para realizar esta operación* (Get-AzureStorageFile: El servidor remoto devolvió un error: (403) Prohibido. Código de estado HTTP: 403 - Mensaje de error HTTP: Esta solicitud no está autorizada para realizar esta operación), porque el equipo no está en la subred *Private* de la red virtual *MyVirtualNetwork*.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no lo necesite, puede usar [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para quitar el grupo de recursos y todos los recursos que contiene:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha habilitado un punto de conexión de servicio para una subred de red virtual. Ha aprendido que los puntos de conexión de servicio pueden habilitarse para los recursos implementados con varios servicios de Azure. Ha creado una cuenta de Azure Storage y ha limitado el acceso de red en la cuenta de almacenamiento solo a los recursos que se encuentran en una subred de red virtual. Antes de crear puntos de conexión de servicio en las redes virtuales de producción, es recomendable que se familiarice a fondo con los [puntos de conexión de servicio](virtual-network-service-endpoints-overview.md).

Si tiene varias redes virtuales en la cuenta, es posible que desee conectar dos de ellas para que los recursos que están dentro de cada red virtual puedan comunicarse entre sí. Continúe con el siguiente tutorial para aprender a conectar redes virtuales.

> [!div class="nextstepaction"]
> [Conexión de redes virtuales](./tutorial-connect-virtual-networks-powershell.md)
