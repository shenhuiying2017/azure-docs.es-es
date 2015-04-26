<properties 
	pageTitle="Notas de la versión de HDInsight | Azure" 
	description="Notas de la versión de HDInsight." 
	services="hdinsight" 
	documentationCenter="" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="bradsev"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/30/2015" 
	ms.author="bradsev"/>


#Notas de la versión de Microsoft HDInsight

## Notas de la versión del 29/01/2015 de HDinsight ##

Los números de versión completos de los clústeres de HDInsight implementados con esta versión son:

* HDInsight 	2.1.10.455.1309616	(HDP 1.3.9.0-01351: sin cambios)
* HDInsight 	3.0.6.455.1309616	(HDP 2.0.9.0-2097:  sin cambios)
* HDInsight 	3.1.2.455.1309616	(HDP 2.1.9.0-2196:  sin cambios)
* SDK			N/D

Esta versión contiene la siguiente actualización.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Área afectada 
(por ejemplo, servicio, componentes de sistemas operativos, SDK, PS, AUX)</p></th>
<th>Tipo de clúster afectado(por ejemplo, Hadoop, HBase, Storm, todos)</th>
<th>JIRA (si es aplicable)</th>
</tr>


<tr>

<td> Corrección de errores</td>
<td>Hemos realizado algunas correcciones de errores importantes que mejoran la confiabilidad de los clústeres de HDInsight durante las actualizaciones de Azure.</td>
<td>Servicios</td>
<td></td>
<td>N/D</td>
</tr>



</table>
<br>

## Notas de la versión del 05/01/2015 de HDinsight ##

Los números de versión completos de los clústeres de HDInsight implementados con esta versión son:

* HDInsight 	2.1.10.420.1246118	(HDP 1.3.9.0-01351: sin cambios)
* HDInsight 	3.0.6.420.1246118	(HDP 2.0.9.0-2097: sin cambios)
* HDInsight 	3.1.2.420.1246118	(HDP 2.1.9.0-2196: sin cambios)


Esta versión contiene la siguiente actualización.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Componente</th>
<th>Tipo de clúster</th>
<th>JIRA (si es aplicable)</th>
</tr>


<tr>
<td>Muestras de análisis de tendencias de Twitter y recomendaciones de películas basadas en Mahout</td>
<td><p>En esta versión, la consola de consultas de HDInsight tiene dos ejemplos adicionales:</p>

<p><b>Análisis de tendencias de Twitter</b><br>
Las API públicas proporcionadas por sitios como Twitter constituyen un origen de datos muy útil para analizar y comprender las tendencias populares. En este tutorial aprenderá a usar Hive para obtener una lista de los usuarios de Twitter que hayan enviado más tweets que contengan una palabra determinada. </p>

<p><b>Recomendación de películas de Mahout</b><br>
Apache Mahout es una biblioteca de aprendizaje automático de Apache Hadoop. Mahout contiene algoritmos para el procesamiento de datos, como filtrado, clasificación y agrupación en clústeres. En este tutorial usará un motor de recomendaciones para generar recomendaciones de películas basadas en películas que sus amigos han visto.</p></td>
<td>Consola de consultas</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Cambiar al valor predeterminado de hive config hive.auto.convert.join.noconditionaltask.size</td>
<td><p>Esta configuración de tamaño se aplica a las combinaciones de asignaciones convertidas automáticamente. El valor representa la suma de los tamaños de las tablas que se pueden convertir en hashmaps que caben en la memoria. En versiones anteriores, este valor aumentó a 128 MB respecto al valor predeterminado de 10 MB . Sin embargo, el nuevo valor de 128 MB provocaba que los trabajos dieran errores debido a la falta de memoria. En esta versión el valor predeterminado se revierte a 10 MB. Sin embargo, los clientes aún pueden elegir invalidar este valor durante la creación del clúster en función de los tamaños de las tablas y las consultas. Para obtener más información sobre esta configuración y cómo invalidarla, consulte cómo <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.0.2/ds_Hive/optimize-joins.html#JoinOptimization-OptimizeAutoJoinConversion" target="_blank">optimizar la conversión de combinaciones automáticas</a> en la documentación de Hortonworks. </p></td>
<td>Hive</td>
<td>Hadoop, Hbase</td>
<td>N/D</td>
</tr>

</table>
<br>

## Notas de la versión del 23/12/2014 de HDinsight ##

Los números de versión completos de los clústeres de HDInsight implementados con esta versión son:

* HDInsight 	2.1.10.420.1246783	(versión de HDP sin cambios)
* HDInsight 	3.0.6.420.1246783	(versión de HDP sin cambios)
* HDInsight 	3.1.1.420.1246783	(versión de HDP sin cambios)

Esta versión contiene la siguiente actualización.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Componente</th>
<th>Tipo de clúster</th>
<th>JIRA (si es aplicable)</th>
</tr>


<tr>
<td>Errores intermitentes de creación de clústeres debido a una carga excesiva</td>
<td><p>Un algoritmo mejorado para descargar paquetes HDP durante la creación del clúster permite un control más sólido de los errores debidos a una carga excesiva. Se esperan algunas mejoras más en esta área que posibilitarán un mejor escalado respecto al aumento de carga relacionado con las creaciones de clústeres.</p></td>
<td>Servicio</td>
<td>Hadoop, Hbase, Storm</td>
<td>N/D</td>
</tr>



</table>
<br>

## Notas de la versión del 18/12/2014 de HDinsight ##

Esta versión contiene las siguientes actualizaciones de componentes.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Componente</th>
<th>Tipo de clúster</th>
<th>JIRA (si es aplicable)</th>
</tr>

<tr>
<td><a href = "http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-customize-cluster/" target="_blank">Personalización de clústeres de disponibilidad general</a></td>
<td><p>La personalización ofrece la capacidad de personalizar los clústeres de HDInsight de Azure con proyectos disponibles del ecosistema de Apache Hadoop. Con esta nueva característica, ahora puede experimentar con los proyectos de Hadoop e implementarlos en HDInsight de Azure. Se habilita a través de la característica <b>Acción de script</b>, que permite la modificación de los clústeres de Hadoop de maneras arbitrarias mediante scripts personalizados. Esta personalización está disponible en todo tipo de clústeres de HDInsight, incluidos Hadoop, HBase y Storm. Para demostrar la eficacia de esta capacidad, hemos documentado el proceso para instalar los conocidos módulos de <a href = "http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-spark-install/" target="_blank">Spark</a>, <a href = "http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-r-scripts/" target="_blank">R</a>, <a href = "http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-solr-install/" target="_blank">Solr</a>y <a href = "http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-giraph-install/" target="_blank">Giraph.</a>   Esta versión también agrega la posibilidad de que los clientes especifiquen su acción de script personalizado mediante el Portal de administración de Azure, proporciona directrices y procedimientos recomendados sobre cómo crear acciones de scripts personalizados mediante métodos auxiliares y ofrece instrucciones sobre cómo probar la acción de script. </p></td>
<td>Característica de disponibilidad general</td>
<td>Todo</td>
<td>N/D</td>
</tr>


</table>
<br>

## Notas de la versión del 05/12/2014 de HDinsight ##

Los números de versión completos de los clústeres de HDInsight implementados con esta versión son:

* HDInsight 	2.1.9.406.1221105	(HDP 1.3.9.0-01351)
* HDInsight 	3.0.5.406.1221105	(HDP 2.0.9.0-2097)
* HDInsight 	3.1.1.406.1221105	(HDP 2.1.9.0-2196)
* HDInsight SDK N/A

Esta versión contiene las siguientes actualizaciones de componentes.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Componente</th>
<th>Tipo de clúster</th>
<th>JIRA (si es aplicable)</th>
</tr>

<tr>
<td>Corrección de errores: Error intermitente al agregar un gran número de particiones a una tabla en un DDL de Hive. </td>
<td><p>Si hay un error de conexión intermitente con la base de datos de la tienda de metadatos de Hive al agregar muchas particiones a una tabla de Hive, el DDL de Hive puede dar error. Si este error se produce, aparecerá la siguiente instrucción en el registro de errores de Hive: </p><p>"ERROR [main]: ql.Driver (SessionState.java:printError(547)) - FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask. MetaException(message:java.lang.RuntimeException: commitTransaction was called but openTransactionCalls = 0. This probably indicates that there are unbalanced calls to openTransaction/commitTransaction)"</p></td>
<td>Hive</td>
<td>Hadoop, Hbase</td>
<td>HIVE-482 (This is an internal JIRA, so it cannot be quoted externally. Noted here for reference.)</td>
</tr>

<tr>
<td>Corrección de errores: Bloqueo ocasional en la consulta a HDInsight  Consola de consultas</td>
<td>Cuando esto sucede, se puede ver la siguiente instrucción en el registro de WebHCat para el trabajo de selector de WebHCat: <p>"org.apache.hive.hcatalog.templeton.CatchallExceptionMapper | org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.yarn.exceptions.YarnRuntimeException): Could not load history file {wasb url to the history file}"</p></td>
<td>WebHCat</td>
<td>Hadoop</td>
<td>HIVE-482 (This is an internal JIRA, so it cannot be quoted externally. Noted here for reference.)</td>
</tr>

<tr>
<td>Corrección de errores: Subida ocasional de latencia en consultas de Hbase</td>
<td>Si esto sucediera, los usuarios advertirían una subida ocasional de 3 segundos en la latencia de las consultas de Hbase. </td>
<td>Puerta de enlace del clúster de HDInsight</td>
<td>HBase</td>
<td>N/D</td>
</tr>

<tr>
<td>Cambios en el nombre de archivo jar de HDP</td>
<td>En la versión 3.0 del clúster de HDI, hay un par de cambios en los archivos jar internos instalados por HDP. jetty-6.1.26.jar
 se ha sustituido por jetty-6.1.26.hwx.jar. jetty-util-6.1.26.jar se ha sustituido por jetty-util-6.1.26.hwx.jar. Estos cambios se aplican a los proyectos de Hadoop, Mahout, WebHCat y Oozie.**</td>
<td>Hadoop, Mahout, WebHCat, Oozie</td>
<td>Hadoop, HBase</td>
<td>N/D</td>
</tr>

</table>
<br>


## Notas de la versión del 21/11/2014 de HDinsight ##

Los números de versión completos de los clústeres de HDInsight implementados con esta versión son:

* HDInsight 2.1.9.382.1169709 (sin cambios desde el 14/11/2014)
* HDInsight 3.0.5.382.1169709 (sin cambios desde el 14/11/2014)
* HDInsight 3.1.1.382.1169709 (sin cambios desde el 14/11/2014)
* HDINsight SDK 1.4.0

Esta versión contiene las siguientes actualizaciones de componentes.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Componente</th>
<th>Tipo de clúster</th>
<th>JIRA (si es aplicable)</th>
</tr>

<tr>
<td>Acceso a los registros de aplicación</td>
<td>Posibilidad de enumerar mediante programación las aplicaciones que se han ejecutado en los clústeres y descargar registros específicos de aplicaciones relevantes o de contenedor para ayudar a depurar aplicaciones problemáticas.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Posibilidad de especificar el nombre de la región en IHdInsightClient.DeleteCluster </td>
<td>El SDK de HDInsight de Azure ofrece ahora la posibilidad de especificar un nombre de región cuando se usa **DeleteCluster**. Esto ayuda a desbloquear a los clientes que tenían dos recursos con el mismo nombre en diferentes regiones y no habían podido eliminar uno de ellos.</td>
<td>SDK</td>
<td>Todo</td>
<td>N/D</td>
</tr>

<tr>
<td>ClusterDetails.DeploymentId</td>
<td>El objeto **ClusterDetails** devuelve ahora un campo **DeploymentID** que representa un identificador único del clúster. Este identificador se garantiza que es único entre intentos de creación de clústeres con el mismo nombre.</td>
<td>SDK</td>
<td>Todo</td>
<td>N/D</td>
</tr>
</table>
<br>

## Notas de la versión del 14/11/2014 de HDinsight ##

Los números de versión completos de los clústeres de HDInsight implementados con esta versión son:

* HDInsight 2.1.9.382.1169709
* HDInsight 3.0.5.382.1169709
* HDInsight 3.1.1.382.1169709

Esta versión contiene las nuevas características, actualizaciones de componentes y correcciones de errores siguientes.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Componente</th>
<th>Tipo de clúster</th>
<th>JIRA (si es aplicable)</th>
</tr>

<tr>
<td>Acción de script (vista previa)</td>
<td>Vista previa de la característica de personalización del clúster que permite la modificación de los clústeres de Hadoop de maneras arbitrarias mediante scripts personalizados. Con esta nueva característica, los usuarios pueden experimentar con los proyectos disponibles en el ecosistema Hadoop de Apache e implementarlos en clústeres de HDInsight de Azure. Esta característica de personalización está disponible en todo tipo de clústeres de HDInsight, como Hadoop, HBase y Storm.</td>
<td>Nueva característica</td>
<td>Todo</td>
<td>N/D</td>
</tr>

<tr>
<td>Trabajos precompilados para sitios web de Azure y análisis de los registros de almacenamiento</td>
<td>La Consola de consultas de HDInsight presenta una galería de introducción que admite soluciones que funcionan en sus datos o en datos de ejemplo.
<p>Soluciones que funcionan en sus datos:<br>
Hemos creado trabajos para algunos de los escenarios más comunes de análisis de datos con el fin de proporcionar un punto de partida para la creación de sus propias soluciones. Puede usar sus propios datos con el trabajo para ver cómo funciona. Luego, cuando esté preparado, use lo que ha aprendido para crear su propia solución modelada después del trabajo precompilado.</p>
<p>Soluciones que funcionan en datos de ejemplo:<br>
Aprenda a trabajar con HDInsight recorriendo algunos de los escenarios básicos como análisis de registros web y datos de sensor. NO solo aprenderá a usar HDInsight para analizar tales datos, sino que puede conectar otras aplicaciones y servicios a estos datos. La visualización de los datos mediante la conexión a Microsoft Excel proporciona un ejemplo del poder de este enfoque.</p></td>
<td>Consola de consultas</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Corrección de pérdida de memoria en Templeton</td>
<td>Se ha solucionado un problema de pérdida de memoria en Templeton que afectaba a los clientes con clústeres de larga ejecución o que enviaban 100s de solicitudes de trabajos por segundo. El problema se manifestaba como errores 5xx de Templeton y la solución temporal pasaba por reiniciar el servicio. Esta solución temporal ya no es necesaria.</td>
<td>Templeton</td>
<td>Todo</td>
<td>https://issues.apache.org/jira/browse/HADOOP-11248</td>
</tr>
</table>
<br>


Nota: para demostrar las nuevas capacidades que están disponibles gracias a la personalización del clúster, se han documentado los procedimientos que usan acciones de script para instalar módulos de Spark y R en un clúster. Para obtener más información, consulte:

* [Instalación y uso de Spark 1.0 en clústeres de HDInsight][hdinsight-install-spark]
* [Instalación y uso de R en clústeres de Hadoop para HDInsight][hdinsight-r-scripts]




## Notas de la versión del 07/11/2014 de HDinsight ##

Los números de versión completos de los clústeres de HDInsight implementados con esta versión son:

* HDInsight 2.1	2.1.9.374.1153876
* HDInsight 3.0	3.0.5.374.1153876
* HDInsight 3.1	3.1.1.374.1153876

Esta versión contiene las siguientes actualizaciones de componentes.

<table border="1">
<tr>
<th>Título</th>
<th>Descripción</th>
<th>Componente</th>
<th>Tipo de clúster</th>
<th>JIRA (si es aplicable)</th>
</tr>

<tr>
<td>HDP 2.1.7</td>
<td>Esta versión se basa en Hortonworks Data Platform (HDP) 2.1.7. Las notas de la versión de HDP 2.1.7 están disponibles en el sitio de Hortonworks en http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html.</td>
<td>HDP</td>
<td>Todo</td>
<td>N/D</td>
</tr>

<tr>
<td>Servidor de escala de tiempo de YARN</td>
<td>El servidor de escala de tiempo de YARN (conocido también como servidor genérico de historia de aplicaciones) se ha habilitado de forma predeterminada. El servidor de escala de tiempo proporciona información genérica sobre aplicaciones completadas, como el Id. de aplicación, el nombre de la aplicación, el estado de la aplicación,la hora de envío de la aplicación y la hora de finalización de la aplicación. <p>Esta información de la aplicación se puede recuperar del nodo principal mediante el acceso al URI http://headnodehost:8188 o la ejecución del comando de YARN: yarn application -list -appStates ALL.</p> 
<p>Esta información se puede recuperar también de forma remota a través de la API de REST en https://{ClusterDnsName}. azurehdinsight.net/ws/v1/applicationhistory/.</p> 
<p>Se puede encontrar información más detallada sobre el servidor de escala de tiempo en http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html.</p></td>
<td>Servicio, YARN</td>
<td>Hadoop, HBase</td>
<td>N/D</td>
</tr>

<tr>
<td>Id. de implementación del clúster</td>
<td>A partir de la última versión del SDK 1.3.3.1.5426.29232, los usuarios pueden acceder al Id. exclusivo emitido por HDInsight de cada clúster. Esto permitirá a los clientes solucionar las instancias únicas de los clústeres cuando se reutiliza un nombre dns en escenarios de creación o eliminación.</td>
<td>SDK</td>
<td>Todo</td>
<td>N/D</td>
</tr>
</table>
<br>

* Tenga en cuenta que el error que impedía que se mostrara la versión completa # en el portal o que se devolviera en el SDK o en PowerShell, se ha resuelto. 

## Notas de la versión del 15/10/2014 ##

Esta revisión corrigió una pérdida de memoria en Templeton que afectaba a los usuarios que hacían un mayor uso de Templeton. En algunos casos, los usuarios que utilizan Templeton de forma intensiva encontraban errores con códigos de error 500 porque no había suficiente memoria para ejecutar las solicitudes. La solución a este problema era reiniciar el servicio Templeton. Ahora se ha corregido.


## Notas de la versión del 07/10/2014 ##

* Al usar el extremo Ambari, "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", el campo *host_name* devuelve ahora el nombre de dominio completo (FQDN) del nodo en lugar de solo el nombre del host. Por ejemplo, en lugar de devolver "**headnode0**", obtiene el FQDN "**headnode0.{ClusterDNS}.azurehdinsight.net**". Este cambio se solicitó para facilitar escenarios donde se pueden implementar varios tipos de clúster, como HBase y Hadoop, en una misma red virtual (VNET). Esto ocurre, por ejemplo, cuando se usa HBase como plataforma de back-end para Hadoop.

* Hemos proporcionado una nueva configuración de memoria para la implementación predeterminada del clúster de HDInsight. La configuración de memoria predeterminada anterior no tenía en cuenta correctamente la guía para el número de núcleos de CPU que se implementaban. Estos nuevos valores de memoria deberían proporcionar mejores resultados, según las recomendaciones de Hortonworks. Para cambiarlos, consulte la documentación de referencia del SDK sobre cómo cambiar la configuración del clúster. En la tabla siguiente se desglosa la nueva configuración de memoria que usa el clúster de HDInsight predeterminado de 4 núcleos de CPU (8 contenedores). Se proporcionan también entre paréntesis los valores que se usaban antes de esta versión. 
 
<table border="1">
<tr><th>Componente</th><th>Asignación de memoria</th></tr>
<tr><td> yarn.scheduler.minimum-allocation</td><td>768MB (antes 512MB)</td></tr>
<tr><td> yarn.scheduler.maximum-allocation</td><td>6144 MB (sin cambios)</td></tr>
<tr><td>yarn.nodemanager.resource.memory</td><td>6144 MB (sin cambios)</td></tr>
<tr><td>mapreduce.map.memory</td><td>768MB (antes 512MB)</td></tr>
<tr><td>mapreduce.map.java.opts</td><td>opts=-Xmx512m (antes -Xmx410m)</td></tr>
<tr><td>mapreduce.reduce.memory</td><td>1536MB (antes 1024MB)</td></tr>
<tr><td>mapreduce.reduce.java.opts</td><td>opts=-Xmx1024m (antes -Xmx819m)</td></tr>
<tr><td>yarn.app.mapreduce.am.resource</td><td>768MB (antes 1024MB)</td></tr>
<tr><td>yarn.app.mapreduce.am.command</td><td>opts=-Xmx512m (antes -Xmx819m)</td></tr>
<tr><td>mapreduce.task.io.sort</td><td>256 MB (antes 200 MB)</td></tr>
<tr><td>tez.am.resource.memory</td><td>1.536 MB (sin cambios)</td></tr>

</table><br>

Para obtener más información sobre la configuración de memoria que usan YARN y MapReduce en la plataforma de datos Hortonworks Data Platform que usa HDInsight, consulte [Determinación de la configuración de memoria de HDP](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1-latest/bk_installing_manually_book/content/rpm-chap1-11.html). Hortonworks proporciona también una herramienta para calcular la configuración de memoria correcta.

Error de HDInsight PowerShell o del SDK de HDInsight: "*El clúster no está configurado para el acceso a servicios HTTP*":

* Este error es un [problema de compatibilidad](https://social.msdn.microsoft.com/Forums/azure/es-es/a7de016d-8de1-4385-b89e-d2e7a1a9d927/hdinsight-powershellsdk-error-cluster-is-not-configured-for-http-services-access?forum=hdinsight) conocido que puede deberse a una diferencia entre la versión del SDK o de PowerShell y la versión del clúster. Los clústeres creados a partir del 15/8 cuentan con nueva funcionalidad de aprovisionamiento en Redes virtuales. Pero las versiones anteriores del SDK o de PowerShell no interpretan correctamente esta funcionalidad. El resultado es un error en algunas operaciones de envío. Si usa las API del SDK o cmdlets de PowerShell para enviar trabajos (**Use-AzureHDInsightCluster**, **Invoke-Hive**), esas operaciones pueden dar error con el mensaje "*El clúster <clustername> no está configurado para el acceso a servicios HTTPs*" o bien, dependiendo de la operación, con otros mensajes de error como "*No se puede conectar al clúster*".

* Estos problemas de compatibilidad se han resuelto en las últimas versiones del SDK de HDInsight y Azure PowerShell. Se recomienda actualizar el SDK de HDInsight a la versión 1.3.1.6 o posterior y Azure PowerShell Tools a la versión 0.8.8 o posterior. Puede obtener acceso al último SDK de HDInsight desde [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) y a Azure PowerShell Tools en [Instalación y configuración de Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/).

* Lo normal es que el SDK y PowerShell continúen funcionando con las nuevas actualizaciones de los clústeres, siempre y cuando la versión del clúster no cambie. Por ejemplo, los clústeres de la versión 3.1 serán compatibles siempre con la versión actual del SDK y PowerShell 1.3.1.6 y 0.8.8.


## Notas de la versión del 12/09/2014 de HDinsight 3.1##

* Esta versión se basa en Hortonworks Data Platform (HDP) 2.1.5. Para ver una lista de los errores corregidos en esta versión, consulte la página [Fixed in this Release](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.5/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.5-fixed.html) en el sitio de Hortonworks.
* En la carpeta de bibliotecas pig, el archivo "avro-mapred-1.7.4.jar" se ha cambiado a avro-mapred-1.7.4-hadoop2.jar. El contenido de este archivo contiene la revisión de un error leve que no interrumpe el funcionamiento. Se recomienda que los clientes no creen una dependencia directa del nombre del archivo JAR para evitar interrupciones cuando se cambie el nombre a los archivos.


## Notas de la versión del 21/08/2014 ##

* Vamos a agregar la siguiente nueva configuración de WebHCat (HIVE-7155) que establece el límite de memoria predeterminado para un trabajo de controlador Templeton en un 1 GB (el valor predeterminado anterior era 512 MB):
	
	* templeton.mapper.memory.mb (=1024)
	* Este cambio soluciona el siguiente error con el que algunas consultas de Hive se han encontrado debido a límites bajos de memoria: "el contenedor se ejecuta por encima de los límites de memoria física".
	* Para volver a los valores predeterminados anteriores, puede establecer este valor de configuración en 512 a través del SDK de PowerShell en el momento de creación del clúster mediante el siguiente comando:
	
		Add-AzureHDInsightConfigValues -Core @{"templeton.mapper.memory.mb"="512";}


* El nombre de host del rol zookeeper se ha cambiado por zookeeper. Esto afecta a la resolución de nombres dentro del clúster, pero no a las API REST externas. Si tiene componentes que usan el nombre de host zookeepernode, deberá actualizarlos para que usen en su lugar el nuevo nombre. Los nuevos nombres de los tres nodos de zookeeper son: 
	* zookeeper0 
	* zookeeper1 
	* zookeeper2 
* Se actualiza la matriz de compatibilidad de versiones de HBase. Solo se admite la versión HDInsight 3.1 (versión 0.98 de HBase) para las cargas de trabajo de HBase de producción. No se permitirá que la versión 3.0 que estaba disponible para vista previa siga avanzando. Durante el período de transición, los clientes pueden seguir creando clústeres de la versión 3.0. 

## Notas sobre clústeres creados antes del 15/08/2014 ##

Puede producirse un error de HDInsight PowerShell o del SDK de HDInsight con el mensaje "El clúster <clustername> no está configurado para el acceso a servicios HTTP" (o, según la operación, otros mensajes de error como: "No se puede conectar al clúster") debido a una diferencia entre la versión del SDK o PowerShell y la versión de un clúster. Los clústeres creados a partir del 15/8 cuentan con nueva funcionalidad de aprovisionamiento en Redes virtuales. Las versiones antiguas del SDK o PowerShell no interpretan correctamente esta funcionalidad y dan lugar a errores en operaciones de envío de trabajos. Si utiliza las API del SDK o cmdlets de PowerShell para enviar trabajos, como Use-AzureHDInsightCluster o Invoke-AzureHDInsightHiveJob, esas operaciones pueden dar error con alguno de los mensajes mencionados.

Estos problemas de compatibilidad se han resuelto en las últimas versiones del SDK y Azure PowerShell. Se recomienda actualizar el SDK de HDInsight a la versión 1.3.1.6 o posterior y Azure PowerShell Tools a la versión 0.8.8 o posterior. Puede obtener acceso al último SDK de HDInsight desde [nuget](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.HDInsight/) y a Azure PowerShell Tools con [Microsoft Web API](http://go.microsoft.com/?linkid=9811175&clcid=0x409).

Lo normal es que el SDK y PowerShell continúen funcionando con las nuevas actualizaciones de los clústeres, siempre y cuando la versión del clúster no cambie. Por ejemplo, los clústeres de la versión 3.1 serán compatibles siempre con la versión actual del SDK y PowerShell 1.3.1.6 y 0.8.8.


## Notas de la versión del 28/07/2014 ##

* **HDInsight disponible en nuevas regiones**: Con esta versión, hemos ampliado la presencia geográfica de HDInsight a tres nuevas regiones. Los clientes de HDInsight pueden crear ahora clústeres en estas regiones. 
	* Asia oriental 
	* Centro-Norte de EE. UU 
	* Centro-Sur de EE. UU 
* Con esta versión, HDInsight v1.6 (HDP1.1, Hadoop 1.0.3) y HDInsight v2.1 (HDP1.3, Hadoop 1.2) se van a eliminar del Portal de administración de Azure. Sin embargo, puede seguir creando clústeres de Hadoop para estas versiones mediante cmdlets de HDInsight PowerShell ([New-AzureHDInsightCluster](http://msdn.microsoft.com/library/dn593744.aspx)) o por medio del [SDK de HDInsight](http://msdn.microsoft.com/library/azure/dn469975.aspx). Consulte la página de [versiones de componentes de HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/) para obtener más información.
* Hortonworks Data Platform (HDP) cambia en esta versión: 

<table border="1">
<tr><th>HDP</th><th>Cambios</th></tr>
<tr><td>HDP 1.3 / HDI 2.1</td><td>Sin cambios</td></tr>
<tr><td>HDP 2.0 / HDI 3.0</td><td>Sin cambios</td></tr>
<tr><td>HDP 2.1 / HDI 3.1</td><td>zookeeper: ['3.4.5.2.1.3.0-1948'] -> ['3.4.5.2.1.3.2-0002']</td></tr>


</table><br>

## Notas de la versión del 24/06/2014 ##

Esta versión contiene varias nuevas mejoras del servicio HDInsight: 

* **Disponibilidad de HDP 2.1**: HDInsight 3.1, que contiene HDP 2.1, está ahora disponible con carácter general y es la versión predeterminada de los nuevos clústeres.
* **HBase: mejoras del Portal de administración de Azure**: Vamos a hacer que los clústeres estén disponibles en vista previa. Ahora puede crear clústeres de HBase desde el portal con tres clics.

![](http://i.imgur.com/cmOl5fM.png)

Con HBase, puede compilar diversas cargas de trabajo en tiempo real en HDInsight, desde sitios web interactivos que trabajan con grandes conjuntos de datos hasta servicios que almacenan datos de sensores y telemetría de millones de extremos. El paso siguiente sería analizar los datos de estas cargas de trabajo con trabajos de Hadoop. Esto es posible inmediatamente en HDInsight gracias a las experiencias proporcionadas como PowerShell y el panel de clúster de Hive.

### Apache Mahout ahora preinstalado en HDInsight 3.1 ###

 [Mahout](http://hortonworks.com/hadoop/mahout/) se encuentra preinstalado en los clústeres de Hadoop de HDInsight 3.1. Por tanto, puede ejecutar trabajos de Mahout sin necesidad de configuración adicional del clúster. Por ejemplo, puede iniciar una sesión remota en un clúster de Hadoop mediante el Protocolo de escritorio remoto (RDP) y ejecutar el comando Hello world de Mahout sin pasos adicionales:

		mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

		mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

Para obtener una descripción completa de este procedimiento, vea la documentación del [ejemplo de Breiman](https://mahout.apache.org/users/classification/breiman-example.html) en el sitio web de Apache Mahout. 


### Las consultas de Hive pueden usar Tez en HDInsight 3.1 ###

Hive 0.13 se encuentra ahora disponible en HDInsight 3.1 y es capaz de ejecutar consultas mediante Tez, que se puede aprovechar para conseguir mejoras sustanciales del rendimiento. 
Tez no está habilitado de forma predeterminada para consultas de Hive. Para usarlo, debe suscribirse. Puede habilitar Tez ejecutando el siguiente snippet de código:

		set hive.execution.engine=tez;
		select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

Hortonworks ha publicado un desglose detallado de las mejoras en el rendimiento de las consultas de Hive con Tez proporcionadas en comparativas estándar. Para obtener detalles, consulte [Comparativa de Apache Hive 13 para Enterprise Hadoop](http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/). 

Para obtener más información sobre el uso de Hive con Tez, compruebe la página wiki [Hive en Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez).

###Disponibilidad global
Con la versión de HDInsight de Azure en Hadoop 2.2, Microsoft ha conseguido que HDInsight esté disponible en las principales regiones geográficas de Azure. Concretamente, los centros de datos de Europa occidental y el Sudeste asiático ahora están en línea. De este modo se permite que los clientes ubiquen los clústeres en un centro de datos cercano y que esté situado en una zona cuyos requisitos legales sean potencialmente similares. 


###Qué se debe hacer y qué no se debe hacer entre versiones de clúster

**La tiendas de metadatos Oozie que se usan con el clúster de HDInsight 3.1 no son compatibles con clústeres de HDInsight 2.1 y no se pueden usar con esta versión anterior**.

Una base de datos personalizada de la tienda de metadatos Oozie implementada con un clúster de HDInsight 3.1 no se puede reutilizar con un clúster de HDInsight 2.1. Esto ocurre incluso si la tienda de metadatos se creó con un clúster de la versión 2.1. No se admite este escenario porque el esquema de la tienda de metadatos se actualiza cuando se usa con un clúster de la versión 3.1; por tanto, deja de ser compatible con la tienda de metadatos que requieren los clústeres de la versión 2.1. Cualquier intento de reutilizar una tienda de metadatos Oozie que se haya usado con un clúster de HDInsight 3.1 dejará inutilizable el clúster de la versión 2.1. 

**Las tiendas de metadatos Oozie no se pueden compartir entre clústeres**
De un modo más general y, en cierto modo, ortogonal, las tiendas de metadatos Oozie se asocian a clústeres específicos y no se pueden compartir entre clústeres.

###Cambios de compatibilidad

**Sintaxis de prefijo**:
La única sintaxis compatible con los clústeres de HDInsight 3.0 y 3.1 es "wasb://". La antigua sintaxis "asv://" es compatible con los clústeres de HDInsight 2.1 y 1.6, pero no es compatible con los clústeres de HDInsight 3.0 o versiones posteriores. Esto significa que los trabajos enviados a un clúster de HDInsight de versión 3.0  o 3.1 que usen explícitamente la sintaxis "asv://" tendrán errores. Debería usarse la sintaxis wasb:// en lugar de la anterior. Además, los trabajos enviados a cualquier clúster de HDInsight 3.0 o 3.1 que se hayan creado con una tienda de metadatos existente que contenga referencias explícitas a recursos con la sintaxis asv:// tendrán errores. Estas tiendas de metadatos tendrán que volver a crearse usando wasb:// para recursos de dirección. 


**Puertos**: Los puertos usados por el servicio HDInsight han cambiado. Los números de puerto que se han estado usando estaban dentro del intervalo de puertos transitorio del sistema operativo Windows. Los puertos se asignan automáticamente desde un intervalo transitorio predefinido en comunicaciones basadas en protocolo de Internet de corta duración. El nuevo conjunto de números de puerto de servicio de Hortonworks Data Platform (HDP) permitidos están ahora fuera de este intervalo para evitar conflictos que podrían surgir con los puertos que usan los servicios que se ejecutan en el nodo principal. Los nuevos números de puerto no deben provocar ningún cambio brusco. Los números usados ahora son los siguientes:

 **HDInsight 1.6 (HDP 1.1)**
<table border="1">
<tr><th>Nombre</th><th>Valor</th></tr>
<tr><td>dfs.http.address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.secondary.http.address</td><td>0.0.0.0:30090</td></tr>
<tr><td>mapred.job.tracker.http.address</td><td>jobtrackerhost:30030</td></tr>
<tr><td>mapred.task.tracker.http.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>mapreduce.history.server.http.address</td><td>0.0.0.0:31111</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

 **HDInsight 3.0 y 3.1 (HDP 2.0 y 2.1)**
<table border="1">
<tr><th>Nombre</th><th>Valor</th></tr>
<tr><td>dfs.namenode.http-address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.namenode.https-address</td><td>headnodehost:30470</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.namenode.secondary.http-address</td><td>0.0.0.0:30090</td></tr>
<tr><td>yarn.nodemanager.webapp.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

###Dependencias 

Las siguientes dependencias se han agregado en HDInsight 3.x (HDP2.x):

* guice-servlet
* optiq-core
* javax.inject
* activation
* jsr305
* geronimo-jaspic_1.0_spec
* jul-to-slf4j
* java-xmlbuilder
* ant
* commons-compiler
* jdo-api
* commons-math3
* paranamer
* jaxb-impl
* stringtemplate
* eigenbase-xom
* jersey-servlet
* commons-exec
* jaxb-api
* jetty-all-server
* janino
* xercesImpl
* optiq-avatica
* jta
* eigenbase-properties
* groovy-all
* hamcrest-core
* mail
* linq4j
* jpam
* jersey-client
* aopalliance
* geronimo-annotation_1.0_spec
* ant-launcher
* jersey-guice
* xml-apis
* stax-api
* asm-commons
* asm-tree
* wadl
* geronimo-jta_1.1_spec
* guice
* leveldbjni-all
* velocity
* jettison
* snappy-java
* jetty-all
* commons-dbcp

Las siguientes dependencias ya no existen en HDInsight 3.x (HDP2.x):

* jdeb
* kfs
* sqlline
* ivy
* aspectjrt
* json
* core
* jdo2-api
* avro-mapred
* datanucleus-enhancer
* jsp
* commons-logging-api
* commons-math
* JavaEWAH
* aspectjtools
* javolution
* hdfsproxy
* hbase
* snappy

###Cambios de versión 

Se han realizado los siguientes cambios de versión entre HDInsight 2.x (HDP1.x) y HDInsight 3.x (HDP2.x):

* metrics-core: ['2.1.2'] -> ['3.0.0']
* derbynet: ['10.4.2.0'] -> ['10.10.1.1']
* datanucleus: ['rdbms-3.0.8'] -> ['rdbms-3.2.9']
* jasper-compiler: ['5.5.12'] -> ['5.5.23']
* log4j: ['1.2.15', '1.2.16'] -> ['1.2.16', '1.2.17']
* derbyclient: ['10.4.2.0'] -> ['10.10.1.1']
* httpcore: ['4.2.4'] -> ['4.2.5']
* hsqldb: ['1.8.0.10'] -> ['2.0.0']
* jets3t: ['0.6.1'] -> ['0.9.0']
* protobuf-java: ['2.4.1'] -> ['2.5.0']
* derby: ['10.4.2.0'] -> ['10.10.1.1']
* jasper: ['runtime-5.5.12'] -> ['runtime-5.5.23']
* commons-daemon: ['1.0.1'] -> ['1.0.13']
* datanucleus-core: ['3.0.9'] -> ['3.2.10']
* datanucleus-api-jdo: ['3.0.7'] -> ['3.2.6']
* zookeeper: ['3.4.5.1.3.9.0-01320'] -> ['3.4.5.2.1.3.0-1948']
* bonecp: ['0.7.1.RELEASE'] -> ['0.8.0.RELEASE']


###Controladores
El controlador JDBC para SQL Server se utiliza de forma interna mediante HDInsight y no se utiliza para realizar operaciones externas. Si desea conectarse a HDInsight mediante ODBC, utilice Microsoft Hive ODBC Driver. Para obtener más información acerca del uso de ODBC para Hive, consulte [Conexión de Excel a HDInsight con Microsoft Hive ODBC Driver][connect-excel-with-hive-ODBC].


### Corrección de errores ###

Con esta versión, hemos actualizado las siguientes versiones de HDInsight   (Hortonworks Data Platform - HDP) con varias correcciones de errores:

* HDInsight 2.1 (HDP 1.3)
* HDInsight 3.0 (HDP 2.0)
* HDInsight 3.1 (HDP 2.1)


## Notas de la versión de Hortonworks ##

Las notas de la versión de los HDP que se usan en las versiones del clúster de HDInsight están disponibles en las siguientes ubicaciones.

* El clúster de HDInsight versión 3.1 utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. Este es el clúster de Hadoop predeterminado que se crea cuando se usa el portal de HDInsight de Azure después del 7/11/2014. Los clústeres de HDInsight 3.1 creados antes del 7/11/2014 se basaban en [Hortonworks Data Platform 2.1.1][hdp-2-1-1]. 

* La versión 3.0 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 2.0][hdp-2-0-8].

* La versión 2.1 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 1.3][hdp-1-3-0]. 

* La versión 1.6 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 1.1][hdp-1-1-0]. 

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html


[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/




<!--HONumber=42-->
