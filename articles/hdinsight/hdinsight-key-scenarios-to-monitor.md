---
title: "Supervisión del rendimiento de un clúster: Azure HDInsight | Microsoft Docs"
description: "Supervisión de la capacidad y rendimiento de un clúster de HDInsight."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: maxluk
ms.openlocfilehash: f2333202cdccc82edea649ff1c295752a414c8b8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-cluster-performance"></a>Supervisión del rendimiento de un clúster

La supervisión del mantenimiento y rendimiento de un clúster de HDInsight es esencial para mantener el máximo rendimiento y utilización de los recursos. La supervisión también puede ayudarle a solucionar posibles errores de codificación o de configuración de un clúster.

En las secciones siguientes se describe cómo optimizar la carga de clústeres, la eficacia de la cola YARN y la accesibilidad del almacenamiento.

## <a name="cluster-loading"></a>Carga de clústeres

Los clústeres de Hadoop deben equilibrar la carga entre los nodos del clúster. Este equilibrio impide que la RAM, la CPU o los recursos de disco limiten las tareas de procesamiento.

Para obtener una visión de alto nivel de los nodos de un clúster y su carga, inicie sesión en la [interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md) y seleccione la pestaña **Hosts**. Los hosts se enumeran por sus nombres de dominio completos. El estado de funcionamiento de cada host se muestra mediante un indicador de mantenimiento de color:

| Color | Descripción |
| --- | --- |
| Rojo | Al menos un componente maestro del host está inactivo. Mueva el puntero encima para una información sobre herramientas que enumera los componentes afectados. |
| Naranja | Al menos un componente subordinado del host está inactivo. Mueva el puntero encima para una información sobre herramientas que enumera los componentes afectados. |
| Amarillo | El servidor de Ambari no recibió ningún latido del host durante más de 3 minutos. |
| Verde | Estado de funcionamiento normal. |

También verá columnas que muestra el número de núcleos y la cantidad de RAM de cada host, así como el uso del disco y el promedio de carga.

![Pestaña Hosts](./media/hdinsight-key-scenarios-to-monitor/hosts-tab.png)

Seleccione cualquiera de los nombres de host para tener una vista detallada de los componentes que se ejecutan en el host y su métrica. La métrica se muestra como una escala de tiempo seleccionable de uso de la CPU, carga, uso del disco, uso de la memoria, uso de la red y números de procesos.

![Detalles del host](./media/hdinsight-key-scenarios-to-monitor/host-details.png)

Para más información acerca del establecimiento de alertas y visualización de métrica, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="yarn-queue-configuration"></a>Configuración de la cola de YARN

Hadoop tiene varios servicios que se ejecutan a través de su plataforma distribuida. YARN (del inglés Yet Another Resource Negotiator) coordina estos servicios, asigna recursos de los clústeres y administra el acceso a un conjunto de datos común.

YARN divide las responsabilidades de JobTracker, la administración de recursos y la programación y supervisión de recursos, en dos demonios: ResourceManager global y ApplicationMaster (AM) por aplicación.

ResourceManager es un *programador puro* y únicamente arbitra los recursos disponibles entre todas las aplicaciones que compiten por ellos. ResourceManager garantiza que todos los recursos están siempre en uso y se optimizan para varias constantes como SLA, garantías de capacidad, etc. ApplicationMaster negocia los recursos de ResourceManager y funciona con NodeManager para ejecutar y supervisar los contenedores y su consumo de recursos.

Cuando varios inquilinos comparten un clúster grande, compiten por sus recursos. CapacityScheduler es un programador acoplable que sirve de ayuda al compartir recursos, ya que ponen las solicitudes en cola. CapacityScheduler también admite *colas jerárquicas* para asegurarse de que los recursos se comparten entre las subcolas de una organización, antes de que se permita a las colas de otras aplicaciones utilizar los recursos libres.

YARN nos permite asignar recursos a estas colas y muestra si todos los recursos disponibles están asignados. Para ver información acerca de las colas, inicie sesión en la interfaz de usuario web de Ambari y, después, seleccione **YARN Queue Manager** (Administrador de colas de YARN) en el menú superior.

![YARN Queue Manager (Administrador de colas de YARN)](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager.png)

La página YARN Queue Manager (Administrador de colas de YARN) muestra una lista de las colas, a la izquierda, y el porcentaje de la capacidad que se asigna a cada una.

![Página de detalles de YARN Queue Manager (Administrador de colas de YARN)](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

Para obtener una visión más detallada de las colas, seleccione en el panel de Ambari el servicio **YARN** en la lista de la izquierda. Después, en el menú desplegable **Quick Links** (Vínculos rápidos), seleccione **ResourceManager UI** (Interfaz de usuario de ResourceManager) debajo del nodo activo.

![Vínculo del menú ResourceManager UI (Interfaz de usuario de ResourceManager)](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

En ResourceManager UI (Interfaz de usuario de ResourceManager), seleccione **Scheduler** (Programador) en el menú de la izquierda. Se ve una lista de las colas debajo de *Application Queues* (Colas de aplicación). Aquí puede ver la capacidad que se usa para cada una de las colas, cómo se distribuyen los trabajos entre ellas y si alguno de los trabajos tiene los recursos restringidos.

![Vínculo del menú ResourceManager UI (Interfaz de usuario de ResourceManager)](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui.png)

## <a name="storage-throttling"></a>Limitación del almacenamiento

Se puede producir un cuello de botella de rendimiento de un clúster a nivel de almacenamiento. Con mucha frecuencia, este tipo de cuello de botella se debe al *bloqueo* de operaciones de entrada y salida (E/S), que se producen cuando las tareas en ejecución envían más E/S de lo que el servicio de almacenamiento puede controlar. Este bloqueo crea una cola de solicitudes de E/S que esperan a que se procesen las E/S actuales para procesarse. Los bloqueos se deben a una *limitación del almacenamiento*, que no es un límite físico, sino más bien un límite que impone el servicio de almacenamiento mediante un Acuerdo de Nivel de Servicio (SLA). Este límite garantiza que ningún cliente o inquilino individuales pueden monopolizar el servicio. El Acuerdo de Nivel de Servicio limita el número de operaciones de E/S por segundo (IOPS) de Azure Storage (para más información, consulte [Objetivos de escalabilidad y rendimiento de Azure Storage](https://docs.microsoft.com/azure/storage/storage-scalability-targets)).

Si usa Azure Storage, para obtener información acerca de la supervisión de problemas relacionados con el almacenamiento, incluida la limitación, consulte el artículo [Supervisión, diagnóstico y solución de problemas de Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Si la memoria auxiliar de su clúster es Azure Data Lake Store (ADLS), lo más probable es que su limitación se deba al ancho de banda. En este caso, la limitación puede identificarse mediante la observación de los errores de limitación en los registros de tarea. En el caso de ADLS, consulte la sección relativa a la limitación del servicio apropiado en estos artículos:

* [Guía para la optimización del rendimiento de Hive en HDInsight y Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Guía para la optimización del rendimiento de MapReduce en HDInsight y Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Guía para la optimización del rendimiento de Storm en HDInsight y Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de la solución de problemas y supervisión de los clústeres, visite los siguientes vínculos:

* [Analizar los registros de HDInsight](hdinsight-debug-jobs.md)
* [Depuración de aplicaciones con registros YARN](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Habilitar los volcados de montón de los servicios de Hadoop en HDInsight basado en Linux (vista previa)](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
