---
title: "Acceso a registros de aplicación de YARN de Hadoop en HDInsight basado en Linux - Azure | Microsoft Docs"
description: "Obtenga información acerca de cómo tener acceso a los registros de aplicaciones de YARN en un clúster de HDInsight (Hadoop) basado en Linux, mediante la línea de comandos y un explorador web."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 3ec08d20-4f19-4a8e-ac86-639c04d2f12e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: larryfr
ms.openlocfilehash: 5f61e94d37a5e5f958a706f0db82526996a4ec02
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="access-yarn-application-logs-on-linux-based-hdinsight"></a>Acceso a registros de aplicación de YARN en HDInsight basado en Linux

Aprenda a acceder a los registros de aplicaciones YARN (del inglés Yet Another Resource Negotiator) en un clúster Hadoop en Azure HDInsight.

> [!IMPORTANT]
> Los pasos descritos en este documento requieren un clúster de HDInsight que use Linux. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para obtener más información, consulte el artículo relativo al [control de versiones de componentes de HDInsight](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="YARNTimelineServer"></a>Servidor de escala de tiempo de YARN

El [Servidor de escala de tiempo de YARN](http://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) ofrece información genérica sobre las aplicaciones completadas e información de aplicaciones específicas del marco a través de dos interfaces diferentes. Concretamente:

* Se ha habilitado el almacenamiento y la recuperación de información de aplicaciones genéricas en clústeres de HDInsight con la versión 3.1.1.374 o superiores.
* El componente de información de aplicaciones específica del marco del servidor de la escala de tiempo no está disponible actualmente en los clústeres de HDInsight.

La información genérica sobre las aplicaciones incluye los siguientes tipos de datos:

* El ID de la aplicación, que es el identificador único de una aplicación.
* El usuario que ha iniciado la aplicación.
* La información acerca de los intentos realizados para completar la aplicación.
* Los contenedores usados por cualquier intento de aplicación concreto.

## <a name="YARNAppsAndLogs"></a>Registros y aplicaciones de YARN

YARN admite varios modelos de programación (MapReduce es uno de ellos) al desacoplar la administración de recursos de la programación/supervisión de aplicaciones. Así, usa una instancia de *Resource Manager* (RM) global por nodo de trabajo *NodeManagers* (NM) y por aplicación *ApplicationMasters* (AM). El AM por aplicación negocia recursos (CPU, memoria, disco, red) para ejecutar la aplicación con el RM. El RM funciona con NM para conceder estos recursos como *contenedores*. El AM es responsable del seguimiento del progreso de los contenedores asignados a él por el RM. Una aplicación puede requerir muchos contenedores según la naturaleza de la aplicación.

Cada aplicación puede constar de varios *intentos de aplicación*. Si se produce un error en una aplicación, se puede reintentar como un nuevo intento. Cada intento se ejecuta en un contenedor. En cierto sentido, un contenedor proporciona el contexto de la unidad básica de trabajo realizado por una aplicación de YARN. Todo el trabajo que se realiza en el contexto de un contenedor se realiza solo en el nodo de trabajo en el que se asignó el contenedor. Consulte [Conceptos de YARN][YARN-concepts] como referencia adicional.

Los registros de aplicación (y los registros de contenedor asociados) son fundamentales en la depuración de las aplicaciones de Hadoop problemáticas. YARN proporciona un buen marco para recopilar, agregar y almacenar registros de aplicaciones con la característica de [agregación de registros][log-aggregation]. La característica Agregación de registro hace que el acceso a los registros de las aplicaciones sea más determinista. Esta característica agrega los registros en todos los contenedores de un nodo de trabajo y los almacena como un archivo de registros agregados por cada nodo de trabajo. El registro se almacena en el sistema de archivos de forma predeterminada una vez finalizada una aplicación. Su aplicación puede usar cientos o miles de contenedores, pero los registros para todos los contenedores que se ejecutan en un único nodo de trabajo se agregarán siempre a un único archivo. Por tanto, solo hay un registro por nodo de trabajo usado por la aplicación. La agregación de registros está habilitada de forma predeterminada en los clústeres de HDInsight de la versión 3.0 y superior. Los registros agregados se encuentran en el almacenamiento predeterminado del clúster. La siguiente ruta de acceso es la ruta de acceso de HDFS a los registros:

    /app-logs/<user>/logs/<applicationId>

En la ruta de acceso, `user` es el nombre del usuario que inició la aplicación. `applicationId` es el identificador único asignado a una aplicación mediante el RM de YARN.

Los registros agregados no son legibles directamente tal como se escriben en un [TFile][T-file], [formato binario][binary-format] indexado por el contenedor. Use los registros de ResourceManager de YARN o las herramientas de la CLI para ver estos registros como texto sin formato para aplicaciones o contenedores de interés.

## <a name="yarn-cli-tools"></a>Herramientas de la CLI de YARN

Para poder usar las herramientas de la CLI de YARN, tiene que conectarse primero al clúster de HDInsight mediante SSH. Para más información, consulte [Uso de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Puede ver estos registros como texto sin formato ejecutando uno de los siguientes comandos:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

Al ejecutar estos comandos, especifique la siguiente información: &lt;applicationId>, &lt;user-who-started-the-application>, &lt;containerId> y &lt;worker-node-address>.

## <a name="yarn-resourcemanager-ui"></a>Interfaz de usuario de ResourceManager de YARN

La interfaz de usuario de ResourceManager de YARN se ejecuta en el nodo principal del clúster. Se accede a ella a través de la interfaz de usuario web de Ambari. Para ver los registros de YARN, use los siguientes pasos:

1. Abra https://CLUSTERNAME.azurehdinsight.net en el explorador web. Reemplace CLUSTERNAME por el nombre del clúster de HDInsight.
2. En la lista de servicios de la izquierda de la página, seleccione **YARN**.

    ![Servicio de YARN seleccionado](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)
3. En la lista desplegable **Vínculos rápidos**, seleccione uno de los nodos del clúster principal y elija **Registro de ResourceManager**.

    ![Vínculos rápidos de YARN](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)

    Aparece una lista de vínculos a los registros de YARN.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
