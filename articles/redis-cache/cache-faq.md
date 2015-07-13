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
	ms.date="05/01/2015" 
	ms.author="sdanie"/>

# P+F de Caché en Redis de Azure

Obtenga información acerca de las respuestas a preguntas comunes, patrones y prácticas recomendadas para Caché en Redis de Azure.

<a name="cache-size"></a>
## ¿Qué oferta y tamaño de Caché en Redis debo utilizar?

Cada oferta de Caché en Redis de Azure ofrece diferentes niveles de opciones de **tamaño**, **ancho de banda**, **alta disponibilidad** y **SLA**.

-	SKU básica: nodo único, sin replicación o SLA, tamaños de caché de 250 MB hasta 53 GB.
-	SKU estándar: nodos principal y secundario con replicación automática, SLA de 99,9%, tamaños de caché de 250 MB a 53 GB.

Si desea alta disponibilidad, elija la oferta de caché estándar que tiene un SLA de 99,9%. La oferta básica puede ser adecuada para el desarrollo y la creación de prototipos, o para escenarios donde no se requiere un SLA.

Los tamaños de caché y el ancho de banda se asignan aproximadamente a los tamaños y al ancho de banda de las máquinas virtuales que hospedan la caché. Los tamaños de 250 MB para las ofertas básicas y estándar se hospedan en el tamaño de máquina virtual extra pequeño (A0), que se hospeda utilizando núcleos compartidos, mientras que los otros tamaños se hospedan mediante núcleos dedicados. Los tamaños de caché de 1 GB se hospedan en el tamaño de máquina virtual pequeño (A1), que tiene 1 núcleo virtual dedicado que se utiliza para mantener el sistema operativo y la caché de redis. Los tamaños de caché mayores se hospedan en instancias de máquinas virtuales más grandes con varios núcleos virtuales dedicados.

Si la memoria caché tiene un alto rendimiento, elija el tamaño de 1 GB o mayor para que la memoria caché se ejecute con núcleos dedicados. El tamaño de la memoria caché de 1 GB se hospeda en una máquina virtual de 1 núcleo. Este núcleo se utiliza para mantener el sistema operativo y la memoria caché. Las cachés mayores de 1 GB se ejecutan en máquinas virtuales con varios núcleos y la caché de Redis utiliza un núcleo dedicado que no se comparte con el sistema operativo.

**Redis es de subproceso único**, por tanto tener dos o más núcleos no proporciona una ventaja adicional sobre tener solo dos núcleos, pero **las máquinas virtuales de mayor tamaño suelen tener más ancho de banda que las de menor tamaño**. Si el servidor o el cliente de caché alcanza a los límites del ancho de banda, recibirá los tiempos de espera del cliente.

En la tabla siguiente se muestran los valores máximos del ancho de banda observados durante la comprobación de los diversos tamaños de Caché en Redis de Azure mediante `redis-benchmark.exe` desde una máquina virtual de Iaas en el extremo de Caché en Redis de Azure. Tenga en cuenta que estos valores no se garantizan y no hay ningún SLA para estos números, pero deben ser los habituales. Debe realizar la prueba de carga de su propia aplicación para determinar el tamaño adecuado de caché para la aplicación.

<table>
  <tr>
    <th>Nombre de caché</th>
    <th>Tamaño de caché</th>
    <th>Get/s (llamadas GET simples de 1 kB valores)</th>
    <th>Ancho de banda (MBits/s)</th>
  </tr>
  <tr>
    <td>C0</td>
    <td>250 MB</td>
    <td>610</td>
    <td>5</td>
  </tr>
  <tr>
    <td>C1</td>
    <td>1 GB</td>
    <td>12.200</td>
    <td>100</td>
  </tr>
  <tr>
    <td>C2</td>
    <td>2,5 GB</td>
    <td>24.300</td>
    <td>200</td>
  </tr>
  <tr>
    <td>C3</td>
    <td>6 GB</td>
    <td>48.875</td>
    <td>400</td>
  </tr>
  <tr>
    <td>C4</td>
    <td>13 GB</td>
    <td>61.350</td>
    <td>500</td>
  </tr>
  <tr>
    <td>C5</td>
    <td>26 GB</td>
    <td>112.275</td>
    <td>1000</td>
  </tr>
  <tr>
    <td>C6</td>
    <td>53 GB</td>
    <td>153.219</td>
    <td>Más de 1.000</td>
  </tr>
</table>

Para obtener instrucciones acerca de cómo descargar las herramientas de Redis como `redis-benchmark.exe`, consulte la sección [¿Cómo puedo ejecutar los comandos de Redis?](#cache-commands).

<a name="cache-region"></a>
## ¿En qué región debo buscar mi caché?

Para obtener el mejor rendimiento y la latencia más baja, busque Caché en Redis de Azure en la misma región que la aplicación cliente de la caché.

<a name="cache-timeouts"></a>
## ¿Por qué estoy viendo los tiempos de expiración?

Los tiempos de expiración se producen en el cliente que usa para comunicarse con Redis. Por lo general, el servidor de Redis no agota el tiempo de espera. Cuando se envía un comando al servidor de Redis, el comando se pone en cola y el servidor de Redis finalmente recoge el comando y lo ejecuta. Sin embargo el cliente puede agotar el tiempo de espera durante este proceso y, si lo hace, se produce una excepción en el lado de la llamada. Para obtener más información sobre cómo solucionar problemas de tiempo de espera, consulte [Investigación de las excepciones del tiempo de expiración en StackExchange.Redis para Caché en Redis de Azure](http://azure.microsoft.com/blog/2015/02/10/investigating-timeout-exceptions-in-stackexchange-redis-for-azure-redis-cache/).

<a name="cache-monitor"></a>
## ¿Cómo se puede supervisar el estado y el rendimiento de la memoria caché?

Se pueden supervisar las instancias de Caché en Redis de Microsoft Azure en el [Portal de vista previa de Azure](https://portal.azure.com). Puede ver las métricas, anclar los gráficos de métricas al panel de inicio, personalizar el intervalo de fecha y hora de los gráficos de supervisión, agregar y quitar métricas de los gráficos y establecer alertas cuando se cumplen determinadas condiciones. Estas herramientas permiten supervisar el estado de las instancias de Caché en Redis de Azure y ayudarle a administrar sus aplicaciones de almacenamiento en caché. Para obtener más información acerca de cómo supervisar las memorias caché, consulte [Supervisión de Caché en Redis de Azure](https://msdn.microsoft.com/library/azure/dn763945.aspx).

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

<table>
  <tr>
    <th>ConfigurationOptions</th>
    <th>Descripción</th>
    <th>Recomendación</th>
  </tr>
  <tr>
    <td>AbortOnConnectFail</td>
    <td>Cuando se establece en true, la conexión no se volverá a conectar después de un error de red.</td>
    <td>Establézcalo en false y deje que StackExchange.Redis se vuelva a conectar automáticamente.</td>
  </tr>
  <tr>
    <td>ConnectRetry</td>
    <td>El número de veces que se repiten los intentos de conexión durante la conexión inicial.</td>
    <td></td>
  </tr>
  <tr>
    <td>ConnectTimeout</td>
    <td>Tiempo de espera en milisegundos para operaciones de conexión.</td>
    <td></td>
  </tr>
</table>

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

-	[Habilite los diagnósticos de caché](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) para que pueda [supervisar](https://msdn.microsoft.com/library/azure/dn763945.aspx) el estado de la memoria caché. Puede ver las métricas en el portal y también [descargarlas y revisarlas](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) usando las herramientas que prefiera.
-	Puede utilizar redis-benchmark.exe para la prueba de carga del servidor Redis.
	-	Asegúrese de que la prueba de carga del cliente y de la caché de Redis se encuentran la misma región.
-	Use redis cli.exe y supervise la memoria caché mediante el comando INFO.
	-	Si la carga provoca una elevada fragmentación de memoria, debe escalar verticalmente a un tamaño mayor de caché.
-	Para obtener instrucciones acerca de cómo descargar las herramientas de Redis, consulte la sección [¿Cómo puedo ejecutar comandos de Redis?](#cache-commands).

<a name="cache-commands"></a>
## ¿Cómo puedo ejecutar comandos de Redis?

Puede utilizar cualquiera de los comandos enumerados en [Comandos de Redis](http://redis.io/commands#). Puede utilizar las siguientes herramientas para ejecutar estos comandos.

-	Descargue las [herramientas de línea de comandos de Redis](https://github.com/MSOpenTech/redis/releases/download/win-2.8.19.1/redis-2.8.19.zip).
-	Conexión a la memoria caché mediante `redis-cli.exe`. Pase el extremo de caché mediante que el modificador -h y la clave mediante - a, tal como se muestra en el ejemplo siguiente.
	-	`redis-cli -h <your cache name>.redis.cache.windows.net -a <key>`
-	Tenga en cuenta que las herramientas de línea de comandos de Redis no funcionan con el puerto SSL, pero puede usar una utilidad como `stunnel` para conectar de forma segura las herramientas al puerto SSL siguiendo las instrucciones de la publicación del blog [Announcing ASP.NET Session State Provider for Redis Preview Release](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) (Comunicación del proveedor de estado de la sesión de ASP.NET para la versión de vista previa de Redis).

<a name="cache-common-patterns"></a>
## ¿Cuáles son algunas consideraciones y patrones comunes de la memoria caché?

-	En Microsoft Patterns & Practices se puede encontrar la guía siguiente.
	-	[Caching guidance](https://github.com/mspnp/azure-guidance/blob/master/Caching.md) (Guía de memoria en caché).
	-	[Azure Cloud Application Design and Implementation Guidance](https://github.com/mspnp/azure-guidance) (Guía de implementación de diseño y aplicaciones de Azure Cloud)
-	[Patrones de caché comunes con Caché en Redis de Azure](cache-howto-common-cache-patterns/)

<a name="cache-reference"></a>
## ¿Por qué Caché en Redis de Azure no tiene una referencia de biblioteca de clases MSDN como otros servicios de Azure?

Caché en Redis de Microsoft Azure se basa en la popular Caché de Redis de código abierto, que proporciona acceso a una caché de Redis segura y dedicada, administrada por Microsoft. Está disponible una variedad de [clientes de Redis](http://redis.io/clients) para muchos lenguajes de programación. Cada cliente tiene su propia API que realiza llamadas a la instancia de Caché de Redis mediante los [comandos de Redis](http://redis.io/commands).

Dado que cada cliente es diferente, no hay no una referencia de clase centralizada en MSDN; en cambio, cada cliente mantiene su propia documentación de referencia. Además de la documentación de referencia, hay varios tutoriales en Azure.com que muestra cómo empezar a trabajar con Caché en Redis de Azure con distintos idiomas y clientes de la caché en la página [Documentación de Caché de Redis](http://azure.microsoft.com/documentatgion/services/redis-cache/).

<!---HONumber=July15_HO1-->