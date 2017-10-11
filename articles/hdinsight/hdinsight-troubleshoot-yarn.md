---
title: "Solución de problemas de YARN mediante Azure HDInsight | Microsoft Docs"
description: "Obtenga respuestas a las preguntas más comunes sobre cómo trabajar con Apache Hadoop YARN y Azure HDInsight."
keywords: "Azure HDInsight, YARN, preguntas más frecuentes, guía de solución de problemas, preguntas comunes"
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
ms.openlocfilehash: 63f2d88ad59661b7fbcffd0aaeb94c58d40bdb73
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="troubleshoot-yarn-by-using-azure-hdinsight"></a>Solución de problemas de YARN mediante Azure HDInsight

Obtenga información sobre los principales problemas y sus soluciones al trabajar con cargas útiles de Apache Hadoop YARN en Apache Ambari.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Creación de una nueva cola de YARN en un clúster


### <a name="resolution-steps"></a>Pasos de la solución 

Para crear una nueva cola de YARN y equilibrar la asignación de capacidad en todas las colas, siga estos pasos en Ambari. 

En este ejemplo, se cambia la capacidad de las dos colas existentes (**default** (predeterminado) y **thriftsvr**) del 50% al 25%, lo que proporciona una capacidad del 50% a la nueva cola (spark).
| Cola | Capacity | Capacidad máxima |
| --- | --- | --- | --- |
| default | 25 % | 50 % |
| thrftsvr | 25 % | 50 % |
| spark | 50 % | 50 % |

1. Seleccione el icono **Ambari Views** (Vistas de Ambari) y, a continuación, seleccione el patrón de cuadrícula. A continuación, seleccione **YARN Queue Manager** (Administrador de colas de YARN).

    ![Selección del icono Vistas de Ambari](media/hdinsight-troubleshoot-yarn/create-queue-1.png)
2. Seleccione la cola **default**.

    ![Selección de la cola default (predeterminada)](media/hdinsight-troubleshoot-yarn/create-queue-2.png)
3. Para la cola **default**, cambie la **capacidad** del 50% al 25%. Para la cola **thriftsvr**, cambie la **capacidad** al 25%.

    ![Cambio de la capacidad al 25 % para las colas default y thriftsvr](media/hdinsight-troubleshoot-yarn/create-queue-3.png)
4. Seleccione **Add Queue** (Agregar cola) para crear una nueva cola.

    ![Selección de Agregar cola](media/hdinsight-troubleshoot-yarn/create-queue-4.png)

5. Asigne un nombre a la cola nueva.

    ![Asignación del nombre Spark a la cola](media/hdinsight-troubleshoot-yarn/create-queue-5.png)  

6. Deje los valores de **Capacity** (Capacidad) en el 50 % y seleccione el botón **Actions** (Acciones).

    ![Selección del botón Actions (Acciones)](media/hdinsight-troubleshoot-yarn/create-queue-6.png)  
7. Seleccione **Save and Refresh Queues** (Guardar y actualizar colas).

    ![Selección de Save and Refresh Queues (Guardar y actualizar colas)](media/hdinsight-troubleshoot-yarn/create-queue-7.png)  

Estos cambios están visibles inmediatamente en la interfaz de usuario de YARN Scheduler.

### <a name="additional-reading"></a>Lecturas adicionales

- [YARN CapacityScheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)


## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Descarga de los registros de YARN desde un clúster


### <a name="resolution-steps"></a>Pasos de la solución 

1. Conéctese al clúster de HDInsight con un cliente Secure Shell (SSH). Para más información, consulte [Lecturas adicionales](#additional-reading-2).

2. Enumere todos los identificadores de aplicación de las aplicaciones Yarn que se están ejecutando con el siguiente comando:

    ```apache
    yarn top
    ```
    Los identificadores aparecen en la columna **APPLICATIONID**. Puede descargar los registros desde la columna **APPLICATIONID**.

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

3. Descargue los registros de contenedor de YARN para todos los maestros de aplicación con el siguiente comando:
   
    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    Este comando crea un archivo de registro denominado amlogs.txt. 

4. Descargue los registros de contenedor de YARN solo para el maestro de aplicación más reciente con el siguiente comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    Este comando crea un archivo de registro denominado latestamlogs.txt. 

4. Descargue los registros de contenedor de YARN para los dos primeros maestros de aplicación con el siguiente comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt 
    ```

    Este comando crea un archivo de registro denominado first2amlogs.txt. 

5. Descargue todos los registros de contenedor de YARN con el siguiente comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    Este comando crea un archivo de registro denominado logs.txt. 

6. Descargue el registro de YARN de un contenedor determinado con el siguiente comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt 
    ```

    Este comando crea un archivo de registro denominado containerlogs.txt.

### <a name="additional-reading-2"></a>Lecturas adicionales

- [Conexión a HDInsight (Hadoop) a través de SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)
- [Apache Hadoop Yarn concepts and applications](https://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/) (Conceptos y aplicaciones de YARN en Apache Hadoop)







