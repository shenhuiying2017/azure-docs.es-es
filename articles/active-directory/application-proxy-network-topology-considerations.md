---
title: "Consideraciones sobre la topología de red al utilizar el Proxy de aplicación de Azure AD | Microsoft Docs"
description: "Se tratan las consideraciones de la topología de red al utilizar el Proxy de aplicación de Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 1fdc00865f31352026a64c2769d9d65047bb2fa6
ms.openlocfilehash: 26ec9167f1f883c3c71947a55142020fb48e206a


---

# <a name="network-topology-considerations-when-using-azure-ad-application-proxy"></a>Consideraciones sobre la topología de red al utilizar el Proxy de aplicación de Azure AD
> [!NOTE]
> Proxy de aplicación es una característica que solo está disponible si actualizó a la edición Premium o Basic de Azure Active Directory. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).
> 

En este artículo se explican las consideraciones de la topología de red al utilizar el Proxy de aplicación de Azure AD para la publicación y el acceso a las aplicaciones de forma remota. 

## <a name="traffic-flow"></a>Flujo de tráfico

Cuando se publica una aplicación a través del Proxy de aplicación de Azure AD, todo el tráfico de los usuarios a las aplicaciones de back-end de destino fluyen a través de los saltos siguientes:

* Salto 1: Usuario al punto de conexión público del servicio de Proxy de aplicación de Azure AD en Azure
* Salto 2: Servicio de Proxy de aplicación al conector
* Saltos 3: Conector a aplicación de destino

 ![Varios proveedores de nube (IaaS de Azure AD)](./media/application-proxy-network-topologies/application-proxy-three-hops.png)

## <a name="tenant-location-and-app-proxy-service"></a>Ubicación del inquilino y el servicio de Proxy de aplicación

Cuando se suscribe a un inquilino de Azure AD, la región de su inquilino (Estados Unidos, EMEA, APAC, etc.) se determina según el país que especifique. Al habilitar el Proxy de aplicación, se muestran las instancias del servicio de Proxy de aplicación para el inquilino de la misma región del inquilino de Azure AD o la región más cercana a este. 

Por ejemplo, si la región de su inquilino de Azure AD es la Unión Europea (UE), todos los conectores del Proxy de aplicación de Azure AD se conectarán a las instancias del servicio de Proxy de aplicación en centros de datos de Azure en la UE. Esto significa también que todos los usuarios se enviarán a través de las instancias del servicio de Proxy de aplicación de esta ubicación al tratar de tener acceso a las aplicaciones publicadas.

## <a name="considerations-for-reducing-latency"></a>Consideraciones para reducir la latencia

Todas las soluciones de proxy introducirán latencia en la conexión de red. Independientemente del proxy o solución VPN que elija como solución de acceso remoto, siempre se incluirá un conjunto de servidores que habilita la conexión hacia su la red corporativa. 

Las organizaciones normalmente incluían puntos de conexión de servidor en su red perimetral. Sin embargo, con el Proxy de aplicación de Azure AD ya no se requiere ninguna red perimetral.  Esto se debe a que con el Proxy de aplicación el tráfico fluye a través del servicio de proxy en la nube, mientras que los conectores residen en la red corporativa.

### <a name="connector-placement"></a>Colocación del conector

El servicio de Proxy de aplicación elige automáticamente la ubicación de las instancias en función de la ubicación del inquilino. Por lo tanto, corresponde al usuario decidir dónde instalar el conector, con lo que tiene la posibilidad de definir las características de la latencia de extremo a extremo del tráfico de su red.

Debe plantearse algunas preguntas al configurar el servicio de Proxy de aplicación:

* ¿Dónde se encuentra la aplicación?
* ¿Dónde se encuentran la mayoría de los usuarios que acceden a la aplicación?
* ¿Dónde se encuentra la instancia del Proxy de aplicación (esto se basa en el inquilino)?
* ¿Ya tiene una conexión de red dedicada a centros de datos de Azure (como ExpressRoute o una configuración VPN similar)?

La colocación del conector determinará la latencia de los saltos 2 y 3. Al evaluar la posición del conector, se debe considerar lo siguiente:

* El conector necesita una línea de visión a un centro de datos para realizar operaciones de delegación limitada de Kerberos (KDC) cuando desee un inicio de sesión único (SSO) en aplicaciones de back-end.
* El conector suele instalarse más cerca de la aplicación a fin de reducir el tiempo desde el conector a la aplicación.

### <a name="general-approach-to-minimize-latency"></a>Método general para minimizar la latencia

Puede probar y minimizar la latencia del tráfico de extremo a extremo mediante la optimización de cada uno de los saltos de red, de manera que el tráfico fluya a través de ellos.

Para optimizar cada salto, puede hacer lo siguiente:

* Reducir la distancia entre los dos extremos del salto.
* Elegir la red adecuada por la que pasar. Por ejemplo, puede ser más rápido pasar por una red privada en lugar de la red pública de Internet debido a los vínculos dedicados.
 
Si tiene un vínculo de VPN/ExpressRoute dedicado entre Azure y la red corporativa, puede que desee aprovechar las ventajas.

## <a name="focus-your-optimizing-strategy"></a>Centrar la estrategia de optimización

Dado que los usuarios pueden tener acceso a aplicaciones de forma remota a través de Internet, siempre debe centrarse en optimizar los saltos 2 y 3. A continuación se muestran algunos de los modelos comunes que puede incorporar.

### <a name="pattern-1-optimize-hop-3"></a>Modelo 1: optimizar el salto 3:

Para optimizar el salto 3, el conector se coloca cerca de la aplicación de destino en la red del cliente. La ventaja de ello es que el conector es probable que necesite una línea de visión con el controlador de dominio, como se mencionó anteriormente. Este enfoque suele ser suficiente para la mayoría de clientes y escenarios. La mayoría de clientes sigue este patrón.

 ![Varios proveedores de nube (IaaS de Azure AD)](./media/application-proxy-network-topologies/application-proxy-hop3.png)


> [!NOTE]
Hay algunos escenarios en los que debe optimizar tanto el salto 2 como el salto 3 para conseguir las características de latencia que desea. Por ejemplo, si tiene una configuración de VPN o ExpressRoute entre la red y el centro de datos de Azure, este escenario permite optimizar el salto 2, además del salto 3.
>

### <a name="pattern-2-take-advantage-of-expressroute-with-public-peering"></a>Modelo 2: sacar partido de ExpressRoute con el emparejamiento público

Si tiene una instalación de ExpressRoute con emparejamiento público, se aprovechará la conexión de ExpressRoute más rápida para el salto 2. El salto 3 ya está optimizado al colocar el conector cerca de la aplicación en la red del cliente.

![Varios proveedores de nube (IaaS de Azure AD)](./media/application-proxy-network-topologies/application-proxy-expressroute-public.png)

### <a name="pattern-3-taking-advantage-expressroute-with-private-peering"></a>Modelo 3: sacar partido de ExpressRoute con el emparejamiento privado

Si tiene una configuración de VPN o ExpressRoute dedicada con emparejamiento privado entre Azure y la red corporativa en la que está instalada la aplicación, tiene otra opción. En esta configuración, la red virtual en Azure normalmente se considera una extensión de la red corporativa. Así pues, puede instalar el conector en el centro de datos de Azure y aun así cumplir los requisitos de latencia baja de la conexión del conector a la aplicación para el salto 3. 

La latencia no se ve comprometida porque el tráfico está fluyendo por una red dedicada. Sin embargo, disfrutará la ventaja adicional de mejorar las características de latencia del salto 2. Esto se debe a que la conexión del servicio al conector (salto 2) es un salto más corto, ya que conector está instalado en un centro de datos de Azure próximo a la ubicación de su inquilino de AAD (y, por tanto, del Proxy de aplicación).

![Varios proveedores de nube (IaaS de Azure AD)](./media/application-proxy-network-topologies/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Otros enfoques

En este artículos se ha abordado hasta el momento la ubicación del conector. Sin embargo, si se plantea el traslado de la aplicación como una opción válida en su caso (por ejemplo, a Azure o a otro entorno hospedado), puede cambiar la ubicación de la aplicación para obtener mejores características de latencia. 

Las organizaciones están cambiando sus redes a entornos hospedados cada vez con más frecuencia. Esto les permite colocar sus aplicaciones en el entorno hospedado que también forma parte de su red corporativa y seguir estando dentro del dominio. En este caso, los patrones anteriores se pueden aplicar a la nueva ubicación de la aplicación.

Considere el uso de grupos de conectores para aplicaciones de destino que se encuentren en diferentes ubicaciones y redes. Si se plantea esta opción, consulte [Servicios de dominio de Azure Active Directory](https://azure.microsoft.com/en-us/services/active-directory-ds). 

## <a name="common-scenarios"></a>Escenarios comunes

En esta sección se abordan algunos de los casos de uso. En todos los casos de uso que se plantean, se asume que el inquilino de Azure AD (y, por tanto, el punto de conexión de servicio del proxy) se encuentra en Estados Unidos. Normalmente se aplican las mismas consideraciones en otras regiones del mundo.

### <a name="use-case-1"></a>Caso de uso 1

La aplicación se encuentra en la red de un cliente de Estados Unidos con usuarios en la misma región. No hay ExpressRoute ni VPN entre el controlador de dominio de Azure y la red corporativa.

**Recomendación:** siga el caso de uso 1 anterior. Para mejorar la latencia, considere la posibilidad de aprovechar ExpressRoute, si es necesario (vea los casos de uso 3 y 4).

Se trata de un modelo simple. El modelo más común es optimizar el salto 3, donde el conector se coloca cerca de la aplicación. Se trata además de una opción natural, ya que el conector se instala normalmente con línea de visión hacia la aplicación y el controlador de dominio para realizar operaciones de KCD.
Este caso de uso se adhiere al siguiente modelo 1.

![Varios proveedores de nube (IaaS de Azure AD)](./media/application-proxy-network-topologies/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Caso de uso 2

La aplicación se encuentra en la red de un cliente de Estados Unidos con usuarios repartidos por todo el mundo. No hay ExpressRoute ni VPN entre el controlador de dominio de Azure y la red corporativa.

**Recomendación:** siga el caso de uso 2 anterior. Para mejorar la latencia, considere la posibilidad de aprovechar ExpressRoute, si es necesario (vea los casos de uso 3 y 4).

De nuevo, el modelo más común es optimizar el salto 3, donde el conector se coloca cerca de la aplicación por los motivos explicados más arriba. El salto 3 no es costoso por lo general, si todo se encuentra dentro de la misma región. Sin embargo, el salto 1 puede resultar más costoso dependiendo de dónde esté el usuario, ya que todos los usuarios tendrán acceso a la instancia del Proxy de aplicación en Estados Unidos. Merece la pena mencionar que cualquier solución de proxy tendrá similares características con respecto a los usuarios que se reparten por todo el mundo.

Este caso de uso se adhiere al siguiente modelo 2.

![Varios proveedores de nube (IaaS de Azure AD)](./media/application-proxy-network-topologies/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Caso de uso 3

La aplicación se encuentra en la red de un cliente en Estados Unidos. Hay una instancia de ExpressRoute con emparejamiento público entre Azure y la red corporativa.

**Recomendación:** coloque el conector lo más próximo posible a la aplicación. El sistema utilizará automáticamente ExpressRoute para el salto 2. Esto sigue el patrón 2 descrito anteriormente.

Si el vínculo de ExpressRoute utiliza emparejamiento público, el tráfico entre el proxy y el conector pasará por ese vínculo y se optimizará la latencia del salto 2.

Este caso de uso se adhiere al siguiente modelo 3.

![Varios proveedores de nube (IaaS de Azure AD)](./media/application-proxy-network-topologies/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Caso de uso 4

La aplicación se encuentra en la red de un cliente en Estados Unidos. Hay una instancia de ExpressRoute con emparejamiento privado entre Azure y la red corporativa.

**Recomendación:** coloque el conector del controlador de dominio de Azure que esté conectado a la red corporativa a través del emparejamiento privado de ExpressRoute. Esto sigue el patrón 3 descrito anteriormente.

El conector puede colocarse en el controlador de dominio de Azure. Puesto que todavía tiene una línea de visión hacia la aplicación y el controlador de dominio a través de la red privada, el salto 3 se mantiene optimizado. Sin embargo, esta configuración optimiza además el salto 2.

Este caso de uso se adhiere al siguiente modelo 4.

![Varios proveedores de nube (IaaS de Azure AD)](./media/application-proxy-network-topologies/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Caso de uso 5

La aplicación se encuentra en la red de un cliente en la Unión Europea y la mayoría de los usuarios están en Estados Unidos.

**Recomendación:** coloque el conector cerca de la aplicación. Por los motivos explicados anteriormente, esta es la mejor opción. Puesto que los usuarios de Estados Unidos tienen acceso a una instancia del Proxy de aplicación que resulta estar en la misma región, el salto 1 no es demasiado costoso. El salto 3 se optimiza. Sin embargo, el salto 2 suele ser caro en este caso de uso.

Este caso de uso se adhiere al siguiente modelo 5.

![Varios proveedores de nube (IaaS de Azure AD)](./media/application-proxy-network-topologies/application-proxy-pattern5a.png)

Considere la posibilidad de aprovechar ExpressRoute como se explica en los anteriores modelos 2 y 3. A continuación se muestra una aplicación del modelo 2.

Este caso de uso se adhiere al siguiente modelo 5b.

![Varios proveedores de nube (IaaS de Azure AD)](./media/application-proxy-network-topologies/application-proxy-pattern5b.png)

A continuación se muestra otra variante para este caso de uso cuyo uso puede considerar.

Si la mayoría de los usuarios de la organización están en Estados Unidos, lo más probable es que la red también se "extienda" a Estados Unidos. Si es así, el conector se puede colocar en Estados Unidos y sacar provecho de la línea de red corporativa interna dedicada a la aplicación en la Unión Europea. De este modo, se optimizan los saltos 2 y 3.

Este caso de uso se adhiere al siguiente modelo 5c.

![Varios proveedores de nube (IaaS de Azure AD)](./media/application-proxy-network-topologies/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Pasos siguientes
[Habilitación del proxy de la aplicación](active-directory-application-proxy-enable.md)<br>
[Habilitar el inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)<br>
[Habilitar el acceso condicional](active-directory-application-proxy-conditional-access.md)<br>
[Solucionar los problemas que tiene con el Proxy de aplicación](active-directory-application-proxy-troubleshoot.md)





<!--HONumber=Feb17_HO1-->


