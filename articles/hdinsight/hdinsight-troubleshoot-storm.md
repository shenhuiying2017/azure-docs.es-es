---
title: "Solución de problemas de Storm mediante Azure HDInsight | Microsoft Docs"
description: "Obtenga respuestas a las preguntas comunes sobre cómo usar con Apache Storm con Azure HDInsight."
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
ms.openlocfilehash: 70a3d762431d90acdd6ed2a432a569f34d0ce447
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="troubleshoot-storm-by-using-azure-hdinsight"></a>Solución de problemas de Storm mediante Azure HDInsight

Obtenga información sobre los principales problemas y sus soluciones al trabajar con cargas útiles de Apache Storm en Apache Ambari.

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Acceso a la interfaz de usuario de Storm en un clúster
Tiene dos opciones para acceder a la interfaz de usuario de Storm desde un explorador:

### <a name="ambari-ui"></a>Interfaz de usuario de Ambari
1. Vaya al panel de Ambari.
2. En la lista de servicios, seleccione **Storm**.
3. En el menú **Vínculos rápidos**, seleccione **Interfaz de usuario de Storm**.

### <a name="direct-link"></a>Vínculo directo
Puede acceder a la interfaz de usuario de Storm en la siguiente URL:

https://\<nombre DNS del clúster\>/stormui

Ejemplo:

 https://stormcluster.azurehdinsight.net/stormui

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Transferencia de información del punto de control del spout del centro de eventos de Storm de una topología a otra

Cuando se desarrollan topologías que leen desde instancias de Azure Event Hubs mediante el archivo .jar del spout del centro de eventos Storm en HDInsight, debe implementar una topología que tenga el mismo nombre en un nuevo clúster. Sin embargo, debe conservar los datos del punto de control que se confirmaron en Apache ZooKeeper en el clúster antiguo.

### <a name="where-checkpoint-data-is-stored"></a>Dónde se almacenan los datos de punto de control
El spout del centro de eventos almacena los datos de punto de control para los desplazamientos en ZooKeeper en dos rutas de acceso raíz:
- Los puntos de control de spout no transaccionales se almacenan en /eventhubspout.
- Los datos de punto de control de spout de transacciones se almacenan en /transactional.

### <a name="how-to-restore"></a>Cómo restaurar
Para obtener los scripts y las bibliotecas que se utilizan para exportar datos de ZooKeeper y, después, importar de nuevo los datos a ZooKeeper con un nuevo nombre, consulte [Ejemplos de Storm de HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

La carpeta lib tiene archivos .jar que contienen la implementación para la operación de exportación e importación. La carpeta bash tiene un script de ejemplo que muestra como exportar datos desde el servidor de ZooKeeper en el clúster anterior y, después, volver a importarlos al servidor de ZooKeeper en el nuevo clúster.

Ejecuta el script [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) desde los nodos de ZooKeeper para importar y después exportar datos. Actualice el script a la versión correcta de Hortonworks Data Platform (HDP). (Estamos trabajando en que estos scripts sean genéricos en HDInsight. Los scripts genéricos pueden ejecutarse desde cualquier nodo en el clúster sin modificaciones por el usuario).

El comando export escribe los metadatos en una ruta de acceso del Sistema de archivos distribuido de Apache Hadoop (HDFS) (en un almacén de Azure Blob Storage o de Azure Data Lake Store) en la ubicación que haya establecido.

### <a name="examples"></a>Ejemplos

#### <a name="export-offset-metadata"></a>Exportación de los metadatos de desplazamiento
1. Use SSH para ir al clúster de ZooKeeper en el clúster desde el que se debe exportar el desplazamiento de punto de control.
2. Ejecute el comando siguiente (después de actualizar la cadena de versión de HDP) para exportar los datos de desplazamiento de ZooKeeper a la ruta de acceso de HDFS /stormmetadta/zkdata:

    ```apache   
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Importación de los metadatos de desplazamiento
1. Use SSH para ir al clúster de ZooKeeper en el clúster desde el que se debe exportar el desplazamiento de punto de control.
2. Ejecute el comando siguiente (después de actualizar la cadena de versión de hdp) para importar los datos de desplazamiento de ZooKeeper desde la ruta de acceso de HDFS /stormmetadata/zkdata al servidor de ZooKeeper en el clúster de destino:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```
   
#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Elimine los metadatos de desplazamiento para que las topologías puedan empezar a procesar los datos desde el principio o desde una marca de tiempo que elija el usuario.
1. Use SSH para ir al clúster de ZooKeeper en el clúster desde el que se debe exportar el desplazamiento de punto de control.
2. Ejecute el comando siguiente (después de actualizar la cadena de versión de HDP) para eliminar todos los datos de desplazamiento de ZooKeeper del clúster actual:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Cómo se buscan los archivos binarios de Storm en un clúster
Los archivos binarios de Storm para la pila de HDP actual se encuentran en: /usr/hdp/current/storm-client. La ubicación es la misma para los nodos principales y para los nodos de trabajo.
 
Puede haber varios archivos binarios para versiones específicas de HDP en /usr/hdp (por ejemplo, /usr/hdp/2.5.0.1233/storm). La carpeta /usr/hdp/current/storm-client tiene un vínculo simbólico a la versión más reciente que se ejecuta en el clúster.

Para más información, consulte cómo [conectarse a un clúster de HDInsight a través de SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) y [Storm](http://storm.apache.org/).
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Cómo se puede determinar la topología de implementación de un clúster de Storm
En primer lugar, identifique todos los componentes que se instalan con Storm de HDInsight. Un clúster de Storm consta de cuatro categorías de nodo:

* Nodos de puerta de enlace
* Nodos principales
* Nodos de ZooKeeper
* Nodos de trabajo
 
### <a name="gateway-nodes"></a>Nodos de puerta de enlace
Un nodo de puerta de enlace es una puerta de enlace y un servicio de proxy inverso que permite el acceso público a un servicio de administración de Ambari activo. También controla la elección del líder de Ambari.
 
### <a name="head-nodes"></a>Nodos principales
Los nodos principales de Storm ejecutan los siguientes servicios:
* Nimbus
* Servidor Ambari
* Servidor de métricas de Ambari
* Recopilador de métricas de Ambari
 
### <a name="zookeeper-nodes"></a>Nodos de ZooKeeper
HDInsight incluye un cuórum de ZooKeeper de tres nodos. El tamaño del cuórum es fijo y no se puede volver a configurar.
 
Los servicios de Storm en el clúster se configuran para usar automáticamente el cuórum de ZooKeeper.
 
### <a name="worker-nodes"></a>Nodos de trabajo
Los nodos de trabajo de Storm ejecutan los siguientes servicios:
* Supervisor
* Máquinas virtuales Java (JVM) de trabajo, para topologías en ejecución
* Agente de Ambari
 
## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Localización de los archivos binarios del spout del centro de eventos de Storm para el desarrollo
 
Para más información sobre el uso de archivos .jar del centro de eventos de Storm con su topología, consulte los siguientes recursos.
 
### <a name="java-based-topology"></a>Topología basada en Java
[Procesamiento de eventos desde Azure Event Hubs con Storm en HDInsight (Java)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology)
 
### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>Topología basada en C# (Mono en clústeres de Storm Linux HDInsight 3.4 o superior)
[Procesamiento de eventos desde Azure Event Hubs con Storm en HDInsight (C#)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)
 
### <a name="latest-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Archivos binarios de spout del centro de eventos de Storm para clústeres de Storm Linux HDInsight 3.5 y superior
Para obtener información sobre cómo usar el spout del centro de eventos de Storm más reciente que funciona con los clústeres de Linux Storm de HDInsight 3.5 o superior, consulte el [archivo Léame](https://github.com/hdinsight/mvn-repo/blob/master/README.md) de mvn-repo.
 
### <a name="source-code-examples"></a>Ejemplos de código fuente
Consulte algunos [ejemplos](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) sobre cómo leer y escribir desde un centro de eventos de Azure Event Hubs mediante una topología de Apache Storm (escrita en Java) en un clúster de Azure HDInsight.
 
## <a name="how-do-i-locate-storm-log4j-configuration-files-on-clusters"></a>Cómo se pueden encontrar los archivos de configuración Log4J de Storm en clústeres
 
Para identificar los archivos de configuración de Log4J de Apache para servicios de Storm.
 
### <a name="on-head-nodes"></a>En nodos principales
La configuración Log4J de Nimbus se lee de /usr/hdp/\<versión de HDP\>/storm/log4j2/cluster.xml.
 
### <a name="on-worker-nodes"></a>En nodos de trabajo
La configuración Log4J del supervisor se lee de /usr/hdp/\<versión de HDP\>/storm/log4j2/cluster.xml.
 
El archivo de configuración Log4J de trabajo se lee de /usr/hdp/\<versión de HDP\>/storm/log4j2/worker.xml.
 
Ejemplos: /usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml /usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml

