---
title: "Azure Traffic Manager: métodos de enrutamiento del tráfico | Microsoft Docs"
description: "Este artículo le ayudará a entender los distintos métodos de enrutamiento de tráfico usados por Traffic Manager"
services: traffic-manager
documentationcenter: 
author: KumudD
manager: timlt
editor: 
ms.assetid: db1efbf6-6762-4c7a-ac99-675d4eeb54d0
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2017
ms.author: kumud
ms.openlocfilehash: fe776e24a4f78b389c6096694055b38befa3c419
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="traffic-manager-routing-methods"></a>Métodos de enrutamiento del Administrador de tráfico

Azure Traffic Manager admite cuatro métodos de enrutamiento de tráfico para determinar cómo enrutar el tráfico de red a los diversos puntos de conexión del servicio. Traffic Manager aplica el método de enrutamiento de tráfico a cada consulta de DNS que recibe. El método de enrutamiento de tráfico determina qué punto de conexión se devuelve en la respuesta de DNS.

En Traffic Manager, hay cuatro métodos de enrutamiento de tráfico disponibles:

* **[Prioridad](#priority):** Seleccione **Prioridad** cuando quiera usar un punto de conexión de servicio primario para todo el tráfico y proporcionar reservas en caso de que los puntos de conexión principal o de reserva no estén disponibles.
* **[Ponderado](#weighted):** Seleccione **Ponderado** cuando quiera distribuir el tráfico entre un conjunto de puntos de conexión, bien de manera uniforme o en función a pesos definidos.
* **[Rendimiento](#performance):** Seleccione **Rendimiento** cuanto tenga puntos de conexión en ubicaciones geográficas diferentes y quiera que los usuarios finales utilicen el punto de conexión "más cercano" según la latencia de red más baja.
* **[Geográfico](#geographic):** Seleccione **Geográfico** para dirigir a los usuarios a puntos de conexión concretos (Azure, Externo o Anidado) en función de la ubicación geográfica de la que parta su consulta de DNS. Esto permite a los clientes de Traffic Manager habilitar escenarios en los que es importante conocer la región geográfica de un usuario y enrutarlo en función de dicha región. Algunos ejemplos incluyen cumplir los mandatos de soberanía de datos, la localización del contenido y de la experiencia del usuario, y la medición del tráfico de otras regiones.

Todos los perfiles de Traffic Manager incluyen supervisión del estado y conmutación por error automática del punto de conexión. Para más información, consulte [Acerca de la supervisión del Administrador de tráfico](traffic-manager-monitoring.md). Con un único perfil de Administrador de tráfico solo se puede usar un método de enrutamiento de tráfico. El método de enrutamiento de tráfico para el perfil se puede cambiar en cualquier momento. Los cambios se aplican en un minuto, sin tiempo de inactividad. Los métodos de enrutamiento de tráfico se pueden combinar mediante perfiles anidados del Administrador de tráfico. La anidación hace posible la creación de configuraciones de enrutamiento de tráfico sofisticadas y flexibles que satisfacen las necesidades de aplicaciones más grandes y complejas. Para más información, consulte [Nested Traffic Manager profiles](traffic-manager-nested-profiles.md)(Perfiles anidados de Administrador de tráfico).

## <a name = "priority"></a>Método de enrutamiento de tráfico Prioridad

Habitualmente, las organizaciones desean ofrecer confiabilidad en sus servicios y, para ello, implementan uno o varios servicios de reserva en caso de que su servicio principal se vuelva inactivo. El método de enrutamiento de tráfico de "Prioridad" permite que los clientes de Azure implementen fácilmente este patrón de conmutación por error.

![Método de enrutamiento de tráfico de "prioridad" de Azure Traffic Manager][1]

El perfil de Traffic Manager contiene una lista de puntos de conexión de servicio ordenada por prioridad. De forma predeterminada, Traffic Manager envía todo el tráfico al punto de conexión primario (prioridad más alta). Si el punto de conexión primario no está disponible, Traffic Manager enruta el tráfico al segundo punto de conexión. Si los puntos de conexión principal y secundario no están disponibles, el tráfico pasa al tercero, y así sucesivamente. La disponibilidad del punto de conexión se basa en el estado configurado (habilitado o deshabilitado) y en la supervisión continuada del punto de conexión.

### <a name="configuring-endpoints"></a>Configuración de puntos de conexión

Con Azure Resource Manager, el usuario configura explícitamente la prioridad del punto de conexión mediante la propiedad de prioridad de cada punto de conexión. Esta propiedad es un valor comprendido entre 1 y 1000. Los valores más bajos representan una prioridad más alta. Los puntos de conexión no pueden compartir valores de prioridad. La configuración de esta propiedad es opcional. Cuando se omite, se usa una prioridad predeterminada basada en el orden del punto de conexión.

##<a name = "weighted"></a>Método de enrutamiento de tráfico Ponderado
El método de enrutamiento de tráfico "Ponderado" le permite distribuir el tráfico uniformemente o utilizar una ponderación predefinida.

![Método de enrutamiento de tráfico "ponderado" de Azure Traffic Manager][2]

En el método de enrutamiento de tráfico ponderado, a cada punto de conexión se le asigna un peso en la configuración de perfiles de Traffic Manager. El peso es un entero comprendido entre 1 y 1000. Este parámetro es opcional. Si se omite, Traffic Manager usará un peso predeterminado de '1'.

Para cada consulta de DNS recibida, Traffic Manager elegirá aleatoriamente un punto de conexión disponible. La probabilidad de elegir un punto de conexión se basa en los pesos asignados a todos los puntos de conexión disponibles. Usar el mismo peso en todos los puntos de conexión dará como resultado una distribución de tráfico uniforme. El uso de pesos mayores o menores en puntos de conexión específicos hace que esos puntos de conexión se devuelvan con mayor o menor frecuencia en las respuestas de DNS.

El método ponderado permite algunos escenarios útiles:

* Actualización gradual de aplicaciones: asigne un porcentaje de tráfico para redirigirlo a un nuevo extremo y aumentar gradualmente el tráfico hasta el 100 %.
* Migración de aplicaciones a Azure: cree un perfil con puntos de conexión de Azure y externos. Ajuste el peso de los puntos de conexión para dar preferencia a los nuevos puntos de conexión.
* Expansión de la nube para conseguir capacidad adicional: expanda rápidamente una implementación local en la nube colocándola detrás de un perfil de Administrador de tráfico. Cuando necesite capacidad adicional en la nube, puede agregar o habilitar más extremos y especificar la porción de tráfico que va a cada extremo.

Resource Manager Azure Portal admite la configuración del enrutamiento del tráfico ponderado.  Las ponderaciones se pueden configurar mediante las versiones de Resource Manager de Azure PowerShell, la CLI y las API de REST.

Es importante comprender que los clientes y los servidores DNS recursivos que los clientes utilizan para resolver nombres DNS, almacenan en caché las respuestas de DNS. Este almacenamiento en caché puede tener un impacto en las distribuciones de tráfico ponderadas. Cuando el número de clientes y servidores DNS recursivos es grande, la distribución del tráfico funciona según lo previsto. Sin embargo, si el número de clientes o servidores DNS recursivos es pequeño, el almacenamiento en caché puede sesgar considerablemente la distribución del tráfico.

Entre los casos de uso comunes se incluye:

* Entornos de desarrollo y pruebas
* Comunicaciones de aplicación a aplicación
* Las aplicaciones dirigidas a una base de usuarios estrecha que comparten una infraestructura DNS recursiva común (por ejemplo, los empleados de la empresa que se conectan a través de un proxy)

Estos efectos de almacenamiento en caché de DNS son comunes a todos los sistemas de enrutamiento de tráfico basados en DNS, no solo al Administrador de tráfico de Azure. En algunos casos, borrar explícitamente la caché DNS puede proporcionar una solución provisional. En otros, puede resultar más adecuado el uso de un método de enrutamiento de tráfico alternativo.

## <a name = "performance"></a>Método de enrutamiento de tráfico Rendimiento

La capacidad de respuesta de muchas aplicaciones se puede mejorar con la implementación de puntos de conexión en dos o más ubicaciones del planeta y el enrutamiento del tráfico a la ubicación más "cercana" al usuario. El método de enrutamiento de tráfico de "rendimiento" ofrece esta funcionalidad.

![Método de enrutamiento de tráfico de "rendimiento" de Azure Traffic Manager][3]

El punto de conexión "más cercano" no es necesariamente el más cercano según la medición de la distancia geográfica. En vez de eso, el método de enrutamiento de tráfico de "rendimiento" determina cuál es el punto de conexión más cercano midiendo la latencia de red. Traffic Manager mantiene una tabla de latencia de Internet que realiza un seguimiento del tiempo que se invierte en la ida y la vuelta entre intervalos de direcciones IP y cada centro de datos de Azure.

Traffic Manager busca la dirección IP de origen de la solicitud de DNS entrante en la tabla de latencia de Internet. Traffic Manager elige un punto de conexión disponible en el centro de datos de Azure que tiene la latencia más baja para ese intervalo de direcciones IP y luego devuelve ese punto de conexión en la respuesta de DNS.

Como se explica en [Cómo funciona Traffic Manager](traffic-manager-overview.md#how-traffic-manager-works), Traffic Manager no recibe consultas de DNS directamente desde los clientes. En su lugar, las consultas de DNS proceden del servicio DNS recursivo que se ha configurado para que lo usen los clientes. Por lo tanto, la dirección IP usada para determinar el punto de conexión "más cercano" no es la dirección IP del cliente, sino la dirección IP de su servicio DNS recursivo. En la práctica, esta dirección IP constituye un buen proxy para el cliente.


Para explicar los cambios en la red de Internet mundial y la incorporación de nuevas regiones de Azure, Traffic Manager actualiza regularmente la tabla de latencia de Internet. Sin embargo, el rendimiento de la aplicación varía en función de las variaciones en tiempo real de la carga en Internet. El enrutamiento de tráfico de rendimiento no supervisa la carga en un punto de conexión de servicio determinado. Sin embargo, si un punto de conexión no está disponible, Traffic Manager no lo incluye en las respuestas a las consultas de DNS.


Puntos a tener en cuenta:

* Si el perfil contiene varios puntos de conexión en la misma región de Azure, Traffic Manager distribuirá el tráfico uniformemente entre los puntos de conexión disponibles en esa región. Si prefiere una distribución de tráfico diferente dentro de una región, puede usar los [perfiles anidados de Traffic Manager](traffic-manager-nested-profiles.md).
* Si se degradan todos los puntos de conexión habilitados en la región de Azure más cercana, Traffic Manager mueve el tráfico a los puntos de conexión en la siguiente región de Azure más cercana. Si desea definir una secuencia preferida de conmutación por error, use los [perfiles anidados de Traffic Manager](traffic-manager-nested-profiles.md).
* Al usar el método de enrutamiento de tráfico de rendimiento con puntos de conexión externos o anidados, deberá especificar la ubicación de esos puntos de conexión. Elija la región de Azure más cercana a su implementación. Estas ubicaciones son los valores compatibles con la tabla de latencia de Internet.
* El algoritmo que elige el punto de conexión es determinista. Las consultas de DNS repetidas del mismo cliente se dirigen al mismo punto de conexión. Normalmente, los clientes utilizan servidores DNS recursivos diferentes durante su recorrido. El cliente puede enrutarse a un punto de conexión diferente. El enrutamiento también puede verse afectado por las actualizaciones de la tabla de latencia de Internet. Por lo tanto, el método de enrutamiento de tráfico de rendimiento no garantiza que un cliente siempre se enrute al mismo punto de conexión.
* Cuando hay cambios en la tabla de latencia de Internet, puede observar que a algunos clientes se les dirige a un punto de conexión diferente. Este cambio de enrutamiento es más adecuado en función de los datos de latencia actuales. Estas actualizaciones son esenciales para mantener la precisión del enrutamiento de tráfico de rendimiento dado que Internet evoluciona constantemente.

## <a name = "geographic"></a>Método de enrutamiento de tráfico Geográfico

Los perfiles de Traffic Manager se pueden configurar para usar el método de enrutamiento geográfico para dirigir a los usuarios a puntos de conexión concretos (Azure, Externo o Anidado) en función de la ubicación geográfica de la que parta su consulta de DNS. Esto permite a los clientes de Traffic Manager habilitar escenarios en los que es importante conocer la región geográfica de un usuario y enrutarlo en función de dicha región. Algunos ejemplos incluyen cumplir los mandatos de soberanía de datos, la localización del contenido y de la experiencia del usuario, y la medición del tráfico de otras regiones.
Cuando se configura un perfil para el enrutamiento geográfico, es preciso que cada punto de conexión asociado a dicho perfil tenga un conjunto de regiones geográficas asignado. Una región geográfica puede estar en los siguientes niveles de granularidad 
- Mundo: cualquier región
- Agrupación regional: por ejemplo, África, Oriente Medio, Australia/Pacífico, etc. 
- País o región: p. ej., Irlanda, Perú, Hong Kong (RAE), etc. 
- Estado o provincia: p. ej., Estados Unidos-California, Australia-Queensland, etc. (este nivel de granularidad solo se admite en estados o provincias de Australia, Canadá, Reino Unido y Estados Unidos).

Si se asigna una región o un conjunto de regiones a un punto de conexión, todas las solicitudes de dichas regiones se enrutarán solo a dicho punto de conexión. Traffic Manager usa la dirección IP de origen de la consulta de DNS para determinar la región desde la que un usuario realiza la consulta (normalmente será la dirección IP de la resolución de DNS local, que realiza la consulta en nombre del usuario).  

![Método de enrutamiento del tráfico "geográfico" de Azure Traffic Manager](./media/traffic-manager-routing-methods/geographic.png)

Traffic Manager lee la dirección IP de origen de la consulta de DNS y decide la región geográfica de la que es originaria. Después, examina si hay algún punto de conexión que tiene dicha región geográfica asignada. Esta búsqueda se inicia en el nivel de granularidad más bajo (estado o provincia donde se admita o en el nivel de país o región si no se admite) y asciende hasta el nivel más alto, que es el **Mundo**. La primera coincidencia que se encuentre mediante este recorrido se designará como el punto de conexión de devolución en la respuesta de la consulta. En el caso de coincidencia con un punto de conexión de tipo Anidado, se devolverá un punto de conexión de dicho perfil secundario, en función de su método de enrutamiento. Los siguientes puntos se pueden aplicar a este comportamiento:

- Si se elige Enrutamiento geográfico, una región geográfica puede asignarse únicamente a un punto de conexión en un perfil de Traffic Manager. Esto garantiza que el enrutamiento de los usuarios es determinista y que los clientes pueden habilitar escenarios que requieran límites geográficos inequívocos.
- Si la región de un usuario se encuentra bajo la asignación geográfica de dos puntos de conexión diferentes, Traffic Manager seleccionará el punto de conexión con la granularidad más baja y no tomará en consideración solicitudes de enrutamiento desde dicha región al otro punto de conexión. Ejemplo: considere un perfil del tipo enrutamiento Geográfico con dos puntos de conexión, Punto de conexión 1 y Punto de conexión 2. Punto de conexión 1 está configurado para recibir tráfico de Irlanda, mientras que Punto de conexión 2 está configurado para recibir tráfico de Europa. Si una solicitud parte de Irlanda, siempre se enrutará al Punto de conexión 1.
- Puesto que una región se puede asignar solo a un punto de conexión, Traffic Manager lo devolverá, independientemente de que el punto de conexión sea correcto, o no.

    >[!IMPORTANT]
    >Se recomienda encarecidamente que los clientes que puedan usar el método de enrutamiento geográfico lo asocien con puntos de conexión del tipo Anidado que tengan perfiles secundarios que contengan al menos dos puntos de conexión cada uno.
- Si se encuentra un punto de conexión coincidente que esté en estado **Detenido**, Traffic Manager devolverá una respuesta NODATA. En este caso, no se realizarán más búsquedas en la parte superior de la jerarquía de la región geográfica. Este comportamiento también se puede aplicar a los tipos de punto de conexión anidados cuando el perfil secundario está en los estados **Detenido** o **Deshabilitado**.
- Si un punto de conexión se encuentra en el estado **Deshabilitado**, no se incluirá en el proceso de coincidencia de región. Este comportamiento también se puede aplicar a los tipos de punto de conexión anidados cuando el punto de conexión se encuentra en estado **Deshabilitado**.
- Si una consulta procede de una región geográfica que no tiene ninguna asignación en dicho perfil, Traffic Manager devolverá una respuesta NODATA. Por consiguiente, se recomienda encarecidamente que los clientes usen el enrutamiento geográfico con un punto de conexión, idealmente del tipo Anidado, con un mínimo de dos puntos de conexión en el perfil secundario, con la región **Mundo** asignada. Esto también garantiza que se administran las direcciones IP que no se asignan a ninguna región.

Como se explica en [Cómo funciona Traffic Manager](traffic-manager-how-traffic-manager-works.md), Traffic Manager no recibe consultas de DNS directamente desde los clientes. En su lugar, las consultas de DNS proceden del servicio DNS recursivo que se ha configurado para que lo usen los clientes. Por tanto, la dirección IP usada para determinar la región no es la del cliente, sino la de su servicio DNS recursivo. En la práctica, esta dirección IP constituye un buen proxy para el cliente.


## <a name="next-steps"></a>Pasos siguientes

Aprenda a desarrollar aplicaciones de alta disponibilidad mediante la [Traffic Manager endpoint monitoring](traffic-manager-monitoring.md)

Aprenda a [crear un perfil de Administrador de tráfico](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-routing-methods/priority.png
[2]: ./media/traffic-manager-routing-methods/weighted.png
[3]: ./media/traffic-manager-routing-methods/performance.png



