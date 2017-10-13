---
title: "Detalles de configuración de red para trabajar con ExpressRoute"
description: "Detalles de configuración de red para ejecutar entornos del Servicio de aplicaciones en las redes virtuales conectadas a un circuito de ExpressRoute."
services: app-service
documentationcenter: 
author: stefsch
manager: nirma
editor: 
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.openlocfilehash: bb3e283e8a9327a9c66c8d8ded037cee5195ffc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="network-configuration-details-for-app-service-environments-with-expressroute"></a>Detalles de configuración de red para entornos del Servicio de aplicaciones con ExpressRoute
## <a name="overview"></a>Información general
Los clientes pueden conectar un circuito de [Azure ExpressRoute][ExpressRoute] a su infraestructura de red virtual, lo que permite ampliar la red local a Azure.  Se puede crear una instancia de App Service Environment en una subred de la infraestructura de esta [red virtual][virtualnetwork].  Las aplicaciones que se ejecutan en el entorno del Servicio de aplicaciones pueden establecer conexiones seguras con los recursos backend a los que solo se puede tener acceso través de la conexión ExpressRoute.  

Un entorno del Servicio de aplicaciones se puede crear **en** una red virtual de Azure Resource Manager **o en** una del modelo de implementación clásica.  Tras el cambio reciente realizado en junio de 2016, los entornos ASE también se pueden implementar ahora en redes virtuales que usen intervalos de direcciones públicas o espacios de direcciones de RFC1918 (es decir, direcciones privadas). 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Conectividad de red necesaria
Existen requisitos de conectividad de red para entornos del Servicio de aplicaciones que no pueden cumplirse inicialmente en una red virtual conectada a ExpressRoute.  Los entornos del Servicio de aplicaciones requieren todas las opciones siguientes para funcionar correctamente:

* Conectividad de red saliente a los puntos de conexión de Almacenamiento de Azure en los puertos 80 y 443.  Esto incluye los puntos de conexión situados en la misma región que el Entorno del Servicio de aplicaciones, así como los puntos de conexión de almacenamiento ubicados en **otras** regiones de Azure.  Los puntos de conexión de Azure Storage se resuelven en los siguientes dominios de DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* y *file.core.windows.net*.  
* Conectividad de red saliente al servicio Archivos de Azure en el puerto 445.
* Conectividad de red saliente a los puntos de conexión de Base de datos SQL ubicados en la misma región que el entorno del Servicio de aplicaciones.  Los puntos de conexión de SQL DB se resuelven en el dominio siguiente: *database.windows.net*.  Para ello, hay que abrir el acceso a los puertos 1433, 11000 a 11999 y 14000 a 14999.  Para obtener más información, consulte [este artículo sobre el uso de puertos en la versión 12 de Base de datos SQL](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).
* Conectividad de la red saliente a los puntos de conexión del plano de administración de Azure (puntos de conexión ASM y ARM).  Incluye conectividad saliente tanto a *management.core.windows.net* como a *management.azure.com*. 
* Conectividad de red saliente con *ocsp.msocsp.com*, *mscrl.microsoft.com* y *crl.microsoft.com*.  Es necesario para admitir la funcionalidad SSL.
* La configuración de DNS para la red virtual debe ser capaz de resolver todos los puntos de conexión y dominios mencionados en los puntos anteriores.  Si estos puntos de conexión no se pueden resolver, se producirá un error en los intentos de creación del entorno del Servicio de aplicaciones y los entornos del Servicio de aplicaciones existentes se marcarán como incorrectos.
* Se requiere acceso saliente en el puerto 53 para establecer la comunicación con los servidores DNS.
* Si existe un servidor DNS personalizado en el otro punto de conexión de una puerta de enlace de VPN, el servidor DNS debe estar accesible desde la subred que contiene el entorno de Servicio de aplicaciones. 
* La ruta de acceso de la red saliente no puede atravesar los servidores proxy corporativos internos, ni puede forzar la tunelización a local.  Si lo hace, se cambiará la dirección NAT en vigor del tráfico de red saliente del entorno del Servicio de aplicaciones.  Al cambiar la dirección NAT del tráfico de red de salida de un entorno del Servicio de aplicaciones causará errores de conectividad a muchos de los puntos de conexión enumerados anteriormente.  Lo que da como resultado un error al intentar la creación del entorno del Servicio de aplicaciones, y además, los entornos del Servicio de aplicaciones previamente correctos estarán marcados como incorrectos.  
* El acceso de red entrante a los puertos requeridos para las instancias de App Service Environment debe estar permitido, como se describe en este [artículo][requiredports].

Se cumplen los requisitos de DNS al asegurar que se configura y se mantiene una infraestructura DNS válida para la red virtual.  Si por algún motivo se cambia la configuración de DNS después de haber creado un entorno del Servicio de aplicaciones, los desarrolladores pueden forzar a un entorno del Servicio de aplicaciones para recoger la nueva configuración de DNS.  Si se desencadena un reinicio gradual del entorno mediante el icono de Reiniciar, ubicado en la parte superior de la hoja de administración del entorno de App Service en [Azure Portal][NewPortal], el entorno recogerá la nueva configuración de DNS.

Los requisitos de acceso de red entrante se pueden cumplir mediante la configuración de un [grupo de seguridad de red][NetworkSecurityGroups] en la subred de App Service Environment, como se describe en este [artículo][requiredports].

## <a name="enabling-outbound-network-connectivity-for-an-app-service-environment"></a>Habilitar la conectividad de red saliente para un entorno del Servicio de aplicaciones
De forma predeterminada, un circuito de ExpressRoute recién creado anuncia una ruta predeterminada que permite la conectividad saliente de Internet.  Con esta configuración, un entorno del Servicio de aplicaciones podrá conectarse a otros extremos de Azure.

Sin embargo, una configuración de cliente común es definir su propia ruta predeterminada (0.0.0.0/0) que fuerza a que el tráfico saliente de Internet fluya a nivel local.  El flujo de tráfico interrumpe invariablemente entornos del Servicio de aplicaciones porque el tráfico saliente está bloqueado de forma local o NAT tendría un conjunto de direcciones irreconocibles que ya no funcionan con varios extremos de Azure.

La solución es definir una (o más) rutas definidas por el usuario (UDR) en la subred que contiene el entorno del Servicio de aplicaciones.  Una ruta definida por el usuario define las rutas de subred específica que se respetarán en lugar de la ruta predeterminada.

Si es posible, se recomienda usar la configuración siguiente:

* La configuración de ExpressRoute anuncia 0.0.0.0/0 y, de manera predeterminada, fuerza la tunelización de todo el tráfico saliente a local.
* La UDR aplicada a la subred que contiene el entorno del Servicio de aplicaciones define 0.0.0.0/0 con un tipo de salto siguiente de Internet (un ejemplo se muestra posteriormente en este artículo).

El efecto combinado de estos pasos es que la UDR a nivel de subred tendrá prioridad sobre la tunelización forzada de ExpressRoute, lo que garantiza el acceso a Internet saliente desde el entorno del Servicio de aplicaciones.

> [!IMPORTANT]
> Las rutas definidas en una UDR **deben** ser lo suficientemente específicas como para que tengan prioridad sobre cualquier otra ruta anunciada por la configuración de ExpressRoute.  En el ejemplo siguiente se usa el intervalo de direcciones 0.0.0.0/0 amplio y como tal puede reemplazarse accidentalmente por los anuncios de ruta con intervalos de direcciones más específicos.
> 
> Las instancias de App Service Environment no son compatibles con las configuraciones de ExpressRoute que **anuncian rutas entre la ruta de acceso de los pares públicos y la ruta de acceso de los pares privados**.  Las configuraciones de ExpressRoute con el emparejamiento público configurado recibirán anuncios de ruta de Microsoft para un amplio conjunto de intervalos de direcciones IP de Microsoft Azure.  Si estos intervalos de direcciones se anuncian en la ruta de acceso de los pares privados, el resultado final es que se forzará la tunelización de todos los paquetes de red salientes desde la subred de App Service Environment a la infraestructura de red local de un cliente.  Actualmente, este flujo de red no es compatible con las instancias de App Service Environment.  La solución a este problema es dejar de anunciar rutas entre la ruta de acceso de los pares públicos y la ruta de acceso de los pares privados.
> 
> 

Se puede encontrar información de contexto sobre las rutas definidas por el usuario en esta [información general][UDROverview].  

En esta [guía de procedimiento][UDRHowTo] se puede encontrar información acerca de la creación y configuración de rutas definidas por el usuario.

## <a name="example-udr-configuration-for-an-app-service-environment"></a>Ejemplo de configuración de rutas definidas por el usuario para un entorno del Servicio de aplicaciones
**Requisitos previos**

1. Instale Azure PowerShell desde la [página de descargas de Azure][AzureDownloads] (fecha: junio de 2015 o posterior).  En "Herramientas de línea de comandos" hay un vínculo "Instalar" en "Windows Powershell" que instalará los cmdlets más recientes de PowerShell.
2. Se recomienda crear una única subred para que el entorno del Servicio de aplicaciones lo utilice de manera exclusiva.  Esto garantiza que las rutas definidas por el usuario aplicadas a la subred solo abrirán el tráfico saliente para el entorno del Servicio de aplicaciones.
3. **Importante**: no implemente App Service Environment hasta **después** de haber completado los siguientes pasos de configuración.  Esto garantiza que la conectividad de red saliente esté disponible antes de intentar implementar un entorno del Servicio de aplicaciones.

**Paso 1: Crear una tabla de rutas con nombre**

El fragmento de código siguiente crea una tabla de ruta llamada "DirectInternetRouteTable" en la región Oeste de EE.UU. de Azure:

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**Paso 2: Crear una o varias rutas en la tabla de rutas**

Necesitará agregar una o varias rutas a la tabla de ruta para habilitar el acceso de salida a Internet.  

El enfoque recomendado para configurar el acceso saliente a Internet es definir una ruta para 0.0.0.0/0, tal como se muestra a continuación.

    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet

Recuerde que 0.0.0.0/0 es un intervalo de direcciones amplio y, como tal, lo reemplazarán intervalos de direcciones más específicos anunciados por ExpressRoute.  Para repetir la recomendación anterior, debe usarse una UDR con una ruta 0.0.0.0/0 junto con una configuración de ExressRoute que solo anuncia 0.0.0.0/0 también. 

Como alternativa, puede descargar una lista completa y actualizada de intervalos CIDR usados por Azure.  El archivo XML que contiene todos los intervalos de direcciones IP de Azure está disponible en el [Centro de descarga de Microsoft][DownloadCenterAddressRanges].  

Sin embargo, tenga en cuenta que estos intervalos cambian con el tiempo, por lo que se necesitan actualizaciones periódicas manuales de las rutas definidas por el usuario para conservar la sincronización.  Además, como en una UDR individual hay un límite superior de 100 rutas, será preciso "resumir" los intervalos de direcciones IP de Azure para ajustarse a dicho límite, teniendo en cuenta que es necesario que las rutas de UDR definidas sean más específicas que las anunciadas por ExpressRoute.  

**Paso 3: Asociar la tabla de rutas a la subred que contiene el entorno del Servicio de aplicaciones**

El último paso de la configuración es asociar la tabla de rutas a la subred en la que se implementará App Service Environment.  El comando siguiente asocia "DirectInternetRouteTable" a la subred "ASESubnet" que, en última instancia, contendrá un entorno del Servicio de aplicaciones.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**Paso 4: Pasos finales**

Una vez que la tabla de ruta está enlazada a la subred, se recomienda que primero pruebe y confirme el efecto deseado.  Por ejemplo, implemente una máquina virtual en la subred y confirme que:

* El tráfico saliente a los puntos de conexión de Azure y los que no son de Azure mencionados anteriormente en este artículo **no** fluye de manera descendente al circuito de ExpressRoute.  Es muy importante comprobar este comportamiento, ya que si en el tráfico saliente de la subred se sigue forzando la tunelización local, siempre se producirá un error de creación del entorno del Servicio de aplicaciones. 
* Las búsquedas de DNS para los puntos de conexión mencionados anteriormente se están resolviendo correctamente. 

Una vez confirmados los pasos anteriores, deberá eliminar la máquina virtual porque la subred debe estar "vacía" en el momento en que se crea el entorno del Servicio de aplicaciones.

Ahora, ya puede continuar con la creación de un entorno del Servicio de aplicaciones.

## <a name="getting-started"></a>Introducción
Para empezar a trabajar con los entornos de App Service, consulte [Introducción al entorno de App Service][IntroToAppServiceEnvironment].

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[AzureDownloads]: http://azure.microsoft.com/en-us/downloads/ 
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653  
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[NewPortal]:  https://portal.azure.com


<!-- IMAGES -->
