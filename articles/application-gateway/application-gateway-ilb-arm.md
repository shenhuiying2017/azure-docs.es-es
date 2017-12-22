---
title: Uso de Azure Application Gateway con el equilibrador de carga interno mediante PowerShell | Microsoft Docs
description: "En esta página se proporcionan instrucciones para crear, configurar, iniciar y eliminar una puerta de enlace de aplicaciones de Azure con un equilibrador de carga interno (ILB) en el Administrador de recursos de Azure"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 75cfd5a2-e378-4365-99ee-a2b2abda2e0d
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: 8d96af009055a5c0349f0ac17054bebee4e54d36
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb-by-using-azure-resource-manager"></a>Creación de una puerta de enlace de aplicaciones con un equilibrador de carga interno (ILB) mediante el Administrador de recursos de Azure

> [!div class="op_single_selector"]
> * [Azure Classic PowerShell](application-gateway-ilb.md)
> * [PowerShell del Administrador de recursos de Azure](application-gateway-ilb-arm.md)

Puerta de enlace de aplicaciones de Azure se puede configurar con una VIP conexión a Internet o con un punto de conexión interno no expuesto a Internet, también conocido como punto de conexión ILB (equilibrador de carga interno). La configuración de la puerta de enlace con un ILB es útil para aplicaciones de línea de negocio internas no expuestas a Internet. También es útil para los distintos servicios y niveles de una aplicación de niveles múltiples que se encuentran dentro de un límite de seguridad no expuesto a Internet, pero que aún así siguen necesitando distribución de carga round robin, permanencia de sesión o terminación SSL (Capa de sockets seguros).

Este artículo le guía por los pasos necesarios para configurar una puerta de enlace de aplicaciones con un ILB.

## <a name="before-you-begin"></a>Antes de empezar

1. Instale la versión más reciente de los cmdlets de Azure PowerShell mediante el Instalador de plataforma web. Puede descargar e instalar la versión más reciente desde la sección **Windows PowerShell** de la página [Descargas](https://azure.microsoft.com/downloads/).
2. Tendrá que crear una red virtual y una subred para Puerta de enlace de aplicaciones. Asegúrese de que ninguna máquina virtual o implementación en la nube usan la subred. La puerta de enlace de aplicaciones debe encontrarse en una subred de una red virtual.
3. Los servidores que configure para que usen la Puerta de enlace de aplicaciones deben existir, o bien sus puntos de conexión deben haberse creado en la red virtual o tener una dirección IP/VIP pública asignada.

## <a name="what-is-required-to-create-an-application-gateway"></a>¿Qué se necesita para crear una Puerta de enlace de aplicaciones?

* **Grupo de servidores back-end:** lista de direcciones IP de los servidores back-end. Las direcciones IP que se enumeran deben pertenecer a la red virtual, pero a otra subred de la puerta de enlace de aplicaciones, o deben ser IP/VIP públicas.
* **Configuración del grupo de servidores back-end:** cada grupo tiene una configuración en la que se incluye el puerto, el protocolo y la afinidad basada en cookies. Estos valores están vinculados a un grupo y se aplican a todos los servidores del grupo.
* **Puerto front-end:** este puerto es el puerto público que se abre en la puerta de enlace de aplicaciones. El tráfico llega a este puerto y después se redirige a uno de los servidores back-end.
* **Agente de escucha** : tiene un puerto front-end, un protocolo (Http o Https, que distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se configura la descarga de SSL).
* **Regla** : enlaza el agente de escucha y el grupo de servidores back-end y define a qué grupo de servidores back-end se dirigirá el tráfico cuando llega a un agente de escucha concreto. Actualmente, solo se admite la regla *básica* . La regla *básica* es la distribución de carga round robin.

## <a name="create-an-application-gateway"></a>Creación de una puerta de enlace de aplicaciones

La diferencia entre el uso del Portal de Azure clásico y Azure Resource Manager es el orden en que se crea la puerta de enlace de aplicaciones y los elementos que es necesario configurar.
Con Resource Manager, todos los elementos que componen una puerta de enlace de aplicaciones se configuran individualmente y, luego, se unen para crear el recurso de la puerta de enlace de aplicaciones.

Estos son los pasos necesarios para crear una puerta de enlace de aplicaciones:

1. Creación de un grupo de recursos para el Administrador de recursos
2. Creación de una red virtual y una subred para la puerta de enlace de aplicaciones
3. Creación de un objeto de configuración de la Puerta de enlace de aplicaciones
4. Crear un recurso de la puerta de enlace de aplicaciones

## <a name="create-a-resource-group-for-resource-manager"></a>Creación de un grupo de recursos para Resource Manager

Asegúrese de cambiar el modo de PowerShell para que use los cmdlets de Azure Resource Manager. Hay más información disponible en [Uso de Windows PowerShell con Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Paso 1

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>Paso 2

Compruebe las suscripciones para la cuenta.

```powershell
Get-AzureRmSubscription
```

Se le solicita que se autentique con sus credenciales.

### <a name="step-3"></a>Paso 3

Elección de la suscripción de Azure que se va a usar.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Paso 4

Cree un grupo de recursos nuevo (omita este paso si usa uno existente).

```powershell
New-AzureRmResourceGroup -Name appgw-rg -location "West US"
```

El Administrador de recursos de Azure requiere que todos los grupos de recursos especifiquen una ubicación. Esta se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una puerta de enlace de aplicaciones usan el mismo grupo de recursos.

En el ejemplo anterior, creamos un grupo de recursos denominado "appgw-rg" y la ubicación "West US".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Creación de una red virtual y una subred para la puerta de enlace de aplicaciones

En el ejemplo siguiente se muestra cómo crear una red virtual con Resource Manager:

### <a name="step-1"></a>Paso 1

```powershell
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

Este paso asigna el intervalo de direcciones 10.0.0.0/24 a la variable de subred que se va a usar para crear una red virtual.

### <a name="step-2"></a>Paso 2

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig
```

Este paso crea una red virtual denominada "appgwvnet" en el grupo de recursos "appgw-rg" para la región West US con el prefijo 10.0.0.0/16 y la subred 10.0.0.0/24.

### <a name="step-3"></a>Paso 3

```powershell
$subnet = $vnet.subnets[0]
```

Este paso asigna el objeto de subred a la variable $subnet para los siguientes pasos.

## <a name="create-an-application-gateway-configuration-object"></a>Creación de un objeto de configuración de la Puerta de enlace de aplicaciones

### <a name="step-1"></a>Paso 1

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

Este paso crea una configuración de la IP de la puerta de enlace de aplicaciones denominada "gatewayIP01". Cuando se inicia la Puerta de enlace de aplicaciones, elige una dirección IP de la subred configurada y redirige el tráfico de red a las direcciones IP en el grupo IP de back-end. Tenga en cuenta que cada instancia toma una dirección IP.

### <a name="step-2"></a>Paso 2

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.1.1.8,10.1.1.9,10.1.1.10
```

Este paso configura el grupo de direcciones IP de back-end denominado "pool01" con las direcciones IP "10.1.1.8, 10.1.1.9, 10.1.1.10". Son las direcciones IP que reciben el tráfico de red procedente del punto de conexión de la IP del front-end. Reemplaza las direcciones IP anteriores para agregar sus propios puntos de conexión de direcciones IP de la aplicación.

### <a name="step-3"></a>Paso 3

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Este paso configura la opción de la puerta de enlace de aplicaciones "poolsetting01" para el tráfico de red con carga equilibrada del grupo de back-end.

### <a name="step-4"></a>Paso 4

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

Este paso configura el puerto IP del front-end denominado "frontendport01" para el ILB.

### <a name="step-5"></a>Paso 5

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet
```

Este paso crea la configuración de la IP del front-end llamada "fipconfig01" y la asocia una dirección IP privada de la subred de la red virtual actual.

### <a name="step-6"></a>Paso 6

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

Este paso crea el agente de escucha "listener01" y asocia el puerto front-end con la configuración de la IP del front-end.

### <a name="step-7"></a>Paso 7

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

Este paso crea la regla de enrutamiento del equilibrador de carga denominada "rule01" que configura el comportamiento del equilibrador de carga.

### <a name="step-8"></a>Paso 8

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

Este paso configura el tamaño de instancia de la puerta de enlace de aplicaciones.

> [!NOTE]
> El valor predeterminado de *InstanceCount* es 2, con un valor máximo de 10. El valor predeterminado de *GatewaySize* es Medium. Se puede elegir entre Standard_Small, Standard_Medium y Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Creación de una puerta de enlace de aplicaciones con New-AzureApplicationGateway

Cree una puerta de enlace de aplicaciones con todos los elementos de configuración de los pasos anteriores. En el ejemplo, la puerta de enlace de aplicaciones se denomina "appgwtest".

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

Este paso crea una puerta de enlace de aplicaciones con todos los elementos de configuración de los pasos anteriores. En el ejemplo, la Puerta de enlace de aplicaciones se denomina "appgwtest".

## <a name="delete-an-application-gateway"></a>Eliminación de una puerta de enlace de aplicaciones

Para eliminar una puerta de enlace de aplicaciones, deberá realizar los pasos siguientes en orden:

1. Use el cmdlet `Stop-AzureRmApplicationGateway` para detener la puerta de enlace.
2. Utilice el cmdlet `Remove-AzureRmApplicationGateway` para quitar la puerta de enlace.
3. Compruebe que se quitó la puerta de enlace con el cmdlet `Get-AzureApplicationGateway`.

### <a name="step-1"></a>Paso 1

Obtenga el objeto de puerta de enlace de aplicaciones y asócielo a una variable "$getgw".

```powershell
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Paso 2

Use `Stop-AzureRmApplicationGateway` para detener la puerta de enlace de aplicaciones. Este ejemplo muestra el cmdlet `Stop-AzureRmApplicationGateway` en la primera línea, seguido de la salida.

```powershell
Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  
```

```
VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8
```

Cuando el estado de la puerta de enlace de aplicaciones sea detenido, use el cmdlet `Remove-AzureRmApplicationGateway` para quitar el servicio.

```powershell
Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force
```

```
VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301
```

> [!NOTE]
> Se puede usar el modificador **-force** para suprimir el mensaje de confirmación de eliminación.

Para comprobar que se ha quitado el servicio, puede usar el cmdlet `Get-AzureRmApplicationGateway`. Este paso no es necesario.

```powershell
Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

```
VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
```

## <a name="next-steps"></a>Pasos siguientes

Si desea configurar la descarga de SSL, consulte [Configuración de una puerta de enlace de aplicaciones para la descarga SSL mediante el modelo de implementación clásica](application-gateway-ssl.md).

Si desea configurar una puerta de enlace de aplicaciones para usarla con un ILB, consulte [Creación de una puerta de enlace de aplicaciones con un equilibrador de carga interno (ILB)](application-gateway-ilb.md).

Si desea obtener más información acerca de opciones de equilibrio de carga en general, vea:

* [Equilibrador de carga de Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

