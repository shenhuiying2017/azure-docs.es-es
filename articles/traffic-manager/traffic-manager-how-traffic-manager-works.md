<properties
   pageTitle="Cómo funciona el Administrador de tráfico | Microsoft Azure"
   description="Este artículo le ayudará a comprender el funcionamiento del Administrador de tráfico de Azure"
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn"/>

<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/07/2016"
   ms.author="sewhee"/>

# Cómo funciona el Administrador de tráfico

El Administrador de tráfico de Azure permite controlar cómo se distribuye el tráfico a través de los puntos de conexión de la aplicación. Un punto de conexión puede ser cualquiera que esté orientado a Internet, esté o no hospedado en Azure.

El Administrador de tráfico ofrece dos ventajas principales:

1. La distribución del tráfico de acuerdo con uno o varios [métodos de enrutamiento de tráfico](traffic-manager-routing-methods.md)
2. La [supervisión continua del estado de los puntos de conexión](traffic-manager-monitoring.md) y la conmutación por error automática en caso de error en estos

Cuando un usuario final intenta conectarse a un punto de conexión de servicio, el cliente (PC, teléfono, etc.) debe resolver en primer lugar el nombre DNS de ese punto de conexión en una dirección IP. Luego, el cliente se conecta a esa dirección IP para tener acceso al servicio.

**Es fundamental entender que el Administrador de tráfico funciona a nivel de DNS.** El Administrador de tráfico utiliza DNS para dirigir a los usuarios finales a puntos de conexión de servicio determinados, según el método de enrutamiento de tráfico elegido y el estado del punto de conexión actual. Los clientes se conectan luego **directamente** al punto de conexión seleccionado. El Administrador de tráfico no es un proxy y no ve el tráfico que circula entre el cliente y el servicio.

## Ejemplo de Administrador de tráfico

Contoso Corp ha desarrollado un nuevo portal para asociados. La dirección URL de este portal será https://partners.contoso.com/login.aspx. La aplicación se hospeda en Azure, y para mejorar la disponibilidad y maximizar el rendimiento global, la empresa desea implementar la aplicación en 3 regiones en todo el mundo y usar el Administrador de tráfico para dirigir a los usuarios finales al punto de conexión disponible más cercano.

Para lograr esta configuración:

- En la empresa se implementan 3 instancias de su servicio. Los nombres DNS de estas implementaciones son "contoso-us.cloudapp.net", "contoso-eu.cloudapp.net" y "contoso-asia.cloudapp.net".
- A continuación, se crea un perfil de Administrador de tráfico con el nombre "contoso.trafficmanager.net", que está configurado para utilizar el método de enrutamiento de tráfico "Rendimiento" entre los 3 puntos de conexión mencionados anteriormente.
- Por último, se configura un dominio personal, "partners.contoso.com", para que apunte a "contoso.trafficmanager.net" con un registro CNAME de DNS.

![Configuración de DNS del Administrador de tráfico][1]

> [AZURE.NOTE] Cuando se utiliza un dominio personal con el Administrador de tráfico de Azure, debe usar un registro CNAME para que el nombre de dominio personalizado apunte a su nombre de dominio del Administrador de tráfico.

> Debido a una restricción de los estándares DNS, no se puede crear un CNAME en la "cúspide" (o raíz) de un dominio. Por lo tanto no se puede crear un registro CNAME para "contoso.com" (lo que también se conoce como un dominio "desnudo"). Solo se puede crear un registro CNAME para un dominio bajo "contoso.com", como "www.contoso.com".

> Por lo tanto no se puede usar el Administrador de tráfico directamente con un dominio desnudo. Para solucionar este problema, se recomienda usar una sencilla redirección HTTP para dirigir las solicitudes de "contoso.com" a un nombre alternativo como "www.contoso.com".

## Conexión de clientes mediante el Administrador de tráfico

Cuando un usuario solicita la página https://partners.contoso.com/login.aspx (como se describe en el ejemplo anterior), el cliente realiza los pasos siguientes para resolver el nombre de DNS y establecer una conexión.

![Establecimiento de la conexión mediante el Administrador de tráfico][2]

1.	El cliente (PC, teléfono, etc.) realiza una consulta de DNS a "partners.contoso.com" en su servicio DNS recursivo configurado. (Un servicio DNS recursivo, a veces denominado servicio "DNS local", no hospeda los dominios DNS directamente. En su lugar, el cliente lo usa para descargar el trabajo de ponerse en contacto con los diversos servicios DNS autoritativos a través de Internet, lo cual es paso necesario para resolver un nombre DNS).
2.	Ahora, el servicio DNS recursivo resuelve el nombre DNS "partners.contoso.com". En primer lugar, el servicio DNS recursivo busca entre los servidores DNS el dominio "contoso.com". A continuación, se pone en contacto con esos servidores DNS a fin de solicitar el registro "partners.contoso.com". Se obtiene el registro CNAME para contoso.trafficmanager.net.
3.	El servicio DNS recursivo ahora busca los servidores DNS para el dominio trafficmanager.net, que se proporcionan con el servicio Administrador de tráfico de Azure. Luego se pone en contacto con estos servidores DNS para solicitar el registro DNS "contoso.trafficmanager.net".
4.	Los servidores DNS del Administrador de tráfico reciben la solicitud. En ese momento, estos eligen qué punto de conexión debe devolverse, tomando en consideración para ello lo siguiente: a. El estado habilitado o deshabilitado de cada punto de conexión (no se devuelven los puntos de conexión deshabilitados). b. El estado actual de cada punto de conexión, según lo determinado por las comprobaciones de estado del Administrador de tráfico. Para más información, consulte Acerca de la supervisión del Administrador de tráfico. c. El método de enrutamiento de tráfico elegido. Para más información, consulte Métodos de enrutamiento del Administrador de tráfico.
5.	El punto de conexión elegido se devuelve como otro registro CNAME de DNS; en este caso, supongamos que es contoso-us.cloudapp.net.
6.	Ahora, el servicio DNS recursivo busca los servidores DNS para el dominio "cloudapp.net". Se pone en contacto con estos servidores DNS para solicitar el registro DNS "contoso-us.cloudapp.net". Se devuelve un registro "A" de DNS que contiene la dirección IP del punto de conexión de servicio basado en Estados Unidos.
7.	El servicio DNS recursivo devuelve los resultados consolidados de la secuencia anterior de resoluciones de nombres al cliente.
8.	El cliente recibe los resultados DNS del servicio DNS recursivo y se conecta a la dirección IP especificada. Tenga en cuenta que se conecta al punto de conexión de servicio de la aplicación directamente, no mediante el Administrador de tráfico. Puesto que es un punto de conexión HTTPS, lleva a cabo el protocolo de enlace SSL/TLS necesario y, a continuación, realiza una solicitud GET de HTTP para la página "/login.aspx".

Tenga en cuenta que el servicio DNS recursivo almacenará en caché las respuestas DNS que reciba, al igual que hará el cliente DNS en el dispositivo del usuario final. De este modo, las consultas DNS posteriores se responderán más rápidamente al utilizar datos de la memoria caché en lugar de consultar otros servidores DNS. La duración de la memoria caché viene determinada por la propiedad de período de vida (TTL) de cada registro DNS. Unos valores más breves darán lugar a una expiración de la caché más rápida y, por tanto, a más consultas a los servidores DNS del Administrador de tráfico; unos valores más largos implican que es posible que se tarde más en redirigir el tráfico desde un punto de conexión no válido. El Administrador de tráfico permite configurar el valor TTL utilizado en las respuestas DNS del Administrador de tráfico, lo que le permite elegir el valor que responda mejor a las necesidades de su aplicación.

## P+F

### ¿Qué dirección IP usa el Administrador de tráfico?

Tal y como se explica al comienzo de este artículo, este servicio funciona utilizando respuestas DNS para dirigir a los clientes al punto de conexión de servicio adecuado. Después, los clientes se conectan directamente al punto de conexión de servicio, y no a través del Administrador de tráfico.

Por lo tanto, este servicio no proporciona un punto de conexión o una dirección IP para que los clientes puedan conectarse. En resumen, se requiere, por ejemplo, una dirección IP estática que debe configurarse en el servicio, no en el Administrador de tráfico.

### ¿Admite el Administrador de tráfico sesiones temporales?

Tal y como explicamos [anteriormente](#how-clients-connect-using-traffic-manager), este servicio funciona utilizando respuestas DNS para dirigir a los clientes al punto de conexión de servicio adecuado. Después, los clientes se conectan directamente al punto de conexión de servicio, y no a través del Administrador de tráfico. Por lo tanto, el Administrador de tráfico no observa el tráfico HTTP entre cliente y servidor, ni tampoco las cookies.

Además, tenga en cuenta que la dirección IP de origen de la consulta de DNS que recibe el Administrador de tráfico es la del servicio DNS recursivo, no la dirección IP del cliente.

Por lo tanto, el Administrador de tráfico no tiene forma de identificar clientes individuales ni de realizar un seguimiento de ellos, así que no puede implementar sesiones temporales. Esto es algo común en todos los sistemas de administración de tráfico basado en DNS; no se trata de una restricción del Administrador de tráfico.

### ¿Por qué obtengo un error HTTP al utilizar el Administrador de tráfico?

Tal y como explicamos [anteriormente](#how-clients-connect-using-traffic-manager), este servicio funciona utilizando respuestas DNS para dirigir a los clientes al punto de conexión de servicio adecuado. Después, los clientes se conectan directamente al punto de conexión de servicio, y no a través del Administrador de tráfico.

Por lo tanto, el Administrador de tráfico no observa el tráfico HTTP entre cliente y servidor, ni tampoco puede generar errores HTTP. Todos los errores HTTP que obtenga deben provenir de la aplicación. Como el cliente se conecta a la aplicación, esto también significa que debe haberse completado la resolución DNS, incluido el rol de Administrador de tráfico.

Por tanto, debe llevar a cabo una investigación más extensa centrándose en la aplicación.

Un problema común sucede cuando se utiliza el Administrador de tráfico. El encabezado HTTP de host que transmite el explorador a la aplicación mostrará el nombre de dominio que emplea el explorador. Puede ser el nombre de dominio del Administrador de tráfico (por ejemplo, miperfil.trafficmanager.net) si lo utiliza durante las pruebas, o bien el registro CNAME de dominio personal configurado para apuntar al nombre de dominio del Administrador de tráfico. En cualquier caso, compruebe que la aplicación está configurada para aceptar este encabezado de host.

Si la aplicación se hospeda en el Servicio de aplicaciones de Azure, consulte [Configuración de un nombre de dominio personalizado para una aplicación web en Servicio de aplicaciones de Azure utilizando el Administrador de tráfico](../app-service-web/web-sites-traffic-manager-custom-domain-name.md).

### ¿Cómo afecta al rendimiento el uso del Administrador de tráfico?

Tal y como explicamos [anteriormente](#how-clients-connect-using-traffic-manager), este servicio funciona utilizando respuestas DNS para dirigir a los clientes al punto de conexión de servicio adecuado. Después, los clientes se conectan directamente al punto de conexión de servicio, y no a través del Administrador de tráfico.

Puesto que los clientes se conectan directamente a los puntos de conexión de servicio, el rendimiento no se verá afectado cuando se utiliza el Administrador de tráfico una vez establecida la conexión.

Como el Administrador de tráfico se integra con las aplicaciones en el nivel de DNS, hay que insertar una búsqueda DNS adicional en la cadena de resolución DNS (consulte [Ejemplo de Administrador de tráfico](#traffic-manager-example)). El impacto del Administrador de tráfico en el tiempo de resolución DNS es mínimo. El Administrador de tráfico usa una red global de servidores de nombres y redes de difusión por proximidad (anycast) para asegurarse de que siempre se enruten las consultas de DNS en el servidor de nombres disponible más cercano. Además, como las respuestas DNS se almacenan en caché, la latencia DNS adicional que se genera por utilizar Administrador de tráfico se aplica solo a una fracción de las sesiones.

Como consecuencia, el impacto de rendimiento general asociado a incorporar el Administrador de tráfico en una aplicación debería ser mínimo.

Además, en los sitios donde se utiliza el [método de enrutamiento de tráfico "Rendimiento"](traffic-manager-routing-methods.md#performance-traffic-routing-method), el aumento de la latencia DNS debe quedar más que compensado con la mejora de rendimiento que se obtiene al dirigir a los usuarios finales a su punto de conexión disponible más cercano.

### ¿Qué protocolos de aplicación puedo usar con el Administrador de tráfico?
Tal y como explicamos [anteriormente](#how-clients-connect-using-traffic-manager), este servicio funciona utilizando respuestas DNS. Una vez finalizada la búsqueda DNS, los clientes se conectan directamente al punto de conexión de la aplicación, y no a través del Administrador de tráfico. Por tanto, esta conexión puede emplear cualquier protocolo de aplicación.

Sin embargo, para realizar las comprobaciones de estado del Administrador de tráfico se requiere un punto de conexión HTTP o HTTPS. Puede ser independiente del punto de conexión de la aplicación al que los clientes se conectan si se especifica una ruta de acceso URI o un puerto TCP distintos en la configuración de comprobación de estado de perfil del Administrador de tráfico.

### ¿Puedo usar el Administrador de tráfico con un nombre de dominio vacío (sin el "www")?

Actualmente, no.

El tipo de registro CNAME de DNS se utiliza para crear una asignación de nombre DNS a otro. Tal y como se explica en la sección [Ejemplo de Administrador de tráfico](#traffic-manager-example), este servicio requiere un registro CNAME de DNS para asignar el nombre DNS personal (por ejemplo, www.contoso.com) al nombre DNS del perfil de Administrador de tráfico (por ejemplo, contoso.trafficmanager.net). Además, este perfil devuelve un segundo registro CNAME de DNS para indicar a qué punto de conexión debe conectarse el cliente.

Los estándares DNS no permiten que los registros CNAME coexistan con otros registros DNS del mismo tipo. Puesto que el vértice (o raíz) de una zona DNS siempre contiene dos registros DNS existentes (los SOA y los NS autoritativos), no podrá crearse un registro CNAME en el vértice de zona sin infringir los estándares DNS.

Para evitar este problema, recomendamos que los servicios que empleen un dominio vacío (sin el "www") que quiera que utilice el Administrador de tráfico usen el redireccionamiento de HTTP para dirigir el tráfico del dominio vacío a una dirección URL diferente, que, luego, puede emplear el Administrador de tráfico. Por ejemplo, el dominio vacío contoso.com puede redirigir a los usuarios a www.contoso.com que, posteriormente, podrá utilizar el Administrador de tráfico.

En nuestra cola de trabajos pendientes de características realizamos un seguimiento de los dominios vacíos que son totalmente compatibles con el Administrador de tráfico. Si le interesa esta característica, registre su idea [votando por ella en nuestro sitio de comentarios de la comunidad](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly).

## Pasos siguientes

Obtenga más información sobre la [supervisión del punto de conexión y la conmutación por error automática](traffic-manager-monitoring.md) del Administrador de tráfico.

Obtenga más información sobre los [métodos de enrutamiento del tráfico](traffic-manager-routing-methods.md) del Administrador de tráfico.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

<!---HONumber=AcomDC_0824_2016-->