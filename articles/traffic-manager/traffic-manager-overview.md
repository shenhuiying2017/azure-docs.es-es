---
title: "¿Qué es Traffic Manager? | Microsoft Docs"
description: "Este artículo lo ayudará a entender qué es Traffic Manager y si se trata de la opción de enrutamiento de tráfico adecuada para su aplicación."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2017
ms.author: kumud
ms.openlocfilehash: 50d7f14d0d4234ee98d8a46e903b5f916cb02fab
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="overview-of-traffic-manager"></a>Introducción a Traffic Manager

Microsoft Azure Traffic Manager permite controlar la distribución del tráfico de los usuarios para puntos de conexión de servicio en distintos centros de datos. Entre los puntos de conexión de servicio compatibles con Traffic Manager, se incluyen máquinas virtuales de Azure, Web Apps y servicios en la nube. También puede utilizar el Administrador de tráfico con puntos de conexión externos, que no forman parte de Azure.

Traffic Manager usa el sistema de nombres de dominio (DNS) para dirigir las solicitudes del cliente al punto de conexión más adecuado en función de un método de enrutamiento del tráfico y el estado de los puntos de conexión. Traffic Manager proporciona una serie de [métodos de enrutamiento del tráfico](traffic-manager-routing-methods.md) y [opciones de supervisión del punto de conexión](traffic-manager-monitoring.md) para satisfacer las distintas necesidades de las aplicaciones y los modelos de conmutación automática por error. Traffic Manager es resistente a errores, incluidos los que afecten a toda una región de Azure.

## <a name="traffic-manager-benefits"></a>Ventajas de Traffic Manager

Traffic Manager puede ayudarle a:

* **Mejorar la disponibilidad de las aplicaciones esenciales**

    Traffic Manager ofrece alta disponibilidad para las aplicaciones al supervisar los puntos de conexión y proporcionar la conmutación automática por error cuando un punto de conexión deja de funcionar.

* **Mejorar la capacidad de respuesta para las aplicaciones de alto rendimiento**

    Azure permite ejecutar servicios en la nube en los centros de datos ubicados en todo el mundo. Traffic Manager mejora la capacidad de respuesta de las aplicaciones al dirigir el tráfico al punto de conexión con la menor latencia de red para el cliente.

* **Realizar el mantenimiento del servicio sin tiempo de inactividad**

    Puede realizar operaciones de mantenimiento planeado en sus aplicaciones sin tiempo de inactividad. Traffic Manager dirige el tráfico a puntos de conexión alternativos mientras el mantenimiento está en curso.

* **Combinar aplicaciones locales y basadas en la nube**

    Traffic Manager admite puntos de conexión externos (que no forman parte de Azure) que pueden emplearse en implementaciones locales y de nube híbrida, entre ellos los escenarios de implementación, migración y conmutación por error a la nube.

* **Distribuir el tráfico para implementaciones grandes y complejas**

    Mediante los [perfiles anidados de Traffic Manager](traffic-manager-nested-profiles.md), se pueden combinar métodos de enrutamiento del tráfico para crear reglas complejas y flexibles que satisfagan las necesidades de las implementaciones más grandes y complejas.

## <a name="how-traffic-manager-works"></a>Cómo funciona Traffic Manager

Azure Traffic Manager permite controlar la distribución del tráfico a través de los puntos de conexión de la aplicación. Un punto de conexión es cualquier servicio accesible desde Internet hospedado dentro o fuera de Azure.

Traffic Manager ofrece dos ventajas principales:

1. La distribución del tráfico de acuerdo con uno o varios [métodos de enrutamiento de tráfico](traffic-manager-routing-methods.md)
2. [supervisión continua del estado de los puntos de conexión](traffic-manager-monitoring.md) y la conmutación por error automática en caso de error en estos

Cuando un cliente intenta conectarse a un servicio, debe resolver primero el nombre DNS del servicio en una dirección IP. Luego, el cliente se conecta a esa dirección IP para tener acceso al servicio.

**Es fundamental entender que Traffic Manager funciona a nivel de DNS.**  Traffic Manager usa DNS para dirigir a los clientes a puntos de conexión específicos del servicio basados en las reglas del método de enrutamiento de tráfico. Los clientes se conectan **directamente**al punto de conexión seleccionado. Traffic Manager no es un proxy ni una puerta de enlace. Traffic Manager no ve el tráfico que circula entre el cliente y el servicio.

### <a name="traffic-manager-example"></a>Ejemplo de Traffic Manager

Contoso Corp ha desarrollado un nuevo portal para asociados. La dirección URL de este portal es https://partners.contoso.com/login.aspx. La aplicación está hospedada en tres regiones de Azure. Para mejorar la disponibilidad y maximizar el rendimiento global, usan Traffic Manager para distribuir el tráfico de cliente al punto de conexión disponible más próximo.

Para lograr esta configuración, se han completado los pasos siguientes:

1. Se implementan tres instancias de su servicio. Los nombres DNS de estas implementaciones son "contoso-us.cloudapp.net", "contoso-eu.cloudapp.net" y "contoso-asia.cloudapp.net".
2. Se crea un perfil de Traffic Manager denominado "contoso.trafficmanager.net" y este se configura para utilizar el método de enrutamiento de tráfico "Rendimiento" entre los tres puntos de conexión.
* Se configura un nombre de dominio personal, "partners.contoso.com", para que apunte a "contoso.trafficmanager.net" con un registro CNAME de DNS.

![Configuración de DNS de Traffic Manager][1]

> [!NOTE]
> Cuando se utiliza un dominio personal con Azure Traffic Manager, debe usar un registro CNAME para que el nombre de dominio personalizado apunte a su nombre de dominio de Traffic Manager. Los estándares DNS no permiten crear un registro CNAME en el "vértice" (o raíz) de un dominio. Por lo tanto no se puede crear un registro CNAME para "contoso.com" (lo que también se conoce como un dominio "desnudo"). Solo se puede crear un registro CNAME para un dominio bajo "contoso.com", como "www.contoso.com". Para solucionar esta limitación, se recomienda usar una sencilla redirección HTTP para dirigir las solicitudes de "contoso.com" a un nombre alternativo como "www.contoso.com".

### <a name="how-clients-connect-using-traffic-manager"></a>Conexión de clientes mediante Traffic Manager

Continuando a partir del ejemplo anterior, cuando un cliente solicita la página https://partners.contoso.com/login.aspx, este realiza los pasos siguientes para resolver el nombre DNS y establecer una conexión:

![Establecimiento de la conexión mediante Traffic Manager][2]

1. El cliente envía una consulta de DNS a su servicio DNS recursivo configurado para resolver el nombre "partners.contoso.com". Un servicio DNS recursivo, a veces denominado servicio "DNS local", no hospeda los dominios DNS directamente. En su lugar, el cliente descarga el trabajo de ponerse en contacto con los diversos servicios DNS autoritativos a través de Internet, lo cual es un paso necesario para resolver un nombre DNS.
2. Para resolver el nombre DNS, el servicio DNS recursivo busca entre los servidores de nombres el dominio "contoso.com". A continuación, se pone en contacto con esos servidores DNS a fin de solicitar el registro "partners.contoso.com". Los servidores DNS de contoso.com devuelven el registro CNAME que apunta a contoso.trafficmanager.net.
3. A continuación, el servicio DNS recursivo busca los servidores DNS para el dominio trafficmanager.net, que se proporcionan con el servicio Azure Traffic Manager. A continuación, envía una solicitud para el registro DNS de "contoso.trafficmanager.net" a esos servidores DNS.
4. Los servidores DNS de Traffic Manager reciben la solicitud. Eligen un punto de conexión en función de:

    - El estado configurado de cada punto de conexión (no se devuelven los puntos de conexión deshabilitados).
    - El estado actual de cada punto de conexión, según lo determinado por las comprobaciones de estado de Traffic Manager. Para más información, consulte [Acerca de la supervisión de Traffic Manager](traffic-manager-monitoring.md).
    - El método de enrutamiento de tráfico elegido. Para más información, consulte [Métodos de enrutamiento de Traffic Manager](traffic-manager-routing-methods.md).

5. El punto de conexión elegido se devuelve como otro registro CNAME de DNS. En este caso, supongamos que contoso-us.cloudapp.net se devuelve.
6. A continuación, el servicio DNS recursivo busca los servidores DNS para el dominio "cloudapp.net". Se pone en contacto con estos servidores DNS para solicitar el registro DNS "contoso-us.cloudapp.net". Se devuelve un registro "A" de DNS que contiene la dirección IP del punto de conexión de servicio basado en Estados Unidos.
7. El servicio DNS recursivo consolida los resultados y devuelve una única respuesta DNS al cliente.
8. El cliente recibe los resultados DNS y se conecta a la dirección IP especificada. El cliente se conecta al punto de conexión de servicio de la aplicación directamente, no mediante Traffic Manager. Puesto que es un punto de conexión HTTPS, el cliente lleva a cabo el protocolo de enlace SSL/TLS necesario y, a continuación, realiza una solicitud GET de HTTP para la página "/login.aspx".

El servicio DNS recursivo almacena en la memoria caché las respuestas DNS que recibe. La resolución DNS del dispositivo cliente también almacena en caché el resultado. El almacenamiento en caché permite que las consultas DNS posteriores se respondan más rápidamente al utilizar datos de la memoria caché en lugar de consultar otros servidores DNS. La duración de la memoria caché viene determinada por la propiedad de período de vida (TTL) de cada registro DNS. Unos valores más cortos producen una expiración de caché más rápida y, por tanto, más recorridos de ida y vuelta a los servidores de nombres de Traffic Manager. Unos valores mayores significan que se puede tardar más tiempo en alejar el tráfico de un punto de conexión con error. Traffic Manager permite configurar el valor de TTL utilizado en las respuestas DNS de Traffic Manager entre 0 segundos y 2.147.483.647 segundos (el intervalo máximo compatible con [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt)), lo que le permite elegir el valor que responda mejor a las necesidades de su aplicación.

## <a name="pricing"></a>Precios

Para obtener información de precios, consulte la página de [precios de Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faq"></a>P+F

Para ver las preguntas más frecuentes sobre Traffic Manager, vea [Preguntas más frecuentes (P+F) sobre Traffic Manager](traffic-manager-FAQs.md).

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre la [supervisión del punto de conexión y la conmutación por error automática](traffic-manager-monitoring.md) de Traffic Manager.

Obtenga más información sobre los [métodos de enrutamiento del tráfico](traffic-manager-routing-methods.md) de Traffic Manager.

Obtenga información sobre las demás [funcionalidades de red](../networking/networking-overview.md) clave de Azure.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

