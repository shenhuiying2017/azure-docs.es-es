---
title: "Solución de errores de puerta de enlace incorrecta (502) en el servicio Azure Application Gateway | Microsoft Docs"
description: "Obtenga información sobre cómo solucionar errores 502 en el servicio Application Gateway."
services: application-gateway
documentationcenter: na
author: amitsriva
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 1d431ead-d318-47d8-b3ad-9c69f7e08813
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2017
ms.author: amsriva
ms.openlocfilehash: e0099734a81cd8b1edf5cf80cb56b5c322a5feee
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Solución de errores de puerta de enlace incorrecta en el servicio Puerta de enlace de aplicaciones

Aprenda a solucionar problemas de errores de puerta de enlace incorrecta (502) recibidos al usar Application Gateway.

## <a name="overview"></a>Información general

Después de configurar una instancia de Azure Application Gateway, uno de los errores que se pueden encontrar los usuarios es "Error de servidor 502: el servidor web recibió una respuesta no válida mientras actuaba como puerta de enlace o servidor proxy". Este error puede ocurrir debido a los siguientes motivos principales:

* Un grupo de seguridad de red, una ruta definida por el usuario o un DNS personalizado están bloqueando el acceso a los miembros del grupo de back-end.
* Las máquinas virtuales de back-end o instancias del conjunto de escalado de máquina virtual [no responden a la sonda de estado predeterminada](#problems-with-default-health-probe.md).
* [La configuración de las sondas de estado personalizadas](#problems-with-custom-health-probe.md) no es válida o adecuada.
* [El grupo back-end de la instancia de Azure Application Gateway no está configurado o está vacío](#empty-backendaddresspool).
* Ninguna de las máquinas virtuales o instancias del [conjunto de escalado de máquina virtual está en buen estado](#unhealthy-instances-in-backendaddresspool).
* [El tiempo de espera de solicitud se superó o hay problemas de conectividad](#request-time-out) con las solicitudes de usuario.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Problema con grupo de seguridad de red, ruta definida por el usuario o DNS personalizado

### <a name="cause"></a>Causa

Si el acceso al back-end se bloquea debido a la presencia de un grupo de seguridad de red, una ruta definida por el usuario o un DNS personalizado, las instancias de Application Gateway no podrán ponerse en contacto con el grupo de back-end y darán como resultado errores de sondeo que provocarán errores 502. Tenga en cuenta que el grupo de seguridad de red y la ruta definida por el usuario pueden estar presentes en la subred de Application Gateway o en la subred donde están implementadas las máquinas virtuales de la aplicación. De igual forma, la presencia de un DNS personalizado en la red virtual también podría provocar problemas si se utiliza un FQDN para los miembros del grupo de back-end y el servidor DNS configurado por el usuario para la red virtual no lo resuelve correctamente.

### <a name="solution"></a>Solución

Valide la configuración de NSG, UDR y DNS mediante los pasos siguientes:
* Compruebe los grupos de seguridad de red asociados a la subred de Application Gateway. Asegúrese de que la comunicación con el back-end no se bloquea.
* Compruebe la ruta definida por el usuario asociada a la subred de Application Gateway. Asegúrese de que la ruta definida por el usuario no aleja el tráfico de la subred de back-end. Por ejemplo, compruebe el enrutamiento a aplicaciones virtuales de red o las rutas predeterminadas anunciadas en la subred de Application Gateway mediante ExpressRoute/VPN.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzureRmVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Compruebe el grupo de seguridad de red y la ruta efectivos con la máquina virtual de back-end.

```powershell
Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzureRmEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Compruebe la presencia de DNS personalizado en la red virtual. El DNS se puede comprobar examinando los detalles de las propiedades de red virtual en la salida.

```json
Get-AzureRmVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Si está presente, asegúrese de que el servidor DNS pueda resolver correctamente el FQDN de los miembros del grupo de back-end.

## <a name="problems-with-default-health-probe"></a>Problemas con la sonda de estado predeterminada

### <a name="cause"></a>Causa

Los errores 502 también pueden ser indicativos frecuentes de que la sonda de estado predeterminada no puede conectarse con las máquinas virtuales back-end. Cuando se aprovisiona una instancia de Application Gateway, se configura automáticamente una sonda de estado predeterminada para cada BackendAddressPool que utiliza las propiedades de BackendHttpSetting. El usuario no tiene que escribir datos para establecer esta sonda. En concreto, cuando se configura una regla de equilibrio de carga, se crea una asociación entre un elemento BackendHttpSetting y otro BackendAddressPool. Se ha configurado una sonda predeterminada para cada una de estas asociaciones y Puerta de enlace de aplicaciones inicia una conexión de comprobación de estado periódica en cada instancia del elemento BackendAddressPool del puerto especificado en BackendHttpSetting. En la tabla siguiente se enumeran los valores asociados con la sonda de estado predeterminada.

| Propiedad de sondeo | Valor | DESCRIPCIÓN |
| --- | --- | --- |
| Dirección URL de sondeo |http://127.0.0.1/ |Ruta de acceso URL |
| Intervalo |30 |Intervalo de sondeo en segundos |
| Tiempo de espera |30 |Tiempo de espera del sondeo en segundos |
| Umbral incorrecto |3 |Número de reintentos de sondeo. El servidor back-end se marca como inactivo después de que el número de errores de sondeo consecutivos alcanza el umbral incorrecto. |

### <a name="solution"></a>Solución

* Asegúrese de que se ha configurado un sitio predeterminado y de que está escuchando en 127.0.0.1.
* Si BackendHttpSetting especifica un puerto distinto de 80, se debe configurar que el sitio predeterminado escuche en ese puerto.
* La llamada a http://127.0.0.1:puerto debe devolver el código de resultado HTTP 200. dentro del periodo de espera de 30 segundos.
* Asegúrese de que el puerto configurado está abierto y de que no hay ninguna regla de firewall ni grupos de seguridad de red de Azure que bloqueen el tráfico entrante o saliente en dicho puerto.
* Si usa el servicio en la nube o las máquinas virtuales clásicas de Azure con una IP pública o un FQDN, asegúrese de que esté abierto el [punto de conexión](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) correspondiente.
* Si la máquina virtual se configura mediante Azure Resource Manager y la instancia de Puerta de enlace de aplicaciones se implementó fuera de la red virtual, debe establecerse el [grupo de seguridad de red](../virtual-network/virtual-networks-nsg.md) para permitir el acceso en el puerto deseado.

## <a name="problems-with-custom-health-probe"></a>Problemas con la sonda de estado personalizada

### <a name="cause"></a>Causa

Las sondas de estado personalizadas dotan al comportamiento de sondeo predeterminado de mayor flexibilidad. Cuando se usan sondas personalizadas, puede configurar el intervalo de sondeo, la dirección URL y la ruta de acceso a la comprobación, además del número de respuestas erróneas que se aceptarán antes de marcar la instancia del grupo back-end como incorrecta. Se agregan las siguientes propiedades adicionales.

| Propiedad de sondeo | DESCRIPCIÓN |
| --- | --- |
| NOMBRE |Nombre del sondeo. Este nombre se usa para hacer referencia al sondeo en la configuración de HTTP de back-end. |
| Protocolo |Protocolo usado para enviar el sondeo. El sondeo utiliza el protocolo definido en la configuración de HTTP del back-end. |
| Host |Nombre de host para enviar el sondeo. Solo es aplicable cuando se ha configurado un entorno multisitio en Puerta de enlace de aplicaciones. Es diferente al nombre de host de máquina virtual. |
| Ruta de acceso |Ruta de acceso relativa del sondeo. La ruta de acceso válida se inicia desde '/'. La sonda se envía a \<protocolo\>://\<host\>:\<puerto\>\<ruta de acceso\> |
| Intervalo |Intervalo de sondeo en segundos. Es el intervalo de tiempo entre dos sondeos consecutivos. |
| Tiempo de espera |Tiempo de espera del sondeo en segundos. Si no se recibe una respuesta válida dentro del período de tiempo de espera, el sondeo se marca como error. |
| Umbral incorrecto |Número de reintentos de sondeo. El servidor back-end se marca como inactivo después de que el número de errores de sondeo consecutivos alcanza el umbral incorrecto. |

### <a name="solution"></a>Solución

Valide que el sondeo de mantenimiento personalizado esté configurado correctamente según la tabla anterior. Además de realizar los pasos de solución de problemas anteriores, no se olvide tampoco de llevar a cabo estos:

* Asegúrese de que la sonda se ha especificado correctamente según la [guía](application-gateway-create-probe-ps.md).
* Si la instancia de Puerta de enlace de aplicaciones está configurada para un único sitio, de forma predeterminada, el nombre de host debe especificarse como 127.0.0.1, salvo que se configure de otra manera en la sonda personalizada.
* Asegúrese de que una llamada a http://\<host\>:\<puerto\>\<ruta de acceso\> devuelva el código de resultado HTTP 200.
* Compruebe que los valores del intervalo, el tiempo de espera y UnhealtyThreshold estén dentro de rangos aceptables.
* Si utiliza un sondeo HTTPS, asegúrese de que el servidor de back-end no requiera SNI; para ello, configure un certificado de reserva en dicho servidor.

## <a name="request-time-out"></a>Tiempo de espera de solicitud superado

### <a name="cause"></a>Causa

Cuando se recibe una solicitud de usuario, la instancia de Application Gateway aplica las reglas configuradas a la solicitud y la enruta a una instancia del grupo back-end. Espera, durante un intervalo de tiempo que puede configurarse, una respuesta de la instancia back-end. De forma predeterminada, este intervalo es de **30 segundos**. Si la instancia de Application Gateway no recibe una respuesta de una aplicación back-end en este intervalo, la solicitud de usuario generaría un error 502.

### <a name="solution"></a>Solución

El servicio Application Gateway permite a los usuarios configurar esta opción mediante BackendHttpSetting que, posteriormente, puede aplicarse a diferentes grupos. Estos grupos de back-end pueden tener distintos elementos BackendHttpSetting y, por tanto, diferentes valores configurados para el tiempo de solicitud superado.

```powershell
    New-AzureRmApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>Valor de BackendAddressPool vacío

### <a name="cause"></a>Causa

Si la instancia de Application Gateway no tiene máquinas virtuales ni un conjunto de escalas de máquina virtual configurado en el grupo de direcciones de back-end, no se pueden enrutar solicitudes de clientes y se genera un error de puerta de enlace incorrecta.

### <a name="solution"></a>Solución

Asegúrese de que el grupo de direcciones back-end no está vacío. Puede hacerlo mediante PowerShell, la CLI o el Portal.

```powershell
Get-AzureRmApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

El resultado del cmdlet anterior debe contener un grupo de direcciones back-end que no esté vacío. A continuación, se muestra un ejemplo en el que se devuelven dos grupos que están configurados con direcciones IP o FQDN de máquinas virtuales de back-end. El estado de aprovisionamiento de BackendAddressPool debe ser "Correcto".

BackendAddressPoolsText:

```json
[{
    "BackendAddresses": [{
        "ipAddress": "10.0.0.10",
        "ipAddress": "10.0.0.11"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool01",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
}, {
    "BackendAddresses": [{
        "Fqdn": "xyx.cloudapp.net",
        "Fqdn": "abc.cloudapp.net"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool02",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
}]
```

## <a name="unhealthy-instances-in-backendaddresspool"></a>Instancias incorrectas en BackendAddressPool

### <a name="cause"></a>Causa

Si todas las instancias de BackendAddressPool son incorrectas, el servicio Application Gateway no tendría ningún back-end al que enrutar la solicitud de usuario. También podría pasar cuando las instancias back-end con correctas, pero no tienen implementada la aplicación necesaria.

### <a name="solution"></a>Solución

Asegúrese de que las instancias son correctas y de que la aplicación está configurada de la forma adecuada. Compruebe si las instancias back-end pueden responder a un ping de otra máquina virtual de la misma red virtual. Si se configura con un punto de conexión público, asegúrese de que se pueda utilizar una solicitud de explorador a la aplicación web.

## <a name="next-steps"></a>pasos siguientes

Si los pasos anteriores no resuelven el problema, abra una [incidencia de soporte técnico](https://azure.microsoft.com/support/options/).

