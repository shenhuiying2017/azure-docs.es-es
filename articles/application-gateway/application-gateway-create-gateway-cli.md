---
title: "Creación de una instancia de Azure Application Gateway mediante la CLI de Azure 2.0 | Microsoft Docs"
description: Aprenda a crear una instancia de Application Gateway mediante la CLI de Azure 2.0 en Resource Manager.
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c2f6516e-3805-49ac-826e-776b909a9104
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1481fcb070f383d158c5a6ae32504e498de4a66b
ms.openlocfilehash: 68d3e3ee9b35f2d6d88cde68365cef91d9683462
ms.lasthandoff: 03/01/2017


---
# <a name="create-an-application-gateway-by-using-the-azure-cli-20"></a>Creación de una puerta de enlace de aplicaciones mediante la CLI de Azure 2.0

> [!div class="op_single_selector"]
> * [Portal de Azure](application-gateway-create-gateway-portal.md)
> * [PowerShell del Administrador de recursos de Azure](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell](application-gateway-create-gateway.md)
> * [Plantilla de Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [CLI de Azure 1.0](application-gateway-create-gateway-cli.md)
> * [CLI de Azure 2.0](application-gateway-create-gateway-cli.md)

Puerta de enlace de aplicaciones de Azure es un equilibrador de carga de nivel 7. Proporciona conmutación por error, solicitudes HTTP de enrutamiento de rendimiento entre distintos servidores, independientemente de que se encuentren en la nube o en una implementación local. Puerta de enlace de aplicaciones tiene las siguientes características de entrega de aplicaciones: equilibrio de carga HTTP, afinidad de sesiones basada en cookies, descarga SSL (capa de sockets seguros), sondeos personalizados sobre el estado y compatibilidad con sitios múltiples.

## <a name="cli-versions-to-complete-the-task"></a>Versiones de la CLI para completar la tarea

Puede completar la tarea mediante una de las siguientes versiones de la CLI:

* [CLI de Azure 1.0](application-gateway-create-gateway-cli-nodejs.md): la CLI para los modelos de implementación clásica y de administración de recursos.
* [CLI de Azure 2.0](application-gateway-create-gateway-cli.md): la CLI de última generación para el modelo de implementación de administración de recursos.

## <a name="prerequisite-install-the-azure-cli-20"></a>Requisito previo: instalar la CLI de Azure 2.0

Para seguir los pasos de este artículo, es preciso [instalar la interfaz de la línea de comandos de Azure para Mac, Linux y Windows (CLI de Azure)](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

> [!NOTE]
> Si no tiene una cuenta de Azure, necesitará una. Regístrese para [obtener una prueba gratuita aquí](../active-directory/sign-up-organization.md).

## <a name="scenario"></a>Escenario

En este escenario, aprenderá a crear una puerta de enlace de aplicaciones mediante el Portal de Azure.

En este escenario:

* Creará una puerta de enlace de aplicaciones media con dos instancias.
* Creará una red virtual denominada AdatumAppGatewayVNET con un bloque CIDR reservado de 10.0.0.0/16.
* Creará una subred denominada Appgatewaysubnet que usa 10.0.0.0/28 como bloque CIDR.
* Configurará un certificado para la descarga SSL.

![Escenario de ejemplo][scenario]

> [!NOTE]
> La configuración adicional de la puerta de enlace de aplicaciones, incluidos los sondeos personalizados sobre el estado, las direcciones del grupo de back-end y las reglas se realiza después de que se configura la puerta de enlace de aplicaciones, no durante la implementación inicial.

## <a name="before-you-begin"></a>Antes de empezar

Puerta de enlace de aplicaciones de Azure requiere su propia subred. Al crear una red virtual, asegúrese de dejar suficiente espacio de direcciones para que tenga varias subredes. Una vez que se implementa una puerta de enlace de aplicaciones en una subred adicional solo se pueden agregar a ella puertas de enlace de aplicaciones adicionales.

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Abra el **símbolo del sistema de Microsoft Azure**e inicie sesión. 

```azurecli
az login -u "username"
```

>[NOTA] También puede usar `az login` sin el modificador de inicio de sesión de dispositivo que exigirá que se escriba un código en aka.ms/devicelogin.

Una vez que haya escrito el ejemplo anterior, se proporciona un código. Navegue a https://aka.ms/devicelogin en un explorador para continuar el proceso de inicio de sesión.

![cmd que muestra el inicio de sesión de dispositivos][1]

En el explorador, escriba el código que recibió. Se le redirigirá a una página de inicio de sesión.

![explorador para escribir código][2]

Una vez especificado el código, habrá iniciado sesión; cierre el explorador para continuar con el escenario.

![ha iniciado sesión correctamente][3]

## <a name="create-the-resource-group"></a>Creación del grupo de recursos

Antes de crear la puerta de enlace de aplicaciones, se creará un grupo de recursos para que pueda contenerla. A continuación, se muestra el comando.

```azurecli
az resource group create --name myresourcegroup --location "West US"
```

## <a name="create-a-virtual-network-and-subnet"></a>Creación de una red virtual y una subred

Una vez creado el grupo de recursos, se crea una red virtual para la puerta de enlace de aplicaciones.  En el ejemplo siguiente, el espacio de direcciones era 10.0.0.0/16 tal como se definió para la red virtual, y se usó 10.0.0.0/28 para la subred, como se ha visto en las notas del escenario anterior.

```azurecli
az network vnet create \
--name AdatumAppGatewayVNET \
--address-prefix 10.0.0.0/16 \
--subnet-name Appgatewaysubnet \
--subnet-prefix 10.0.0.0/28 \
--resource-group AdatumAppGateway \
--location eastus
```

## <a name="create-the-application-gateway"></a>Creación de la puerta de enlace de aplicaciones

Una vez que se crean la red virtual y la subred, los requisitos previos de la puerta de enlace de aplicaciones están completos. Además, para el paso siguiente son necesarios un certificado .pfx exportado previamente y la contraseña para el certificado. La direcciones IP que se usan para el back-end son las direcciones IP para el servidor back-end. Estos valores pueden ser direcciones IP privadas de la red virtual, direcciones IP públicas o nombres de dominio completos de los servidores back-end.

```azurecli
az network application-gateway create \
--name AdatumAppGateway \
--location eastus \
--resource-group AdatumAppGatewayRG \
--vnet-name AdatumAppGatewayVNET \
--vnet-address-prefix 10.0.0.0/16 \
--subnet Appgatewaysubnet \
--subnet-address-prefix 10.0.0.0/28 \
--servers 10.0.0.4 10.0.0.5 \
--cert-file /mnt/c/Users/username/Desktop/application-gateway/fabrikam.pfx \
--cert-password P@ssw0rd \
--capacity 2 \
--sku-tier Standard \
--sku-name Standard_Small \
--http-settings-cookie-based-affinity Enabled \
--http-settings-protocol Http \
--frontend-port 443 \
--routing-rule-type Basic \
--http-settings-port 80

```

> [!NOTE]
> Para ver una lista de parámetros que se pueden usar durante la creación, ejecute el siguiente comando: **az network application-gateway create --help**.

Con este ejemplo sea crea una puerta de enlace de aplicaciones básica con la configuración predeterminada para el agente de escucha, el grupo de back-end, la configuración de http de back-end y las reglas. También configura la descarga SSL. Esta configuración se puede modificar para adaptarse a la implementación una vez que el aprovisionamiento sea correcto.
Si ya definió una aplicación web con el grupo de back-end en los pasos anteriores, una vez creada, comienza el equilibrio de carga.

## <a name="next-steps"></a>Pasos siguientes

Para aprender a crear sondeos de estado personalizado, visite [Create a custom probe for Application Gateway by using the portal](application-gateway-create-probe-portal.md)

Para aprender a configurar la descarga de SSL y eliminar la cara descripción de SSL de los servidores web, visite [Configuración de una puerta de enlace de aplicaciones para la descarga SSL mediante el Administrador de recursos de Azure](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png

