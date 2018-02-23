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
ms.date: 02/12/2018
ms.author: ergreenl
ms.openlocfilehash: cc9a61314de7e10afe370c3b1307d03544a379d5
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/13/2018
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>Solución de problemas de configuración de red no válida para el dominio administrado
Este artículo le ayuda a solucionar problemas y resolver errores de configuración relacionados con la red que generan el mensaje de alerta siguiente:

## <a name="alert-aadds104-network-error"></a>Alerta AADDS104: error de red
**Mensaje de alerta:** *Microsoft no puede tener acceso a los controladores de dominio de este dominio administrado. Esto puede ocurrir si un grupo de seguridad de red (NSG) configurado en la red virtual bloquea el acceso al dominio administrado. Otro motivo posible es que hay una ruta definida por el usuario que bloquea el tráfico entrante desde Internet.*

Las configuraciones de NSG no válidas son la causa más común de los errores de red en Azure AD Domain Services. El grupo de seguridad de red (NSG) configurado para la red virtual debe permitir el acceso a [puertos específicos](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services). Si estos puertos están bloqueados, Microsoft no puede supervisar ni actualizar el dominio administrado. Además, se afecta la sincronización entre el directorio de Azure AD y el dominio administrado. Al crear el NSG, mantenga abiertos estos puertos para evitar la interrupción del servicio.


## <a name="sample-nsg"></a>NSG de ejemplo
En la tabla siguiente se muestra un NSG de ejemplo que mantendría protegido el dominio administrado al permitir que Microsoft supervise, administre y actualice la información.

![NSG de ejemplo](.\media\active-directory-domain-services-alerts\default-nsg.png)

>[!NOTE]
> Azure AD Domain Services requiere acceso saliente sin restricciones desde la red virtual. No se recomienda crear ninguna regla de NSG adicional que restrinja el acceso saliente para la red virtual.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Incorporación de una regla en un grupo de seguridad de red con Azure Portal
Si no desea usar PowerShell, puede agregar reglas únicas de manera manual en grupos de seguridad de red con Azure Portal. Para crear reglas en el grupo de seguridad de red, complete estos pasos:

1. Vaya a la página [Grupos de seguridad de red](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) en Azure Portal.
2. En la tabla, elija el NSG asociado con la subred en que está habilitado el dominio administrado.
3. En **Configuración** del panel de la izquierda, haga clic en **Reglas de seguridad de entrada** o en **Reglas de seguridad de salida**.
4. Para crear la regla, haga clic en **Agregar** y rellene la información. Haga clic en **OK**.
5. Para comprobar que se creó la regla, ubíquela en la tabla de reglas.


## <a name="create-an-nsg-for-azure-ad-domain-services-using-powershell"></a>Creación de un grupo de seguridad de red para Azure AD Domain Services mediante PowerShell
Este NSG está configurado para permitir el tráfico entrante a los puertos que requiere Azure AD Domain Services, a la vez que se deniega cualquier otro acceso entrante no deseado.

**Requisito previo: instalación y configuración de Azure PowerShell** Siga las instrucciones para [instalar el módulo de Azure PowerShell y conéctese a la suscripción de Azure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

>[!TIP]
> Se recomienda usar la versión más reciente del módulo de Azure PowerShell. Si ya tiene instalada una versión anterior del módulo de Azure PowerShell, actualice a la versión más reciente.
>

Use estos pasos para crear un grupo de seguridad de red nuevo mediante PowerShell. 
1. Inicie sesión en la suscripción de Azure.

  ```PowerShell
  # Log in to your Azure subscription.
  Login-AzureRmAccount
  ```

2. Cree un NSG con tres reglas. El script siguiente define tres reglas para el NSG que permiten el acceso a los puertos que se necesitan para ejecutar Azure AD Domain Services. Luego, el script crea un NSG nuevo que contiene esas reglas. Use el mismo formato para agregar reglas adicionales que permitan otro tráfico entrante, si es necesario para las cargas de trabajo implementadas en la red virtual.

  ```PowerShell
  # Allow inbound HTTPS traffic to enable synchronization to your managed domain.
  $SyncRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowSyncWithAzureAD `
  -Description "Allow synchronization with Azure AD" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 443

  # Allow management of your domain over port 5986 (PowerShell Remoting)
  $PSRemotingRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowPSRemoting `
  -Description "Allow management of domain through port 5986" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
  -SourceAddressPrefix 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, `
  13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, `
  52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, `
  52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161 `
  -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 5986

  # Allow management of your domain over port 3389 (remote desktop).
  $RemoteDesktopRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowRD `
  -Description "Allow management of domain through port 3389" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389

  # Create the NSG with the 3 rules above
  $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroup -Location $Location `
  -Name "AAD-DomainServices-NSG" -SecurityRules $SyncRule,$PSRemotingRule,$RemoteDesktopRule
  ```

3. Por último, asocie el grupo de seguridad de red con la red virtual y la subred de su preferencia.

  ```PowerShell
  # Find vnet and subnet
  $Vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroup -Name $VnetName
  $Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $Vnet -Name $SubnetName

  # Set the nsg to the subnet and save the changes
  $Subnet.NetworkSecurityGroup = $Nsg
  Set-AzureRmVirtualNetwork -VirtualNetwork $Vnet
  ```

## <a name="full-script-to-create-and-apply-an-nsg-for-azure-ad-domain-services"></a>Script completo para crear y aplicar un grupo de seguridad de red para Azure AD Domain Services
>[!TIP]
> Se recomienda usar la versión más reciente del módulo de Azure PowerShell. Si ya tiene instalada una versión anterior del módulo de Azure PowerShell, actualice a la versión más reciente.
>

```PowerShell
# Change the following values to match your deployment
$ResourceGroup = "ResourceGroupName"
$Location = "westus"
$VnetName = "exampleVnet"
$SubnetName = "exampleSubnet"

# Log in to your Azure subscription.
Login-AzureRmAccount

# Allow inbound HTTPS traffic to enable synchronization to your managed domain.
$SyncRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowSyncWithAzureAD `
-Description "Allow synchronization with Azure AD" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 443

# Allow management of your domain over port 5986 (PowerShell Remoting)
$PSRemotingRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowPSRemoting `
-Description "Allow management of domain through port 5986" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
-SourceAddressPrefix 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, `
13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, `
52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, `
52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161 `
-SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 5986

# Allow management of your domain over port 3389 (remote desktop).
$RemoteDesktopRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowRD `
-Description "Allow management of domain through port 3389" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389

# Create the NSG with the 3 rules above
$Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroup -Location $Location `
-Name "AAD-DomainServices-NSG" -SecurityRules $SyncRule,$PSRemotingRule,$RemoteDesktopRule

# Find vnet and subnet
$Vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroup -Name $VnetName
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $Vnet -Name $SubnetName

# Set the nsg to the subnet and save the changes
$Subnet.NetworkSecurityGroup = $Nsg
Set-AzureRmVirtualNetwork -VirtualNetwork $Vnet
```

> [!NOTE]
> Este NSG predeterminado no bloquea el acceso al puerto que se usa para LDAP seguro. Para bloquear el acceso LDAP seguro a través de Internet, consulte [este artículo](active-directory-ds-troubleshoot-ldaps.md).
>

## <a name="need-help"></a>¿Necesita ayuda?
Póngase en contacto con el equipo de productos de Active Directory Domain Services para [compartir comentarios u obtener asistencia](active-directory-ds-contact-us.md).
