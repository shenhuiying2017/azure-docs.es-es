---
title: "Configuración del firewall de aplicaciones web en una instancia de Azure Application Gateway | Microsoft Docs"
description: "En este artículo se proporcionan instrucciones sobre cómo comenzar a usar el firewall de aplicaciones web en una puerta de enlace de aplicaciones nueva o existente."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: ac6c629ceaf1a8036643f593ce3d7ef9ea096ef8
ms.contentlocale: es-es
ms.lasthandoff: 08/04/2017

---
# <a name="configure-web-application-firewall-on-a-new-or-existing-application-gateway-with-azure-cli"></a>Configuración del firewall de aplicaciones web en una puerta de enlace de aplicaciones nueva o existente con la CLI de Azure

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [CLI de Azure](application-gateway-web-application-firewall-cli.md)

Aprenda a crear una puerta de enlace de aplicaciones habilitada para un firewall de aplicaciones web o a agregar un firewall de aplicaciones web a una puerta de enlace de aplicaciones existente.

El firewall de aplicaciones web (WAF) de Azure Application Gateway protege las aplicaciones web de ataques web comunes, como inyección de código SQL, ataques de scripts entre sitios y secuestros de sesiones.

Azure Application Gateway es un equilibrador de carga de nivel 7. Proporciona conmutación por error, solicitudes HTTP de enrutamiento de rendimiento entre distintos servidores, independientemente de que se encuentren en la nube o en una implementación local. Application Gateway proporciona numerosas características del controlador de entrega de aplicaciones (ADC), entre las que se incluyen el equilibrio de carga HTTP, la afinidad de sesiones basada en cookies, la descarga SSL (Capa de sockets seguros), los sondeos personalizados sobre el estado, la compatibilidad con multisitio, y muchas más. Para obtener una lista completa de las características admitidas, consulte la [información general sobre Application Gateway](application-gateway-introduction.md).

En el artículo siguiente se muestra cómo [agregar el firewall de aplicaciones web a una puerta de enlace de aplicaciones existente](#add-web-application-firewall-to-an-existing-application-gateway) y [crear una puerta de enlace de aplicaciones que usa el firewall de aplicaciones web](#create-an-application-gateway-with-web-application-firewall).

![imagen de escenario][scenario]

## <a name="prerequisite-install-the-azure-cli-20"></a>Requisito previo: instalar la CLI de Azure 2.0

Para seguir los pasos de este artículo, es preciso [instalar la interfaz de la línea de comandos de Azure para Mac, Linux y Windows (CLI de Azure)](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

## <a name="waf-configuration-differences"></a>Diferencias de configuración de WAF

Si ha leído [Creación de una puerta de enlace de aplicaciones con la CLI de Azure](application-gateway-create-gateway-cli.md), comprenderá los valores de SKU que se deben configurar al crear una puerta de enlace de aplicaciones. WAF proporciona una configuración adicional que se puede definir al configurar la SKU en una puerta de enlace de aplicaciones. No hay ningún otro cambio que deba realizar en la puerta de enlace de aplicaciones propiamente dicha.

| **Configuración** | **Detalles**
|---|---|
|**SKU** |Una puerta de enlace de aplicaciones normal sin WAF admite los tamaños **Standard\_Small**, **Standard\_Medium** y **Standard\_Large**. Con la introducción de WAF, hay dos SKU adicionales, **WAF\_Medium** y **WAF\_Large**. No se admite WAF en puertas de enlace de aplicaciones de pequeño tamaño.|
|**Modo** | Esta configuración es el modo de WAF. Los valores permitidos son **Detección** y **Prevención**. Cuando WAF está configurado en modo de detección, todas las amenazas se almacenan en un archivo de registro. En modo de prevención, los eventos se siguen registrando pero el atacante recibe una respuesta 403 no autorizado de la puerta de enlace de aplicaciones.|

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Adición del firewall de aplicaciones web a una puerta de enlace de aplicaciones existente

El siguiente comando cambia la puerta de enlace de aplicaciones estándar existente a una puerta de enlace de aplicaciones con WAF habilitado.

```azurecli-interactive
#!/bin/bash

az network application-gateway waf-config set \
  --enabled true \
  --firewall-mode Prevention \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"
```

Este comando actualiza la puerta de enlace de aplicaciones con el firewall de aplicaciones web. Consulte [Diagnósticos de Application Gateway](application-gateway-diagnostics.md) para entender cómo ver los registros de la puerta de enlace de aplicaciones. Debido a la naturaleza de la seguridad de WAF, los registros se deben revisar periódicamente para entender la postura de seguridad de las aplicaciones web.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Creación de una puerta de enlace de aplicaciones con el firewall de aplicaciones web

El comando siguiente permite crear una instancia de Application Gateway con firewall de aplicaciones web.

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
> Las puertas de enlace de la aplicación creadas con la configuración de firewall de aplicación web básica se configuran con CRS 3.0 para protección.

## <a name="get-application-gateway-dns-name"></a>Obtención del nombre DNS de una puerta de enlace de aplicaciones

Una vez creada la puerta de enlace, el siguiente paso es configurar el front-end para la comunicación. Cuando se utiliza una dirección IP pública, la puerta de enlace de aplicaciones requiere un nombre DNS asignado dinámicamente, que no es descriptivo. Para asegurarse de que los usuarios finales puedan llegar a la Application Gateway, se puede utilizar un registro CNAME para que apunte al punto de conexión público de la Application Gateway. [Configuración de un nombre de dominio personalizado en Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Para configurar un registro CNAME, recupere los detalles de la puerta de enlace de aplicaciones y su nombre DNS o IP asociados mediante el elemento PublicIPAddress asociado a Application Gateway. El nombre DNS de la puerta de enlace de aplicaciones se debe utilizar para crear un registro CNAME, que apunta las dos aplicaciones web a este nombre DNS. No se recomienda el uso de registros A, ya que la VIP puede cambiar al reiniciarse la puerta de enlace de aplicaciones.

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

Aprenda a personalizar reglas de WAF visitando: [Customize web application firewall rules through the Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md) (Personalización de reglas de firewall de aplicaciones web a través de la CLI de Azure 2.0).

[scenario]: ./media/application-gateway-web-application-firewall-cli/scenario.png

