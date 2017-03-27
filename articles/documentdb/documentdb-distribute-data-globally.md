---
title: "Distribución de datos global con DocumentDB| Microsoft Docs"
description: "Aprenda acerca de la replicación geográfica, la conmutación por error y la recuperación de datos a escala mundial mediante bases de datos globales de Azure DocumentDB, un servicio de base de datos NoSQL totalmente administrado."
services: documentdb
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: ba5ad0cc-aa1f-4f40-aee9-3364af070725
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 8e1fccf953579beb138d47d1897bf702461fc39a
ms.lasthandoff: 03/15/2017


---
# <a name="documentdb---a-globally-distributed-database-service-on-azure"></a>DocumentDB, un servicio de bases de datos distribuidas globalmente de Azure
Azure es ubicuo, se puede encontrar en más de treinta regiones geográficas y se encuentra en continua expansión. Dada su presencia en todo el mundo, una de las funcionalidades únicas que Azure ofrece a los desarrolladores es la capacidad de compilar, implementar y administrar fácilmente aplicaciones distribuidas globalmente. DocumentDB es un sistema multiinquilino de bases de datos distribuidas globalmente de Microsoft diseñado para que los desarrolladores puedan compilar aplicaciones a escala planetaria. DocumentDB permite escalar de forma elástica tanto el rendimiento como el almacenamiento en varias regiones geográficas. El servicio ofrece una latencia baja garantizada en P99, alta disponibilidad del 99,99 %, rendimiento predecible y [varios modelos de coherencia bien definidos](documentdb-consistency-levels.md) (todo ello con el respaldo de completos contratos de nivel de servicio). En virtud de su [motor de bases de datos optimizadas para la escritura e independientes de esquemas](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), DocumentDB puede indexar automáticamente todos los datos que ingiere y sirve [consultas integradas en el lenguaje JavaScript](documentdb-sql-query.md), [MongoDB](documentdb-protocol-mongodb.md), y [SQL](documentdb-programming.md#javascript-language-integrated-query-api) de forma independiente de la escala. Al ser un servicio en la nube, DocumentDB se ha diseñado meticulosamente desde cero con servicios multiinquilino y distribución global.

**Una sola colección de DocumentDB con particiones y distribuida en tres regiones de Azure**

![Colección de Azure DocumentDB con particiones y distribuida en tres regiones](./media/documentdb-distribute-data-globally/documentdb-global-apps.png)

Como hemos aprendido al crear DocumentDB, la incorporación de la distribución global no es algo que se pueda hacer en el último momento, no se puede "atornillar" sobre un sistema de base de datos de "sitio único". Las funcionalidades que ofrece una base de datos distribuida globalmente superan las de una recuperación ante desastres geográficos (Geo-DR) tradicional que ofrecen las bases de datos de "sitio único". Las bases de datos de sitio único que ofrecen la funcionalidad de Geo-DR son un estricto subconjunto de las bases de datos distribuidas globalmente. 

Con la distribución global llave en mano de DocumentDB, no es preciso que los desarrolladores compilen su propio scaffolding de replicación mediante el empleo del patrón Lambda (por ejemplo, la [replicación de DynamoDB en AWS](https://github.com/awslabs/dynamodb-cross-region-library/blob/master/README.md)) en el registro de base de datos o mediante la realización de "escrituras dobles" en varias regiones. Estos enfoques no se recomiendan, ya que es imposible garantizar su corrección y proporcionar los contratos de nivel de servicio sólidos. 

En este artículo se proporciona información general sobre las funcionalidades de la distribución global de DocumentDB. También se describe el enfoque único de DocumentDB que se adopta con respecto a proporcionar contratos de nivel de servicio sólidos. 

## <a id="EnableGlobalDistribution"></a>Habilitación de una distribución global llave en mano
DocumentDB proporciona las siguientes funcionalidades, que permiten escribir fácilmente aplicaciones a escala planetaria. Estas funcionalidades están disponibles a través de las [API de REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/) basadas en proveedor de recursos de DocumentDB, así como de Azure Portal.

### <a id="RegionalPresence"></a>Presencia regional ubicua 
La presencia geográfica de Azure crece constantemente y aumenta el número de regiones en línea. De manera predeterminada, DocumentDB está disponible en todas las regiones de Azure nuevas. Esto permite asociar una región geográfica a una cuenta de base de datos de DocumentDB en cuanto Azure abra la nueva región para poder realizar negocios.

**De manera predeterminada, DocumentDB está disponible en todas las regiones de Azure**

![DocumentDB está disponible en todas las regiones de Azure](./media/documentdb-distribute-data-globally/azure-regions.png)

### <a id="UnlimitedRegionsPerAccount"></a>Asociación de un número ilimitado de regiones a una cuenta de base de datos de documentos de DocumentDB
DocumentDB permite asociar cualquier número de regiones de Azure con su cuenta de base de datos de DocumentDB. Fuera de las restricciones de geovallado (por ejemplo, China o Alemania), no hay limitaciones en el número de regiones que se pueden asociar a una cuenta de base de datos de DocumentDB. La siguiente ilustración muestra una cuenta de base de datos configurada para abarcar 21 regiones de Azure.  

**Una cuenta de base de datos de DocumentDB un inquilino que abarca 21 regiones de Azure**

![Cuenta de base de datos de DocumentDB un inquilino que abarca 21 regiones de Azure](./media/documentdb-distribute-data-globally/documentdb-spanning-regions.png)

### <a id="PolicyBasedGeoFencing"></a>Geovallado basado en directivas
DocumentDB está diseñado para tener funcionalidades de geovallado basadas en directivas. El geovallado es un componente importante para asegurar las restricciones de cumplimiento y regulación de datos, y puede evitar la asociación de una región específica a una cuenta. Entre los ejemplos de geovallado se incluyen los siguientes (aunque hay más): limitación del ámbito de distribución global a las regiones que estén dentro de una nube soberana (por ejemplo, China y en Alemania) o dentro de un límite impositivo de gobierno (por ejemplo, Australia). Las directivas se controlan mediante los metadatos de la suscripción de Azure.

### <a id="DynamicallyAddRegions"></a>Incorporación y eliminación dinámicas de regiones
DocumentDB permite agregar (asociar) o quitar (desasociar) regiones de una cuenta de base de datos en cualquier momento (vea la [ilustración anterior](#UnlimitedRegionsPerAccount)). En virtud de la replicación de datos entre particiones en paralelo, DocumentDB garantiza que cuando una nueva región esté en línea estará disponible en menos de 30 minutos en cualquier lugar del mundo para un máximo de 100 TB. 

### <a id="FailoverPriorities"></a>Prioridades de conmutación por error
Para controlar la secuencia exacta de las conmutaciones por error regionales cuando se produzca una interrupción en varias regiones, DocumentDB permite asociar la prioridad a diversas regiones asociadas a la cuenta de la base de datos (consulte la siguiente ilustración). DocumentDB garantiza que la secuencia de la conmutación por error automática se produce en el orden de prioridad que especificó.

**Un inquilino de DocumentDB puede configurar el orden de prioridad de la conmutación por error (panel derecho) para las regiones asociadas a una cuenta de la base de datos**

![Configuración de prioridades de conmutación por error con Azure DocumentDB](./media/documentdb-distribute-data-globally/documentdb-failover-priorities.png)

### <a id="OfflineRegions"></a>Desconexión dinámica de una región
DocumentDB permite desconectar la cuenta de la base de datos de una región específica y volver a conectarla posteriormente. Las regiones marcadas como sin conexión no participan activamente en la replicación y no forman parte de la secuencia de conmutación por error. Esto permite congelar la ultima imagen válida conocida de la base de datos en una de las regiones de lectura antes de implementar actualizaciones potencialmente peligrosos en la aplicación.

### <a id="ConsistencyLevels"></a>Varios modelos de coherencia bien definidos para bases de datos replicadas globalmente
DocumentDB expone [varios niveles de coherencia bien definidos](documentdb-consistency-levels.md) respaldados por Acuerdos de Nivel de Servicio. Puede elegir un modelo de coherencia concreto (de la lista de opciones disponibles), en función de la carga de trabajo o de los escenarios. 

### <a id="TunableConsistency"></a>Coherencia optimizable para bases de datos replicadas globalmente
DocumentDB permite invalidar y ser menos exigentes con la opción de coherencia predeterminada en función de cada solicitud mediante programación en tiempo de ejecución. 

### <a id="DynamicallyConfigurableReadWriteRegions"></a>Regiones de escritura y lectura configurables dinámicamente
DocumentDB permite configurar las regiones (asociadas a la base de datos) para "lectura", "escritura" o "lectura/escritura". 

### <a id="ElasticallyScaleThroughput"></a>Rendimiento de escalado elástico en regiones de Azure
Para escalar elásticamente las colecciones de DocumentDB, es preciso aprovisionar el rendimiento mediante programación. El rendimiento se aplica a todas las áreas en que se distribuye la colección.

### <a id="GeoLocalReadsAndWrites"></a>Escrituras y lecturas geolocalizadas
La principal ventaja de una base de datos distribuida globalmente es ofrecer acceso con latencia baja a datos en cualquier lugar del mundo. DocumentDB ofrece garantía de baja latencia en P99 para realizar diversas operaciones de base de datos. Garantiza que todas las lecturas se enrutan a la región de lectura local más cercana. Para atender una solicitud de lectura, se usa el cuórum más cercano a la región en la que se emite la lectura, y lo mismo se aplica a las escrituras. Las operaciones de escritura solo se reconocen después de que una mayoría de las réplicas hayan confirmado de manera duradera la escritura localmente, pero sin que sea preciso canalizarlas en réplicas remotas para confirmar las escrituras. O dicho de otro modo, el protocolo de replicación de DocumentDB opera con la asunción de que los cuórums de lectura y escritura son siempre locales en las regiones de lectura y escritura, respectivamente, en las que se emitió la solicitud.

### <a id="ManualFailover"></a>Inicio manual de una conmutación por error regional
DocumentDB permite desencadenar la conmutación por error de la cuenta de la base de datos para validar las propiedades de disponibilidad *de un extremo a otro* de toda la aplicación (más allá de la base de datos). Puesto que se garantizan las propiedades de seguridad y vida de la detección de errores y la elección de líder, DocumentDB garantiza *que no habrá pérdida de datos* en las operaciones de conmutación por error manual iniciadas por un inquilino.

### <a id="AutomaticFailover"></a>Conmutación por error automática
DocumentDB admite la conmutación por error automática en caso de una o varias interrupciones regionales. Durante una conmutación por error regional, DocumentDB mantiene sus Acuerdos de Nivel de Servicio de latencia de lectura, disponibilidad del tiempo activo, coherencia y rendimiento. DocumentDB proporciona un límite superior con respecto al tiempo que puede tardar en completarse una operación de conmutación por error automática. Esta es la ventana de la potencial pérdida de datos durante la interrupción regional.

### <a id="GranularFailover"></a>Diseñado para diferentes granularidades de conmutación por error
En la actualidad, las funcionalidades de conmutación por error automática y manual se exponen en la granularidad de la cuenta de la base de datos. Tenga en cuenta que, internamente, DocumentDB está diseñado para ofrecer conmutación por error *automática* con una granularidad más fina de una base de datos, colección o incluso partición (de una colección que posee un intervalo de claves). 

### <a id="MultiHomingAPIs"></a>API de hospedaje múltiple en DocumentDB
DocumentDB le permite interactuar con la base de datos mediante puntos de conexión lógicos (independientes de la región) o físicos (específicos de la región). El uso de puntos de conexión lógicos garantiza que la aplicación puede alojarse de forma transparente en múltiples hosts en caso de conmutación por error. Los últimos, los puntos de conexión físicos, proporcionan un control específico de la aplicación para redirigir las lecturas y escrituras a regiones concretas.

### <a id="ReadPreferencesAPIforMongoDB"></a> Preferencias de lectura configurables de la API de MongoDB
API de MongoDB permite especificar preferencias de lectura de la colección para una base de datos distribuida globalmente. Para lecturas de poca latencia y alta disponibilidad global, se recomienda establecer las preferencias de lectura de la colección en *más cercano*. Una preferencia de lectura de *más cercano* está configurada para leer desde la región más cercana.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Nearest));
```

Para las aplicaciones con una región de lectura/escritura principal y una secundaria para escenarios de recuperación ante desastres, se recomienda establecer preferencias de lectura de la colección en *secundaria preferida*. Una preferencia de lectura de *secundaria preferida* está configurado para leer desde la región secundaria cuando la región principal no está disponible.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.SecondaryPreferred));
```

Por último, si quiere hacerlo manualmente, especifique las áreas de lectura. Puede establecer la etiqueta de región dentro de sus preferencias de lectura.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
var tag = new Tag("region", "Southeast Asia");
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Secondary, new[] { new TagSet(new[] { tag }) }));
```

### <a id="TransparentSchemaMigration"></a>Migración transparente y coherente del índice y esquema de la base de datos 
DocumentDB es totalmente [independiente del esquema](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). El diseño exclusivo de su motor de base de datos le permite indexar de forma automática y sincrónica todos los datos que ingiere sin necesidad de que el usuario aporte ningún esquema ni índices secundarios. Esto le permite recorrer en iteración la aplicación distribuida globalmente rápidamente, sin tener que preocuparse de la migración del índice y del esquema de la base de datos ni coordinar los lanzamientos de aplicaciones de varias fases de cambios de esquema. DocumentDB garantiza que los cambios en directivas de indexación que realiza explícitamente el usuario no generan una degradación del rendimiento ni de la disponibilidad.  

### <a id="ComprehensiveSLAs"></a>Acuerdos de Nivel de Servicio completos (algo más que la alta disponibilidad)
Al ser un servicio de base de datos distribuido globalmente, DocumentDB ofrece un Acuerdo de Nivel de Servicio bien definido de **pérdida de datos**, **disponibilidad**, **latencia en P99**, **rendimiento** y **coherencia** para la base de datos en conjunto, independientemente del número de regiones asociadas a la base de datos.  

## <a id="LatencyGuarantees"></a>Garantías de latencia
La principal ventaja de un servicio de base de datos distribuido globalmente como DocumentDB es ofrecer acceso con latencia baja a datos en cualquier lugar del mundo. DocumentDB ofrece una baja latencia garantizada en P99 para realizar diversas operaciones de base de datos. El protocolo de replicación que DocumentDB emplea garantiza que las operaciones de la base de datos (idealmente, lecturas y escrituras) siempre se realicen en la región más cercana a la del cliente. El Acuerdo de Nivel de Servicio de latencia de DocumentDB incluye P99 para lecturas, escrituras indexadas (sincrónicamente) y consultas para diversos tamaños de solicitud y respuesta. Las garantías de latencia de las escrituras incluyen confirmaciones de cuórum de mayoría duradero en el centro de datos local.

### <a id="LatencyAndConsistency"></a>Relación de la latencia con la coherencia 
Para que un servicio distribuido globalmente ofrezca coherencia alta en una configuración distribuida globalmente, necesita replicar sincrónicamente las escrituras o realizar lecturas sincrónicas entre regiones (la velocidad de la luz y la confiabilidad de la red de área extensa dictan que la coherencia alta genera latencias altas y la baja disponibilidad de las operaciones de base de datos). Por consiguiente, para ofrecer bajas latencias garantizadas en P99 y una disponibilidad del 99,99 %, el servicio debe emplear replicación asincrónica. Esto, a su vez, requiere que el servicio también ofrezca [opciones de coherencia no estrictas y bien definidas](documentdb-consistency-levels.md): que no lleguen a ser seguras (para ofrecer garantía de disponibilidad y latencia baja), pero más seguras que una coherencia "eventual" (para ofrecer un modelo de programación intuitivo).

DocumentDB garantiza que no se requiere una operación de lectura para ponerse en contacto con réplicas de varias regiones para ofrecer la garantía de un nivel de coherencia específico. También garantiza que no se bloquea una operación de escritura en el momento en que los datos se replican por todas las regiones (es decir, las operaciones de escritura se replican de manera asincrónica por las regiones). Para las cuentas de base de datos de varias regiones hay disponibles varios niveles de coherencia relajada. 

### <a id="LatencyAndAvailability"></a>Relación de la latencia con la disponibilidad 
La latencia y la disponibilidad son las dos caras de la misma moneda. Hablamos de la latencia de la operación en estado estable y de disponibilidad en caso de errores. Desde la perspectiva de las aplicaciones, una operación de base de datos que se ejecute lentamente no se puede diferencia de una base de datos que no esté disponible. 

Para diferenciar una latencia elevada de una falta de disponibilidad, DocumentDB proporciona un límite superior absoluto a la latencia de varias operaciones de base de datos. Si una operación de base de datos tarda en completarse más tiempo que el que establece el límite superior, DocumentDB devuelve un error de tiempo de espera. El Acuerdo de Nivel de Servicio de disponibilidad de DocumentDB garantiza de que los tiempos de espera se cuentan con respecto a él. 

### <a id="LatencyAndThroughput"></a>Relación de la latencia con el rendimiento
DocumentDB no obliga a elegir entre latencia y rendimiento. Respeta el Acuerdo de Nivel de Servicio de latencia en P99 y de ofrecer el rendimiento que ha aprovisionado. 

## <a id="ConsistencyGuarantees"></a>Garantías de coherencia
Aunque el [modelo de coherencia alta](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) es el estándar de oro de la programación, obliga a pagar el precio de una latencia alta (en estado estable) de pérdida de disponibilidad (en caso de errores). 

DocumentDB ofrece un modelo de programación bien definido para razonar acerca de la coherencia de los datos replicados. Para que pueda compilar aplicaciones de host múltiple, los modelos de coherencia que expone DocumentDB están diseñados para no depender de la región desde la que se atienden las operaciones de lectura y escritura. 

El Acuerdo de Nivel de Servicio de coherencia de DocumentDB garantiza que el 100 % de las solicitudes de lectura cumplirán la garantía de coherencia en el nivel de coherencia solicitado por el usuario (el nivel de coherencia predeterminado en la cuenta de la base de datos o el valor reemplazado en la solicitud). Se considera que una solicitud de lectura ha cumplido el Acuerdo de Nivel de Servicio de coherencia si se satisfacen todas las garantías de coherencia asociadas al nivel de coherencia. En la tabla siguiente se muestran las garantías de coherencia que corresponden a los niveles de coherencia específicos que ofrece DocumentDB.

**Garantías de coherencia asociadas a un nivel determinado de coherencia en DocumentDB**

<table>
    <tr>
        <td><strong>Nivel de coherencia</strong></td>
        <td><strong>Características de coherencia</strong></td>
        <td><strong>Acuerdo de Nivel de Servicio</strong></td>
    </tr>
    <tr>
        <td rowspan="3">Sesión</td>
        <td>Leer su propia escritura</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Lectura monotónica</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Prefijo coherente</td>
        <td>100%</td>
    </tr>
    <tr>
        <td rowspan="3">Uso vinculado</td>
        <td>Lectura monotónica (dentro de una región)</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Prefijo coherente</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Límite de obsolescencia &lt; K, T</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Ocasional</td>
        <td>Prefijo coherente</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Alta</td>
        <td>Linearizable</td>
        <td>100%</td>
    </tr>
</table>

### <a id="ConsistencyAndAvailability"></a>Relación de la coherencia con la disponibilidad
El [resultado de imposibilidad](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) del [teorema de CAP](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) demuestra que es totalmente imposible que el sistema permanezca disponible y ofrezca coherencia linearizable en caso de errores. El servicio de base de datos debe ser CP o AP. Los sistemas CP renuncian a la disponibilidad en favor de una coherencia linearizable, mientras que los sistemas AP renuncian a la [coherencia linearizable](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) en favor de disponibilidad. DocumentDB nunca infringe el nivel de coherencia solicitado, lo que hace que formalmente sea un sistema CP. Sin embargo, en la práctica, la coherencia no es una propuesta de todo o nada, ya que hay varios modelos de coherencia bien definidos a lo largo de todo el espectro de coherencia que va de la coherencia linearizable a la eventual. En DocumentDB, hemos intentado identificar varios de los modelos de coherencia relajada con la capacidad para aplicarlos en el mundo real y un modelo de programación intuitivo. DocumentDB ofrece una solución intermedia de coherencia-disponibilidad al ofrecer un Acuerdo de Nivel de Servicio de un 99,99 % de disponibilidad, junto con [varios niveles de coherencia relajada, pero bien definida](documentdb-consistency-levels.md). 

### <a id="ConsistencyAndAvailability"></a>Relación de la coherencia con la latencia
Una variación más exhaustiva de CAP la propuso el profesor Daniel Abadi y se llama [PACELC](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf), que también tiene en cuenta las compensaciones de latencia y coherencia en estado estable. Indica que en estado estable, el sistema de base de datos debe elegir entre coherencia y latencia. Con varios modelos de coherencia relajada (respaldados por cuórums de escritura, lectura local y replicación asincrónica), DocumentDB garantiza que todas las operaciones de lectura y escritura son locales en las regiones de lectura y escritura, respectivamente.  Esto permite que DocumentDB ofrezca garantías de latencia baja dentro de la región para los niveles de coherencia.  

### <a id="ConsistencyAndThroughput"></a>Relación de la consistencia con el rendimiento
Dado que la implementación de un modelo de coherencia específico depende de la elección de un [tipo de cuórum](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf), el rendimiento también varía en función de la coherencia elegida. Por ejemplo, en DocumentDB, el rendimiento con lecturas con coherencia fuerte es aproximadamente la mitad que el de lecturas eventualmente coherentes. 
 
**Relación de la capacidad de lectura de un nivel de coherencia específico en DocumentDB**

![Relación entre coherencia y rendimiento](./media/documentdb-distribute-data-globally/consistency-and-throughput.png)

## <a id="ThroughputGuarantees"></a>Garantías de rendimiento 
DocumentDB permite escalar el rendimiento (así como el almacenamiento) elásticamente en diferentes regiones en función de la demanda. 

**Una colección de DocumentDB individual con particiones (en tres particiones) y luego distribuida en tres regiones de Azure**

![Colecciones distribuidas y con particiones de Azure DocumentDB](./media/documentdb-distribute-data-globally/documentdb-throughput.png)

Las colecciones de DocumentDB se distribuyen en dos dimensiones (tanto dentro de una región como entre regiones). Este es el procedimiento: 

* En una región individual, una colección de DocumentDB se escala horizontalmente en términos de particiones de recursos. Cada partición del recurso administra un conjunto de claves y tiene coherencia fuerte y tiene alta disponibilidad en virtud de la replicación del estado de la máquina entre un conjunto de réplicas. DocumentDB es un sistema de recursos regido globalmente donde una partición del recurso es responsable de entregar su cuota de rendimiento para el presupuesto de los recursos del sistema asignados a él. El escalado de una colección de DocumentDB es completamente transparente (DocumentDB administra las particiones de recursos y la divide y combinan según sea necesario). 
* Después, cada una de las particiones de recursos se distribuye en varias regiones. Las particiones de recursos que poseen el mismo conjunto de claves en diversas regiones forman el conjunto de particiones (vea [la ilustración anterior](#ThroughputGuarantees)).  Las particiones de recursos dentro de un conjunto de particiones se coordinan mediante el uso de replicación del estado de la máquina en varias regiones. En función del nivel de coherencia configurado, las particiones de recursos de un conjunto de particiones se configuran dinámicamente mediante diferentes topologías (por ejemplo, estrella, cadena de margarita, árbol, etc.). 

En virtud de una administración de particiones con gran capacidad de respuesta, del equilibrio de carga y de una regulación de recursos estricta, DocumentDB permite escalar elásticamente el rendimiento entre varias regiones de Azure en una colección de DocumentDB. El cambio de rendimiento en una colección es una operación de runtime en DocumentDB (como sucede en cualquier otra operación con bases de datos DocumentDB garantiza el límite superior absoluto de la latencia de la solicitud de cambio de rendimiento). Por ejemplo, la siguiente ilustración muestra una colección de un cliente con rendimiento aprovisionado elásticamente (oscila entre un millón y diez millones de solicitudes por segundo en dos regiones) en función de la demanda.
 
**Colección de un cliente con rendimiento aprovisionado elásticamente (entre un millón y diez millones de solicitudes por segundo)**

![Rendimiento de Azure DocumentDB aprovisionado elásticamente](./media/documentdb-distribute-data-globally/documentdb-elastic-throughput.png)

### <a id="ThroughputAndConsistency"></a>Relación del rendimiento con la coherencia 
Lo mismo que [Relación de la coherencia con el rendimiento](#ConsistencyAndThroughput).

### <a id="ThroughputAndAvailability"></a>Relación del rendimiento con la disponibilidad
DocumentDB no pierde su disponibilidad cuando se realizan cambios en el rendimiento. DocumentDB administra de forma transparente las particiones (por ejemplo, las operaciones de división, combinación y clonación) y se asegura de que las operaciones no degradan el rendimiento o la disponibilidad, mientras que la aplicación aumenta o disminuye el rendimiento de forma elástica. 

## <a id="AvailabilityGuarantees"></a>Garantías de disponibilidad
DocumentDB ofrece un Acuerdo de Nivel de Servicio de una disponibilidad del 99,99 % del tiempo de actividad en cada una de las operaciones de datos y del plano de control. Como ya se ha descrito, las garantías de disponibilidad de DocumentDB incluyen un límite superior absoluto de la latencia de todas las operaciones de datos y del plano de control. Las garantías de disponibilidad son sólidas y no cambian con el número de regiones ni con la distancia geográfica entre regiones. Las garantías de disponibilidad se aplican tanto a la conmutación por error manual como a la automática. DocumentDB ofrece API de hospedaje múltiple transparentes que garantizan que la aplicación puede funcionar en puntos de conexión lógicos y puede enrutar de forma transparente las solicitudes a la nueva región en el caso de conmutación por error. Dicho de otro modo, no es preciso volver a implementar la aplicación si se produce una conmutación por error regional y los Acuerdos de Nivel de Servicio se mantienen.

### <a id="AvailabilityAndConsistency"></a>Relación de la disponibilidad con la coherencia, la latencia y el rendimiento
La relación de la disponibilidad con la coherencia, la latencia y el rendimiento se describe en [Relación de la coherencia con la disponibilidad](#ConsistencyAndAvailability), [Relación de la latencia con la disponibilidad](#LatencyAndAvailability) y [Relación del rendimiento con la disponibilidad](#ThroughputAndAvailability). 

## <a id="GuaranteesAgainstDataLoss"></a>Garantías y comportamiento del sistema ante la "pérdida de datos"
En DocumentDB, la alta disponibilidad de cada partición (de una colección) se logra mediante varias réplicas, que están dispersas por un mínimo de 10 a 20 dominios de error. Todas las operaciones de escritura las confirma de forma sincrónica y duradera un cuórum de mayoría de las réplicas antes de que se confirmen al cliente. La replicación asincrónica se aplica a la coordinación entre las particiones dispersas por varias regiones. DocumentDB garantiza que no hay pérdida de datos en las conmutaciones por error manuales iniciadas por los inquilinos. Durante la conmutación por error automática, DocumentDB garantiza, como parte de su Acuerdo de Nivel de Servicio, un límite superior del intervalo de obsolescencia limitada configurado en la ventana de pérdida de datos.

## <a id="CustomerFacingSLAMetrics"></a>Métricas de Acuerdo de Nivel de Servicio orientadas al cliente
DocumentDB expone de forma transparente las métricas de rendimiento, latencia, coherencia y disponibilidad. A estas métricas se puede acceder mediante programación y a través de Azure Portal (vea la siguiente ilustración). También se pueden configurar alertas en varios umbrales mediante Azure Application Insights.
 
**Las métricas de coherencia, latencia, rendimiento y disponibilidad están disponibles de forma transparente para todos los inquilinos**

![Métricas del Acuerdo de Nivel de Servicio visibles para clientes Azure DocumentDB](./media/documentdb-distribute-data-globally/documentdb-customer-slas.png)

## <a id="Next Steps"></a>Pasos siguientes
* Para implementar la replicación global en una cuenta de DocumentDB mediante Azure Portal, consulte [Replicación global de bases de datos de DocumentDB mediante Azure Portal](documentdb-portal-global-replication.md).
* Para obtener información acerca de cómo implementar arquitecturas de varios maestros con DocumentDB, consulte ///[Multi-master database architectures with Azure DocumentDB](documentdb-multi-region-writers.md) (Arquitecturas de base de datos de varios maestros con Azure DocumentDB).
* Para más información acerca del funcionamiento de las conmutaciones por error automáticas y manuales en DocumentDB, consulte ///[Regional Failovers in Azure DocumentDB](documentdb-regional-failovers.md) (Conmutaciones por error regionales en Azure DocumentDB).

## <a id="References"></a>Referencias
1. Eric Brewer. [Towards Robust Distributed Systems](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) (Hacia unos sistemas distribuidos sólidos)
2. Eric Brewer. [CAP Twelve Years Later – How the rules have changed](http://informatik.unibas.ch/fileadmin/Lectures/HS2012/CS341/workshops/reportsAndSlides/PresentationKevinUrban.pdf) (CAP doce años después, cómo han cambiado las reglas)
3. Gilbert, Lynch. - [Brewer&#39;s Conjecture and Feasibility of Consistent, Available, Partition Tolerant Web Services](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) (Conjetura de Brewer y viabilidad de servicios web coherentes, disponibles y tolerantes a particiones)
4. Daniel Abadi. [Consistency Tradeoffs in Modern Distributed Database Systems Design](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf) (Equilibrios de coherencia en el diseño de los modernos sistemas de bases de datos distribuidas)
5. Martin Kleppmann. [Please stop calling databases CP or AP](https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html) (Deje de llamar a las bases de datos CP o AP)
6. Peter Bailis et al. [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf) [Obsolescencia limitada probabilística (PBS) para cuórums parciales prácticos].
7. Naor and Wool. [Load, Capacity and Availability in Quorum Systems](http://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) (Carga, capacidad y disponibilidad en sistemas de quórum)
8. Herlihy and Wing. [Lineralizability: A correctness condition for concurrent objects](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) (Linearización: una condición de corrección para objetos concurrentes)
9. Acuerdo de Nivel de Servicio de Azure DocumentDB (última actualización, diciembre de 2016)

