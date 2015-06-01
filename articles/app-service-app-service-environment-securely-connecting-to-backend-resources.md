<properties 
	pageTitle="Conexión segura a los recursos de back-end desde un entorno del Servicio de aplicaciones" 
	description="Obtenga información acerca de cómo conectarse de forma segura a los recursos de back-end desde un entorno del Servicio de aplicaciones." 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="stefsh"/>

# Conexión segura a los recursos de back-end desde un entorno del Servicio de aplicaciones #

## Información general ##
Dado que un entorno del Servicio de aplicaciones siempre se crea en una subred de una [red virtual][virtualnetwork] regional, las conexiones salientes de dicho entorno a otros recursos de back-end solo pueden fluir a través de la red virtual.

Por ejemplo, puede haber un servidor SQL Server que se ejecute en un clúster de máquinas virtuales con el puerto 1433 bloqueado. En el extremo puede incluirse una lista de control de acceso para permitir únicamente el acceso de otros recursos de la misma red virtual.

Otro ejemplo: los extremos con información confidencial pueden ejecutarse localmente y conectarse a Azure mediante conexiones [De sitio a sitio][SiteToSite] o [Azure ExpressRoute][ExpressRoute]. Como resultado, solo los recursos de las redes virtuales conectadas a túneles ExpressRoute o De sitio a sitio podrán tener acceso a los extremos locales.

En todos estos escenarios, las aplicaciones que se ejecutan en un entorno del Servicio de aplicaciones podrán conectarse de forma segura a los distintos servidores y recursos. El tráfico saliente de las aplicaciones que se ejecutan en un entorno del Servicio de aplicaciones hacia extremos privados de la misma red virtual (o conectadas a la misma red virtual) solamente fluirá a través de la red virtual. El tráfico saliente hacia extremos privados no fluirá a través de la red pública de Internet.


## Conexión a un servidor SQL Server
Una configuración común de SQL Server tiene un extremo que escucha en el puerto 1433:

![Extremo de SQL Server][SqlServerEndpoint]

Existen dos formas de restringir el tráfico a este extremo:


- [Listas de control de acceso de red][NetworkAccessControlLists] (ACL de red)

- [Grupos de seguridad de red][NetworkSecurityGroups]


## Restricción del acceso con una lista de control de acceso de red

El puerto 1433 se puede proteger mediante una lista de control de acceso de red. En el ejemplo siguiente se incluyen en una lista blanca las direcciones de cliente que proceden de una red virtual concreta y se bloquea el acceso al resto de clientes.

![Ejemplo de lista de control de acceso de red][NetworkAccessControlListExample]

Todas las aplicaciones que se ejecuten en el entorno del Servicio de aplicaciones en la misma red virtual que el servidor SQL Server pueden conectarse a la instancia de SQL Server mediante la dirección IP **interna de la red virtual** para la máquina virtual SQL Server.

La cadena de conexión de ejemplo siguiente hace referencia a SQL Server mediante su dirección IP privada.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Aunque la máquina virtual tiene también un extremo público, los intentos de conexión mediante la dirección IP pública se rechazarán debido a la lista de control de acceso de red.

## Restricción del acceso con un grupo de seguridad de red
Un enfoque alternativo para proteger el acceso consiste en usar un grupo de seguridad de red. Los grupos de seguridad de red pueden aplicarse a máquinas virtuales individuales o a una subred que contenga máquinas virtuales.

En primer lugar, es necesario crear un grupo de seguridad de red:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Restringir el acceso exclusivamente al tráfico interno de la red virtual es muy sencillo con un grupo de seguridad de red. Las reglas predeterminadas de un grupo de este tipo solo permiten el acceso de otros clientes de la misma red virtual.

Como resultado, bloquear el acceso a SQL Server es tan sencillo como aplicar un grupo de seguridad de red con sus reglas predeterminadas a las máquinas virtuales que ejecutan SQL Server o a la subred que contiene las máquinas virtuales.

En el ejemplo siguiente se aplica un grupo de seguridad de red a la subred contenedora:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
    
El resultado final es un conjunto de reglas de seguridad que bloquean el acceso externo y permiten el acceso interno de la red virtual:

![Reglas de seguridad de red predeterminadas][DefaultNetworkSecurityRules]


## Introducción

Para empezar a trabajar con los entornos del Servicio de aplicaciones, vea [Introducción al entorno del Servicio de aplicaciones][IntroToAppServiceEnvironment].

Para obtener más detalles sobre cómo controlar el tráfico entrante en el entorno del Servicio de aplicaciones, vea [Control del tráfico entrante en un entorno del Servicio de aplicaciones][ControlInboundASE]

Para obtener más información acerca de la plataforma Servicio de aplicaciones de Azure, consulte [Servicio de aplicaciones de Azure][AzureAppService].

<!-- LINKS -->
[virtualnetwork]: https://msdn.microsoft.com/library/azure/dn133803.aspx
[ControlInboundTraffic]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SiteToSite]: https://msdn.microsoft.com/library/azure/dn133795.aspx
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://msdn.microsoft.com/library/azure/dn376541.aspx
[NetworkSecurityGroups]: https://msdn.microsoft.com/library/azure/dn848316.aspx
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ControlInboundASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png
<!--HONumber=52-->
