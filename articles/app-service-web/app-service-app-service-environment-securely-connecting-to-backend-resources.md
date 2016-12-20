---
title: "Conexión segura a los recursos de back-end desde un entorno del Servicio de aplicaciones"
description: "Obtenga información acerca de cómo conectarse de forma segura a los recursos de back-end desde un entorno del Servicio de aplicaciones."
services: app-service
documentationcenter: 
author: stefsch
manager: wpickett
editor: 
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2783e9c84684a52fb9b85074eabc490c24b6eb9a


---
# <a name="securely-connecting-to-backend-resources-from-an-app-service-environment"></a>Conexión segura a los recursos de back-end desde un entorno del Servicio de aplicaciones
## <a name="overview"></a>Información general
Dado que las instancias de App Service Environment siempre se crean **en** una red virtual de Azure Resource Manager **o** en una [red virtual][virtualnetwork] del modelo de implementación clásica, las conexiones salientes de dicho entorno a otros recursos de back-end solo pueden fluir a través de la red virtual.  Tras el cambio reciente realizado en junio de 2016, los ASE también se pueden implementar en redes virtuales que usen intervalos de direcciones públicas o espacios de direcciones de RFC1918 (es decir, direcciones privadas).  

Por ejemplo, puede haber un servidor SQL Server que se ejecute en un clúster de máquinas virtuales con el puerto 1433 bloqueado.  En el extremo puede incluirse una lista de control de acceso para permitir únicamente el acceso de otros recursos de la misma red virtual.  

Otro ejemplo: los puntos de conexión con información confidencial pueden ejecutarse localmente y conectarse a Azure mediante conexiones [de sitio a sitio][SiteToSite] o de [Azure ExpressRoute][ExpressRoute].  Como resultado, solo los recursos de las redes virtuales conectadas a túneles ExpressRoute o De sitio a sitio podrán tener acceso a los extremos locales.

En todos estos escenarios, las aplicaciones que se ejecutan en un entorno del Servicio de aplicaciones podrán conectarse de forma segura a los distintos servidores y recursos.  El tráfico saliente de las aplicaciones que se ejecutan en un entorno del Servicio de aplicaciones hacia extremos privados de la misma red virtual (o conectadas a la misma red virtual) solamente fluirá a través de la red virtual.  El tráfico saliente hacia extremos privados no fluirá a través de la red pública de Internet.

Existe una excepción en cuanto al tráfico saliente desde un Entorno del Servicio de aplicaciones hacia los puntos de conexión de una red virtual.  Los Entornos del Servicio de aplicaciones no pueden acceder a los puntos de conexión de máquinas virtuales ubicadas en la **misma** subred que el Entorno del Servicio de aplicaciones.  Normalmente, esto no debería causar ningún problema siempre y cuando el Entorno del Servicio de aplicaciones se implemente en una subred reservada para uso exclusivo del mismo.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

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
Todos los artículos y procedimientos para los entornos del Servicio de aplicaciones están disponibles en el archivo [Léame para entornos del Servicio de aplicaciones](../app-service/app-service-app-service-environments-readme.md).

Para empezar a trabajar con los entornos del App Service, consulte [Introducción a App Service Environment][IntroToAppServiceEnvironment]

Para más información acerca de cómo controlar el tráfico que entra a App Service Environment, consulte [Control del tráfico de entrada a una instancia de App Service Environment][ControlInboundASE]

Para obtener más información acerca de la plataforma de Azure App Service, consulte [Azure App Service][AzureAppService].

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/ 
[ControlInboundASE]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/ 

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png 



<!--HONumber=Nov16_HO3-->


