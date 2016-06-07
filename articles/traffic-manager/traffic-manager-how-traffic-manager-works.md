<properties
   pageTitle="Cómo funciona el Administrador de tráfico | Microsoft Azure"
   description="Este artículo le ayudará a comprender el funcionamiento del Administrador de tráfico de Azure"
   services="traffic-manager"
   documentationCenter=""
   authors="jtuliani"
   manager="carmonm"
   editor="tysonn"/>

<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/25/2016"
   ms.author="jonatul"/>

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
3.	El servicio DNS recursivo ahora busca los servidores DNS para el dominio "trafficmanager.net", que se proporcionan con el servicio Administrador de tráfico de Azure. Luego se pone en contacto con estos servidores DNS para solicitar el registro DNS "contoso.trafficmanager.net".
4.	Los servidores DNS del Administrador de tráfico reciben la solicitud. En ese momento, estos eligen qué punto de conexión debe devolverse, tomando en consideración para ello lo siguiente: a. El estado habilitado o deshabilitado de cada punto de conexión (no se devuelven los puntos de conexión deshabilitados). b. El estado actual de cada punto de conexión, según lo determinado por las comprobaciones de estado del Administrador de tráfico. Para más información, consulte Acerca de la supervisión del Administrador de tráfico. c. El método de enrutamiento de tráfico elegido. Para más información, consulte Métodos de enrutamiento del Administrador de tráfico.
5.	El punto de conexión elegido se devuelve como otro registro CNAME de DNS; en este caso, supongamos que es contoso-us.cloudapp.net.
6.	Ahora, el servicio DNS recursivo busca los servidores DNS para el dominio "cloudapp.net". Se pone en contacto con estos servidores DNS para solicitar el registro DNS "contoso-us.cloudapp.net". Se devuelve un registro "A" de DNS que contiene la dirección IP del punto de conexión de servicio basado en Estados Unidos.
7.	El servicio DNS recursivo devuelve los resultados consolidados de la secuencia anterior de resoluciones de nombres al cliente.
8.	El cliente recibe los resultados DNS del servicio DNS recursivo y se conecta a la dirección IP especificada. Tenga en cuenta que se conecta al punto de conexión de servicio de la aplicación directamente, no mediante el Administrador de tráfico. Puesto que es un punto de conexión HTTPS, lleva a cabo el protocolo de enlace SSL/TLS necesario y, a continuación, realiza una solicitud GET de HTTP para la página "/login.aspx".

Tenga en cuenta que el servicio DNS recursivo almacenará en caché las respuestas DNS que reciba, al igual que hará el cliente DNS en el dispositivo del usuario final. De este modo, las consultas DNS posteriores se responderán más rápidamente al utilizar datos de la memoria caché en lugar de consultar otros servidores DNS. La duración de la memoria caché viene determinada por la propiedad de período de vida (TTL) de cada registro DNS. Unos valores más breves darán lugar a una expiración de la caché más rápida y, por tanto, a más consultas a los servidores DNS del Administrador de tráfico; unos valores más largos implican que es posible que se tarde más en redirigir el tráfico desde un punto de conexión no válido. El Administrador de tráfico permite configurar el valor TTL utilizado en las respuestas DNS del Administrador de tráfico, lo que le permite elegir el valor que responda mejor a las necesidades de su aplicación.

## Pasos siguientes

Obtenga más información acerca de la [supervisión del punto de conexión y la conmutación por error automática](traffic-manager-monitoring.md) del Administrador de tráfico.

Obtenga más información acerca de los [métodos de enrutamiento del tráfico](traffic-manager-routing-methods.md) del Administrador de tráfico.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

<!---HONumber=AcomDC_0525_2016-->