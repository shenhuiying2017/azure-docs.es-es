<properties 
   pageTitle="Administrador de tráfico: métodos de enrutamiento del tráfico | Microsoft Azure"
   description="Este artículo le ayudará a entender los distintos métodos de enrutamiento de tráfico usados por el Administrador de tráfico"
   services="traffic-manager"
   documentationCenter=""
   authors="jtuliani"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/25/2016"
   ms.author="jtuliani" />

# Métodos de enrutamiento de tráfico del Administrador de tráfico

En esta página se describen los métodos de enrutamiento de tráfico admitidos por el Administrador de tráfico de Azure. Se utilizan para dirigir a los usuarios finales al punto de conexión de servicio correcto.

> [AZURE.NOTE] La API de Azure Resource Manager (ARM) para el Administrador de tráfico emplea terminología distinta a la de la API de administración de servicios de Azure (ASM). Este cambio se introdujo siguiendo los comentarios del cliente para mejorar la claridad y reducir los malentendidos más comunes. En esta página, usaremos la terminología de ARM. Las diferencias son:

>- En ARM, usamos "método de enrutamiento de tráfico" para describir el algoritmo que se utiliza para determinar a qué punto de conexión se debe dirigir a un usuario final determinado en un momento determinado. En ASM, llamamos a esto "método de equilibrio de carga".

>- En ARM, usamos "ponderado" para hacer referencia al método de enrutamiento de tráfico que distribuye el tráfico entre todos los puntos de conexión disponibles, en función del peso definido para cada uno. En ASM, llamamos a esto "round-robin".
>- En ARM, usamos "prioridad" para hacer referencia al método de enrutamiento de tráfico que dirige todo el tráfico al primer punto de conexión disponible en una lista ordenada. En ASM, llamamos a esto "conmutación por error".

> En todos los casos, la única diferencia está en el nombre. No hay ninguna diferencia en la funcionalidad.


El Administrador de tráfico de Azure admite varios algoritmos para determinar cómo se enruta a los usuarios finales a los diferentes puntos de conexión de servicio. Estos se conocen como métodos de enrutamiento de tráfico. El método de enrutamiento de tráfico se aplica a cada consulta de DNS recibida con el fin de determinar qué punto de conexión se debe devolver en la respuesta DNS.

Existen tres métodos de enrutamiento disponibles en el Administrador de tráfico:

- **Prioridad:** seleccione "Prioridad" cuando quiera usar un punto de conexión de servicio primario para todo el tráfico, y proporcione reservas en caso de que los puntos de conexión primarios o de reserva no se encuentren disponibles. Para más información, consulte [Método de enrutamiento del tráfico prioritario](#wriority-traffic-routing-method).

- **Ponderado:** seleccione "Ponderado" cuando quiera distribuir el tráfico entre un conjunto de puntos de conexión, o bien de manera uniforme o según los pesos definidos. Para más información, consulte [Método de enrutamiento del tráfico ponderado](#weighted-traffic-routing-method).

- **Rendimiento**: seleccione "Rendimiento" cuando tenga puntos de conexión en diferentes ubicaciones geográficas y quiera que los usuarios finales utilicen el punto de conexión "más cercano" según la latencia de red más baja. Para más información, consulte [Método de enrutamiento de tráfico de rendimiento](#performance-traffic-routing-method).

> [AZURE.NOTE] Todos los perfiles del Administrador de tráfico incluyen supervisión continua del estado y conmutación por error automática del punto de conexión. Esto es aplicable a todos los métodos de enrutamiento de tráfico. Para más información, consulte [Acerca de la supervisión del Administrador de tráfico](traffic-manager-monitoring.md).

Con un único perfil de Administrador de tráfico solo se puede usar un método de enrutamiento de tráfico. El método de enrutamiento de tráfico para el perfil se puede cambiar en cualquier momento. Los cambios se aplican en un minuto, sin tiempo de inactividad. Los métodos de enrutamiento de tráfico se pueden combinar mediante perfiles anidados del Administrador de tráfico. Esto hace posible la creación de configuraciones de enrutamiento de tráfico sofisticadas y flexibles que satisfacen las necesidades de aplicaciones más grandes y complejas. Para más información, consulte [Nested Traffic Manager profiles](traffic-manager-nested-profiles.md) (Perfiles anidados de Administrador de tráfico).

## Método de enrutamiento de tráfico de prioridad

Habitualmente, las organizaciones desean ofrecer confiabilidad en sus servicios y, para ello, proporcionan uno o varios servicios de reserva para el caso en que su servicio principal se vuelva inactivo. El método de enrutamiento de tráfico de "Prioridad" permite que los clientes de Azure implementen fácilmente este patrón de conmutación por error.

![Método de enrutamiento de tráfico de "prioridad" del Administrador de tráfico de Azure][1]

El perfil de Administrador de tráfico está configurado con una lista de puntos de conexión de servicio ordenada por prioridad. De forma predeterminada, todo el tráfico de usuario final se envía al punto de conexión principal (prioridad más alta). Si el punto de conexión principal no está disponible (en función del estado habilitado o deshabilitado del punto de conexión configurado y la supervisión continua del punto de conexión), se remite a los usuarios al segundo punto de conexión. Si los puntos de conexión principal y secundario no están disponibles, el tráfico pasa al tercero, y así sucesivamente.

La configuración de las prioridades de los puntos de conexión se lleva a cabo de forma diferente en las API de ARM (nuevo Portal de Azure) y las API de ASM (portal clásico):

- En las API de ARM, la prioridad del punto de conexión se configura explícitamente, mediante la propiedad de prioridad definida en cada punto de conexión. Esta propiedad debe tomar un valor entre 1 y 1000, donde los valores más bajos representan una prioridad más alta. Dos puntos de conexión cualesquiera no pueden compartir el mismo valor de prioridad. La propiedad es opcional y, cuando se omite, se usa una prioridad predeterminada basada en el orden del punto de conexión.

- En las API de ASM, la prioridad del punto de conexión se configura implícitamente, según el orden en que se enumeran los puntos de conexión en la definición de perfil. También puede configurar el orden de conmutación por error en el Portal de Azure clásico, en la página de configuración del perfil.

## Método de enrutamiento de tráfico ponderado

Un enfoque común para conseguir alta disponibilidad y maximizar la utilización de los servicios es proporcionar un conjunto de puntos de conexión y distribuir el tráfico entre todos ellos, bien de manera uniforme o según una ponderación definida previamente. Este es el objetivo que persigue el método de enrutamiento de tráfico "ponderado".

![Método de enrutamiento de tráfico "ponderado" del Administrador de tráfico de Azure][2]

En el método de enrutamiento de tráfico ponderado, a cada punto de conexión se le asigna un peso como parte de la configuración de perfiles de Administrador de tráfico. Cada peso es un entero comprendido entre 1 y 1000. Este parámetro es opcional y, si se omite, se utiliza un peso predeterminado de "1".
  
El tráfico de usuario final se distribuye entre todos los puntos de conexión de servicio disponibles (según el estado habilitado o deshabilitado del punto de conexión configurado y la supervisión continua del punto de conexión). Para cada consulta de DNS recibida, se elige de forma aleatoria uno de los puntos de conexión disponibles, con una probabilidad basada en el peso asignado a ese punto de conexión y a los otros puntos de conexión disponibles.

El uso del mismo peso en todos los puntos de conexión da lugar a una distribución uniforme del tráfico, lo que resulta perfecto para crear un uso coherente entre un conjunto de puntos de conexión idénticos. El uso de pesos mayores (o menores) en determinados puntos de conexión hace que dichos puntos de conexión se devuelvan con mayor (o menor) frecuencia en las respuestas DNS y que, por tanto, reciban más tráfico. Esto permite varios escenarios útiles:

- Actualización gradual de aplicaciones: asigne un porcentaje de tráfico para redirigirlo a un nuevo extremo y aumentar gradualmente el tráfico hasta el 100 %.

- Migración de aplicaciones a Azure: cree un perfil con Azure y los extremos externos, y especifique el peso del tráfico que se redirigirá a cada extremo.

- Expansión de la nube para conseguir capacidad adicional: expanda rápidamente una implementación local en la nube colocándola detrás de un perfil de Administrador de tráfico. Cuando necesite capacidad adicional en la nube, puede agregar o habilitar más extremos y especificar la porción de tráfico que va a cada extremo.

El enrutamiento de tráfico ponderado se puede configurar mediante el nuevo Portal de Azure, sin embargo, los pesos no se pueden configurar en el portal "clásico". También se puede configurar mediante ARM y ASM mediante Azure PowerShell, la CLI de Azure y las API de REST de Azure.

Nota: Las respuestas DNS se almacenan en caché por los clientes y los servidores DNS recursivos que usan esos clientes para crear sus consultas DNS. Es importante comprender el posible efecto de este almacenamiento en caché en las distribuciones del tráfico ponderado. Si el número de clientes y servidores DNS recursivos es grande, como en el caso de aplicaciones típicas accesibles desde Internet, la distribución del tráfico funciona como se espera. Sin embargo, si el número de clientes o servidores DNS recursivos es pequeño, entonces este almacenamiento en caché puede sesgar considerablemente la distribución del tráfico. Casos de uso comunes en los que puede darse esto:

- Entornos de desarrollo y pruebas
- Comunicaciones de aplicación a aplicación
- Aplicaciones dirigidas a una base de usuarios limitada que comparte una infraestructura DNS recursiva común, por ejemplo, los empleados de una organización.

Estos efectos de almacenamiento en caché de DNS son comunes a todos los sistemas de enrutamiento de tráfico basados en DNS, no solo al Administrador de tráfico de Azure. En algunos casos, borrar explícitamente la caché DNS puede proporcionar una solución provisional. En otros, puede resultar más adecuado el uso de un método de enrutamiento de tráfico alternativo.

## Método de enrutamiento de tráfico de rendimiento

La capacidad de respuesta de muchas aplicaciones se puede mejorar con la implementación de puntos de conexión en dos o más ubicaciones del planeta y el enrutamiento de los usuarios finales a la ubicación más "cercana" a ellos. El método de enrutamiento de tráfico de "rendimiento" sirve a este propósito.

![Método de enrutamiento de tráfico de "rendimiento" del Administrador de tráfico de Azure][3]

Para maximizar la capacidad de respuesta, el punto de conexión "más cercano" no es necesariamente el más cercano según la medición de la distancia geográfica. Si no que el método de enrutamiento de tráfico de "rendimiento" determina qué punto de conexión es el más cercano al usuario final, medido por la latencia de red. La "proximidad" se determina mediante una tabla de latencia de Internet que muestra el tiempo que se invierte en la ida y la vuelta entre intervalos direcciones IP y cada centro de datos de Azure.

El Administrador de tráfico examina las solicitudes DNS de entrada y busca la dirección IP de origen de esa solicitud en la tabla de latencia de Internet. Esto determina la latencia de esa dirección IP para cada centro de datos de Azure. A continuación, elige cuál de los puntos de conexión disponibles (según el estado habilitado o deshabilitado del punto de conexión configurado y la supervisión continua del punto de conexión) tiene la menor latencia y devuelve dicho punto de conexión en la respuesta DNS. De ahí que se dirija al usuario final al punto de conexión que le proporciona la menor latencia y, por tanto, el mejor rendimiento.

Como se ha explicado en [How Traffic Manager Works](traffic-manager-how-traffic-manager-works.md) (Cómo funciona el Administrador de tráfico), el Administrador de tráfico no recibe consultas DNS directamente de los usuarios finales, sino del servicio DNS recursivo que tienen configurado para usar. Por lo tanto, la dirección IP usada para determinar el punto de conexión "más cercano" no es la dirección IP del usuario final, sino la dirección IP de su servicio DNS recursivo. En la práctica, esta dirección IP constituye un buen proxy para el usuario final a tal efecto.

Para explicar los cambios en la red Internet mundial y la incorporación de nuevas regiones de Azure, el Administrador de tráfico actualiza regularmente la tabla de latencia de Internet que consume. Sin embargo, no se tienen en cuenta las variaciones en tiempo real en el rendimiento o la carga en Internet.

El enrutamiento de tráfico de rendimiento no tiene en cuenta la carga en un punto de conexión de servicio determinado, aunque el Administrador de tráfico supervisa los puntos de conexión y no los incluye en las respuestas de las consultas de DNS si no están disponibles.

Puntos a tener en cuenta:

- Si el perfil contiene varios puntos de conexión en la misma región de Azure, el tráfico dirigido a esa región se distribuye uniformemente entre los puntos de conexión disponibles (según el estado habilitado o deshabilitado del punto de conexión configurado y la supervisión continua del punto de conexión). Si prefiere una distribución de tráfico diferente dentro de una región, lo puede lograr mediante los [perfiles anidados del Administrador de tráfico](traffic-manager-nested-profiles.md).

- Si todos los puntos de conexión de una región de Azure dada están degradados (según la supervisión de puntos de conexión en curso), el tráfico de esos puntos de conexión se distribuirá entre todos los demás puntos de conexión disponibles que se especifiquen en el perfil, y no a los puntos de conexión más próximos. Esto ayuda a evitar un error en cascada que podría producirse si se sobrecarga el extremo más cercano. Si prefiere definir la secuencia de conmutación por error de punto de conexión, puede hacerlo mediante los [perfiles anidados del Administrador de tráfico](traffic-manager-nested-profiles.md).

- Al usar el método de enrutamiento de tráfico de rendimiento con puntos de conexión externos anidados, deberá especificar la ubicación de esos puntos de conexión. Elija la región de Azure más cercana a su implementación; las opciones disponibles son las regiones de Azure, dado que esas son las ubicaciones que se admiten en la tabla de latencia de Internet.

- El algoritmo que elige qué punto de conexión devolver a un usuario final dado es determinista, no hay nada aleatorio. Las consultas DNS repetidas del mismo cliente se dirigirán al mismo punto de conexión. Sin embargo, el método de enrutamiento de tráfico de rendimiento no debe basarse en enrutar siempre a un usuario dado a una implementación dada (lo que puede ser necesario si, por ejemplo, los datos de usuario de ese usuario se almacenan solo localmente). El motivo es que cuando un usuario viaja, utiliza normalmente distintos servidores DNS recursivos, así que podrían enrutarse a un punto de conexión diferente. También puede verse afectado por las actualizaciones de la tabla de latencia de Internet.

- Cuando se actualiza la tabla de latencia de Internet, puede observar que a algunos clientes se les dirige a un punto de conexión diferente. El número de usuarios afectados debe ser mínimo y refleja un enrutamiento más preciso basado en los datos de latencia actuales. Estas actualizaciones son esenciales para mantener la precisión del enrutamiento de tráfico de rendimiento dado que Internet evoluciona constantemente.


## Pasos siguientes

Aprenda a desarrollar aplicaciones de alta disponibilidad mediante la [supervisión de puntos de conexión del Administrador de tráfico](traffic-manager-monitoring.md).

Aprenda a [crear un perfil de Administrador de tráfico](traffic-manager-manage-profiles.md).


<!--Image references-->
[1]: ./media/traffic-manager-routing-methods/priority.png
[2]: ./media/traffic-manager-routing-methods/weighted.png
[3]: ./media/traffic-manager-routing-methods/performance.png

<!---HONumber=AcomDC_0601_2016-->