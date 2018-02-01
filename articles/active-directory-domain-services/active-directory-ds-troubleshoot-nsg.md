---
title: "Azure Active Directory Domain Services: solución de problemas de configuración de grupo de seguridad de red | Microsoft Docs"
description: "Solución de problemas de configuración de NSG para Azure AD Domain Services"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: ergreenl
ms.openlocfilehash: 447f9119ea379e278be77d8699c7dcb751ea3085
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-domain-services---troubleshooting-network-security-group-configuration"></a>Azure AD Domain Services: solución de problemas de configuración de grupo de seguridad de red



## <a name="aadds104-network-error"></a>AADDS104: error de red

**Mensaje de alerta:** *Microsoft no puede tener acceso a los controladores de dominio de este dominio administrado. Esto puede ocurrir si un grupo de seguridad de red (NSG) configurado en la red virtual bloquea el acceso al dominio administrado. Otro motivo posible es que hay una ruta definida por el usuario que bloquea el tráfico entrante desde Internet.*

La causa más común de los errores de red en Azure AD Domain Services se puede atribuir a las configuraciones incorrectas de NSG. Para garantizar que Microsoft puede mantener el dominio administrado, debe usar un grupo de seguridad de red (NSG) para permitir el acceso a [puertos específicos](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services) dentro de la subred. Si estos puertos se encuentran bloqueados, Microsoft no puede acceder a los recursos que necesita, lo que puede resultar perjudicial para el servicio. Al crear el NSG, mantenga abiertos estos puertos para asegurarse de que no haya interrupciones en el servicio.

## <a name="sample-nsg"></a>NSG de ejemplo
En la tabla siguiente se muestra un NSG de ejemplo que mantendría protegido el dominio administrado al permitir que Microsoft supervise, administre y actualice la información.

![NSG de ejemplo](.\media\active-directory-domain-services-alerts\default-nsg.png)

>[!NOTE]
> Azure AD Domain Services requiere acceso saliente sin restricciones. No se recomienda crear ninguna regla de salida adicional en los NSG.

## <a name="adding-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Incorporación de una regla en un grupo de seguridad de red con Azure Portal
Si no desea usar PowerShell, puede agregar reglas únicas de manera manual en grupos de seguridad de red con Azure Portal. Siga estos pasos para crear las reglas en su grupo de seguridad de red.

1. Vaya a la página [Grupos de seguridad de red](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) en Azure Portal.
2. Elija el grupo de seguridad de red asociado al dominio de la tabla.
3. En Configuración del menú de navegación de la izquierda, haga clic en **Reglas de seguridad de entrada** o en **Reglas de seguridad de salida**.
4. Para crear la regla, haga clic en **Agregar** y rellene la información. Haga clic en **OK**.
5. Para comprobar que se creó la regla, ubíquela en la tabla de reglas.


## <a name="create-a-default-nsg-using-powershell"></a>Creación de un NSG predeterminado mediante PowerShell

Puede usar los pasos anteriores para crear mediante PowerShell un NSG nuevo que conserva todos los puertos necesarios para ejecutar Azure AD Domain Services abiertos a la vez que deniega cualquier otro acceso no deseado.


Esta resolución requiere instalar y ejecutar [Azure AD Powershell](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?toc=%2Fazure%2Factive-directory-domain-services%2Ftoc.json&view=azureadps-2.0).

1. Conéctese a su instancia de Azure AD Directory.

  ```PowerShell
  # Connect to your Azure AD directory.
  Connect-AzureAD
  ```
2. Inicie sesión en la suscripción de Azure.

  ```PowerShell
  # Log in to your Azure subscription.
  Login-AzureRmAccount
  ```

3. Cree un NSG con tres reglas. El script siguiente define tres reglas para el NSG que permiten el acceso a los puertos que se necesitan para ejecutar Azure AD Domain Services. Luego, el script crea un NSG nuevo que contiene esas reglas. Puede agregar reglas adicionales con el mismo formato cuando lo considere apropiado.

  ```PowerShell
  # Create the rules needed
  $rule1 = New-AzureRmNetworkSecurityRuleConfig -Name https-rule -Description "Allow HTTP" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 443
  $rule2 = New-AzureRmNetworkSecurityRuleConfig -Name manage-3389 -Description "Manage domain through port 3389" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389
  $rule3 = New-AzureRmNetworkSecurityRuleConfig -Name manage-5986 -Description "Manage domain through port 5986" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
  -SourceAddressPrefix $serviceIPs -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 5986
  # Create the NSG with the 3 rules above
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
  -Name "AADDS-NSG" -SecurityRules $rule1,$rule2,$rule3
  ```

4. Por último, el script asocia el NSG con la red virtual y la subred de su preferencia.

  ```PowerShell
  # Find vnet and subnet
  $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
  # Set the nsg to the subnet and save the changes
  $subnet.NetworkSecurityGroup = $nsg
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```

### <a name="full-script"></a>Script completo

```PowerShell
#Change the following values to match your deployment
$resourceGroup = "ResourceGroupName"
$vnetName = "exampleVnet"
$subnetName = "exampleSubnet"

$serviceIPs = "52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161"

# Create the rules needed
$rule1 = New-AzureRmNetworkSecurityRuleConfig -Name https-rule -Description "Allow HTTP" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 443

$rule2 = New-AzureRmNetworkSecurityRuleConfig -Name manage-3389 -Description "Manage domain through port 3389" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389

$rule3 = New-AzureRmNetworkSecurityRuleConfig -Name manage-5986 -Description "Manage domain through port 5986" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
-SourceAddressPrefix $serviceIPs -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389


# Connect to your Azure AD directory.
Connect-AzureAD

# Log in to your Azure subscription.
Login-AzureRmAccount

# Create the NSG with the 3 rules above
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
-Name "NSG-Default" -SecurityRules $rule1,$rule2,$rule3

# Find vnet and subnet
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName

# Set the nsg to the subnet and save the changes
$subnet.NetworkSecurityGroup = $nsg
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

> [!NOTE]
>Este NSG predeterminado no bloquea el acceso al puerto que se usa para LDAP seguro. Para saber cómo crear una regla para este puerto, consulte [este artículo](active-directory-ds-troubleshoot-ldaps.md).
>

## <a name="contact-us"></a>Ponerse en contacto con nosotros
Póngase en contacto con el equipo de productos de Active Directory Domain Services para [compartir comentarios u obtener asistencia](active-directory-ds-contact-us.md).
