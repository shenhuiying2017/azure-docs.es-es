<properties urlDisplayName="Monitor Hadoop clusters  in HDInsight using the Ambari API" pageTitle="Supervisi&oacute;n de cl&uacute;steres de Hadoop en HDInsight con la API de Ambari | Azure" metaKeywords="" description="Use las API de Apache Ambari para el aprovisionamiento, la administraci&oacute;n y la supervisi&oacute;n de cl&uacute;steres de Hadoop. Las API y herramientas de operador de Ambari son muy intuitivas y ocultan la complejidad de Hadoop." services="hdinsight" documentationCenter="" title="Supervisi&oacute;n de cl&uacute;steres de Hadoop en HDInsight con la API de Ambari" umbracoNaviHide="0" disqusComments="1" authors="jgao" editor="cgronlun" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Supervisión de clústeres de Hadoop en HDInsight con la API de Ambari

Aprenda a supervisar los clústeres de HDInsight de las versiones 3.1 y 2.1 con las API de Ambari.

**Duración aproximada:** 15 minutos

## En este artículo

-   [¿Qué es Ambari?][¿Qué es Ambari?]
-   [Requisitos previos][Requisitos previos]
-   [Inicio de la aplicación][Inicio de la aplicación]
-   [API de supervisión de Ambari][API de supervisión de Ambari]
-   [Pasos siguientes][Pasos siguientes]

## <span id="whatisambari"></span></a> ¿Qué es Ambari?

[Apache Ambari][Apache Ambari] sirve para el aprovisionamiento, la administración y la supervisión de clústeres de Hadoop de Apache. Incluye una recopilación intuitiva de herramientas de operador y un conjunto sólido de API que ocultan la complejidad de Hadoop y simplifican la operación de clústeres. Si desea obtener más información sobre las API, consulte [Referencia de API de Ambari][Referencia de API de Ambari] (en inglés).

HDInsight actualmente solo es compatible con la característica de supervisión de Ambari. La API de Ambari v1.0 es compatible con la versión 2.1 y 3.0 de clústeres de HDInsight. Este artículo abarca el acceso a API de Ambari en un clúster de HDInsight de las versiones 3.1 y 2.1. La principal diferencia entre las dos es que algunos componentes han cambiado con la incorporación de nuevas funciones (como el servidor de historial de trabajos).

## <span id="prerequisites"></span></a>Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

-   Una **estación de trabajo** con Azure PowerShell instalado y configurado. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell][Instalación y configuración de Azure PowerShell]. Para ejecutar scripts de PowerShell, debe ejecutar Azure PowerShell como administrador y establecer la directiva de ejecución en *RemoteSigned*. Consulte [Running Windows PowerShell scripts][Running Windows PowerShell scripts].

    [Curl][Curl] es opcional. Se puede instalar desde [aquí][aquí].

    > [WACOM.NOTE] Cuando utilice el comando Curl en Windows, utilice comillas dobles en lugar de comillas simples para los valores de opción.

-   **Un clúster de HDInsight de Azure**. Para obtener instrucciones acerca del aprovisionamiento del clúster, consulte [Introducción al uso de HDInsight de Azure][Introducción al uso de HDInsight de Azure] o [Aprovisionamiento de clústeres de HDInsight][Aprovisionamiento de clústeres de HDInsight]. Para completar el tutorial, necesitará los datos siguientes:

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Propiedad del clúster</th>
    <th align="left">Nombre de variable de PowerShell</th>
    <th align="left">Valor</th>
    <th align="left">Descripción</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">Nombre del clúster de HDInsight</td>
    <td align="left">$clusterName</td>
    <td align="left"></td>
    <td align="left">El nombre del clúster de HDInsight.</td>
    </tr>
    <tr class="even">
    <td align="left">Nombre de usuario del clúster</td>
    <td align="left">$clusterUsername</td>
    <td align="left"></td>
    <td align="left">Nombre de usuario del clúster especificado en el aprovisionamiento</td>
    </tr>
    <tr class="odd">
    <td align="left">Contraseña de clúster</td>
    <td align="left">$clusterPassword</td>
    <td align="left"></td>
    <td align="left">Contraseña de usuario de clúster</td>
    </tr>
    </tbody>
    </table>

    > [WACOM.NOTE] Complete los valores de las tablas, le resultará útil para completar el tutorial.

## <span id="jumpstart"></span></a>Inicio de la aplicación

Existen varias maneras de usar Ambari para supervisar clústeres de HDInsight.

**Uso de Azure PowerShell**

El siguiente es un script de PowerShell para obtener la información del jobtracker de MapReduce *en un clúster de la versión 3.1*. La principal diferencia aquí es que extraeremos estos detalles del servicio YARN (en lugar de MapReduce).

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/yarn/components/resourcemanager"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus 

    $response.metrics.'yarn.queueMetrics'

El siguiente es un script de PowerShell para obtener la información del jobtracker de MapReduce *en un clúster de la versión 2.1*.

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

![Salida de seguimiento de trabajo][Salida de seguimiento de trabajo]

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
                 "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

Nota para la versión del 8/10/2014:
Cuando se usa un extremo Ambari, "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", el campo *host\_name* devuelve ahora el nombre de dominio completo (FQDN) del nodo en lugar del nombre de host. Antes de la versión del 8/10/2014, este ejemplo devolvía simplemente "**headnode0**". Después del 8/10/2014, se obtiene el FQDN “**headnode0.{ClusterDNS}.azurehdinsight.net**” como se muestra en el ejemplo anterior. Este cambio se solicitó para facilitar escenarios donde se pueden implementar varios tipos de clúster, como HBase y Hadoop, en una misma red virtual (VNET). Esto ocurre, por ejemplo, cuando se usa HBase como plataforma de back-end para Hadoop.

## <span id="monitor"></span></a>API de supervisión de Ambari

La siguiente lista enumera algunas de las llamadas a API desde la supervisión de Ambari. Si desea obtener más información sobre las API, consulte [Referencia de API de Ambari][Referencia de API de Ambari] (en inglés).

<table border="1">
<tr>
<th>
Supervisión de la llamada a la API

</th>
<th>
URI

</th>
<th>
Descripción

</th>
</tr>
<tr>
<td>
Obtener clústeres

</td>
<td>
`/api/v1/clusters`

</td>
<td>
</td>
</tr>
<tr>
<td>
Obtener información de clúster.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net`

</td>
<td>
clústeres, servicios, hosts

</td>
</tr>
<tr>
<td>
Obtener servicios

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services`

</td>
<td>
Los servicios incluyen: hdfs, mapreduce

</td>
</tr>
<tr>
<td>
Obtener información sobre los servicios.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>`

</td>
<td>
</td>
</tr>
<tr>
<td>
Obtener componentes de servicio

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components`

</td>
<td>
HDFS: namenode, datanode
MapReduce: jobtracker; tasktracker

</td>
</tr>
<tr>
<td>
Obtener información de componente.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>`

</td>
<td>
ServiceComponentInfo, componentes de host, métricas

</td>
</tr>
<tr>
<td>
Obtener hosts

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts`

</td>
<td>
headnode0, workernode0

</td>
</tr>
<tr>
<td>
Obtener información sobre host.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName> `

<td>
</td>
</tr>
<tr>
<td>
Obtener componentes de host

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components`

</td>
<td>
namenode, resourcemanager

</td>
</tr>
<tr>
<td>
Obtener información de componente de host

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>`

</td>
<td>
HostRoles, componente, host, métricas

</td>
</tr>
<tr>
<td>
Obtener configuraciones

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations `

</td>
<td>
Tipos de configuración: núcleo-sitio, hdfs-sitio, mapred-sitio, hive-sitio

</td>
</tr>
<tr>
<td>
Obtener información de configuración

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName> `

</td>
<td>
Tipos de configuración: núcleo-sitio, hdfs-sitio, mapred-sitio, hive-sitio

</td>
</tr>
</table>
## <span id="nextsteps"></span></a>Pasos siguientes

Ahora sabe cómo usar las llamadas de API de supervisión de Ambari. Para obtener más información, consulte:

-   [Administración de clústeres de HDInsight con el portal de administración][Administración de clústeres de HDInsight con el portal de administración]
-   [Administración de clústeres de HDInsight con Azure PowerShell][Administración de clústeres de HDInsight con Azure PowerShell]
-   [Administración de clústeres de HDInsight con la interfaz de línea de comandos][Administración de clústeres de HDInsight con la interfaz de línea de comandos]
-   [Documentación de HDInsight][Documentación de HDInsight]
-   [Introducción a HDInsight][Introducción al uso de HDInsight de Azure]

  [¿Qué es Ambari?]: #whatisambari
  [Requisitos previos]: #prerequisites
  [Inicio de la aplicación]: #jumpstart
  [API de supervisión de Ambari]: #monitor
  [Pasos siguientes]: #nextsteps
  [Apache Ambari]: http://ambari.apache.org/
  [Referencia de API de Ambari]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [Instalación y configuración de Azure PowerShell]: ../install-configure-powershell/
  [Running Windows PowerShell scripts]: http://technet.microsoft.com/es-es/library/ee176949.aspx
  [Curl]: http://curl.haxx.se
  [aquí]: http://curl.haxx.se/download.html
  [Introducción al uso de HDInsight de Azure]: ../hdinsight-get-started/
  [Aprovisionamiento de clústeres de HDInsight]: ../hdinsight-provision-clusters/
  [Salida de seguimiento de trabajo]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
  [Administración de clústeres de HDInsight con el portal de administración]: ../hdinsight-administer-use-management-portal/
  [Administración de clústeres de HDInsight con Azure PowerShell]: ../hdinsight-administer-use-powershell/
  [Administración de clústeres de HDInsight con la interfaz de línea de comandos]: ../hdinsight-administer-use-command-line/
  [Documentación de HDInsight]: /es-es/documentation/services/hdinsight/
