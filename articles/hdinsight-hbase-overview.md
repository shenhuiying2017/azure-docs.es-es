<properties urlDisplayName="HDInsight HBase overview" pageTitle="Informaci&oacute;n general sobre HBase en HDInsight | Azure" metaKeywords="" description="Introducci&oacute;n a HBase en HDInsight, casos de uso y comparaci&oacute;n con otras soluciones de base de datos." metaCanonical="" services="hdinsight" documentationCenter="" title="Informaci&oacute;n general de HBase de HDInsight" authors="bradsev" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="bradsev" />

# Información general de HBase de HDInsight

## ¿Qué es HBase?

HBase es una base de datos NoSQL de código abierto Apache basada en Hadoop que proporciona un acceso aleatorio y una coherencia fuerte para grandes cantidades de datos no estructurados y semiestructurados. Se modeló en BigTable de Google y es una base de datos orientada a las familias de columnas. Los datos se almacenan en las filas de una tabla, mientras que los datos de una fila se agrupan por familia de columnas. HBase es una base de datos sin esquemas en el sentido de que no es preciso que ni las columnas ni el tipo de datos almacenados en ellas se definan antes de usarlos. Mike Cafarella lanzó por primera vez el código fuente abierto en 2007. Realiza la escalación linealmente para gestionar petabytes de datos en miles de nodos. Puede basarse en la redundancia de datos, el procesamiento de lotes y otras características proporcionadas por aplicaciones distribuidas en el ecosistema Hadoop.

## ¿Qué es HBase de HDInsight de Azure?

HBase de HDInsight se ofrece como clúster administrado integrado en el entorno de Azure. Los clústeres están configurados para almacenar datos directamente en el almacenamiento de blobs de Azure, que proporciona una latencia baja y una mayor elasticidad de las opciones de rendimiento o coste. Esto permite que los clientes creen sitios web interactivos que trabajen con grandes conjuntos de datos, creen servicios que almacenen datos del sensor y telemetría desde millones de extremos y analicen estos datos con trabajos de Hadoop. HBase y Hadoop son buenos puntos de partida para proyectos con datos de gran tamaño en Azure y, en concreto, pueden permitir aplicaciones en tiempo real para trabajar con grandes conjuntos de datos.

La implementación de HDInsight aprovecha la arquitectura de escala de HBase para proporcionar particionamiento automático de tablas, una coherencia fuerte para lecturas y escrituras, y una conmutación automática por error. Se mejora el rendimiento mediante el almacenamiento en caché de memoria en una lectura y escritos de streaming de alto rendimiento. Asimismo, hay disponible un aprovisionamiento de redes virtuales para HBase de HDInsight. Para obtener detalles, consulte [Aprovisionamiento de clústeres de HDInsight en Red virtual de Azure][Aprovisionamiento de clústeres de HDInsight en Red virtual de Azure].

## ¿Cómo se administran los datos en HBase de HDInsight?

Los datos pueden administrarse en HBase mediante los comandos `create` `get`, `put` y `scan` del shell de HBase. Los datos se escriben en la base de datos usando `put` y se leen con los comandos `get`. El comando `scan` se usa para obtener datos de varias filas de una tabla. Los datos también se pueden administrar usando la API de C# de HBase, que proporciona una biblioteca de cliente en la parte superior de la API de REST de HBase. Asimismo, una base de datos de HBase se puede consultar usando Hive. Para obtener una introducción a estos modelos de programación, consulte [Introducción al uso de HBase con Hadoop en HDInsight][Introducción al uso de HBase con Hadoop en HDInsight]. También hay coprocesadores disponibles que permiten el procesamiento de datos en los nodos que hospedan la base de datos.

## Escenarios: ¿Cuáles son los casos de uso de HBase?

El caso de uso canónico para el que se creó BigTable y, por extensión, HBase, fue la búsqueda web. Los motores de búsqueda crean índices que asignan términos a las páginas web que los contienen. Sin embargo, hay muchos otros casos de uso a los que se adapta HBase, varios de los cuales se muestran en esta sección.

### Caso de uso n.º 1: almacén de pares clave-valor

HBase se puede usar como almacén de pares clave-valor y es adecuado para la administración de sistemas de mensajes. Facebook usa HBase para su sistema de mensajería y es ideal para almacenar y administrar comunicaciones de Internet. WebTable usa HBase para buscar y administrar tablas extraídas de páginas web.

### Caso de uso n.º 2: datos del sensor

HBase es útil para capturar datos que se recopilan cada vez más a partir de diversas fuentes. Entre estos se incluyen análisis sociales, serie temporal, actualización de paneles interactivos con tendencias y contadores y administración de sistemas de registro de auditoría. Entre los ejemplos se incluyen un terminal comercial de Bloomberg
y la base de datos de series temporales abierta (OpenTSDB), que almacena y proporciona acceso a las métricas recopiladas en beneficio de los sistemas de servidor.

### Caso de uso n.º 3: consulta en tiempo real

[Phoenix][Phoenix] es un motor de consulta de SQL para HBase Apache. Se obtiene acceso a él como controlador de JDBC y permite consultar y administrar tablas de HBase usando SQL.

### Caso de uso n.º 4: HBase como plataforma

Las aplicaciones pueden ejecutarse en la parte superior de HBase usándolo como almacén de datos. Entre los ejemplos se incluyen Phoenix, OpenTSDB, Kiji y Titan. Las aplicaciones también pueden integrarse con HBase. Entre los ejemplos se incluyen Hive, Pig, Solr, Storm, Flume, Impala, Spark, Ganglia y Drill.

## <a name="next-steps"></a>Pasos siguientes

[Introducción al uso de HBase con Hadoop en HDInsight][Introducción al uso de HBase con Hadoop en HDInsight]

[Aprovisionamiento de clústeres de HDInsight en Red virtual de Azure][Aprovisionamiento de clústeres de HDInsight en Red virtual de Azure]

[Análisis del espíritu de Twitter con HBase en HDInsight][Análisis del espíritu de Twitter con HBase en HDInsight]

[Uso de Maven para crear aplicaciones Java que utilicen HBase con HDInsight (Hadoop)][Uso de Maven para crear aplicaciones Java que utilicen HBase con HDInsight (Hadoop)]

[SDK de HBase de C#][SDK de HBase de C#]

## <a name="see-also"></a>Otras referencias

[HBase Apache][HBase Apache]

[Bigtable: un sistema de almacenamiento distribuido para datos estructurados][Bigtable: un sistema de almacenamiento distribuido para datos estructurados]

  [Aprovisionamiento de clústeres de HDInsight en Red virtual de Azure]: ../hdinsight-hbase-provision-vnet/
  [Introducción al uso de HBase con Hadoop en HDInsight]: http://azure.microsoft.com/es-es/documentation/articles/hdinsight-hbase-get-started/
  [Phoenix]: http://phoenix.apache.org/
  [Análisis del espíritu de Twitter con HBase en HDInsight]: ../hdinsight-hbase-analyze-twitter-sentiment/
  [Uso de Maven para crear aplicaciones Java que utilicen HBase con HDInsight (Hadoop)]: ../hdinsight-hbase-build-java-maven/
  [SDK de HBase de C#]: https://github.com/hdinsight/hbase-sdk-for-net
  [HBase Apache]: https://hbase.apache.org/
  [Bigtable: un sistema de almacenamiento distribuido para datos estructurados]: http://research.google.com/archive/bigtable.html
