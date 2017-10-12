---
title: "Configuración de la descarga de SSL para Azure Application Gateway mediante PowerShell | Microsoft Docs"
description: "En este artículo se ofrecen instrucciones para crear una puerta de enlace de aplicaciones con la descarga SSL mediante Azure Resource Manager."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 3c3681e0-f928-4682-9d97-567f8e278e13
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/19/2017
ms.author: davidmu
ms.openlocfilehash: ee48ca45ae0d337b5b919dbbb28341caf8af0d45
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-resource-manager"></a>Configuración de una puerta de enlace de aplicaciones para la descarga SSL mediante Azure Resource Manager

> [!div class="op_single_selector"]
> * [Portal de Azure](application-gateway-ssl-portal.md)
> * [PowerShell del Administrador de recursos de Azure](application-gateway-ssl-arm.md)
> * [PowerShell clásico de Azure](application-gateway-ssl.md)
> * [CLI de Azure 2.0](application-gateway-ssl-cli.md)

Azure Application Gateway puede configurarse para terminar la sesión Capa de sockets seguros (SSL) en la puerta de enlace para evitar las costosas tareas de descifrado SSL que tienen lugar en la granja de servidores web. La descarga SSL también simplifica la configuración del servidor front-end y la administración de la aplicación web.

## <a name="before-you-begin"></a>Antes de empezar

1. Instale la versión más reciente de los cmdlets de Azure PowerShell mediante el Instalador de plataforma web. Puede descargar e instalar la versión más reciente desde la sección **Windows PowerShell** de la [página Descargas](https://azure.microsoft.com/downloads/).
2. Cree una red virtual y una subred para la puerta de enlace de aplicaciones. Asegúrese de que ninguna máquina virtual o implementación en la nube usan la subred. La puerta de enlace de aplicaciones debe encontrarse en una subred de red virtual.
3. Los servidores que configure para que usen la puerta de enlace de aplicaciones deben existir, o bien sus puntos de conexión deben haberse creado en la red virtual o tener asignada una dirección IP pública o una dirección IP virtual (VIP).

## <a name="what-is-required-to-create-an-application-gateway"></a>¿Qué se necesita para crear una puerta de enlace de aplicaciones?

* **Back-end server pool** (Grupo de servidores back-end): lista de direcciones IP de los servidores back-end. Las direcciones IP que se enumeran deben pertenecer a la subred de la red virtual o ser una IP/VIP pública.
* **Configuración del grupo de servidores back-end:** cada grupo tiene una configuración en la que se incluye el puerto, el protocolo y la afinidad basada en cookies. Estos valores están vinculados a un grupo y se aplican a todos los servidores del grupo.
* **Front-end port** (Puerto front-end): este puerto es el puerto público que se abre en la puerta de enlace de aplicaciones. El tráfico llega a este puerto y después se redirige a uno de los servidores back-end.
* **Agente de escucha:** tiene un puerto front-end, un protocolo (Http o Https, que distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se configura la descarga SSL).
* **Regla:** la regla enlaza el agente de escucha y el grupo de servidores back-end y define a qué grupo de servidores back-end se va a dirigir el tráfico cuando llegue a un determinado agente de escucha. Actualmente, solo se admite la regla *básica* . La regla *básica* es la distribución de carga round robin.

**Notas de configuración adicionales**

Para la configuración de certificados SSL, el protocolo de **HttpListener** debería cambiar a **Https** (con distinción entre mayúsculas y minúsculas). Agregue el elemento **SslCertificate** a **HttpListener** con el valor de la variable configurado para el certificado SSL. El puerto front-end debe actualizarse al **443**.

**Para habilitar la afinidad basada en cookies:** puede configurar una puerta de enlace de aplicaciones para asegurarse de que las solicitudes de una sesión de cliente siempre se dirigen a la misma máquina virtual de la granja de servidores web. Para conseguirlo, inserte una cookie de sesión que permita a la puerta de enlace dirigir el tráfico de forma adecuada. Para habilitar la afinidad basada en cookies, establezca **CookieBasedAffinity** en **Habilitado** en el elemento **BackendHttpSettings**.

## <a name="create-an-application-gateway"></a>Creación de una puerta de enlace de aplicaciones

La diferencia entre el uso del modelo de implementación clásica de Azure y el de Azure Resource Manager es el orden en el que se crea una puerta de enlace de aplicaciones y los elementos que es preciso configurar.

Con Resource Manager, todos los componentes de una puerta de enlace de aplicaciones se configuran individualmente y, luego, se unen para crear un recurso de la puerta de enlace de aplicaciones.

Estos son los pasos necesarios para crear una puerta de enlace de aplicaciones:

1. [Creación de un grupo de recursos para Resource Manager](#create-a-resource-group-for-resource-manager)
2. [Creación de una red virtual, una subred y una IP pública para la puerta de enlace de aplicaciones](#create-virtual-network-subnet-and-public-IP-for-the-application-gateway)
3. [Creación de un objeto de configuración de la puerta de enlace de aplicaciones](#create-an-application-gateway-configuration-object)
4. [Creación de un recurso de la puerta de enlace de aplicaciones](#create-an-application-gateway-resource)

## <a name="create-a-resource-group-for-resource-manager"></a>Creación de un grupo de recursos para el Administrador de recursos

Asegúrese de cambiar el modo de PowerShell para que use los cmdlets de Azure Resource Manager. Para obtener más información, consulte [Uso de Windows PowerShell con el Administrador de recursos](../powershell-azure-resource-manager.md).

   1. Escriba el comando siguiente:

   ```powershell
   Login-AzureRmAccount
   ```

   2. Para comprobar las suscripciones de la cuenta, escriba los siguientes comandos:

   ```powershell
   Get-AzureRmSubscription
   ```

   Se le solicita que se autentique con sus credenciales.

   3. Para elegir qué suscripciones de Azure va a usar, escriba los siguientes comandos:

   ```powershell
   Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
   ```

   4. Para crear un grupo de recursos, escriba el siguiente comando. (Si utiliza un grupo de recursos existente, puede omitir este paso).

   ```powershell
   New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
   ```

Azure Resource Manager requiere que todos los grupos de recursos especifiquen una ubicación. Esta configuración se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una puerta de enlace de aplicaciones usan el mismo grupo de recursos.

En el ejemplo anterior, hemos creado un grupo de recursos denominado **appgw-RG** y la ubicación es **Oeste de EE. UU.**

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Creación de una red virtual y una subred para la puerta de enlace de aplicaciones

En el ejemplo siguiente se muestra cómo crear una red virtual con Resource Manager:

   1. Escriba el comando siguiente:

   ```powershell
   $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
   ```

   En este ejemplo se asigna el intervalo de direcciones **10.0.0.0/24** a la variable de subred que se va a usar para crear una red virtual.

   2. Escriba el comando siguiente:

   ```powershell
   $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

   En este ejemplo se crea una red virtual denominada **appgwvnet** en el grupo de recursos **appgw-rg** para la región **Oeste de EE. UU.** con el prefijo **10.0.0.0/16** y la subred **10.0.0.0/24**.

   3. Escriba el comando siguiente:

   ```powershell
   $subnet = $vnet.Subnets[0]
   ```

   En este ejemplo se asigna el objeto de subred a la variable **$subnet** para los siguientes pasos.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Creación de una dirección IP pública para la configuración del front-end

Para crear una dirección IP pública para la configuración del front-end, escriba el siguiente comando:

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

En este ejemplo se crea un recurso de IP público **publicIP01** en el grupo de recursos **appgw-rg** para la región **Oeste de EE. UU.**

## <a name="create-an-application-gateway-configuration-object"></a>Creación de un objeto de configuración de la Puerta de enlace de aplicaciones

   1. Para crear un objeto de configuración de la puerta de enlace de aplicaciones, escriba el siguiente comando:

   ```powershell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
   ```

   En este ejemplo se crea una configuración de la IP de la puerta de enlace de aplicaciones denominada **gatewayIP01**. Cuando se inicia Application Gateway, elige una dirección IP de la subred configurada y redirige el tráfico de red a las direcciones IP en el grupo IP de back-end. Tenga en cuenta que cada instancia toma una dirección IP.

   2. Escriba el comando siguiente:

   ```powershell
   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50
   ```

   En este ejemplo se configura el grupo de direcciones IP de back-end denominado **pool01** con las direcciones IP **134.170.185.46**, **134.170.188.221** y **134.170.185.50**. Estos valores las direcciones IP que reciben el tráfico de red procedente del punto de conexión de la IP del front-nd. Reemplace las direcciones IP del ejemplo anterior por las de los puntos de conexión de la aplicación web.

   3. Escriba el comando siguiente:

   ```powershell
   $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled
   ```

   En este ejemplo se configura la opción de la puerta de enlace de aplicaciones **poolsetting01** para el tráfico de red con equilibrio de carga en el grupo de back-end.

   4. Escriba el comando siguiente:

   ```powershell
   $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443
   ```

   En este ejemplo se configura el puerto IP del front-end denominado **frontendport01** para el punto de conexión de la IP pública.

   5. Escriba el comando siguiente:

   ```powershell
   $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password "<password>"
   ```

   En este ejemplo se configura el certificado que se usa para la conexión SSL. Es preciso que el certificado tenga el formato PFX, y que la contraseña tenga entre 4 y 12 caracteres.

   6. Escriba el comando siguiente:

   ```powershell
   $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
   ```

   En este ejemplo se crea la configuración de la IP de front-end denominada **fipconfig01** y asocia la dirección IP pública con dicha configuración.

   7. Escriba el comando siguiente:

   ```powershell
   $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert
   ```

   En este ejemplo se crea el agente de escucha llamado **listener01** y se asocia el puerto de front-end con la configuración de la IP del front-end y el certificado.

   8. Escriba el comando siguiente:

   ```powershell
   $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

   En este ejemplo se crea la regla de enrutamiento del equilibrador de carga denominada **rule01** que configura el comportamiento del equilibrador de carga.

   9. Escriba el comando siguiente:

   ```powershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
   ```

   En este ejemplo se configura el tamaño de instancia de la puerta de enlace de aplicaciones.

   > [!NOTE]
   > El valor predeterminado de **InstanceCount** es **2**, con un valor máximo de 10. El valor predeterminado de **GatewaySize** es **Medium**. Se puede elegir entre Standard_Small, Standard_Medium y Standard_Large.

   10. Escriba el comando siguiente:

   ```powershell
   $policy = New-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S
   ```

   Este paso permite definir la directiva SSL que se usará en la puerta de enlace de aplicaciones. Para más información, consulte [Configuración de versiones de directivas SSL y conjuntos de cifrado en Application Gateway](application-gateway-configure-ssl-policy-powershell.md).

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Creación de una puerta de enlace de aplicaciones con New-AzureApplicationGateway

Escriba el comando siguiente:

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert -SslPolicy $policy
```

En este ejemplo se crea una puerta de enlace de aplicaciones con todos los elementos de configuración de los pasos anteriores. En el ejemplo, la puerta de enlace de aplicaciones se denomina **appgwtest**.

## <a name="get-the-application-gateway-dns-name"></a>Obtención del nombre DNS de una puerta de enlace de aplicaciones

Una vez que se crea la puerta de enlace, el siguiente paso es configurar el front-end para la comunicación. La puerta de enlace de aplicaciones requiere un nombre DNS asignado dinámicamente, que no es descriptivo, cuando se utiliza una dirección IP pública. Para asegurarse de que los usuarios finales puedan llegar a la puerta de enlace de aplicaciones, puede utilizar un registro CNAME para que apunte al punto de conexión público de dicha puerta. Para más información, consulte [Configuración de un nombre de dominio personalizado en Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Para obtener el nombre DNS de la puerta de enlace de aplicaciones, recupere los detalles de esta puerta y su nombre DNS o IP asociados mediante el elemento **PublicIPAddress** asociado a dicha puerta. Utilice el nombre DNS de la puerta de enlace de aplicaciones para crear un registro CNAME, que hace que las dos aplicaciones web apunten a este nombre DNS. No se recomienda el uso de registros A, ya que la IP virtual puede cambiar al reiniciarse la puerta de enlace de aplicaciones.


```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Pasos siguientes

Si quiere configurar una puerta de enlace de aplicaciones para usarla con el equilibrador de carga interno, consulte [Creación de una puerta de enlace de aplicaciones con un equilibrador de carga interno](application-gateway-ilb.md).

Para más información sobre las de opciones de equilibrio de carga en general, consulte:

* [Equilibrador de carga de Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
