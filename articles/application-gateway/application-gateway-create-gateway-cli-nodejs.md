---
title: "Creación de una instancia de Azure Application Gateway (CLI de Azure 1.0) | Microsoft Docs"
description: Aprenda a crear una puerta de enlace de aplicaciones mediante la CLI de Azure 1.0 en Resource Manager.
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c2f6516e-3805-49ac-826e-776b909a9104
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.openlocfilehash: fe50fb3a7434702101dc5ae7a9dd176a33423119
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Creación de una puerta de enlace de aplicaciones mediante la CLI de Azure

> [!div class="op_single_selector"]
> * [Portal de Azure](application-gateway-create-gateway-portal.md)
> * [PowerShell de Azure Resource Manager](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell](application-gateway-create-gateway.md)
> * [Plantilla de Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [CLI de Azure 1.0](application-gateway-create-gateway-cli.md)
> * [CLI de Azure 2.0](application-gateway-create-gateway-cli.md)
> 
> 

Puerta de enlace de aplicaciones de Azure es un equilibrador de carga de nivel 7. Proporciona conmutación por error, solicitudes HTTP de enrutamiento de rendimiento entre distintos servidores, independientemente de que se encuentren en la nube o en una implementación local. Puerta de enlace de aplicaciones tiene las siguientes características de entrega de aplicaciones: equilibrio de carga HTTP, afinidad de sesiones basada en cookies, descarga SSL (capa de sockets seguros), sondeos personalizados sobre el estado y compatibilidad con sitios múltiples.

## <a name="prerequisite-install-the-azure-cli"></a>Requisito previo: instalar la CLI de Azure

Para seguir los pasos de este artículo, es preciso [instalar la interfaz de la línea de comandos de Azure para Mac, Linux y Windows (CLI de Azure)](../xplat-cli-install.md) e [iniciar sesión en Azure](/cli/azure/authenticate-azure-cli). 

> [!NOTE]
> Si no tiene una cuenta de Azure, necesitará una. Regístrese para [obtener una prueba gratuita aquí](../active-directory/sign-up-organization.md).

## <a name="scenario"></a>Escenario

En este escenario, aprenderá a crear una puerta de enlace de aplicaciones mediante Azure Portal.

En este escenario:

* Creará una puerta de enlace de aplicaciones media con dos instancias.
* Creará una red virtual denominada ContosoVNET con un bloque CIDR reservado de 10.0.0.0/16.
* Creará una subred denominada subnet01 que usa 10.0.0.0/28 como bloque CIDR.

> [!NOTE]
> La configuración adicional de la puerta de enlace de aplicaciones, incluidos los sondeos personalizados sobre el estado, las direcciones del grupo de back-end y las reglas se realiza después de que se configura la puerta de enlace de aplicaciones, no durante la implementación inicial.

## <a name="before-you-begin"></a>Antes de empezar

Azure Application Gateway requiere su propia subred. Al crear una red virtual, asegúrese de dejar suficiente espacio de direcciones para que tenga varias subredes. Una vez que se implementa una puerta de enlace de aplicaciones en una subred adicional solo se pueden agregar a ella puertas de enlace de aplicaciones adicionales.

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Abra el **símbolo del sistema de Microsoft Azure**e inicie sesión. 

```azurecli-interactive
azure login
```

Una vez que haya escrito el ejemplo anterior, se proporciona un código. Navegue a https://aka.ms/devicelogin en un explorador para continuar el proceso de inicio de sesión.

![cmd que muestra el inicio de sesión de dispositivos][1]

En el explorador, escriba el código que recibió. Se le redirigirá a una página de inicio de sesión.

![explorador para escribir código][2]

Una vez especificado el código, habrá iniciado sesión; cierre el explorador para continuar con el escenario.

![ha iniciado sesión correctamente][3]

## <a name="switch-to-resource-manager-mode"></a>Cambie al modo Resource Manager.

```azurecli-interactive
azure config mode arm
```

## <a name="create-the-resource-group"></a>Creación del grupo de recursos

Antes de crear la puerta de enlace de aplicaciones, se creará un grupo de recursos para que pueda contenerla. A continuación, se muestra el comando.

```azurecli-interactive
azure group create \
--name ContosoRG \
--location eastus
```

## <a name="create-a-virtual-network"></a>Crear una red virtual

Una vez creado el grupo de recursos, se crea una red virtual para la puerta de enlace de aplicaciones.  En el ejemplo siguiente, el espacio de direcciones era 10.0.0.0/16 tal como se definió en las notas del escenario anterior.

```azurecli-interactive
azure network vnet create \
--name ContosoVNET \
--address-prefixes 10.0.0.0/16 \
--resource-group ContosoRG \
--location eastus
```

## <a name="create-a-subnet"></a>Creación de una subred

Después de crear la red virtual, se agrega una subred para la puerta de enlace de aplicaciones.  Si piensa utilizar la puerta de enlace de aplicaciones con una aplicación web hospedada en la misma red virtual que la puerta de enlace de aplicaciones asegúrese de dejar suficiente espacio para otra subred.

```azurecli-interactive
azure network vnet subnet create \
--resource-group ContosoRG \
--name subnet01 \
--vnet-name ContosoVNET \
--address-prefix 10.0.0.0/28 
```

## <a name="create-the-application-gateway"></a>Creación de la puerta de enlace de aplicaciones

Una vez que se crean la red virtual y la subred, los requisitos previos de la puerta de enlace de aplicaciones están completos. Además, para el paso siguiente son necesarios un certificado .pfx exportado previamente y la contraseña para el certificado. La direcciones IP que se usan para el back-end son las direcciones IP para el servidor back-end. Estos valores pueden ser direcciones IP privadas de la red virtual, direcciones IP públicas o nombres de dominio completos de los servidores back-end.

```azurecli-interactive
azure network application-gateway create \
--name AdatumAppGateway \
--location eastus \
--resource-group ContosoRG \
--vnet-name ContosoVNET \
--subnet-name subnet01 \
--servers 134.170.185.46,134.170.188.221,134.170.185.50 \
--capacity 2 \
--sku-tier Standard \
--routing-rule-type Basic \
--frontend-port 80 \
--http-settings-cookie-based-affinity Enabled \
--http-settings-port 80 \
--http-settings-protocol http \
--frontend-port http \
--sku-name Standard_Medium
```

> [!NOTE]
> Para ver una lista de parámetros que se pueden usar durante la creación, ejecute el siguiente comando: **azure network application-gateway create --help**.

Con este ejemplo sea crea una puerta de enlace de aplicaciones básica con la configuración predeterminada para el agente de escucha, el grupo de back-end, la configuración de http de back-end y las reglas. Esta configuración se puede modificar para adaptarse a la implementación una vez que el aprovisionamiento sea correcto.
Si ya definió una aplicación web con el grupo de back-end en los pasos anteriores, una vez creada, comienza el equilibrio de carga.

## <a name="next-steps"></a>Pasos siguientes

Para aprender a crear sondeos de estado personalizado, visite [Create a custom probe for Application Gateway by using the portal](application-gateway-create-probe-portal.md)

Para aprender a configurar la descarga de SSL y eliminar la cara descripción de SSL de los servidores web, visite [Configuración de una puerta de enlace de aplicaciones para la descarga SSL mediante Azure Resource Manager](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli-nodejs/scenario.png
[1]: ./media/application-gateway-create-gateway-cli-nodejs/figure1.png
[2]: ./media/application-gateway-create-gateway-cli-nodejs/figure2.png
[3]: ./media/application-gateway-create-gateway-cli-nodejs/figure3.png
