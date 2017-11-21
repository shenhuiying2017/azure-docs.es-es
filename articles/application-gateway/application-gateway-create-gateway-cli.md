---
title: 'Crear una puerta de enlace de aplicaciones: CLI de Azure 2.0 | Microsoft Docs'
description: Aprenda a crear una puerta de enlace de aplicaciones mediante la CLI de Azure 2.0 en Resource Manager.
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c2f6516e-3805-49ac-826e-776b909a9104
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.openlocfilehash: 9d3732d538f3ed9ecb87247f378a3736692025ca
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="create-an-application-gateway-by-using-the-azure-cli-20"></a>Creación de una puerta de enlace de aplicaciones mediante la CLI de Azure 2.0

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-gateway-portal.md)
> * [PowerShell del Administrador de recursos de Azure](application-gateway-create-gateway-arm.md)
> * [PowerShell clásico de Azure](application-gateway-create-gateway.md)
> * [Plantilla de Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [CLI de Azure 1.0](application-gateway-create-gateway-cli.md)
> * [CLI de Azure 2.0](application-gateway-create-gateway-cli.md)

Azure Application Gateway es una aplicación virtual dedicada que ofrece un controlador de entrega de aplicaciones (ADC) que se ofrece como servicio y que proporciona numerosas funcionalidades de equilibrio de carga de nivel 7 para una aplicación.

## <a name="cli-versions"></a>Versiones de la CLI

Puede crear una puerta de enlace de aplicaciones mediante una de las siguientes versiones de la interfaz de la línea de comandos (CLI):

* [CLI de Azure 1.0](application-gateway-create-gateway-cli-nodejs.md): CLI de Azure para los modelos de implementación clásico y de Azure Resource Manager
* [CLI de Azure 2.0](application-gateway-create-gateway-cli.md): CLI de última generación del modelo de implementación de Resource Manager.

## <a name="prerequisite-install-the-azure-cli-20"></a>Requisito previo: instalar la CLI de Azure 2.0

Para seguir los pasos de este artículo, es preciso [instalar la CLI de Azure para macOS, Linux y Windows](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

> [!NOTE]
> Necesita una cuenta de Azure para crear una puerta de enlace de aplicaciones. Si no tiene una, suscríbase para [una prueba gratuita](../active-directory/sign-up-organization.md).

## <a name="scenario"></a>Escenario

En este escenario aprenderá a crear una puerta de enlace de aplicaciones mediante Azure Portal.

En este escenario:

* Creará una puerta de enlace de aplicaciones media con dos instancias.
* Creará una red virtual denominada AdatumAppGatewayVNET con un bloque CIDR reservado de 10.0.0.0/16.
* Creará una subred denominada Appgatewaysubnet que usa 10.0.0.0/28 como bloque CIDR.

> [!NOTE]
> La configuración adicional de la puerta de enlace de aplicaciones, como los sondeos personalizados sobre el estado, las direcciones del grupo de servidores back-end y las reglas adicionales, tiene lugar después de haberse creado la puerta de enlace de aplicaciones, y no durante la implementación inicial.

## <a name="before-you-begin"></a>Antes de empezar

Las puertas de enlace de aplicaciones necesitan su propia subred. Al crear una red virtual, asegúrese de dejar suficiente espacio de direcciones para varias subredes. Una vez implementada una puerta de enlace de aplicaciones en una subred, solo puede agregar puertas de enlace de aplicaciones adicionales a esa subred.

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Abra el **símbolo del sistema de Microsoft Azure** e inicie sesión:

```azurecli-interactive
az login -u "username"
```

> [!NOTE]
> También puede usar `az login` sin el modificador de inicio de sesión de dispositivo que exigirá que se escriba un código en aka.ms/devicelogin.

Después de escribir el comando anterior recibirá un código. Vaya a https://aka.ms/devicelogin en un explorador para continuar con el proceso de inicio de sesión.

![Cmd que muestra el inicio de sesión del dispositivo][1]

En el explorador, escriba el código que recibió. Se le redirigirá a una página de inicio de sesión.

![Explorador para escribir el código][2]

Escriba el código para iniciar sesión y cierre el explorador para continuar.

![Inicio de sesión correcto][3]

## <a name="create-the-resource-group"></a>Creación del grupo de recursos

Antes de crear la puerta de enlace de aplicaciones, cree un grupo de recursos que la contenga. Use el comando siguiente:

```azurecli-interactive
az group create --name myresourcegroup --location "eastus"
```

## <a name="create-the-application-gateway"></a>Creación de la puerta de enlace de aplicaciones

Use las direcciones IP de servidores back-end para las direcciones IP de servidor back-end. Estos valores pueden ser direcciones IP privadas de la red virtual, direcciones IP públicas o nombres de dominio completos de los servidores back-end. En el ejemplo siguiente se crea una puerta de enlace de aplicaciones con configuraciones adicionales para la configuración de HTTP, puertos y reglas:

```azurecli-interactive
az network application-gateway create \
--name "AdatumAppGateway" \
--location "eastus" \
--resource-group "myresourcegroup" \
--vnet-name "AdatumAppGatewayVNET" \
--vnet-address-prefix "10.0.0.0/16" \
--subnet "Appgatewaysubnet" \
--subnet-address-prefix "10.0.0.0/28" \
--servers 10.0.0.4 10.0.0.5 \
--capacity 2 \
--sku Standard_Small \
--http-settings-cookie-based-affinity Enabled \
--http-settings-protocol Http \
--frontend-port 80 \
--routing-rule-type Basic \
--http-settings-port 80 \
--public-ip-address "pip2" \
--public-ip-address-allocation "dynamic" \

```

En el ejemplo anterior se muestran numerosas propiedades que no son necesarias durante la creación de una puerta de enlace de aplicaciones. En el siguiente ejemplo de código se crea una puerta de enlace de aplicaciones con la información necesaria:

```azurecli-interactive
az network application-gateway create \
--name "AdatumAppGateway" \
--location "eastus" \
--resource-group "myresourcegroup" \
--vnet-name "AdatumAppGatewayVNET" \
--vnet-address-prefix "10.0.0.0/16" \
--subnet "Appgatewaysubnet" \
--subnet-address-prefix "10.0.0.0/28" \
--servers "10.0.0.5"  \
--public-ip-address pip
```
 
> [!NOTE]
> Para ver una lista de parámetros que se pueden usar durante la creación, ejecute el siguiente comando: `az network application-gateway create --help`.

Con este ejemplo se crea una puerta de enlace de aplicaciones básica con la configuración predeterminada para el agente de escucha, el grupo de servidores back-end, la configuración de HTTP de servidores back-end y las reglas. Esta configuración se puede modificar para adaptarse a la implementación una vez efectuado correctamente el aprovisionamiento.

Si la aplicación web se ha definido con el grupo de servidores back-end en los pasos anteriores, el equilibrio de carga comienza ahora.

## <a name="get-the-application-gateway-dns-name"></a>Obtención del nombre DNS de una puerta de enlace de aplicaciones
Después de crear la puerta de enlace, debe configurar el front-end para la comunicación. Si usa una dirección IP pública, la puerta de enlace de aplicaciones necesita un nombre DNS asignado dinámicamente (estos nombres no son descriptivos). Para garantizar que los usuarios puedan llegar a la puerta de enlace de aplicaciones, use un registro CNAME que apunte al punto de conexión público de la puerta de enlace de aplicaciones. Para más información, consulte [Usar Azure DNS para proporcionar la configuración de un dominio personalizado para un servicio de Azure](../dns/dns-custom-domain.md).

Para configurar un alias, recupere los detalles de la puerta de enlace de aplicaciones y su nombre de IP o DNS asociado mediante el elemento PublicIPAddress asociado a dicha puerta de enlace de aplicaciones. Utilice el nombre DNS de la puerta de enlace de aplicaciones para crear un registro CNAME, que hace que las dos aplicaciones web apunten a este nombre DNS. No use registros A, ya que la IP virtual puede cambiar al reiniciarse la puerta de enlace de aplicaciones.


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

## <a name="delete-all-resources"></a>Eliminación de todos los recursos

Para eliminar todos los recursos creados en este artículo, ejecute el siguiente comando:

```azurecli-interactive
az group delete --name AdatumAppGatewayRG
```
 
## <a name="next-steps"></a>Pasos siguientes

Para aprender a crear sondeos personalizados sobre el estado, vaya a [Creación de un sondeo personalizado para Puerta de enlace de aplicaciones mediante el portal](application-gateway-create-probe-portal.md).

Para aprender a configurar la descarga de SSL y prescindir del costoso descifrado SSL de los servidores web, vea [Configuración de una puerta de enlace de aplicaciones para la descarga de SSL mediante Azure Resource Manager](application-gateway-ssl-arm.md).

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png
