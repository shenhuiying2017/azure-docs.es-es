---
title: Distribución de datos global con Azure Cosmos DB | Microsoft Docs
description: Obtenga más información sobre replicación geográfica, conmutación por error y recuperación de datos a escala mundial con bases de datos globales de Azure Cosmos DB, un servicio de base de datos con varios modelos distribuido globalmente.
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: ba5ad0cc-aa1f-4f40-aee9-3364af070725
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2018
ms.author: sngun
ms.openlocfilehash: 967e7458d43dccd4601440138b7445eb876b9f01
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-distribute-data-globally-with-azure-cosmos-db"></a>Cómo se distribuyen datos globalmente con Azure Cosmos DB
Azure es ubicuo, se puede encontrar en más de cincuenta regiones geográficas y se halla en continua expansión. Dada su presencia en todo el mundo, una de las funcionalidades únicas que Azure ofrece a los desarrolladores es la capacidad de compilar, implementar y administrar fácilmente aplicaciones distribuidas globalmente. 

[Azure Cosmos DB](../cosmos-db/introduction.md) es un servicio de base de datos con varios modelos y distribución global de Microsoft para aplicaciones críticas. Azure Cosmos DB ofrece una distribución global inmediata, [escalado elástico de rendimiento y almacenamiento](../cosmos-db/partition-data.md) en todo el mundo, latencias inferiores a 10 milisegundos en el percentil 99, [cinco modelos de coherencia bien definidos](consistency-levels.md) y alta disponibilidad garantizada, todo ello respaldado por [exhaustivos Acuerdos de Nivel de Servicio líderes del sector](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [indexa datos automáticamente todos sus datos](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sin que haya que ocuparse de la administración de esquemas ni de índices. Es un servicio multimodelo que admite modelos de datos de documentos, pares clave-valor, grafos y en columnas. Al ser un servicio surgido de forma nativa en la nube, Azure Cosmos DB se ha diseñado meticulosamente desde cero con servicios multiinquilino y distribución global.


![Colección de Azure Cosmos DB particionada y distribuida en tres regiones](./media/distribute-data-globally/global-apps.png)

**Un solo contenedor de Azure Cosmos DB particionado y distribuido en varias regiones de Azure**

Como hemos aprendido al crear Azure Cosmos DB, la incorporación de distribución global no es algo que se pueda hacer en el último momento. No se puede "atornillar" sobre un sistema de base de datos de "único sitio". Las funcionalidades que ofrece una base de datos distribuida globalmente superan las de una recuperación ante desastres geográficos (Geo-DR) tradicional que ofrecen las bases de datos de "sitio único". Las bases de datos de sitio único que ofrecen la funcionalidad de Geo-DR son un estricto subconjunto de las bases de datos distribuidas globalmente. 

Con la distribución global inmediata de Azure Cosmos DB, no es preciso que los desarrolladores compilen su propia scaffolding (generación automática de interfaz de usuario) de replicación empleando el patrón Lambda (por ejemplo, la [replicación de DynamoDB en AWS](https://github.com/awslabs/dynamodb-cross-region-library/blob/master/README.md)) en el registro de base de datos o realizando "escrituras dobles" en varias regiones. Estos enfoques *no* se recomiendan, ya que es imposible garantizar su corrección y proporcionar Acuerdos de Nivel de Servicio sólidos. 

En este artículo se ofrece información general sobre las funcionalidades de distribución global de Azure Cosmos DB. También se describe el enfoque exclusivo de Azure Cosmos DB en cuanto a ofrecer acuerdos de nivel de servicio exhaustivos. 

## <a id="EnableGlobalDistribution"></a>Habilitación de una distribución global inmediata
Azure Cosmos DB ofrece las siguientes funcionalidades, que permiten escribir fácilmente aplicaciones distribuidas a escala mundial. Estas funcionalidades están disponibles a través de las [API de REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/) basadas en el proveedor de recursos de Azure Cosmos DB, así como en Azure Portal.

Vea el siguiente vídeo para ver en acción la característica de distribución global inmediata en Azure Cosmos DB.

> [!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]
>

### <a id="RegionalPresence"></a>Presencia regional ubicua 
La presencia geográfica de Azure crece constantemente y aumenta el número de [regiones nuevas](https://azure.microsoft.com/regions/) en línea. Azure Cosmos DB está clasificado como *servicio fundamental* en Azure y está disponible en todas las nuevas regiones de Azure de forma predeterminada. Esto permite asociar una región geográfica a una cuenta de base de datos de Azure Cosmos DB en cuanto Azure abra la nueva región para la empresa.

### <a id="UnlimitedRegionsPerAccount"></a>Asociación de un número ilimitado de regiones a una cuenta de base de datos de Azure Cosmos DB
Azure Cosmos DB permite asociar cualquier número de regiones de Azure con su cuenta de base de datos de Azure Cosmos DB. Fuera de las restricciones de geovallado (por ejemplo, China o Alemania), no hay limitaciones en el número de regiones que se pueden asociar a una cuenta de base de datos de Azure Cosmos DB. La siguiente ilustración muestra una cuenta de base de datos configurada para abarcar 25 regiones de Azure.  

![Cuenta de base de datos de Azure Cosmos DB que abarca 25 regiones de Azure](./media/distribute-data-globally/spanning-regions.png)

**Cuenta de base de datos de Azure Cosmos DB de un inquilino que abarca 25 regiones de Azure**


### <a id="PolicyBasedGeoFencing"></a>Geovallado basado en directivas
Azure Cosmos DB está diseñado para admitir el geovallado basado en directivas. El geovallado es un componente importante para asegurar las restricciones de cumplimiento y regulación de datos, y puede evitar la asociación de una región específica a una cuenta. Entre los ejemplos de geovallado se incluyen los siguientes (aunque hay más): limitación del ámbito de distribución global a las regiones que estén dentro de una nube soberana (por ejemplo, China y Alemania) o dentro de un límite impositivo de gobierno (por ejemplo, Australia). Las directivas se controlan mediante los metadatos de la suscripción de Azure.

### <a id="DynamicallyAddRegions"></a>Incorporación y eliminación dinámicas de regiones
Azure Cosmos DB permite agregar (asociar) o quitar (desasociar) regiones en una cuenta de base de datos en cualquier momento (vea la [ilustración anterior](#UnlimitedRegionsPerAccount)). Gracias a la replicación paralela de los datos entre particiones, Azure Cosmos DB garantiza que cuando se agregue una nueva región, esta estará disponible para las operaciones en un plazo de 30 minutos en cualquier lugar del mundo (suponiendo que los datos tengan 100 TB o menos). 

### <a id="FailoverPriorities"></a>Prioridades de conmutación por error
Para controlar la secuencia exacta de las conmutaciones por error regionales en los casos de una interrupción, Azure Cosmos DB permite asociar una *prioridad* a diversas regiones asociadas a la cuenta de la base de datos (vea la ilustración siguiente). Azure Cosmos DB garantiza que la secuencia de la conmutación por error automática se produce en el orden de prioridad que se haya especificado. Para obtener más información sobre las conmutaciones por error regionales, vea [Conmutaciones por error regionales automáticas para la continuidad empresarial en Azure Cosmos DB](regional-failover.md).


![Configuración de prioridades de conmutación por error con Azure Cosmos DB](./media/distribute-data-globally/failover-priorities.png)

**Un inquilino de Azure Cosmos DB puede configurar el orden de prioridad de la conmutación por error (panel derecho) para las regiones asociadas a una cuenta de la base de datos**

### <a id="ConsistencyLevels"></a>Varios modelos de coherencia bien definidos para bases de datos distribuidas globalmente
Azure Cosmos DB admite [varios modelos de coherencia bien definidos, intuitivos y prácticos](consistency-levels.md) respaldados por Acuerdos de Nivel de Servicio. Puede elegir un modelo de coherencia concreto (de la lista de opciones disponibles), en función de la carga de trabajo o de los escenarios. 

### <a id="TunableConsistency"></a>Coherencia optimizable para bases de datos replicadas globalmente
Azure Cosmos DB permite invalidar y moderar la opción de coherencia predeterminada en función de cada solicitud mediante programación en tiempo de ejecución. 

### <a id="DynamicallyConfigurableReadWriteRegions"></a>Regiones de escritura y lectura configurables dinámicamente
Azure Cosmos DB permite configurar las regiones (asociadas a la base de datos) como regiones de "lectura", "escritura" o "lectura/escritura". 

### <a id="ElasticallyScaleThroughput"></a>Rendimiento de escalado elástico en regiones de Azure
Para escalar elásticamente un contenedor de Azure Cosmos DB, es preciso aprovisionar el rendimiento mediante programación. El rendimiento se aplica a todas las regiones en que está distribuido el contenedor de Azure Cosmos DB.

### <a id="GeoLocalReadsAndWrites"></a>Escrituras y lecturas geolocalizadas
La principal ventaja de una base de datos distribuida globalmente es que ofrece acceso con latencia baja a datos en cualquier lugar del mundo. Azure Cosmos DB ofrece lecturas de latencia baja y escribe en el percentil 99 en todo el mundo. Garantiza que todas las lecturas se atiendan desde la región de lectura más cercana (local). Para atender una solicitud de lectura, se usa el cuórum local de la región en la que se emite la lectura. Lo mismo se aplica a las escrituras. Las operaciones de escritura solo se reconocen después de que una mayoría de las réplicas hayan confirmado de manera duradera la escritura localmente, pero sin que sea preciso canalizarlas en réplicas remotas para confirmar las escrituras. Dicho de otro modo, el protocolo de replicación de Azure Cosmos DB opera con la asunción de que los cuórums de lectura y escritura son siempre locales en las región en la que se emitió la solicitud.

### <a id="ManualFailover"></a>Conmutación por error manual
Azure Cosmos DB permite desencadenar la conmutación por error de la cuenta de base de datos para validar las propiedades de disponibilidad *de un extremo a otro* de toda la aplicación (más allá de la base de datos). Puesto que se garantizan las propiedades de seguridad y actividad de la detección de errores y la elección de líder, Azure Cosmos DB garantiza *que no habrá pérdida de datos* en las operaciones de conmutación por error manual iniciadas por un inquilino.

### <a id="AutomaticFailover"></a>Conmutación por error automática
Azure Cosmos DB admite la conmutación por error automática en caso de una o varias interrupciones regionales. Durante una conmutación por error regional, Azure Cosmos DB mantiene sus acuerdos de nivel de servicio de latencia de lectura, disponibilidad del tiempo de actividad, coherencia y rendimiento. Azure Cosmos DB ofrece un límite superior con respecto al tiempo que puede tardar en completarse una operación de conmutación por error automática. Esta es la ventana de la potencial pérdida de datos durante la interrupción regional.

### <a id="GranularFailover"></a>Diseñado para diferentes granularidades de conmutación por error
En la actualidad, las funcionalidades de conmutación por error automática y manual se exponen en la granularidad de la cuenta de la base de datos. Tenga en cuenta que, internamente, el servicio Azure Cosmos DB está diseñado para ofrecer conmutación por error *automática* con la granularidad más precisa de una base de datos, contenedor o incluso partición (de un contenedor que posee un intervalo de claves). 

### <a id="MultiHomingAPIs"></a>Hospedaje múltiple en Azure Cosmos DB
Azure Cosmos DB le permite interactuar con una base de datos mediante puntos de conexión *lógicos* (independientes de la región) o *físicos* (específicos de la región). El uso de puntos de conexión lógicos garantiza que la aplicación puede alojarse de forma transparente en múltiples hosts en caso de conmutación por error. El último, un punto de conexión físico, proporciona un control específico de la aplicación para redirigir las lecturas y escrituras a regiones concretas.

Encontrará información sobre cómo configurar preferencias de lectura para la [API SQL](../cosmos-db/tutorial-global-distribution-sql-api.md), [API Gremlin](../cosmos-db/tutorial-global-distribution-graph.md), [Table API](../cosmos-db/tutorial-global-distribution-table.md) y la [API MongoDB](../cosmos-db/tutorial-global-distribution-mongodb.md) en estos artículos.

### <a id="TransparentSchemaMigration"></a>Migración transparente y coherente del índice y esquema de la base de datos 
Azure Cosmos DB es totalmente [independiente del esquema](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). El diseño exclusivo de su motor de base de datos permite a Azure Cosmos DB indexar de forma automática y sincrónica todos los datos que ingiere sin necesidad de que el usuario aporte ningún esquema ni índices secundarios. Esto le permite recorrer en iteración la aplicación distribuida globalmente rápidamente, sin tener que preocuparse de la migración del índice y del esquema de la base de datos ni coordinar los lanzamientos de aplicaciones de varias fases de cambios de esquema. Azure Cosmos DB garantiza que los cambios en directivas de indexación que realiza explícitamente el usuario no generan una degradación del rendimiento ni de la disponibilidad.  

### <a id="ComprehensiveSLAs"></a>Acuerdos de Nivel de Servicio completos (algo más que la alta disponibilidad)
Al ser un servicio de base de datos distribuido globalmente, Azure Cosmos DB ofrece un Acuerdo de Nivel de Servicio exhaustivo y bien definido para la **disponibilidad**, la **latencia**, el **rendimiento** y la **coherencia** para la base de datos que se ejecuta a nivel global, independientemente del número de regiones asociadas a esta.  

## <a id="LatencyGuarantees"></a>Garantías de latencia
La principal ventaja de un servicio de base de datos distribuido globalmente como Azure Cosmos DB es ofrecer acceso con baja latencia a datos en cualquier lugar del mundo. Azure Cosmos DB ofrece una baja latencia garantizada en el percentil 99 para realizar diversas operaciones de base de datos. El protocolo de replicación que Azure Cosmos DB emplea garantiza que las operaciones de la base de datos (lecturas y escrituras) siempre se realizan en la región más cercana a la del cliente. El Acuerdo de Nivel de Servicio de latencia de Azure Cosmos DB ofrece garantías en el percentil 99 para lecturas y escrituras indexadas (sincrónicamente) y consulta diversos tamaños de solicitud y respuesta. Las garantías de latencia de las escrituras incluyen confirmaciones de cuórum de mayoría duradero en la región local.

### <a id="LatencyAndConsistency"></a>Relación de la latencia con la coherencia 
Para que un servicio distribuido globalmente ofrezca una coherencia segura en una instalación distribuida globalmente, necesita replicar las escrituras de forma sincrónica o realizar lecturas entre regiones de forma sincrónica. La velocidad de la luz y la red de área extensa dictan que esta coherencia segura dará como resultado latencias más altas y una menor disponibilidad de las operaciones de base de datos. Por lo tanto, a fin de garantizarle latencias bajas en el percentil 99 y una disponibilidad del 99,99 % para todas las cuentas de una sola región y todas las cuentas de varias regiones con coherencia moderada, y un 99,999 % de disponibilidad de lectura en todas las cuentas de base de datos de varias regiones, el servicio debe emplear la replicación asincrónica. Esto, a su vez, requiere que el servicio también ofrezca [modelos de coherencia no estrictos y bien definidos](consistency-levels.md): que no lleguen a ser seguros (para ofrecer garantía de disponibilidad y latencia baja), pero más seguras que una coherencia "eventual" (para ofrecer un modelo de programación intuitivo).

Azure Cosmos DB garantiza que no se requiere una operación de lectura para ponerse en contacto con réplicas de varias regiones para ofrecer la garantía de un nivel de coherencia específico. También garantiza que no se bloquea una operación de escritura en el momento en que los datos se replican por todas las regiones (es decir, las operaciones de escritura se replican de manera asincrónica por las regiones). Para las cuentas de base de datos de varias regiones hay disponibles niveles de coherencia segura y moderada. 

### <a id="LatencyAndAvailability"></a>Relación de la latencia con la disponibilidad 
La latencia y la disponibilidad son las dos caras de la misma moneda. Hablamos de la latencia de la operación en estado estable y de disponibilidad en caso de errores y particiones de red. Desde la perspectiva de las aplicaciones, una operación de base de datos que se ejecute lentamente no se puede diferencia de una base de datos que no esté disponible. 

Para diferenciar una latencia elevada de una falta de disponibilidad, Azure Cosmos DB ofrece un límite superior absoluto para la latencia de varias operaciones de base de datos. Si una operación de base de datos tarda en completarse más tiempo que el que establece el límite superior, Azure Cosmos DB devuelve un error de tiempo de espera. El Acuerdo de Nivel de Servicio de disponibilidad de Azure Cosmos DB garantiza de que los tiempos de espera se cuentan con respecto a este acuerdo. 

### <a id="LatencyAndThroughput"></a>Relación de la latencia con el rendimiento
Azure Cosmos DB no obliga a elegir entre latencia y rendimiento. Respeta el Acuerdo de Nivel de Servicio de latencia en el percentil 99 y de ofrecer el rendimiento que ha aprovisionado. 

## <a id="ConsistencyGuarantees"></a>Garantías de coherencia
Aunque el [modelo de coherencia segura](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) es el estándar de oro de la programación de datos, obliga a pagar el precio de una latencia más alta (en estado estable) y de una disponibilidad reducida (en caso de errores). 

Azure Cosmos DB ofrece un modelo de programación bien definido para que justifique la coherencia de los datos replicados. Para que pueda compilar con facilidad aplicaciones distribuidas globalmente compatibles con el hospedaje múltiple, los modelos de coherencia que expone Azure Cosmos DB están diseñados para no depender de la región desde la que se atienden las operaciones de lectura y escritura. 

El Acuerdo de Nivel de Servicio de coherencia de Azure Cosmos DB garantiza que el 100 % de las solicitudes de lectura cumplirán la garantía de coherencia en el modelo de coherencia especificado por el usuario (la cuenta de la base de datos o el nivel de la solicitud). Se considera que una solicitud de lectura ha cumplido el Acuerdo de Nivel de Servicio de coherencia si se satisfacen todas las garantías de coherencia asociadas al nivel de coherencia. En la tabla siguiente se muestran las garantías de coherencia que corresponden a los modelos de coherencia específicos que ofrece Azure Cosmos DB.

<table>
    <tr>
        <td><strong>Modelo de coherencia</strong></td>
        <td><strong>Características de coherencia</strong></td>
        <td><strong>Acuerdo de Nivel de Servicio</strong></td>
    </tr>
        <tr>
        <td>Alta</td>
        <td>Linearizable</td>
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
        <td>Prefijo coherente</td>
        <td>Prefijo coherente</td>
        <td>100%</td>
    </tr>
</table>

**Garantías de coherencia asociadas a un modelo determinado de coherencia en Azure Cosmos DB**


### <a id="ConsistencyAndAvailability"></a>Relación de la coherencia con la disponibilidad
El [resultado de imposibilidad](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) del [teorema de CAP](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) demuestra que es totalmente imposible que un sistema permanezca disponible y ofrezca coherencia linearizable en caso de errores. El servicio de base de datos debe ser CP o AP, donde los sistemas CP renuncian a la disponibilidad en favor de una coherencia linearizable, mientras que los sistemas AP renuncian a la [coherencia linearizable](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) en favor de disponibilidad. Azure Cosmos DB nunca infringe el modelo de coherencia solicitado, lo que hace que formalmente sea un sistema CP. Sin embargo, en la práctica, la coherencia no es una propuesta de todo o nada, ya que hay varios modelos de coherencia bien definidos a lo largo de todo el espectro de coherencia que va de la coherencia linearizable a la eventual. En Azure Cosmos DB, hemos intentado identificar varios de los modelos de coherencia moderada que son aplicables a escenarios de la vida real y responden a un modelo de uso intuitivo. Azure Cosmos DB contempla los inconvenientes de la disponibilidad de coherencia y ofrece [varios modelos de coherencia moderados pero bien definidos](consistency-levels.md), así como una disponibilidad del 99,99 % para todas las cuentas de base de datos de una sola región y un 99,999 % de disponibilidad de lectura y escritura en todas las cuentas de base de datos de varias regiones. 

### <a id="ConsistencyAndAvailability"></a>Relación de la coherencia con la latencia
Una variación más exhaustiva del teorema de CAP se llama [PACELC](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf), que también tiene en cuenta las compensaciones de latencia y coherencia en estado estable. Indica que, en estado estable, el sistema de base de datos debe elegir entre coherencia y latencia. Con varios modelos de coherencia moderada (respaldados por cuórums de escritura, lectura local y replicación asincrónica), Azure Cosmos DB garantiza que todas las operaciones de lectura y escritura son locales en las regiones de lectura y escritura, respectivamente. Esto permite que Azure Cosmos DB ofrezca garantías de baja latencia dentro de la región para los modelos de coherencia especificados.  

### <a id="ConsistencyAndThroughput"></a>Relación de la consistencia con el rendimiento
Dado que la implementación de un modelo de coherencia específico depende de la elección de un [tipo de cuórum](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf), el rendimiento también varía en función del modelo de coherencia elegido. Por ejemplo, en Azure Cosmos DB, el cargo de RU para lecturas con coherencia fuerte es aproximadamente el *doble* que el de lecturas posiblemente coherentes. En este caso, necesitará aprovisionar el doble de RU para lograr el mismo rendimiento.


![Relación entre coherencia y rendimiento](./media/distribute-data-globally/consistency-and-throughput.png)

**Relación de la capacidad de lectura con un modelo de coherencia específico en Azure Cosmos DB**

## <a id="ThroughputGuarantees"></a>Garantías de rendimiento 
Azure Cosmos DB permite escalar elásticamente el rendimiento (así como el almacenamiento) en diferentes regiones en función de las necesidades o la demanda. 

![Colecciones particionadas y distribuidas de Azure Cosmos DB](../cosmos-db/media/introduction/azure-cosmos-db-global-distribution.png)

**Un único contenedor de Azure Cosmos DB con particiones horizontales (en tres particiones de recursos dentro de una región) y luego distribución global en tres regiones de Azure**

Un contenedor de Azure Cosmos DB se distribuye en dos dimensiones: i) dentro de una región y ii) entre regiones. Este es el procedimiento: 

* **Distribución local**: en una sola región, un contenedor de Azure Cosmos DB se escala horizontalmente en cuanto a *particiones de recursos*. Cada partición de recursos administra un conjunto de claves y tiene una alta coherencia y disponibilidad, que se representa físicamente por cuatro réplicas, también conocidas como *conjunto de réplicas*, y la replicación del estado de la máquina entre las réplicas. Azure Cosmos DB es un sistema de recursos regido globalmente donde una partición de recursos es responsable de entregar su cuota de rendimiento del presupuesto de recursos del sistema que se le asignen. El escalado de un contenedor de Azure Cosmos DB es completamente transparente para los usuarios. Azure Cosmos DB administra las particiones de recursos y las divide y combina según sea necesario a medida que cambien los requisitos de almacenamiento y rendimiento. 
* **Distribución global**: si es una base de datos de varias regiones, cada una de las particiones de recursos se distribuye entre esas regiones. Las particiones de recursos que poseen el mismo conjunto de claves en diversas regiones forman el *conjunto de particiones* (vea la [ilustración anterior](#ThroughputGuarantees)).  Las particiones de recursos dentro de un conjunto de particiones se coordinan mediante el uso de replicación del estado de la máquina en varias regiones asociadas a la base de datos. En función del nivel de coherencia configurado, las particiones de recursos de un conjunto de particiones se configuran dinámicamente mediante diferentes topologías (por ejemplo, estrella, cadena de margarita, árbol, etc.). 

En virtud de una administración de particiones con gran capacidad de respuesta, del equilibrio de carga y de una regulación de recursos estricta, Azure Cosmos DB permite escalar elásticamente el rendimiento entre varias regiones de Azure asociadas a un contenedor o base de datos de Azure Cosmos DB. El cambio del rendimiento aprovisionado es una operación en tiempo de ejecución en Azure Cosmos DB. De similar manera a otras operaciones de base de datos, Azure Cosmos DB garantiza el límite superior absoluto en la latencia de la solicitud para cambiar el rendimiento aprovisionado. Por ejemplo, la siguiente ilustración muestra un contenedor de un cliente con rendimiento aprovisionado elásticamente (oscila entre un millón y diez millones de solicitudes por segundo en dos regiones) en función de la demanda.

![Rendimiento aprovisionado elásticamente de Azure Cosmos DB](./media/distribute-data-globally/elastic-throughput.png)

**Contenedor de un cliente con rendimiento aprovisionado elásticamente (entre un millón y diez millones de solicitudes por segundo)**

### <a id="ThroughputAndConsistency"></a>Relación del rendimiento con la coherencia 
Es lo mismo que se describió en [Relación de la coherencia con el rendimiento](#ConsistencyAndThroughput).

### <a id="ThroughputAndAvailability"></a>Relación del rendimiento con la disponibilidad
Azure Cosmos DB sigue manteniendo su disponibilidad alta cuando se realizan cambios en el rendimiento aprovisionado. Azure Cosmos DB administra de forma transparente las particiones de recursos (y realiza operaciones de división, combinación y clonación) y se asegura de que las operaciones no degraden el rendimiento o la disponibilidad, mientras que la aplicación aumenta o disminuye elásticamente el rendimiento. 

## <a id="AvailabilityGuarantees"></a>Garantías de disponibilidad
Azure Cosmos DB ofrece un Acuerdo de Nivel de Servicio con disponibilidad del 99,99 % para todas las cuentas de base de datos de una sola región y todas las cuentas de varias regiones con coherencia moderada, y disponibilidad del 99,999 % para todas las cuentas de base de datos de varias regiones. Como ya se ha descrito, las garantías de disponibilidad de Azure Cosmos DB incluyen un límite superior absoluto de la latencia de todas las operaciones de datos y del plano de control. Las garantías de disponibilidad no cambian con el número de regiones ni con la distancia geográfica entre regiones. Las garantías de disponibilidad son aplicables tanto a la conmutación por error manual como a la automática. Azure Cosmos DB ofrece API de hospedaje múltiple transparentes que garantizan que la aplicación puede funcionar en puntos de conexión lógicos y puede enrutar de forma transparente las solicitudes a la nueva región en caso de conmutación por error. No es preciso volver a implementar la aplicación si se produce una conmutación por error regional y los Acuerdos de Nivel de Servicio se mantienen en todo momento.

### <a id="AvailabilityAndConsistency"></a>Relación de la disponibilidad con la coherencia, la latencia y el rendimiento
La relación de la disponibilidad con la coherencia, la latencia y el rendimiento se describe en las secciones [Relación de la coherencia con la disponibilidad](#ConsistencyAndAvailability), [Relación de la latencia con la disponibilidad](#LatencyAndAvailability) y [Relación del rendimiento con la disponibilidad](#ThroughputAndAvailability). 

## <a id="CustomerFacingSLAMetrics"></a>Métricas de Acuerdo de Nivel de Servicio orientadas al cliente
Azure Cosmos DB expone de forma transparente las métricas de rendimiento, latencia, coherencia y disponibilidad. A estas métricas se puede acceder mediante programación y a través de Azure Portal (vea la siguiente ilustración). También se pueden configurar alertas en varios umbrales mediante Azure Application Insights.
 

![Métricas del Acuerdo de Nivel de Servicio visibles para clientes de Azure Cosmos DB](./media/distribute-data-globally/customer-slas.png)

**Las métricas de coherencia, latencia, rendimiento y disponibilidad están disponibles de forma transparente para todos los inquilinos**

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
