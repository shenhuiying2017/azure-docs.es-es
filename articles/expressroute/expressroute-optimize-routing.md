---
title: "Optimización del enrutamiento de ExpressRoute: Azure | Microsoft Docs"
description: "Esta página muestra información acerca de cómo optimizar el enrutamiento cuando tenga más de un circuito de ExpressRoute que se conecte entre Microsoft y la red corporativa."
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: 
ms.assetid: fca53249-d9c3-4cff-8916-f8749386a4dd
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/06/2017
ms.author: charwen
ms.openlocfilehash: c3a85b9445d69330c3f6c7d298169efddb6ecca0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="optimize-expressroute-routing"></a>Optimización de enrutamiento de ExpressRoute
Cuando hay varios circuitos ExpressRoute, tiene más de una ruta de acceso para conectarse a Microsoft. Como resultado, se puede producir un enrutamiento no óptimo: es decir, el tráfico puede utilizar una ruta más larga para conectarse con Microsoft y este a su vez, con su red. Cuanto más larga sea la ruta de acceso a la red, mayor será la latencia. La latencia tiene un efecto directo en la experiencia del usuario y en el rendimiento de las aplicaciones. En este artículo se describe este problema y se explica cómo optimizar el enrutamiento mediante las tecnologías de enrutamiento estándar.

## <a name="suboptimal-routing-from-customer-to-microsoft"></a>Enrutamiento no óptimo de cliente a Microsoft
Eche un vistazo más detenidamente al problema del enrutamiento mediante un ejemplo. Imagine que tiene dos oficinas en Estados Unidos, una en Los Ángeles y la otra en Nueva York. Las oficinas están conectadas a una red de área extensa (WAN), que puede ser su propia red troncal o la VPN de la dirección IP del proveedor de servicios. Tiene dos circuitos ExpressRoute, uno en el oeste de EE.UU. y otro en el este, que también están conectados a la red de área extensa. Obviamente, tiene dos rutas de acceso para conectarse a la red de Microsoft. Ahora supongamos que tiene una implementación de Azure (por ejemplo, Azure App Service) en el Oeste de EE. UU. y en el Este de EE. UU. Su intención es que los usuarios de Los Ángeles se conecten a la implementación de Azure en el oeste y los de Nueva York a la del este, porque el Administrador de servicios aconseja que los usuarios de cada oficina accedan a los servicios de Azure más cercanos para conseguir un rendimiento óptimo. Desafortunadamente, el plan funciona bien para los usuarios de la costa este, pero no para los usuarios de la costa oeste. La causa del problema es la siguiente. En cada circuito ExpressRoute, se indica el prefijo para la implementación de Azure del este (23.100.0.0/16) y el prefijo para la del oeste (13.100.0.0/16). Si no sabe qué prefijo pertenece a cada región, probablemente los tratará de la misma forma. La red WAN puede considerar que ambos prefijos están más próximos a la implementación del este de EE.UU. que a la del oeste y, por tanto, enrutará a los usuarios de ambas oficinas al circuito ExpressRoute del este. Al final, tendrá muchos usuarios descontentos en la oficina de Los Ángeles.

![Problema de ExpressRoute caso 1: enrutamiento no óptimo de cliente a Microsoft](./media/expressroute-optimize-routing/expressroute-case1-problem.png)

### <a name="solution-use-bgp-communities"></a>Solución: utilice comunidades de BGP.
Para optimizar el enrutamiento para los usuarios de ambas oficinas, debe saber qué prefijo pertenece al oeste y qué prefijo al este. Esta información se codifica mediante los [valores de las comunidades de BGP](expressroute-routing.md). Se ha asignado un valor único de comunidad de BGP para cada región de Azure, por ejemplo, "12076:51004" para el este de EE.UU. y "12076:51006" para el oeste. Ahora que sabe qué prefijo pertenece a cada región de Azure, puede configurar qué circuito de ExpressRoute debe ser el preferido. Como se utiliza el BGP para intercambiar información de enrutamiento, puede usar la preferencia local de BGP para influir en el enrutamiento. En nuestro ejemplo, puede asignar un valor de preferencia local superior a 13.100.0.0/16 para el oeste y, de forma similar, un valor de preferencia local superior a 23.100.0.0/16 para el este. Esta configuración garantiza que, cuando estén disponibles ambas rutas de acceso a Microsoft, los usuarios de Los Ángeles utilizarán el circuito de ExpressRoute del oeste para conectarse a la implementación oeste de Azure, mientras que los usuarios de Nueva York utilizarán el del este para conectarse a la implementación este. Con lo cual, se obtendrá un enrutamiento óptimo en ambas regiones. 

![Solución de ExpressRoute caso 1: utilice comunidades de BGP](./media/expressroute-optimize-routing/expressroute-case1-solution.png)

> [!NOTE]
> La misma técnica, con preferencia Local, se puede aplicar en el enrutamiento de cliente a Azure Virtual Network. El valor de la comunidad de BGP no se etiqueta a los prefijos anunciados de Azure a su red. Sin embargo, dado que usted sabe cuál de las implementaciones de Virtual Network está cerca de cada oficina, puede configurar en consecuencia los enrutadores para que prefieran un circuito ExpressRoute a otro.
>
>

## <a name="suboptimal-routing-from-microsoft-to-customer"></a>Enrutamiento no óptimo de Microsoft al cliente
Este es otro ejemplo en el que las conexiones de Microsoft toman una ruta de acceso más larga para llegar a la red. En este caso, se utilizan servidores de Exchange locales y Exchange Online en un [entorno híbrido](https://technet.microsoft.com/library/jj200581%28v=exchg.150%29.aspx). Las oficinas están conectadas a una red WAN. Usted indica los prefijos de los servidores locales de ambas oficinas a Microsoft a través de los dos circuitos de ExpressRoute. Exchange Online inicia las conexiones a los servidores locales en algunos casos como, por ejemplo, la migración de buzones. Desafortunadamente, la conexión a la oficina de Los Ángeles se enruta a través del circuito de ExpressRoute en el este de EE.UU antes de atravesar todo el país hasta la costa oeste. La causa del problema es similar a la del primer caso. Sin ninguna indicación, la red de Microsoft no puede saber qué prefijo de cliente está más próximo al este de EE.UU. y cuál al oeste. Y sucede que se elige la ruta de acceso incorrecta a la oficina de Los Ángeles.

![ExpressRoute caso 2: enrutamiento no óptimo de Microsoft al cliente](./media/expressroute-optimize-routing/expressroute-case2-problem.png)

### <a name="solution-use-as-path-prepending"></a>Solución: anteponga AS PATH
Hay dos soluciones al problema. La primera consiste simplemente en que anuncie el prefijo local para la oficina de Los Ángeles, 177.2.0.0/31, en el circuito de ExpressRoute en el oeste y el prefijo local para la oficina de Nueva York, 177.2.0.2/31, en el circuito de ExpressRoute en el este. Como resultado, habrá solo una ruta de acceso para que Microsoft se conecte a cada una de sus oficinas. No hay ninguna ambigüedad y, por tanto, el enrutamiento se optimiza. Con este diseño, debe pensar acerca de la estrategia de conmutación por error. En caso de que la ruta de acceso a Microsoft a través de ExpressRoute se interrumpa, deberá asegurarse de que Exchange Online puede conectarse a los servidores locales a pesar de ello. 

La segunda solución es que continúe anunciando ambos prefijos para los dos circuitos de ExpressRoute y, además, nos indique qué prefijo está más cerca de cada una de sus oficinas. Dado que se admite la anteposición de BGP AS Path, puede configurar AS Path como prefijo para que influya en el enrutamiento. En este ejemplo, puede alargar AS PATH para 172.2.0.0/31 en el este de EE.UU para que se prefiera el circuito de ExpressRoute del oeste para el tráfico destinado a este prefijo (ya que nuestra red creerá que la ruta de acceso a este prefijo es más corta en el oeste). De igual manera, puede alargar AS PATH para 172.2.0.2/31 en el oeste para que se prefiera el circuito de ExpressRoute en el este de EE.UU. De esta forma, se optimizará el enrutamiento para ambas oficinas. Con este diseño, si se interrumpe un circuito de ExpressRoute, Exchange Online podrá conectarse a través de otro circuito de ExpressRoute y de la red WAN. 

> [!IMPORTANT]
> Se quitan los números AS privados en AS PATH para los prefijos recibidos en el emparejamiento de Microsoft. Debe adjuntar números AS públicos en AS PATH para que influyan en el enrutamiento del emparejamiento de Microsoft.
> 
> 

![Solución de ExpressRoute caso 2: anteponga la ruta de acceso AS](./media/expressroute-optimize-routing/expressroute-case2-solution.png)

> [!NOTE]
> Aunque los ejemplos que se proporcionan aquí son para Microsoft y los emparejamientos públicos, se admiten las mismas funcionalidades para el emparejamiento privado. Además, la ruta de acceso de AS antepuesta funciona dentro de un único circuito ExpressRoute, para influir en la selección de las rutas de acceso principales y secundarias.
> 
> 

## <a name="suboptimal-routing-between-virtual-networks"></a>Enrutamiento no óptimo entre redes virtuales
Con ExpressRoute, puede habilitar la comunicación de una instancia de Virtual Network a otra instancia de Virtual Network (también conocidas cada una como "VNet" o "red virtual") vinculándolas a un circuito de ExpressRoute. Cuando se vinculan a varios circuitos de ExpressRoute, puede producirse enrutamiento no óptimo entre las redes virtuales. Veamos un ejemplo. Tiene dos circuitos ExpressRoute, uno en el Oeste de EE. UU. y otro en el Este de EE. UU. En cada región, tiene dos redes virtuales. Los servidores web están implementados en una red virtual y los servidores de aplicaciones en la otra. Para conseguir redundancia, vincule las dos redes virtuales en cada región tanto al circuito de ExpressRoute local como al remoto. Como puede verse a continuación, de cada red virtual hay dos rutas de acceso a la otra red virtual. Las redes virtuales no saben cuál circuito de ExpressRoute es el local y cuál es el remoto. Por lo tanto, teniendo en cuenta que hacen enrutamiento multidireccional de igual costo (ECMP) para equilibrar la carga del tráfico entre redes virtuales, algunos flujos de tráfico tomarán la ruta de acceso más larga y se enrutarán al circuito de ExpressRoute remoto.

![ExpressRoute caso 3: enrutamiento no óptimo entre redes virtuales](./media/expressroute-optimize-routing/expressroute-case3-problem.png)

### <a name="solution-assign-a-high-weight-to-local-connection"></a>Solución: asignar un peso alto a la conexión local
La solución es sencilla. Dado que usted sabe dónde están las redes virtuales y los circuitos, puede decirnos cuál debería ser la ruta de acceso preferida de cada red virtual. Específicamente para este ejemplo, se asigna a la conexión local un peso mayor que a la conexión remota (vea el ejemplo de configuración [aquí](expressroute-howto-linkvnet-arm.md#modify-a-virtual-network-connection)). Cuando una red virtual recibe el prefijo de la otra red virtual en varias conexiones preferirá la conexión con el mayor peso para enviar el tráfico destinado a ese prefijo.

![Solución de ExpressRoute caso 3: asignar un peso alto a la conexión local](./media/expressroute-optimize-routing/expressroute-case3-solution.png)

> [!NOTE]
> También puede influir en el enrutamiento de la red virtual a la red local, si tiene varios circuitos ExpressRoute, configurando el peso de una conexión en lugar de aplicar la anteposición de la ruta de acceso AS, una técnica que se describe en el segundo escenario anterior. Para cada prefijo siempre atenderemos primero al peso de la conexión antes que a la longitud de ruta de acceso AS a la hora de decidir cómo enviar tráfico.
>
>
