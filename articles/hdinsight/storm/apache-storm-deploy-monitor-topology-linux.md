---
title: "Implementación y administración de topologías de Apache Storm en HDInsight basado en Linux | Microsoft Docs"
description: "Aprenda a implementar, supervisar y administrar topologías de Apache Storm mediante el panel de Storm en HDInsight basado en Linux. Utilice herramientas de Hadoop para Visual Studio"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 35086e62-d6d8-4ccf-8cae-00073464a1e1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: larryfr
ms.openlocfilehash: a972344e2b6205fbcf69d2969c42211ec5b24869
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="deploy-and-manage-apache-storm-topologies-on-hdinsight"></a>Implementación y administración de topologías de Apache Storm en HDInsight

En este documento, aprenderá los aspectos básicos de administración y supervisión de las topologías de Storm que se ejecutan en clústeres de Storm en HDInsight.

> [!IMPORTANT]
> Para realizar los pasos que se describen en este artículo se requiere un clúster de Storm basado en Linux en HDInsight. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement). 
>
> Para obtener información sobre la implementación y la supervisar de topologías en HDInsight basado en Windows, vea [Implementar y administrar topologías de Apache Storm en HDInsight basado en Windows](apache-storm-deploy-monitor-topology.md)


## <a name="prerequisites"></a>Requisitos previos

* **Clúster de Storm basado en Linux en HDInsight**: consulte [Introducción a Apache Storm en HDInsight](apache-storm-tutorial-get-started-linux.md) para conocer los pasos para crear un clúster.

* **Familiaridad con SSH y SCP** (opcional): para obtener más información, consulte [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

* (Opcional) **Visual Studio**: SDK de Azure 2.5.1 o versiones más recientes y Herramientas de Data Lake para Visual Studio. Para más información, consulte [Introducción al uso de Herramientas de Data Lake para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    Una de las siguientes versiones de Visual Studio:

  * Visual Studio 2012 con [Update 4](http://www.microsoft.com/download/details.aspx?id=39305)

  * Visual Studio 2013 con [Update 4](http://www.microsoft.com/download/details.aspx?id=44921) o [Visual Studio Community 2013](http://go.microsoft.com/fwlink/?LinkId=517284)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/)

  * Visual Studio 2015 (cualquier edición)

  * Visual Studio 2017 (cualquier edición). Herramientas de Data Lake para Visual Studio 2017 se instalan como parte de la carga de trabajo de Azure.

## <a name="submit-a-topology-visual-studio"></a>Envío de una topología: Visual Studio

Las herramientas de HDInsight puede utilizarse para enviar las topologías de C# o híbridas al clúster de Storm. Los pasos siguientes usan una aplicación de muestra. Para obtener información sobre cómo crear o utilizar las herramientas de HDInsight, consulte [Desarrollo de las topologías de C# mediante las herramientas de HDInsight para Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

1. Si todavía no tiene instalada la versión más reciente de las herramientas de Data Lake para Visual Studio, consulte [Introducción al uso de Herramientas de Data Lake para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]
    > Herramientas de Data Lake para Visual Studio anteriormente se llamaban Herramientas de HDInsight para Visual Studio.
    >
    > Herramientas de Data Lake para Visual Studio están incluidas en la __carga de trabajo de Azure__ para Visual Studio 2017.

2. Abra Visual Studio, seleccione **Archivo** > **Nuevo** > **Proyecto**.

3. En el cuadro de diálogo **Nuevo proyecto**, expanda **Instalado** > **Plantillas** y seleccione **HDInsight**. En la lista de plantillas, seleccione **Muestra de Storm**. En la parte inferior del cuadro de diálogo, escriba un nombre para la aplicación.

    ![imagen](./media/apache-storm-deploy-monitor-topology-linux/sample.png)

4. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Submit to Storm on HDInsight** (Enviar a Storm en HDInsight).

   > [!NOTE]
   > Si se le solicita, introduzca las credenciales de inicio de sesión de su suscripción de Azure. Si tiene más de una suscripción, inicie sesión en la que contenga el clúster de Storm en HDInsight.

5. Seleccione el clúster de Storm en HDInsight desde el menú desplegable **Storm Cluster** (Clúster de Storm y seleccione **Submit** (Enviar). Puede supervisar si el envío es correcto mediante la ventana **Salida** .

## <a name="submit-a-topology-ssh-and-the-storm-command"></a>Envío de una topología: SSH y el comando Storm

1. Use SSH para conectarse al clúster de HDInsight. Reemplace **USERNAME** por el nombre de su inicio de sesión de SSH. Reemplace **CLUSTERNAME** por el nombre del clúster de HDInsight.

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Para obtener más información sobre cómo establecer una conexión mediante SSH a su clúster de HDInsight, consulte [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Use el comando siguiente para iniciar una topología de ejemplo:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount

    Este comando inicia la topología WordCount de ejemplo en el clúster. Esta topología generará frases de forma aleatoria y contará la aparición de cada palabra en las oraciones.

   > [!NOTE]
   > Al enviar la topología al clúster, primero debe copiar el archivo jar que contiene el clúster antes de usar el comando `storm`. Para copiar el archivo en el clúster, puede usar el comando `scp`. Por ejemplo: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
   >
   > El ejemplo de WordCount, y otros ejemplos de inicio de Storm, ya están incluidos en el clúster en `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Envío de una topología: de manera programática

Puede implementar mediante programación una topología con el servicio Nimbus. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) proporciona un ejemplo de aplicación de Java que muestra cómo implementar e iniciar una topología a través del servicio Nimbus.

## <a name="monitor-and-manage-visual-studio"></a>Supervisión y administración: Visual Studio

Cuando una topología se envía correctamente con Visual Studio, aparece la vista **Topologías de Storm**. Seleccione la topología de la lista para ver información acerca de la topología de ejecución.

![supervisión de visual studio](./media/apache-storm-deploy-monitor-topology-linux/vsmonitor.png)

> [!NOTE]
> También puede ver las **topologías de Storm** en el **Explorador de servidores** expandiendo **Azure** > **HDInsight** y, después, haciendo clic con el botón derecho en un clúster de Storm en HDInsight y seleccionando **View Storm Topologies** (Ver topologías de Storm).

Seleccione la forma de los spouts o bolts para ver información sobre estos componentes. Se abrirá una ventana nueva para cada elemento seleccionado.

### <a name="deactivate-and-reactivate"></a>Desactivar y reactivar

Al desactivar una topología se pone en pausa hasta que se elimine o se reactive. Para realizar estas operaciones, use los botones __Desactivar__ y __Reactivar__ que se encuentra en la parte superior del __Resumen de las topologías__.

### <a name="rebalance"></a>Reequilibrar

El reequilibrio de una topología permite que el sistema revise el paralelismo de la topología. Por ejemplo, si cambió el tamaño del clúster para agregar más notas, el reequilibrio permite que una topología vea los nodos nuevos.

Para reequilibrar una topología, use el botón __Reequilibrar__ que se encuentra en la parte superior del __Resumen de las topologías__.

> [!WARNING]
> El reequilibrio de una topología desactiva primero la topología, redistribuye los trabajadores de manera uniforme en el clúster y luego devuelve finalmente la topología al estado en el que se encontraba antes de que se produjera el reequilibrio. Por tanto, si la topología estaba activa, se activa de nuevo. Si se desactivó, seguirá desactivada.

### <a name="kill-a-topology"></a>Terminación de una topología

Las topologías de Storm continúan ejecutándose hasta que se detengan o se elimine el clúster. Para detener una topología, use el botón __Terminar__ que se encuentra en la parte superior del __Resumen de las topologías__.

## <a name="monitor-and-manage-ssh-and-the-storm-command"></a>Supervisión y administración: SSH y el comando Storm

La utilidad `storm` le permite trabajar con topologías en ejecución desde la línea de comandos. Use `storm -h` para obtener una lista completa de comandos.

### <a name="list-topologies"></a>Topologías de lista

Use el siguiente comando para enumerar todas las topologías en ejecución:

    storm list

Este comando devuelve información similar al texto siguiente:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Desactivar y reactivar

Al desactivar una topología se pone en pausa hasta que se elimine o se reactive. Use el comando siguiente para desactivar y reactivar:

    storm Deactivate TOPOLOGYNAME

    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Eliminar una topología de ejecución

Las topologías de Storm, una vez iniciadas, se siguen ejecutando hasta que se detengan. Para detener una topología, use el siguiente comando:

    storm kill TOPOLOGYNAME

### <a name="rebalance"></a>Reequilibrar

El reequilibrio de una topología permite que el sistema revise el paralelismo de la topología. Por ejemplo, si cambió el tamaño del clúster para agregar más notas, el reequilibrio permite que una topología vea los nodos nuevos.

> [!WARNING]
> El reequilibrio de una topología desactiva primero la topología, redistribuye los trabajadores de manera uniforme en el clúster y luego devuelve finalmente la topología al estado en el que se encontraba antes de que se produjera el reequilibrio. Por tanto, si la topología estaba activa, se activa de nuevo. Si se desactivó, seguirá desactivada.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-storm-ui"></a>Supervisión y administración: IU de Storm

La interfaz de usuario de Storm ofrece una interfaz web para trabajar con topologías en ejecución y se incluye en el clúster de HDInsight. Para ver la IU de Storm, use un explorador web para abrir **https://CLUSTERNAME.azurehdinsight.net/stormui**, donde **CLUSTERNAME** es el nombre del clúster.

> [!NOTE]
> Si se le pide que ofrezca un nombre de usuario y una contraseña, escriba el administrador de clústeres (admin) y la contraseña que usó al crear el clúster.

### <a name="main-page"></a>Página principal

La página principal de la interfaz de usuario de Storm ofrece la siguiente información:

* **Resumen del clúster**: información básica sobre el clúster de Storm.
* **Resumen de las topologías**: una lista de las topologías en ejecución. Use los vínculos de esta sección para obtener más información sobre las topologías específicas.
* **Resumen de supervisor**: información acerca del supervisor de Storm.
* **Configuración de Nimbus**: configuración de Nimbus del clúster.

### <a name="topology-summary"></a>Resumen de las topologías

Si selecciona un vínculo desde la sección **Resumen de la topología** , se mostrará la siguiente información sobre la topología.

* **Resumen de la topología**: información básica sobre la topología.
* **Acciones de topología**: acciones de administración que puede realizar para la topología.

  * **Activar**: reanuda el procesamiento de una topología desactivada.
  * **Desactivar**: pausa una topología en ejecución.
  * **Reequilibrar**: ajusta el paralelismo de la topología. Debe volver a equilibrar las topologías en ejecución después de haber cambiado el número de nodos del clúster. Esta operación permite que la topología ajuste el paralelismo para compensar el mayor o menor número de nodos del clúster.

    Para más información, consulte la entrada de blog <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Understanding the parallelism of a Storm topology</a>.
  * **Eliminar**: finaliza una topología de Storm tras el tiempo de espera especificado.
* **Estadísticas de topología**: estadísticas sobre la topología. Para establecer el período de las entradas restantes en la página, use los vínculos que se encuentran en la columna **Ventana**.
* **Spouts**: los spouts que usa la topología. Use los vínculos de esta sección para obtener más información acerca de spouts específicos.
* **Bolts**: los bolts que usa la topología. Use los vínculos de esta sección para obtener más información acerca de bolts específicos.
* **Configuración de la topología**: la configuración de la topología seleccionada.

### <a name="spout-and-bolt-summary"></a>Resumen de spouts y bolts

Si se selecciona un spout en la sección **Spouts** o **Bolts**, se muestra la siguiente información sobre el elemento seleccionado:

* **Resumen de componentes**: información básica acerca del spout o bolt.
* **Estadísticas de spouts/bolts**: estadísticas sobre el spout o bolt. Para establecer el período de las entradas restantes en la página, use los vínculos que se encuentran en la columna **Ventana**.
* **Estadísticas de entrada** (solo bolt): información sobre las secuencias de entrada consumidas por el bolt.
* **Estadísticas de salida**: información sobre las secuencias emitidas por este spout o bolt.
* **Ejecutores**: información sobre las instancias del spout o bolt. Seleccione la entrada **Puerto** de un ejecutor específico para ver un registro de información de diagnóstico generado por esta instancia.
* **Errores**: cualquier información de error de este spout o bolt.

## <a name="monitor-and-manage-rest-api"></a>Supervisión y administración: API de REST

La interfaz de usuario de Storm se basa en la API de REST, lo que permite realizar una funcionalidad similar de administración y supervisión mediante la API de REST. Puede usar la API de REST para crear herramientas personalizadas para administrar y supervisar las topologías de Storm.

Para más información, vea la [API de REST de la IU de Storm](http://storm.apache.org/releases/0.9.6/STORM-UI-REST-API.html). La siguiente información es específica para usar la API de REST con Apache Storm en HDInsight.

> [!IMPORTANT]
> La API de REST de Storm no está públicamente disponible a través de Internet y debe tener acceso mediante un túnel SSH en el nodo principal del clúster de HDInsight. Para información sobre la creación y el uso de un túnel SSH, consulte [Uso de la tunelización SSH para tener acceso a la interfaz de usuario web de Ambari, ResourceManager, JobHistory, NameNode, Oozie y otras interfaces de usuario web](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>URI base

El URI base de la API de REST en los clústeres de HDInsight basado en Linux está disponible en el nodo principal en **https://HEADNODEFQDN:8744/api/v1/**. El nombre de dominio del nodo principal se genera durante la creación de clúster y no es estático.

Puede encontrar el nombre de dominio completo (FQDN) del nodo principal del clúster de varias maneras distintas:

* **Desde una sesión de SSH**: use el comando `headnode -f` desde una sesión SSH al clúster.
* **Desde Web de Ambari**: seleccione **Servicios** en la parte superior de la pantalla y, luego, seleccione **Storm**. En la pestaña **Resumen**, seleccione **Storm UI Server** (Servidor de IU de Storm). El FQDN del nodo que hospeda la IU de Storm y la API de REST se muestra en la parte superior de la página.
* **Desde la API de REST de Ambari**: use el comando `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` para recuperar información sobre el nodo en que se ejecutan la IU de Storm y la API de REST. Reemplace **PASSWORD** por la contraseña de administrador del clúster. Reemplace **CLUSTERNAME** por el nombre del clúster. En la respuesta, la entrada "host_name" contiene el FQDN del nodo.

### <a name="authentication"></a>Autenticación

Las solicitudes a la API de REST deben usar la **autenticación básica**; use el nombre y la contraseña de administrador del clúster de HDInsight.

> [!NOTE]
> Dado que la autenticación básica se envía mediante texto no cifrado, **siempre** debe usar HTTPS para proteger las comunicaciones con el clúster.

### <a name="return-values"></a>Valores devueltos

La información que devuelve la API de REST solo puede utilizarse desde el clúster. Por ejemplo, no se puede obtener el acceso desde Internet al nombre de dominio completo (FQDN) devuelto para servidores de Zookeeper.

## <a name="next-steps"></a>Pasos siguientes

Aprenda a [desarrollar topologías basadas en Java con Maven](apache-storm-develop-java-topology.md).

Para obtener una lista con más topologías de ejemplo, consulte [Topologías de ejemplo para Storm en HDInsight](apache-storm-example-topology.md).
