<properties
   pageTitle="Extensión de HDInsight con Red virtual de Azure | VNet" metaKeywords="virtual network, vnet, azure, hdinsight" description="Aprenda a usar la Red virtual de Azure para conectar HDInsight con otros recursos en la nube o recursos en su centro de datos."
   services="hdinsight"
   documentationCenter=""
   authors="blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/2/2015"
   ms.author="larryfr"/>


# Extensión de las funcionalidades de HDInsight con Red virtual de Azure

La Red virtual de Azure le permite extender sus soluciones de Hadoop para incorporar recursos locales como SQL Server o para crear redes privadas seguras entre los recursos existentes en la nube.

> [AZURE.NOTE] Actualmente, HDInsight no es compatible con las Redes virtuales de Azure basadas en afinidad. Cuando use HDInsight, debe usar redes virtuales basadas en ubicación.

## <a id="whatis"></a>¿Qué es Red virtual de Azure?

[Red virtual de Azure](/documentation/services/virtual-network/) permite crear una red segura y persistente que contenga los recursos que necesita para la solución. Una red virtual permite hacer lo siguiente:

* Conectar recursos en la nube en una red privada (solo en la nube)

	![diagram of cloud-only configuration](.\media\hdinsight-provision-clusters\cloud-only.png)

	El uso de Red virtual para vincular servicios de Azure con HDInsight permite los siguientes escenarios.

	* **Invocación de servicios o trabajos de HDInsight** desde Servicios web Azure o servicios que se ejecutan en Máquinas virtuales de Azure.

	* **Transferencia directa de datos** entre HDInsight y SQL Database o SQL Server u otra solución de almacenamiento de datos en ejecución en una máquina virtual.

	* **Combinación de varios servidores de HDInsight** en una sola solución. Al igual que usar un servidor de HDInsight Storm para consumir datos entrantes y, a continuación, almacenar los datos procesados en un servidor de HDInsight HBase. Los datos sin procesar también podrían almacenarse en un servidor de HDInsight Hadoop para un análisis posterior con MapReduce.

* Conectar sus recursos en la nube a la red de su centro de datos local (sitio a sitio o punto a sitio) usando una red privada virtual (VPN)

	La configuración sitio a sitio permite conectar varios recursos de su centro de datos a la Red virtual de Azure usando una VPN de hardware o el servicio de enrutamiento y acceso remoto.

	![diagram of site-to-site configuration](.\media\hdinsight-provision-clusters\site-to-site.png)

	La configuración punto a sitio permite conectar un recurso específico a la Red virtual de Azure usando una VPN de software

	![diagram of point-to-site configuration](.\media\hdinsight-provision-clusters\point-to-site.png)

	Usar la red virtual para vincular la nube con su centro de datos habilita escenarios similares a la configuración solo para la nube pero, en lugar de limitarse a trabajar con los recursos existentes en la nube, también puede trabajar con recursos existentes en el centro de datos.

	* **Transferencia directa de datos** entre HDInsight y el centro de datos. Por ejemplo, el uso de Sqoop para transferir datos desde o hacia SQL Server o leer datos generados por una aplicación de línea de negocio.

	* **Invocación de servicios o trabajos de HDInsight** desde una aplicación de línea de negocio. Por ejemplo, usar las API de Java de HBase para almacenar y recuperar datos desde un clúster de HDInsight HBase.

Para obtener más información sobre las características, las ventajas y la funcionalidad de Red virtual, consulte [Información general sobre Red virtual de Azure](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [WACOM.NOTE] Debe crear la red virtual de Azure antes de aprovisionar un clúster de HDInsight. Para obtener más información, consulte [Tareas de configuración de la red virtual](http://msdn.microsoft.com/library/azure/jj156206.aspx).
>
> HDInsight de Azure solo admite redes virtuales basadas en la ubicación y actualmente no funciona con redes virtuales basadas en grupos de afinidad.
>
> Se recomienda encarecidamente designar una única subred para cada clúster.

Para obtener más información sobre el aprovisionamiento de un clúster de HDInsight en una red virtual, consulte [Aprovisionamiento de clústeres de Hadoop en HDInsight](/documentation/articles/hdinsight-provision-clusters/).

## <a id="tasks"></a>Tareas e información

Esta sección contiene información sobre la información y las tareas comunes que probablemente necesite al usar HDInsight con una red virtual.

### Determinación del nombre de dominio completo

El clúster de HDInsight tendrá asignado un nombre de dominio completo específico para la interfaz de la red virtual. Esta es la dirección que debe usar al conectarse al clúster desde otros recursos en la red virtual. Para determinar el nombre de dominio completo, use la siguiente dirección URL para consultar el servicio de administración Ambari.

	https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

> [AZURE.NOTE] Para obtener más información sobre el uso de Ambari con HDInsight, consulte [Supervisión de clústeres de Hadoop en HDInsight con la API de Ambari](/documentation/articles/hdinsight-monitor-use-ambari-api/).

Debe especificar el nombre del clúster y un servicio y componente en ejecución en el clúster, como el administrador de recursos de YARN.

> [AZURE.NOTE] Los datos devueltos son un documento JSON que contiene gran cantidad de información acerca del componente. Para extraer únicamente el nombre de dominio completo, debe usar un analizador JSON para recuperar el valor `host_components[0].HostRoles.host_name`.

Por ejemplo, para devolver el nombre de dominio completo desde un clúster de HDInsight Hadoop, podría usar uno de los siguientes métodos a fin de recuperar los datos para el administrador de recursos de YARN.

* [Azure PowerShell](/documentation/articles/install-configure-powershell/)

		$ClusterDnsName = <clustername>
		$Username = <cluster admin username>
		$Password = <cluster admin password>
		$DnsSuffix = ".azurehdinsight.net"
		$ClusterFQDN = $ClusterDnsName + $DnsSuffix
		
		$webclient = new-object System.Net.WebClient
		$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)
		
		$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/		components/resourcemanager"
		$Response = $webclient.DownloadString($Url)
		$JsonObject = $Response | ConvertFrom-Json
		$FQDN = $JsonObject.host_components[0].HostRoles.host_name
		Write-host $FQDN

* [Curl](http://curl.haxx.se/) y [jq](http://stedolan.github.io/jq/)

		curl -G -u <username>:<password> https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/yarn/components/resourcemanager | jq .host_components[0].HostRoles.host_name

### Conexión a HBase

Para conectarse remotamente a HBase mediante la API de Java, debe determinar las direcciones de cuórum de Zookeeper para el clúster de HBase y especificar esto en su aplicación.

Para obtener la dirección de cuórum de Zookeeper, use uno de los siguientes métodos para consultar el servicio de administración de Ambari.

* [Azure PowerShell](/documentation/articles/install-configure-powershell/)

		$ClusterDnsName = <clustername>
		$Username = <cluster admin username>
		$Password = <cluster admin password>
		$DnsSuffix = ".azurehdinsight.net"
		$ClusterFQDN = $ClusterDnsName + $DnsSuffix

		$webclient = new-object System.Net.WebClient
		$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

		$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'

* [Curl](http://curl.haxx.se/) y [jq](http://stedolan.github.io/jq/)

		curl -G -u <username>:<password> "https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum" | jq .items[0].properties[]

> [AZURE.NOTE] Para obtener más información sobre el uso de Ambari con HDInsight, consulte [Supervisión de clústeres de Hadoop en HDInsight con la API de Ambari](/documentation/articles/hdinsight-monitor-use-ambari-api/).

Una vez que tenga la información de cuórum, úsela en su aplicación cliente.

Por ejemplo, en una aplicación Java que usa la API de HBase, agregue un archivo **hbase-site.xml** al proyecto y especifique la información de cuórum en el archivo de la siguiente manera.

```
<configuration>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.address,zookeeper1.address,zookeeper2.address</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.clientPort</name>
    <value>2181</value>
  </property>
</configuration>
```

### Comprobación de la conectividad de la red

Algunos servicios, como SQL Server, pueden limitar las conexiones de red entrantes. Esto impedirá que HDInsight trabaje correctamente con estos servicios.

Si tiene problemas para obtener acceso a un servicio desde HDInsight, consulte la documentación del servicio para asegurarse de que ha habilitado el acceso a la red. También puede comprobar el acceso a la red si crea una máquina virtual de Azure en la misma red virtual y usa utilidades cliente para comprobar que la máquina virtual puede conectarse al servicio a través de la red virtual.

## <a id="nextsteps"></a>Pasos siguientes

Los siguientes ejemplos demuestran cómo usar HDInsight con Red virtual de Azure.

* [Análisis de los datos de sensor con Storm y HBase en HDInsight](/documentation/articles/hdinsight-storm-sensor-data-analysis/): demuestra cómo configurar un clúster de Storm y HBase en una red virtual, así como escribir datos de manera remota en HBase desde Storm.

* [Aprovisionamiento de clústeres de HBase en Red virtual de Azure](/documentation/articles/hdinsight-hbase-provision-vnet/) : proporciona información sobre el aprovisionamiento de un clúster de HBase en una Red virtual de Azure.

* [Aprovisionamiento de clústeres de Hadoop en HDInsight](/documentation/articles/hdinsight-provision-clusters/): proporciona información sobre el aprovisionamiento de clústeres de Hadoop, incluyendo información sobre cómo usar Red virtual de Azure.

* [Uso de Sqoop con Hadoop en HDInsight](/documentation/articles/hdinsight-use-sqoop/) : proporciona información sobre el uso de Sqoop para transferir datos con SQL Server a través de una red virtual.

Para obtener más información acerca de Redes virtuales de Azure, consulte la [Información general sobre Red virtual de Azure](http://msdn.microsoft.com/library/azure/jj156007.aspx).

<!--HONumber=47-->
