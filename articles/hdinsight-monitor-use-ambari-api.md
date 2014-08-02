<properties linkid="manage-services-hdinsight-use-Ambari" urlDisplayName="Monitor HDInsight clusters using the Ambari API" pageTitle="Monitor HDInsight clusters using the Ambari API | Azure" metaKeywords="" description="Use the Apache Ambari APIs for provisioning, managing, and monitoring Hadoop clusters. Ambari's intuitive operator tools and APIs hide the complexity of Hadoop." services="hdinsight" documentationCenter="" title="Monitor HDInsight clusters using the Ambari API" umbracoNaviHide="0" disqusComments="1" authors="jgao" editor="cgronlun" manager="paulettm" />

Supervisión de clústeres de HDInsight con la API de Ambari
==========================================================

Aprenda a supervisar los clústeres de HDInsight versión 2.1 con API de Ambari.

**Duración aproximada:** 15 minutos

En este artículo
----------------

-   [¿Qué es Ambari?](#whatisambari)
-   [Requisitos previos](#prerequisites)
-   [Inicio de la aplicación](#jumpstart)
-   [API de supervisión de Ambari](#monitor)
-   [Pasos siguientes](#nextsteps)

¿Qué es Ambari?
---------------

[Apache Ambari](http://ambari.apache.org/) sirve para el aprovisionamiento, la administración y la supervisión de clústeres de Hadoop de Apache. Incluye una recopilación intuitiva de herramientas de operador y un conjunto sólido de API que ocultan la complejidad de Hadoop y simplifican la operación de clústeres. Si desea obtener más información sobre las API, consulte [Referencia de API de Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md) (en inglés).

HDInsight actualmente solo es compatible con la característica de supervisión de Ambari. La API de Ambari v1.0 es compatible con la versión 2.1 y 3.0 de clústeres de HDInsight. Este artículo solo abarca la ejecución de API de Ambari en clúster de HDInsight, versión 2.1.

Requisitos previos
------------------

Antes de empezar este tutorial, debe contar con lo siguiente:

-   Una **estación de trabajo** con Azure PowerShell instalado y configurado. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell](../install-configure-powershell/). Para ejecutar scripts de PowerShell, debe ejecutar Azure PowerShell como administrador y establecer la directiva de ejecución en *RemoteSigned*. Consulte [Running Windows PowerShell scripts](http://technet.microsoft.com/en-us/library/ee176949.aspx).

    [Curl](http://curl.haxx.se) es opcional. Se puede instalar desde [aquí](http://curl.haxx.se/download.html).

    > [WACOM.NOTE] Cuando utilice el comando Curl en Windows, utilice comillas dobles en lugar de comillas simples para los valores de opción.

-   **Un clúster de HDInsight de Azure**. Para obtener instrucciones acerca del aprovisionamiento del clúster, consulte [Introducción al uso de HDInsight de Azure](../hdinsight-get-started/) o [Aprovisionamiento de clústeres de HDInsight](../hdinsight-provision-clusters/). Para completar el tutorial, necesitará los datos siguientes:

    Propiedad del clúster

    Nombre de variable de PowerShell

    Valor

    Descripción

    Nombre del clúster de HDInsight

    \$clusterName

    El nombre del clúster de HDInsight.

    Nombre de usuario del clúster

    \$clusterUsername

    Nombre de usuario del clúster especificado en el aprovisionamiento

    Contraseña de clúster

    \$clusterPassword

    Contraseña de usuario de clúster

    > [WACOM.NOTE] Complete los valores de las tablas, le resultará útil para completar el tutorial.

Inicio de la aplicación
-----------------------

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

![Salida de seguimiento de trabajo](./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png)

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

API de supervisión de Ambari
----------------------------

La siguiente lista enumera algunas de las llamadas a API desde la supervisión de Ambari. Si desea obtener más información sobre las API, consulte [Referencia de API de Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md) (en inglés).

Supervisión de la llamada a la API

URI

Descripción

Obtener clústeres

/api/v1/clusters

Obtener información de clúster.

/api/v1/clusters/&lt;ClusterName\>.azurehdinsight.net

clústeres, servicios, hosts

Obtener servicios

/api/v1/clusters/&lt;ClusterName\>.azurehdinsight.net/services

Los servicios incluyen: hdfs, mapreduce

Obtener información sobre los servicios.

/api/v1/clusters/&lt;ClusterName\>.azurehdinsight.net/services/&lt;ServiceName\>

Obtener componentes de servicio

/api/v1/clusters/&lt;ClusterName\>.azurehdinsight.net/services/&lt;ServiceName\>/components

HDFS: namenode, datanode
MapReduce: jobtracker; tasktracker

Obtener información de componente.

/api/v1/clusters/&lt;ClusterName\>.azurehdinsight.net/services/&lt;ServiceName\>/components/&lt;ComponentName\>

ServiceComponentInfo, componentes de host, métricas

Obtener hosts

/api/v1/clusters/&lt;ClusterName\>.azurehdinsight.net/hosts

headnode0, workernode0

Obtener información sobre host.

/api/v1/clusters/&lt;ClusterName\>.azurehdinsight.net/hosts/&lt;HostName\>

Obtener componentes de host

/api/v1/clusters/&lt;ClusterName\>.azurehdinsight.net/hosts/&lt;HostName\>/host\_components

namenode, resourcemanager

Obtener información de componente de host

/api/v1/clusters/&lt;ClusterName\>.azurehdinsight.net/hosts/&lt;HostName\>/host\_components/&lt;ComponentName\>

HostRoles, componente, host, métricas

Obtener configuraciones

/api/v1/clusters/&lt;ClusterName\>.azurehdinsight.net/configurations

Tipos de configuración: núcleo-sitio, hdfs-sitio, mapred-sitio, hive-sitio

Obtener información de configuración

/api/v1/clusters/&lt;ClusterName\>.azurehdinsight.net/configurations?type=&lt;ConfigType\>&tag=&lt;VersionName\>

Tipos de configuración: núcleo-sitio, hdfs-sitio, mapred-sitio, hive-sitio

Pasos siguientes
----------------

Ahora sabe cómo usar las llamadas de API de supervisión de Ambari. Para obtener más información, consulte:

-   [Administración de clústeres de HDInsight con el portal de administración](../hdinsight-administer-use-management-portal/)
-   [Administración de clústeres de HDInsight con Azure PowerShell](../hdinsight-administer-use-powershell/)
-   [Administración de clústeres de HDInsight con la interfaz de línea de comandos](../hdinsight-administer-use-command-line/)
-   [Documentación de HDInsight](/en-us/documentation/services/hdinsight/)
-   [Introducción a HDInsight](../hdinsight-get-started/)

