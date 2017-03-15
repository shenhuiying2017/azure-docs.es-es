---
title: "Consideraciones sobre la topología de red al utilizar el Proxy de aplicación de Azure Active Directory | Microsoft Docs"
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
ms.sourcegitcommit: 4b21bf6bc1be59facd503000a4f83a56189d55d3
ms.openlocfilehash: aea1b35348bec0affe2288ff683e0320e2b0f714
ms.lasthandoff: 02/28/2017


---

# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Consideraciones sobre la topología de red al utilizar el Proxy de aplicación de Azure Active Directory
> [!NOTE]
> Proxy de aplicación es una característica que solo está disponible si actualizó a la edición Premium o Basic de Azure Active Directory. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).
>

En este artículo se explican las consideraciones de la topología de red al utilizar el Proxy de aplicación de Azure Active Directory (Azure AD) para la publicación y el acceso a las aplicaciones de forma remota.

## <a name="traffic-flow"></a>Flujo de tráfico

Cuando se publica una aplicación a través del Proxy de aplicación de Azure AD, todo el tráfico de los usuarios a las aplicaciones de back-end de destino fluye a través de los saltos siguientes:

* Salto 1: Usuario al punto de conexión público del servicio de Proxy de aplicación de Azure AD en Azure
* Salto 2: Servicio de Proxy de aplicación al conector
* Saltos 3: Conector a aplicación de destino

 ![Diagrama que muestra el flujo de tráfico del usuario a la aplicación de destino](./media/application-proxy-network-topologies/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Ubicación del inquilino y el servicio de Proxy de aplicación

Cuando se suscribe a un inquilino de Azure AD, la región de su inquilino viene determinada por el país que especifique. Al habilitar el Proxy de aplicación, se muestran las instancias del servicio de Proxy de aplicación para el inquilino de la misma región del inquilino de Azure AD o la región más cercana a este.

Por ejemplo, si la región del inquilino de Azure AD es la Unión Europea (UE), todos los conectores de Proxy de aplicación utilizarán las instancias de servicio de los centros de datos de Azure en la Unión Europea. Esto significa también que todos los usuarios se enviarán a través de las instancias del servicio de Proxy de aplicación de esta ubicación al tratar de tener acceso a las aplicaciones publicadas.

## <a name="considerations-for-reducing-latency"></a>Consideraciones para reducir la latencia

Todas las soluciones de proxy introducirán latencia en la conexión de red. Independientemente del proxy o solución VPN que elija como solución de acceso remoto, siempre se incluirá un conjunto de servidores que habilita la conexión hacia su la red corporativa.

Las organizaciones normalmente incluían puntos de conexión de servidor en su red perimetral. Pero con el Proxy de aplicación de Azure AD, no se requiere ninguna red perimetral. Esto se debe a que con el Proxy de aplicación el tráfico fluye a través del servicio de proxy en la nube, mientras que los conectores residen en la red corporativa.

### <a name="connector-placement"></a>Colocación del conector

Proxy de aplicación elige automáticamente la ubicación de las instancias en función de la ubicación del inquilino. Por lo tanto, corresponde al usuario decidir dónde instalar el conector, con lo que tiene la posibilidad de definir las características de la latencia del tráfico de su red.

Al configurar el servicio de Proxy de aplicación, deberá formular las siguientes preguntas:

* ¿Dónde se encuentra la aplicación?
* ¿Dónde se encuentran la mayoría de los usuarios que acceden a la aplicación?
* ¿Dónde se encuentra la instancia del Proxy de aplicación? (esto se basa en el inquilino)
* ¿Ya tiene configurada una conexión de red dedicada a centros de datos de Azure? (como ExpressRoute o una VPN similar)

La colocación del conector determina la latencia de los saltos 2 y 3 (que se han descrito en la sección anterior). Al evaluar la posición del conector, se debe considerar lo siguiente:

* El conector necesita una línea de visión a un centro de datos. Esto permite al conector realizar operaciones de delegación limitada de Kerberos (KDC) cuando desee un inicio de sesión único (SSO) en aplicaciones de back-end.
* El conector suele instalarse más cerca de la aplicación a fin de reducir el tiempo desde el conector a la aplicación.

### <a name="general-approach-to-minimize-latency"></a>Método general para minimizar la latencia

Puede probar y minimizar la latencia del tráfico de extremo a extremo mediante la optimización de cada uno de los saltos de red. Para optimizar cada salto, puede hacer lo siguiente:

* Reducir la distancia entre los dos extremos del salto.
* Elegir la red adecuada por la que pasar. Por ejemplo, puede ser más rápido pasar por una red privada en lugar de la red pública de Internet debido a los vínculos dedicados.

Si tiene un vínculo de VPN o ExpressRoute dedicado entre Azure y la red corporativa, puede que desee usarlo.

## <a name="focus-your-optimizing-strategy"></a>Centrar la estrategia de optimización

Dado que los usuarios pueden tener acceso a aplicaciones de forma remota a través de Internet, siempre debe centrarse en optimizar los saltos 2 y 3. A continuación, se muestran algunos de los patrones comunes que puede incorporar.

### <a name="pattern-1-optimize-hop-3"></a>Patrón 1: optimizar el salto 3

Para optimizar el salto 3, el conector se coloca cerca de la aplicación de destino en la red del cliente. La ventaja de ello es que el conector es probable que necesite una línea de visión al controlador de dominio. Este enfoque es suficiente para la mayoría de los escenarios. (De hecho, la mayoría de clientes sigue este patrón).

 ![Diagrama que muestra la optimización del salto 3, con el conector situado cerca de la aplicación de destino](./media/application-proxy-network-topologies/application-proxy-hop3.png)


> [!NOTE]
Hay algunos escenarios en los que debe optimizar tanto el salto 2 como el salto 3 para conseguir las características de latencia que desea. Por ejemplo, si tiene una configuración de VPN o ExpressRoute entre la red y el centro de datos de Azure podrá optimizar ambos saltos.
>

### <a name="pattern-2-take-advantage-of-expressroute-with-public-peering"></a>Patrón 2: sacar partido de ExpressRoute con el emparejamiento público

Si tiene una configuración de ExpressRoute con emparejamiento público, podrá usar la conexión de ExpressRoute más rápida para el salto 2. El salto 3 ya está optimizado al colocar el conector cerca de la aplicación en la red.

![Diagrama que muestra la optimización del salto 2 mediante una conexión de ExpressRoute](./media/application-proxy-network-topologies/application-proxy-expressroute-public.png)

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Patrón 3: sacar partido de ExpressRoute con el emparejamiento privado

Si tiene una configuración de VPN o ExpressRoute dedicada con emparejamiento privado entre Azure y la red corporativa, tiene otra opción. En esta configuración, la red virtual de Azure normalmente se considera una extensión de la red corporativa. Así pues, puede instalar el conector en el centro de datos de Azure y aun así cumplir los requisitos de latencia baja de la conexión del conector a la aplicación para el salto 3.

La latencia no se ve comprometida porque el tráfico está fluyendo por una conexión dedicada. Además, disfrutará la ventaja adicional de mejorar las características de latencia del salto 2. Esto se debe a que la conexión del servicio Proxy de aplicación al conector presenta ahora un salto más corto. El conector está instalado en un centro de datos de Azure cercano a la ubicación del inquilino de Azure AD (y, por tanto, al Proxy de aplicación).

![Diagrama que muestra el conector instalado en un centro de datos de Azure](./media/application-proxy-network-topologies/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Otros enfoques

Aunque el centro de atención de este artículo es la selección de la ubicación del conector, también puede cambiar la ubicación de la aplicación para obtener mejores características de latencia.

Las organizaciones están cambiando sus redes a entornos hospedados cada vez con más frecuencia. Esto les permite colocar sus aplicaciones en un entorno hospedado que también forma parte de su red corporativa y seguir estando dentro del dominio. En este caso, los patrones analizados en las secciones anteriores se pueden aplicar a la nueva ubicación de la aplicación.

Considere el uso de grupos de conectores para aplicaciones de destino que se encuentren en diferentes ubicaciones y redes. Si se plantea esta opción, consulte [Servicios de dominio de Azure Active Directory](https://azure.microsoft.com/services/active-directory-ds).

## <a name="common-scenarios"></a>Escenarios comunes

En esta sección se abordan algunos de los casos de uso. Suponga que el inquilino de Azure AD (y, por tanto, el punto de conexión de servicio del proxy) se encuentra en Estados Unidos. Los aspectos descritos en estos casos de uso normalmente también se aplican a otras regiones de todo el mundo.

### <a name="use-case-1"></a>Caso de uso 1

La aplicación se encuentra en la red de una organización en los Estados Unidos con usuarios en la misma región. No hay ExpressRoute ni VPN entre el centro de datos de Azure y la red corporativa.

**Recomendación:** siga el patrón 1 explicado en la sección anterior. Para mejorar la latencia, considere la posibilidad de usar ExpressRoute, si es necesario.

Se trata de un modelo simple. Puede optimizar el salto 3 colocando el conector cerca de la aplicación. Se trata además de una opción natural, ya que el conector se instala normalmente con línea de visión hacia la aplicación y el centro de datos para realizar operaciones de KCD.

![Diagrama que muestra el esquema de Estados Unidos y cómo se organizan los saltos en este caso de uso](./media/application-proxy-network-topologies/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Caso de uso 2

La aplicación se encuentra en la red de una organización en los Estados Unidos con usuarios dispersos por todo el mundo. No hay ExpressRoute ni VPN entre el centro de datos de Azure y la red corporativa.

**Recomendación:** siga el patrón 2 explicado en la sección anterior. Para mejorar la latencia, considere la posibilidad de usar ExpressRoute, si es necesario.

Nuevamente, el patrón más común es optimizar el salto 3, donde el conector se coloca cerca de la aplicación. El salto 3 no es costoso por lo general si todo se encuentra dentro de la misma región. Sin embargo, el salto 1 puede resultar más costoso dependiendo de dónde esté el usuario, ya que todos los usuarios tendrán acceso a la instancia del Proxy de aplicación en Estados Unidos. Merece la pena mencionar que cualquier solución de proxy tendrá similares características con respecto a los usuarios que se reparten por todo el mundo.

![Diagrama que muestra el esquema de todos los continentes y cómo se organizan los saltos en este caso de uso](./media/application-proxy-network-topologies/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Caso de uso 3

La aplicación se encuentra en una red de una organización en los Estados Unidos. Hay una instancia de ExpressRoute con emparejamiento público entre Azure y la red corporativa.

**Recomendación:** coloque el conector lo más próximo posible a la aplicación. El sistema utilizará automáticamente ExpressRoute para el salto 2. Esto es una continuación del patrón 2 explicado en la sección anterior.

Si el vínculo de ExpressRoute utiliza emparejamiento público, el tráfico entre el proxy y el conector pasará por ese vínculo. El salto 2 tiene la latencia optimizada.

![Diagrama que muestra el esquema de Estados Unidos y cómo se organizan los saltos en este caso de uso](./media/application-proxy-network-topologies/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Caso de uso 4

La aplicación se encuentra en una red de una organización en los Estados Unidos. Hay una instancia de ExpressRoute con emparejamiento privado entre Azure y la red corporativa.

**Recomendación:** coloque el conector del centro de datos de Azure que esté conectado a la red corporativa a través del emparejamiento privado de ExpressRoute. Esto es una continuación del patrón 3 explicado en la sección anterior.

El conector puede colocarse en el centro de datos de Azure. Puesto que el conector todavía tiene una línea de visión hacia la aplicación y el centro de datos a través de la red privada, el salto 3 se mantiene optimizado. Además, el salto 2 se optimiza aún más.

![Diagrama que muestra el esquema de Estados Unidos y cómo se organizan los saltos en este caso de uso](./media/application-proxy-network-topologies/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Caso de uso 5

La aplicación se encuentra en la red de una organización en los Estados Unidos con la mayoría de los usuarios en Estados Unidos.

**Recomendación:** coloque el conector cerca de la aplicación. Puesto que los usuarios de Estados Unidos tienen acceso a una instancia del Proxy de aplicación que resulta estar en la misma región, el salto 1 no es demasiado costoso. El salto 3 se optimiza. Sin embargo, el salto 2 suele ser caro en este caso de uso.

![Diagrama que muestra el esquema de todos los continentes y cómo se organizan los saltos en este caso de uso](./media/application-proxy-network-topologies/application-proxy-pattern5a.png)

Considere el uso de ExpressRoute, como se describe en las secciones anteriores sobre los patrones 2 y 3.

![Diagrama que muestra el esquema de todos los continentes y cómo se organizan los saltos en este caso de uso](./media/application-proxy-network-topologies/application-proxy-pattern5b.png)

También puede considerar la utilización de alguna otra variante en esta situación. Si la mayoría de los usuarios de la organización están en Estados Unidos, lo más probable es que la red también se extienda a Estados Unidos. Si es así, el conector se puede colocar en Estados Unidos y usar la línea de red corporativa interna dedicada a la aplicación en la Unión Europea. De esta forma se optimizan los saltos 2 y 3.

![Diagrama que muestra el esquema de todos los continentes y cómo se organizan los saltos en este caso de uso](./media/application-proxy-network-topologies/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Pasos siguientes
[Habilitación del proxy de la aplicación](active-directory-application-proxy-enable.md)<br>
[Habilitar el inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)<br>
[Habilitar el acceso condicional](active-directory-application-proxy-conditional-access.md)<br>
[Solucionar los problemas que tiene con el Proxy de aplicación](active-directory-application-proxy-troubleshoot.md)

