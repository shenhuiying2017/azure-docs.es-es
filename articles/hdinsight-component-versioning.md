<properties linkid="manage-services-hdinsight-version" urlDisplayName="HDInsight Hadoop Version" pageTitle="What's new in the cluster versions provided by HDInsight? | Azure" metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure" description="HDInsight supports multiple Hadoop cluster versions deployable at any time. See the Hadoop and HortonWorks Data Platform (HDP) distribution versions supported." services="HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="What's new in the cluster versions provided by HDInsight?" authors="bradsev" />

¿Cuáles son las novedades en las versiones de clústeres proporcionadas por HDInsight?
=====================================================================================

HDInsight de Azure ahora admite Hadoop 2.2 con el clúster de HDinsight versión 3.0 y aprovecha completamente esta plataforma para proporcionar a los clientes una gran variedad de importantes ventajas. De entre ellas, las más destacadas son:

-   Hive: Mejoras exponenciales de los tiempos de respuesta de las consultas de Hive (hasta 40 veces más rápidas) y de la compresión de los datos (hasta el 80 %) mediante el formato Optimized Row Columnar (ORC).

-   YARN: Marco de administración de aplicaciones nuevo, de uso general y distribuido que ha reemplazado al marco Apache Hadoop MapReduce para el procesamiento de datos en los clústeres de Hadoop. De hecho, sirve como sistema operativo de Hadoop y consigue que Hadoop pase de ser una plataforma de datos para el procesamiento por lotes y para un único usuario a una plataforma multiuso que permite el procesamiento por lotes, interactivo, en línea y por secuencia. Este nuevo marco de administración mejora la escalabilidad y el uso de clústeres de acuerdo con criterios como las garantías de capacidad, la legitimidad y los contratos de nivel de servicio.

-   Pig, Sqoop, Qozie, Ambari: Actualizaciones de versión de componentes para el clúster de HDinsight versión 3.0 (HDP 2.0) que proporcionan paridad con el clúster de HDinsight versión 2.1 (HDP 1.3). Consulte las tablas de versiones a continuación para obtener información específica. Observe que Hbase, Mahout y Flume no están incluidos.

**Implementación:** La creación de clústeres de HDInsight 3.0 en Hadoop 2.2 es compatible con el Portal de Azure, el SDK de HDinsight y PowerShell de Azure.

**Disponibilidad global:** Con el lanzamiento de HDInsight de Azure en Hadoop 2.2, Microsoft pone a su disposición HDInsight en todas las regiones principales de Azure a excepción de China. Concretamente, los centros de datos de Europa occidental y el Sudeste asiático ahora están en línea. De este modo se permite que los clientes ubiquen los clústeres en un centro de datos cercano y que esté situado en una zona cuyos requisitos legales sean potencialmente similares.

**Cambios de compatibilidad:** La única sintaxis compatible con los clústeres de HDInsight 3.0 es "wasb://". La antigua sintaxis "asv://" es compatible con los clústeres de HDInsight 2.1 y 1.6, pero no es compatible con los clústeres de HDInsight 3.0 y no será compatible con las versiones posteriores. Es decir, que los trabajos enviados a un clúster de HDInsight 3.0 que usen explícitamente la sintaxis "asv://" tendrán errores. Debería usarse la sintaxis wasb:// en lugar de la anterior. Además, los trabajos enviados a cualquier clúster de HDInsight 3.0 que se hayan creado con una tienda de metadatos existente que contenga referencias explícitas a recursos con la sintaxis asv:// tendrán errores. Estas tiendas de metadatos tendrán que volver a crearse usando wasb:// para recursos de dirección.

Versiones de HDInsight
----------------------

HDInsight es compatible con varias versiones de clústeres de Hadoop que se pueden implementar en cualquier momento. Cada versión aprovisiona una versión específica de la distribución HortonWorks Data Platform (HDP) y un conjunto de componentes que están incluidos en esa distribución.

### Versión de clúster 3.0

HDInsight de Azure ya es compatible con Hadoop 2.2. Se basa en la versión 2.0 de la distribución Hortonworks Data Platform y proporciona a los servicios de Hadoop las versiones de los componentes que se muestran en la siguiente tabla:

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><th data-morhtml="true">Componente</th><th data-morhtml="true">Versi&oacute;n</th></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hadoop</td><td data-morhtml="true">2.2.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hive</td><td data-morhtml="true">0.12.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Pig</td><td data-morhtml="true">0.12</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Sqoop</td><td data-morhtml="true">1.4.4</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Oozie</td><td data-morhtml="true">4.0.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache HCatalog</td><td data-morhtml="true">Fusionado con Hive</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Templeton</td><td data-morhtml="true">Fusionado con Hive</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Ambari</td><td data-morhtml="true">API v1.0</td></tr>
</table>

### Versión de clúster 2.1

La versión de clúster que utiliza [HDInsight de Azure](http://go.microsoft.com/fwlink/?LinkID=285601) de forma predeterminada es la 2.1. Se basa en la versión 1.3.0 de la distribución Hortonworks Data Platform y proporciona a los servicios de Hadoop las versiones de los componentes que se muestran en la siguiente tabla:

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><th data-morhtml="true">Componente</th><th data-morhtml="true">Versi&oacute;n</th></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hadoop</td><td data-morhtml="true">1.2.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hive</td><td data-morhtml="true">0.11.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Pig</td><td data-morhtml="true">0.11</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Sqoop</td><td data-morhtml="true">1.4.3</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Oozie</td><td data-morhtml="true">3.2.2</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache HCatalog</td><td data-morhtml="true">Fusionado con Hive</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Templeton</td><td data-morhtml="true">Fusionado con Hive</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Ambari</td><td data-morhtml="true">API v1.0</td></tr>
</table>

### Versión de clúster 1.6

El clúster de [HDInsight de Azure](http://go.microsoft.com/fwlink/?LinkID=285601) versión 1.6 también está disponible. Se basa en la versión 1.1.0 de la distribución Hortonworks Data Platform y proporciona a los servicios de Hadoop las versiones de los componentes que se muestran en la siguiente tabla:

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><th data-morhtml="true">Componente</th><th data-morhtml="true">Versi&oacute;n</th></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hadoop</td><td data-morhtml="true">1.0.3</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hive</td><td data-morhtml="true">0.9.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Pig</td><td data-morhtml="true">0.9.3</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Sqoop</td><td data-morhtml="true">1.4.2</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Oozie</td><td data-morhtml="true">3.2.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache HCatalog</td><td data-morhtml="true">0.4.1</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Templeton</td><td data-morhtml="true">0.1.4</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Controlador JDBC para SQL Server, versi&oacute;n</td><td data-morhtml="true">3.0</td></tr>
</table>

### Selección de una versión al aprovisionar un clúster de HDInsight

Al crear un clúster a través de los cmdlets de PowerShell para HDInsight o del SDK .NET de HDInsight, puede elegir la versión del clúster de Hadoop para HDInsight mediante el parámetro "Versión".

Si utiliza la opción **Creación rápida**, obtendrá la versión 2.1 del clúster de Hadoop para HDInsight de forma predeterminada. Si utiliza la opción **Creación personalizada** del Portal de Azure, puede elegir la versión del clúster que implementará desde el menú desplegable **Versión de HDInsight** en la página **Detalles del clúster**. La versión 3.0 del clúster de Hadoop para HDInsight solo está disponible como opción en el asistente de **Creación personalizada**.

![Pantalla de versión de HDInsight](./media/hdinsight-component-versioning/hdi-versioning-version-screen.png)

Versiones compatibles
---------------------

En la siguiente tabla se enumeran las versiones de HDInsight que está disponibles actualmente, las versiones correspondientes de la distribución Hortonworks Data Platform (HDP) que utilizan y sus fechas de lanzamiento. Si se conoce la fecha de degradación, también se proporciona.

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><th data-morhtml="true">Versi&oacute;n de HDInsight</th><th data-morhtml="true">Versi&oacute;n de HDP</th><th data-morhtml="true">Fecha de lanzamiento</th></tr>
<tr data-morhtml="true"><td data-morhtml="true">HDI 3.0</td><td data-morhtml="true">HDP 2.0</td><td data-morhtml="true">02/11/2014</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">HDI 2.1</td><td data-morhtml="true">HDP 1.3</td><td data-morhtml="true">10/28/2013</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">HDI 1.6</td><td data-morhtml="true">HDP 1.1</td><td data-morhtml="true">10/28/2013</td></tr>
</table>

### El contrato de nivel de servicio (SLA) para las versiones de clúster de HDInsight

El SLA se define en términos de "plazo de soporte técnico". Un plazo de soporte técnico se refiere al período durante el cual la versión del clúster de HDInsight puede recibir soporte técnico por parte del Servicio de soporte al cliente de Microsoft. Un clúster de HDInsight está fuera del plazo de soporte técnico si su versión cuenta con una fecha de expiración de soporte técnico anterior a la fecha actual. En la tabla anterior se puede consultar una lista de las versiones de clúster de HDInsight con soporte técnico. La fecha de expiración de soporte técnico para una versión de HDInsight determinada (indicada como versión X) se calcula como la posterior de:

-   Fórmula 1: Agregue 180 días a la fecha en la que se lanzó la versión X del clúster de HDInsight.
-   Fórmula 2: Agregue 90 días a la fecha en la que la versión X+1 (versión subsiguiente después de X) del clúster de HDInsight se puso a su disposición en el Portal de administración de Azure.

> [WACOM.NOTE] Tanto HDInsight 2.1 como 3.0 utilizan la versión de 64 bits de Windows 2008 R2 SP1 con .NET Framework 4.0.

**Notas e información adicionales acerca de las versiones**

-   El controlador JDBC para SQL Server se utiliza de forma interna mediante HDInsight y no se utiliza para realizar operaciones externas. Si desea conectarse a HDInsight mediante ODBC, utilice el controlador ODBC para Microsoft Hive. Para obtener más información acerca del uso de ODBC para Hive, consulte [Connect Excel to HDInsight with the Microsoft Hive ODBC Driver](/es-es/documentation/articles/hdinsight-connect-excel-hive-ODBC-driver).

-   La versión 3.0 del clúster de HDInsight utiliza una distribución de Hadoop basada en la distribución [Hortonworks Data Platform 2.0](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html).

-   La versión 2.1 del clúster de HDInsight utiliza una distribución de Hadoop basada en la distribución [Hortonworks Data Platform 1,3](http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html). Este es el clúster de Hadoop predeterminado que se crea al utilizar el portal de HDInsight de Azure.

-   La versión 1.6 del clúster de HDInsight utiliza una distribución de Hadoop basada en la distribución [Hortonworks Data Platform 1.1](http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html).

-   Las versiones de los componentes asociados a las versiones de clúster de HDInsight pueden cambiar en futuras actualizaciones de HDInsight. Una forma de determinar los componentes disponibles y comprobar las versiones que se están utilizando para un clúster es la API REST de Ambari. El comando GetComponentInformation se puede utilizar para recuperar información acerca de un componente del servicio. Para obtener más información, consulte la [documentación de Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md) (en inglés). Otra forma de obtener esta información es iniciar sesión en un clúster mediante un escritorio remoto y examinar directamente el contenido del directorio "C:\\apps\\dist".


