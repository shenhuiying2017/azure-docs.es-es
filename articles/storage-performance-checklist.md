<properties title="Lista de comprobaci&oacute;n de rendimiento y escalabilidad de Almacenamiento de Microsoft Azure " pageTitle="Lista de comprobaci&oacute;n de rendimiento y escalabilidad de Almacenamiento de Microsoft Azure " description="Requerido" metaKeywords="Optional" services="Optional" solutions="Optional" documentationCenter="Optional" authors="Tamra Myers" videoId="Optional" scriptId="Optional" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/08/2014" ms.author="tamram" />

# Lista de comprobación de rendimiento y escalabilidad de Almacenamiento de Microsoft Azure

## Introducción

Desde el lanzamiento de los servicios de Almacenamiento de Microsoft Azure, Microsoft ha desarrollado numerosas prácticas probadas para usar estos servicios de una manera eficiente. Este artículo pretende consolidar lo más importante de ellos en una lista de tipo “lista de comprobación”. El objetivo de este artículo es ayudar a los desarrolladores de aplicaciones a comprobar que están usando prácticas probadas con Almacenamiento de Azure y ayudarles a identificar otras prácticas probadas cuya adopción deben plantearse. Este artículo no pretende cubrir todas las posibilidades de rendimiento y escalabilidad posibles y excluye aquellas cuyo impacto es pequeño o tienen poco margen de aplicación. Hasta donde el comportamiento de la aplicación puede predecirse durante el diseño, es útil recordar esto al comienzo para evitar diseños que tengan problemas de rendimiento.

Todos los desarrolladores de aplicaciones que usen Almacenamiento de Azure deben invertir tiempo en leer este artículo y comprobar que su aplicación sigue todas las prácticas probadas que se enumeran a continuación.

## Lista de comprobación

Este artículo organiza las prácticas probadas en los siguientes grupos. Prácticas probadas aplicables a:

-   Todos los servicios de Almacenamiento de Azure (blobs, tablas, colas y archivos)
-   Blobs
-   Tablas
-   Colas

El servicio Archivos de Azure se encuentra actualmente en vista previa y agregaremos las prácticas probadas aquí próximamente.

<table>
<colgroup>
<col width="25%" />
<col width="25%" />
<col width="25%" />
<col width="25%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">¡Listo!</th>
<th align="left">Ámbito</th>
<th align="left">Category</th>
<th align="left">Pregunta</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"></td>
<td align="left">Todos los servicios</td>
<td align="left">Objetivos de escalabilidad</td>
<td align="left"><a href="#subheading1">¿Está su aplicación diseñada para evitar aproximarse a los objetivos de escalabilidad?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Todos los servicios</td>
<td align="left">Redes</td>
<td align="left"><a href="#subheading2">¿Tienen los dispositivos del cliente un ancho de banda grande y una latencia baja suficientes como para lograr el rendimiento necesario?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Todos los servicios</td>
<td align="left">Redes</td>
<td align="left"><a href="#subheading3">¿Tienen los dispositivos del lado cliente un enlace con una calidad suficientemente alta?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Todos los servicios</td>
<td align="left">Redes</td>
<td align="left"><a href="#subheading4">¿Está la aplicación cliente ubicada &quot;cerca&quot; de la cuenta de almacenamiento?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Todos los servicios</td>
<td align="left">Distribución de contenido</td>
<td align="left"><a href="#subheadin5">¿Usa una red CDN para distribución de contenido?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Todos los servicios</td>
<td align="left">Acceso directo del cliente</td>
<td align="left"><a href="#subheading6">¿Usa SAS y CORS para permitir el acceso directo al almacenamiento en lugar de proxy?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Todos los servicios</td>
<td align="left">Almacenamiento en caché</td>
<td align="left"><a href="#subheading7">¿Su aplicación almacena datos en caché que se usan repetidamente y rara vez cambian?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Todos los servicios</td>
<td align="left">Almacenamiento en caché</td>
<td align="left"><a href="#subheading8">¿Su aplicación procesa por lotes las actualizaciones (almacenándolas en caché en el cliente y, a continuación, cargándolas en conjuntos más grandes)?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Todos los servicios</td>
<td align="left">Configuración .NET</td>
<td align="left"><a href="#subheading9">¿Ha configurado el cliente para usar un número suficiente de conexiones simultáneas?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Todos los servicios</td>
<td align="left">Configuración .NET</td>
<td align="left"><a href="#subheading10">¿Ha configurado .NET para usar un número suficiente de subprocesos?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Todos los servicios</td>
<td align="left">Configuración .NET</td>
<td align="left"><a href="#subheading11">¿Usa .NET 4.5 o posterior, que ha mejorado la recolección de elementos no usados?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Todos los servicios</td>
<td align="left">Paralelismo</td>
<td align="left"><a href="#subheading12">¿Se ha asegurado de que el paralelismo está vinculado correctamente de forma que no se sobrecarguen las capacidades del cliente o los objetivos de escalabilidad?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Todos los servicios</td>
<td align="left">Herramientas</td>
<td align="left"><a href="#subheading13">¿Usa la versión más reciente de las herramientas y bibliotecas de cliente proporcionadas por Microsoft?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Todos los servicios</td>
<td align="left">Reintentos</td>
<td align="left"><a href="#subheading14">¿Usa una directiva de reintentos de retroceso exponencial para errores de limitación y tiempos de espera?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Todos los servicios</td>
<td align="left">Reintentos</td>
<td align="left"><a href="#subheading15">¿Evita su aplicación reintentos para errores que no se pueden reintentar?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Blobs</td>
<td align="left">Objetivos de escalabilidad</td>
<td align="left"><a href="#subheading16">¿Permanece su aplicación dentro del ancho de banda o de los objetivos de escalabilidad de las operaciones para un solo blob?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Blobs</td>
<td align="left">Copia de blobs</td>
<td align="left"><a href="#subheading17">¿Copia blobs de una manera eficiente?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Blobs</td>
<td align="left">Copia de blobs</td>
<td align="left"><a href="#subheading18">¿Usa AzCopy para copias de blobs en masa?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Blobs</td>
<td align="left">Copia de blobs</td>
<td align="left"><a href="#subheading19">¿Usa Importación/Exportación de Microsoft Azure para transferir grandes volúmenes de datos?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Blobs</td>
<td align="left">Usar metadatos</td>
<td align="left"><a href="#subheading20">¿Almacena metadatos usados frecuentemente acerca de blobs en los metadatos de estos?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Blobs</td>
<td align="left">Carga rápida</td>
<td align="left"><a href="#subheading21">Cuando intenta cargar un blob rápidamente, ¿carga blobs en paralelo?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Blobs</td>
<td align="left">Carga rápida</td>
<td align="left"><a href="#subheading22">Cuando intenta cargar muchos blobs rápidamente, ¿carga blobs en paralelo?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Blobs</td>
<td align="left">Tipo de blob correcto</td>
<td align="left"><a href="#subheading23">¿Usa blobs en páginas o blobs en bloques cuando es apropiado?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Tablas</td>
<td align="left">Objetivos de escalabilidad</td>
<td align="left"><a href="#subheading24">¿Se aproxima a los objetivos de escalabilidad para entidades por segundo?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Tablas</td>
<td align="left">Configuración</td>
<td align="left"><a href="#subheading25">¿Usa JSON para sus solicitudes de tabla?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Tablas</td>
<td align="left">Configuración</td>
<td align="left"><a href="#subheading26">¿Ha desactivado el algoritmo de Nagle para mejorar el rendimiento de solicitudes pequeñas?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Tablas</td>
<td align="left">Tablas y particiones</td>
<td align="left"><a href="#subheading27">¿Ha particionado sus datos correctamente?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Tablas</td>
<td align="left">Particiones calientes</td>
<td align="left"><a href="#subheading28">¿Evita los patrones Solo anexar y Solo anteponer?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Tablas</td>
<td align="left">Particiones calientes</td>
<td align="left"><a href="#subheading29">¿Se despliegan sus inserciones y actualizaciones por muchas particiones?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Tablas</td>
<td align="left">Ámbito de las consultas</td>
<td align="left"><a href="#subheading30">¿Ha diseñado su esquema para permitir el uso de consultas puntuales en la mayoría de los casos y el uso de consultas de tabla con moderación?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Tablas</td>
<td align="left">Densidad de las consultas</td>
<td align="left"><a href="#subheading31">¿Normalmente sus consultas solamente examinan y devuelven filas que usará su aplicación?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Tablas</td>
<td align="left">Limitación de datos devueltos</td>
<td align="left"><a href="#subheading32">¿Usa filtros para evitar la devolución de entidades que no se necesitan?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Tablas</td>
<td align="left">Limitación de datos devueltos</td>
<td align="left"><a href="#subheading33">¿Usa proyección para evitar la devolución de propiedades que no se necesitan?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Tablas</td>
<td align="left">Desnormalización</td>
<td align="left"><a href="#subheading34">¿Ha desnormalizado los datos de forma que evite consultas ineficientes o varias solicitudes de lectura cuando intenta obtener datos?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Tablas</td>
<td align="left">Inserción, actualización y eliminación</td>
<td align="left"><a href="#subheading35">¿Procesa por lotes solicitudes que necesitan ser transaccionales o se pueden realizar al mismo tiempo para reducir recorridos de ida y vuelta?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Tablas</td>
<td align="left">Inserción, actualización y eliminación</td>
<td align="left"><a href="#subheading36">¿Evita la recuperación de una entidad simplemente para determinar si llama a la inserción o a la actualización?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Tablas</td>
<td align="left">Inserción, actualización y eliminación</td>
<td align="left"><a href="#subheading37">¿Ha pensado en almacenar series de datos que se recuperarán frecuentemente de forma conjunta en una sola entidad como propiedades en lugar de varias entidades?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Tablas</td>
<td align="left">Inserción, actualización y eliminación</td>
<td align="left"><a href="#subheading38">Para entidades que siempre se recuperarán conjuntamente y que se pueden escribir en lotes (por ejemplo, datos de serie de temporales), ¿ha pensado en usar blobs en lugar de tablas?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Colas</td>
<td align="left">Objetivos de escalabilidad</td>
<td align="left"><a href="#subheading39">¿Se aproxima a los objetivos de escalabilidad para mensajes por segundo?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Colas</td>
<td align="left">Configuración</td>
<td align="left"><a href="#subheading40">¿Ha desactivado el algoritmo de Nagle para mejorar el rendimiento de solicitudes pequeñas?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Colas</td>
<td align="left">Tamaño de los mensajes</td>
<td align="left"><a href="#subheading41">¿Son sus mensajes compactos para mejorar el rendimiento de la cola?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Colas</td>
<td align="left">Recuperación en masa</td>
<td align="left"><a href="#subheading41">¿Recupera varios mensajes en una sola operación &quot;Get&quot;?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Colas</td>
<td align="left">Frecuencia de sondeo</td>
<td align="left"><a href="#subheading42">¿Realiza sondeos con la suficiente frecuencia para reducir la latencia percibida de su aplicación?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Colas</td>
<td align="left">Actualizar mensaje</td>
<td align="left"><a href="#subheading43">¿Usa UpdateMessage para almacenar el progreso en mensajes de procesamiento evitando tener que volver a procesar todo el mensaje si se produce un error?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Colas</td>
<td align="left">Arquitectura</td>
<td align="left"><a href="#subheading44">¿Usa colas para hacer que toda su aplicación sea más escalable manteniendo cargas de trabajo de ejecución prolongada fuera de la ruta de acceso crítica y escala después de forma independiente?</a></td>
</tr>
</tbody>
</table>

## <a name="allservices"></a>Todos los servicios

En esta sección se enumeran las prácticas probadas aplicables al uso de cualquier servicio de Almacenamiento de Azure (blobs, tablas, colas o archivos).

### <a name="subheading1"></a>Objetivos de escalabilidad

Cada uno de los servicios de Almacenamiento de Azure tiene objetivos de escalabilidad en lo que se refiere a capacidad (GB), tasa de transacciones y ancho de banda. Si su aplicación se aproxima o supera cualquiera de estos objetivos de escalabilidad, puede encontrar un aumento en la limitación o latencias de transacción. Cuando un servicio de almacenamiento limita su aplicación, el servicio comienza a devolver códigos de error “503 Servidor ocupado” o “500 Se agotó el tiempo de espera de la operación” para algunas transacciones de almacenamiento. En esta sección se describen tanto el enfoque general para abordar los objetivos de escalabilidad como los objetivos de escalabilidad de ancho de banda en particular. En secciones posteriores que abordan los servicios de almacenamiento individuales, se describen objetivos de escalabilidad en el contexto de ese servicio específico:

-   [Ancho de banda de blob y solicitudes por segundo][¿Permanece su aplicación dentro del ancho de banda o de los objetivos de escalabilidad de las operaciones para un solo blob?]
-   [Entidades de tabla por segundo][¿Se aproxima a los objetivos de escalabilidad para entidades por segundo?]
-   [Mensajes de cola por segundo][¿Se aproxima a los objetivos de escalabilidad para mensajes por segundo?]

#### <a name="sub1bandwidth"></a>Objetivo de escalabilidad de ancho de banda para todos los servicios

En el momento de escribir estas líneas, los objetivos de ancho de banda en EE. UU. para una cuenta de almacenamiento con redundancia geográfica (GRS) son 10 gigabits por segundo (Gbps) para entrada (datos enviados a la cuenta de almacenamiento) y 20 Gbps para salida (datos enviados desde la cuenta de almacenamiento). Para una cuenta de almacenamiento con redundancia local (LRS), los límites son mayores: 20 Gbps para entrada y 30 Gbps para salida. Los límites de ancho de banda internacionales pueden ser menores y puede encontrarlos en nuestra [página de objetivos de escalabilidad][página de objetivos de escalabilidad]. Para obtener más información acerca de las opciones de redundancia de almacenamiento, consulte los vínculos de [recursos útiles][recursos útiles] que se indican a continuación.

#### ¿Qué debo hacer cuando un objetivo de escalabilidad está próximo?

Si su aplicación se aproxima a los objetivos de escalabilidad para una sola cuenta de almacenamiento, plantéese la adopción de uno de los siguientes enfoques:

-   Reconsidere la carga de trabajo que hace que su aplicación se aproxime al objetivo de escalabilidad o lo supere. ¿Puede designarla de forma diferente para que use menos ancho de banda o capacidad, o menos transacciones?
-   Si una aplicación debe superar uno de los objetivos de escalabilidad, debe crear varias cuentas de almacenamiento y particionar los datos de su aplicación entre dichas cuentas. Si usa este patrón, entonces debe asegurarse de designar la aplicación de forma que pueda agregar más cuentas de almacenamiento en el futuro para equilibrio de carga. En el momento escribir estas líneas, cada suscripción de Azure puede tener hasta 100 cuentas de almacenamiento. Las cuentas de almacenamiento tampoco tienen ningún costo que no sea el de su uso en términos de datos almacenados, transacciones realizadas o datos transferidos.
-   Si su aplicación alcanza los objetivos de ancho de banda, plantéese comprimir los datos contenidos en el cliente para reducir el ancho de banda requerido para enviar los datos al servicio de almacenamiento. Tenga en cuenta que aunque esto puede ahorrar ancho de banda y mejorar el rendimiento de la red, también puede tener algunos impactos negativos. Debe evaluar el impacto de rendimiento de todo esto debido a los requisitos de procesamiento adicionales para comprimir y descomprimir datos en el cliente. Además, el almacenamiento de datos comprimidos puede dificultar la solución de problemas porque podría ser más complejo ver datos almacenados usando herramientas estándar.
-   Si su aplicación alcanza los objetivos de escalabilidad, asegúrese entonces de usar un retroceso exponencial para reintentos (consulte [Reintentos][¿Usa una directiva de reintentos de retroceso exponencial para errores de limitación y tiempos de espera?]). Es mejor asegurarse de que nunca se aproxima a los objetivos de escalabilidad (usando uno de los métodos anteriores), pero esto garantizará que la aplicación no sigue realizando reintentos rápidamente, empeorando la limitación.

#### Recursos útiles

Los siguientes vínculos proporcionan detalles adicionales sobre objetivos de escalabilidad:

-   Puede ver los objetivos de escalabilidad en la página [Objetivos de escalabilidad y rendimiento del almacenamiento de Azure en MSDN][página de objetivos de escalabilidad].
-   Puede obtener más información acerca de las opciones de redundancia de almacenamiento en la publicación del blog [Opciones de redundancia de almacenamiento de Microsoft Azure y almacenamiento con redundancia geográfica con acceso de lectura][Opciones de redundancia de almacenamiento de Microsoft Azure y almacenamiento con redundancia geográfica con acceso de lectura] (consulte la sección siguiente para obtener más detalles).
-   Para obtener información actual acerca de los precios de los servicios de Azure, consulte [Precios de Azure][Precios de Azure].

### Redes

Si bien las llamadas de API son importantes, a menudo las restricciones de red físicas de la aplicación tienen un impacto significativo en el rendimiento. A continuación se describen algunas de las limitaciones que pueden encontrar los usuarios.

#### Capacidad de red del cliente

##### <a name="subheading2"></a>Rendimiento

Para el ancho de banda, el problema suele residir en las capacidades del cliente. Por ejemplo, mientras una sola cuenta de almacenamiento puede administrar 10 Gbps o más de entrada (consulte [destinos de escalabilidad de ancho de banda][destinos de escalabilidad de ancho de banda]), la velocidad de la red en una instancia de rol de trabajo de Azure “pequeña” solo es de 100 Mbps aproximadamente. Las instancias de Azure más grandes tienen NIC con mayor capacidad, por lo que debe plantearse el uso de una instancia más grande o más máquinas virtuales si necesita aumentar los límites de red a partir de una sola máquina. Si obtiene acceso a un servicio de almacenamiento desde una aplicación local, entonces se aplica la misma regla: comprender las capacidades de red del dispositivo cliente y la conectividad de red con la ubicación de Almacenamiento de Azure, o bien mejorarlas según sea necesario o designar su aplicación para que funcione dentro de sus capacidades.

##### <a name="subheading3"></a>Calidad del enlace

Como con cualquier uso de red, sea consciente de que las condiciones de la red dan lugar a errores y la pérdida de paquetes reducirá el rendimiento efectivo. El uso de WireShark o NetMon puede ayudar a diagnosticar este problema.

##### Recursos útiles

Para obtener más información acerca de los tamaños de las máquinas virtuales y del ancho de banda designado, consulte [Práctica recomendada para el diseño de servicios a gran escala en Servicios en la nube de Azure][Práctica recomendada para el diseño de servicios a gran escala en Servicios en la nube de Azure] en MSDN.

#### <a name="subheading4"></a>Ubicación

En cualquier entorno distribuido, la ubicación del cliente cerca del servidor ofrece el mejor rendimiento. Para acceder a Almacenamiento de Azure con la mínima latencia, la mejor ubicación para el cliente es dentro de la misma región de Azure. Por ejemplo, si tiene un sitio web Azure que usa Almacenamiento de Azure, debe colocar ambos dentro de una sola región (por ejemplo Oeste de EE. UU. o Sudeste de Asia). Esto reduce la latencia y el costo (en el momento de escribir estas líneas, el uso de ancho de banda dentro de una sola región es gratuito).

Si las aplicaciones cliente no están hospedadas dentro de Azure (como por ejemplo aplicaciones de dispositivos móviles o servicios empresariales locales), una vez más la ubicación de la cuenta de almacenamiento en una región próxima a los dispositivos a los que obtendrá acceso, generalmente reducirá la latencia. Si los clientes están muy distribuidos (por ejemplo algunos en Norteamérica y algunos otros en Europa), debe plantearse el uso de varias cuentas de almacenamiento: una ubicada en una región norteamericana y otra en una región europea. Esto ayudará a reducir la latencia para los usuarios de ambas regiones. Este enfoque normalmente es más sencillo de implementar si los datos que almacena la aplicación son específicos de usuarios individuales y no se requiere la replicación de datos entre cuentas de almacenamiento. Para distribución de contenido amplio, se recomienda una red CDN (consulte la siguiente sección para obtener más detalles).

### <a name="subheading5"></a>Distribución de contenido

Algunas veces, una aplicación necesita servir el mismo contenido a muchos usuarios (por ejemplo, un vídeo de demostración de producto o usado en la página principal de un sitio web) ubicados bien en la misma región, bien en varias regiones. En este escenario, debe usar una Red de entrega de contenido (CDN), como por ejemplo CDN de Azure, y dicha red CDN debe usar el almacenamiento de Azure como el origen de los datos. A diferencia de la cuenta de Almacenamiento de Azure que existe en una sola región y que no puede entregar contenido con baja latencia a otras regiones, la red CDN de Azure usa servidores en varios centros de datos alrededor del mundo. Además, una red CDN normalmente puede admitir límites de salida mucho más altos que una sola cuenta de almacenamiento.

Para obtener más información acerca de la red CDN de Azure, consulte [Red CDN de Azure][Red CDN de Azure].

### <a name="subheading6"></a>Uso de SAS y CORS

Cuando necesita autorizar código, como por ejemplo JavaScript, en un explorador web del usuario o en una aplicación para teléfonos móviles para obtener acceso a datos de Almacenamiento de Azure, un enfoque es usar una aplicación de rol web como un proxy: el dispositivo del usuario se autentica con el rol que, a su vez, se autentica con el servicio de almacenamiento. De esta forma, puede evitar la exposición de sus claves de cuenta de almacenamiento en dispositivos no seguros. Sin embargo, este enfoque coloca una gran sobrecarga en el rol web porque todos los datos transferidos entre el dispositivo del usuario y el servicio de almacenamiento deben pasar a través de dicho rol. Puede evitar el uso de un rol web como un proxy para el servicio de almacenamiento usando firmas de acceso compartido (SAS), algunas veces junto con encabezados de uso compartido de recursos entre orígenes (CORS). Mediante SAS, puede permitir que el dispositivo del usuario realice solicitudes directamente a un servicio de almacenamiento por medio de un token de acceso limitado. Por ejemplo, si un usuario desea cargar una foto en su aplicación, el rol web puede generar y enviar un token SAS al dispositivo del usuario que garantice que el permiso se escribe en un blob o contenedor específico durante los próximos 30 minutos (transcurridos los cuales el token SAS expira).

Normalmente, un explorador no permitirá que el código JavaScript de una página hospedada por un sitio web en un dominio realice operaciones específicas como, por ejemplo, una operación “PUT” en otro dominio. Por ejemplo, si hospeda un rol web en “contosomarketing.cloudapp.net” y desea usar el código JavaScript del cliente para cargar un blob en la cuenta de almacenamiento en “contosoproducts.blob.core.windows.net”, la “misma directiva de origen” del explorador prohibida esta operación. CORS es una característica de explorador que permite al dominio de destino (en este caso la cuenta de almacenamiento) comunicar al explorador en el que confía solicitudes que se originan en el dominio de origen (en este caso el rol web).

Estas dos tecnologías pueden ayudarle a evitar una carga innecesaria (y cuellos de botella) en la aplicación web.

#### Recursos útiles

Para obtener más información acerca de SAS, consulte [Firmas de acceso compartido, parte 1: Descripción del modelo de firmas de acceso compartido][Firmas de acceso compartido, parte 1: Descripción del modelo de firmas de acceso compartido].

Para obtener más información acerca de CORS, consulte [Compatibilidad con Uso compartido de recursos entre orígenes (CORS) para los Servicios de almacenamiento de Azure][Compatibilidad con Uso compartido de recursos entre orígenes (CORS) para los Servicios de almacenamiento de Azure] en MSDN.

### Almacenamiento en caché

#### <a name="subheading7"></a>Obtención de datos

En general, obtener datos de un servicio una vez es mejor que obtenerlos dos veces. Considere el ejemplo de una aplicación web de MVC que se ejecuta en un rol web que ya haya recuperado un blob de 50 MB del servicio de almacenamiento para servir como contenido al usuario. La aplicación podría recuperar después ese mismo blob cada vez que un usuario lo solicitara o podría almacenarlo en caché localmente en el disco y volver a usar la versión almacenada en caché para posteriores solicitudes del usuario. Además, siempre que un usuario solicitara los datos, la aplicación podría emitir un comando GET con un encabezado condicional para la hora de modificación, lo que evitaría obtener todo el blob si no se ha modificado. Puede aplicar este mismo patrón para trabajar con entidades de tabla.

En algunos casos, puede decidir que su aplicación pueda asumir que el blob sigue siendo válido durante un corto período después de recuperarlo y que durante dicho período la aplicación no necesite comprobar si el blob se modificó.

Los datos de configuración y búsqueda, así como otros datos que siempre usa la aplicación, son magníficos candidatos para el almacenamiento en caché.

Para ver un ejemplo de cómo obtener las propiedades de un blob para descubrir la fecha de la última modificación usando .NET, consulte [Establecer y recuperar propiedades y metadatos][Establecer y recuperar propiedades y metadatos] en MSDN. Para obtener más información acerca de las descargas condicionales, consulte [Actualización condicional de una copia local de un blob][Actualización condicional de una copia local de un blob] en MSDN.

#### <a name="subheading8"></a>Carga de datos en lotes

En algunos escenarios de aplicación, puede agregar datos localmente y, a continuación, cargarlos periódicamente en un lote en lugar de cargar cada parte de datos inmediatamente. Por ejemplo, una aplicación web podría mantener un archivo de registro de actividades: la aplicación podría cargar detalles de cada actividad cuando ocurre como una entidad de tabla (lo que requiere muchas operaciones de almacenamiento), o bien podría guardar detalles de la actividad en un archivo de registro local y, a continuación, cargar todos los detalles de actividad periódicamente como un archivo delimitado en un blob. Si cada entrada de registro tiene un tamaño de 1 KB, puede cargar miles en una sola transacción “Put Blob” (puede cargar un blob de hasta 64 MB de tamaño en una sola transacción). Por supuesto, si la máquina local se bloquea antes de la carga, posiblemente perderá algunos datos de registro: el desarrollador de la aplicación debe realizar el diseño contemplando la posibilidad de errores del dispositivo cliente o de carga. Si los datos de actividad necesitan descargarse para períodos de tiempo (no simplemente una actividad), se recomiendan los blobs sobre las tablas.

### Configuración .NET

Si usa .NET Framework, esta sección enumera varias configuraciones rápidas que puede usar para realizar mejoras de rendimiento significativas. Si usa otros lenguajes, compruebe si se aplican conceptos similares en el lenguaje elegido.

#### <a name="subheading9"></a>Aumento del límite de conexiones predeterminado

En .NET, el siguiente código aumenta el límite de conexiones predeterminado (que normalmente es 2 en un entorno cliente o 10 en un entorno servidor) a 100. Normalmente, debe establecer el valor en aproximadamente el número de subprocesos usados por su aplicación.

    ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  

Debe establecer el límite de conexiones antes de abrir cualquier conexión.

Para otros lenguajes de programación, consulte la documentación del lenguaje en cuestión para determinar cómo establecer el límite de conexiones.

Para obtener información adicional, consulte la publicación [Servicios web: conexiones simultáneas][Servicios web: conexiones simultáneas] en MSDN.

#### <a name="subheading10"></a>Aumento de los subprocesos mínimos ThreadPool si se usa código sincrónico con tareas asincrónicas

Este código aumentará los subprocesos mínimos del grupo de subprocesos:

    ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  

Para obtener más información, consulte [Método ThreadPool.SetMinThreads][Método ThreadPool.SetMinThreads] en MSDN.

#### <a name="subheading11"></a>Aprovechamiento de la recolección de elementos no usados de .NET 4.5

Use .NET 4.5 o posterior para que la aplicación cliente aproveche las mejoras de rendimiento en la recolección de elementos no usados del servidor.

Para obtener más información, consulte el artículo [Información general de las mejoras de rendimiento en .NET 4.5][Información general de las mejoras de rendimiento en .NET 4.5] en MSDN.

### <a name="subheading12"></a>Paralelismo no vinculado

Aunque el paralelismo puede ser magnífico para el rendimiento, tenga cuidado cuando use paralelismo no vinculado (sin límite en el número de subprocesos y/o solicitudes paralelas) para cargar o descargar datos y cuando use varios trabajadores para obtener acceso a varias particiones (contenedores, colas o particiones de tabla) en la misma cuenta de almacenamiento o para obtener acceso a varios elementos de la misma partición. Si el paralelismo es no vinculado, la aplicación puede superar las capacidades del dispositivo cliente o los objetivos de escalabilidad de la cuenta de almacenamiento, lo que puede dar lugar a latencias más largas y a limitaciones.

### <a name="subheading13"></a>Herramientas y bibliotecas de cliente de almacenamiento

Use siempre las herramientas y bibliotecas de cliente más recientes proporcionadas por Microsoft. En el momento de escribir estas líneas, hay bibliotecas RTM disponibles para .NET, Windows Phone, Windows Runtime y Java, así como bibliotecas de vista previa en otros lenguajes como node.js y C++. Además, Microsoft ha lanzado cmdlets PowerShell y herramientas de línea de comandos entre plataformas desarrolladas en Node.js para trabajar con Almacenamiento de Azure. Microsoft desarrolla de forma activa estas herramientas pensando en el rendimiento, manteniéndolas actualizadas con las versiones de servicio más recientes, y garantiza que administran muchas de las prácticas de rendimiento probadas internamente.

### Reintentos

#### <a name="subheading14"></a>Limitación y servidor ocupado

En algunos casos, el servicio de almacenamiento puede limitar su aplicación o simplemente no ser capaz de atender a la solicitud debido a alguna condición transitoria y devolver un mensaje “503 Servidor ocupado” o “500 Se agotó el tiempo de espera”. Esto puede ocurrir si la aplicación se está aproximando a cualquiera de los objetivos de escalabilidad o si el sistema está equilibrando los datos particionados para permitir un mayor rendimiento. Normalmente, la aplicación cliente debe de intentar la operación que causa tal error: el intento de llevar a cabo la misma solicitud más tarde puede funcionar. Sin embargo, si el servicio de almacenamiento está limitando la aplicación porque está superando los objetivos de escalabilidad, o incluso si el servicio no pudo atender a la solicitud por alguna otra razón, lo intentos agresivos normalmente empeorarán el problema. Por esta razón, debe usar un retroceso exponencial (las bibliotecas de cliente se comportan así de forma predeterminada). Por ejemplo, su aplicación puede llevar a cabo los reintentos después de 2 segundos, luego 4 segundos, después 10 segundos, luego 30 segundos y, después, renunciar. Este comportamiento da lugar a una reducción significativa de la carga de la aplicación en el servicio en lugar de agravar los problemas.

Tenga en cuenta que los errores de conectividad se pueden reintentar inmediatamente porque no se derivan de la limitación y se espera que sean transitorios.

#### <a name="subheading15"></a>Errores que no se pueden reintentar

Las bibliotecas de cliente saben qué errores se pueden reintentar y cuáles no. Sin embargo, si escribe su propio código contra la API REST de almacenamiento, recuerde que hay algunos errores que no deberá intentar: Por ejemplo, una respuesta 400 (Solicitud incorrecta) indica que la aplicación cliente envió una solicitud que no se pudo procesar porque no tenía la forma esperada. El reenvío de esta solicitud dará lugar a la misma respuesta cada vez, por lo que no tiene sentido reintentarla. Si escribe su propio código contra la API REST de almacenamiento, sepa lo que significan los códigos de error y la forma adecuada de reintentar (o no) cada uno de ellos.

#### Recursos útiles

Para obtener más información acerca de códigos de error de almacenamiento, consulte [Códigos de estado y de error][Códigos de estado y de error] en el sitio web de Microsoft Azure.

## Blobs

Además de las prácticas probadas para [todos los servicios][todos los servicios] descritas anteriormente, las siguientes prácticas probadas se aplican específicamente al servicio BLOB.

### Objetivos de escalabilidad específicos de blob

#### <a name="subheading16"></a>Ancho de banda y operaciones por blob

Puede realizar operaciones de lectura y escritura en un solo blob a una velocidad máxima de 60 MB/segundos (lo que equivale a 480 Mbps aproximadamente, lo cual supera las capacidades de muchas redes de cliente, incluida la tarjeta NIC física del dispositivo cliente). Además, un solo blob admite hasta 500 solicitudes por segundo. Si tiene varios clientes que necesitan leer el mismo blob y podría superar estos límites, debe plantearse el uso de una red CDN para distribuir dicho blob.

Para obtener más información acerca del rendimiento objetivo para blobs, consulte [Objetivos de escalabilidad y rendimiento del almacenamiento de Azure][página de objetivos de escalabilidad] en MSDN.

### Copia y movimiento de blobs

#### <a name="subheading17"></a>Copia de blobs

La versión del 12 de febrero de 2012 de API REST introdujo la útil capacidad de copiar blobs entre cuentas: una aplicación cliente puede indicar al servicio de almacenamiento que copie un blob desde otro origen (posiblemente en una cuenta de almacenamiento diferente) y, a continuación, dejar que el servicio realice la copia asincrónicamente. Esto puede reducir significativamente el ancho de banda necesario para la aplicación cuando migra datos desde otras cuentas de almacenamiento porque no necesita descargar y cargar los datos.

Una consideración, sin embargo, es que, cuando realiza copias entre cuentas de almacenamiento, no hay una garantía de tiempo sobre cuándo se completará la copia. Si la aplicación necesita completar una copia de blobs rápidamente bajo su control, puede ser mejor copiar dicho blob descargándolo a la máquina virtual y, a continuación, cargándolo en el destino. Para conseguir previsibilidad completa en esa situación, asegúrese de que la copia se realiza por una máquina virtual que se ejecuta en la misma región de Azure ya que, de lo contrario, las condiciones de la red pueden (y probablemente lo harán) afectar al rendimiento de la copia. Además, puede supervisar el progreso de una copia asincrónica mediante programación.

Tenga en cuenta que las copias dentro de la misma cuenta de almacenamiento, por lo general, se completan rápidamente.

Para obtener más información, consulte [Copy Blob][Copy Blob] en MSDN.

#### <a name="subheading18"></a>Uso de AzCopy

El equipo de Almacenamiento de Azure ha lanzado “AzCopy”, una herramienta de línea de comandos diseñada para ayudarle a transferir muchos blobs a, desde y entre cuentas de almacenamiento. Esta herramienta está optimizada para este escenario y puede lograr altas tasas de transferencia. Su uso es muy recomendable para escenarios de carga, descarga y copia en masa. Puede obtener más información acerca de esta herramienta y descargarla [aquí][aquí].

#### <a name="subheading19"></a>Servicio Importación/Exportación de Azure

Para volúmenes muy grandes de datos (más de 1 TB), Almacenamiento de Azure ofrece el servicio Importación/Exportación, que permite realizar operaciones de carga y descarga desde el almacenamiento de blobs enviando unidades de disco duro. Puede poner sus datos en una unidad de disco duro y enviarla a Microsoft para cargarlos o enviar una unidad de disco duro vacía a Microsoft para descargar datos. Puede obtener más información acerca de ello [aquí][2]. Esto puede ser mucho más eficiente que cargar y descargar este volumen de datos a través de la red.

### <a name="subheading20"></a>Uso de metadatos

El servicio BLOB admite solicitudes de encabezado, que pueden incluir metadatos acerca del blob. Por ejemplo, si su aplicación necesita los datos EXIF fuera de una foto, podría recuperar la foto y extraerlos. Para ahorrar ancho de banda y mejorar el rendimiento, la aplicación podría almacenar los datos EXIF en los metadatos del blob cuando la aplicación carga la foto: después, puede recuperar los datos EXIF de los metadatos usando solamente una solicitud HEAD, ahorrando un ancho de banda significativo y el tiempo de procesamiento necesario para extraer los datos EXIF cada vez que se lee el blob. Esto sería útil en escenarios donde solamente necesita los metadatos y no el contenido completo de un blob. Tenga en cuenta que solamente se pueden almacenar 8 KB de metadatos por blob (el servicio no aceptará una solicitud para almacenar más que eso), por lo que si los datos no caben en ese tamaño, no podrá usar este enfoque.

Para ver un ejemplo de cómo obtener los metadatos de un blob usando .NET, consulte [Establecer y recuperar propiedades y metadatos][Establecer y recuperar propiedades y metadatos] en MSDN.

### Carga rápida

Para cargar blobs rápidamente, la primera pregunta en responder es: ¿va a cargar un blob o muchos? Utilice las instrucciones siguientes para determinar el método correcto que va a utilizar dependiendo del escenario.

#### <a name="subheading21"></a>Carga de un blob grande rápidamente

Para cargar un solo blob grande rápidamente, la aplicación cliente debe cargar sus bloques o páginas en paralelo (teniendo en cuenta los objetivos de escalabilidad para blobs individuales y la cuenta de almacenamiento como un todo). Tenga en cuenta que las bibliotecas del cliente de almacenamiento RTM proporcionadas por Microsoft (.NET y Java) tienen la capacidad de hacer esto. Para cada una de las bibliotecas, use el objeto o propiedad que se especifica a continuación para establecer el nivel de simultaneidad:

-   .NET: establezca ParallelOperationThreadCount en un objeto BlobRequestOptions para usar.
-   Java/Android: use BlobRequestOptions.setConcurrentRequestCount()
-   Node.js: use parallelOperationThreadCount en las opciones de solicitud o en el servicio BLOB.
-   C++: use el método blob\_request\_options::set\_parallelism\_factor.

#### <a name="subheading22"></a>Cargar muchos blobs rápidamente

Para cargar muchos blobs rápidamente, cárguelos en paralelo. Este método es más rápido que cargar blobs de uno en uno con cargas de bloque paralelas porque distribuye la carga entre varias particiones del servicio de almacenamiento. Un solo blob únicamente admite un rendimiento de 60 MB/segundo (aproximadamente 480 Mbps). En el momento de escribir estas líneas, una cuenta GRS admite entradas de hasta 20 Gbps que está más lejos del rendimiento admitido por un blob individual. [AzCopy][¿Usa AzCopy para copias de blobs en masa?] realiza cargas en paralelo de forma predeterminada y se recomienda para este escenario.

### <a name="subheading23"></a>Elección del tipo correcto de blob

Almacenamiento de Azure admite dos tipos de blob: blobs en *páginas* y blobs en *bloques*. Para un escenario de uso dado, el tipo de blob que elija afectará al rendimiento y escalabilidad de la solución. Los blobs en bloques son apropiados cuando desea cargar grandes cantidades de datos de forma eficiente: por ejemplo, una aplicación cliente puede que necesite cargar fotos o vídeos a almacenamiento de blobs. Los blobs en páginas son apropiados si la aplicación necesita realizar escrituras aleatorias en los datos: por ejemplo, los discos duros virtuales de Azure se almacenan como blobs en páginas.

Para obtener más información, consulte [Introducción a los blobs en bloques y a los blobs en páginas][Introducción a los blobs en bloques y a los blobs en páginas] en MSDN.

## Tablas

Además de las prácticas probadas para [todos los servicios][todos los servicios] descritas anteriormente, las siguientes prácticas probadas se aplican específicamente al servicio Tabla.

### <a name="subheading24"></a>Objetivos de escalabilidad específicos de tabla

Además de las limitaciones de ancho de banda de una cuenta de almacenamiento completa, las tablas tienen el siguiente límite de escalabilidad específico. Tenga en cuenta que el sistema equilibrará la carga a medida que el tráfico aumente, pero si este tráfico tiene ráfagas repentinas, no podrá obtener este volumen de rendimiento inmediatamente. Si su patrón tiene ráfagas, es de esperar que se produzcan limitaciones y/o tiempos de espera durante dichas ráfagas, ya que el servicio de almacenamiento equilibrará la carga automáticamente fuera de la tabla. El aumento lento generalmente tiene mejores resultados ya que proporciona al sistema tiempo para equilibrar la carga apropiadamente.

#### Entidades por segundo (cuenta)

El límite de escalabilidad para el acceso a tablas es de hasta 20.000 unidades (1 KB cada una) por segundo para una cuenta. En general, cada entidad que se inserta, actualiza, elimina o examina, cuenta para este objetivo. Por tanto, una inserción por lotes que contiene 100 entidades contaría como 100 entidades. Una consulta que examina 1.000 y devuelve 5, contaría como 1.000 entidades.

#### Entidades por segundo (partición)

Dentro de una sola partición, el objetivo de escalabilidad para obtener acceso a las tablas es de 2.000 entidades (1 KB cada una) por segundo, usando el mismo recuento descrito en la sección anterior.

### Configuración

En esta sección se enumeran varias configuraciones rápidas que puede usar para realizar mejoras de rendimiento significativas en el servicio Tabla:

#### <a name="subheading25"></a>Uso de JSON

A partir de la versión del 15 de agosto de 2013 del servicio de almacenamiento, el servicio Tabla admite el uso de JSON en lugar del formato AtomPub basado en XML para transferir datos de tabla. Esto puede reducir los tamaños de carga hasta en un 75 % y puede mejorar significativamente el rendimiento de la aplicación.

Para obtener más información, consulte la publicación [Tablas de Microsoft Azure: introducción a JSON][Tablas de Microsoft Azure: introducción a JSON] y [Formato de carga para las operaciones del servicio Tabla][Formato de carga para las operaciones del servicio Tabla] en MSDN.

#### <a name="subheading26"></a>Desactivación de Nagle

El algoritmo de Nagle está ampliamente implementado en redes TCP/IP como un medio de mejorar el rendimiento de red. Sin embargo, no es óptimo en todas las situaciones (como por ejemplo en entornos altamente interactivos). Para Almacenamiento de Azure, el algoritmo de Nagle tiene un impacto negativo en el rendimiento de solicitudes en los servicios Tabla y Cola y debe deshabilitarlo si es posible.

Para obtener más información, consulte nuestra publicación en blob [El algoritmo de Nagle no es idóneo para pequeñas solicitudes][El algoritmo de Nagle no es idóneo para pequeñas solicitudes], que explica por qué el algoritmo de Nagle interactúa de forma precaria con solicitudes de tabla y cola, y muestra cómo deshabilitarlo en su aplicación cliente.

### Esquema

La forma de representar los datos y realizar consultas en los mismos es el factor más importante por sí mismo que afecta al rendimiento del servicio Tabla. Aunque cada aplicación es diferente, en esta sección se describen algunas prácticas probadas generales relacionadas con:

-   Diseño de tablas
-   Consultas eficientes
-   Actualizaciones de datos eficientes

#### <a name="subheading27"></a>Tablas y particiones

Las tablas se dividen en dos particiones. Cada entidad almacenada en una partición comparte la misma clave de partición y tiene una clave de fila única para identificarla dentro de esa partición. Las particiones proporcionan ventajas pero también presentan limitaciones de escalabilidad.

-   Ventajas: puede actualizar entidades de la misma partición en una sola transacción por lotes atómica que contenga hasta 100 operaciones de almacenamiento independientes (límite de tamaño total de 4 MB). Suponiendo que se recupera el mismo número de entidades, también puede consultar datos dentro de una sola partición más eficientemente que los datos que se extienden por particiones (siga leyendo para conocer más recomendaciones sobre la consulta de datos de tabla).
-   Límite de escalabilidad: el equilibrio de carga no se puede realizar en el acceso a entidades almacenadas en una sola partición porque las particiones admiten transacciones por lotes atómicas. Por esta razón, el objetivo de escalabilidad para una partición de tabla individual es inferior al del servicio Tabla como un todo.

Debido a estas características de tablas y particiones, debe adoptar los siguientes principios de diseño:

-   Los datos que su aplicación cliente actualiza o consulta frecuentemente en la misma unidad lógica de trabajo se deben ubicar en la misma partición. Esto puede ser porque la aplicación agrega escrituras o porque desea aprovechar las operaciones por lotes atómicas. Asimismo, los datos de una sola partición pueden ser consultados de forma más eficiente en una sola consulta que los datos que se encuentran repartidos por particiones.
-   Los datos que la aplicación cliente no inserta, actualiza o consulta en la misma unidad lógica de trabajo (una consulta o actualización por lotes) se deben encontrar en particiones independientes. Una nota importante es que no hay límite en el número de claves de partición en una sola tabla, por lo que tener millones de claves de partición no es un problema y no afectará al rendimiento. Por ejemplo, si la aplicación es un sitio web conocido con inicio de sesión de usuario, el uso del identificador de usuario como clave de partición podría ser una buena elección.

#### Particiones calientes

Una partición caliente es aquella que recibe un porcentaje desproporcionado de tráfico en una cuenta y no se puede realizar un equilibrio de carga en ella porque es una sola partición. En general, las particiones calientes se crean de una de dos formas:

##### <a name="subheading28"></a>Patrones Solo anexar y Solo anteponer

El patrón “Solo anexar” es aquel donde todo (o prácticamente todo) el tráfico a una clave de partición dada aumenta y disminuye conforme a la hora actual. Un ejemplo es si la aplicación usó la fecha actual como clave de partición para datos de registro. El resultado de esto es que todas las inserciones van a la última partición de la tabla y el sistema no puede equilibrar la carga porque todas las escrituras van al final de la tabla. Si el volumen del tráfico en esa partición supera el objetivo de escalabilidad de nivel de partición, dará lugar a una limitación. Es mejor garantizar que el tráfico se envía a varias particiones para habilitar el equilibrio de carga en las solicitudes en la tabla.

##### <a name="subheading29"></a>Datos con mucho tráfico

Si el esquema de participaciones da lugar a una sola partición que solamente tiene datos y que se utiliza mucho más que otras particiones, también puede ver limitaciones cuando esa partición se aproxime al objetivo de escalabilidad para una sola partición. Es mejor asegurarse de que el esquema de partición no da lugar a una sola partición que se aproxima a los objetivos de escalabilidad.

#### Consultas

En esta sección se describen prácticas probadas para realizar consultas en el servicio Tabla.

##### <a name="subheading30"></a>Ámbito de las consultas

Hay varias formas de especificar el intervalo de entidades para consultar. A continuación se muestra un análisis de los usos de cada una de ellas.

En general, evite los exámenes (consultas más grandes que una sola entidad), pero si debe realizarlos, intente organizar los datos de forma que dichos exámenes recuperen los datos que necesita sin examinar o devolver grandes cantidades de entidades que no necesita.

###### Consultas puntuales

Una consulta puntual recupera exactamente una entidad. Lo hace especificando tanto la clave de partición como la clave de fila de la entidad que se va a recuperar. Estas consultas son muy diferentes y debe usarlas siempre que sea posible.

###### Consultas de partición

Una consulta de partición es una consulta que recupera un conjunto de datos que comparte una clave de partición común. Normalmente, la consulta especifica un intervalo de valores de clave de fila o un intervalo de valores para alguna propiedad de entidad además de una clave de partición. Son menos eficientes que las consultas puntuales y se deben usar con moderación.

###### Consultas de tabla

Una consulta de tabla es una consulta que recupera un conjunto de entidades que no comparte una clave de partición común. Estas consultas no son diferentes y debe evitarlas siempre que sea posible.

##### <a name="subheading31"></a>Densidad de las consultas

Otro factor clave en la eficiencia de las consultas es el número de entidades devueltas comparado con el número de entidades examinadas para encontrar el conjunto devuelto. Si la aplicación realiza una consulta de tabla con un filtro para un valor de propiedad que solamente comparte el 1 % de los datos, la consulta examinará 100 entidades por cada entidad que devuelva. Todos los objetivos de escalabilidad de tabla analizados anteriormente están relacionados con el número de entidades examinadas y no con el número de entidades de vueltas: una densidad de consultas baja puede hacer que el servicio Tabla límite fácilmente la aplicación porque debe examinar muchas entidades para recuperar la entidad que está buscando. Consulte la sección siguiente sobre [desnormalización][¿Ha desnormalizado los datos de forma que evite consultas ineficientes o varias solicitudes de lectura cuando intenta obtener datos?] para obtener más información sobre cómo evitar esto.

##### Limitación de la cantidad de datos devueltos

###### <a name="subheading32"></a>Filtros

Donde sepa que una consulta va a devolver entidades que no necesita en la aplicación cliente, plantéese el uso de un filtro para reducir el tamaño del conjunto devuelto. Aunque las entidades no devueltas al cliente siguen contando para los límites de escalabilidad, el rendimiento de la aplicación mejorará debido a la reducción del tamaño de la carga de red y a la reducción del número de entidades que la aplicación cliente debe procesar. No obstante, tenga en cuenta la nota anterior del apartado [Densidad de las consultas][¿Normalmente sus consultas solamente examinan y devuelven filas que usará su aplicación?]: los objetivos de escalabilidad están relacionados con el número de entidades examinadas, de forma que una consulta que filtre muchas entidades, puede seguir dando lugar a limitaciones aunque se devuelvan pocas entidades.

###### <a name="subheading33"></a>Proyección

Si la aplicación cliente solamente necesita un conjunto limitado de propiedades de las entidades de la tabla, puede usar proyección para limitar el tamaño del conjunto de datos devuelto. Como en el caso de los filtros, esto ayuda a reducir la carga de red y el procesamiento del cliente.

##### <a name="subheading34"></a>Desnormalización

A diferencia del trabajo con bases de datos relacionales, las prácticas probadas para consultar datos de tabla de forma eficiente conducen a la desnormalización de los datos. Es decir, duplicar los mismos datos en varias entidades (una por cada clave que puede usar para encontrar los datos) para minimizar el número de entidades que una consulta debe examinar para encontrar los datos que el cliente necesita, en lugar de tener que examinar grandes números de entidades para encontrar los datos que la aplicación necesita. Por ejemplo, en un sitio web de comercio electrónico, puede que le interese encontrar un pedido tanto por el identificador del cliente (deme los pedidos de este cliente) como por la fecha (deme los pedidos con esa fecha). En el almacenamiento de tablas es mejor almacenar la entidad (o una referencia a ella) dos veces, una vez con el nombre de tabla, la clave de partición y la clave de fila para facilitar la búsqueda por identificador de cliente y una vez para facilitar su búsqueda por la fecha.

#### Inserción, actualización y eliminación

En esta sección se describen prácticas probadas para modificar entidades almacenadas en el servicio Tabla.

##### <a name="subheading35"></a>Lotes

En Almacenamiento de Azure, las transacciones por lotes son conocidas como transacciones con grupos de entidades (ETG); todas las operaciones dentro de una ETG deben estar en una sola partición y, a su vez, en una única tabla. Cuando sea posible, use ETG para realizar inspecciones, actualizaciones y eliminaciones por lotes. Esto reduce el número de recorridos de ida y vuelta de la aplicación cliente al servidor, disminuye el número de transacciones facturables (una ETG cuenta como una sola transacción desde el punto de vista de facturación y puede contener hasta 100 operaciones de almacenamiento) y permite actualizaciones atómicas (todas las operaciones se realizan correctamente o ninguna de ellas se realiza correctamente dentro de una ETG). Los entornos con altas latencias, como por ejemplo dispositivos móviles, sacarán un enorme provecho al uso de ETG.

##### <a name="subheading36"></a>Upsert

Use operaciones **Upsert** de tabla siempre que sea posible. Hay dos tipos de operaciones **Upsert**; ambos pueden ser más eficientes que las operaciones **Insert** y **Update** tradicionales:

-   **InsertOrMerge**: use este tipo cuando desee cargar un subconjunto de propiedades de la entidad sin estar seguro de si la entidad ya existe o no. Si la entidad ya existe, esta llamada actualiza las propiedades incluidas en la operación **Upsert** y deja todas las propiedades existentes tal y como están; si la entidad no existe, inserta la nueva entidad. Esto es similar a usar proyección en una consulta, donde solamente necesita actualizar las propiedades que cambian.
-   **InsertOrReplace**: use este tipo cuando desee cargar una nueva entidad completamente sin estar seguro de si ya existe o no. Solamente debe usar este tipo cuando sabe que la entidad recién cargada es totalmente correcta porque sobrescribe la entidad antigua completamente. Por ejemplo, desea actualizar la entidad que almacena la ubicación actual de un usuario independientemente de si la aplicación ha almacenado los datos de ubicación del usuario anteriormente; la nueva entidad de ubicación está completa y no necesita ninguna información de ninguna otra entidad anterior.

##### <a name="subheading37"></a>Almacenamiento de series de datos en una sola entidad

Algunas veces, una aplicación almacena una serie de datos que frecuentemente necesita para recuperarlos todos a la vez: por ejemplo, un aplicación podría realizar un seguimiento del uso de CPU a lo largo del tiempo para representar un gráfico dinámico de los datos de las últimas 24 horas. Un enfoque es tener una entidad de tabla por hora, de forma que cada entidad represente una hora específica y almacene el uso de CPU para esa hora. Para representar estos datos, la aplicación necesita recuperar las entidades que contienen los datos de las 24 horas más recientes.

Alternativamente, la aplicación podría almacenar el uso de CPU por cada hora como una propiedad independiente de una sola entidad: para actualizar cada hora, la aplicación puede usar una sola llamada **InsertOrMerge Upsert** para actualizar el valor para la hora más reciente. Para representar los datos, la aplicación solamente necesita recuperar una sola entidad en lugar de 24, creando a una consulta muy eficiente (consulte el análisis anterior acerca del [ámbito de las consultas][¿Ha diseñado su esquema para permitir el uso de consultas puntuales en la mayoría de los casos y el uso de consultas de tabla con moderación?]).

##### <a name="subheading38"></a>Almacenamiento de datos estructurados en blobs

Algunas veces, da la sensación de que los datos estructurados deben ir en tablas, pero los intervalos de entidades siempre se recuperan conjuntamente y se pueden insertar por lotes. Un buen ejemplo de esto es un archivo de registro. En este caso, puede procesar por lotes varios minutos de registros, insertarlos y, después, siempre estará recuperando varios minutos de registros simultáneamente. En este caso, por rendimiento, es mejor usar blobs en lugar de tablas ya que puede reducir significativamente el número de objetos escritos y devueltos, así como, por lo general, el número de solicitudes que necesita realizar.

## Colas

### <a name="subheading39&quot;"></a>Scalability Limits

A single queue can process approximately 2,000 messages (1KB each) per second (each AddMessage, GetMessage, and DeleteMessage count as a message here). If this is insufficient for your application, you should use multiple queues and spread the messages across them.

You can view the current scalability targets on the page [Azure Storage Scalability and Performance Targets][página de objetivos de escalabilidad] on MSDN.

### <a name="subheading40&quot;"></a>Desactivación de Nagle

Consulte la sección sobre la configuración de tablas que analiza el algoritmo de Nagle (el algoritmo de Nagle suele ser malo para el rendimiento de solicitudes de cola y debe deshabilitarlo).

### <a name="subheading41&quot;"></a>Message Size

Queue performance and scalability decreases as message size increases. You should place only the information the receiver needs in a message.

### <a name="subheading42&quot;"></a>Recuperación por lotes

Puede recuperar hasta 32 mensajes de una cola en una sola operación. Esto puede reducir el número de recorridos de ida y vuelta de la aplicación cliente, lo cual es especialmente útil para entornos, como por ejemplo dispositivos móviles, con alta latencia.

### <a name="subheading43&quot;"></a>Queue Polling Interval

Most applications poll for messages from a queue, which can be one of the largest sources of transactions for that application. Select your polling interval wisely: polling too frequently could cause your application to approach the scalability targets for the queue. However, at 200,000 transactions for $0.01 (at the time of writing), a single processor polling once every second for a month would cost less than 15 cents so cost is not typically a factor that affects your choice of polling interval.

For up to date cost information, see [Storage Pricing Details][Storage Pricing Details].

### <a name="subheading44&quot;"></a>UpdateMessage

Puede usar **UpdateMessage** para aumentar el tiempo de espera de invisibilidad o para actualizar la información de estado de un mensaje. Aunque esto es muy eficiente, recuerde que cada operación **UpdateMessage** cuenta para el objetivo de escalabilidad. Sin embargo, esto puede ser un enfoque mucho más eficiente que tener un flujo de trabajo que pasa un trabajo de una cola a la siguiente, cuando cada paso del trabajo se completa. El uso de la operación **UpdateMessage** permite que la aplicación guarde el estado del trabajo en el mensaje y, a continuación, continúe trabajando, en lugar de volver a poner en cola el mensaje para el próximo paso del trabajo cada vez que se completa un paso.

Para obtener más información, consulte [Cambio del contenido de un mensaje en cola][Cambio del contenido de un mensaje en cola].

### \<a name=subheading45"\>\</a\>Arquitectura de la aplicación\</h3\> \<p\>Debe usar colas para hacer que la arquitectura de la aplicación sea escalable. A continuación se enumeran algunas formas de usar colas para hacer que la aplicación sea más escalable:\</p\> \<ul\> \<li\>Puede usar colas para crear trabajos pendientes para el procesamiento y aliviar las cargas de la aplicación. Por ejemplo, podría poner en cola solicitudes de usuarios para realizar un trabajo que requiere muchos recursos de procesador, como por ejemplo cambiar el tamaño de las imágenes cargadas.\</li\> \<li\>Puede usar colas para desacoplar partes de la aplicación para poder escalarlas por separado. Por ejemplo, un front-end web podría poner resultados de encuesta de usuarios en una cola para analizarlos y almacenarlos posteriormente. Podría agregar más instancias de rol de trabajo para procesar los datos de cola según sea necesario.\</li\> \</ul\> \<h2 id="conclusion"\>Conclusión\</h2\> \<p\>En este artículo se analizaron algunas de las prácticas probadas más comunes para optimizar el rendimiento cuando se usa Almacenamiento de Azure. Animamos a todos los desarrolladores de aplicaciones a que evalúen sus aplicaciones tomando como referencia todas las prácticas anteriores y que se planteen seguir las recomendaciones para obtener un magnífico rendimiento para aquellas de sus aplicaciones que usan Almacenamiento de Azure.\</p\>

  [¿Usa una directiva de reintentos de retroceso exponencial para errores de limitación y tiempos de espera?]: #subheading14
  [¿Permanece su aplicación dentro del ancho de banda o de los objetivos de escalabilidad de las operaciones para un solo blob?]: #subheading16
  [¿Usa AzCopy para copias de blobs en masa?]: #subheading18
  [¿Se aproxima a los objetivos de escalabilidad para entidades por segundo?]: #subheading24
  [¿Ha diseñado su esquema para permitir el uso de consultas puntuales en la mayoría de los casos y el uso de consultas de tabla con moderación?]: #subheading30
  [¿Ha desnormalizado los datos de forma que evite consultas ineficientes o varias solicitudes de lectura cuando intenta obtener datos?]: #subheading34
  [¿Se aproxima a los objetivos de escalabilidad para mensajes por segundo?]: #subheading39
  [página de objetivos de escalabilidad]: http://msdn.microsoft.com/es-es/library/azure/dn249410.aspx
  [recursos útiles]: #sub1useful
  [Opciones de redundancia de almacenamiento de Microsoft Azure y almacenamiento con redundancia geográfica con acceso de lectura]: http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx
  [Precios de Azure]: http://azure.microsoft.com/es-es/pricing/overview/
  [destinos de escalabilidad de ancho de banda]: #sub1bandwidth
  [Práctica recomendada para el diseño de servicios a gran escala en Servicios en la nube de Azure]: http://msdn.microsoft.com/es-es/library/dn197896.aspx
  [Red CDN de Azure]: http://azure.microsoft.com/es-es/services/cdn/
  [Firmas de acceso compartido, parte 1: Descripción del modelo de firmas de acceso compartido]: http://azure.microsoft.com/es-es/documentation/articles/storage-dotnet-shared-access-signature-part-1/
  [Compatibilidad con Uso compartido de recursos entre orígenes (CORS) para los Servicios de almacenamiento de Azure]: http://msdn.microsoft.com/library/azure/dn535601.aspx
  [Establecer y recuperar propiedades y metadatos]: http://msdn.microsoft.com/es-es/library/hh225342.aspx
  [Actualización condicional de una copia local de un blob]: http://msdn.microsoft.com/es-es/library/azure/dd179371.aspx
  [Servicios web: conexiones simultáneas]: http://blogs.msdn.com/b/darrenj/archive/2005/03/07/386655.aspx
  [Método ThreadPool.SetMinThreads]: http://msdn.microsoft.com/es-es/library/system.threading.threadpool.setminthreads(v=vs.110).aspx
  [Información general de las mejoras de rendimiento en .NET 4.5]: http://msdn.microsoft.com/es-es/magazine/hh882452.aspx
  [Códigos de estado y de error]: http://msdn.microsoft.com/es-es/library/azure/dd179382.aspx
  [todos los servicios]: #allservices
  [Copy Blob]: http://msdn.microsoft.com/es-es/library/dd894037.aspx
  [aquí]: http://azure.microsoft.com/es-es/documentation/articles/storage-use-azcopy/
  [2]: http://azure.microsoft.com/es-es/documentation/articles/storage-import-export-service/
  [Introducción a los blobs en bloques y a los blobs en páginas]: http://msdn.microsoft.com/es-es/library/azure/ee691964.aspx
  [Tablas de Microsoft Azure: introducción a JSON]: http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx
  [Formato de carga para las operaciones del servicio Tabla]: http://msdn.microsoft.com/es-es/library/azure/dn535600.aspx
  [El algoritmo de Nagle no es idóneo para pequeñas solicitudes]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx
  [Storage Pricing Details]: http://azure.microsoft.com/es-es/pricing/details/storage/
  [Cambio del contenido de un mensaje en cola]: http://azure.microsoft.com/es-es/documentation/articles/storage-dotnet-how-to-use-queues/#change-contents
