<properties urlDisplayName="HDInsight Introduction" pageTitle="Introducci&oacute;n a Hadoop en HDInsight: An&aacute;lisis de grandes vol&uacute;menes de datos en la nube | Azure" metaKeywords="" description="Obtenga informaci&oacute;n sobre c&oacute;mo HDInsight de Azure usa cl&uacute;steres de Hadoop de Apache en la nube con el fin de proporcionar un marco de software para realizar tareas de administraci&oacute;n, an&aacute;lisis y generaci&oacute;n de informes sobre grandes vol&uacute;menes de datos." metaCanonical="" services="hdinsight" documentationCenter="" title="Introducci&oacute;n a Hadoop en HDInsight: procesamiento y an&aacute;lisis de grandes vol&uacute;menes de datos en la nube" authors="cgronlun" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="cgronlun" />

# Introducción a Hadoop en HDInsight: procesamiento y análisis de grandes volúmenes de datos en la nube

Obtenga información general sobre componentes de HDInsight, terminología común y escenarios, y consulte recursos para el uso de Hadoop en HDInsight.

Azure HDInsight implementa y aprovisiona clústeres de Apache Hadoop en la nube con el fin de proporcionar un marco de software diseñado para realizar tareas de administración, análisis y generación de informes en relación con grandes volúmenes de datos. El núcleo de Hadoop proporciona almacenamiento de datos confiable con el sistema de archivos distribuido Hadoop (HDFS, Hadoop Distributed File System) y un sencillo modelo de programación MapReduce para procesar y analizar, en paralelo, los datos almacenados en este sistema distribuido.

### ¿Qué son grandes volúmenes de datos?

Grandes volúmenes de datos se refiere a datos que se recopilan en volúmenes de escala continua, a velocidades cada vez mayores, y para una variedad cada vez más amplia de formatos no estructurados y contextos semánticos variables.

Los grandes volúmenes de datos describen cualquier conjunto de gran tamaño de información digital, desde el texto de una fuente de Twitter, hasta información del sensor de un equipamiento industrial e información sobre la actividad de exploración y compras en un catálogo en línea. Los grandes volúmenes de datos pueden ser históricos (es decir, datos almacenados) o en tiempo real (es decir, transferidos directamente desde el origen).

Para que los grandes volúmenes de datos proporcionen conocimientos o información de inteligencia procesables, no solo es necesario formular las preguntas correctas y recopilar datos relevantes para los problemas en cuestión, sino que los datos también deben ser accesibles y poderse limpiar y analizar para presentarlos de forma útil. Y ahí es donde Hadoop en HDInsight puede resultar práctico.

## En este artículo

En este artículo se ofrece información general sobre Hadoop en HDInsight, que incluye:

-   **[Información general sobre el ecosistema de Hadoop en HDInsight:][Información general sobre el ecosistema de Hadoop en HDInsight:]** HDInsight es la solución de Hadoop en Azure y ofrece implementaciones de Storm, HBase, Pig, Hive, Sqoop, Oozie, Ambari, etc. HDInsight se integra también con herramientas de Business Intelligence (BI) como, por ejemplo, Excel, SQL Server Analysis Services y SQL Server Reporting Services.
-   **[Ventajas de Hadoop en la nube:][Ventajas de Hadoop en la nube:]** Motivos que deben tenerse en cuenta para la implementación de Hadoop en la nube de HDInsight.
-   **[Soluciones de HDInsight para el análisis de grandes volúmenes de datos:][Soluciones de HDInsight para el análisis de grandes volúmenes de datos:]** algunas formas prácticas en que puede usar HDInsight para responder a preguntas sobre su organización, desde el análisis de la opinión de Twitter hasta el análisis de la eficacia del sistema HVAC.
-   **[Recursos para obtener más información sobre el análisis de grandes volúmenes de datos, Hadoop y HDInsight:][Recursos para obtener más información sobre el análisis de grandes volúmenes de datos, Hadoop y HDInsight:]** Vínculos a información adicional.

## <a name="overview"></a>Información general sobre el ecosistema de Hadoop en HDInsight

Hadoop es el componente tecnológico en rápida expansión que constituye la solución imprescindible para el análisis de grandes volúmenes de datos. HDInsight es el marco para la implementación de Hadoop en la nube de Microsoft Azure.

Los clústeres de Hadoop en HDInsight usan versiones de la distribución de Hortonworks Data Platform (HDP) y el conjunto de componentes de Hadoop en dicha distribución. Para obtener información sobre los componentes y sus versiones en clústeres de HDInsight, consulte [Novedades en las versiones de clústeres de Hadoop proporcionadas por HDInsight][Novedades en las versiones de clústeres de Hadoop proporcionadas por HDInsight]

Apache Hadoop es un marco de software para la administración y el análisis de grandes volúmenes de datos. El núcleo de Apache Hadoop proporciona almacenamiento de datos fiable con el sistema de archivos distribuidos Hadoop (HDFS, Hadoop Distributed File System) y un modelo de programación MapReduce sencillo para procesar y analizar, en paralelo, los datos almacenados en este sistema distribuido. HDFS utiliza la replicación de datos para resolver los problemas ocasionados por errores de hardware que surgen al implementar dichos sistemas altamente distribuidos.

A continuación se indican las tecnologías de Hadoop en HDInsight:

-   **[Ambari][Ambari]:** aprovisionamiento, administración y supervisión de clústeres
-   **[Avro][Avro]** (biblioteca de Microsoft .NET para Avro): serialización de datos para el entorno de Microsoft .NET
-   **[HBase][HBase]:** base de datos no relacional para tablas de gran tamaño
-   **[HDFS][HDFS]:** sistema de archivos distribuido de Hadoop
-   **[Hive][Hive]:** consultas de tipo SQL
-   **[Mahout][Mahout]:** aprendizaje automático
-   **[MapReduce y YARN][MapReduce y YARN]:** procesamiento distribuido y administración de recursos
-   **[Oozie][Oozie]:** administración de flujos de trabajo
-   **[Pig][Pig]:** scripts más sencillos para transformaciones de MapReduce
-   **[Sqoop][Sqoop]:** importación y exportación de datos
-   **[Storm][Storm]:** procesamiento en tiempo real de secuencias rápidas de datos de gran tamaño
-   **[Zookeeper][Zookeeper]:** procesos coordinados en sistemas distribuidos

Además, descubra las **[herramientas de Business Intelligence][herramientas de Business Intelligence]** que puede usar con HDInsight.

### <a name="ambari"></a>Ambari

Apache Ambari sirve para el aprovisionamiento, la administración y la supervisión de clústeres de Hadoop de Apache. Incluye una recopilación intuitiva de herramientas de operador y un conjunto sólido de API que ocultan la complejidad de Hadoop y simplifican la operación de clústeres. Consulte [Supervisión de clústeres de Hadoop en HDInsight con la API de Ambari][Supervisión de clústeres de Hadoop en HDInsight con la API de Ambari] y [Referencia de API de Ambari][Referencia de API de Ambari].

### <a name="avro"></a>Avro (biblioteca de Microsoft .NET para Avro)

La biblioteca de Microsoft .NET para Avro implementa el formato compacto de intercambio de datos binarios de Apache Avro para la serialización del entorno de Microsoft .NET. Utiliza [JSON][JSON] para definir un esquema independiente del lenguaje que garantiza la interoperabilidad de lenguajes, lo que se traduce en que los serializados en un lenguaje se pueden leer en otro. Encontrará información detallada sobre el formato en las [Especificaciones de Apache Avro][Especificaciones de Apache Avro].
El formato de los archivos de Avro admite el modelo de programación distribuida de MapReduce. Los archivos son "divisibles" en el sentido de que se puede buscar cualquier punto en un archivo y comenzar a leer desde un bloque concreto. Para descubrir cómo, consulte [Serialización de datos con la biblioteca de Microsoft .NET para Avro][Serialización de datos con la biblioteca de Microsoft .NET para Avro].

### <a name="hbase"></a>HBase

[Apache HBase][Apache HBase] es una base de datos no relacional basada en Hadoop y diseñada para grandes cantidades de datos no estructurados y semiestructurados: posiblemente miles de millones de filas multiplicados por millones de columnas. Los clústeres de HBase en HDInsight están configurados para almacenar datos directamente en el almacenamiento de blobs de Azure, con una latencia baja y una mayor elasticidad. Consulte [Información general sobre Hadoop en HDInsight][Información general sobre Hadoop en HDInsight].

### <a name="hdfs"></a>HDFS

El sistema de archivos distribuido de Hadoop (HDFS) es un sistema de archivos distribuido que, junto con MapReduce y YARN, conforma el núcleo del ecosistema Hadoop. HDFS es el sistema de archivos estándar para los clústeres de Hadoop en HDInsight.

### <a name="hive"></a>Hive

[Apache Hive][Apache Hive] es el software de almacén de datos basado en Hadoop que le permite consultar y administrar grandes conjuntos de datos en almacenamiento distribuido con un lenguaje de tipo SQL denominado HiveQL. Hive, al igual que Pig, es una abstracción situada en un nivel superior a MapReduce y, al ejecutarse, traduce las consultas en una serie de trabajos de MapReduce. Hive se encuentra conceptualmente más cerca de un sistema de administración de bases de datos relacionales que Pig y, por lo tanto, es más adecuado para su uso con datos más estructurados. Para los datos no estructurados, Pig es una mejor opción. Consulte [Uso de Hive con Hadoop en HDInsight][Uso de Hive con Hadoop en HDInsight]

### <a name="mahout"></a>Mahout

[Apache Mahout][Apache Mahout] es una biblioteca escalable de algoritmos de aprendizaje automático que se ejecuta en Hadoop. Mediante principios de estadísticas, las aplicaciones de aprendizaje automático enseñan a los sistemas a aprender de los datos y a usar los resultados obtenidos en el pasado para determinar el comportamiento en el futuro. Consulte [Generación de recomendaciones de películas mediante Mahout en Hadoop][Generación de recomendaciones de películas mediante Mahout en Hadoop].

### <a name="mapreduce"></a>MapReduce y YARN

MapReduce de Hadoop es un marco de software para la escritura de aplicaciones con el fin de procesar en paralelo conjuntos de grandes volúmenes de datos. Un trabajo de MapReduce divide conjuntos de gran tamaño y organiza los datos en pares clave-valor para su procesamiento.

Apache YARN es la última generación de MapReduce (MapReduce 2.0 o MRv2) que divide las dos tareas principales de la herramienta de seguimiento de trabajos, la administración de recursos y la programación y supervisión de trabajos, en entidades independientes.

Para obtener más información sobre MapReduce, consulte [MapReduce][MapReduce] en Hadoop Wiki. Para obtener información sobre YARN, consulte [MapReduce de última generación de Apache Hadoop (YARN)][MapReduce de última generación de Apache Hadoop (YARN)].

### <a name="oozie"></a>Oozie

[Apache Oozie][Apache Oozie] es un sistema de coordinación de flujos de trabajo que administra trabajos de Hadoop. Se integra con la pila de Hadoop y es compatible con los trabajos de Hadoop para MapReduce, Pig, Hive y Sqoop. También puede usarse para programar trabajos específicos de un sistema, como scripts de shell o programas Java. Consulte [Uso del coordinador de Oozie de tiempo con Hadoop en HDInsight][Uso del coordinador de Oozie de tiempo con Hadoop en HDInsight].

### <a name="pig"></a>Pig

[Apache Pig][Apache Pig] es una plataforma de alto nivel que permite realizar transformaciones complejas de MapReduce en conjuntos de datos de gran tamaño con un sencillo lenguaje de scripting denominado Pig Latin. Pig traduce los scripts de Pig Latin para que se ejecuten en Hadoop. Se pueden crear funciones definidas por el usuario (UDF) para ampliar Pig Latin. Consulte [Uso de Pig con Hadoop para analizar un archivo de registro de Apache][Uso de Pig con Hadoop para analizar un archivo de registro de Apache].

### <a name="sqoop"></a>Sqoop

[Apache Sqoop][Apache Sqoop] es una herramienta que transfiere grandes cantidades de datos entre Hadoop y bases de datos relacionales, como SQL u otros almacenes de datos estructurados, de la forma más eficiente posible. Consulte [Uso de Sqoop con Hadoop][Uso de Sqoop con Hadoop].

### <a name="storm"></a>Storm

[Apache Storm][Apache Storm] es un sistema distribuido de cálculo en tiempo real para el procesamiento rápido de grandes secuencias de datos. Storm se ofrece como clúster administrado en HDInsight. Consulte [Análisis de datos de sensor en tiempo real con Storm y Hadoop][Análisis de datos de sensor en tiempo real con Storm y Hadoop].

### <a name="zookeeper"></a>Zookeeper

[Apache Zookeeper][Apache Zookeeper] coordina procesos en grandes sistemas distribuidos por medio de un espacio de nombres jerárquico compartido de registros de datos (znodes). Znodes contiene pequeñas cantidades de metadatos necesarios para la coordinación de procesos: estado, ubicación, configuración, etc.

### <a name="bi"></a>Herramientas de Business Intelligence

Las herramientas de Business Intelligence habituales (como Excel, PowerPivot, SQL Server Analysis Services y Reporting Services) recuperan, analizan y generan informes de datos integrados en HDInsight con el complemento Power Query o Microsoft Hive ODBC Driver.

Estas herramientas de BI pueden ayudarle en el análisis de grandes volúmenes de datos:

-   [Descarga de Microsoft Power Query para Excel][Descarga de Microsoft Power Query para Excel]
-   [Descarga de Microsoft Hive ODBC Driver][Descarga de Microsoft Hive ODBC Driver]
-   [Más información sobre SQL Server Analysis Services][Más información sobre SQL Server Analysis Services]
-   [Más información sobre SQL Server Reporting Services][Más información sobre SQL Server Reporting Services]

## <a name="advantage"></a>Ventajas de Hadoop en la nube

Como parte del ecosistema de la nube de Azure, Hadoop en HDInsight ofrece varias ventajas, entre otras:

-   Componentes de Hadoop de última generación. Para obtener información más detallada, consulte [Novedades en las versiones de clústeres proporcionadas por HDInsight][Novedades en las versiones de clústeres de Hadoop proporcionadas por HDInsight]
-   Alta disponibilidad y confiabilidad de los clústeres. Si desea información más detallada, consulte [Disponibilidad y confiabilidad de clústeres de Hadoop en HDInsight][Disponibilidad y confiabilidad de clústeres de Hadoop en HDInsight].
-   Almacenamiento de datos eficiente y económico con el almacenamiento de blobs de Azure, opción compatible con Hadoop. Si desea información más detallada, consulte [Uso del almacenamiento de blobs de Azure con Hadoop en HDInsight][Uso del almacenamiento de blobs de Azure con Hadoop en HDInsight].
-   Integración con otros servicios de Azure, como [Sitios web][Sitios web] y [Base de datos SQL][Base de datos SQL].
-   Coste inicial bajo. Inicie una [evaluación gratuita][evaluación gratuita] o consulte [Detalles de precios de HDInsight][Detalles de precios de HDInsight].

Para obtener más información sobre las ventajas de Hadoop en HDInsight, consulte la [página de características de Azure para HDInsight][página de características de Azure para HDInsight].

## <a name="solutions"></a>Prueba de soluciones de HDInsight para el análisis de grandes volúmenes de datos

Analice los datos de su organización para obtener mayor conocimiento sobre su negocio. A continuación se muestran algunos ejemplos:

-   [Análisis de datos del sensor de HVAC][Análisis de datos del sensor de HVAC]: Aprenda a analizar los datos del sensor mediante Hive con HDInsight (Hadoop) y visualice a continuación los datos en Microsoft Excel. En este ejemplo, usará Hive para procesar datos históricos producidos por sistemas HVAC para ver los sistemas que pueden mantener de forma confiable una temperatura establecida.
-   [Uso de Hive con HDInsight para analizar registros de sitios web][Uso de Hive con HDInsight para analizar registros de sitios web]: A prenda a usar HiveQL in HDInsight para analizar los registros del sitio web con objeto de conocer la frecuencia de las visitas diarias al mismo desde sitios web externos y obtener un resumen de los errores del sitio web que los usuarios experimentan.
-   [Análisis de datos de sensor en tiempo real con Storm y HBase en HDInsight (Hadoop)][Análisis de datos de sensor en tiempo real con Storm y Hadoop]: Aprenda a compilar una solución que use un clúster de Storm en HDInsight con el fin de procesar datos de sensor desde centros de eventos de Azure y visualice los datos de sensor procesados como información prácticamente en tiempo real en un panel basado en web.

Para probar Hadoop en HDInsight, consulte artículos sobre "Introducción" en la sección de exploración de la [página de documentación de HDInsight][página de documentación de HDInsight]. Para probar ejemplos más avanzados, desplácese hasta la sección de análisis.

## <a name="resources"></a>Recursos para obtener más información sobre el análisis de grandes volúmenes de datos, Hadoop y HDInsight

### Hadoop en HDInsight

-   [Documentación de HDInsight][página de documentación de HDInsight]: Página de documentación de Azure HDInsight con vínculos a artículos, vídeos y otros recursos.

-   [Mapa de aprendizaje para Hadoop en HDInsight][Mapa de aprendizaje para Hadoop en HDInsight]: Paseo guiado por la documentación de Hadoop para HDInsight.

-   [Get started with Azure HDInsight][Get started with Azure HDInsight]: Tutorial de inicio rápido sobre el uso de Hadoop en HDInsight.

-   [Run the HDInsight samples][Run the HDInsight samples]: Tutorial sobre la ejecución de ejemplos incluidos en HDInsight.

-   [Azure HDInsight SDK][Azure HDInsight SDK]: Documentación de referencia de HDinsight SDK.

### Base de datos SQL en Azure

-   [Base de datos SQL de Azure][Base de datos SQL de Azure]: Documentación de MSDN sobre Base de datos SQL.

-   [Portal de administración de Base de datos SQL][Portal de administración de Base de datos SQL]: Herramienta de administración de bases de datos ligera y fácil de usar para administrar el modelo de ejecución Base de datos SQL en la nube.

-   [Adventure Works for SQL Database][Adventure Works for SQL Database]: Página de descarga de bases de datos de ejemplo para el modelo de ejecución Base de datos SQL.

### Microsoft Business Intelligence

-   [Conexión de Excel a Hadoop con Power Query][Conexión de Excel a Hadoop con Power Query]: Aprenda a conectar Excel a una cuenta de almacenamiento de Azure que almacene los datos asociados a su clúster HDInsight mediante Microsoft Power Query para Excel.

-   [Conexión de Excel a Hadoop con Microsoft Hive ODBC Driver][Conexión de Excel a Hadoop con Microsoft Hive ODBC Driver]: Aprenda a importar datos desde HDInsight con Microsoft Hive ODBC Driver.

-   [Microsoft Business Intelligence (BI)][Microsoft Business Intelligence (BI)]: Obtenga información sobre Power BI para Office 365, descargue la evaluación de SQL Server y configure SharePoint Server 2013 y SQL Server BI.

### Apache Hadoop

-   [Apache Hadoop][Apache Hadoop]: Obtenga más información acerca de la biblioteca de software Apache Hadoop, un marco que permite efectuar el procesamiento distribuido de grandes conjuntos de datos en clústeres de equipos.

-   [HDFS][1]: Obtenga más información acerca de la arquitectura y el diseño del sistema de archivos distribuidos Hadoop (HDFS), el sistema de almacenamiento principal de las aplicaciones de Hadoop.

-   [MapReduce][2]: Obtenga más información acerca de este marco de programación para escribir aplicaciones de Hadoop con el fin de procesar enormes cantidades de datos, en paralelo, en grandes clústeres de nodos de proceso.

  [Información general sobre el ecosistema de Hadoop en HDInsight:]: #overview
  [Ventajas de Hadoop en la nube:]: #advantage
  [Soluciones de HDInsight para el análisis de grandes volúmenes de datos:]: #solutions
  [Recursos para obtener más información sobre el análisis de grandes volúmenes de datos, Hadoop y HDInsight:]: #resources
  [Novedades en las versiones de clústeres de Hadoop proporcionadas por HDInsight]: ../hdinsight-component-versioning/
  [Ambari]: #ambari
  [Avro]: #avro
  [HBase]: #hbase
  [HDFS]: #hdfs
  [Hive]: #hive
  [Mahout]: #mahout
  [MapReduce y YARN]: #mapreduce
  [Oozie]: #oozie
  [Pig]: #pig
  [Sqoop]: #sqoop
  [Storm]: #storm
  [Zookeeper]: #zookeeper
  [herramientas de Business Intelligence]: #bi
  [Supervisión de clústeres de Hadoop en HDInsight con la API de Ambari]: ../hdinsight-monitor-use-ambari-api/
  [Referencia de API de Ambari]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [JSON]: http://www.json.org/
  [Especificaciones de Apache Avro]: http://avro.apache.org/docs/current/spec.html
  [Serialización de datos con la biblioteca de Microsoft .NET para Avro]: ../hdinsight-dotnet-avro-serialization/
  [Apache HBase]: http://hbase.apache.org/
  [Información general sobre Hadoop en HDInsight]: ../hdinsight-hbase-overview/
  [Apache Hive]: http://hive.apache.org/
  [Uso de Hive con Hadoop en HDInsight]: ../hdinsight-use-hive/
  [Apache Mahout]: https://mahout.apache.org/
  [Generación de recomendaciones de películas mediante Mahout en Hadoop]: ../hdinsight-mahout/
  [MapReduce]: http://wiki.apache.org/hadoop/MapReduce
  [MapReduce de última generación de Apache Hadoop (YARN)]: http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html
  [Apache Oozie]: http://oozie.apache.org/
  [Uso del coordinador de Oozie de tiempo con Hadoop en HDInsight]: ../hdinsight-use-oozie-coordinator-time/
  [Apache Pig]: http://pig.apache.org/
  [Uso de Pig con Hadoop para analizar un archivo de registro de Apache]: ../hdinsight-use-pig/
  [Apache Sqoop]: http://sqoop.apache.org/
  [Uso de Sqoop con Hadoop]: ../hdinsight-use-sqoop/
  [Apache Storm]: https://storm.incubator.apache.org/
  [Análisis de datos de sensor en tiempo real con Storm y Hadoop]: ../hdinsight-storm-sensor-data-analysis/
  [Apache Zookeeper]: http://zookeeper.apache.org/
  [Descarga de Microsoft Power Query para Excel]: http://www.microsoft.com/es-es/download/details.aspx?id=39379
  [Descarga de Microsoft Hive ODBC Driver]: http://www.microsoft.com/es-es/download/details.aspx?id=40886
  [Más información sobre SQL Server Analysis Services]: http://www.microsoft.com/es-es/server-cloud/solutions/business-intelligence/analysis.aspx
  [Más información sobre SQL Server Reporting Services]: http://msdn.microsoft.com/es-es/library/ms159106.aspx
  [Disponibilidad y confiabilidad de clústeres de Hadoop en HDInsight]: ../hdinsight-high-availability/
  [Uso del almacenamiento de blobs de Azure con Hadoop en HDInsight]: ../hdinsight-use-blob-storage/
  [Sitios web]: ../documentation/services/websites/
  [Base de datos SQL]: ../documentation/services/sql-database/
  [evaluación gratuita]: /pricing/free-trial/
  [Detalles de precios de HDInsight]: ../pricing/details/hdinsight/
  [página de características de Azure para HDInsight]: ../services/hdinsight/
  [Análisis de datos del sensor de HVAC]: ../hdinsight-hive-analyze-sensor-data/
  [Uso de Hive con HDInsight para analizar registros de sitios web]: ../hdinsight-hive-analyze-website-log/
  [página de documentación de HDInsight]: ../documentation/services/hdinsight/
  [Mapa de aprendizaje para Hadoop en HDInsight]: ../hdinsight-learn-map
  [Get started with Azure HDInsight]: ../hdinsight-get-started/
  [Run the HDInsight samples]: ../hdinsight-run-samples/
  [Azure HDInsight SDK]: http://msdnstage.redmond.corp.microsoft.com/es-es/library/dn479185.aspx
  [Base de datos SQL de Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/ee336279.aspx
  [Portal de administración de Base de datos SQL]: http://msdn.microsoft.com/es-es/library/windowsazure/gg442309.aspx
  [Adventure Works for SQL Database]: http://msftdbprodsamples.codeplex.com/releases/view/37304
  [Conexión de Excel a Hadoop con Power Query]: ../hdinsight-connect-excel-power-query/
  [Conexión de Excel a Hadoop con Microsoft Hive ODBC Driver]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [Microsoft Business Intelligence (BI)]: http://www.microsoft.com/es-es/server-cloud/solutions/business-intelligence/default.aspx
  [Apache Hadoop]: http://hadoop.apache.org/
