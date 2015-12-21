<properties 
	pageTitle="P+F de Caché en Redis de Azure" 
	description="Conozca las respuestas a preguntas comunes, patrones y prácticas recomendadas para Caché en Redis de Azure" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/03/2015" 
	ms.author="sdanie"/>

# P+F de Caché en Redis de Azure

Obtenga información acerca de las respuestas a preguntas comunes, patrones y prácticas recomendadas para Caché en Redis de Azure.

<a name="cache-size"></a>
## ¿Qué oferta y tamaño de Caché en Redis debo utilizar?
Cada oferta de Caché en Redis de Azure ofrece diferentes niveles de opciones de **tamaño**, **ancho de banda**, **alta disponibilidad** y **SLA**.

Las siguientes son consideraciones para elegir una oferta de caché.

-	**Memoria**: los niveles Básico y Estándar ofrecen 250 MB – 53 GB. El nivel Premium ofrece hasta 530 GB con más disponible [tras su solicitud](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Para obtener más información, consulte [Precios de Caché en Redis de Azure](https://azure.microsoft.com/pricing/details/cache/).
-	**Rendimiento de la red**: si tiene una carga de trabajo que requiere un rendimiento alto, el nivel Premium ofrece más ancho de banda en comparación con Estándar o Básico. También dentro de cada nivel, las cachés de mayor tamaño tienen más ancho de banda debido a la máquina virtual subyacente que hospeda la memoria caché. Para obtener más información, vea la tabla siguiente.
-	**Rendimiento**: el nivel Premium ofrece el máximo rendimiento disponible. Si el servidor o el cliente de caché alcanza los límites del ancho de banda, recibirá los tiempos de espera del cliente. Para obtener más información, vea la tabla siguiente.
-	**Alta disponibilidad/SLA**: Caché en Redis de Azure garantiza que la memoria caché de los niveles Estándar y Premium estará disponible como mínimo un 99,9 % del tiempo. Para obtener más información sobre nuestro SLA, consulte [Precios de Caché en Redis de Azure](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). El SLA solo cubre la conectividad para los extremos de la memoria caché. El SLA no cubre la protección frente a la pérdida de datos. Se recomienda usar la característica de persistencia de datos de Redis en el nivel Premium para aumentar la resistencia contra la pérdida de datos.
-	**Persistencia de datos de Redis**: el nivel Premium le permite conservar los datos de la memoria caché en una cuenta de Almacenamiento de Azure. En una caché Básico/Estándar todos los datos se almacenan solo en la memoria. En caso de problemas con la infraestructura subyacente, podría producirse una pérdida de los datos. Se recomienda usar la característica de persistencia de datos de Redis en el nivel Premium para aumentar la resistencia contra la pérdida de datos. Caché en Redis de Azure ofrece opciones de RDB y AOF (próximamente) en la persistencia de Redis. Para obtener más información, vea [Cómo configurar la persistencia para una Caché en Redis de Azure Premium](cache-how-to-premium-persistence.md).
-	**Clúster de Redis**: si quiere crear memorias caché de más de 53 GB o quiere realizar particiones de datos en varios nodos de Redis, puede usar la agrupación en clústeres de Redis que está disponible en el nivel Premium. Cada nodo consta de un par de caché principal/réplica para alta disponibilidad. Para obtener más información, vea [Cómo configurar la agrupación en clústeres de Redis para una Caché en Redis de Azure Premium](cache-how-to-premium-clustering.md).
-	**Aislamiento de red y seguridad mejorado**: la implementación de la Red virtual de Azure ofrece seguridad mejorada y aislamiento para su Caché en Redis de Azure, así como subredes, directivas de control de acceso y otras características para restringir aún más el acceso. Para obtener más información, vea [Cómo configurar la compatibilidad de red virtual para una Caché en Redis de Azure Premium](cache-how-to-premium-vnet.md).
-	**Configurar Redis**: tanto en los niveles Estándar como Premium, puede configurar Redis para las notificaciones de Keyspace.
-	**Número máximo de conexiones de cliente**: el nivel Premium la ofrece el número máximo de clientes que se pueden conectar a Redis, con un número mayor de conexiones para memorias caché de mayor tamaño. [Consulte la página de información de precios para obtener detalles](https://azure.microsoft.com/pricing/details/cache/).
-	**Núcleo dedicado para servidor Redis**: en el nivel Premium todos los tamaños de caché tienen un núcleo dedicado para Redis. En los niveles Básico/Estándar el tamaño C1 y superiores tienen un núcleo dedicado para el servidor de Redis.
-	**Redis es de subproceso único**, por tanto tener dos o más núcleos no proporciona una ventaja adicional sobre tener solo dos núcleos, pero las máquinas virtuales de mayor tamaño suelen tener más ancho de banda que las de menor tamaño. Si el servidor o el cliente de caché alcanza a los límites del ancho de banda, recibirá los tiempos de espera del cliente.
-	**Mejoras de rendimiento**: las memorias caché en el nivel Premium se implementan en el hardware que tienen procesadores más rápidos y ofrecen un mejor rendimiento en comparación con el nivel Básico o Estándar. Las cachés de nivel Premium tienen un mayor rendimiento y latencias más bajas.

En la tabla siguiente se muestran los valores máximos del ancho de banda observados durante la comprobación de los diversos tamaños de memorias caché Estándar y Premium mediante `redis-benchmark.exe` desde una máquina virtual de Iaas en el extremo de Caché en Redis de Azure. Tenga en cuenta que estos valores no se garantizan y no hay ningún SLA para estos números, pero deben ser los habituales. Debe realizar la prueba de carga de su propia aplicación para determinar el tamaño adecuado de caché para la aplicación.

A partir de esta tabla, podemos extraer las conclusiones siguientes.

-	El rendimiento de una memoria caché de nivel Premium es superior al de una memoria caché de nivel Standard del mismo tamaño . Por ejemplo, en el caso de una caché de 6 GB, el rendimiento de P1 es de 140K solicitudes por segundo frente a las 49K para C3.
-	Con la agrupación en clústeres de Redis, el rendimiento aumenta de manera lineal a medida que aumenta el número de particiones (nodos) del clúster. Por ejemplo, si se crea un clúster P4 de 10 particiones, el rendimiento disponible es de 250.000 * 10 = 2,5 millones de solicitudes por segundo.
-	El rendimiento para los tamaños de clave más grandes es más alto en el nivel Premium que en el nivel Estándar.

| Nivel de precios | Tamaño | Ancho de banda disponible | Tamaño de clave de 1 KB |
|----------------------|--------|----------------------------|--------------------------------|
| **Tamaños de caché estándar** | &nbsp; |**Megabits por segundo (Mbps)** | **Solicitudes por segundo (RPS)** |
| C0 | 250 MB | 5 | 600 |
| C1 | 1 GB | 100 | 12200 |
| C2 | 2,5 GB | 200 | 24000 |
| C3 | 6 GB | 400 | 49000 |
| C4 | 13 GB | 500 | 61000 |
| C5 | 26 GB | 1000 | 115000 |
| C6 | 53 GB | 2000 | 150000 |
| **Tamaños de caché Premium** | &nbsp; | &nbsp; | **Solicitudes por segundo (RPS), por partición** |
| P1 | 6 GB | 1000 | 140000 |
| P2 | 13 GB | 2000 | 220000 |
| P3 | 26 GB | 2000 | 220000 |
| P4 | 53 GB | 4000 | 250000 |


Para obtener instrucciones acerca de cómo descargar las herramientas de Redis como `redis-benchmark.exe`, consulte la sección [¿Cómo puedo ejecutar los comandos de Redis?](#cache-commands).

<a name="cache-region"></a>
## ¿En qué región debo buscar mi caché?

Para obtener el mejor rendimiento y la latencia más baja, busque Caché en Redis de Azure en la misma región que la aplicación cliente de la caché.

<a name="cache-billing"></a>
## ¿Cómo me facturan por la Caché en Redis de Azure?

Los precios de la Caché en Redis de Azure están [aquí](http://azure.microsoft.com/pricing/details/cache/). La página de precios muestra los precios por hora. Las memorias caché se facturan por minuto desde el momento en que se crea la memoria caché hasta el momento en que se elimina una memoria caché. No hay ninguna opción de detener o pausar la facturación de una memoria caché.

<a name="cache-timeouts"></a>
## ¿Por qué estoy viendo los tiempos de expiración?

Los tiempos de expiración se producen en el cliente que usa para comunicarse con Redis. Por lo general, el servidor de Redis no agota el tiempo de espera. Cuando se envía un comando al servidor de Redis, el comando se pone en cola y el servidor de Redis finalmente recoge el comando y lo ejecuta. Sin embargo el cliente puede agotar el tiempo de espera durante este proceso y, si lo hace, se produce una excepción en el lado de la llamada. Para obtener más información sobre cómo solucionar problemas de tiempo de espera, consulte [Investigación de las excepciones del tiempo de expiración en StackExchange.Redis para Caché en Redis de Azure](http://azure.microsoft.com/blog/2015/02/10/investigating-timeout-exceptions-in-stackexchange-redis-for-azure-redis-cache/).

<a name="cache-monitor"></a>
## ¿Cómo se puede supervisar el estado y el rendimiento de la memoria caché?

Se pueden supervisar las instancias de Caché en Redis de Microsoft Azure en el [Portal de Azure](https://portal.azure.com). Puede ver las métricas, anclar los gráficos de métricas al panel de inicio, personalizar el intervalo de fecha y hora de los gráficos de supervisión, agregar y quitar métricas de los gráficos y establecer alertas cuando se cumplen determinadas condiciones. Estas herramientas permiten supervisar el estado de las instancias de Caché en Redis de Azure y ayudarle a administrar sus aplicaciones de almacenamiento en caché. Para obtener más información acerca de cómo supervisar las memorias caché, consulte [Supervisión de Caché en Redis de Azure](https://msdn.microsoft.com/library/azure/dn763945.aspx).

<a name="cache-disconnect"></a>
## ¿Por qué se desconectó el cliente desde la memoria caché?

A continuación se indican algunas razones habituales por las que se desconecta la memoria caché.

-	Causas de cliente
	-	Se volvió a implementar la aplicación cliente.
	-	La aplicación cliente realiza una operación de escalado.
		-	En el caso de Servicios en la nube o Aplicaciones web, puede deberse a la escala automática.
	-	Cambia el nivel de red del cliente.
	-	Se produjeron errores transitorios en el cliente o en los nodos de red entre el cliente y el servidor.
	-	Se han alcanzado los límites de umbral de ancho de banda.
	-	Las operaciones de la CPU tardaron demasiado tiempo en completarse.
-	Causas de servidor
	-	En la oferta de caché estándar, el servicio Caché en Redis de Azure inicia una conmutación por error desde el nodo principal al nodo secundario.
	-	Azure revisó la instancia donde se implementó la memoria caché
		-	Esto puede servir para actualizaciones del servidor de Redis o para el mantenimiento general de máquinas virtuales.

<a name="cache-configuration"></a>
## ¿Qué hacen las opciones de configuración de StackExchange.Redis?

StackExchange.Redis tiene muchas opciones. En esta sección se describen algunas de las configuraciones comunes. Para obtener más información acerca de las opciones de StackExchange.Redis, consulte [Configuración de StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md).

ConfigurationOptions|Descripción|Recomendación
---|---|---
AbortOnConnectFail|Cuando se establece en true, la conexión no se volverá a conectar después de un error de red.|Establézcalo en false y deje que StackExchange.Redis se vuelva a conectar automáticamente.
ConnectRetry|El número de veces que se repiten los intentos de conexión durante la conexión inicial.||
ConnectTimeout|Tiempo de espera en milisegundos para operaciones de conexión.|

En la mayoría de los casos los valores predeterminados del cliente son suficientes. Puede ajustar las opciones en función de la carga de trabajo.

-	Reintentos
	-	Para ConnectRetry y ConnectTimeout la regla general es un error rápido y un reintento. Esto se basa en la carga de trabajo y en cuánto tiempo como promedio tarda el cliente en enviar un comando de Redis y en recibir una respuesta.
	-	Permita que StackExchange.Redis se vuelva a conectar automáticamente en lugar de comprobar el estado de conexión y volver a conectarse. **Evite el uso de la propiedad ConnectionMultiplexer.IsConnected**.
	-	Efecto bola de nieve: a veces puede encontrarse con un problema cuando lo está intentando y este error aumenta y nunca se recupera. En este caso debe considerar el uso de un algoritmo de reintento de retroceso exponencial, tal como se describe en [Retry general guidance](https://github.com/mspnp/azure-guidance/blob/master/Retry-General.md) (Guía general de reintentos) publicado por el grupo Microsoft Patterns & Practices.
-	Valores de tiempo de expiración
	-	Tenga en cuenta la carga de trabajo y establezca los valores según corresponda. Si almacena valores grandes, establezca el tiempo de expiración en un valor superior.
		-	Establezca ABortOnConnectFail en false y deje que StackExchange.Redis vuelva a conectarse automáticamente.
-	Utilice una única instancia de ConnectionMultiplexer para la aplicación. Puede usar LazyConnection para crear una instancia única que se devuelva por una propiedad de conexión, tal como se muestra en [Conexión a la caché mediante la clase ConnectionMultiplexer](https://msdn.microsoft.com/library/azure/dn690521.aspx#Connect).
-	Establezca la propiedad `ConnectionMultiplexer.ClientName` a un nombre único de la instancia de aplicación con fines de diagnóstico.
-	Use varias instancias `ConnectionMultiplexer` para cargas de trabajo personalizadas.
	-	Puede seguir este modelo si tiene una carga variable en la aplicación. Por ejemplo:
		-	Puede tener un multiplexor para tratar con claves grandes. 
		-	Puede tener un multiplexor para tratar con claves pequeñas. 
		-	Puede establecer distintos valores para los tiempos de expiración de la conexión así como lógica de reintento para cada ConnectionMultiplexer que use.
		-	Establezca la propiedad `ClientName` en cada multiplexor para ayudar con el diagnóstico. 
		-	Esto dará lugar a una latencia más simplificada por `ConnectionMultiplexer`.

<a name="cache-redis-commands"></a>
## ¿Cuáles son algunas de las consideraciones al usar los comandos de Redis comunes?

-	No se deben ejecutar determinados comandos de Redis que tardan mucho tiempo en completarse sin comprender el impacto de estos comandos.
	-	Por ejemplo, no ejecute el comando [KEYS](http://redis.io/commands/keys) en producción ya que puede tardar mucho tiempo en devolver según el número de claves. Redis es un servidor de un único subproceso y procesa los comandos uno a la vez. Si tiene otros comandos emitidos después de KEYS, no se procesarán hasta que Redis procese el comando KEYS.
-	Tamaños de clave: ¿debo usar claves/valores pequeños o claves/valores grandes? En términos generales, depende del escenario. Si su escenario requiere claves de mayor tamaño, puede ajustar el valor de ConnectionTimeout y los valores del reintento y ajustar la lógica de reintento. Desde una perspectiva del servidor Redis, se observan valores menores que tiene un mejor rendimiento.
	-	Esto no significa que no pueda almacenar valores mayores en Redis; debe tener en cuenta las consideraciones siguientes. Las latencias serán mayores. Si tiene un conjunto de datos mayor y otro más pequeño, puede usar varias instancias de ConnectionMultiplexer, cada una configurado con un conjunto diferente de valores de tiempo de expiración y reintentos, tal como se describe en la sección anterior [¿Qué hacen las opciones de configuración de StackExchange.Redis?](#cache-configuration).


<a name="cache-ssl"></a>
## ¿Cuándo se debe habilitar el puerto que no es SSL para la conexión a Redis?

El servidor de Redis no admite SSL desde el principio, pero hace Caché en Redis de Azure. Si se conecta a Caché en Redis de Azure y el cliente admite SSL, como StackExchange.Redis, deberá utilizar SSL.

Tenga en cuenta que el puerto no SSL está deshabilitado de forma predeterminada para instancias nuevas de Caché en Redis de Azure. Si el cliente no admite SSL, debe habilitar el puerto que no es SSL siguiendo las instrucciones de la sección [Puertos de acceso](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) del artículo [Configuración de una memoria caché en Caché en Redis de Azure](https://msdn.microsoft.com/library/azure/dn793612.aspx) artículo.

Las herramientas de Redis como `redis-cli` no funcionan con el puerto SSL, pero puede usar una utilidad como `stunnel` para conectar de forma segura las herramientas al puerto SSL siguiendo las instrucciones de la publicación del blog [Announcing ASP.NET Session State Provider for Redis Preview Release](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) (Comunicación del proveedor de estado de la sesión de ASP.NET para la versión de vista previa de Redis).

Para obtener instrucciones acerca de cómo descargar las herramientas de Redis, consulte la sección [¿Cómo puedo ejecutar comandos de Redis?](#cache-commands).

<a name="cache-benchmarking"></a>
## ¿Cómo se pueden realizar bancos de pruebas y probar el rendimiento del caché?

-	[Habilite los diagnósticos de caché](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) para que pueda [supervisar](https://msdn.microsoft.com/library/azure/dn763945.aspx) el estado de la memoria caché. Puede ver las métricas en el Portal de Azure y también [descargarlas y revisarlas](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) mediante el uso de las herramientas que prefiera.
-	Puede utilizar redis-benchmark.exe para la prueba de carga del servidor Redis.
	-	Asegúrese de que la prueba de carga del cliente y de la caché de Redis se encuentran la misma región.
-	Use redis cli.exe y supervise la memoria caché mediante el comando INFO.
	-	Si la carga provoca una elevada fragmentación de memoria, debe escalar verticalmente a un tamaño mayor de caché.
-	Para obtener instrucciones acerca de cómo descargar las herramientas de Redis, consulte la sección [¿Cómo puedo ejecutar comandos de Redis?](#cache-commands).

<a name="cache-commands"></a>
## ¿Cómo puedo ejecutar comandos de Redis?

Puede usar cualquiera de los comandos enumerados en [Comandos de Redis](http://redis.io/commands#), excepto los comandos mostrados en [Comandos de Redis no admitidos en Caché en Redis de Azure](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache). Para ejecutar los comandos de Redis tiene varias opciones.

-	Si tiene una caché Estándar o Premium, puede ejecutar comandos de Redis mediante la [Consola de Redis](cache-configure.md#redis-console). Esto ofrece una manera segura de ejecutar comandos de Redis en el portal de Azure.
-	Use las herramientas de línea de comandos de Redis. Para usarlas, realizará los siguientes pasos.
	-	Descargue las [herramientas de línea de comandos de Redis](https://github.com/MSOpenTech/redis/releases/download/win-2.8.19.1/redis-2.8.19.zip).
	-	Conexión a la memoria caché mediante `redis-cli.exe`. Pase el extremo de caché mediante que el modificador -h y la clave mediante - a, tal como se muestra en el ejemplo siguiente.
		-	`redis-cli -h <your cache name>.redis.cache.windows.net -a <key>`
	-	Tenga en cuenta que las herramientas de línea de comandos de Redis no funcionan con el puerto SSL, pero puede usar una utilidad como `stunnel` para conectar de forma segura las herramientas al puerto SSL siguiendo las instrucciones de la publicación del blog [Announcing ASP.NET Session State Provider for Redis Preview Release](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) (Comunicación del proveedor de estado de la sesión de ASP.NET para la versión de vista previa de Redis).

<a name="cache-common-patterns"></a>
## ¿Cuáles son algunas consideraciones y patrones comunes de la memoria caché?

-	En Microsoft Patterns & Practices se puede encontrar la guía siguiente.
	-	[Caching guidance](https://github.com/mspnp/azure-guidance/blob/master/Caching.md) (Guía de memoria en caché).
	-	[Azure Cloud Application Design and Implementation Guidance (Guía de implementación de diseño y aplicaciones de Azure Cloud)](https://github.com/mspnp/azure-guidance)
-	[Patrones de caché comunes con Caché en Redis de Azure](cache-howto-common-cache-patterns.md)

<a name="cache-reference"></a>
## ¿Por qué Caché en Redis de Azure no tiene una referencia de biblioteca de clases MSDN como otros servicios de Azure?

Caché en Redis de Microsoft Azure se basa en la popular Caché de Redis de código abierto, que proporciona acceso a una caché de Redis segura y dedicada, administrada por Microsoft. Está disponible una variedad de [clientes de Redis](http://redis.io/clients) para muchos lenguajes de programación. Cada cliente tiene su propia API que realiza llamadas a la instancia de Caché de Redis mediante los [comandos de Redis](http://redis.io/commands).

Dado que cada cliente es diferente, no hay no una referencia de clase centralizada en MSDN; en cambio, cada cliente mantiene su propia documentación de referencia. Además de la documentación de referencia, hay varios tutoriales en Azure.com que muestra cómo empezar a trabajar con Caché en Redis de Azure con distintos idiomas y clientes de la caché en la página [Documentación de Caché de Redis](http://azure.microsoft.com/documentation/services/redis-cache/).


## ¿Qué oferta de caché de Azure es adecuada para mí?

>[AZURE.IMPORTANT]Microsoft recomienda que todos los trabajos de desarrollo nuevos usen Caché en Redis de Azure.

Caché de Azure tiene tres ofertas actualmente:

-	Caché en Redis de Azure
-	Servicio de caché administrado de Azure
-	Caché en rol de Azure

>[AZURE.IMPORTANT]El 30 de noviembre de 2016 se retirará el Servicio de caché administrado de Azure y la Caché en rol de Azure. Se recomienda que migre a la Caché en Redis de Azure con vistas a prepararse para la mencionada retirada.
>
>Caché en Redis de Azure ha sido la solución recomendada de almacenamiento en caché de Azure, ya que el servicio ya está disponible con carácter general en todas las regiones de Azure, incluidas China y el gobierno de Estados Unidos. Debido a esta disponibilidad, anunciamos la próxima retirada para el Servicio de caché administrado y el Caché en rol.
>
>El Servicio de caché administrado y el servicio de Caché en rol seguirán estando disponibles para los clientes existentes durante un máximo de 12 meses a partir de la fecha de este anuncio el 30 de noviembre de 2015; la última fecha para el final del servicio será el 30 de noviembre de 2016. Tras esta fecha, se cerrará el Servicio de caché administrado y ya no se admitirá el servicio de Caché en rol.
>
>Eliminaremos la compatibilidad para la creación de nuevas cachés en rol en el primer SDK de Azure que tendrá lugar después del 1 de febrero de 2016. Los clientes podrán abrir proyectos existentes que tengan memorias caché en rol.
>
>Durante este período, animamos a todos los clientes actuales del Servicio de caché administrado y el servicio de Caché en rol para migrar a la Caché en Redis de Azure. Caché en Redis de Azure ofrece más características y un mejor valor general. Para más información sobre la migración, visite la página web de documentación [Migración desde el Servicio de caché administrado a Caché en Redis de Azure](cache-migrate-to-redis.md).
>
>Si tiene preguntas, [póngase en contacto con nosotros](https://azure.microsoft.com/support/options/?WT.mc_id=azurebg_email_Trans_933).

### Caché en Redis de Azure
Caché en Redis de Azure está disponible con carácter general en tamaños de hasta 53 GB y tiene un contrato de nivel de servicio de disponibilidad del 99,9 %. El nuevo [nivel premium](cache-premium-tier.md) ofrece tamaños de hasta 530 GB, además de compatibilidad con clústeres, redes virtuales y persistencia, con un contrato de nivel de servicio del 99,9 %.

Caché en Redis de Azure ofrece a los clientes la posibilidad de usar una Caché en Redis segura y dedicada administrada por Microsoft. Con esta oferta, puede aprovechar el variado conjunto de características y ecosistema proporcionados por Redis, junto con el hospedaje y la supervisión confiables que Microsoft pone a su disposición.

A diferencia de las memorias caché tradicionales que solo tratan con pares clave-valor, Redis es popular por sus tipos de datos de gran rendimiento. Además, Redis también admite la ejecución de operaciones individuales en estos tipos, por ejemplo: asociar a una cadena; incrementar el valor en un objeto hash; insertar en una lista; calcular la intersección, la unión y la diferencia de conjuntos; u obtener el miembro con la clasificación más alta de un conjunto ordenado. Otras características son la compatibilidad con las transacciones, pub/sub, scripting Lua, claves con un período de vida limitado y opciones de configuración que hacen que Redis se comporte como una memoria caché tradicional.

Otro aspecto clave para el éxito de Redis es su ecosistema de código abierto vibrante y en buen estado. Esto se refleja en el variado conjunto de clientes de Redis disponibles en varios lenguajes, que permite que pueda usarlo prácticamente cualquier carga de trabajo compilada dentro de Azure.

Para más información sobre cómo empezar a usar Caché en Redis de Azure, vea [Uso de memoria caché en Redis de Azure](cache-dotnet-how-to-use-azure-redis-cache.md) y la [Documentación de memoria caché en Redis de Azure](https://azure.microsoft.com/documentation/services/redis-cache/).

### Servicio de caché administrado
Si ya es cliente del Servicio de caché administrado de Azure, puede seguir usando este servicio o migrar a Caché en Redis de Azure para aprovechar el completo conjunto de características que ofrece. El Servicio de caché administrado de Azure también está disponible con carácter general y ofrece un contrato de nivel de servicio por el que se garantiza una disponibilidad del 99,9 %.

### Caché en rol
Si usa Caché en rol para autohospedar la memoria caché, puede seguir usando este método. Como Caché en rol es un componente de software autohospedado y no un servicio hospedado de Microsoft, no ofrece ningún contrato de nivel de servicio. Los usuarios de Caché en rol pueden migrar a Caché en Redis de Azure para aprovechar su completo conjunto de características y obtener un contrato de nivel de servicio.

<!---HONumber=AcomDC_1210_2015-->