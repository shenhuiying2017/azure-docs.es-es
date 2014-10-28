<properties title="HDInsight Release Notes" pageTitle="HDInsight Release Notes | Azure" description="HDInsight release notes." metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure, release notes" services="HDInsight" solutions="" documentationCenter="" editor="cgronlun" manager="paulettm"  authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev"></tags>

# Notas de la versión de Microsoft HDInsight

## Notas de la versión del 21/08/2014

-   Vamos a agregar la siguiente nueva configuración de WebHCat (HIVE-7155) que establece el límite de memoria predeterminado para un trabajo de controlador Templeton en un 1 GB (el valor predeterminado anterior era 512 MB):

    -   templeton.mapper.memory.mb (=1024)
    -   Este cambio soluciona el siguiente error con el que algunas consultas de Hive se han encontrado debido a límites bajos de memoria: "el contenedor se ejecuta por encima de los límites de memoria física".
    -   Para volver a los valores predeterminados anteriores, puede establecer este valor de configuración en 512 a través del SDK de PowerShell en el momento de creación del clúster mediante el siguiente comando:

        Add-AzureHDInsightConfigValues -Core @{"templeton.mapper.memory.mb"="512";}

-   El nombre de host del rol zookeeper se ha cambiado por zookeeper. Esto afecta a la resolución de nombres dentro del clúster, pero no a las API REST externas. Si tiene componentes que usan el nombre de host zookeepernode, deberá actualizarlos para que usen en su lugar el nuevo nombre. Los nuevos nombres de los tres nodos de zookeeper son:

    -   zookeeper0
    -   zookeeper1
    -   zookeeper2
-   Se actualiza la matriz de compatibilidad de versiones de HBase. Solo se admite la versión HDInsight 3.1 (versión 0.98 de HBase) para las cargas de trabajo de HBase de producción. No se permitirá que la versión 3.0 que estaba disponible para vista previa siga avanzando. Durante el período de transición, los clientes pueden seguir creando clústeres de la versión 3.0.

## Notas de la versión del 28/07/2014

-   **HDInsight disponible en nuevas regiones**: Con esta versión, hemos ampliado la presencia geográfica de HDInsight a tres nuevas regiones. Los clientes de HDInsight pueden crear ahora clústeres en estas regiones.

    -   Asia oriental
    -   Centro-Norte de EE. UU
    -   Centro-Sur de EE. UU
-   Con esta versión, HDInsight v1.6 (HDP1.1, Hadoop 1.0.3) y HDInsight v2.1 (HDP1.3, Hadoop 1.2) se van a eliminar del Portal de administración de Azure. Sin embargo, puede seguir creando clústeres de Hadoop para estas versiones mediante cmdlets de HDInsight PowerShell ([New-AzureHDInsightCluster][New-AzureHDInsightCluster]) o por medio del [SDK de HDInsight][SDK de HDInsight]. Consulte la página de [versiones de componentes de HDInsight][versiones de componentes de HDInsight] para obtener más información.
-   Hortonworks Data Platform (HDP) cambia en esta versión:

| HDP               | Cambios                                                      |
|-------------------|--------------------------------------------------------------|
| HDP 1.3 / HDI 2.1 | Sin cambios                                                  |
| HDP 2.0 / HDI 3.0 | Sin cambios                                                  |
| HDP 2.1 / HDI 3.1 | zookeeper: ['3.4.5.2.1.3.0-1948'] -\> ['3.4.5.2.1.3.2-0002'] |

## Notas de la versión del 24/06/2014

Esta versión contiene varias nuevas mejoras del servicio HDInsight:

-   **Disponibilidad de HDP 2.1**: HDInsight 3.1, que contiene HDP 2.1, está ahora disponible con carácter general y es la versión predeterminada de los nuevos clústeres.
-   **HBase: mejoras del Portal de administración de Azure**: Vamos a hacer que los clústeres estén disponibles en vista previa. Ahora puede crear clústeres de HBase desde el portal con tres clics.

![][]

Con HBase, puede compilar diversas cargas de trabajo en tiempo real en HDInsight, desde sitios web interactivos que trabajan con grandes conjuntos de datos hasta servicios que almacenan datos de sensores y telemetría de millones de extremos. El paso siguiente sería analizar los datos de estas cargas de trabajo con trabajos de Hadoop. Esto es posible inmediatamente en HDInsight gracias a las experiencias proporcionadas como PowerShell y el panel de clúster de Hive.

### Apache Mahout ahora preinstalado en HDInsight 3.1

[Mahout][Mahout] se encuentra preinstalado en los clústeres de Hadoop de HDInsight 3.1. Por tanto, puede ejecutar trabajos de Mahout sin necesidad de configuración adicional del clúster. Por ejemplo, puede iniciar una sesión remota en un clúster de Hadoop mediante el Protocolo de escritorio remoto (RDP) y ejecutar el comando Hello world de Mahout sin pasos adicionales:

        mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

        mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

Para obtener una descripción completa de este procedimiento, vea la documentación del [ejemplo de Breiman][ejemplo de Breiman] en el sitio web de Apache Mahout.

### Las consultas de Hive pueden usar Tez en HDinsight 3.1

Hive 0.13 se encuentra ahora disponible en HDInsight 3.1 y es capaz de ejecutar consultas mediante Tez, que se puede aprovechar para conseguir mejoras sustanciales del rendimiento.
Tez no está habilitado de forma predeterminada para consultas de Hive. Para usarlo, debe suscribirse. Puede habilitar Tez ejecutando el siguiente snippet de código:

        set hive.execution.engine=tez;
        select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

Hortonworks ha publicado un desglose detallado de las mejoras en el rendimiento de las consultas de Hive con Tez proporcionadas en comparativas estándar. Para obtener detalles, consulte [Comparativa de Apache Hive 13 para Enterprise Hadoop][Comparativa de Apache Hive 13 para Enterprise Hadoop].

Para obtener más información sobre el uso de Hive con Tez, consulte la [página de wiki Hive en Tez][página de wiki Hive en Tez].

### Disponibilidad global

Con la versión de HDInsight de Azure en Hadoop 2.2, Microsoft ha conseguido que HDInsight esté disponible en las principales regiones geográficas de Azure. Concretamente, los centros de datos de Europa occidental y el Sudeste asiático ahora están en línea. De este modo se permite que los clientes ubiquen los clústeres en un centro de datos cercano y que esté situado en una zona cuyos requisitos legales sean potencialmente similares.

### Cambios bruscos

**Sintaxis de prefijo**:
La única sintaxis compatible con los clústeres de HDInsight 3.0 y 3.1 es "wasb://". La antigua sintaxis "asv://" es compatible con los clústeres de HDInsight 2.1 y 1.6, pero no es compatible con los clústeres de HDInsight 3.0 o versiones posteriores. Es decir, que los trabajos enviados a un clúster de HDInsight cuya versión sea 3.0 o 3.1 y que usen explícitamente la sintaxis "asv://" tendrá errores. Debería usarse la sintaxis wasb:// en lugar de la anterior. Además, los trabajos enviados a cualquier clúster de HDInsight 3.0 o 3.1 que se hayan creado con una tienda de metadatos existente que contenga referencias explícitas a recursos con la sintaxis asv:// tendrán errores. Estas tiendas de metadatos tendrán que volver a crearse usando wasb:// para recursos de dirección.

**Puertos**: Los puertos usados por el servicio HDInsight han cambiado. Los números de puerto que se han estado usando estaban dentro del intervalo de puertos transitorio del sistema operativo Windows. Los puertos se asignan automáticamente desde un intervalo transitorio predefinido en comunicaciones basadas en protocolo de Internet de corta duración. El nuevo conjunto de números de puerto de servicio de Hortonworks Data Platform (HDP) permitidos están ahora fuera de este intervalo para evitar conflictos que podrían surgir con los puertos que usan los servicios que se ejecutan en el nodo principal. Los nuevos números de puerto no deben provocar ningún cambio brusco. Los números usados ahora son los siguientes:

**HDInsight 1.6 (HDP 1.1)**

<table border="1">

<tr>
<th>
Nombre

</th>
<th>
Valor

</th>
</tr>

<tr>
<td>
dfs.http.address

</td>
<td>
namenodehost:30070

</td>
</tr>

<tr>
<td>
dfs.datanode.address

</td>
<td>
0.0.0.0:30010

</td>
</tr>

<tr>
<td>
dfs.datanode.http.address

</td>
<td>
0.0.0.0:30075

</td>
</tr>

<tr>
<td>
dfs.datanode.ipc.address

</td>
<td>
0.0.0.0:30020

</td>
</tr>

<tr>
<td>
dfs.secondary.http.address

</td>
<td>
0.0.0.0:30090

</td>
</tr>

<tr>
<td>
mapred.job.tracker.http.address

</td>
<td>
jobtrackerhost:30030

</td>
</tr>

<tr>
<td>
mapred.task.tracker.http.address

</td>
<td>
0.0.0.0:30060

</td>
</tr>

<tr>
<td>
mapreduce.history.server.http.address

</td>
<td>
0.0.0.0:31111

</td>
</tr>

<tr>
<td>
templeton.port

</td>
<td>
30111

</td>
</tr>

</table>

</p>
**HDInsight 3.0 y 3.1 (HDP 2.0 y 2.1)**

<table border="1">

<tr>
<th>
Nombre

</th>
<th>
Valor

</th>
</tr>

<tr>
<td>
dfs.namenode.http-address

</td>
<td>
namenodehost:30070

</td>
</tr>

<tr>
<td>
dfs.namenode.https-address

</td>
<td>
headnodehost:30470

</td>
</tr>

<tr>
<td>
dfs.datanode.address

</td>
<td>
0.0.0.0:30010

</td>
</tr>

<tr>
<td>
dfs.datanode.http.address

</td>
<td>
0.0.0.0:30075

</td>
</tr>

<tr>
<td>
dfs.datanode.ipc.address

</td>
<td>
0.0.0.0:30020

</td>
</tr>

<tr>
<td>
dfs.namenode.secondary.http-address

</td>
<td>
0.0.0.0:30090

</td>
</tr>

<tr>
<td>
yarn.nodemanager.webapp.address

</td>
<td>
0.0.0.0:30060

</td>
</tr>

<tr>
<td>
templeton.port

</td>
<td>
30111

</td>
</tr>

</table>

</p>
### Dependencias

Las siguientes dependencias se han agregado en HDInsight 3.x (HDP2.x):

-   guice-servlet
-   optiq-core
-   javax.inject
-   activation
-   jsr305
-   geronimo-jaspic\_1.0\_spec
-   jul-to-slf4j
-   java-xmlbuilder
-   ant
-   commons-compiler
-   jdo-api
-   commons-math3
-   paranamer
-   jaxb-impl
-   stringtemplate
-   eigenbase-xom
-   jersey-servlet
-   commons-exec
-   jaxb-api
-   jetty-all-server
-   janino
-   xercesImpl
-   optiq-avatica
-   jta
-   eigenbase-properties
-   groovy-all
-   hamcrest-core
-   mail
-   linq4j
-   jpam
-   jersey-client
-   aopalliance
-   geronimo-annotation\_1.0\_spec
-   ant-launcher
-   jersey-guice
-   xml-apis
-   stax-api
-   asm-commons
-   asm-tree
-   wadl
-   geronimo-jta\_1.1\_spec
-   guice
-   leveldbjni-all
-   velocity
-   jettison
-   snappy-java
-   jetty-all
-   commons-dbcp

Las siguientes dependencias ya no existen en HDInsight 3.x (HDP2.x):

-   jdeb
-   kfs
-   sqlline
-   ivy
-   aspectjrt
-   json
-   core
-   jdo2-api
-   avro-mapred
-   datanucleus-enhancer
-   jsp
-   commons-logging-api
-   commons-math
-   JavaEWAH
-   aspectjtools
-   javolution
-   hdfsproxy
-   hbase
-   snappy

### Cambios de versión

Se han realizado los siguientes cambios de versión entre HDInsight 2.x (HDP1.x) y HDInsight 3.x (HDP2.x):

-   metrics-core: ['2.1.2'] -\> ['3.0.0']
-   derbynet: ['10.4.2.0'] -\> ['10.10.1.1']
-   datanucleus: ['rdbms-3.0.8'] -\> ['rdbms-3.2.9']
-   jasper-compiler: ['5.5.12'] -\> ['5.5.23']
-   log4j: ['1.2.15', '1.2.16'] -\> ['1.2.16', '1.2.17']
-   derbyclient: ['10.4.2.0'] -\> ['10.10.1.1']
-   httpcore: ['4.2.4'] -\> ['4.2.5']
-   hsqldb: ['1.8.0.10'] -\> ['2.0.0']
-   jets3t: ['0.6.1'] -\> ['0.9.0']
-   protobuf-java: ['2.4.1'] -\> ['2.5.0']
-   derby: ['10.4.2.0'] -\> ['10.10.1.1']
-   jasper: ['runtime-5.5.12'] -\> ['runtime-5.5.23']
-   commons-daemon: ['1.0.1'] -\> ['1.0.13']
-   datanucleus-core: ['3.0.9'] -\> ['3.2.10']
-   datanucleus-api-jdo: ['3.0.7'] -\> ['3.2.6']
-   zookeeper: ['3.4.5.1.3.9.0-01320'] -\> ['3.4.5.2.1.3.0-1948']
-   bonecp: ['0.7.1.RELEASE'] -\> ['0.8.0.RELEASE']

### Controladores

El controlador JDBC para SQL Server se utiliza de forma interna mediante HDInsight y no se utiliza para realizar operaciones externas. Si desea conectarse a HDInsight mediante ODBC, utilice el controlador ODBC para Microsoft Hive. Para obtener más información acerca del uso de ODBC para Hive, consulte Connect Excel to HDInsight with the Microsoft Hive ODBC Driver [connect-excel-with-hive-ODBC].

### Corrección de errores

Con esta versión, hemos actualizado las siguientes versiones de HDInsight (Hortonworks Data Platform - HDP) con varias correcciones de errores:

-   HDInsight 2.1 (HDP 1.3)
-   HDInsight 3.0 (HDP 2.0)
-   HDInsight 3.1 (HDP 2.1)

## Notas de la versión de Hortonworks

Las notas de la versión de los HDP que se usan en las versiones del clúster de HDInsight están disponibles en las siguientes ubicaciones.

-   El clúster de HDInsight versión 3.1 utiliza una distribución de Hadoop basada en [HortonWorks Data Platform 2.1][HortonWorks Data Platform 2.1]. Este es el clúster de Hadoop predeterminado que se crea cuando se usa el portal de HDInsight de Azure.

-   La versión 3,0 del clúster de HDInsight utiliza una distribución de Hadoop basada en la distribución [Hortonworks Data Platform 2,0][Hortonworks Data Platform 2,0].

-   La versión 2,1 del clúster de HDInsight utiliza una distribución de Hadoop basada en la distribución [Hortonworks Data Platform 1,3][Hortonworks Data Platform 1,3].

-   La versión 1.6 del clúster de HDInsight utiliza una distribución de Hadoop basada en la distribución [Hortonworks Data Platform 1.1][Hortonworks Data Platform 1.1].

  [New-AzureHDInsightCluster]: http://msdn.microsoft.com/es-es/library/dn593744.aspx
  [SDK de HDInsight]: http://msdn.microsoft.com/es-es/library/azure/dn469975.aspx
  [versiones de componentes de HDInsight]: http://azure.microsoft.com/es-es/documentation/articles/hdinsight-component-versioning/
  []: http://i.imgur.com/cmOl5fM.png
  [Mahout]: http://hortonworks.com/hadoop/mahout/
  [ejemplo de Breiman]: https://mahout.apache.org/users/classification/breiman-example.html
  [Comparativa de Apache Hive 13 para Enterprise Hadoop]: http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/
  [página de wiki Hive en Tez]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
  [HortonWorks Data Platform 2.1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html
  [Hortonworks Data Platform 2,0]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html
  [Hortonworks Data Platform 1,3]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html
  [Hortonworks Data Platform 1.1]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html
