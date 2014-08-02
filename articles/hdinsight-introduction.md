<properties linkid="manage-services-hdinsight-introduction-hdinsight" urlDisplayName="HDInsight Introduction" pageTitle="Introduction to Azure HDInsight | Azure" metaKeywords="" description="Learn how Azure HDInsight uses Apache Hadoop clusters in the cloud, to provide a software framework to manage, analyze, and report on big data." metaCanonical="" services="hdinsight" documentationCenter="" title="Introduction to Azure HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

Introducción a Azure HDInsight
==============================

Información general
-------------------

Azure HDInsight es un servicio que implementa y aprovisiona clústers Apache Hadoop® en la nube con el fin de proporcionar un marco de software que se ha diseñado para realizar tareas de administración, análisis y generación de informes en relación con datos de gran tamaño.

### Datos de gran tamaño

Los datos se describen como "datos de gran tamaño" para indicar que se están recopilando en volúmenes de escala continua, a velocidades cada vez mayores, y para una variedad cada vez más amplia de formatos no estructurados y contextos semánticos variables. La recopilación de datos de gran tamaño no proporciona valor por sí mismo a una empresa. Para que los datos de gran tamaño proporcionen valor en forma de conocimientos o datos de inteligencia procesables, no solo es necesario formular las preguntas correctas y recopilar datos relevantes para los problemas en cuestión, sino que también los datos deben ser accesibles y se deben limpiar y analizar para presentarlos de forma útil, normalmente en combinación con datos de otras fuentes diversas que establezcan una perspectiva y un contexto en relación con lo que actualmente se denomina mashup.

### Apache Hadoop

Apache Hadoop es un marco de software que facilita la administración y el análisis de datos de gran tamaño. El núcleo de Apache Hadoop proporciona almacenamiento de datos fiable con el sistema de archivos distribuidos Hadoop (HDFS, Hadoop Distributed File System) y un modelo de programación MapReduce sencillo para procesar y analizar, en paralelo, los datos almacenados en este sistema distribuido. HDFS utiliza la replicación de datos para resolver los problemas ocasionados por errores de hardware que surgen al implementar dichos sistemas altamente distribuidos.

### MapReduce

Para simplificar las complejidades del análisis de datos no estructurados procedentes de diversas fuentes, el modelo de programación MapReduce proporciona una abstracción del núcleo que sobrescribe el cierre del mapa y reduce las operaciones. El modelo de programación MapReduce visualiza todos los trabajos como cálculos sobre los conjuntos de datos que estén compuestos de pares clave-valor. Por lo tanto, tanto los archivos de entrada como los de salida deben contener conjuntos de datos que estén compuestos de pares clave-valor únicamente. Lo más importante de esta limitación es que los trabajos de MapReduce, en consecuencia, admiten composición.

Otros proyectos relacionados con Hadoop, por ejemplo Pig y Hive, se compilan sobre el sistema HDFS y el marco MapReduce. Proyectos como estos se utilizan para proporcionar una manera de administrar un clúster más sencilla que si se trabajara directamente con programas MapReduce. Pig, por ejemplo, permite escribir programas, mediante un lenguaje de procedimientos denominado Pig Latin, que se compilan en programas MapReduce en el clúster. También proporciona controles fluidos para administrar el flujo de datos. Hive es una infraestructura de almacenes de datos que proporciona una abstracción de datos en tablas para los archivos que estén almacenados en un clúster, con el fin de poder realizar consultas mediante instrucciones de tipo SQL en un lenguaje declarativo que se denomina HiveQL.

### HDInsight

Azure HDInsight pone Apache Hadoop a disposición de los usuarios como servicio en la nube. Proporciona el marco de software HDFS/MapReduce y los proyectos relacionados, como Pig y Hive, en un entorno más sencillo, escalable y rentable.

Uno de los aspectos más eficientes que se han introducido en HDInsight es el modo de administración y almacenamiento de los datos. HDInsight utiliza el almacenamiento de blobs de Azure como sistema de archivos predeterminado. El almacenamiento de blobs y el sistema HDFS son sistemas de archivos distintos que se han optimizado, respectivamente, para el almacenamiento de datos y la ejecución de cálculos sobre esos datos.

-   El almacenamiento de blobs de Azure proporciona un opción de almacenamiento altamente escalable y disponible, económica, duradera e intercambiable para los datos que se deben procesar con HDInsight.
-   Los clústeres de Hadoop que HDInsight implementa en HDFS están optimizados para ejecutar tareas de cálculo de MapReduce sobre esos datos.

Los clústeres de HDInsight se implementan en Azure, en nodos de proceso, para ejecutar tareas MapReduce y los usuarios pueden descartarlos una vez que estas tareas se hayan completado. El mantenimiento de datos en los clústeres de HDFS después de haber completado los cálculos supondría un alto coste para el almacenamiento de estos datos. El almacenamiento de blobs es una solución de almacenamiento en Azure robusta y de uso general. Por lo tanto, el almacenamiento de datos en blobs permite que los clústeres que se utilizan para realizar cálculos se puedan eliminar de forma segura sin pérdida de datos del usuario. No obstante, el almacenamiento en blobs no solo es una solución de bajo coste. Proporciona una interfaz de sistema de archivos HDFS completa que ofrece una experiencia inigualable a los clientes mediante la habilitación de un conjunto de componentes completo en el ecosistema Hadoop para procesar directamente (de forma predeterminada) los datos que administra.

HDInsight utiliza Azure PowerShell para configurar, ejecutar y procesar posteriormente los trabajos de Hadoop. HDInsight también proporciona un conector Sqoop que se puede utilizar para importar datos de una base de datos SQL de Azure al sistema HDFS o exportar datos a una base de datos SQL de Azure desde el sistema HDFS.

Microsoft Power Query para Excel está disponible para importar datos de Azure HDInsight o cualquier sistema HDFS a Excel. Este complemento mejora la experiencia de Business Intelligence (BI) de autoservicio en Excel al simplificar la detección de datos y el acceso a los mismos para una gran variedad de orígenes de datos. Además de Power Query, Microsoft Hive ODBC Driver está disponible para integrar herramientas de BI, como Excel, SQL Server Analysis Services y Reporting Services, con el fin de facilitar y simplificar el análisis de datos de extremo a extremo.

### Descripción general

Este tema describe el ecosistema Hadoop que admite HDInsight, los principales escenarios de uso en HDInsight y una guía de recursos adicionales. Contiene las siguientes secciones:

-   [El ecosistema Hadoop en HDInsight](#Ecosystem): HDInsight proporciona implementaciones de Pig, Hive y Sqoop y admite otras herramientas de BI como Excel, SQL Server Analysis Services y Reporting Services, que se integran con el almacenamiento de blobs o HDFS y el marco MapReduce mediante Power Query o Microsoft Hive ODBC Driver. En esta sección se describen los trabajos que pueden procesar estos programas en el ecosistema Hadoop.

-   [Escenarios de datos de gran tamaño en HDInsight](#Scenarios): Esta sección responde a la pregunta: ¿para qué tipos de trabajos es apropiada la tecnología HDInsight?

-   [Recursos para HDInsight](#Resources): Esta sección indica el lugar en el que se pueden encontrar recursos adecuados para obtener información adicional.


<h2 id="ecosystem"> <a name="Ecosystem">El ecosistema Hadoop en Azure </a></h2>

### Introducción

HDInsight ofrece un marco para implementar la solución basada en la nube de Microsoft para el tratamiento de datos de gran tamaño. Este ecosistema federado administra y analiza grandes cantidades de datos para explotar las capacidades de procesamiento en paralelo del modelo de programación MapReduce. Las tecnologías Hadoop compatibles con Apache que se pueden utilizar con HDInsight se presentan una a una y se describen brevemente en esta sección.

HDInsight proporciona implementaciones de Hive y Pig para integrar el procesamiento de datos y las capacidades de almacenamiento. La solución para datos de gran tamaño de Microsoft se integra con las herramientas de BI de Microsoft, como SQL Server Analysis Services, Reporting Services, PowerPivot y Excel. De este modo es posible realizar un procesamiento de BI sencillo con los datos que almacena y administra HDInsight en el almacenamiento de blobs.

También se pueden descargar y utilizar con HDInsight otras tecnologías compatibles con Apache y tecnologías similares que forman parte del ecosistema Hadoop y que se han compilado para su ejecución sobre los clústeres de Hadoop. Entre ellas se incluyen tecnologías de código abierto, como Sqoop, que integra HDFS con el almacenamiento de datos relacionales.

### Pig

Pig es una plataforma de alto nivel para el procesamiento de datos de gran tamaño en clústeres de Hadoop. Pig consta de un lenguaje de flujo de datos, denominado Pig Latin, compatible con la escritura de consultas en grandes conjuntos de datos y un entorno que ejecuta programas desde una consola. Los programas escritos en Pig Latin constan de una serie de transformaciones de conjuntos de datos que se convierten en una serie de programas MapReduce. Las abstracciones de Pig Latin proporcionan estructuras de datos más detalladas que las de MapReduce y hacen en Hadoop lo que SQL hace en los sistemas de administración de bases de datos relacionales (RDBMS). Pig Latin es completamente extensible. Las funciones definidas por el usuario (UDF), escritas en Java, Python, Ruby, C\# o JavaScript, se pueden llamar para personalizar cada fase de ruta de procesamiento al elaborar el análisis. Para obtener información adicional, consulte la página de [presentación de Apache Pig](http://pig.apache.org/) (en inglés)

### Hive

Hive es un almacén de datos distribuidos que administra datos almacenados en un sistema HDFS. Es el motor de consultas de Hadoop. Hive proporciona a los analistas expertos en SQL una interfaz parecida a SQL y un modelo de datos relacionales. Hive utiliza un lenguaje denominado HiveQL, un dialecto de SQL. Hive, al igual que Pig, es una abstracción situada en un nivel superior a MapReduce y, al ejecutarse, traduce las consultas en una serie de trabajos de MapReduce. Los escenarios de Hive se parecen mucho a los de RDBMS, por lo que son apropiados para el uso con datos más estructurados. Para los datos no estructurados, Pig es una mejor opción. Para obtener información adicional, consulte la página de [presentación de Apache Hive](http://hive.apache.org/) (en inglés)

### Sqoop

Sqoop es una herramienta que transfiere grandes cantidades de datos entre Hadoop y bases de datos relacionales, como SQL u otros almacenes de datos estructurados, de la forma más eficiente posible. Puede utilizar Sqoop para importar datos de almacenes de datos estructurados externos al sistema HDFS o a sistemas similares como Hive. Sqoop también puede extraer datos de Hadoop y exportar los datos extraídos a bases de datos relacionales externas, almacenes de datos empresariales o cualquier otro tipo de almacén de datos estructurados. Para obtener información adicional, consulte el sitio web de [Apache Sqoop](http://sqoop.apache.org/) (en inglés).

### Herramientas de Business Intelligence y conectores

Las herramientas de Business Intelligence habituales (como Excel, PowerPivot, SQL Server Analysis Services y Reporting Services) recuperan, analizan y generan informes de datos integrados en HDInsight con el complemento Power Query o Microsoft Hive ODBC Driver.

-   Microsoft Power Query para Excel se puede descargar en [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkID=286689).

-   Microsoft Hive ODBC Driver se puede descargar en [este sitio de descarga](http://go.microsoft.com/fwlink/?LinkID=286698) (en inglés).

-   Para obtener información sobre Analysis Services, consulte la página [SQL Server 2012 Analysis Services](http://www.microsoft.com/sqlserver/en/us/solutions-technologies/business-intelligence/SQL-Server-2012-analysis-services.aspx).

-   Para obtener información sobre Reporting Services, consulte la página [SQL Server 2012 Reporting](http://www.microsoft.com/en-us/sqlserver/solutions-technologies/business-intelligence/reporting.aspx).

Escenarios de datos de gran tamaño en HDInsight
-----------------------------------------------

Un análisis puntual, por lotes, de un conjunto de datos no estructurado y completo que esté almacenado en nodos de Azure y que no requiera actualizarse periódicamente es un escenario ejemplar que ofrece un caso de aplicación de HDInsight.

Estas condiciones se aplican a una gran variedad de actividades empresariales, científicas y gubernamentales. Entre ellas se incluyen, por ejemplo, la supervisión de cadenas de suministro al por menor, los modelos de comercialización sospechosos en el ámbito financiero, los modelos de demanda de los servicios públicos, la calidad del aire y el agua en redes de sensores para el control medioambiental o los modelos delictivos en áreas metropolitanas.

Las tecnologías HDInsight (y las tecnologías Hadoop en general) son principalmente adecuadas para el tratamiento de grandes cantidades de datos registrados o archivados que no requieren actualizarse periódicamente una vez escritos y que se leen con frecuencia, normalmente para realizar un análisis completo. Este escenario complementa el de los datos que se tratan de una forma más adecuada con un RDBMS, con el que es necesario utilizar una menor cantidad de datos (gigabytes en lugar de petabytes) y que se deben actualizar continuamente o consultar en busca de datos específicos del conjunto total de datos. RDBMS funciona especialmente bien con datos estructurados que estén organizados y almacenados según un esquema fijo. MapReduce funciona bien con datos no estructurados y sin un esquema predefinido porque es capaz de interpretar esos datos durante el procesamiento.

Pasos siguientes: Recursos para HDInsight
-----------------------------------------

**Microsoft: HDInsight**

-   [HDInsight Documentation](http://go.microsoft.com/fwlink/?LinkID=285601): Página de documentación de Azure HDInsight con vínculos a artículos, vídeos y otros recursos.

-   [Get started with Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/): Tutorial que proporciona una guía de inicio rápido para el uso de HDInsight.

-   [Run the HDInsight samples](/en-us/manage/services/hdinsight/howto-run-samples/): Tutorial sobre la ejecución de ejemplos incluidos en HDInsight.

-   [Big data and Azure](http://www.windowsazure.com/en-us/home/scenarios/big-data/): Escenarios de datos de gran tamaño que analizan lo que se puede compilar con Azure.

-   [Azure HDInsight SDK](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx): Documentación de referencia de HDinsight SDK.

**Microsoft: Windows y Base de datos SQL**

-   [Página de inicio de Azure](https://www.windowsazure.com/en-us/): Contiene los escenarios, la opción de registro para evaluación gratuita, las herramientas de desarrollo y la documentación que necesita para iniciarse en la compilación de aplicaciones.

-   [Base de datos SQL de Azure](http://msdn.microsoft.com/en-us/library/windowsazure/ee336279.aspx): Documentación de MSDN sobre Base de datos SQL.

-   [Portal de administración de Base de datos SQL](http://msdn.microsoft.com/en-us/library/windowsazure/gg442309.aspx): Herramienta de administración de bases de datos ligera y fácil de usar para administrar el modelo de ejecución Base de datos SQL en la nube.

-   [Adventure Works for SQL Database](http://msftdbprodsamples.codeplex.com/releases/view/37304): Página de descarga de bases de datos de ejemplo para el modelo de ejecución Base de datos SQL.

**Microsoft: Business intelligence**

-   [Connect Excel to HDInsight with Power Query](/en-us/manage/services/hdinsight/connect-excel-with-power-query/): Aprenda a conectar Excel a una cuenta de almacenamiento de Azure que almacene los datos asociados a su clúster HDInsight mediante Microsoft Power Query para Excel.

-   [Connect Excel to HDInsight with the Microsoft Hive ODBC Driver](/en-us/manage/services/hdinsight/connect-excel-with-hive-ODBC/): Aprenda a importar datos desde Azure HDInsight con Microsoft Hive ODBC Driver.

-   [Microsoft BI PowerPivot](http://www.microsoft.com/en-us/bi/PowerPivot.aspx): Descargue y obtenga información acerca de un potente mashup de datos y una herramienta de exploración.

-   [SQL Server 2012 Analysis Services](http://www.microsoft.com/sqlserver/en/us/solutions-technologies/business-intelligence/SQL-Server-2012-analysis-services.aspx): Descargue una copia de evaluación de SQL Server 2012 y aprenda a crear soluciones de análisis completas, de escala empresarial, que proporcionen conocimientos procesables.

-   [SQL Server 2012 Reporting](http://www.microsoft.com/sqlserver/en/us/solutions-technologies/business-intelligence/SQL-Server-2012-reporting-services.aspx): Descargue una copia de evaluación de SQL Server 2012 y aprenda a crear soluciones completas y altamente escalables que permitan a su empresa tomar decisiones en tiempo real.

**Apache Hadoop**:

-   [Apache Hadoop](http://hadoop.apache.org/): Obtenga más información acerca de la biblioteca de software Apache Hadoop, un marco que permite efectuar el procesamiento distribuido de grandes conjuntos de datos en clústeres de equipos.

-   [HDFS](http://hadoop.apache.org/docs/r0.18.1/hdfs_design.html): Obtenga más información acerca de la arquitectura y el diseño del sistema de archivos distribuidos Hadoop (HDFS), el sistema de almacenamiento principal de las aplicaciones de Hadoop.

-   [MapReduce](http://mapreduce.org/): Obtenga más información acerca de este marco de programación para escribir aplicaciones de Hadoop con el fin de procesar enormes cantidades de datos, en paralelo, en grandes clústeres de nodos de proceso.


