---
title: "Diagnóstico de la conectividad local a través de VPN Gateway con Azure Network Watcher | Microsoft Docs"
description: "En este artículo se describe cómo diagnosticar la conectividad local a través de VPN Gateway con la solución de problemas de recursos de Azure Network Watcher."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: e51d31035a8b05238ef0f8d13dd6b6c3f9ad02e8
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>Diagnóstico de la conectividad local a través de puertas de enlace de VPN

Azure VPN Gateway le permite crear soluciones híbridas que dan soluciones a la necesidad de una conexión segura entre su red local y la red virtual de Azure. Como sus necesidades son únicas, también lo es la elección del dispositivo VPN local. Azure admite actualmente [varios dispositivos VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable) que se validan constantemente en asociación con los proveedores de los dispositivos. Revise los valores de configuración específicos del dispositivo antes de configurar el dispositivo VPN local. Igualmente, Azure VPN Gateway está configurado con un conjunto de [parámetros de IPsec admitidos](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) que se utilizan para establecer conexiones. Actualmente no hay ninguna manera de especificar o seleccionar una combinación específica de parámetros de IPsec desde Azure VPN Gateway. Para establecer una conexión correcta entre la red local y Azure, la configuración del dispositivo VPN local debe estar de acuerdo con los parámetros de IPsec prescritos por Azure VPN Gateway. Si la configuración es incorrecta, se produce una pérdida de conectividad y además, hasta ahora, solucionar estos problemas no era algo trivial y normalmente se tardaban horas en identificar y corregir el problema.

Con la característica de solución de problemas de Azure Network Watcher, es posible diagnosticar cualquier problema de la puerta de enlace y las conexiones y, en cuestión de minutos, disponer de información suficiente para tomar una decisión informada para rectificar el problema.

## <a name="scenario"></a>Escenario

Quiere configurar una conexión de sitio a sitio entre Azure y la red local con FortiGate como la instancia de VPN Gateway local. Para lograr este escenario, necesitará la siguiente configuración:

1. Puerta de enlace de red virtual: la instancia de VPN Gateway en Azure
1. Puerta de enlace de red local: la representación de la[ instancia de VPN Gateway local (FortiGate](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway)) en la nube de Azure
1. Conexión de sitio a sitio (basada en rutas): [conexión entre la puerta de enlace de VPN y el enrutador local](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#createconnection)
1. [Configuración de FortiGate](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

Encontrará instrucciones paso a paso para definir una configuración de sitio a sitio si visita: [Creación de una red virtual con una conexión de sitio a sitio mediante Azure Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

Uno de los pasos de configuración más importantes es la configuración de los parámetros de comunicación de IPsec, ya que una configuración incorrecta conlleva una pérdida de conectividad entre la red local y Azure. Actualmente, las instancias de Azure VPN Gateway están configuradas para admitir los siguientes parámetros de IPsec durante la fase 1. Tenga en cuenta que, como ya se mencionó anteriormente, esta configuración no se puede modificar.  Como puede ver en la tabla siguiente, los algoritmos de cifrado admitidos por Azure VPN Gateway son AES256 y AES128 y 3DES.

### <a name="ike-phase-1-setup"></a>Configuración de la fase 1 de IKE

| **Propiedad** | **PolicyBased** | **Puerta de enlace de VPN de alto rendimiento o estándar y RouteBased** |
| --- | --- | --- |
| Versión de IKE |IKEv1 |IKEv2 |
| Grupo Diffie-Hellman |Grupo 2 (1024 bits) |Grupo 2 (1024 bits) |
| Método de autenticación |Clave previamente compartida |Clave previamente compartida |
| Algoritmos de cifrado |AES256 AES128 3DES |AES256 3DES |
| Algoritmo hash |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Vida útil (tiempo) de la asociación de seguridad (SA) de la fase 1 |28.800 segundos |10.800 segundos |

Como usuario, se le pedirá que configure FortiGate. Puede encontrar un ejemplo de configuración en [GitHub](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt). Sin saberlo, ya configuró FortiGate para que use SHA-512 como algoritmo hash. Como este algoritmo no es un algoritmo compatible con las conexiones basadas en directivas, la conexión VPN funciona.

Estos problemas son difíciles de solucionar y las causas principales suelen ser poco intuitivas. En este caso puede abrir un vale de soporte técnico para obtener ayuda acerca de cómo resolver el problema. Pero con la API de solución de problemas de Azure Network Watcher puede identificar estos problemas por su cuenta.

## <a name="troubleshooting-using-azure-network-watcher"></a>Solución de problemas mediante Azure Network Watcher

Para diagnosticar la conexión, conéctese a Azure PowerShell e inicie el cmdlet `Start-AzureRmNetworkWatcherResourceTroubleshooting`. Puede encontrar los detalles sobre el uso de este cmdlet en [Solución de problemas de las conexiones y la puerta de enlace de Virtual Network mediante PowerShell](network-watcher-troubleshoot-manage-powershell.md). Este cmdlet puede tardar varios minutos en completarse.

Una vez completado el cmdlet, puede navegar a la ubicación de almacenamiento especificada en el cmdlet para obtener información detallada sobre el problema y los registros. Azure Network Watcher crea una carpeta zip que contiene los archivos de registro siguientes:

![1][1]

Abra el archivo denominado IKEErrors.txt que muestra el siguiente error que indica un problema con una configuración incorrecta de IKE local.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

Puede obtener información detallada sobre el error a partir del archivo Scrubbed-wfpdiag.txt, ya que en este caso menciona que se ha producido un error `ERROR_IPSEC_IKE_POLICY_MATCH` que hace que la conexión no funcione correctamente.

Otro error de configuración común es la especificación de claves compartidas incorrectas. Si ha especificado diferentes claves compartidas en el ejemplo anterior, el archivo IKEErrors.txt mostrará el siguiente error: `Error: Authentication failed. Check shared key`.

La característica de solución de problemas de Azure Network Watcher le permite diagnosticar y solucionar problemas de VPN Gateway y problemas de conexión con la facilidad de un simple cmdlet de PowerShell. En la actualidad se admite el diagnóstico de las siguientes condiciones y en el futuro se agregarán más.

### <a name="gateway"></a>Puerta de enlace

| Tipo de error | Motivo | Registro|
|---|---|---|
| NoFault | Cuando no se detecta ningún error. |Sí|
| GatewayNotFound | No se encuentra la puerta de enlace o no está aprovisionada. |No|
| PlannedMaintenance |  La instancia de puerta de enlace está en mantenimiento.  |No|
| UserDrivenUpdate | Cuando hay una actualización del usuario en curso. Podría tratarse de una operación de cambio de tamaño. | No |
| VipUnResponsive | No se puede alcanzar la instancia principal de la puerta de enlace. Esto sucede cuando se produce un error en el sondeo de estado. | No |
| PlatformInActive | Hay un problema con la plataforma. | No|
| ServiceNotRunning | No se está ejecutando el servicio subyacente. | No|
| NoConnectionsFoundForGateway | No existe ninguna conexión en la puerta de enlace. Esto es solo una advertencia.| No|
| ConnectionsNotConnected | No está conectada ninguna de las conexiones. Esto es solo una advertencia.| Sí|
| GatewayCPUUsageExceeded | El uso de CPU de la puerta de enlace actual es >95 %. | Sí |

### <a name="connection"></a>Conexión

| Tipo de error | Motivo | Registro|
|---|---|---|
| NoFault | Cuando no se detecta ningún error. |Sí|
| GatewayNotFound | No se encuentra la puerta de enlace o no está aprovisionada. |No|
| PlannedMaintenance | La instancia de puerta de enlace está en mantenimiento.  |No|
| UserDrivenUpdate | Cuando hay una actualización del usuario en curso. Podría tratarse de una operación de cambio de tamaño.  | No |
| VipUnResponsive | No se puede alcanzar la instancia principal de la puerta de enlace. Sucede cuando se produce un error en el sondeo de estado. | No |
| ConnectionEntityNotFound | Falta una configuración de conexión. | No |
| ConnectionIsMarkedDisconnected | La conexión está marcada como "desconectada". |No|
| ConnectionNotConfiguredOnGateway | El servicio subyacente no tiene configurada la conexión. | Sí |
| ConnectionMarkedStandy | El servicio subyacente está marcado como en espera activa.| Sí|
| Autenticación | Error de coincidencia de clave previamente compartida. | Sí|
| PeerReachability | La puerta de enlace del mismo nivel no está accesible. | Sí|
| IkePolicyMismatch | La puerta de enlace del mismo nivel tiene directivas IKE que no son compatibles con Azure. | Sí|
| Error de WfpParse | Se produjo un error al analizar el registro de WFP. |Sí|

## <a name="next-steps"></a>Pasos siguientes

Aprenda a comprobar la conectividad de VPN Gateway con PowerShell y Azure Automation visitando [Supervisión de las puertas de enlace de VPN con la solución de problemas de Azure Network Watcher](network-watcher-monitor-with-azure-automation.md)

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png
