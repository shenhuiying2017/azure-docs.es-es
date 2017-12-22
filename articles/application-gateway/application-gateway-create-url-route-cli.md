---
title: "Creación de una puerta de enlace de aplicaciones con reglas de enrutamiento de direcciones URL y la CLI de Azure 2.0 | Microsoft Docs"
description: "En esta página se proporcionan instrucciones sobre cómo crear y configurar una puerta de enlace de aplicaciones con reglas de enrutamiento de direcciones URL."
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
ms.openlocfilehash: 10d01d5d80e2d111d6b39598eed3612f80162b23
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="create-an-application-gateway-by-using-path-based-routing-with-azure-cli-20"></a>Creación de una puerta de enlace de aplicaciones con enrutamiento basado en rutas de acceso con la CLI de Azure 2.0

> [!div class="op_single_selector"]
> * [Portal de Azure](application-gateway-create-url-route-portal.md)
> * [PowerShell de Azure Resource Manager](application-gateway-create-url-route-arm-ps.md)
> * [CLI de Azure 2.0](application-gateway-create-url-route-cli.md)

Con el enrutamiento basado en ruta de dirección URL puede asociar rutas a partir de la ruta de dirección URL de solicitudes HTTP. Comprueba si hay una ruta a un grupo de servidores back-end configurada para la dirección URL indicada en la puerta de enlace de aplicaciones y, luego, envía el tráfico de red al grupo definido. Un uso habitual del enrutamiento basado en ruta de dirección URL es el equilibrio de carga de las solicitudes de diferentes tipos de contenido entre diferentes grupos de servidores back-end.

Azure Application Gateway usa dos tipos de reglas: básicas y basadas en ruta de dirección URL. El tipo de regla básico proporciona un servicio round robin para los grupos de servidores back-end. Las reglas basadas en ruta de acceso, además de la distribución round robin, también usan el patrón de ruta de acceso de la dirección URL de solicitud al elegir el grupo de servidores back-end adecuado.

## <a name="scenario"></a>Escenario

En el siguiente ejemplo, una puerta de enlace de aplicaciones atiende el tráfico de contoso.com con dos grupos de servidores de back-end: un grupo de servidores predeterminado y un grupo de servidores de imagen.

Las solicitudes para http://contoso.com/image* se enrutan al grupo de servidores de imagen (**imagesBackendPool**). Si el patrón de ruta de acceso no coincide, la puerta de enlace de aplicaciones selecciona el grupo de servidores predeterminado (**appGatewayBackendPool**).

![Ruta de dirección URL](./media/application-gateway-create-url-route-cli/scenario.png)

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Abra el **símbolo del sistema de Microsoft Azure** e inicie sesión:

```azurecli
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

## <a name="add-a-path-based-rule-to-an-existing-application-gateway"></a>Incorporación de una regla basada en ruta de acceso a una puerta de enlace de aplicaciones existente

Con los siguientes pasos se describe cómo agregar una regla basada en ruta de acceso a una puerta de enlace de aplicaciones existente.
### <a name="create-a-new-back-end-pool"></a>Creación de un nuevo grupo de back-end

Configure la opción de la puerta de enlace de aplicaciones **imagesBackendPool** para el tráfico de red de carga equilibrada del grupo de back-end. En este ejemplo, se configuran distintas opciones de configuración para el nuevo grupo de back-end. Cada grupo de servidores back-end puede tener su propia configuración. Las reglas basadas en ruta de acceso usan la configuración de HTTP de los servidores back-end para enrutar el tráfico hacia los miembros del grupo de servidores back-end correctos. Esto determina el protocolo y el puerto empleados cuando se envía tráfico a los miembros del grupo de servidores back-end. La configuración de HTTP de los servidores back-end determina las sesiones basadas en cookies.  Si está habilitada, la afinidad de sesión basada en cookies envía el tráfico al mismo servidor back-end que las solicitudes anteriores para cada paquete.

```azurecli-interactive
az network application-gateway address-pool create \
--gateway-name AdatumAppGateway \
--name imagesBackendPool  \
--resource-group myresourcegroup \
--servers 10.0.0.6 10.0.0.7
```

### <a name="create-a-new-front-end-port-for-an-application-gateway"></a>Crear un puerto front-end de una puerta de enlace de aplicaciones

Un agente de escucha usa el objeto de configuración de puerto front-end para definir en qué puerto escucha la puerta de enlace de aplicaciones el tráfico en el agente de escucha.

```azurecli-interactive
az network application-gateway frontend-port create --port 82 --gateway-name AdatumAppGateway --resource-group myresourcegroup --name port82
```

### <a name="create-a-new-listener"></a>Creación de un nuevo agente de escucha

En este paso se configura el agente de escucha para la dirección IP pública y el puerto que se utiliza para recibir el tráfico de red entrante. En el ejemplo siguiente se toma la configuración de la dirección IP de front-end configurada anteriormente, la configuración del puerto front-end y un protocolo http o https (distinguen mayúsculas de minúsculas). Luego, se configura el agente de escucha. En este ejemplo, el agente escucha el tráfico HTTP en el puerto 82 en la dirección IP pública creada anteriormente en este escenario.

```azurecli-interactive
az network application-gateway http-listener create --name imageListener --frontend-ip appGatewayFrontendIP  --frontend-port port82 --resource-group myresourcegroup --gateway-name AdatumAppGateway
```

### <a name="create-the-url-path-map"></a>Crear la asignación de rutas de direcciones URL

En este paso, se configura la ruta de acceso de dirección URL relativa que usa la puerta de enlace de aplicaciones para definir la asignación entre la ruta de acceso y el grupo de back-end asignados para administrar el tráfico entrante.

> [!IMPORTANT]
> Cada una de las rutas debe comenzar por "/" y el único lugar donde se permite un asterisco es al final. Algunos ejemplos válidos son /xyz, /xyz* o /xyz/*. La cadena que se suministra al comprobador de rutas de acceso no incluye texto después del primer "?" o "#", y esos caracteres no se permiten. 

En el siguiente ejemplo se crea una regla para la ruta de acceso /images/* que enruta el tráfico al back-end **imagesBackendPool**. Esta regla garantiza que el tráfico se enruta al back-end para cada conjunto de direcciones URL. Por ejemplo, http://adatum.com/images/figure1.jpg va a **imagesBackendPool**. Si la ruta de acceso no coincide con ninguna de las reglas de ruta de acceso predefinidas, la configuración de asignación de ruta de acceso de regla también configura un grupo de direcciones de back-end predeterminado. Por ejemplo, http://adatum.com/shoppingcart/test.html irá a **pool1** ya que es el grupo predeterminado del tráfico no coincidente.

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
