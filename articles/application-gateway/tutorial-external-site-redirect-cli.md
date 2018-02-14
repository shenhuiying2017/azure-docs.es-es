---
title: "Creación de una puerta de enlace de aplicaciones con el redireccionamiento del tráfico externo mediante la CLI de Azure | Microsoft Docs"
description: "Obtenga información sobre cómo crear una puerta de enlace de aplicaciones que redirija el tráfico web interno al grupo adecuado mediante la CLI de Azure."
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: davidmu
ms.openlocfilehash: fed23c7138516b3af8af170383948b14c49a61a9
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/03/2018
---
# <a name="create-an-application-gateway-with-external-redirection-using-the-azure-cli"></a>Creación de una puerta de enlace de aplicaciones con redireccionamiento externo mediante la CLI de Azure

Puede usar la CLI de Azure para configurar el [redireccionamiento del tráfico web](application-gateway-multi-site-overview.md) al crear una [puerta de enlace de aplicaciones](application-gateway-introduction.md). En este tutorial, va a configurar un agente de escucha y una regla que redirigirán el tráfico web que llega a la puerta de enlace de aplicaciones de sitio externo.

En este artículo, aprenderá a:

> [!div class="checklist"]
> * Configuración de la red
> * Crear un agente de escucha y una regla de redireccionamiento
> * Creación de una puerta de enlace de aplicaciones

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para esta guía de inicio rápido es preciso que ejecute la CLI de Azure versión 2.0.4 o posterior. Para encontrar la versión, ejecute `az --version`. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Para crear un grupo de recursos, use [az group create](/cli/azure/group#create).

En el ejemplo siguiente, se crea un grupo de recursos llamado *myResourceGroupAG* en la ubicación *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Crear recursos de red 

Cree la red virtual llamada *myVNet* y la subred llamada *myAGSubnet* mediante [az network vnet create](/cli/azure/network/vnet#az_net). Cree la dirección IP pública llamada *myAGPublicIPAddress* mediante [az network public-ip create](/cli/azure/public-ip#az_network_public_ip_create). Estos recursos se usan para proporcionar conectividad de red a la puerta de enlace de aplicaciones y sus recursos asociados.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway"></a>Creación de una puerta de enlace de aplicaciones

Puede usar [az network application-gateway create](/cli/azure/application-gateway#create) para crear la puerta de enlace de aplicaciones llamada *myAppGateway*. Cuando se crea una puerta de enlace de aplicaciones mediante la CLI de Azure, se especifica información de configuración, como capacidad, SKU y HTTP. La puerta de enlace de aplicaciones se asigna a los elementos *myAGSubnet* y *myPublicIPSddress* creados anteriormente. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 8080 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

La puerta de enlace de aplicaciones puede tardar varios minutos en crearse. Después de crear la puerta de enlace de aplicaciones, puede ver estas nuevas características de ella:

- *appGatewayBackendPool*: una puerta de enlace de aplicaciones debe tener al menos un grupo de direcciones de servidores back-end.
- *appGatewayBackendHttpSettings*: especifica que se use el puerto 80 y un protocolo HTTP para la comunicación.
- *appGatewayHttpListener*: agente de escucha predeterminado asociado con *appGatewayBackendPool*.
- *appGatewayFrontendIP*: asigna *myAGPublicIPAddress* a *appGatewayHttpListener*.
- *rule1*: la regla de enrutamiento predeterminada asociada a *appGatewayHttpListener*.

### <a name="add-the-redirection-configuration"></a>Adición de la configuración de redireccionamiento

Agregue la configuración de redireccionamiento que envía el tráfico desde la puerta de enlace de aplicaciones a *bing.com* mediante [az network application-gateway redirect-config create](/cli/azure/network/application-gateway/redirect-config#az_network_application_gateway_redirect_config_create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name myredirect \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Temporary \
  --target-url "http://bing.com"
```

### <a name="add-a-listener-and-routing-rule"></a>Adición de un agente de escucha y una regla de enrutamiento

Se necesita un agente de escucha para habilitar la puerta de enlace de aplicaciones para enrutar el tráfico correctamente. Cree el agente de escucha mediante [az network application-gateway http-listener create](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create) con el puerto de front-end creado mediante [az network application-gateway frontend-port create](/cli/azure/application-gateway#az_network_application_gateway_frontend_port_create). Se necesita una regla para que el agente de escucha sepa dónde enviar el tráfico entrante. Cree una regla básica llamada *redirectRule* mediante [az network application-gateway rule create](/cli/azure/application-gateway#az_network_application_gateway_rule_create) con la configuración de redireccionamiento.

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name redirectPort
az network application-gateway http-listener create \
  --name redirectListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port redirectPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectListener \
  --rule-type Basic \
  --redirect-config myredirect
```

## <a name="test-the-application-gateway"></a>Prueba de la puerta de enlace de aplicaciones

Para obtener la dirección IP pública de la puerta de enlace de aplicaciones, puede usar [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Copie la dirección IP pública y péguela en la barra de direcciones del explorador.

En el explorador debe aparecer *bing.com*.

## <a name="next-steps"></a>pasos siguientes

En este tutorial aprendió lo siguiente:

> * Configuración de la red
> * Crear un agente de escucha y una regla de redireccionamiento
> * Creación de una puerta de enlace de aplicaciones

> [!div class="nextstepaction"]
> [Obtenga más información sobre lo que puede hacer con la puerta de enlace de aplicaciones](./application-gateway-introduction.md).