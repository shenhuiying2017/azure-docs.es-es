---
title: "Creación de una puerta de enlace de aplicaciones mediante reglas de enrutamiento de direcciones URL y la CLI de Azure 2.0 | Microsoft Docs"
description: "En esta página se proporcionan instrucciones para crear y configurar una puerta de enlace de aplicaciones mediante reglas de enrutamiento de direcciones URL"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 958049830d6753ec26635f18f8f8b2fabdec0733
ms.contentlocale: es-es
ms.lasthandoff: 08/04/2017

---
# <a name="create-an-application-gateway-using-path-based-routing-with-azure-cli-20"></a>Creación de una puerta de enlace de aplicaciones mediante enrutamiento basado en rutas de acceso con la CLI de Azure 2.0

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-url-route-portal.md)
> * [PowerShell de Azure Resource Manager](application-gateway-create-url-route-arm-ps.md)
> * [CLI de Azure 2.0](application-gateway-create-url-route-cli.md)

El enrutamiento basado en URL permite asociar rutas en función de la dirección URL de la solicitud HTTP. Comprueba si hay una ruta a un grupo de back-end configurado para la dirección URL de Application Gateway y envía el tráfico de red al grupo de back-end definido. Un uso común del enrutamiento basado en URL es el equilibrio de carga de las solicitudes de diferentes tipos de contenido entre diferentes grupos de servidores de back-end.

El enrutamiento basado en URL incorpora un nuevo tipo de regla en Application Gateway. Application Gateway tiene dos tipos de reglas: básicas y PathBasedRouting. El tipo de regla básica proporciona un servicio round robin mientras que el tipo PathBasedRouting, además de la distribución round robin, también tiene en cuenta el patrón de ruta de acceso de la URL de la solicitud durante la elección del grupo de back-end.

## <a name="scenario"></a>Escenario

En el ejemplo siguiente, Application Gateway atiende el tráfico de contoso.com con dos grupos de servidores de back-end: un grupo de servidores predeterminado y un grupo de servidores de imagen.

Las solicitudes para http://contoso.com/image* se enrutan al grupo de servidores de imagen (imagesBackendPool). Si el patrón de ruta de acceso no coincide, se selecciona un grupo de servidores de forma predeterminada (appGatewayBackendPool).

![ruta de dirección URL](./media/application-gateway-create-url-route-cli/scenario.png)

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Abra el **símbolo del sistema de Microsoft Azure**e inicie sesión. 

```azurecli
az login -u "username"
```

> [!NOTE]
> También puede usar `az login` sin el modificador de inicio de sesión de dispositivo que exigirá que se escriba un código en aka.ms/devicelogin.

Una vez que haya escrito el ejemplo anterior, se proporciona un código. Navegue a https://aka.ms/devicelogin en un explorador para continuar el proceso de inicio de sesión.

![cmd que muestra el inicio de sesión de dispositivos][1]

En el explorador, escriba el código que recibió. Se le redirigirá a una página de inicio de sesión.

![explorador para escribir código][2]

Una vez especificado el código, habrá iniciado sesión; cierre el explorador para continuar con el escenario.

![ha iniciado sesión correctamente][3]

## <a name="add-a-path-based-rule-to-an-existing-application-gateway"></a>Incorporación de una regla basada en ruta de acceso a una puerta de enlace de aplicaciones existente

Cree una puerta de enlace de aplicaciones con una regla de ruta definida.

### <a name="create-a-new-back-end-pool"></a>Creación de un nuevo grupo de back-end

Configure la opción de la puerta de enlace de aplicaciones **imagesBackendPool** para el tráfico de red de carga equilibrada del grupo de back-end. En este ejemplo, se configuran distintas opciones de configuración para el nuevo grupo de back-end. Cada grupo de back-end puede tener su propia configuración de grupo de back-end.  Las reglas usan la configuración de HTTP de back-end para enrutar el tráfico hacia los miembros del grupo back-end correctos. Esto determina el protocolo y el puerto que se usa al enviar tráfico a los miembros del grupo back-end. Las sesiones basadas en cookies también dependen de la configuración de HTTP de back-end.  Si está habilitada, la afinidad de la sesión basada en cookies envía tráfico al mismo servidor back-end como solicitudes anteriores para cada paquete.

```azurecli-interactive
az network application-gateway address-pool create \
--gateway-name AdatumAppGateway \
--name imagesBackendPool  \
--resource-group myresourcegroup \
--servers 10.0.0.6 10.0.0.7
```

### <a name="create-a-new-front-end-port"></a>Creación de un nuevo puerto de front-end

Configuración del puerto front-end de una puerta de enlace de aplicaciones Un agente de escucha usa el objeto de configuración de puerto front-end para definir en qué puerto escucha Application Gateway el tráfico en el agente de escucha.

```azurecli-interactive
az network application-gateway frontend-port create --port 82 --gateway-name AdatumAppGateway --resource-group myresourcegroup --name port82
```

### <a name="create-a-new-listener"></a>Creación de un nuevo agente de escucha

Configuración del agente de escucha En este paso se configura el agente de escucha para la dirección IP pública y el puerto que se utiliza para recibir el tráfico de red entrante. En el ejemplo siguiente se toma la configuración de la dirección IP de front-end y la configuración del puerto front-end que se han configurado anteriormente, junto con un protocolo (http o https), y se configura el agente de escucha. En este ejemplo, el agente escucha el tráfico HTTP en el puerto 82 en la dirección IP pública que se creó anteriormente.

```azurecli-interactive
az network application-gateway http-listener create --name imageListener --frontend-ip appGatewayFrontendIP  --frontend-port port82 --resource-group myresourcegroup --gateway-name AdatumAppGateway
```

### <a name="create-the-url-path-map"></a>Creación de la asignación de rutas de direcciones URL

Configuración de rutas de acceso de reglas de URL para los grupos de back-end En este paso, se configura la ruta de acceso relativa que utiliza la puerta de enlace de aplicaciones para definir la asignación entre la ruta de dirección URL y el grupo de back-end que se asigna para administrar el tráfico entrante.

> [!IMPORTANT]
> Cada una de las rutas debe comenzar por / y el único lugar donde se permite un carácter "\*" es al final. Algunos ejemplos válidos son /xyz, /xyz* o /xyz/*. La cadena que se suministra al comprobador de rutas de acceso no incluye texto después del primer "?" o "#", y esos caracteres no se permiten. 

En el ejemplo siguiente se crea una regla para la ruta de acceso "/images/" que enruta el tráfico al back-end "imagesBackendPool". Esta regla garantiza que el tráfico se enruta al back-end para cada conjunto de direcciones URL. Por ejemplo, http://adatum.com/images/figure1.jpg va a "imagesBackendPool." Si la ruta de acceso no coincide con ninguna de las reglas de ruta de acceso predefinidas, la configuración de asignación de ruta de acceso de regla también configura un grupo de direcciones de back-end predeterminado. Por ejemplo, http://adatum.com/shoppingcart/test.html irá a pool1 ya que es el grupo predeterminado para el tráfico no coincidente.

```azurecli-interactive
az network application-gateway url-path-map create \
--gateway-name AdatumAppGateway \
--name imagespathmap \
--paths /images/* \
--resource-group myresourcegroup2 \
--address-pool imagesBackendPool \
--default-address-pool appGatewayBackendPool \
--default-http-settings appGatewayBackendHttpSettings \
--http-settings appGatewayBackendHttpSettings \
--rule-name images
```

## <a name="next-steps"></a>Pasos siguientes

Si quiere obtener información sobre la descarga de Capa de sockets seguros (SSL), consulte [Configuración de una puerta de enlace de aplicaciones para la descarga SSL mediante Azure Resource Manager](application-gateway-ssl-cli.md).


[scenario]: ./media/application-gateway-create-url-route-cli/scenario.png
[1]: ./media/application-gateway-create-url-route-cli/figure1.png
[2]: ./media/application-gateway-create-url-route-cli/figure2.png
[3]: ./media/application-gateway-create-url-route-cli/figure3.png

