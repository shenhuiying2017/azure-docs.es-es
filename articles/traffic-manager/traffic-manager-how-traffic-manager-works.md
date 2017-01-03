---
title: "Cómo funciona Traffic Manager | Microsoft Docs"
description: "Este artículo explica cómo funciona Azure Traffic Manager"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: a6c9370d-e60d-440f-aa82-b6d3fa5416b0
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: 6ba1732840d094497a9e2cb7a7ea7825a7f4bc89

---

# <a name="how-traffic-manager-works"></a>Cómo funciona el Administrador de tráfico

Azure Traffic Manager permite controlar la distribución del tráfico a través de los puntos de conexión de la aplicación. Un punto de conexión es cualquier servicio accesible desde Internet hospedado dentro o fuera de Azure.

El Administrador de tráfico ofrece dos ventajas principales:

1. La distribución del tráfico de acuerdo con uno o varios [métodos de enrutamiento de tráfico](traffic-manager-routing-methods.md)
2. [supervisión continua del estado de los puntos de conexión](traffic-manager-monitoring.md) y la conmutación por error automática en caso de error en estos

Cuando un cliente intenta conectarse a un servicio, debe resolver primero el nombre DNS del servicio en una dirección IP. Luego, el cliente se conecta a esa dirección IP para tener acceso al servicio.

**Es fundamental entender que el Administrador de tráfico funciona a nivel de DNS.**  Traffic Manager usa DNS para dirigir a los clientes a puntos de conexión específicos del servicio basados en las reglas del método de enrutamiento de tráfico. Los clientes se conectan **directamente**al punto de conexión seleccionado. Traffic Manager no es un proxy ni una puerta de enlace. Traffic Manager no ve el tráfico que circula entre el cliente y el servicio.

## <a name="traffic-manager-example"></a>Ejemplo de Administrador de tráfico

Contoso Corp ha desarrollado un nuevo portal para asociados. La dirección URL de este portal es https://partners.contoso.com/login.aspx. La aplicación está hospedada en tres regiones de Azure. Para mejorar la disponibilidad y maximizar el rendimiento global, usan Traffic Manager para distribuir el tráfico de cliente al punto de conexión disponible más próximo.

Para lograr esta configuración:

* Se implementan 3 instancias de su servicio. Los nombres DNS de estas implementaciones son "contoso-us.cloudapp.net", "contoso-eu.cloudapp.net" y "contoso-asia.cloudapp.net".
* A continuación, se crea un perfil de Traffic Manager denominado "contoso.trafficmanager.net", y este se configura para utilizar el método de enrutamiento de tráfico "Rendimiento" entre los 3 puntos de conexión.
* Por último, se configura un nombre de dominio personal, "partners.contoso.com", para que apunte a "contoso.trafficmanager.net" con un registro CNAME de DNS.

![Configuración de DNS del Administrador de tráfico][1]

> [!NOTE]
> Cuando se utiliza un dominio personal con el Administrador de tráfico de Azure, debe usar un registro CNAME para que el nombre de dominio personalizado apunte a su nombre de dominio del Administrador de tráfico. Los estándares DNS no permiten crear un registro CNAME en el "vértice" (o raíz) de un dominio. Por lo tanto no se puede crear un registro CNAME para "contoso.com" (lo que también se conoce como un dominio "desnudo"). Solo se puede crear un registro CNAME para un dominio bajo "contoso.com", como "www.contoso.com". Para solucionar esta limitación, se recomienda usar una sencilla redirección HTTP para dirigir las solicitudes de "contoso.com" a un nombre alternativo como "www.contoso.com".

## <a name="how-clients-connect-using-traffic-manager"></a>Conexión de clientes mediante el Administrador de tráfico

Continuando a partir del ejemplo anterior, cuando un cliente solicita la página https://partners.contoso.com/login.aspx, este realiza los pasos siguientes para resolver el nombre DNS y establecer una conexión:

![Establecimiento de la conexión mediante el Administrador de tráfico][2]

1. El cliente envía una consulta de DNS a su servicio DNS recursivo configurado para resolver el nombre "partners.contoso.com". Un servicio DNS recursivo, a veces denominado servicio "DNS local", no hospeda los dominios DNS directamente. En su lugar, el cliente descarga el trabajo de ponerse en contacto con los diversos servicios DNS autoritativos a través de Internet, lo cual es un paso necesario para resolver un nombre DNS.
2. Para resolver el nombre DNS, el servicio DNS recursivo busca entre los servidores de nombres el dominio "contoso.com". A continuación, se pone en contacto con esos servidores DNS a fin de solicitar el registro "partners.contoso.com". Los servidores DNS de contoso.com devuelven el registro CNAME que apunta a contoso.trafficmanager.net.
3. A continuación, el servicio DNS recursivo busca los servidores DNS para el dominio trafficmanager.net, que se proporcionan con el servicio Azure Traffic Manager. A continuación, envía una solicitud para el registro DNS de "contoso.trafficmanager.net" a esos servidores DNS.
4. Los servidores DNS del Administrador de tráfico reciben la solicitud. Eligen un punto de conexión en función de:

    - El estado configurado de cada punto de conexión (no se devuelven los puntos de conexión deshabilitados).
    - El estado actual de cada punto de conexión, según lo determinado por las comprobaciones de estado del Administrador de tráfico. Para más información, consulte [Acerca de la supervisión del Administrador de tráfico](traffic-manager-monitoring.md).
    - El método de enrutamiento de tráfico elegido. Para más información, consulte [Métodos de enrutamiento de Traffic Manager](traffic-manager-routing-methods.md).

5. El punto de conexión elegido se devuelve como otro registro CNAME de DNS. En este caso, supongamos que contoso-us.cloudapp.net se devuelve.
6. A continuación, el servicio DNS recursivo busca los servidores DNS para el dominio "cloudapp.net". Se pone en contacto con estos servidores DNS para solicitar el registro DNS "contoso-us.cloudapp.net". Se devuelve un registro "A" de DNS que contiene la dirección IP del punto de conexión de servicio basado en Estados Unidos.
7. El servicio DNS recursivo consolida los resultados y devuelve una única respuesta DNS al cliente.
8. El cliente recibe los resultados DNS y se conecta a la dirección IP especificada. El cliente se conecta al punto de conexión de servicio de la aplicación directamente, no mediante Traffic Manager. Puesto que es un punto de conexión HTTPS, el cliente lleva a cabo el protocolo de enlace SSL/TLS necesario y, a continuación, realiza una solicitud GET de HTTP para la página "/login.aspx".

El servicio DNS recursivo almacena en la memoria caché las respuestas DNS que recibe. La resolución DNS del dispositivo cliente también almacena en caché el resultado. El almacenamiento en caché permite que las consultas DNS posteriores se respondan más rápidamente al utilizar datos de la memoria caché en lugar de consultar otros servidores DNS. La duración de la memoria caché viene determinada por la propiedad de período de vida (TTL) de cada registro DNS. Unos valores más cortos producen una expiración de caché más rápida y, por tanto, más recorridos de ida y vuelta a los servidores de nombres de Traffic Manager. Unos valores mayores significan que se puede tardar más tiempo en alejar el tráfico de un punto de conexión con error. El Administrador de tráfico permite configurar el valor TTL utilizado en las respuestas DNS del Administrador de tráfico, lo que le permite elegir el valor que responda mejor a las necesidades de su aplicación.

## <a name="faq"></a>P+F

### <a name="what-ip-address-does-traffic-manager-use"></a>¿Qué dirección IP usa el Administrador de tráfico?

Tal y como se explica al comienzo de este artículo, este servicio funciona utilizando respuestas DNS Envía respuestas DNS para dirigir a los clientes al punto de conexión de servicio adecuado. Después, los clientes se conectan directamente al punto de conexión de servicio, y no a través del Administrador de tráfico.

Por lo tanto, este servicio no proporciona un punto de conexión o una dirección IP para que los clientes puedan conectarse. Por tanto, si desea una dirección IP estática para el servicio, esta debe configurarse en el servicio y no en Traffic Manager.

### <a name="does-traffic-manager-support-sticky-sessions"></a>¿Admite Traffic Manager sesiones temporales?

Tal y como explicamos [anteriormente](#how-clients-connect-using-traffic-manager), Traffic Manager funciona utilizando respuestas DNS. para dirigir a los clientes al punto de conexión de servicio adecuado. Los clientes se conectan directamente al punto de conexión de servicio, y no a través de Traffic Manager. Por tanto, Traffic Manager no ve el tráfico HTTP que circula entre el cliente y el servidor.

Además, la dirección IP de origen de la consulta de DNS que recibe Traffic Manager pertenece al servicio DNS recursivo, no al cliente. Por lo tanto, Traffic Manager no tiene forma de realizar un seguimiento de los clientes individuales y no puede implementar sesiones temporales. Esta limitación es común en todos los sistemas de administración de tráfico basado en DNS; no se trata de una limitación específica de Traffic Manager.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>¿Por qué obtengo un error HTTP al utilizar Traffic Manager?

Tal y como explicamos [anteriormente](#how-clients-connect-using-traffic-manager), Traffic Manager funciona utilizando respuestas DNS. para dirigir a los clientes al punto de conexión de servicio adecuado. Después, los clientes se conectan directamente al punto de conexión de servicio, y no a través del Administrador de tráfico. Traffic Manager no ve el tráfico HTTP que circula entre cliente y servidor. Por tanto, todos los errores HTTP que obtenga deben provenir de la aplicación. Para que el cliente pueda conectarse a la aplicación, todos los pasos de la resolución DNS deben estar completos. Esto incluye cualquier interacción que tenga Traffic Manager en el flujo de tráfico de la aplicación.

Por tanto, debe llevar a cabo una investigación más extensa centrándose en la aplicación.

El encabezado host HTTP enviado desde el explorador del cliente es la fuente más habitual de problemas. Asegúrese de que la aplicación está configurada para aceptar el encabezado host correcto para el nombre de dominio que use. Para los puntos de conexión que utilizan Azure App Service, consulte [Configuración de un nombre de dominio personalizado para una aplicación web en Azure App Service mediante Traffic Manager](../app-service-web/web-sites-traffic-manager-custom-domain-name.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>¿Cómo afecta al rendimiento el uso del Administrador de tráfico?

Tal y como explicamos [anteriormente](#how-clients-connect-using-traffic-manager), Traffic Manager funciona utilizando respuestas DNS. Puesto que los clientes se conectan directamente a los puntos de conexión de servicio, el rendimiento no se verá afectado cuando se utiliza el Administrador de tráfico una vez establecida la conexión.

Como el Administrador de tráfico se integra con las aplicaciones en el nivel de DNS, hay que insertar una búsqueda DNS adicional en la cadena de resolución DNS (consulte [Ejemplo de Administrador de tráfico](#traffic-manager-example)). El impacto del Administrador de tráfico en el tiempo de resolución DNS es mínimo. Traffic Manager usa una red global de servidores de nombres y redes de difusión por proximidad [anycast](https://en.wikipedia.org/wiki/Anycast) para asegurarse de que siempre se enruten las consultas de DNS en el servidor de nombres disponible más cercano. Además, como las respuestas DNS se almacenan en caché, la latencia DNS adicional que se genera por utilizar Administrador de tráfico se aplica solo a una fracción de las sesiones.

El método Rendimiento enruta el tráfico al punto de conexión más cercano disponible. Como resultado, el impacto en el rendimiento general asociado a este método debería ser mínimo. Cualquier aumento en la latencia de DNS se debería reemplazar por una menor latencia de red al punto de conexión.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>¿Qué protocolos de aplicación puedo usar con el Administrador de tráfico?

Tal y como explicamos [anteriormente](#how-clients-connect-using-traffic-manager), Traffic Manager funciona utilizando respuestas DNS. Una vez finalizada la búsqueda DNS, los clientes se conectan directamente al punto de conexión de la aplicación, y no a través del Administrador de tráfico. Por tanto, la conexión puede emplear cualquier protocolo de aplicación. Sin embargo, para realizar las comprobaciones de estado de Traffic Manager se requiere un punto de conexión HTTP o HTTPS. El punto de conexión de una comprobación de estado puede ser diferente al punto de conexión de la aplicación al que se conectan los clientes.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>¿Puedo usar Traffic Manager con un nombre de dominio desnudo?

No. Los estándares DNS no permiten que los registros CNAME coexistan con otros registros DNS del mismo nombre. El vértice (o raíz) de una zona DNS siempre contiene dos registros DNS preexistentes; los registros SOA y NS autoritativos. Esto significa que no se puede crear un registro CNAME en el vértice de la zona sin infringir los estándares DNS.

Como se explica en el [ejemplo de Traffic Manager](#traffic-manager-example), este requiere un registro CNAME de DNS para asignar el nombre DNS del dominio personal. Por ejemplo, asigne www.contoso.com al nombre DNS del perfil de Traffic Manager contoso.trafficmanager.net. Además, este perfil devuelve un segundo registro CNAME de DNS para indicar a qué punto de conexión debe conectarse el cliente.

Para solucionar este problema, se recomienda utilizar una redirección HTTP para dirigir el tráfico desde el nombre de dominio desnudo a una dirección URL diferente que, a continuación, puede utilizar Traffic Manager. Por ejemplo, el dominio desnudo "contoso.com" puede redirigir a los usuarios al registro CNAME "www.contoso.com" que apunta al nombre DNS de Traffic Manager.

En nuestra cola de trabajos pendientes de características realizamos un seguimiento de los dominios vacíos que son totalmente compatibles con el Administrador de tráfico. Puede dar su apoyo a esta solicitud de característica [votando por ella en nuestro sitio de comentarios de la comunidad](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly).

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre la [supervisión del punto de conexión y la conmutación por error automática](traffic-manager-monitoring.md)del Administrador de tráfico.

Obtenga más información sobre los [métodos de enrutamiento del tráfico](traffic-manager-routing-methods.md)del Administrador de tráfico.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png



<!--HONumber=Jan17_HO1-->


