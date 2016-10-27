<properties
   pageTitle="Optimización del enrutamiento de ExpressRoute | Microsoft Azure"
   description="Esta página proporciona detalles acerca de como optimizar el enrutamiento cuando un cliente tiene más de un circuito de ExpressRoute que se conecta entre Microsoft y la red corporativa del cliente."
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="charwen"/>


# <a name="optimize-expressroute-routing"></a>Optimización de enrutamiento de ExpressRoute
Cuando hay varios circuitos ExpressRoute, tiene más de una ruta de acceso para conectarse a Microsoft. Como resultado, se puede producir un enrutamiento no óptimo: es decir, el tráfico puede utilizar una ruta más larga para conectarse con Microsoft y este a su vez, con su red. Cuanto más larga sea la ruta de acceso a la red, mayor será la latencia. La latencia tiene un efecto directo en la experiencia del usuario y en el rendimiento de las aplicaciones. En este artículo se describe este problema y se explica cómo optimizar el enrutamiento mediante las tecnologías de enrutamiento estándar.

## <a name="suboptimal-routing-case-1"></a>Caso 1 de enrutamiento no óptimo
Eche un vistazo más detenidamente al problema del enrutamiento mediante un ejemplo. Imagine que tiene dos oficinas en Estados Unidos, una en Los Ángeles y la otra en Nueva York. Las oficinas están conectadas a una red de área extensa (WAN), que puede ser su propia red troncal o la VPN de la dirección IP del proveedor de servicios. Tiene dos circuitos ExpressRoute, uno en el oeste de EE.UU. y otro en el este, que también están conectados a la red de área extensa. Obviamente, tiene dos rutas de acceso para conectarse a la red de Microsoft. Ahora imagine que tiene una implementación de Azure (por ejemplo, Servicio de aplicaciones de Azure) en el oeste y en el este de EE.UU. Su intención es que los usuarios de Los Ángeles se conecten a la implementación de Azure en el oeste y los de Nueva York a la del este, porque el Administrador de servicios aconseja que los usuarios de cada oficina accedan a los servicios de Azure más cercanos para conseguir un rendimiento óptimo. Desafortunadamente, el plan funciona bien para los usuarios de la costa este, pero no para los usuarios de la costa oeste. La causa del problema es la siguiente. En cada circuito ExpressRoute, se indica el prefijo para la implementación de Azure del este (23.100.0.0/16) y el prefijo para la del oeste (13.100.0.0/16). Si no sabe qué prefijo pertenece a cada región, probablemente los tratará de la misma forma. La red WAN puede considerar que ambos prefijos están más próximos a la implementación del este de EE.UU. que a la del oeste y, por tanto, enrutará a los usuarios de ambas oficinas al circuito ExpressRoute del este. Al final, tendrá muchos usuarios descontentos en la oficina de Los Ángeles.

![](./media/expressroute-optimize-routing/expressroute-case1-problem.png)

### <a name="solution:-use-bgp-communities"></a>Solución: utilice comunidades de BGP.
Para optimizar el enrutamiento para los usuarios de ambas oficinas, debe saber qué prefijo pertenece al oeste y qué prefijo al este. Esta información se codifica mediante los [valores de las comunidades de BGP](expressroute-routing.md). Se ha asignado un valor único de comunidad de BGP para cada región de Azure, por ejemplo, "12076:51004" para el este de EE.UU. y "12076:51006" para el oeste. Ahora que sabe qué prefijo pertenece a cada región de Azure, puede configurar qué circuito de ExpressRoute debe ser el preferido. Al utilizar el BGP para intercambiar información de enrutamiento, se puede usar la preferencia local de BGP para influir en el enrutamiento. En nuestro ejemplo, puede asignar un valor de preferencia local superior a 13.100.0.0/16 para el oeste y, de forma similar, un valor de preferencia local superior a 23.100.0.0/16 para el este. Esta configuración garantiza que, cuando estén disponibles ambas rutas de acceso a Microsoft, los usuarios de Los Ángeles utilizarán el circuito de ExpressRoute del oeste para conectarse a la implementación oeste de Azure, mientras que los usuarios de Nueva York utilizarán el del este para conectarse a la implementación este. Con lo cual, se obtendrá un enrutamiento óptimo en ambas regiones. 

![](./media/expressroute-optimize-routing/expressroute-case1-solution.png)

## <a name="suboptimal-routing-case-2"></a>Caso 2 de enrutamiento no óptimo
Este es otro ejemplo en el que las conexiones de Microsoft toman una ruta de acceso más larga para llegar a la red. En este caso, se utilizan servidores de Exchange locales y Exchange Online en un [entorno híbrido](https://technet.microsoft.com/library/jj200581%28v=exchg.150%29.aspx). Las oficinas están conectadas a una red WAN. Usted indica los prefijos de los servidores locales de ambas oficinas a Microsoft a través de los dos circuitos de ExpressRoute. Exchange Online inicia las conexiones a los servidores locales en algunos casos como, por ejemplo, la migración de buzones. Desafortunadamente, la conexión a la oficina de Los Ángeles se enruta a través del circuito de ExpressRoute en el este de EE.UU antes de atravesar todo el país hasta la costa oeste. La causa del problema es similar a la del primer caso. Sin ninguna indicación, la red de Microsoft no puede saber qué prefijo de cliente está más próximo al este de EE.UU. y cuál al oeste. Y sucede que se elige la ruta de acceso incorrecta a la oficina de Los Ángeles.

![](./media/expressroute-optimize-routing/expressroute-case2-problem.png)

### <a name="solution:-use-as-path-prepending"></a>Solución: anteponga AS PATH
Hay dos soluciones al problema. La primera consiste simplemente en que anuncie el prefijo local para la oficina de Los Ángeles, 177.2.0.0/31, en el circuito de ExpressRoute en el oeste y el prefijo local para la oficina de Nueva York, 177.2.0.2/31, en el circuito de ExpressRoute en el este. Como resultado, habrá solo una ruta de acceso para que Microsoft se conecte a cada una de sus oficinas. No hay ninguna ambigüedad y, por tanto, el enrutamiento se optimiza. Con este diseño, debe pensar acerca de la estrategia de conmutación por error. En caso de que la ruta de acceso a Microsoft a través de ExpressRoute se interrumpa, deberá asegurarse de que Exchange Online puede conectarse a los servidores locales a pesar de ello. 

La segunda solución es que continúe anunciando ambos prefijos para los dos circuitos de ExpressRoute y, además, nos indique qué prefijo está más cerca de cada una de sus oficinas. Dado que se admite la anteposición de BGP AS Path, puede configurar AS Path como prefijo para que influya en el enrutamiento. En este ejemplo, puede alargar AS PATH para 172.2.0.0/31 en el este de EE.UU para que se prefiera el circuito de ExpressRoute del oeste para el tráfico destinado a este prefijo (ya que nuestra red creerá que la ruta de acceso a este prefijo es más corta en el oeste). De igual manera, puede alargar AS PATH para 172.2.0.2/31 en el oeste para que se prefiera el circuito de ExpressRoute en el este de EE.UU. De esta forma, se optimizará el enrutamiento para ambas oficinas. Con este diseño, si se interrumpe un circuito de ExpressRoute, Exchange Online podrá conectarse a través de otro circuito de ExpressRoute y de la red WAN. 

>[AZURE.IMPORTANT] Se quitan los números AS privados en AS PATH para los prefijos recibidos en el emparejamiento de Microsoft. Debe adjuntar números AS públicos en AS PATH para que influyan en el enrutamiento del emparejamiento de Microsoft.

![](./media/expressroute-optimize-routing/expressroute-case2-solution.png)

>[AZURE.IMPORTANT] Aunque los ejemplos que se proporcionan aquí son para Microsoft y los emparejamientos públicos, se admiten las mismas funcionalidades para el emparejamiento privado. Además, la ruta de acceso de AS antepuesta funciona dentro de un único circuito ExpressRoute, para influir en la selección de las rutas de acceso principales y secundarias.



<!--HONumber=Oct16_HO2-->


