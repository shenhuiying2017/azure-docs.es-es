---
title: "Solución de problemas de HIVE: Azure HDInsight | Microsoft Docs"
description: "Use las preguntas más frecuentes de Hive para obtener respuestas a preguntas comunes sobre Hive en la plataforma Azure HDInsight."
keywords: "Azure HDInsight, Hive, preguntas más frecuentes, guía de solución de problemas, preguntas comunes"
services: Azure HDInsight
documentationcenter: na
author: dharmeshkakadia
manager: 
editor: 
ms.assetid: 15B8D0F3-F2D3-4746-BDCB-C72944AA9252
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: dharmeshkakadia
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 67b096c0585f7b73a57b784683944052d1cfaa33
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="hive-troubleshooting"></a>Solución de problemas de HIVE

En este artículo se describen los principales problemas y sus soluciones para trabajar con cargas de HIVE en Apache Ambari.

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>¿Cómo se exporta Hive Metastore y se importa en otro clúster?

### <a name="issue"></a>Problema:

Necesita exportar Hive Metastore e importarlo en otro clúster de HDInsight.  

### <a name="resolution-steps"></a>Pasos de resolución: 

1. Conéctese al clúster de HDInsight con un cliente Secure Shell (SSH). Consulte la sección Lecturas adicionales que figura a continuación.
1. Ejecute el siguiente comando en el clúster de HDInsight desde donde desea exportar Metastore:

```apache
for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
```

Esto generará un archivo denominado `allatables.sql`.

- Copie el archivo `alltables.sql` en el nuevo clúster de HDInsight y ejecute el siguiente comando:

```apache
hive -f alltables.sql
```

Este código supone que las rutas de acceso de datos en el nuevo clúster son las mismas que en el antiguo. Si no es así, puede editar manualmente el archivo  
`alltables.sql` generado para reflejar los cambios.

### <a name="further-reading"></a>Lecturas adicionales:

- [Conexión a través de SSH con HDInsight (Hadoop)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)


## <a name="how-do-locate-hive-logs-on-a-cluster"></a>¿Cómo se buscan registros de Hive en un clúster?

### <a name="issue"></a>Problema:

Necesita buscar los registros de cliente de Hive, Metastore y Hiveserver en un clúster de HDInsight.  

### <a name="resolution-steps"></a>Pasos de resolución: 

- Conéctese al clúster de HDInsight con un cliente Secure Shell (SSH). Consulte la sección Lecturas adicionales que figura a continuación.
- Los registros del cliente Hive se encuentran en:

```apache
/tmp/<username>/hive.log 
```

- Los registros de Hive Metastore se pueden encontrar en:

```apache
/var/log/hive/hivemetastore.log 
```

- Los registros de Hiveserver se pueden encontrar en:

```apache
/var/log/hive/hiveserver2.log 
```

### <a name="further-reading"></a>Lecturas adicionales:

- [Conexión a través de SSH con HDInsight (Hadoop)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)

## <a name="how-do-i-launch-hive-shell-with-specific-configurations-on-a-cluster"></a>¿Cómo se inicia el shell de Hive con configuraciones específicas en un clúster?

### <a name="issue"></a>Problema:

Necesita invalidar o especificar configuraciones de shell de Hive en tiempo de inicio en clústeres de HDInsight.  

### <a name="resolution-steps"></a>Pasos de resolución: 

- Especifique un par clave-valor de configuración al iniciar el shell de Hive. Consulte la sección Lecturas adicionales que figura a continuación:

```apache
hive -hiveconf a=b 
```

- Enumere todas las configuraciones efectivas en el shell de Hive con el siguiente comando:

```apache
hive> set;
```

Por ejemplo, utilice el siguiente comando para iniciar el shell de hive con el registro de depuración habilitado en la consola:
             
```apache
hive -hiveconf hive.root.logger=ALL,console 
```

### <a name="further-reading"></a>Lecturas adicionales:

- [Propiedades de configuración de Hive](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)


## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>¿Cómo se analizan datos de DAG de Tez en una ruta crítica de clúster?

### <a name="issue"></a>Problema:

Necesita analizar la información de grafo acíclico dirigido (DAG) de Tez, especialmente la ruta crítica en el clúster de HDInsight.

### <a name="resolution-steps"></a>Pasos de resolución:
 
- Conéctese al clúster de HDInsight con un cliente Secure Shell (SSH). Consulte la sección Lecturas adicionales que figura a continuación.

- Ejecute el siguiente comando en el símbolo del sistema:
   
```apache
hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
```

- Enumere otros analizadores que pueden usarse para analizar DAG de Tez con el siguiente comando:

```apache
hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
```

Debe proporcionarse un programa de ejemplo como el primer argumento.

Los nombres de programa válidos son: ContainerReuseAnalyzer: imprimir los detalles de la reutilización de contenedor en un DAG. CriticalPath: buscar la ruta crítica de un DAG. LocalityAnalyzer: imprimir los detalles de localidad en un DAG. ShuffleTimeAnalyzer: analizar los detalles de tiempo de orden aleatorio en un DAG. SkewAnalyzer: analizar los detalles de distorsión en un DAG. SlowNodeAnalyzer: imprimir los detalles del nodo en un DAG. SlowTaskIdentifier: imprimir los detalles de la tarea de baja velocidad de impresión en un DAG. SlowestVertexAnalyzer: imprimir detalles del vértice más lento en un DAG. SpillAnalyzer: Imprimir los detalles de volcado en un DAG. TaskConcurrencyAnalyzer: imprimir los detalles de simultaneidad de tareas en un DAG. VertexLevelCriticalPathAnalyzer: buscar la ruta crítica en el nivel de vértice en un DAG.


### <a name="further-reading"></a>Lecturas adicionales:

- [Conexión a través de SSH con HDInsight (Hadoop)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)



## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Cómo se descargan datos de DAG de Tez de un clúster

#### <a name="issue"></a>Problema:

Necesita descargar información de grafo acíclico dirigido (DAG) de Tez de un clúster de HDInsight.

#### <a name="resolution-steps"></a>Pasos de resolución:

Hay dos maneras de recopilar los datos de DAG de Tez.

- Desde la línea de comandos:
 
    Conéctese al clúster de HDInsight con un cliente Secure Shell (SSH). Ejecute el siguiente comando en el símbolo del sistema:
   
```apache
hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId> 
```

- O bien, puede usar la vista de Tez de Ambari:
   
Vaya a Ambari--> Vaya a la vista de Tez (oculta en el icono de cuadrados en la esquina superior derecha)--> Haga clic en el DAG en el que esté interesado --> Haga clic en Download data (Descargar datos).

#### <a name="further-reading"></a>Lecturas adicionales:

1) [Conexión a través de SSH con HDInsight (Hadoop)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)








