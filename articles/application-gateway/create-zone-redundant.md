---
title: Creación de una instancia de Azure Application Gateway con redundancia de zona
description: Aprenda a crear una instancia de Azure Application Gateway con redundancia de zona que no requiere una puerta de enlace distinta en cada zona.
services: application-gateway
author: amsriva
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 5/8/2018
ms.author: victorh
ms.openlocfilehash: 03bc58db813534fdd17c9567f6425ab7357ed901
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937708"
---
# <a name="create-a-zone-redundant-azure-application-gateway---private-preview"></a>Creación de una instancia de Azure Application Gateway con redundancia de zona: versión preliminar privada

La plataforma con redundancia de zona de Application Gateway es una nueva referencia de almacén (SKU) que ofrece muchas mejoras sobre la SKU de Application Gateway existente, por ejemplo:
- **Resistencia de la zona**: una implementación de Application Gateway ahora puede abarcar varias Availability Zones, lo que elimina la necesidad de aprovisionar y anclar distintas instancias de Application Gateway en cada zona con Traffic Manager. Puede elegir una sola zona o varias donde se implementen instancias de Application Gateway, lo que garantiza la resistencia a errores de zona. El grupo de back-end de las aplicaciones se puede distribuir de manera similar entre las zonas de disponibilidad.
- **Mejoras de rendimiento**
- **VIP estática**: la dirección IP virtual de Application Gateway admite ahora exclusivamente el tipo VIP estática. Con esto se garantiza que la VIP asociada a Application Gateway no cambia tras un reinicio.
- **Integración de Key Vault para los certificados SSL de cliente**
- **Implementación y actualización más rápidas**

> [!NOTE]
> Application Gateway con redundancia de zona se encuentra en este momento en versión preliminar privada. Esta versión preliminar se ofrece sin contrato de nivel de servicio y no es aconsejable usarla para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="supported-regions"></a>Regiones admitidas

Las instancias de Azure Application Gateway con redundancia de zona se admiten en este momento en la región Este de EE. UU. 2. Próximamente se agregarán otras regiones.

## <a name="topologies"></a>Topologías

Con la versión actual, ya no es necesario crear instancias de Application Gateway ancladas por zona para obtener redundancia de zona. Una misma implementación de Application Gateway puede abarcar ahora varias zonas.

Se requieren por lo menos tres instancias para garantizar que se extiendan a través de las tres zonas. Application Gateway distribuye las instancias en distintas zonas a medida que se agregan otras instancias.

Las topologías con redundancia de zona anteriores presentan un aspecto similar al del diagrama siguiente:

![Anterior topología con redundancia de zona](media/create-zone-redundant/old-redundant.png)

La nueva topología con redundancia de zona presenta un aspecto similar al del diagrama siguiente:

![Nueva topología con redundancia de zona](media/create-zone-redundant/new-redundant.png)

## <a name="deployment"></a>Implementación

### <a name="prerequisites"></a>requisitos previos

La funcionalidad de redundancia de zona solo está disponible en este momento en versión preliminar privada. Debe enviar un correo electrónico a: appgwxzone@microsoft.com para que se le incluya en la lista de permitidos. Una vez que reciba una confirmación, puede continuar con los pasos siguientes. Incluya la siguiente información en el correo electrónico de inclusión en la lista blanca:

- Id. de suscripción
- Nombre de la región
- Número aproximado de instancias de Application Gateway necesarias

### <a name="resource-manager-template-deployment"></a>Implementación de plantillas del Administrador de recursos

1. Asegúrese de que la suscripción que utiliza está en la lista de permitidos tal y como se ha indicado anteriormente.
2. Después de recibir una confirmación, inicie sesión en la cuenta de Azure y seleccione la suscripción correspondiente si hay más de una suscripción. Asegúrese de seleccionar la suscripción que se incluyó en la lista de permitidos.

   ```PowerShell
   Login-AzureRmAccount

   Select-AzureRmSubscription -Subscription "<whitelisted subscription name>”
   ```
3. Creación de un nuevo grupo de recursos

   ``` PowerShell
   New-AzureRmResourceGroup -Name <resource group name> -Location "East US 2"
   ```
4. Descargue las plantillas de [GitHub](https://github.com/amitsriva/CrossZonePreview) y tome nota de la carpeta donde las guarda.
5. Cree una implementación en el grupo de recursos que ha creado. Modifique el archivo de plantilla y parámetros de forma adecuada antes de realizar la implementación. 

   El siguiente diagrama muestra donde puede recuperar el identificador del inquilino en Azure Portal:

   ![Identificador de inquilino en el portal](media/create-zone-redundant/tenant-id.png)

Esta plantilla crea los siguientes recursos:

- **Identidad asignada por el usuario**: se utiliza para habilitar instancias de Application Gateway con acceso a Key Vault para recuperar los certificados almacenados por el usuario.
- **KeyVault**: Key Vault donde se guarda el certificado del usuario. También puede ser una instancia de Key Vault existente.
- **Secreto**: clave privada que se guarda en Key Vault.
- **Directiva de acceso**: directiva de acceso que se aplica a la instancia de Key Vault que concede el permiso con la identidad asignada por el usuario para que las implementaciones de Application Gateway puedan recuperar los certificados de usuario.
- **Dirección IP pública**: dirección IP reservada que se utiliza para el acceso a Application Gateway. Esta dirección IP no cambia nunca durante el ciclo de vida de Application Gateway.
- **Grupos de seguridad de red**: NSG creado automáticamente en la subred de Application Gateway que abre el tráfico de puerto en el agente de escucha configurado. Este NSG se crea y administra explícitamente en la nueva SKU, en comparación con la SKU anterior en que estaba implícito.
- **Microsoft Azure Virtual Network**: red virtual donde se implementan Application Gateway y las aplicaciones.
- **Application Gateway**: crea un servicio Application Gateway con instancias en las zonas necesarias. Se seleccionan todas las zonas (1,2,3) de manera predeterminada. El nombre de SKU se cambia a *Standard_v2*. El nombre de esta SKU está sujeto a cambios. Actualmente, la configuración de escalado automático tiene el número mínimo y máximo establecido en el número necesario de instancias. Una vez habilitado el escalado automático, esto puede ajustarse.

```PowerShell
New-AzureRmResourceGroupDeployment -Name Deployment1 -ResourceGroupName AmitVMSSLinuxTest9 -TemplateFile <complete path to template.json> -TemplateParameterFile <complete path to parameters.json>
```
### <a name="powershell-deployment"></a>Implementación de PowerShell

1. Asegúrese de que la suscripción que utiliza está en la lista de permitidos tal y como se indica en los requisitos previos.
2. Descargue e instale el MSI PowerShell privado desde [GitHub](https://github.com/amitsriva/CrossZonePreview/blob/master/Azure-Cmdlets-5.7.0.19009-x64.msi).
3. Descargue el archivo zip de PowerShell privado desde la ubicación indicada en el correo electrónico de confirmación de registro de versión preliminar. Descomprima el archivo en la unidad y tome nota de la ubicación.
4. Una vez habilitada la versión preliminar, cargue primero los módulos en versión preliminar antes de iniciar sesión en la cuenta:

   ```PowerShell
   $azurePSPath = "<complete path to Azure-PowerShell folder>"
   import-module "$azurePSPath\AzureRM.Profile\AzureRM.Profile.psd1"
   import-module "$azurePSPath\Azure.Storage\Azure.Storage.psd1"
   import-module "$azurePSPath\AzureRM.Resources\AzureRM.Resources.psd1"
   import-module "$azurePSPath\AzureRM.Network\AzureRM.Network.psd1"
   import-module "$azurePSPath\AzureRM.KeyVault\AzureRM.KeyVault.psd1"
   ```

4. Inicie sesión en la cuenta de Azure y seleccione la suscripción que quiere si hay más de una. Asegúrese de seleccionar la suscripción adecuada que se incluyó en la lista de permitidos.
5. Ejecute los siguientes comandos para establecer constantes comunes que incluyen los nombres de la mayoría de las entidades que se crean. 

   Modifique las entradas según sea necesario para sus preferencias de nomenclatura.

   ```PowerShell
   $location = "eastus2"
   $version = "300"

   $rgname = "RG_A_$version"
   $appgwName = "AGW_A_$version"
   $vaultName = "KVA$version"
   $userAssignedIdentityName = "UI_A_$version"
   $certificateName = "KVCA$version"
   $nsgName = "NSG_A_$version"
   $vnetName = "VN_A_$version"
   $gwSubnetName = "SN_A_$version"
   $gipconfigname = "GC_A_$version"
   $publicIpName = "PIP_A_$version"
   $fipconfig01Name = "FC_A_$version"
   $poolName = "BP_A_$version"
   $frontendPort01Name = "FP1_A_$version"
   $frontendPort02Name = "FP2_A_$version"
   $poolSetting01Name = "BS_A_$version"
   $listener01Name = "HL1_A_$version"
   $listener02Name = "HL2_A_$version"
   $rule01Name = "RR1_A_$version"
   $rule02Name = "RR2_A_$version"
   $AddressPrefix = "111.111.222.0" 
   ```
6. Creación del grupo de recursos:

   ```PowerShell
   $resourceGroup = New-AzureRmResourceGroup -Name $rgname -Location $location -Force
   ```
7. Cree la identidad asignada por el usuario que se usa para proporcionar acceso a Application Gateway a fin de recuperar los certificados de Key Vault.

   ```PowerShell
   $userAssignedIdentity = New-AzureRmResource -ResourceGroupName $rgname -Location $location -ResourceName $userAssignedIdentityName -ResourceType Microsoft.ManagedIdentity/userAssignedIdentities -Force
   ```
8. Cree la instancia de Key Vault que se va a usar para guardar el certificado:

   ```PowerShell
   $keyVault = New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgname -Location $location -EnableSoftDelete
   ```
9. Cargue el certificado en Key Vault como secreto:

   ```PowerShell
   $securepfxpwd = ConvertTo-SecureString -String "<password>" -AsPlainText -Force

   $cert = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name         $certificateName -FilePath ‘<path to pfx file>'  -Password $securepfxpwd
   ```
10. Asigne la directiva de acceso a la instancia de Key Vault con la identidad asignada por el usuario. Esto permite el acceso de las instancias de Application Gateway al secreto de Key Vault:

   ```PowerShell
   Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $rgname -PermissionsToSecrets get -ObjectId $userAssignedIdentity.Properties.principalId
   ```
11. Cree el grupo de seguridad de red (NSG) para permitir el acceso a la subred de Application Gateway en los puertos en los que se crean agentes de escucha.

    Por ejemplo, si se usa HTTP/HTTPS en los puertos predeterminados, el NSG permitiría el acceso de entrada a 80, 443 y 65200-65535 para operaciones de administración.

   ```PowerShell
   $srule01 = New-AzureRmNetworkSecurityRuleConfig -Name "listeners" -Direction Inbound -SourceAddressPrefix * -SourcePortRange * -Protocol * -DestinationAddressPrefix * -DestinationPortRange 22,80,443 -Access Allow -Priority 100

   $srule02 = New-AzureRmNetworkSecurityRuleConfig -Name "managementPorts" -Direction Inbound -SourceAddressPrefix * -SourcePortRange * -Protocol * -DestinationAddressPrefix * -DestinationPortRange "65200-65535" -Access Allow -Priority 101

   $nsg = New-AzureRmNetworkSecurityGroup -Name $nsgName -ResourceGroupName $rgname -Location $location -SecurityRules $srule01,$srule02 -Force
   ```

12. Cree una red virtual y subredes:

   ```PowerShell
   $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 
   $gwSubnetName -AddressPrefix "$AddressPrefix/24" -NetworkSecurityGroup $nsg

   $vnet = New-AzureRmvirtualNetwork -Name $vnetName -ResourceGroupName $rgname -Location $location -AddressPrefix "$AddressPrefix/24" -Subnet $gwSubnet -Force
   ```
13. Cree una dirección IP pública de tipo reservada o estática:

   ```PowerShell
   $publicip = New-AzureRmPublicIpAddress -ResourceGroupName $rgname -name $publicIpName -location $location -AllocationMethod Static -Sku Standard -Force
   ```

14. Cree la instancia de Application Gateway:

   ```PowerShell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name $gipconfigname -Subnet $gwSubnet

   $fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name $fipconfig01Name -PublicIPAddress $publicip

   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name $poolName -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

   $fp01 = New-AzureRmApplicationGatewayFrontendPort -Name $frontendPort01Name -Port 443

   $fp02 = New-AzureRmApplicationGatewayFrontendPort -Name $frontendPort02Name -Port 80

   $sslCert01 = New-AzureRmApplicationGatewaySslCertificate -Name "SSLCert" -KeyVaultSecretId $secret.Id

   $listener01 = New-AzureRmApplicationGatewayHttpListener -Name $listener01Name -Protocol Https -FrontendIPConfiguration
 $fipconfig01 -FrontendPort $fp01 -SslCertificate $sslCert01

   $listener02 = New-AzureRmApplicationGatewayHttpListener -Name $listener02Name -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp02

   $poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name $poolSetting01Name -Port 80 -Protocol Http -CookieBasedAffinity Disabled

   $rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name $rule01Name -RuleType basic -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

   $rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name $rule02Name -RuleType basic -BackendHttpSettings $poolSetting01 -HttpListener $listener02 -BackendAddressPool $pool

   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2

   $listeners = @($listener02)

   $fps = @($fp01, $fp02)

   $fipconfigs = @($fipconfig01)

   $sslCerts = @($sslCert01)

   $rules = @($rule01, $rule02)

   $listeners = @($listener01, $listener02)

   $appgw = New-AzureRmApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Location $location -UserAssignedIdentityId $userAssignedIdentity.ResourceId -Probes $probeHttps -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfigs -FrontendPorts $fps -HttpListeners $listeners -RequestRoutingRules $rules -Sku $sku -SslPolicy $sslPolicy -sslCertificates $sslCerts -Force
   ```

15. Recupere la dirección IP pública de la instancia de Application Gateway creada:

   ```PowerShell
   $pip = Get-AzureRmPublicIpAddress -Name $publicIpName -ResourceGroupName $rgname $pip.IpAddress
   ```

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

-  ¿Se me cobrará Application Gateway en versión preliminar?

   Durante la versión preliminar, no hay ningún cargo. Se le cobrarán los recursos que no sean Application Gateway, como el almacén de claves, las máquinas virtuales, etcétera.
- ¿En qué regiones está disponible la versión preliminar?

   La versión preliminar se encuentra disponible actualmente en la región Este de EE. UU. 2. Próximamente se agregarán otras regiones.
- ¿Se admite el portal en la versión preliminar?

   No, durante la versión preliminar privada solo se admite un módulo de PowerShell privado y una plantilla del Administrador de recursos.

- ¿Se admiten cargas de trabajo de producción durante la versión preliminar privada?

   No hay ningún contrato de nivel de servicio ni soporte durante la versión preliminar privada. No es aconsejable colocar cargas de trabajo de producción durante las versiones preliminares. El soporte se limita a la interacción directa con el grupo de productos mediante el alias de correo electrónico de la versión preliminar.

- ¿Cómo se informa de un problema?

   Puede que la versión preliminar privada contenga errores y tenga implementaciones de código frecuentes. Utilice el alias de soporte appgwxzone@microsoft.com para informar de problemas y obtener ayuda.

## <a name="known-issues-and-limitations"></a>Problemas conocidos y limitaciones


|Problema  |Detalles  |
|---------|---------|---------|
|Facturación     |No hay facturación actualmente.|
|Registros de diagnóstico (no métricas)     |En este momento no aparecen registros de rendimiento ni de solicitudes y respuestas.|
|Portal/CLI/SDK     |No se ofrece soporte para el portal, la CLI ni el SDK. El portal no debe utilizarse para emitir actualizaciones de puertas de enlace en versión preliminar.|
|La actualización mediante la plantilla no se realiza a veces     |Esto se debe a la condición de carrera con la directiva de acceso de KeyVault|Una vez creada la instancia de Key Vault y la identidad asignada por el usuario, se pueden quitar de la plantilla y en esta solo se requieren referencias al secreto y la identidad.|
|Escalado automático     |Actualmente no se ofrece soporte para el escalado automático.|
|WAF     |WAF no se admite en estos momentos.|
|Direcciones VIP dinámicas y certificados suministrados por el usuario     |No se admiten en el nuevo modelo. Use Key Vault para almacenar certificados y direcciones VIP estáticas.|
|Misma subred para la versión anterior y la versión preliminar de Application Gateway     |No se puede usar una subred con una instancia de Application Gateway existente (modelo anterior) para la versión preliminar privada.|
|HTTP/2, modo FIPS, WebSocket, Azure Web Apps como back-end     |No se admiten actualmente. |


## <a name="support-and-feedback"></a>Soporte y comentarios

Para recibir soporte y comentarios, póngase en contacto con appgwxzone@microsoft.com. El grupo de productos de Application Gateway estará encantado de recibir sus comentarios sobre las mejoras y ofrecerle orientación en caso necesario.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre otras características de Application Gateway:

- [¿Qué es Azure Application Gateway?](overview.md)