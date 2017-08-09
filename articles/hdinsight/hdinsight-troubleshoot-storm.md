---
title: "Solución de problemas de STORM: Azure HDInsight | Microsoft Docs"
description: "Use las preguntas más frecuentes de Storm para obtener respuestas a preguntas comunes sobre Storm en la plataforma Azure HDInsight."
keywords: "Azure HDInsight, Storm, preguntas más frecuentes, guía de solución de problemas, problemas comunes"
services: Azure HDInsight
documentationcenter: na
author: raviperi
manager: 
editor: 
ms.assetid: 74E51183-3EF4-4C67-AA60-6E12FAC999B5
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: raviperi
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: b52462096ce977b94ff9514df650607b05e17030
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="storm-troubleshooting"></a>Solución de problemas de STORM

En este artículo se describen los principales problemas y sus soluciones para trabajar con cargas de Storm en Apache Ambari.

## <a name="how-do-i-access-storm-ui-on-a-cluster"></a>Cómo se obtiene acceso a la interfaz de usuario de Storm en un clúster

### <a name="issue"></a>Problema:
Hay dos maneras de obtener acceso a la interfaz de usuario de Storm desde un explorador:

#### <a name="ambari-ui"></a>Interfaz de usuario de Ambari
1. Vaya al panel de Ambari.
1. Seleccione Storm en lista de servicios en la izquierda.
1. Seleccione la opción Storm UI (Interfaz de usuario de Storm) en el menú desplegable de vínculos rápidos.

#### <a name="direct-link"></a>Vínculo directo
Se puede obtener acceso a la interfaz de usuario de Storm desde la dirección URL:

https://\<NombreDnsClúster\>/stormui

ejemplo: https://stormcluster.azurehdinsight.net/stormui

## <a name="how-do-i-transfer-storm-eventhub-spout-checkpoint-information-from-one-topology-to-another"></a>Cómo se transfiere información de punto de control del spout de EventHub de Storm de una topología a otra

### <a name="issue"></a>Problema:
Al desarrollar topologías que leen en Event Hubs mediante el archivo jar de spout de EventHubs con Storm en HDInsight, ¿cómo se puede implementar una topología con el mismo nombre en un nuevo clúster, pero conservar los datos del punto de control enviado a Zookeeper en el clúster anterior?

#### <a name="where-is-checkpoint-data-stored"></a>Dónde se almacenan los datos de punto de control
El spout EventHub almacena los datos de punto de control para los desplazamientos en Zookeeper bajo dos rutas de acceso raíz:
- Los puntos de control de spout no transaccionales se almacenan en: /eventhubspout
- Los datos de punto de control de spout de transacciones se almacenan en: /transactional

#### <a name="how-to-restore"></a>Cómo restaurar
Los scripts y las bibliotecas para exportar datos desde Zookeeper y volver a importarlos con un nuevo nombre se pueden encontrar en: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0

La carpeta lib tiene archivos Jar que contienen la implementación para la operación de importación y exportación.
La carpeta bash tiene un script de ejemplo sobre cómo exportar datos desde el servidor de Zookeeper en el clúster anterior y volver a importarlos al servidor de Zookeeper en el nuevo clúster.

Es necesario ejecutar el script [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) desde los nodos de Zookeeper para importar o exportar datos.
El script debe actualizarse para corregir la cadena de versión de HDP que contiene.
(HDInsight está trabajando para que estos scripts sean genéricos, para que se puedan ejecutar desde cualquier nodo del clúster sin necesidad de modificaciones por parte del usuario final).

El comando de exportación escribirá los metadatos en una ruta de acceso de HDFS (almacén BLOB o ADLS) en la ubicación especificada.

### <a name="examples"></a>Ejemplos

##### <a name="export-offset-metadata"></a>Exportar los metadatos de desplazamiento:
1. Use SSH en el clúster de Zookeeper en el clúster anterior desde el que se debe exportar el desplazamiento de punto de comprobación.
1. Ejecute el comando siguiente (después de actualizar la cadena de versión de hdp) para exportar los datos de desplazamiento de Zookeeper a la ruta de acceso de HDFS zkdata/stormmetadta/.

```apache   
   java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
```

##### <a name="import-offset-metadata"></a>Importar los metadatos de desplazamiento
1. Use SSH en el clúster de Zookeeper en el clúster anterior desde el que se debe exportar el desplazamiento de punto de comprobación.
1. Ejecute el comando siguiente (después de actualizar la cadena de versión de hdp) para importar los datos de desplazamiento de Zookeeper desde la ruta de acceso de HDFS /stormmetadata/zkdataal servidor de Zookeeper en el clúster de destino.

```apache
   java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
```
   
##### <a name="delete-offset-metadata-so-topologies-can-start-processing-data-from-either-beginning-or-timestamp-of-user-choice"></a>Eliminar los metadatos de desplazamiento para que las topologías puedan empezar a procesar los datos (desde el principio o desde la marca de tiempo que el usuario elija)
1. Use SSH en el clúster de Zookeeper en el clúster anterior desde el que se debe exportar el desplazamiento de punto de comprobación.
1. Ejecute el comando siguiente (después de actualizar la cadena de versión de hdp) para eliminar todos los datos de desplazamiento de Zookeeper del clúster actual.

```apache
   java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Cómo se buscan los archivos binarios de Storm en un clúster

### <a name="issue"></a>Problema:
 Conocer la ubicación de los archivos binarios para los servicios de Storm en clúster de HDInsight

### <a name="resolution-steps"></a>Pasos de la solución:

Los archivos binarios de Storm para la pila de HDP actual se encuentran en: /usr/hdp/current/storm-client.

Esta ubicación es la misma para los nodos principales y para los nodos de trabajo.
 
Pueden haber varios archivos binarios específicos de la versión de HDP ubicados en/usr/hdp (ejemplo: /usr/hdp/2.5.0.1233/storm).

Pero /usr/hdp/current/storm-client tiene un vínculo simbólico a la versión más reciente que se ejecuta en el clúster.

### <a name="further-reading"></a>Lecturas adicionales:
 [Conexión al clúster de HDInsight con SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) [Storm](http://storm.apache.org/)
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Cómo se puede determinar la topología de implementación de un clúster de Storm
 
### <a name="issue"></a>Problema:
 
Identificar todos los componentes que se instalan con Storm de HDInsight.
 
El clúster de Storm consta de cuatro categorías de nodo
1. Puerta de enlace
1. Nodos principales
1. Nodos Zookeeper
1. Nodos de trabajo
 
#### <a name="gateway-nodes"></a>Nodos de puerta de enlace
Es una puerta de enlace y un servicio de proxy inverso que permite el acceso público al servicio de administración de Ambari activo y controla la elección del líder de Ambari.
 
#### <a name="zookeeper-nodes"></a>Nodos de Zookeeper
HDInsight incluye un cuórum de Zookeeper de tres nodos.
El tamaño del cuórum es fijo y no se puede configurar.
 
Los servicios de Storm en el clúster están configurados para usar automáticamente el cuórum ZK.
 
#### <a name="head-nodes"></a>Nodos principales
Los nodos principales de Storm ejecutan los siguientes servicios:
1. Nimbus
1. Servidor Ambari
1. Servidor de medición de Ambari
1. Recopilador de medición de Ambari
 
#### <a name="worker-nodes"></a>Nodos de trabajo
 Los nodos de trabajo de Storm ejecutan los siguientes servicios:
1. Supervisor
1. Máquinas virtuales Java de trabajo para ejecutar topologías
1. Agente de Ambari
 
## <a name="how-do-i-locate-storm-eventhub-spout-binaries-for-development"></a>Cómo se pueden localizar los archivos binarios de Storm-EventHub-Spout para el desarrollo
 
### <a name="issue"></a>Problema:
Cómo se puede obtener más información sobre el uso de archivos JAR de spout EventHub de Storm para su uso con la topología.
 
#### <a name="msdn-articles-on-how-to"></a>Artículos de procedimientos en MSDN
 
##### <a name="java-based-topology"></a>Topología basada en Java
https://docs.microsoft.com/es-es/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology
 
##### <a name="c-based-topology-using-mono-on-hdi-34-linux-storm-clusters"></a>Topología basada en C# (mediante Mono en clústeres de Storm Linux HDI 3.4 o superior)
https://docs.microsoft.com/es-es/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology
 
##### <a name="latest-storm-eventhub-spout-binaries-for-hdi35-linux-storm-clusters"></a>Archivos binarios de spout EventHub de Storm para clústeres de Storm Linux HDI 3.5 y superior
Revise https://github.com/hdinsight/mvn-repo/blob/master/README.md para obtener información sobre cómo usar el spout EventHub de Storm más reciente que funciona con los clústeres de Storm Linux HDI 3.5 y superior.
 
##### <a name="source-code-examples"></a>Ejemplos de código fuente:
https://github.com/Azure-Samples/hdinsight-java-storm-eventhub
 
## <a name="how-do-i-locate-storm-log4j-configuration-files-on-clusters"></a>Cómo se pueden encontrar los archivos de configuración Log4J de Storm en clústeres
 
### <a name="issue"></a>Problema:
 
Identificar los archivos de configuración de Log4J para servicios de Storm.
 
#### <a name="on-headnodes"></a>En nodos principales:
La configuración Log4J de Nimbus se lee de: /usr/hdp/<HDPVersion>/storm/log4j2/cluster.xml.
 
#### <a name="worker-nodes"></a>Nodos de trabajo
La configuración Log4J de Supervisor se lee de: /usr/hdp/<HDPVersion>/storm/log4j2/cluster.xml.
 
El archivo configuración Log4J de trabajo se lee de: /usr/hdp/<HDPVersion>/storm/log4j2/worker.xml.
 
Ejemplo: /usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml /usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml







