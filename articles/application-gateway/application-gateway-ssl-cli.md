---
title: "Configuración de la descarga SSL para Azure Application Gateway mediante la CLI 2.0 de Azure | Microsoft Docs"
description: "Este artículo contiene instrucciones para crear una puerta de enlace de aplicaciones con descarga SSL mediante la CLI de Azure 2.0."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.openlocfilehash: a2c4062db821e39e1af4fa1d54da0121d3993db4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-cli-20"></a>Configuración de una puerta de enlace de aplicaciones para la descarga SSL la CLI de Azure 2.0

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-ssl-portal.md)
> * [PowerShell del Administrador de recursos de Azure](application-gateway-ssl-arm.md)
> * [PowerShell clásico de Azure](application-gateway-ssl.md)
> * [CLI de Azure 2.0](application-gateway-ssl-cli.md)

Azure Application Gateway puede configurarse para terminar la sesión Capa de sockets seguros (SSL) en la puerta de enlace para evitar las costosas tareas de descifrado SSL que tienen lugar en la granja de servidores web. La descarga SSL también simplifica la administración de certificados en el servidor front-end.

## <a name="prerequisite-install-the-azure-cli-20"></a>Requisito previo: instalar la CLI de Azure 2.0

Para seguir los pasos de este artículo, es preciso [instalar la interfaz de la línea de comandos de Azure para Mac, Linux y Windows (CLI de Azure)](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

## <a name="required-components"></a>Componentes necesarios

* **Back-end server pool** (Grupo de servidores back-end): lista de direcciones IP de los servidores back-end. Las direcciones IP de la lista deben pertenecer a la subred de la red virtual o ser una dirección IP pública o una dirección IP virtual (VIP).
* **Configuración del grupo de servidores back-end:** cada grupo tiene una configuración en la que se incluye el puerto, el protocolo y la afinidad basada en cookies. Estos valores están vinculados a un grupo y se aplican a todos los servidores del grupo.
* **Front-end port** (Puerto front-end): este puerto es el puerto público que se abre en la puerta de enlace de aplicaciones. El tráfico llega a este puerto y después se redirige a uno de los servidores back-end.
* **Agente de escucha:** tiene un puerto front-end, un protocolo (Http o Https, que distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se configura la descarga SSL).
* **Regla:** la regla enlaza el agente de escucha y el grupo de servidores back-end y define a qué grupo de servidores back-end se va a dirigir el tráfico cuando llegue a un determinado agente de escucha. Actualmente, solo se admite la regla *básica* . La regla *básica* es la distribución de carga round robin.

**Notas de configuración adicionales**

Para la configuración de certificados SSL, el protocolo de **HttpListener** debería cambiar a *Https* (con distinción entre mayúsculas y minúsculas). Agregue el elemento **SslCertificate** a **HttpListener** con el valor de la variable configurado para el certificado SSL. El puerto front-end debe actualizarse al **443**.

**Para habilitar la afinidad basada en cookies:** puede configurar una puerta de enlace de aplicaciones para asegurarse de que las solicitudes de una sesión de cliente siempre se dirigen a la misma máquina virtual de la granja de servidores web. Para conseguirlo, inserte una cookie de sesión que permita a la puerta de enlace dirigir el tráfico de forma adecuada. Para habilitar la afinidad basada en cookies, establezca **CookieBasedAffinity** en *Habilitado* en el elemento **BackendHttpSettings**.

## <a name="configure-ssl-offload-on-an-existing-application-gateway"></a>Configuración de la descarga de SSL en una puerta de enlace de aplicaciones existente

Escriba los comandos siguientes para configurar la descarga SSL en una puerta de enlace de aplicaciones existente:

```azurecli-interactive
#!/bin/bash

# Create a new front end port to be used for SSL
az network application-gateway frontend-port create \
  --name sslport \
  --port 443 \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Upload the .pfx certificate for SSL offload
az network application-gateway ssl-cert create \
  --name "newcert" \
  --cert-file /home/azureuser/self-signed/AdatumAppGatewayCert.pfx \
  --cert-password P@ssw0rd \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Create a new listener referencing the port and certificate created earlier
az network application-gateway http-listener create \
  --frontend-ip "appGatewayFrontendIP" \
  --frontend-port sslport  \
  --name sslListener \
  --ssl-cert newcert \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Create a new back-end pool to be used
az network application-gateway address-pool create \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG" \
  --name "appGatewayBackendPool2" \
  --servers 10.0.0.7 10.0.0.8

# Create a new back-end HTTP settings using the new probe
az network application-gateway http-settings create \
  --name "settings2" \
  --port 80 \
  --cookie-based-affinity Enabled \
  --protocol "Http" \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Create a new rule linking the listener to the back-end pool
az network application-gateway rule create \
  --name "rule2" \
  --rule-type Basic \
  --http-settings settings2 \
  --http-listener ssllistener \
  --address-pool temp1 \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

```

## <a name="create-an-application-gateway-with-ssl-offload"></a>Creación de una puerta de enlace de aplicaciones con descarga SSL

En el ejemplo siguiente se crea una puerta de enlace de aplicaciones con descarga de SSL. El certificado y la contraseña de este se deben actualizar a una clave privada válida.

```azurecli-interactive
#!/bin/bash

# Creates an application gateway with SSL offload
az network application-gateway create \
  --name "AdatumAppGateway3" \
  --location "eastus" \
  --resource-group "AdatumAppGatewayRG2" \
  --vnet-name "AdatumAppGatewayVNET2" \
  --cert-file /home/azureuser/self-signed/AdatumAppGatewayCert.pfx \
  --cert-password P@ssw0rd \
  --vnet-address-prefix "10.0.0.0/16" \
  --subnet "Appgatewaysubnet" \
  --subnet-address-prefix "10.0.0.0/28" \
  --frontend-port 443 \
  --servers "10.0.0.5 10.0.0.4" \
  --capacity 2 \
  --sku "Standard_Small" \
  --http-settings-cookie-based-affinity "Enabled" \
  --http-settings-protocol "Http" \
  --frontend-port "80" \
  --routing-rule-type "Basic" \
  --http-settings-port "80" \
  --public-ip-address "pip" \
  --public-ip-address-allocation "dynamic"
```

## <a name="get-an-application-gateway-dns-name"></a>Obtención de un nombre DNS de una puerta de enlace de aplicaciones

Una vez que se crea la puerta de enlace, el siguiente paso es configurar el front-end para la comunicación.  La puerta de enlace de aplicaciones requiere un nombre DNS asignado dinámicamente, que no es descriptivo, cuando se utiliza una dirección IP pública. Para asegurarse de que los usuarios finales puedan llegar a la puerta de enlace de aplicaciones, puede utilizar un registro CNAME para que apunte al punto de conexión público de dicha puerta. Para más información, consulte [Configuración de un nombre de dominio personalizado en Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Para configurar un alias, recupere los detalles de la puerta de enlace de aplicaciones y su nombre de IP o DNS asociado mediante el elemento **PublicIPAddress** asociado a dicha puerta. Utilice el nombre DNS de la puerta de enlace de aplicaciones para crear un registro CNAME, que hace que las dos aplicaciones web apunten a este nombre DNS. No se recomienda el uso de registros A, ya que la IP virtual puede cambiar al reiniciarse la puerta de enlace de aplicaciones.


```azurecli-interactive
az network public-ip show --name "pip" --resource-group "AdatumAppGatewayRG"
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

Si quiere configurar una puerta de enlace de aplicaciones para usarla con el equilibrador de carga interno, consulte [Creación de una puerta de enlace de aplicaciones con un equilibrador de carga interno](application-gateway-ilb.md).

Para más información sobre las opciones de equilibrio de carga en general, consulte:

* [Equilibrador de carga de Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
