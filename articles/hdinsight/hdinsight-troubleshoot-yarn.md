---
title: "Solución de problemas de YARN: Azure HDInsight | Microsoft Docs"
description: "Use las preguntas más frecuentes de Yarn para obtener respuestas a preguntas comunes sobre Yarn en la plataforma Azure HDInsight."
keywords: "Azure HDInsight, Yarn, preguntas más frecuentes, guía de solución de problemas, preguntas comunes"
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: 
editor: 
ms.assetid: F76786A9-99AB-4B85-9B15-CA03528FC4CD
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: arijitt
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 576d6bfe9697d2abd6f84824960dad62e9ed3c11
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="yarn-troubleshooting"></a>Solución de problemas de YARN

En este artículo se describen los principales problemas y sus soluciones para trabajar con cargas de YARN en Apache Ambari.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Creación de una nueva cola Yarn en un clúster

### <a name="issue"></a>Problema:

Crear una nueva cola Yarn con asignación de capacidad en un clúster de HDInsight.  

### <a name="resolution-steps"></a>Pasos de la solución: 

Para crear una nueva cola Yarn y equilibrar la asignación de capacidad en todas las colas, siga estos pasos en Ambari. 

En este ejemplo, dos colas existentes (default y thriftsvr) están cambiadas del 50 al 25 % de la capacidad, lo que permite a la nueva cola (Spark) tener una capacidad del 50 %.
| Cola | Capacidad | Máx. Capacity |
| --- | --- | --- | --- |
| default | 25 % | 50 % |
| thrftsvr | 25 % | 50 % |
| spark | 50 % | 50 % |
1. Haga clic en el icono de vistas de Ambari, el patrón de cuadrícula, y elija **Yarn Queue Manager** (Administrador de colas de Yarn).

    ![Clic en el icono de vistas de Ambari](media/hdinsight-troubleshoot-yarn/create-queue-1.png)
1. Seleccione la cola **default**.

    ![Selección de la cola default](media/hdinsight-troubleshoot-yarn/create-queue-2.png)
1. Cambie **Capacity** (Capacidad) del 50 % al 25 % para la cola **default** y la cola **thriftsvr**.

    ![Cambio de Capacity (Capacidad) al 25 % para las colas default y thriftsvr](media/hdinsight-troubleshoot-yarn/create-queue-3.png)
1. Haga clic en **Add Queue** (Agregar cola) para crear una cola.

    ![Clic en Add Queue (Agregar cola)](media/hdinsight-troubleshoot-yarn/create-queue-4.png)
1. Asigne un nombre a la cola nueva.

    ![Asignación del nombre Spark a la cola](media/hdinsight-troubleshoot-yarn/create-queue-5.png)  
1. Deje los valores de **Capacity** (Capacidad) en el 50 % y haga clic en el **Actions** (Acciones).

    ![Clic en el botón Actions (Acciones)](media/hdinsight-troubleshoot-yarn/create-queue-6.png)  
1. Elija **Save and Refresh Queues** (Guardar y actualizar colas).

    ![Elección de Save and Refresh Queues (Guardar y actualizar colas)](media/hdinsight-troubleshoot-yarn/create-queue-7.png)  

Estos cambios estarán visibles inmediatamente en la interfaz de usuario de Yarn Scheduler.

### <a name="further-reading"></a>Lecturas adicionales:

- [Yarn Capacity Scheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)


## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Descarga de los registros de Yarn desde un clúster

#### <a name="issue"></a>Problema:

Necesito volver a descargar el maestro y otros registros de contenedor de la aplicación Yarn desde el clúster de HDInsight.  

#### <a name="resolution-steps"></a>Pasos de la solución: 

1. Conéctese al clúster de HDInsight con un cliente de Shell seguro (SSH) (consulte la sección Lecturas adicionales a continuación).
1. Enumere todos los identificadores de aplicación de las aplicaciones Yarn que se estén ejecutando con el siguiente comando:

```apache
yarn top
```
Los identificadores aparecen en la columna `APPLICATIONID`, cuyos registros pueden descargarse.

```apache
YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
Queue(s) Containers: 2 allocated, 0 pending, 0 reserved

                  APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
 application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
 application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
```

Descargue los registros de contenedor de Yarn para todos los maestros de aplicación con el siguiente comando:
   
```apache
yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
```

Esto creará el archivo de registro denominado `amlogs.txt` con formato de texto. 

Descargue los registros de contenedor de Yarn solo para el maestro de aplicación más reciente con el siguiente comando:

```apache
yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
```

Esto creará el archivo de registro denominado `latestamlogs.txt` con formato de texto. 

Descargue los registros de contenedor de YARN para los dos primeros maestros de aplicación con el siguiente comando:

```apache
yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt 
```

Esto creará el archivo de registro denominado `first2amlogs.txt` con formato de texto. 

Descargue todos los registros de contenedor de Yarn con el siguiente comando:

```apache
yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
```

Esto creará el archivo de registro denominado `logs.txt` con formato de texto. 

Descargue el registro de Yarn de un contenedor determinado con el siguiente comando:

```apache
yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt 
```

Esto creará el archivo de registro denominado `containerlogs.txt` con formato de texto.

#### <a name="further-readings"></a>Lecturas adicionales:

- [Conexión al clúster de HDInsight a través de SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)

- [Apache Hadoop Yarn concepts and applications](https://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/) (Conceptos y aplicaciones de Yarn en Apache Hadoop)









