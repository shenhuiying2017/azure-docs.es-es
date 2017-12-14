---
title: "Configuración de un firewall de aplicaciones web: Azure Application Gateway | Microsoft Docs"
description: "Este artículo contiene instrucciones acerca de cómo comenzar a usar el firewall de aplicaciones web en una puerta de enlace de aplicaciones nueva o existente."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: davidmu
ms.openlocfilehash: e60bfc89378569b154f4f973d1dceb683fa58482
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="configure-a-web-application-firewall-on-a-new-or-existing-application-gateway-with-azure-cli"></a>Configuración del firewall de aplicaciones web en una puerta de enlace de aplicaciones nueva o existente con la CLI de Azure

> [!div class="op_single_selector"]
> * [Portal de Azure](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [CLI de Azure](application-gateway-web-application-firewall-cli.md)

Aprenda a crear una puerta de enlace de aplicaciones web habilitada para un firewall de aplicaciones web (WAF). Descubra también cómo puede agregar un firewall de aplicaciones web a una puerta de enlace de aplicaciones existente.

El firewall de aplicaciones web de Azure Application Gateway protege las aplicaciones web de ataques web comunes, como inyección de código SQL, ataques de scripts entre sitios y secuestros de sesiones.

 Application Gateway es un equilibrador de carga de nivel 7. Proporciona solicitudes HTTP de enrutamiento de rendimiento y conmutación por error entre distintos servidores, tanto en la nube como en entornos locales. Application Gateway cuenta con numerosas características de controlador de entrega de aplicaciones (ADC):

 * Equilibrio de carga HTTP 
 * Afinidad de sesión basada en cookies 
 * Descarga de Capa de sockets seguros (SSL) 
 * Sondeos de estado personalizados 
 * Compatibilidad con la funcionalidad de multisitio
 
 Para ver una lista completa de las características admitidas, consulte [Introducción a Application Gateway](application-gateway-introduction.md).

En este artículo se explica el proceso de [incorporación de un firewall de aplicaciones web en una puerta de enlace de aplicaciones existente](#add-web-application-firewall-to-an-existing-application-gateway). También se explica el proceso de [creación de una puerta de enlace de aplicaciones que utiliza un firewall de aplicaciones web](#create-an-application-gateway-with-web-application-firewall).

![Imagen de escenario][scenario]

## <a name="prerequisite-install-the-azure-cli-20"></a>Requisito previo: instalar la CLI de Azure 2.0

Para seguir los pasos de este artículo, es preciso [instalar la interfaz de la línea de comandos de Azure (CLI de Azure) para Mac, Linux y Windows](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="waf-configuration-differences"></a>Diferencias de configuración de WAF

Si ha leído este artículo sobre la [creación de una puerta de enlace de aplicaciones con la CLI de Azure](application-gateway-create-gateway-cli.md), sabrá qué opciones de la SKU deben configurarse al crear una puerta de enlace de aplicaciones. El WAF proporciona una configuración adicional que se puede definir al configurar la SKU en una puerta de enlace de aplicaciones. No hay ningún otro cambio que deba realizar en la puerta de enlace de aplicaciones propiamente dicha.

| **Configuración** | **Detalles**
|---|---|
|**SKU** |Una puerta de enlace de aplicaciones normal sin un firewall de aplicaciones web admite los tamaños **Standard\_Small**, **Standard\_Medium** y **Standard\_Large**. Con la introducción de un firewall de aplicaciones web, hay dos SKU adicionales: **WAF\_Medium** y **WAF\_Large**. WAF no se admite en puertas de enlace de aplicaciones de pequeño tamaño.|
|**Modo** | Esta opción determina el modo del firewall de aplicaciones web. Los valores permitidos son **Detección** y **Prevención**. Cuando el WAF está configurado en modo **Detección**, todas las amenazas se guardan en un archivo de registro. En el modo **Prevención**, los eventos también se registran, pero el atacante recibe una respuesta de tipo "403-No autorizado" de la puerta de enlace de aplicaciones.|

## <a name="add-a-web-application-firewall-to-an-existing-application-gateway"></a>Incorporación de un firewall de aplicaciones web a una puerta de enlace de aplicaciones existente

El siguiente comando cambia una puerta de enlace de aplicaciones estándar existente por una puerta de enlace de aplicaciones habilitadas para WAF:

```azurecli-interactive
#!/bin/bash

az network application-gateway waf-config set \
  --enabled true \
  --firewall-mode Prevention \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"
```

Este comando actualiza la puerta de enlace de aplicaciones con un WAF. Para aprender a consultar los registros de la puerta de enlace de aplicaciones, consulte [Diagnósticos de Application Gateway](application-gateway-diagnostics.md). Debido a la naturaleza de la seguridad de WAF, los registros deben revisarse periódicamente para entender la posición de seguridad de las aplicaciones web.

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>Creación de una puerta de enlace de aplicaciones con un firewall de aplicaciones web

El comando siguiente permite crear una puerta de enlace de aplicaciones con un WAF:

```azurecli-interactive
#!/bin/bash

az network application-gateway create \
  --name "AdatumAppGateway2" \
  --location "eastus" \
  --resource-group "AdatumAppGatewayRG" \
  --vnet-name "AdatumAppGatewayVNET2" \
  --vnet-address-prefix "10.0.0.0/16" \
  --subnet "Appgatewaysubnet2" \
  --subnet-address-prefix "10.0.0.0/28" \
 --servers "10.0.0.5 10.0.0.4" \
  --capacity 2 
  --sku "WAF_Medium" \
  --http-settings-cookie-based-affinity "Enabled" \
  --http-settings-protocol "Http" \
  --frontend-port "80" \
  --routing-rule-type "Basic" \
  --http-settings-port "80" \
  --public-ip-address "pip2" \
  --public-ip-address-allocation "dynamic" \
  --tags "cli[2] owner[administrator]"
```

> [!NOTE]
> Las puertas de enlace de aplicaciones creadas con la configuración básica de WAF se configuran con CRS 3.0 como medida de protección.

## <a name="get-an-application-gateway-dns-name"></a>Obtención del nombre DNS de una puerta de enlace de aplicaciones

Una vez creada la puerta de enlace, el siguiente paso consiste en configurar el front-end para la comunicación. Si se utiliza una dirección IP pública, la puerta de enlace de aplicaciones necesita un nombre DNS asignado dinámicamente (estos nombres no son descriptivos). Para asegurarse de que los usuarios finales pueden llegar a la puerta de enlace de aplicaciones, utilice un registro CNAME que apunte al punto de conexión público de la puerta de enlace de aplicaciones. Para más información, consulte [Configuración de un nombre de dominio personalizado para un servicio en la nube de Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Para configurar un registro CNAME, recupere los datos de la puerta de enlace de aplicaciones y su nombre DNS o dirección IP asociados utilizando el elemento PublicIPAddress asociado a la puerta de enlace de aplicaciones. Utilice el nombre DNS de la puerta de enlace de aplicaciones para crear un registro CNAME, que hace que las dos aplicaciones web apunten a este nombre DNS. No es recomendable utilizar registros de tipo A, ya que la VIP podría cambiar al reiniciarse la puerta de enlace de aplicaciones.

```azurecli-interactive
#!/bin/bash

az network public-ip show \
  --name pip2 \
  --resource-group "AdatumAppGatewayRG"
```

```
{
  "dnsSettings": {
    "domainNameLabel": null,
    "fqdn": "8c786058-96d4-4f3e-bb41-660860ceae4c.cloudapp.net",
    "reverseFqdn": null
  },
  "etag": "W/\"3b0ac031-01f0-4860-b572-e3c25e0c57ad\"",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/publicIPAddresses/pip2",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "40.121.167.250",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway2/frontendIPConfigurations/appGatewayFrontendIP",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "AdatumAppGatewayRG",
    "subnet": null
  },
  "location": "eastus",
  "name": "pip2",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "AdatumAppGatewayRG",
  "resourceGuid": "3c30d310-c543-4e9d-9c72-bbacd7fe9b05",
  "tags": {
    "cli[2] owner[administrator]": ""
  },
  "type": "Microsoft.Network/publicIPAddresses"
}
```

## <a name="next-steps"></a>Pasos siguientes

Para aprender a personalizar las reglas del WAF, consulte [Personalización de reglas de firewall de aplicaciones web mediante la CLI de Azure 2.0](application-gateway-customize-waf-rules-cli.md).

[scenario]: ./media/application-gateway-web-application-firewall-cli/scenario.png
