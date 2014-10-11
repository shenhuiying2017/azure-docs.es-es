<properties linkid="manage-services-hdinsight-use-Ambari" urlDisplayName="Monitor Hadoop clusters  in HDInsight using the Ambari API" pageTitle="Monitor Hadoop clusters in HDInsight using the Ambari API | Azure" metaKeywords="" description="Use the Apache Ambari APIs for provisioning, managing, and monitoring Hadoop clusters. Ambari's intuitive operator tools and APIs hide the complexity of Hadoop." services="hdinsight" documentationCenter="" title="Monitor Hadoop clusters in HDInsight using the Ambari API" umbracoNaviHide="0" disqusComments="1" authors="jgao" editor="cgronlun" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Supervisión de clústeres de Hadoop en HDInsight con la API de Ambari

Aprenda a supervisar los clústeres de HDInsight versión 2.1 con API de Ambari.

**Duración aproximada:** 15 minutos

## En este artículo

-   [¿Qué es Ambari?][]
-   [Requisitos previos][]
-   [Inicio de la aplicación][]
-   [API de supervisión de Ambari][]
-   [Pasos siguientes][]

## <span id="whatisambari"></span></a> ¿Qué es Ambari?

[Apache Ambari][] sirve para el aprovisionamiento, la administración y la supervisión de clústeres de Hadoop de Apache. Incluye una recopilación intuitiva de herramientas de operador y un conjunto sólido de API que ocultan la complejidad de Hadoop y simplifican la operación de clústeres. Si desea obtener más información sobre las API, consulte [Referencia de API de Ambari][] (en inglés).

HDInsight actualmente solo es compatible con la característica de supervisión de Ambari. La API de Ambari v1.0 es compatible con la versión 2.1 y 3.0 de clústeres de HDInsight. Este artículo solo abarca la ejecución de API de Ambari en clúster de HDInsight, versión 2.1.

## <span id="prerequisites"></span></a>Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

-   Una **estación de trabajo** con Azure PowerShell instalado y configurado. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell][]. Para ejecutar scripts de PowerShell, debe ejecutar Azure PowerShell como administrador y establecer la directiva de ejecución en *RemoteSigned*. Consulte [Running Windows PowerShell scripts][].

    [Curl][] es opcional. Se puede instalar desde [aquí][].

    > [WACOM.NOTE] Cuando utilice el comando Curl en Windows, utilice comillas dobles en lugar de comillas simples para los valores de opción.

-   **Un clúster de HDInsight de Azure**. Para obtener instrucciones acerca del aprovisionamiento del clúster, consulte [Introducción al uso de HDInsight de Azure][] o [Aprovisionamiento de clústeres de HDInsight][]. Para completar el tutorial, necesitará los datos siguientes:

	<table border="1">
	<tr><th>Propiedad del clúster</th><th>Nombre de variable de PowerShell</th><th>Valor</th><th>Descripción</th></tr>
	<tr><td>Nombre del clúster de HDInsight</td><td>$clusterName</td><td></td><td>El nombre del clúster de HDInsight.</td></tr>
	<tr><td>Nombre de usuario del clúster</td><td>$clusterUsername</td><td></td><td>Nombre de usuario del clúster especificado en el aprovisionamiento</td></tr>
	<tr><td>Contraseña de clúster</td><td>$clusterPassword</td><td></td><td>Contraseña de usuario de clúster</td></tr>
	</table>

    > [WACOM.NOTE] Complete los valores de las tablas, le resultará útil para completar el tutorial.

## <span id="jumpstart"></span></a>Inicio de la aplicación

Existen varias maneras de usar Ambari para supervisar clústeres de HDInsight.

**Uso de Azure PowerShell**

El siguiente es un script de PowerShell para obtener la información de seguimiento de trabajo de MapReduce:

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus 

    $response.metrics.'mapred.JobTracker'

La salida es la siguiente:

![Salida de seguimiento de trabajo][]

**Uso de curl**

El siguiente es un ejemplo de cómo obtener información de clústeres con Curl:

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

La salida es la siguiente:

    {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
     "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
     "services"[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
     "hosts":[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"workernode0"}}]}

## <span id="monitor"></span></a>API de supervisión de Ambari

La siguiente lista enumera algunas de las llamadas a API desde la supervisión de Ambari. Si desea obtener más información sobre las API, consulte [Referencia de API de Ambari][] (en inglés).

<table border="1">
<tr><th>Supervisión de la llamada a la API</th><th>URI</th><th>Descripción</th></tr>
<tr><td>Obtener clústeres</td><td>/api/v1/clusters</td><td></td></tr>
<tr><td>Obtener información de clúster.</td><td>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net</td><td>clusters, services, hosts</td></tr>
<tr><td>Obtener servicios</td><td>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services</td><td>Services include: hdfs, mapreduce</td></tr>
<tr><td>Obtener información sobre los servicios.</td><td>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;</td><td></td></tr>
<tr><td>Obtener componentes de servicio</td><td>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;/components</td><td>HDFS: namenode, datanode<br/>MapReduce: jobtracker; tasktracker</td></tr>
<tr><td>Obtener información de componente.</td><td>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;/components/&lt;ComponentName&gt;</td><td>ServiceComponentInfo, host-components, metrics</td></tr>
<tr><td>Obtener hosts</td><td>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts</td><td>headnode0, workernode0</td></tr>
<tr><td>Obtener información sobre host.</td><td>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt; 
</td><td></td></tr>
<tr><td>Obtener componentes de host</td><td>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;/host_components
</td><td>namenode, resourcemanager</td></tr>
<tr><td>Obtener información de componente de host</td><td>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;/host_components/&lt;ComponentName&gt;
</td><td>HostRoles, component, host, metrics</td></tr>
<tr><td>Obtener configuraciones</td><td>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/configurations 
</td><td>Config types: core-site, hdfs-site, mapred-site, hive-site</td></tr>
<tr><td>Obtener información de configuración</td><td>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/configurations?type=&lt;ConfigType&gt;&tag=&lt;VersionName&gt; 
</td><td>Tipos de configuración: núcleo-sitio, hdfs-sitio, mapred-sitio, hive-sitio</td></tr>
</table>

## <span id="nextsteps"></span></a>Pasos siguientes

Ahora sabe cómo usar las llamadas de API de supervisión de Ambari. Para obtener más información, consulte:

-   [Administración de clústeres de HDInsight con el portal de administración][]
-   [Administración de clústeres de HDInsight con Azure PowerShell][]
-   [Administración de clústeres de HDInsight con la interfaz de línea de comandos][]
-   [Documentación de HDInsight][]
-   [Introducción a HDInsight][Introducción al uso de HDInsight de Azure]

  [¿Qué es Ambari?]: #whatisambari
  [Requisitos previos]: #prerequisites
  [Inicio de la aplicación]: #jumpstart
  [API de supervisión de Ambari]: #monitor
  [Pasos siguientes]: #nextsteps
  [Apache Ambari]: http://ambari.apache.org/
  [Referencia de API de Ambari]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [Instalación y configuración de Azure PowerShell]: ../install-configure-powershell/
  [Running Windows PowerShell scripts]: http://technet.microsoft.com/en-us/library/ee176949.aspx
  [Curl]: http://curl.haxx.se
  [aquí]: http://curl.haxx.se/download.html
  [Introducción al uso de HDInsight de Azure]: ../hdinsight-get-started/
  [Aprovisionamiento de clústeres de HDInsight]: ../hdinsight-provision-clusters/
  [Salida de seguimiento de trabajo]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
  [Administración de clústeres de HDInsight con el portal de administración]: ../hdinsight-administer-use-management-portal/
  [Administración de clústeres de HDInsight con Azure PowerShell]: ../hdinsight-administer-use-powershell/
  [Administración de clústeres de HDInsight con la interfaz de línea de comandos]: ../hdinsight-administer-use-command-line/
  [Documentación de HDInsight]: /en-us/documentation/services/hdinsight/
