---
title: "Preguntas más frecuentes sobre Azure Redis Cache | Microsoft Docs"
description: "Conozca las respuestas a preguntas comunes, patrones y prácticas recomendadas para Azure Redis Cache"
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: c2c52b7d-b2d1-433a-b635-c20180e5cab2
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: wesmc
ms.openlocfilehash: af185725433b0eacc5d57b90fb2e75edd143a59a
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="azure-redis-cache-faq"></a>P+F de Azure Redis Cache
Conozca las respuestas a preguntas comunes, patrones y prácticas recomendadas para Azure Redis Cache.

## <a name="what-if-my-question-isnt-answered-here"></a>Mi pregunta no está respondida aquí. ¿Qué debo hacer?
Si su pregunta no aparece aquí, háganoslo saber y lo ayudaremos a encontrar una respuesta.

* Puede publicar una pregunta en los comentarios al final de estas preguntas más frecuentes y ponerse en contacto con el equipo de Azure Cache y otros miembros de la comunidad con cualquier tema que tenga relación con este artículo.
* Para llegar a más público, puede publicar una pregunta en el [foro de MSDN de Caché de Microsoft Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) y ponerse en contacto con el equipo Caché de Microsoft Azure y otros miembros de la Comunidad.
* Si desea presentar una solicitud para una característica, puede enviar sus solicitudes e ideas al [foro de voz del usuario de Azure Redis Cache](https://feedback.azure.com/forums/169382-cache).
* También puede enviarnos un mensaje de correo electrónico a [Comentarios externos de Caché de Microsoft Azure](mailto:azurecache@microsoft.com).

## <a name="azure-redis-cache-basics"></a>Conceptos básicos de Azure Redis Cache
Las preguntas más frecuentes de esta sección tratan algunos de los aspectos básicos de Azure Redis Cache.

* [¿Qué es Azure Redis Cache?](#what-is-azure-redis-cache)
* [Introducción a Azure Redis Cache](#how-can-i-get-started-with-azure-redis-cache)

Las siguientes preguntas y respuestas abordan los conceptos básicos y las cuestiones sobre Azure Redis Cache; en una de las otras secciones de preguntas más frecuentes encontrará las respuestas.

* [¿Qué oferta y tamaño de Caché en Redis debo utilizar?](#what-redis-cache-offering-and-size-should-i-use)
* [¿Qué clientes de Caché en Redis puedo usar?](#what-redis-cache-clients-can-i-use)
* [¿Hay un emulador local para Azure Redis Cache?](#is-there-a-local-emulator-for-azure-redis-cache)
* [¿Cómo se puede supervisar el estado y el rendimiento de la memoria caché?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>Preguntas más frecuentes de planeación
* [¿Qué oferta y tamaño de Caché en Redis debo utilizar?](#what-redis-cache-offering-and-size-should-i-use)
* [Rendimiento de Azure Redis Cache](#azure-redis-cache-performance)
* [¿En qué región debo buscar mi caché?](#in-what-region-should-i-locate-my-cache)
* [¿Cómo me facturan por Azure Redis Cache?](#how-am-i-billed-for-azure-redis-cache)
* [¿Puedo utilizar Azure Redis Cache con la nube de Azure Government, la nube de China de Azure o Microsoft Azure Alemania?](#can-i-use-azure-redis-cache-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>Preguntas más frecuentes de desarrollo
* [¿Qué hacen las opciones de configuración de StackExchange.Redis?](#what-do-the-stackexchangeredis-configuration-options-do)
* [¿Qué clientes de Caché en Redis puedo usar?](#what-redis-cache-clients-can-i-use)
* [¿Hay un emulador local para Azure Redis Cache?](#is-there-a-local-emulator-for-azure-redis-cache)
* [¿Cómo puedo ejecutar comandos de Redis?](#how-can-i-run-redis-commands)
* [¿Por qué Azure Redis Cache no tiene una referencia de biblioteca de clases MSDN como otros servicios de Azure?](#why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [¿Puedo usar Azure Redis Cache como caché de la sesión PHP?](#can-i-use-azure-redis-cache-as-a-php-session-cache)
* [¿Cuáles son las bases de datos de Redis?](#what-are-redis-databases)

## <a name="security-faqs"></a>Preguntas más frecuentes de seguridad
* [¿Cuándo se debe habilitar el puerto que no es SSL para la conexión a Redis?](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>Preguntas más frecuentes de producción
* [¿Cuáles son algunas prácticas recomendadas de producción?](#what-are-some-production-best-practices)
* [¿Cuáles son las consideraciones que deben tenerse en cuenta al usar los comandos de Redis comunes?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [¿Cómo se pueden realizar bancos de pruebas y probar el rendimiento del caché?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Detalles importantes sobre el crecimiento del grupo de subprocesos](#important-details-about-threadpool-growth)
* [Habilitación de GC del servidor para mejorar el rendimiento del cliente cuando se usa StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Consideraciones sobre rendimiento de las conexiones](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>Preguntas más frecuentes de supervisión y solución de problemas
Las preguntas más frecuentes de esta sección abarcan las cuestiones comunes sobre supervisión y solución de problemas. Para más información sobre la supervisión y la solución de problemas de las instancias de Azure Redis Cache, consulte [Supervisión de Azure Redis Cache](cache-how-to-monitor.md) y [Solución de problemas de Azure Redis Cache](cache-how-to-troubleshoot.md).

* [¿Cómo se puede supervisar el estado y el rendimiento de la memoria caché?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [¿Por qué estoy viendo los tiempos de expiración?](#why-am-i-seeing-timeouts)
* [¿Por qué se desconectó el cliente desde la memoria caché?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>Preguntas más frecuentes de la oferta de caché anterior
* [¿Qué oferta de caché de Azure es adecuada para mí?](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-redis-cache"></a>¿Qué es Azure Redis Cache?
Azure Redis Cache se basa en la popular [caché en Redis](http://redis.io) de código abierto. Ofrece acceso a una instancia de Redis Cache segura y dedicada, administrada por Microsoft y accesible desde cualquier aplicación de Azure. Para obtener más información, consulte la página del producto [Azure Redis Cache](https://azure.microsoft.com/services/cache/) en Azure.com.

### <a name="how-can-i-get-started-with-azure-redis-cache"></a>Introducción a Azure Redis Cache
Hay varias maneras de empezar a utilizar Azure Redis Cache.

* Puede consultar uno de los tutoriales disponibles para [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md) y [Python](cache-python-get-started.md).
* Puede ver el vídeo [How to Build High Performance Apps Using Microsoft Azure Redis Cache](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/)(Procedimiento para compilar aplicaciones de alto rendimiento con Microsoft Azure Redis Cache).
* Puede consultar la documentación de los clientes que coincidan con el lenguaje de desarrollo de su proyecto para ver cómo usar Redis. Hay muchos clientes de Redis que pueden utilizarse con Azure Redis Cache. Para ver una lista de clientes de Redis, consulte [http://redis.io/clients](http://redis.io/clients).

Si no tiene una cuenta de Azure, siga estos pasos:

* [Abrir una cuenta de Azure gratis](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Obtenga créditos que puede usar para probar los servicios de Azure de pago. Incluso después de que se agoten los créditos, puede mantener la cuenta y usar los servicios y características gratuitos de Azure.
* [Activar los beneficios de suscripción a Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Su suscripción a MSDN le proporciona créditos todos los meses que puede usar para servicios de Azure de pago.

<a name="cache-size"></a>

### <a name="what-redis-cache-offering-and-size-should-i-use"></a>¿Qué oferta y tamaño de Caché en Redis debo utilizar?
Cada oferta de Azure Redis Cache proporciona diferentes niveles de **tamaño**, **ancho de banda**, **alta disponibilidad** y opciones del **Acuerdo de Nivel de Servicio**.

Las siguientes son consideraciones para elegir una oferta de caché.

* **Memoria**: los niveles Básico y Estándar ofrecen 250 MB – 53 GB. El nivel Premium ofrece hasta 530 GB. Para obtener más información, consulte [Precios de Azure Redis Cache](https://azure.microsoft.com/pricing/details/cache/).
* **Rendimiento de la red**: si tiene una carga de trabajo que requiere un rendimiento alto, el nivel Premium ofrece más ancho de banda en comparación con los niveles Estándar o Básico. También dentro de cada nivel, las cachés de mayor tamaño tienen más ancho de banda debido a la máquina virtual subyacente que hospeda la memoria caché. Para más información, consulte la [tabla siguiente](#cache-performance).
* **Rendimiento**: el nivel Premium ofrece el máximo rendimiento disponible. Si el servidor o el cliente de caché alcanzan los límites del ancho de banda, recibirá los tiempos de espera del cliente. Para más información, vea la tabla siguiente.
* **Alta disponibilidad/SLA**: Azure Redis Cache garantiza que la caché de los niveles Estándar y Premium estará disponible como mínimo un 99,9 % del tiempo. Para más información sobre nuestro SLA, vea [Precios de Azure Redis Cache](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). El SLA solo cubre la conectividad para los extremos de la memoria caché. El SLA no cubre la protección frente a la pérdida de datos. Se recomienda usar la característica de persistencia de datos de Redis en el nivel Premium para aumentar la resistencia contra la pérdida de datos.
* **Persistencia de datos de Redis**: el nivel Premium le permite conservar los datos de la memoria caché en una cuenta de Azure Storage. En una caché Básico/Estándar todos los datos se almacenan solo en la memoria. Si existen problemas con la infraestructura subyacente, podría producirse una pérdida de los datos. Se recomienda usar la característica de persistencia de datos de Redis en el nivel Premium para aumentar la resistencia contra la pérdida de datos. Azure Redis Cache ofrece opciones de RDB y AOF (próximamente) en la persistencia de Redis. Para más información, vea [Cómo configurar la persistencia para una instancia de Azure Redis Cache Premium](cache-how-to-premium-persistence.md).
* **Clúster en Redis**: para crear memorias caché de más de 53 GB o particionar los datos entre varios nodos de Redis, puede usar la agrupación en clústeres Redis, disponible en el nivel Premium. Cada nodo consta de un par de caché principal/réplica para alta disponibilidad. Para más información, vea [Cómo configurar la agrupación en clústeres para una instancia Premium de Azure Redis Cache](cache-how-to-premium-clustering.md).
* **Aislamiento de red y seguridad mejorado**: la implementación de Azure Virtual Network ofrece seguridad mejorada y aislamiento para su Azure Redis Cache, así como subredes, directivas de control de acceso y otras características para restringir aún más el acceso. Para más información, vea [Cómo configurar la compatibilidad de red virtual para Azure Redis Cache Premium](cache-how-to-premium-vnet.md).
* **Configurar Redis**: tanto en los niveles Estándar como Premium, puede configurar Redis para las notificaciones de Keyspace.
* **Número máximo de conexiones de cliente**: el nivel Premium la ofrece el número máximo de clientes que se pueden conectar a Redis, con un número mayor de conexiones para memorias caché de mayor tamaño. Para obtener más información, consulte [Precios de Azure Redis Cache](https://azure.microsoft.com/pricing/details/cache/).
* **Núcleo dedicado para el servidor Redis**: en el nivel Premium todos los tamaños de caché tienen un núcleo dedicado para Redis. En los niveles Básico/Estándar, el tamaño C1 y superiores tienen un núcleo dedicado para el servidor Redis.
* **Redis es de subproceso único**, por tanto tener dos o más núcleos no proporciona una ventaja adicional sobre tener solo dos núcleos, pero las máquinas virtuales de mayor tamaño suelen tener más ancho de banda que las de menor tamaño. Si el servidor o el cliente de caché alcanza los límites del ancho de banda, recibirá los tiempos de espera del cliente.
* **Mejoras de rendimiento**: las memorias caché en el nivel Premium se implementan en el hardware que tiene procesadores más rápidos y ofrecen un mejor rendimiento en comparación con el nivel Básico o Estándar. Las cachés de nivel Premium tienen un mayor rendimiento y latencias más bajas.

<a name="cache-performance"></a>

### <a name="azure-redis-cache-performance"></a>Rendimiento de Azure Redis Cache
En la tabla siguiente se muestran los valores máximos del ancho de banda observados durante la comprobación de los diversos tamaños de memorias caché Estándar y Premium mediante `redis-benchmark.exe` desde una máquina virtual de Iaas en el punto de conexión de Azure Redis Cache. 

>[!NOTE] 
>Estos valores no se garantizan y no hay ningún SLA para estos números, sino que deben ser los habituales. Debe realizar la prueba de carga de su propia aplicación para determinar el tamaño adecuado de caché para la aplicación.
>
>

A partir de esta tabla, podemos extraer las conclusiones siguientes:

* El rendimiento de las memorias caché que tienen el mismo tamaño es mayor en el nivel Premium que en el nivel Estándar. Por ejemplo, en el caso de una memoria caché de 6 GB, el rendimiento de P1 es de 180 000 solicitudes por segundo frente a las 49 000 de C3.
* Con la agrupación en clústeres de Redis, el rendimiento aumenta de manera lineal a medida que aumenta el número de particiones (nodos) del clúster. Por ejemplo, si se crea un clúster P4 de 10 particiones, el rendimiento disponible es de 400 000 * 10 = 4 millones de solicitudes por segundo.
* El rendimiento para los tamaños de clave más grandes es mayor en el nivel Premium que en el nivel Estándar.

| Nivel de precios | Tamaño | Núcleos de CPU | Ancho de banda disponible | Tamaño del valor de 1 kB |
| --- | --- | --- | --- | --- |
| **Tamaños de caché estándar** | | |**Megabits por segundo (Mb/s) o Megabytes por segundo (MB/s)** |**Solicitudes por segundo (RPS)** |
| C0 |250 MB |Compartido |5 / 0.625 |600 |
| C1 |1 GB |1 |100 / 12.5 |12,200 |
| C2 |2,5 GB |2 |200 / 25 |24,000 |
| C3 |6 GB |4 |400 / 50 |49,000 |
| C4 |13 GB |2 |500 / 62.5 |61,000 |
| C5 |26 GB |4 |1,000 / 125 |115,000 |
| C6 |53 GB |8 |2,000 / 250 |150 000 |
| **Tamaños de caché Premium** | |**Núcleos de CPU por partición** | **Megabits por segundo (Mb/s) o Megabytes por segundo (MB/s)** |**Solicitudes por segundo (RPS), por partición** |
| P1 |6 GB |2 |1,500 / 187.5 |180,000 |
| P2 |13 GB |4 |3,000 / 375 |360,000 |
| P3 |26 GB |4 |3,000 / 375 |360,000 |
| P4 |53 GB |8 |6,000 / 750 |400.000 |

Para obtener instrucciones acerca de cómo descargar las herramientas de Redis como `redis-benchmark.exe`, consulte la sección [¿Cómo puedo ejecutar los comandos de Redis?](#cache-commands) .

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>¿En qué región debo buscar mi caché?
Para obtener el mejor rendimiento y la latencia más baja, sitúe su instancia de Azure Redis Cache en la misma región que la aplicación cliente de la caché.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-redis-cache"></a>¿Cómo me facturan por Azure Redis Cache?
Los precios de Azure Redis Cache están [aquí](https://azure.microsoft.com/pricing/details/cache/). La página de precios muestra los precios por hora. Las memorias caché se facturan por minuto desde el momento en que se crea la memoria caché hasta el momento en que se elimina una memoria caché. No hay ninguna opción de detener o pausar la facturación de una memoria caché.

### <a name="can-i-use-azure-redis-cache-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>¿Puedo utilizar Azure Redis Cache con la nube de Azure Government, la nube de China de Azure o Microsoft Azure Alemania?
Sí, Azure Redis Cache está disponible en la nube de Azure Government, la nube de China de Azure y Microsoft Azure Alemania. Las direcciones URL para acceder a Azure Redis Cache y administrar esta solución son diferentes en estas nubes en comparación con la nube pública de Azure. 

| Nube   | Sufijo DNS para Redis            |
|---------|---------------------------------|
| Público  | *.redis.cache.windows.net       |
| Gobierno de EE. UU.  | *.redis.cache.usgovcloudapi.net |
| Alemania | *.redis.cache.cloudapi.de       |
| China   | *.redis.cache.chinacloudapi.cn  |

Para más información sobre el uso de Azure Redis Cache con otras nubes, consulte los vínculos siguientes.

- [Bases de datos de Azure Government - Azure Redis Cache](../azure-government/documentation-government-services-database.md#azure-redis-cache)
- [Nube de China de Azure - Azure Redis Cache](https://www.azure.cn/documentation/services/redis-cache/)
- [Microsoft Azure Alemania](https://azure.microsoft.com/overview/clouds/germany/)

Para más información sobre el uso de Azure Redis Cache con PowerShell en la nube de Azure Government, la nube de China de Azure y Microsoft Azure Alemania, consulte [cómo conectar con otras nubes con Azure Redis Cache PowerShell](cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>¿Qué hacen las opciones de configuración de StackExchange.Redis?
StackExchange.Redis tiene muchas opciones. En esta sección se describen algunas de las configuraciones comunes. Para obtener más información acerca de las opciones de StackExchange.Redis, consulte [Configuración de StackExchange.Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | DESCRIPCIÓN | Recomendación |
| --- | --- | --- |
| AbortOnConnectFail |Cuando se establece en true, la conexión no se volverá a conectar después de un error de red. |Establézcalo en false y deje que StackExchange.Redis se vuelva a conectar automáticamente. |
| ConnectRetry |El número de veces que se repiten los intentos de conexión durante la conexión inicial. |Consulte la siguiente imagen para obtener instrucciones. |
| ConnectTimeout |Tiempo de espera en milisegundos para operaciones de conexión. |Consulte la siguiente imagen para obtener instrucciones. |

Habitualmente, los valores predeterminados del cliente son suficientes. Ajuste las opciones en función de su carga de trabajo.

* **Reintentos**
  * Para ConnectRetry y ConnectTimeout, la regla general es fracasar y responder rápido a los errores y volver a intentarlo. Esta regla se basa en la carga de trabajo y en cuánto tiempo como media tarda el cliente en enviar un comando de Redis y en recibir una respuesta.
  * Permita que StackExchange.Redis se vuelva a conectar automáticamente en lugar de comprobar el estado de conexión y volver a conectarse. **Evite el uso de la propiedad ConnectionMultiplexer.IsConnected**.
  * Efecto bola de nieve: a veces puede encontrarse con un problema cuando lo está intentando y este error aumenta y nunca se recupera. En este caso, debe considerar el uso de un algoritmo de reintento de retroceso exponencial, tal y como se describe en el artículo [Orientación general sobre reintentos](../best-practices-retry-general.md) publicado por el grupo de Microsoft Patterns & Practices.
* **Valores de tiempo de expiración**
  * Tenga en cuenta la carga de trabajo y establezca los valores según corresponda. Si almacena valores grandes, establezca el tiempo de expiración en un valor superior.
  * Establezca `AbortOnConnectFail` en False y deje que StackExchange.Redis se vuelva a conectar automáticamente.
  * Utilice una única instancia de ConnectionMultiplexer para la aplicación. Puede usar LazyConnection para crear una instancia única que se devuelva por una propiedad de conexión, tal como se muestra en [Conexión a la caché mediante la clase ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * Establezca la propiedad `ConnectionMultiplexer.ClientName` a un nombre único de la instancia de aplicación con fines de diagnóstico.
  * Use varias instancias `ConnectionMultiplexer` para cargas de trabajo personalizadas.
      * Puede seguir este modelo si tiene una carga variable en la aplicación. Por ejemplo: 
      * Puede tener un multiplexor para tratar con claves grandes.
      * Puede tener un multiplexor para tratar con claves pequeñas.
      * Puede establecer distintos valores para los tiempos de expiración de la conexión así como lógica de reintento para cada ConnectionMultiplexer que use.
      * Establezca la propiedad `ClientName` en cada multiplexor para ayudar con el diagnóstico.
      * Esta guía puede conducir a una latencia más simplificada por `ConnectionMultiplexer`.

### <a name="what-redis-cache-clients-can-i-use"></a>¿Qué clientes de Caché en Redis puedo usar?
Una de las grandes virtudes de Redis es que hay muchos clientes que admiten muchos lenguajes de desarrollo diferentes. Para obtener una lista de clientes, consulte [Redis clients](http://redis.io/clients)(Clientes de Redis). Para ver tutoriales que abarcan varios lenguajes y clientes diferentes, consulte [Uso de Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md) y haga clic en el idioma deseado en el selector de idioma en la parte superior del artículo.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-redis-cache"></a>¿Hay un emulador local para Azure Redis Cache?
No hay ningún emulador local para Azure Redis Cache, pero puede ejecutar la versión MSOpenTech de redis-server.exe desde las [herramientas de la línea de comandos de Redis](https://github.com/MSOpenTech/redis/releases/) en su máquina local y conectarse a ella para obtener una experiencia similar a un emulador de memoria caché local, tal y como se muestra en el ejemplo siguiente.

    private static Lazy<ConnectionMultiplexer>
          lazyConnection = new Lazy<ConnectionMultiplexer>
        (() =>
        {
            // Connect to a locally running instance of Redis to simulate a local cache emulator experience.
            return ConnectionMultiplexer.Connect("127.0.0.1:6379");
        });

        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


Si lo desea, también puede configurar un archivo [redis.conf](http://redis.io/topics/config) para ajustarse con más precisión a la [configuración de caché predeterminada](cache-configure.md#default-redis-server-configuration) del servicio en línea Azure Redis Cache.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>¿Cómo puedo ejecutar comandos de Redis?
Puede usar cualquiera de los comandos enumerados en [Redis commands](http://redis.io/commands#) (Comandos de Redis), salvo los comandos indicados en [Comandos de Redis no admitidos en Azure Redis Cache](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache). Para ejecutar comandos de Redis, tiene varias opciones.

* Si tiene una caché Estándar o Premium, puede ejecutar comandos de Redis mediante la [Consola de Redis](cache-configure.md#redis-console). La consola de Redis ofrece una manera segura de ejecutar comandos de Redis en Azure Portal.
* También puede usar las herramientas de línea de comandos de Redis. Para ello, realice los siguientes pasos:
* Descargue las [herramientas de línea de comandos de Redis](https://github.com/MSOpenTech/redis/releases/).
* Conexión a la memoria caché mediante `redis-cli.exe`. Pase el punto de conexión de caché con el modificador -h y la clave con - a, tal como se muestra en el ejemplo siguiente:
* `redis-cli -h <your cache="" name="">
  .redis.cache.windows.net -a <key>
  `

> [!NOTE]
> Las herramientas de línea de comandos de Redis no funcionan con el puerto SSL, pero puede usar una utilidad como `stunnel` para conectar de forma segura las herramientas al puerto SSL siguiendo las instrucciones de la publicación del blog [Announcing ASP.NET Session State Provider for Redis Preview Release](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) (Comunicación del proveedor de estado de la sesión de ASP.NET para la versión preliminar de Redis).
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>¿Por qué Azure Redis Cache no tiene una referencia de biblioteca de clases MSDN como otros servicios de Azure?
Microsoft Azure Redis Cache se basa en la popular caché en Redis de código abierto, y son varios los [clientes de Redis](http://redis.io/clients) disponibles para muchos lenguajes de programación los que pueden tener acceso a ella. Cada cliente tiene su propia API que realiza llamadas a la instancia de Caché de Redis mediante los [comandos de Redis](http://redis.io/commands).

Dado que cada cliente es diferente, no hay no una referencia de clase centralizada en MSDN; cada cliente mantiene su propia documentación de referencia. Además de la documentación de referencia, hay varios tutoriales que muestran cómo empezar a trabajar con Azure Redis Cache con distintos idiomas y clientes de la caché. Para obtener acceso a estos tutoriales, consulte [Uso de Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md) y haga clic en el idioma deseado en el selector de idioma en la parte superior del artículo.

### <a name="can-i-use-azure-redis-cache-as-a-php-session-cache"></a>¿Puedo usar Azure Redis Cache como caché de la sesión PHP?
Sí, para usar Azure Redis Cache como una memoria caché de sesión PHP, especifique la cadena de conexión a la instancia de Azure Redis Cache en `session.save_path`.

> [!IMPORTANT]
> Al usar Azure Redis Cache como una caché de sesión PHP, debe codificar como dirección URL la clave de seguridad que se usa para conectarse a la memoria caché, tal como se muestra en el ejemplo siguiente.
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Si la clave no está codificada como dirección URL, puede recibir una excepción con un mensaje similar al siguiente: `Failed to parse session.save_path`
>
>

Para más información sobre el uso de Redis Cache como caché de la sesión PHP con el cliente de PhpRedis, consulte [PHP Session handler](https://github.com/phpredis/phpredis#php-session-handler)(Controlador de sesión de PHP).

### <a name="what-are-redis-databases"></a>¿Cuáles son las bases de datos de Redis?

Las bases de datos de Redis son simplemente una separación lógica de los datos en la misma instancia de Redis. La memoria caché se comparte entre todas las bases de datos y el consumo real de una base de datos depende de los pares clave-valor almacenados en esa base de datos. Por ejemplo, una caché C6 tiene 53 GB de memoria. Puede elegir colocar todos los 53 GB en una base de datos o dividirlo entre varias bases de datos. 

> [!NOTE]
> Cuando se utiliza una instancia Premium de Azure Redis Cache con clústeres habilitados, solo está disponible la base de datos 0. Se trata de una limitación de Redis intrínseca y no es específica de Azure Redis Cache. Para más información, consulte [Configuración de Caché en Redis de Azure](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>¿Cuándo se debe habilitar el puerto que no es SSL para la conexión a Redis?
El servidor Redis no admite SSL de forma nativa, pero sí Azure Redis Cache. Si se conecta a Azure Redis Cache y el cliente admite SSL, como StackExchange.Redis, deberá utilizar SSL.

>[!NOTE]
>El puerto no SSL está deshabilitado de forma predeterminada para instancias nuevas de Azure Redis Cache. Si el cliente no admite SSL, debe habilitar el puerto no SSL siguiendo las instrucciones que se indican en la sección [Puertos de acceso](cache-configure.md#access-ports) del artículo [Configuración de una memoria caché en Azure Redis Cache](cache-configure.md).
>
>

Las herramientas de Redis, como `redis-cli`, no funcionan con el puerto SSL. Sin embargo, puede usar una utilidad (por ejemplo, `stunnel`) para conectar de forma segura las herramientas con el puerto SSL. Para ello, siga las instrucciones que se describen en la publicación del blog [Announcing ASP.NET Session State Provider for Redis Preview Release](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) (Comunicación del proveedor de estado de la sesión de ASP.NET en la versión preliminar de Redis).

Para obtener instrucciones acerca de cómo descargar las herramientas de Redis, consulte la sección [¿Cómo puedo ejecutar comandos de Redis?](#cache-commands) .

### <a name="what-are-some-production-best-practices"></a>¿Cuáles son algunas prácticas recomendadas de producción?
* [Prácticas recomendadas de StackExchange.Redis](#stackexchangeredis-best-practices)
* [Configuración y conceptos](#configuration-and-concepts)
* [Pruebas de rendimiento](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>Prácticas recomendadas de StackExchange.Redis
* Establecer `AbortConnect` en false y deje que el ConnectionMultiplexer se vuelva a conectar automáticamente. [Haga clic aquí para obtener información detallada](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* Reutilice el ConnectionMultiplexer; no cree uno nuevo para cada solicitud. Se recomienda el patrón `Lazy<ConnectionMultiplexer>`[mostrado aquí](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
* Redis funciona mejor con valores más pequeños, por lo que puede cortar los datos más grandes en varias claves. En [esta discusión de Redis](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ), 100 kB se considera "grande". Lea [este artículo](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) para ver un problema de ejemplo que puede deberse a valores grandes.
* Configure [ThreadPool](#important-details-about-threadpool-growth) para evitar que se agoten los tiempos de espera.
* Utilice al menos el valor de connectTimeout predeterminado de 5 segundos. Este intervalo daría a StackExchange.Redis tiempo suficiente para volver a establecer la conexión, en caso de una interrupción momentánea de la red.
* Tenga en cuenta los costos de rendimiento de las diferentes operaciones que se estén ejecutando. Por ejemplo, el comando `KEYS` es una operación O(n) y debe evitarse. El [sitio redis.io](http://redis.io/commands/) tiene información sobre la complejidad de tiempo de cada operación admitida. Haga clic en cada comando para ver la complejidad de cada operación.

#### <a name="configuration-and-concepts"></a>Configuración y conceptos
* Use el nivel Estándar o Premium en sistemas de producción. El nivel básico es un sistema de nodo único sin replicación de datos ni Acuerdo de Nivel de Servicio. Además, utilice al menos una caché de C1. Las cachés C0 se usan normalmente en escenarios de desarrollo o pruebas sencillos.
* Recuerde que Redis es un almacén de datos **en memoria** . Lea [este artículo](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) para ser consciente de los escenarios donde puede producirse la pérdida de datos.
* Desarrolle el sistema para que pueda controlar interrupciones momentáneas de conexión [debido a la aplicación de revisiones y la conmutación por error](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Pruebas de rendimiento
* Empiece utilizando `redis-benchmark.exe` para hacerse una idea del rendimiento posible antes de escribir sus propias pruebas de rendimiento. Como `redis-benchmark` no admite SSL, debe [habilitar el puerto no SSL mediante Azure Portal](cache-configure.md#access-ports) antes de ejecutar la prueba. Para ver ejemplos, consulte [¿Cómo se pueden realizar bancos de pruebas y probar el rendimiento del caché?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* El cliente para pruebas de máquina virtual debe estar en la misma región que la instancia de caché de Redis.
* Se recomienda usar la serie de máquina virtual Dv2 para su cliente, ya que tiene mejor hardware y logrará los mejores resultados.
* Asegúrese de que la VM cliente que elija tenga al menos tantas características de computación y de ancho de banda como la memoria caché que se esté probando.
* Habilite VRSS en el equipo cliente si se encuentra en Windows. [Haga clic aquí para obtener información detallada](https://technet.microsoft.com/library/dn383582.aspx).
* Las instancias de Redis de nivel Premium tienen mejor latencia de red y rendimiento porque se ejecutan en un hardware mejor de CPU y red.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>¿Cuáles son algunas de las consideraciones al usar los comandos de Redis comunes?
* No se deben ejecutar determinados comandos de Redis que tardan mucho tiempo en completarse sin comprender el impacto de estos comandos.
  * Por ejemplo, no ejecute el comando [KEYS](http://redis.io/commands/keys) en producción ya que puede tardar mucho tiempo en devolver según el número de claves. Redis es un servidor de un único subproceso y procesa los comandos uno a la vez. Si tiene otros comandos emitidos después de KEYS, no se procesarán hasta que Redis procese el comando KEYS. El [sitio redis.io](http://redis.io/commands/) tiene información sobre la complejidad de tiempo de cada operación admitida. Haga clic en cada comando para ver la complejidad de cada operación.
* Tamaños de clave: ¿debo usar claves/valores pequeños o claves/valores grandes? En términos generales, depende del escenario. Si su escenario requiere claves de mayor tamaño, puede ajustar el valor de ConnectionTimeout y los valores de reintento y ajustar la lógica de reintento. Desde una perspectiva del servidor Redis, se observan valores menores que tiene un mejor rendimiento.
* Estas consideraciones no significan que no pueda almacenar valores mayores en Redis, simplemente debe tenerlas en cuenta. Las latencias serán mayores. Si tiene un conjunto de datos mayor y otro más pequeño, puede usar varias instancias de ConnectionMultiplexer, cada una configurado con un conjunto diferente de valores de tiempo de expiración y reintentos, tal como se describe en la sección anterior [¿Qué hacen las opciones de configuración de StackExchange.Redis?](#cache-configuration) .

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>¿Cómo se pueden realizar bancos de pruebas y probar el rendimiento del caché?
* [Habilite los diagnósticos de la memoria caché](cache-how-to-monitor.md#enable-cache-diagnostics) para que pueda [supervisar](cache-how-to-monitor.md) su estado. Puede ver las métricas en el Portal de Azure y también [descargarlas y revisarlas](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) mediante las herramientas que prefiera.
* Puede utilizar redis-benchmark.exe para la prueba de carga del servidor Redis.
* Asegúrese de que la prueba de carga del cliente y de la caché de Redis se encuentran la misma región.
* Use redis cli.exe y supervise la memoria caché mediante el comando INFO.
* Si la carga provoca una elevada fragmentación de memoria, debe escalar verticalmente a un tamaño mayor de caché.
* Para obtener instrucciones acerca de cómo descargar las herramientas de Redis, consulte la sección [¿Cómo puedo ejecutar comandos de Redis?](#cache-commands) .

Los siguientes comandos proporcionan un ejemplo de uso de redis benchmark.exe. Para obtener resultados precisos, ejecute estos comandos desde una máquina virtual de la misma región que la caché.

* Pruebe solicitudes SET con canalización con una carga útil de 1000

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Pruebe solicitudes GET con canalización con una carga útil de 1000.
  Nota: Ejecute antes la prueba SET mostrada anteriormente para rellenar la caché

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>Detalles importantes sobre el crecimiento del grupo de subprocesos
El grupo de subprocesos de CLR tiene dos tipos de subprocesos: subprocesos de "trabajo" y subprocesos de "puertos de terminación de E/S" (lo que se conoce como IOCP).

* Los subprocesos de trabajo se utilizan para aspectos como el procesamiento de métodos `Task.Run(…)` o `ThreadPool.QueueUserWorkItem(…)`. Estos subprocesos también se utilizan en varios componentes del CLR cuando el trabajo se debe ejecutar en un subproceso en segundo plano.
* Los subprocesos IOCP se usan cuando se produce E/S asincrónica (por ejemplo, leer de la red).

El grupo de subprocesos proporciona nuevos subprocesos de trabajo o de terminación de E/S a petición (sin limitación) hasta que se llega a la configuración "mínima" de cada tipo de subproceso. De forma predeterminada, el número mínimo de subprocesos se establece en el número de procesadores en un sistema.

Cuando el número de subprocesos existentes (ocupado) alcanza el número "mínimo" de subprocesos, el grupo de subprocesos limitará la velocidad a la que inserta nuevos subprocesos a un subproceso por 500 milisegundos. Normalmente, si su sistema obtiene una ráfaga de trabajo que necesita un subproceso IOCP, ese trabajo se procesará muy rápidamente. Sin embargo, si la ráfaga de trabajo es mayor que la configuración "mínima", habrá cierto retraso en el procesamiento de parte del trabajo ya que el grupo de subprocesos espera a que pasen dos cosas:

1. Un subproceso existente queda libre para procesar el trabajo.
2. Ningún subproceso existente queda libra durante 500 ms, por lo que se crea un nuevo subproceso.

Básicamente, esto significa que cuando el número de subprocesos ocupados es mayor que los subprocesos mínimos, es probable que pague un retraso de 500 ms antes de que la aplicación procese el tráfico de red. Además, es importante tener en cuenta que, cuando un subproceso existente permanece inactivo durante más de 15 segundos (según lo que yo recuerdo), se elimina, y este ciclo de crecimiento y merma se puede repetir.

Si examinamos un mensaje de error de ejemplo de StackExchange.Redis (compilación 1.0.450 o posterior), verá que ahora se imprimen estadísticas del grupo de subprocesos (consulte a continuación los detalles de trabajo e IOCP).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
    IOCP: (Busy=6,Free=994,Min=4,Max=1000),
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

En el ejemplo anterior, puede ver que para el subproceso de IOCP hay seis subprocesos ocupados y el sistema está configurado para permitir cuatro subprocesos mínimos. En este caso, el cliente probablemente habría visto dos retrasos de 500 ms porque 6 > 4.

Tenga en cuenta que StackExchange.Redis puede alcanzar los tiempos de espera si se limita el crecimiento de los subprocesos de trabajo o de IOCP.

### <a name="recommendation"></a>Recomendación
Dada esta información, se recomienda encarecidamente que los clientes establezcan el valor de configuración mínimo para los subprocesos IOPC y de trabajo en un valor algo mayor que el predeterminado. No podemos dar una orientación exacta sobre cuál debe ser este valor porque el que sea correcto para una aplicación puede ser demasiado alto o bajo para otra. Esta configuración también puede afectar al rendimiento de otras partes de aplicaciones complicadas, por lo que cada cliente debe ajustar este valor de acuerdo con sus necesidades específicas. Un buen punto de partida es 200 o 300, y luego probar y ajustar según sea necesario.

Cómo configurar este valor:

* En ASP.NET, utilice la [opción de configuración "minIoThreads"]["minIoThreads" configuration setting] que se encuentra en el elemento de configuración `<processModel>` de web.config. Si está trabajando dentro de Azure WebSites, esta configuración no se expone a través de las opciones de configuración. Sin embargo, todavía podrá configurar este valor mediante programación (consulte a continuación) con el método Application_Start de global.asax.cs.

  > [!NOTE] 
  > El valor especificado en este elemento de configuración es *por núcleo*. Por ejemplo, si tiene una máquina de 4 núcleos y quiere que su configuración de minIOThreads sea 200 en tiempo de ejecución, use `<processModel minIoThreads="50"/>`.
  >

* Fuera de ASP.NET, use la API [ThreadPool.SetMinThreads(...)](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads.aspx) .

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Habilitación de GC del servidor para obtener más rendimiento en el cliente cuando se usa StackExchange.Redis
La habilitación de GC del servidor puede optimizar el cliente y mejorar el rendimiento y la capacidad cuando se usa StackExchange.Redis. Para más información sobre GC del servidor y cómo habilitarlo, consulte los siguientes artículos.

* [Para habilitar GC del servidor](https://msdn.microsoft.com/library/ms229357.aspx)
* [Fundamentos de la recolección de elementos no utilizados](https://msdn.microsoft.com/library/ee787088.aspx)
* [Recolección de elementos no utilizados y rendimiento](https://msdn.microsoft.com/library/ee851764.aspx)


### <a name="performance-considerations-around-connections"></a>Consideraciones sobre rendimiento de las conexiones

Cada plan de tarifa tiene distintos límites para las conexiones de cliente, memoria y ancho de banda. Si bien cada tamaño de caché permite *hasta* cierta cantidad de conexiones, cada conexión a Redis tiene asociada una sobrecarga. Un ejemplo de dicha sobrecarga podría ser el uso de memoria y CPU como resultado del cifrado TLS/SSL. El límite máximo de conexiones para un tamaño de caché determinado supone una caché con poca carga. Si la carga proveniente de la sobrecarga de conexiones *más* la carga proveniente de las operaciones de clientes supera la capacidad del sistema, la caché puede tener problemas de capacidad incluso si no ha excedido el límite de conexiones para el tamaño de la caché actual.

Para más información acerca de los diferentes límites de conexiones para cada nivel, consulte los [precios de Azure Redis Cache](https://azure.microsoft.com/pricing/details/cache/). Para más información sobre las conexiones y otras configuraciones predeterminadas, consulte [Configuración predeterminada del servidor Redis](cache-configure.md#default-redis-server-configuration).

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>¿Cómo se puede supervisar el estado y el rendimiento de la memoria caché?
Se pueden supervisar instancias de Microsoft Azure Redis Cache en [Azure Portal](https://portal.azure.com). Puede ver las métricas, anclar los gráficos de métricas al panel de inicio, personalizar el intervalo de fecha y hora de los gráficos de supervisión, agregar y quitar métricas de los gráficos y establecer alertas cuando se cumplen determinadas condiciones. Para obtener más información, consulte [Supervisión de Azure Redis Cache](cache-how-to-monitor.md).

El **menú de recursos** de Redis Cache también contiene varias herramientas para supervisar las cachés y solucionar sus problemas.

* En **Diagnosticar y solucionar problemas** se proporciona información sobre los problemas comunes y las estrategias para resolverlos.
* **estado de los recursos** supervisa el recurso e indica si se ejecuta del modo previsto. Para obtener más información sobre el servicio Estado de los recursos de Azure, consulte [Información general sobre Estado de los recursos de Azure](../resource-health/resource-health-overview.md).
* **Nueva solicitud de soporte** para abrir una solicitud de soporte técnico para su memoria caché.

Estas herramientas permiten supervisar el estado de las instancias de Azure Redis Cache y ayudarle a administrar sus aplicaciones de almacenamiento en caché. Para más información, vea la sección "Configuración de soporte técnico y solución de problemas" de [Configuración de Azure Redis Cache](cache-configure.md).

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>¿Por qué estoy viendo los tiempos de expiración?
Los tiempos de expiración se producen en el cliente que usa para comunicarse con Redis. Cuando se envía un comando al servidor de Redis, el comando se pone en cola y el servidor de Redis finalmente recoge el comando y lo ejecuta. Sin embargo el cliente puede agotar el tiempo de espera durante este proceso y, si lo hace, se produce una excepción en el lado de la llamada. Para más información sobre la solución de problemas de tiempo de espera, consulte [Solución de problemas del lado cliente](cache-how-to-troubleshoot.md#client-side-troubleshooting) y [Excepciones de tiempo de espera de StackExchange.Redis](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions).

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>¿Por qué se desconectó el cliente desde la memoria caché?
A continuación se indican algunas razones habituales por las que se desconecta la memoria caché.

* Causas de cliente
  * Se volvió a implementar la aplicación cliente.
  * La aplicación cliente realiza una operación de escalado.
    * En el caso de Cloud Services o Web Apps, puede deberse a la escala automática.
  * Cambia el nivel de red del cliente.
  * Se produjeron errores transitorios en el cliente o en los nodos de red entre el cliente y el servidor.
  * Se han alcanzado los límites de umbral de ancho de banda.
  * Las operaciones de la CPU tardaron demasiado tiempo en completarse.
* Causas de servidor
  * En la oferta de caché estándar, el servicio Azure Redis Cache inicia una conmutación por error desde el nodo principal al nodo secundario.
  * Azure revisó la instancia donde se implementó la memoria caché
    * Esto puede servir para actualizaciones del servidor de Redis o para el mantenimiento general de máquinas virtuales.

### <a name="which-azure-cache-offering-is-right-for-me"></a>¿Qué oferta de caché de Azure es adecuada para mí?
> [!IMPORTANT]
> Según el [anuncio](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) del año pasado, Azure Managed Cache Service y el servicio Azure In-Role Cache **se retiraron** el 30 de noviembre de 2016. Le recomendamos usar [Azure Redis Cache](https://azure.microsoft.com/services/cache/). Para más información sobre la migración, consulte [Migración desde Managed Cache Service a Azure Redis Cache](cache-migrate-to-redis.md).
>
>

### <a name="azure-redis-cache"></a>Azure Redis Cache
Azure Redis Cache está disponible con carácter general en tamaños de hasta 53 GB y tiene un contrato de nivel de servicio de disponibilidad del 99,9 %. El nuevo [nivel Premium](cache-premium-tier-intro.md) ofrece tamaños de hasta 530 GB, además de compatibilidad con clústeres, redes virtuales y persistencia, con un contrato de nivel de servicio del 99,9 %.

Azure Redis Cache ofrece a los clientes la posibilidad de usar una instancia de Redis Cache segura y dedicada administrada por Microsoft. Con esta oferta, puede aprovechar el variado conjunto de características y ecosistema proporcionados por Redis, junto con el hospedaje y la supervisión confiables que Microsoft pone a su disposición.

A diferencia de las memorias caché tradicionales que solo tratan con pares clave-valor, Redis es popular por sus tipos de datos de gran rendimiento. Además, Redis también admite la ejecución de operaciones individuales en estos tipos, por ejemplo: asociar a una cadena; incrementar el valor en un objeto hash; insertar en una lista; calcular la intersección, la unión y la diferencia de conjuntos; u obtener el miembro con la clasificación más alta de un conjunto ordenado. Otras características son la compatibilidad con las transacciones, pub/sub, scripting Lua, claves con un período de vida limitado y opciones de configuración que hacen que Redis se comporte como una memoria caché tradicional.

Otro aspecto clave para el éxito de Redis es su ecosistema de código abierto vibrante y en buen estado. Esto se refleja en el variado conjunto de clientes de Redis disponibles en varios lenguajes, Este ecosistema y la amplia variedad de clientes permiten que prácticamente cualquier carga de trabajo que compilaría dentro de Azure use Azure Redis Cache.

Para más información sobre los primeros pasos con Azure Redis Cache, consulte [Uso de Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md) y [Documentación de Azure Redis Cache](index.md).

### <a name="managed-cache-service"></a>Managed Cache service
[Managed Cache Service se retiró el 30 de noviembre de 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Para ver la documentación archivada, consulte la [documentación archivada de Managed Cache Service](https://msdn.microsoft.com/library/azure/dn386094.aspx).

### <a name="in-role-cache"></a>Caché en rol
[In-Role Cache se retiró el 30 de noviembre de 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Para ver la documentación archivada, consulte la [documentación de archivada de In-Role Cache](https://msdn.microsoft.com/library/azure/dn386103.aspx).

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
