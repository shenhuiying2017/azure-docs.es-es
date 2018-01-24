---
title: "Distribución de datos global con Azure Cosmos DB | Microsoft Docs"
description: "Obtenga más información sobre replicación geográfica, conmutación por error y recuperación de datos a escala mundial con bases de datos globales de Azure Cosmos DB, un servicio de base de datos con varios modelos distribuido globalmente."
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: ba5ad0cc-aa1f-4f40-aee9-3364af070725
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: arramac
ms.openlocfilehash: 0be81802996f27a4c063e4e728a3c95ad757bea0
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="how-to-distribute-data-globally-with-azure-cosmos-db"></a>Cómo se distribuyen datos globalmente con Azure Cosmos DB
Azure es ubicuo, se puede encontrar en más de treinta regiones geográficas y se encuentra en continua expansión. Dada su presencia en todo el mundo, una de las funcionalidades únicas que Azure ofrece a los desarrolladores es la capacidad de compilar, implementar y administrar fácilmente aplicaciones distribuidas globalmente. 

[Azure Cosmos DB](../cosmos-db/introduction.md) es un servicio de base de datos con varios modelos y distribución global de Microsoft para aplicaciones críticas. Azure Cosmos DB ofrece una distribución global inmediata, [escalado elástico de rendimiento y almacenamiento](../cosmos-db/partition-data.md) en todo el mundo, latencias inferiores a 10 milisegundos en el percentil 99, [cinco niveles de coherencia bien definidos](consistency-levels.md) y alta disponibilidad garantizada, todo ello respaldado por [acuerdos de nivel de servicio líderes del sector](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [indexa datos automáticamente](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sin que haya que ocuparse de la administración de esquemas ni de índices. Sigue varios modelos y es compatible con los modelos de datos de documento, de clave-valor, de grafo y de columnas. Al ser un servicio surgido en la nube, Azure Cosmos DB se ha diseñado meticulosamente desde cero con servicios multiinquilino y distribución global.

**Una sola colección de Azure Cosmos DB particionada y distribuida en varias regiones de Azure**

![Colección de Azure Cosmos DB particionada y distribuida en tres regiones](./media/distribute-data-globally/global-apps.png)

Como hemos aprendido al crear Azure Cosmos DB, la incorporación de distribución global no es algo que se pueda hacer en el último momento, no se puede "atornillar" sobre un sistema de base de datos de "único sitio". Las funcionalidades que ofrece una base de datos distribuida globalmente superan las de una recuperación ante desastres geográficos (Geo-DR) tradicional que ofrecen las bases de datos de "sitio único". Las bases de datos de sitio único que ofrecen la funcionalidad de Geo-DR son un estricto subconjunto de las bases de datos distribuidas globalmente. 

Con la distribución global inmediata de Azure Cosmos DB, no es preciso que los desarrolladores compilen su propia scaffolding (generación automática de interfaz de usuario) de replicación empleando el patrón Lambda (por ejemplo, la [replicación de DynamoDB en AWS](https://github.com/awslabs/dynamodb-cross-region-library/blob/master/README.md)) en el registro de base de datos o realizando "escrituras dobles" en varias regiones. Estos enfoques no se recomiendan, ya que es imposible garantizar su corrección y proporcionar los contratos de nivel de servicio sólidos. 

En este artículo se ofrece información general sobre las funcionalidades de distribución global de Azure Cosmos DB. También se describe el enfoque exclusivo de Azure Cosmos DB en cuanto a ofrecer acuerdos de nivel de servicio exhaustivos. 

## <a id="EnableGlobalDistribution"></a>Habilitación de una distribución global inmediata
Azure Cosmos DB ofrece las siguientes funcionalidades, que permiten escribir fácilmente aplicaciones a escala mundial. Estas funcionalidades están disponibles a través de las [API de REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/) basadas en el proveedor de recursos de Azure Cosmos DB, así como en Azure Portal.

### <a id="RegionalPresence"></a>Presencia regional ubicua 
La presencia geográfica de Azure crece constantemente y aumenta el número de [regiones nuevas](https://azure.microsoft.com/regions/) en línea. Azure Cosmos DB está disponible de manera predeterminada en todas las nuevas regiones de Azure. Esto permite asociar una región geográfica a una cuenta de base de datos de Azure Cosmos DB en cuanto Azure abra la nueva región para la empresa.

### <a id="UnlimitedRegionsPerAccount"></a>Asociación de un número ilimitado de regiones a una cuenta de base de datos de Azure Cosmos DB
Azure Cosmos DB permite asociar cualquier número de regiones de Azure con su cuenta de base de datos de Azure Cosmos DB. Fuera de las restricciones de geovallado (por ejemplo, China o Alemania), no hay limitaciones en el número de regiones que se pueden asociar a una cuenta de base de datos de Azure Cosmos DB. La siguiente ilustración muestra una cuenta de base de datos configurada para abarcar 25 regiones de Azure.  

**Cuenta de base de datos de Azure Cosmos DB de un inquilino que abarca 25 regiones de Azure**

![Cuenta de base de datos de Azure Cosmos DB que abarca 25 regiones de Azure](./media/distribute-data-globally/spanning-regions.png)

### <a id="PolicyBasedGeoFencing"></a>Geovallado basado en directivas
Azure Cosmos DB está diseñada para tener funcionalidades de geovallado basadas en directivas. El geovallado es un componente importante para asegurar las restricciones de cumplimiento y regulación de datos, y puede evitar la asociación de una región específica a una cuenta. Entre los ejemplos de geovallado se incluyen los siguientes (aunque hay más): limitación del ámbito de distribución global a las regiones que estén dentro de una nube soberana (por ejemplo, China y en Alemania) o dentro de un límite impositivo de gobierno (por ejemplo, Australia). Las directivas se controlan mediante los metadatos de la suscripción de Azure.

### <a id="DynamicallyAddRegions"></a>Incorporación y eliminación dinámicas de regiones
Azure Cosmos DB permite agregar (asociar) o quitar (desasociar) regiones en una cuenta de base de datos en cualquier momento (vea la [ilustración anterior](#UnlimitedRegionsPerAccount)). En virtud de la replicación de datos entre particiones en paralelo, Azure Cosmos DB garantiza que cuando una nueva región esté en línea estará disponible en menos de 30 minutos en cualquier lugar del mundo para un máximo de 100 TB. 

### <a id="FailoverPriorities"></a>Prioridades de conmutación por error
Para controlar la secuencia exacta de las conmutaciones por error regionales cuando se produzca una interrupción en varias regiones, Azure Cosmos DB permite asociar la prioridad a diversas regiones asociadas a la cuenta de la base de datos (vea la ilustración siguiente). Azure Cosmos DB garantiza que la secuencia de la conmutación por error automática se produce en el orden de prioridad que se haya especificado. Para obtener más información sobre las conmutaciones por error regionales, vea [Conmutaciones por error regionales automáticas para la continuidad empresarial en Azure Cosmos DB](regional-failover.md).

**Un inquilino de Azure Cosmos DB puede configurar el orden de prioridad de la conmutación por error (panel derecho) para las regiones asociadas a una cuenta de la base de datos**

![Configuración de prioridades de conmutación por error con Azure Cosmos DB](./media/distribute-data-globally/failover-priorities.png)

### <a id="ConsistencyLevels"></a>Varios modelos de coherencia bien definidos para bases de datos replicadas globalmente
Azure Cosmos DB expone [varios niveles de coherencia bien definidos](consistency-levels.md) respaldados por acuerdos de nivel de servicio. Puede elegir un modelo de coherencia concreto (de la lista de opciones disponibles), en función de la carga de trabajo o de los escenarios. 

### <a id="TunableConsistency"></a>Coherencia optimizable para bases de datos replicadas globalmente
Azure Cosmos DB permite invalidar y relajar la opción de coherencia predeterminada en función de cada solicitud mediante programación en tiempo de ejecución. 

### <a id="DynamicallyConfigurableReadWriteRegions"></a>Regiones de escritura y lectura configurables dinámicamente
Azure Cosmos DB permite configurar las regiones (asociadas a la base de datos) como regiones de "lectura", "escritura" o "lectura/escritura". 

### <a id="ElasticallyScaleThroughput"></a>Rendimiento de escalado elástico en regiones de Azure
Para escalar elásticamente las colecciones de Azure Cosmos DB, es preciso aprovisionar el rendimiento mediante programación. El rendimiento se aplica a todas las áreas en que se distribuye la colección.

### <a id="GeoLocalReadsAndWrites"></a>Escrituras y lecturas geolocalizadas
La principal ventaja de una base de datos distribuida globalmente es ofrecer acceso con latencia baja a datos en cualquier lugar del mundo. Azure Cosmos DB ofrece garantía de baja latencia en P99 para realizar diversas operaciones de base de datos. Garantiza que todas las lecturas se enrutan a la región de lectura local más cercana. Para atender una solicitud de lectura, se usa el cuórum más cercano a la región en la que se emite la lectura, y lo mismo se aplica a las escrituras. Las operaciones de escritura solo se reconocen después de que una mayoría de las réplicas hayan confirmado de manera duradera la escritura localmente, pero sin que sea preciso canalizarlas en réplicas remotas para confirmar las escrituras. Dicho de otro modo, el protocolo de replicación de Azure Cosmos DB opera con la asunción de que los cuórums de lectura y escritura son siempre locales en las regiones de lectura y escritura, respectivamente, en las que se emitió la solicitud.

### <a id="ManualFailover"></a>Inicio manual de una conmutación por error regional
Azure Cosmos DB permite desencadenar la conmutación por error de la cuenta de base de datos para validar las propiedades de disponibilidad *de un extremo a otro* de toda la aplicación (más allá de la base de datos). Puesto que se garantizan las propiedades de seguridad y actividad de la detección de errores y la elección de líder, Azure Cosmos DB garantiza *que no habrá pérdida de datos* en las operaciones de conmutación por error manual iniciadas por un inquilino.

### <a id="AutomaticFailover"></a>Conmutación por error automática
Azure Cosmos DB admite la conmutación por error automática en caso de una o varias interrupciones regionales. Durante una conmutación por error regional, Azure Cosmos DB mantiene sus acuerdos de nivel de servicio de latencia de lectura, disponibilidad del tiempo de actividad, coherencia y rendimiento. Azure Cosmos DB ofrece un límite superior con respecto al tiempo que puede tardar en completarse una operación de conmutación por error automática. Esta es la ventana de la potencial pérdida de datos durante la interrupción regional.

### <a id="GranularFailover"></a>Diseñado para diferentes granularidades de conmutación por error
En la actualidad, las funcionalidades de conmutación por error automática y manual se exponen en la granularidad de la cuenta de la base de datos. Tenga en cuenta que, internamente, Azure Cosmos DB está diseñada para ofrecer conmutación por error *automática* con la granularidad más precisa de una base de datos, colección o incluso partición (de una colección que posee un intervalo de claves). 

### <a id="MultiHomingAPIs"></a>API de hospedaje múltiple en Azure Cosmos DB
Azure Cosmos DB le permite interactuar con la base de datos mediante puntos de conexión lógicos (independientes de la región) o físicos (específicos de la región). El uso de puntos de conexión lógicos garantiza que la aplicación puede alojarse de forma transparente en múltiples hosts en caso de conmutación por error. Los últimos, los puntos de conexión físicos, proporcionan un control específico de la aplicación para redirigir las lecturas y escrituras a regiones concretas.

Encontrará información sobre cómo configurar preferencias de lectura para la [API de SQL](../cosmos-db/tutorial-global-distribution-sql-api.md), [Graph API](../cosmos-db/tutorial-global-distribution-graph.md), [Table API](../cosmos-db/tutorial-global-distribution-table.md) y la [API de MongoDB](../cosmos-db/tutorial-global-distribution-mongodb.md) en sus respectivos artículos vinculados.

### <a id="TransparentSchemaMigration"></a>Migración transparente y coherente del índice y esquema de la base de datos 
Azure Cosmos DB es totalmente [independiente del esquema](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). El diseño exclusivo de su motor de base de datos le permite indexar de forma automática y sincrónica todos los datos que ingiere sin necesidad de que el usuario aporte ningún esquema ni índices secundarios. Esto le permite recorrer en iteración la aplicación distribuida globalmente rápidamente, sin tener que preocuparse de la migración del índice y del esquema de la base de datos ni coordinar los lanzamientos de aplicaciones de varias fases de cambios de esquema. Azure Cosmos DB garantiza que los cambios en directivas de indexación que realiza explícitamente el usuario no generan una degradación del rendimiento ni de la disponibilidad.  

### <a id="ComprehensiveSLAs"></a>Acuerdos de Nivel de Servicio completos (algo más que la alta disponibilidad)
Al ser un servicio de base de datos distribuido globalmente, Azure Cosmos DB ofrece un Acuerdo de Nivel de Servicio bien definido de **pérdida de datos**, **disponibilidad**, **latencia en P99**, **rendimiento** y **coherencia** para la base de datos en conjunto, independientemente del número de regiones asociadas a la misma.  

## <a id="LatencyGuarantees"></a>Garantías de latencia
La principal ventaja de un servicio de base de datos distribuido globalmente como Azure Cosmos DB es ofrecer acceso con baja latencia a datos en cualquier lugar del mundo. Azure Cosmos DB ofrece una baja latencia garantizada en P99 para realizar diversas operaciones de base de datos. El protocolo de replicación que Azure Cosmos DB emplea garantiza que las operaciones de la base de datos (idealmente, lecturas y escrituras) siempre se realizan en la región más cercana a la del cliente. El Acuerdo de Nivel de Servicio de latencia de Azure Cosmos DB incluye P99 para lecturas, escrituras indexadas (sincrónicamente) y consultas para diversos tamaños de solicitud y respuesta. Las garantías de latencia de las escrituras incluyen confirmaciones de cuórum de mayoría duradero en el centro de datos local.

### <a id="LatencyAndConsistency"></a>Relación de la latencia con la coherencia 
Para que un servicio distribuido globalmente ofrezca coherencia fuerte en una configuración distribuida globalmente, necesita replicar sincrónicamente las escrituras o realizar lecturas sincrónicas entre regiones (la velocidad de la luz y la confiabilidad de la red de área extensa dictan que la coherencia fuerte genera latencias más altas y disponibilidad reducida de las operaciones de base de datos). Por lo tanto, a fin de garantizarle latencias bajas en el percentil 99 y una disponibilidad del 99,99 % para todas las cuentas de una sola región y todas las cuentas de varias regiones con coherencia moderada, y un 99,999 % de disponibilidad de lectura en todas las cuentas de base de datos de varias regiones, el servicio debe emplear la replicación asincrónica. Esto, a su vez, requiere que el servicio también ofrezca [opciones de coherencia no estrictas y bien definidas](consistency-levels.md): que no lleguen a ser seguras (para ofrecer garantía de disponibilidad y latencia baja), pero más seguras que una coherencia "eventual" (para ofrecer un modelo de programación intuitivo).

Azure Cosmos DB garantiza que no se requiere una operación de lectura para ponerse en contacto con réplicas de varias regiones para ofrecer la garantía de un nivel de coherencia específico. También garantiza que no se bloquea una operación de escritura en el momento en que los datos se replican por todas las regiones (es decir, las operaciones de escritura se replican de manera asincrónica por las regiones). Para las cuentas de base de datos de varias regiones hay disponibles varios niveles de coherencia relajada. 

### <a id="LatencyAndAvailability"></a>Relación de la latencia con la disponibilidad 
La latencia y la disponibilidad son las dos caras de la misma moneda. Hablamos de la latencia de la operación en estado estable y de disponibilidad en caso de errores. Desde la perspectiva de las aplicaciones, una operación de base de datos que se ejecute lentamente no se puede diferencia de una base de datos que no esté disponible. 

Para diferenciar una latencia elevada de una falta de disponibilidad, Azure Cosmos DB ofrece un límite superior absoluto a la latencia de varias operaciones de base de datos. Si una operación de base de datos tarda en completarse más tiempo que el que establece el límite superior, Azure Cosmos DB devuelve un error de tiempo de espera. El Acuerdo de Nivel de Servicio de disponibilidad de Azure Cosmos DB garantiza de que los tiempos de espera se cuentan con respecto a este acuerdo. 

### <a id="LatencyAndThroughput"></a>Relación de la latencia con el rendimiento
Azure Cosmos DB no obliga a elegir entre latencia y rendimiento. Respeta el Acuerdo de Nivel de Servicio de latencia en P99 y de ofrecer el rendimiento que ha aprovisionado. 

## <a id="ConsistencyGuarantees"></a>Garantías de coherencia
Aunque el [modelo de coherencia fuerte](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) es el estándar de oro de la programación, obliga a pagar el precio de una latencia más alta (en estado estable) y de una disponibilidad reducida (en caso de errores). 

Azure Cosmos DB ofrece un modelo de programación bien definido para que justifique la coherencia de los datos replicados. Para que pueda compilar aplicaciones de hosts múltiples, los modelos de coherencia que expone Azure Cosmos DB están diseñados para no depender de la región desde la que se atienden las operaciones de lectura y escritura. 

El Acuerdo de Nivel de Servicio de coherencia de Azure Cosmos DB garantiza que el 100 % de las solicitudes de lectura cumplirán la garantía de coherencia en el nivel de coherencia solicitado por el usuario (el nivel de coherencia predeterminado en la cuenta de la base de datos o el valor reemplazado en la solicitud). Se considera que una solicitud de lectura ha cumplido el Acuerdo de Nivel de Servicio de coherencia si se satisfacen todas las garantías de coherencia asociadas al nivel de coherencia. En la tabla siguiente se muestran las garantías de coherencia que corresponden a los niveles de coherencia específicos que ofrece Azure Cosmos DB.

**Garantías de coherencia asociadas a un nivel determinado de coherencia en Azure Cosmos DB**

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
        <td>Prefijo coherente</td>
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
El [resultado de imposibilidad](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) del [teorema de CAP](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) demuestra que es totalmente imposible que el sistema permanezca disponible y ofrezca coherencia linearizable en caso de errores. El servicio de base de datos debe ser CP o AP. Los sistemas CP renuncian a la disponibilidad en favor de una coherencia linearizable, mientras que los sistemas AP renuncian a la [coherencia linearizable](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) en favor de disponibilidad. Azure Cosmos DB nunca infringe el nivel de coherencia solicitado, lo que hace que formalmente sea un sistema CP. Sin embargo, en la práctica, la coherencia no es una propuesta de todo o nada, ya que hay varios modelos de coherencia bien definidos a lo largo de todo el espectro de coherencia que va de la coherencia linearizable a la eventual. En Azure Cosmos DB, hemos intentado identificar varios de los modelos de coherencia relajada con la capacidad para aplicarlos en el mundo real y un modelo de programación intuitivo. Azure Cosmos DB contempla los inconvenientes de la disponibilidad de coherencia y ofrece [varios niveles de coherencia moderados pero bien definidos](consistency-levels.md) y una disponibilidad del 99,99 % para todas las cuentas de una sola región y todas las cuentas de varias regiones con coherencia moderada, además de un 99,999 % de disponibilidad de lectura en todas las cuentas de base de datos de varias regiones. 

### <a id="ConsistencyAndAvailability"></a>Relación de la coherencia con la latencia
Una variación más exhaustiva de CAP la propuso el profesor Daniel Abadi y se llama [PACELC](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf), que también tiene en cuenta las compensaciones de latencia y coherencia en estado estable. Indica que en estado estable, el sistema de base de datos debe elegir entre coherencia y latencia. Con varios modelos de coherencia relajada (respaldados por cuórums de escritura, lectura local y replicación asincrónica), Azure Cosmos DB garantiza que todas las operaciones de lectura y escritura son locales en las regiones de lectura y escritura, respectivamente.  Esto permite que Azure Cosmos DB ofrezca garantías de baja latencia dentro de la región para los niveles de coherencia.  

### <a id="ConsistencyAndThroughput"></a>Relación de la consistencia con el rendimiento
Dado que la implementación de un modelo de coherencia específico depende de la elección de un [tipo de cuórum](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf), el rendimiento también varía en función de la coherencia elegida. Por ejemplo, en Azure Cosmos DB, el cargo de RU para lecturas con coherencia fuerte es aproximadamente el doble que el de lecturas posiblemente coherentes. En este caso, necesitará aprovisionar el doble de RU en la colección para lograr el mismo rendimiento.
 
**Relación de la capacidad de lectura con un nivel de coherencia específico en Azure Cosmos DB**

![Relación entre coherencia y rendimiento](./media/distribute-data-globally/consistency-and-throughput.png)

## <a id="ThroughputGuarantees"></a>Garantías de rendimiento 
Azure Cosmos DB permite escalar elásticamente el rendimiento (así como el almacenamiento) en diferentes regiones en función de la demanda. 

**Una sola colección particionada de Azure Cosmos DB (en tres particiones) y luego distribuida en tres regiones de Azure**

![Colecciones particionadas y distribuidas de Azure Cosmos DB](../cosmos-db/media/introduction/azure-cosmos-db-global-distribution.png)

Las colecciones de Azure Cosmos DB se distribuyen en dos dimensiones (dentro de una región y luego entre regiones). Este es el procedimiento: 

* En una sola región, una colección de Azure Cosmos DB se escala horizontalmente en cuanto a particiones de recursos. Cada partición del recurso administra un conjunto de claves y tiene coherencia fuerte y tiene alta disponibilidad en virtud de la replicación del estado de la máquina entre un conjunto de réplicas. Azure Cosmos DB es un sistema de recursos regido globalmente donde una partición de recursos es responsable de entregar su cuota de rendimiento del presupuesto de recursos del sistema que se le asignen. El escalado de una colección de Azure Cosmos DB es completamente transparente (Azure Cosmos DB administra las particiones de recursos y las divide y combina según sea necesario). 
* Después, cada una de las particiones de recursos se distribuye en varias regiones. Las particiones de recursos que poseen el mismo conjunto de claves en diversas regiones forman el conjunto de particiones (vea [la ilustración anterior](#ThroughputGuarantees)).  Las particiones de recursos dentro de un conjunto de particiones se coordinan mediante el uso de replicación del estado de la máquina en varias regiones. En función del nivel de coherencia configurado, las particiones de recursos de un conjunto de particiones se configuran dinámicamente mediante diferentes topologías (por ejemplo, estrella, cadena de margarita, árbol, etc.). 

En virtud de una administración de particiones con gran capacidad de respuesta, del equilibrio de carga y de una regulación de recursos estricta, Azure Cosmos DB permite escalar elásticamente el rendimiento entre varias regiones de Azure en una colección de Azure Cosmos DB. El cambio de rendimiento en una colección es una operación de runtime en Azure Cosmos DB (como sucede en cualquier otra operación con bases de datos, Azure Cosmos DB garantiza el límite superior absoluto de la latencia de la solicitud de cambio de rendimiento). Por ejemplo, la siguiente ilustración muestra una colección de un cliente con rendimiento aprovisionado elásticamente (oscila entre un millón y diez millones de solicitudes por segundo en dos regiones) en función de la demanda.
 
**Colección de un cliente con rendimiento aprovisionado elásticamente (entre un millón y diez millones de solicitudes por segundo)**

![Rendimiento aprovisionado elásticamente de Azure Cosmos DB](./media/distribute-data-globally/elastic-throughput.png)

### <a id="ThroughputAndConsistency"></a>Relación del rendimiento con la coherencia 
Lo mismo que [Relación de la coherencia con el rendimiento](#ConsistencyAndThroughput).

### <a id="ThroughputAndAvailability"></a>Relación del rendimiento con la disponibilidad
Azure Cosmos DB sigue manteniendo su disponibilidad cuando se realizan cambios en el rendimiento. Azure Cosmos DB administra de forma transparente las particiones (por ejemplo, las operaciones de división, combinación y clonación) y se asegura de que las operaciones no degraden el rendimiento o la disponibilidad, mientras que la aplicación aumenta o disminuye elásticamente el rendimiento. 

## <a id="AvailabilityGuarantees"></a>Garantías de disponibilidad
Azure Cosmos DB ofrece un Acuerdo de Nivel de Servicio con disponibilidad del 99,99 % para todas las cuentas de una sola región y todas las cuentas de varias regiones con coherencia moderada, y disponibilidad de lectura del 99,999 % para todas las cuentas de base de datos de varias regiones. Como ya se ha descrito, las garantías de disponibilidad de Azure Cosmos DB incluyen un límite superior absoluto de la latencia de todas las operaciones de datos y del plano de control. Las garantías de disponibilidad son sólidas y no cambian con el número de regiones ni con la distancia geográfica entre regiones. Las garantías de disponibilidad se aplican tanto a la conmutación por error manual como a la automática. Azure Cosmos DB ofrece API de hospedaje múltiple transparentes que garantizan que la aplicación puede funcionar en puntos de conexión lógicos y puede enrutar de forma transparente las solicitudes a la nueva región en caso de conmutación por error. Dicho de otro modo, no es preciso volver a implementar la aplicación si se produce una conmutación por error regional y los Acuerdos de Nivel de Servicio se mantienen.

### <a id="AvailabilityAndConsistency"></a>Relación de la disponibilidad con la coherencia, la latencia y el rendimiento
La relación de la disponibilidad con la coherencia, la latencia y el rendimiento se describe en [Relación de la coherencia con la disponibilidad](#ConsistencyAndAvailability), [Relación de la latencia con la disponibilidad](#LatencyAndAvailability) y [Relación del rendimiento con la disponibilidad](#ThroughputAndAvailability). 

## <a id="GuaranteesAgainstDataLoss"></a>Garantías y comportamiento del sistema ante la "pérdida de datos"
En Azure Cosmos DB, la alta disponibilidad de cada partición (de una colección) se logra mediante varias réplicas, que se distribuyen entre 10 y 20 dominios de error como mínimo. Todas las operaciones de escritura las confirma de forma sincrónica y duradera un cuórum de mayoría de las réplicas antes de que se confirmen al cliente. La replicación asincrónica se aplica a la coordinación entre las particiones dispersas por varias regiones. Azure Cosmos DB garantiza que no hay pérdida de datos en las conmutaciones por error manuales iniciadas por los inquilinos. Durante la conmutación por error automática, Azure Cosmos DB garantiza, como parte de su Acuerdo de Nivel de Servicio, un límite superior del intervalo de obsolescencia limitada configurado en la ventana de pérdida de datos.

## <a id="CustomerFacingSLAMetrics"></a>Métricas de Acuerdo de Nivel de Servicio orientadas al cliente
Azure Cosmos DB expone de forma transparente las métricas de rendimiento, latencia, coherencia y disponibilidad. A estas métricas se puede acceder mediante programación y a través de Azure Portal (vea la siguiente ilustración). También se pueden configurar alertas en varios umbrales mediante Azure Application Insights.
 
**Las métricas de coherencia, latencia, rendimiento y disponibilidad están disponibles de forma transparente para todos los inquilinos**

![Métricas del Acuerdo de Nivel de Servicio visibles para clientes de Azure Cosmos DB](./media/distribute-data-globally/customer-slas.png)

## <a id="Next Steps"></a>Pasos siguientes
* Para implementar la replicación global en una cuenta de Azure Cosmos DB mediante Azure Portal, vea [Configuración de la distribución global de Azure Cosmos DB con DocumentDB API](tutorial-global-distribution-sql-api.md).
* Para obtener información sobre cómo implementar arquitecturas con varios maestros con Azure Cosmos DB, vea [Arquitecturas de bases de datos con varios maestros con Azure Cosmos DB](multi-region-writers.md).
* Para más información sobre el funcionamiento de las conmutaciones por error automáticas y manuales de Azure Cosmos DB, vea [Conmutaciones por error regionales de Azure DocumentDB](regional-failover.md).

## <a id="References"></a>Referencias
1. Eric Brewer. [Towards Robust Distributed Systems](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) (Hacia unos sistemas distribuidos sólidos)
2. Eric Brewer. [CAP Twelve Years Later – How the rules have changed](http://informatik.unibas.ch/fileadmin/Lectures/HS2012/CS341/workshops/reportsAndSlides/PresentationKevinUrban.pdf) (CAP doce años después, cómo han cambiado las reglas)
3. Gilbert, Lynch. - [Brewer&amp;#39;s Conjecture and Feasibility of Consistent, Available, Partition Tolerant Web Services](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) (Conjetura de Brewer y viabilidad de servicios web coherentes, disponibles y tolerantes a particiones)
4. Daniel Abadi. [Consistency Tradeoffs in Modern Distributed Database Systems Design](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf) (Equilibrios de coherencia en el diseño de los modernos sistemas de bases de datos distribuidas)
5. Martin Kleppmann. [Please stop calling databases CP or AP](https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html) (Deje de llamar a las bases de datos CP o AP)
6. Peter Bailis et al. [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf) [Obsolescencia limitada probabilística (PBS) para cuórums parciales prácticos].
7. Naor and Wool. [Load, Capacity and Availability in Quorum Systems](http://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) (Carga, capacidad y disponibilidad en sistemas de quórum)
8. Herlihy and Wing. [Lineralizability: A correctness condition for concurrent objects](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) (Linearización: una condición de corrección para objetos concurrentes)
9. [Acuerdo de Nivel de Servicio de Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/)
