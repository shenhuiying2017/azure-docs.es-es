---
title: Compatibilidad de WebSocket en Application Gateway | Microsoft Docs
description: "En esta página se proporciona información general sobre la compatibilidad de Application Gateway con WebSocket."
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: 8968dac1-e9bc-4fa1-8415-96decacab83f
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: amsriva
ms.openlocfilehash: 75b06ddd02da231b7813c609c848c75e42116da5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Introducción a la compatibilidad de WebSocket en Application Gateway

Application Gateway proporciona una compatibilidad nativa con WebSocket en todas las puertas de enlace, con independencia de su tamaño. No hay ninguna opción de configuración que permita al usuario habilitar o deshabilitar la compatibilidad con WebSocket. 

El protocolo WebSocket, estándar en [RFC6455](https://tools.ietf.org/html/rfc6455) , permite una comunicación dúplex completa entre un servidor y un cliente a través de una conexión TCP de larga duración. Esta característica permite una comunicación más interactiva entre el servidor web y el cliente, que puede ser bidireccional sin necesidad de realizar sondeos como en las implementaciones basadas en HTTP. A diferencia de HTTP, WebSocket tiene poca sobrecarga y puede reutilizar la misma conexión TCP para varias solicitudes y respuestas, lo que conlleva un uso más eficaz de los recursos. Los protocolos WebSocket están diseñados para utilizarse a través de los puertos HTTP tradicionales 80 y 443.

Puede seguir usando una escucha HTTP estándar en los puertos 80 o 443 para recibir tráfico de WebSocket. Después, el tráfico de WebSocket se dirige al servidor back-end con este protocolo habilitado utilizando el grupo back-end adecuado según lo especificado en las reglas de Application Gateway. El servidor back-end debe responder a los sondeos de la puerta de enlace de aplicaciones, que se describen en la sección de [información general sobre el sondeo de estado](application-gateway-probe-overview.md) . Los sondeos del estado de Application Gateway son solo HTTP o HTTPS. Cada servidor de back-end debe responder a los sondeos HTTP de Application Gateway para enrutar el tráfico de WebSocket al servidor.

## <a name="listener-configuration-element"></a>Elemento de configuración de agente de escucha

Una escucha HTTP existente se puede utilizar para admitir tráfico de WebSocket. A continuación, se muestra un fragmento de código de un elemento httpListeners de un archivo de plantilla de ejemplo. Necesitaría los agentes de escucha de HTTP y HTTPS para admitir WebSocket y proteger el tráfico procedente de este protocolo. De forma similar, puede usar el [portal](application-gateway-create-gateway-portal.md) o [PowerShell](application-gateway-create-gateway-arm.md) para crear una puerta de enlace de aplicaciones con agentes de escucha en el puerto 80 o 443, con el fin de permitir el tráfico de WebSocket.

```json
"httpListeners": [
        {
            "name": "appGatewayHttpsListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/DefaultFrontendPublicIP"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort443'"
                },
                "Protocol": "Https",
                "SslCertificate": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/sslCertificates/appGatewaySslCert1'"
                },
            }
        },
        {
            "name": "appGatewayHttpListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/appGatewayFrontendIP'"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort80'"
                },
                "Protocol": "Http",
            }
        }
    ],
```

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>Configuración de reglas de enrutamiento, BackendHttpSetting y BackendAddressPool

BackendAddressPool se usa para definir un grupo back-end con servidores con WebSocket habilitado. backendHttpSetting se define con un puerto de back-end 80 y 443. Las propiedades de la afinidad basada en cookies y requestTimeouts no son pertinentes para el tráfico de WebSocket. No se requiere ningún cambio en la regla de enrutamiento, se usa "Básico" para vincular el agente de escucha adecuado al grupo de direcciones back-end correspondiente. 

```json
"requestRoutingRules": [{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpsListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}, {
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }

    }
}]
```

## <a name="websocket-enabled-backend"></a>Back-end con WebSocket habilitado

El back-end debe tener un servidor web HTTP o HTTPS que se esté ejecutando en el puerto configurado (normalmente, el 80 o 443) para que WebSocket funcione. Este requisito se debe a que el protocolo WebSocket necesita que el protocolo de enlace inicial sea HTTP con la actualización a protocolo WebSocket como campo de encabezado. A continuación, se muestra un ejemplo de un encabezado:

```
    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: http://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

Otro de los motivos es que el sondeo del estado del back-end de la puerta de enlace de aplicaciones solo admite los protocolos HTTP y HTTPS. Si el servidor back-end no responde a sondeos HTTP o HTTPS, se saca del grupo de back-end.

## <a name="next-steps"></a>Pasos siguientes

Cuando haya terminado de leer la información sobre compatibilidad con WebSocket, vaya al artículo sobre [cómo crear una puerta de enlace de aplicaciones](application-gateway-create-gateway.md) para empezar a trabajar con una aplicación web con WebSocket habilitado.

