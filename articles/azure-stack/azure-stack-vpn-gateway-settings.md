---
title: "Configuración de puerta de enlace VPN para Azure Stack | Microsoft Docs"
description: "Obtenga información acerca de la configuración de las puertas de enlace VPN que se utilizan con Azure Stack."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: fa8d3adc-8f5a-4b4f-8227-4381cf952c56
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/01/2017
ms.author: brenduns
ms.openlocfilehash: 1276310a35d0d69a4111a58b9675f15bb5285a08
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="vpn-gateway-configuration-settings-for-azure-stack"></a>Valores de la configuración de una puerta de enlace VPN para Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y kit de desarrollo de Azure Stack*

Una puerta de enlace de VPN es un tipo de puerta de enlace de red virtual que envía tráfico cifrado entre una red virtual de Azure Stack y una puerta de enlace de VPN remota. La puerta de enlace de VPN remota puede estar en Azure, un dispositivo del centro de datos o un dispositivo de otro sitio.  Si hay conectividad de red entre los dos puntos de conexión, puede establecer una conexión VPN de sitio a sitio (S2S) segura entre las dos redes.

Una conexión de puerta de enlace de VPN se basa en la configuración de varios recursos, cada uno de los cuales contiene valores configurables. Las secciones de este artículo tratan los recursos y la configuración relacionados con una puerta de enlace de VPN para una red virtual creada en el modelo de implementación de Resource Manager. Puede encontrar las descripciones y los diagramas de topología de cada solución de conexión en el artículo [About VPN gateway for Azure Stack](azure-stack-vpn-gateway-about-vpn-gateways.md) (Acerca VPN Gateway para Azure Stack).

## <a name="vpn-gateway-settings"></a>Configuración de VPN Gateway

### <a name="gateway-types"></a>Tipos de puerta de enlace
Cada red virtual de Azure Stack es compatible con una puerta de enlace de red virtual individual, que debe ser del tipo **Vpn**.  Esta compatibilidad difiere de la de Azure, que admite tipos adicionales.  

Al crear una puerta de enlace de red virtual, debe asegurarse de que el tipo de puerta de enlace es el correcto para su configuración. Una puerta de enlace de VPN requiere `-GatewayType Vpn`.

Ejemplo:
```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn
-VpnType RouteBased
```

### <a name="gateway-skus"></a>SKU de puerta de enlace
Al crear una puerta de enlace de red virtual, debe especificar la SKU de la puerta de enlace que desea usar. Seleccione las SKU que cumplan sus requisitos en función de los tipos de cargas de trabajo, rendimientos, características y Acuerdos de Nivel de Servicio.

>[!NOTE]
> Las redes virtuales clásicas deben seguir utilizando las SKU antiguas. Para más información acerca de las SKU de puerta de enlace antiguas, consulte [Working with virtual network gateway SKUs (old)](/azure/vpn-gateway/vpn-gateway-about-skus-legacy) (Uso de las antiguas SKU de puerta de enlace de red virtual).

Azure Stack ofrece las siguientes SKU de VPN Gateway:

|   | Rendimiento de VPN Gateway |Túneles IPsec máx. de VPN Gateway |
|-------|-------|-------|
|**SKU básica**  | 100 Mbps  | 10    |
|**SKU estándar**           | 100 Mbps  | 10    |
|**SKU de alto rendimiento** | 200 Mbps    | 30    |

### <a name="resizing-gateway-skus"></a>Cambio de tamaño de las SKU de puerta de enlace
Azure Stack no admite un cambio de tamaño de las SKU entre las SKU heredadas admitidas.

De igual modo, Azure Stack no admite un cambio de las SKU heredados compatibles (Básico, Estándar y Alto rendimiento), a las SKU más recientes compatibles con Azure (VpnGw1, VpnGw2 y VpnGw3).

### <a name="configure-the-gateway-sku"></a>Configuración de la SKU de puerta de enlace
#### <a name="azure-stack-portal"></a>Portal de Azure Stack
Si usa el portal de Azure Stack para crear una puerta de enlace de red virtual de Resource Manager, puede seleccionar la SKU de la puerta de enlace mediante el menú desplegable. Las opciones que se presentan corresponden con el tipo de puerta de enlace y tipo de VPN que seleccione.

#### <a name="powershell"></a>PowerShell
En el siguiente ejemplo de PowerShell se especifica -GatewaySku como VpnGw1.

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku VpnGw1
-GatewayType Vpn -VpnType RouteBased
```
### <a name="connection-types"></a>Tipos de conexión
En el modelo de implementación de Resource Manager, cada configuración requiere un tipo de conexión de puerta de enlace de red virtual específico. Los valores de PowerShell de Resource Manager disponibles para -ConnectionType son:

- IPsec

En el siguiente ejemplo de PowerShell, se crea una conexión S2S que requiere el tipo de conexión IPsec.  

```PowerShell
New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

### <a name="vpn-types"></a>Tipos de VPN
Al crear la puerta de enlace de red virtual para una configuración de puerta de enlace de VPN, debe especificar un tipo de VPN. El tipo de VPN que elija dependerá de la topología de conexión que desee crear.  Un tipo de VPN también puede depender del hardware que se esté usando. Las configuraciones de S2S requieren un dispositivo VPN. Algunos dispositivos VPN solo serán compatibles con un determinado tipo de VPN.

> [!IMPORTANT]  
> En este momento, Azure Stack solo admite el tipo de VPN basado en ruta. Si el dispositivo solo admite VPN basadas en directiva, no se admiten conexiones a dichos dispositivos desde Azure Stack.

- **PolicyBased**: *(compatible con Azure, pero no con Azure Stack)* las VPN basadas en directiva cifran y dirigen los paquetes a través de túneles de IPsec basados en las directivas de IPsec, que están configuradas con las combinaciones de prefijos de dirección entre su red local y la red virtual de Azure Stack. La directiva (o el selector de tráfico) se define normalmente como una lista de acceso en la configuración del dispositivo VPN.

- **RouteBased**: las VPN basadas en rutas utilizan "rutas" en la dirección IP de reenvío o en la tabla de enrutamiento para dirigir los paquetes a sus correspondientes interfaces de túnel. A continuación, las interfaces de túnel cifran o descifran los paquetes dentro y fuera de los túneles. La directiva o el selector de tráfico para las VPN basadas en enrutamiento se configura como una conectividad del tipo de cualquiera a cualquiera (o caracteres comodín). El valor de un tipo de VPN basada en ruta es RouteBased.

En el siguiente ejemplo de PowerShell se especifica -VpnType como RouteBased. Al crear una puerta de enlace, debe asegurarse de que el tipo de VPN es el correcto para su configuración.

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig
-GatewayType Vpn -VpnType RouteBased
```
### <a name="gateway-requirements"></a>Requisitos de la puerta de enlace
La tabla siguiente enumera los requisitos de las puertas de enlace VPN.

| |VPN Gateway de nivel Básico basada en directiva | VPN Gateway de nivel Básico basada en ruta | VPN Gateway de nivel Estándar basada en ruta | VPN Gateway de alto rendimiento basada en ruta|
|--|--|--|--|--|
| **Conectividad de sitio a sitio (conectividad S2S)** | No compatible | Configuración de VPN de RouteBased | Configuración de VPN de RouteBased | Configuración de VPN de RouteBased |
| **Método de autenticación**  | No compatible | Clave precompartida para conectividad S2S  | Clave precompartida para conectividad S2S  | Clave precompartida para conectividad S2S  |   
| **Número máximo de conexiones S2S**  | No compatible | 10 | 10| 30|
|**Compatibilidad con enrutamiento activo (BGP)** | No compatible | No compatible | Compatible | Compatible |

### <a name="gateway-subnet"></a>Subred de puerta de enlace
Antes de crear una puerta de enlace de VPN, debe crear una subred de puerta de enlace. La subred de puerta de enlace contiene las direcciones IP que usan los servicios y las máquinas virtuales de la puerta de enlace de red virtual. Al crear la puerta de enlace de red virtual, las máquinas virtuales de puerta de enlace se implementan en la subred de puerta de enlace, y se configuran con las opciones de puerta de enlace de VPN necesarias. No implemente nada más (por ejemplo, máquinas virtuales adicionales) en la subred de puerta de enlace. Para que la subred de puerta de enlace funcione correctamente, su nombre tiene que ser “GatewaySubnet2”. Asignar el nombre "GatewaySubnet" a la subred de puerta de enlace permite a Azure Stack identificar la subred en la que se implementan las máquinas virtuales y los servicios de puerta de enlace de red virtual.

Al crear la subred de puerta de enlace, especifique el número de direcciones IP que contiene la subred. Las direcciones IP de la subred de puerta de enlace se asignan a las máquinas virtuales y los servicios de puerta de enlace. Algunas configuraciones requieren más direcciones IP que otras. Consulte los requisitos de configuración que desea crear y compruebe que la subred de puerta de enlace que desea crear los cumple. Debe asegurarse también de que la subred de puerta de enlace contenga suficientes direcciones IP para acomodar posibles configuraciones adicionales. Aunque es posible crear una subred de puerta de enlace tan pequeña como /29, se recomienda que sea /28 o mayor (/28, /27, /26, etc.). De este modo, si agrega funcionalidad en el futuro, no tendrá que desconectar la puerta de enlace y luego eliminar y volver a crear la subred de puerta de enlace para que admita más direcciones IP.

En el ejemplo de PowerShell de Resource Manager siguiente, se muestra una subred de puerta de enlace con el nombre GatewaySubnet. Puede ver que la notación CIDR especifica /27, que permite suficientes direcciones IP para la mayoría de las configuraciones que existen.

```PowerShell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

> [!IMPORTANT]
> Cuando trabaje con subredes de la puerta de enlace, evite asociar un grupo de seguridad de red (NSG) a la subred de la puerta de enlace. La asociación de un grupo de seguridad de red a esta subred puede causar que la puerta de enlace de VPN deje de funcionar como cabría esperar. Para más información acerca de los grupos de seguridad de red, consulte [¿Qué es un grupo de seguridad de red?](/azure/virtual-network/virtual-networks-nsg)

### <a name="local-network-gateways"></a>Puertas de enlace de red local
Al crear una configuración de puerta de enlace de VPN en Azure, la puerta de enlace de red local a menudo representa la ubicación local. En Azure Stack representa cualquier dispositivo VPN remoto que se encuentra fuera de Azure Stack.  Podría ser un dispositivo VPN de su centro de datos, un centro de datos remoto o VPN Gateway en Azure.

Asigne un nombre a la puerta de enlace de red local, así como la dirección IP pública del dispositivo VPN y especificar los prefijos de dirección que se encuentran en la ubicación local. Azure examina los prefijos de dirección de destino para el tráfico de red, consulta la configuración que especificó para la puerta de enlace de red local y enruta los paquetes según corresponda.

En el ejemplo siguiente de PowerShell, se crea una nueva puerta de enlace de red local:

```PowerShell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```
A veces es necesario modificar la configuración de la puerta de enlace de red local. Por ejemplo, al agregar o modificar el intervalo de direcciones, o si cambia la dirección IP del dispositivo VPN. Vea [Modificación de la configuración de la puerta de enlace de red local mediante PowerShell](/azure/vpn-gateway/vpn-gateway-modify-local-network-gateway).

## <a name="ipsecike-parameters"></a>Parámetros de IPsec/IKE
Cuando se configura una conexión VPN en Azure Stack, es preciso configurar la conexión en ambos extremos.  Si va a configurar una conexión VPN entre Azure Stack y un dispositivo de hardware, como un conmutador o un enrutador que actúa como VPN Gateway, dicho dispositivo puede pedirle más valores.

A diferencia de Azure, que admite varias ofertas como iniciador y respondedor, Azure Stack admite solo una.

###  <a name="ike-phase-1-main-mode-parameters"></a>Parámetros de la fase 1 de IKE (Modo principal)
| Propiedad              | Valor|
|-|-|
| Versión de IKE           | IKEv2 |
|Grupo Diffie-Hellman   | Grupo 2 (1024 bits) |
| Método de autenticación | Clave previamente compartida |
|Algoritmos de cifrado y hash | AES256, SHA256 |
|Vigencia de SA (tiempo)     | 28.800 segundos|

### <a name="ike-phase-2-quick-mode-parameters"></a>Parámetros de la fase 2 de IKE (modo rápido)
| Propiedad| Valor|
|-|-|
|Versión de IKE |IKEv2 |
|Cifrados y algoritmos hash (cifrado)     | GCMAES256|
|Cifrados y algoritmos hash (autenticación) | GCMAES256|
|Vigencia de SA (tiempo)  | 3.600 segundos |
|Vigencia de SA (bytes) | 819 200       |
|Confidencialidad directa perfecta (PFS) |PFS2048 |
|Detección de cuellos del mismo nivel | Compatible|  
