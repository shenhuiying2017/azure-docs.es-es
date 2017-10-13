---
title: "Conexión segura a los recursos de back-end desde un entorno del Servicio de aplicaciones"
description: "Obtenga información acerca de cómo conectarse de forma segura a los recursos de back-end desde un entorno del Servicio de aplicaciones."
services: app-service
documentationcenter: 
author: stefsch
manager: erikre
editor: 
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.openlocfilehash: a43d88d64710b95dd753c19f02582f22accac8b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="securely-connecting-to-backend-resources-from-an-app-service-environment"></a>Conexión segura a los recursos de back-end desde un entorno del Servicio de aplicaciones
## <a name="overview"></a>Información general
Dado que siempre se crea un entorno de App Service **en** una red virtual de Azure Resource Manager **o** en [una][virtualnetwork] del modelo de implementación clásica, las conexiones salientes de dicho entorno a otros recursos de back-end solo pueden fluir a través de la red virtual.  Tras el cambio reciente realizado en junio de 2016, los entornos ASE también se pueden implementar en redes virtuales que usen intervalos de direcciones públicas o espacios de direcciones de RFC1918 (es decir, direcciones privadas).  

Por ejemplo, puede haber un servidor SQL Server que se ejecute en un clúster de máquinas virtuales con el puerto 1433 bloqueado.  En el extremo puede incluirse una lista de control de acceso para permitir únicamente el acceso de otros recursos de la misma red virtual.  

Otro ejemplo: los puntos de conexión con información confidencial pueden ejecutarse localmente y conectarse a Azure mediante conexiones [De sitio a sitio][SiteToSite] o [Azure ExpressRoute][ExpressRoute].  Como resultado, solo los recursos de las redes virtuales conectadas a túneles ExpressRoute o De sitio a sitio podrán tener acceso a los extremos locales.

En todos estos escenarios, las aplicaciones que se ejecutan en un entorno del Servicio de aplicaciones podrán conectarse de forma segura a los distintos servidores y recursos.  El tráfico saliente de las aplicaciones que se ejecutan en un entorno del Servicio de aplicaciones hacia extremos privados de la misma red virtual (o conectadas a la misma red virtual) solamente fluirá a través de la red virtual.  El tráfico saliente hacia extremos privados no fluirá a través de la red pública de Internet.

Existe una excepción en cuanto al tráfico saliente desde un Entorno del Servicio de aplicaciones hacia los puntos de conexión de una red virtual.  Los Entornos del Servicio de aplicaciones no pueden acceder a los puntos de conexión de máquinas virtuales ubicadas en la **misma** subred que el Entorno del Servicio de aplicaciones.  Normalmente, esto no debería causar ningún problema siempre y cuando el Entorno del Servicio de aplicaciones se implemente en una subred reservada para uso exclusivo del mismo.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Requisitos de DNS y conectividad saliente
Para que un Entorno del Servicio de aplicaciones funcione correctamente, necesita acceso de salida a varios puntos de conexión. Una lista completa de los puntos de conexión externos utilizados por un ASE en la sección "Conectividad de red necesaria" del artículo [Detalles de configuración de red para entornos del Servicio de aplicaciones con ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

Los Entornos del Servicio de aplicaciones requieren una infraestructura DNS válida configurada para la red virtual.  Si por algún motivo se cambia la configuración de DNS después de haber creado un entorno del Servicio de aplicaciones, los desarrolladores pueden forzar a un entorno del Servicio de aplicaciones para recoger la nueva configuración de DNS.  Si se desencadena un reinicio gradual del entorno mediante el icono de Reiniciar, ubicado en la parte superior de la hoja de administración del entorno del Servicio de aplicaciones en el portal, el entorno recogerá la nueva configuración de DNS.

También se recomienda configurar de antemano los servidores DNS personalizados de la red virtual antes de crear un entorno del Servicio de aplicaciones.  Si se cambia la configuración de DNS de una red virtual al crear un entorno del Servicio de aplicaciones, se generará un error en el proceso de creación de dicho entorno.  De manera similar, si existe un servidor DNS personalizado en el otro extremo de una puerta de enlace de VPN y el servidor DNS es inaccesible o no está disponible, el proceso de creación del entorno de servicio de aplicaciones también producirá un error.

## <a name="connecting-to-a-sql-server"></a>Conexión a un servidor SQL Server
Una configuración común de SQL Server tiene un extremo que escucha en el puerto 1433:

![Extremo de SQL Server][SqlServerEndpoint]

Existen dos formas de restringir el tráfico a este extremo:

* [Listas de control de acceso de red][NetworkAccessControlLists] (ACL de red)
* [Grupos de seguridad de red][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Restricción del acceso con una lista de control de acceso de red
El puerto 1433 se puede proteger mediante una lista de control de acceso de red.  En el ejemplo siguiente se incluyen en una lista blanca las direcciones de cliente que proceden de una red virtual concreta y se bloquea el acceso al resto de clientes.

![Ejemplo de lista de control de acceso de red][NetworkAccessControlListExample]

Todas las aplicaciones que se ejecuten en el entorno del Servicio de aplicaciones en la misma red virtual que el servidor SQL Server pueden conectarse a la instancia de SQL Server mediante la dirección IP **interna de la red virtual** para la máquina virtual SQL Server.  

La cadena de conexión de ejemplo siguiente hace referencia a SQL Server mediante su dirección IP privada.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Aunque la máquina virtual tiene también un extremo público, los intentos de conexión mediante la dirección IP pública se rechazarán debido a la lista de control de acceso de red. 

## <a name="restricting-access-with-a-network-security-group"></a>Restricción del acceso con un grupo de seguridad de red
Un enfoque alternativo para proteger el acceso consiste en usar un grupo de seguridad de red.  Los grupos de seguridad de red pueden aplicarse a máquinas virtuales individuales o a una subred que contenga máquinas virtuales.

En primer lugar, es necesario crear un grupo de seguridad de red:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Restringir el acceso exclusivamente al tráfico interno de la red virtual es muy sencillo con un grupo de seguridad de red.  Las reglas predeterminadas de un grupo de este tipo solo permiten el acceso de otros clientes de la misma red virtual.

Como resultado, bloquear el acceso a SQL Server es tan sencillo como aplicar un grupo de seguridad de red con sus reglas predeterminadas a las máquinas virtuales que ejecutan SQL Server o a la subred que contiene las máquinas virtuales.

En el ejemplo siguiente se aplica un grupo de seguridad de red a la subred contenedora:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'

El resultado final es un conjunto de reglas de seguridad que bloquean el acceso externo y permiten el acceso interno de la red virtual:

![Reglas de seguridad de red predeterminadas][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Introducción
Para empezar a trabajar con los entornos de App Service, consulte [Introducción al entorno de App Service][IntroToAppServiceEnvironment].

Para obtener más detalles sobre cómo controlar el tráfico entrante en el entorno de App Service, vea el artículo sobre el [control del tráfico entrante en un entorno de App Service][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png 
