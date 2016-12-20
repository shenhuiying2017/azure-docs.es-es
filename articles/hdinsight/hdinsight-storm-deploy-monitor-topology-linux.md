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
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/07/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d5c33fc7d24eb2b14db9cdf3211a06e6fcc1cb68


---
# <a name="deploy-and-manage-apache-storm-topologies-on-linux-based-hdinsight"></a>Implementación y administración de topologías de Apache Storm en HDInsight basado en Linux
En este documento, conozca los aspectos básicos de administración y supervisión de las topologías de Storm que se ejecutan en clústeres de Storm basado en Linux en HDInsight.

> [!IMPORTANT]
> Para realizar los pasos que se describen en este artículo se requiere un clúster de Storm basado en Linux en HDInsight. Para obtener información sobre la implementación y la supervisar de topologías en HDInsight basado en Windows, vea [Implementar y administrar topologías de Apache Storm en HDInsight basado en Windows](hdinsight-storm-deploy-monitor-topology.md)
> 
> 

## <a name="prerequisites"></a>Requisitos previos
* **Clúster de Storm basado en Linux en HDInsight**: consulte [Introducción a Apache Storm en HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md) para conocer los pasos para crear un clúster.
* **Familiaridad con SSH y SCP**: para obtener más información sobre el uso de SSH y SCP con HDInsight, vea lo siguiente:
  
  * **Clientes Linux, Unix u OS X**: See [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X (vista previa)](hdinsight-hadoop-linux-use-ssh-unix.md)
  * **Clientes Windows**: vea [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows (vista previa)](hdinsight-hadoop-linux-use-ssh-windows.md)
* **Cliente de SCP**: se ofrece con todos los sistemas Linux, Unix y OS X. Para clientes Windows, se recomienda PSCP, que está disponible en la [página de descarga de PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

## <a name="start-a-storm-topology"></a>Iniciar una topología de Storm
### <a name="using-ssh-and-the-storm-command"></a>Uso de SSH y el comando Storm
1. Use SSH para conectarse al clúster de HDInsight. Reemplace **USERNAME** por el nombre de su inicio de sesión de SSH. Reemplace **CLUSTERNAME** por el nombre del clúster de HDInsight.
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Para obtener más información sobre el uso de SSH para conectarse a su clúster de HDInsight, vea los siguientes documentos:
   
   * **Clientes Linux, Unix u OS X**: See [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X (vista previa)](hdinsight-hadoop-linux-use-ssh-unix.md)
   * **Clientes Windows**: vea [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows (vista previa)](hdinsight-hadoop-linux-use-ssh-windows.md)
2. Use el comando siguiente para iniciar una topología de ejemplo:
   
        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.9.3.2.2.4.9-1.jar storm.starter.WordCountTopology WordCount
   
    Esto iniciará la topología WordCount de ejemplo en el clúster. Generará frases aleatoriamente y contará la aparición de cada palabra en las oraciones.
   
   > [!NOTE]
   > Al enviar la topología al clúster, primero debe copiar el archivo jar que contiene el clúster antes de usar el comando `storm`. Esto se puede lograr mediante el comando `scp` desde el cliente donde se encuentra el archivo. Por ejemplo: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
   > 
   > El ejemplo de WordCount, y otros ejemplos de inicio de Storm, ya están incluidos en el clúster en `/usr/hdp/current/storm-client/contrib/storm-starter/`.
   > 
   > 

### <a name="programmatically"></a>De manera programática
Mediante programación, puede implementar una topología en Storm en HDInsight estableciendo una comunicación con el servicio Nimbus hospedado en el clúster. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) proporciona un ejemplo de aplicación de Java que muestra cómo implementar e iniciar una topología a través del servicio Nimbus.

## <a name="monitor-and-manage-using-the-storm-command"></a>Supervisar y administrar mediante el comando de Storm
La utilidad `storm` le permite trabajar con topologías en ejecución desde la línea de comandos. La siguiente es una lista de comandos usados habitualmente. Use `storm -h` para obtener una lista completa de comandos.

### <a name="list-topologies"></a>Topologías de lista
Use el siguiente comando para enumerar todas las topologías en ejecución:

    storm list

Devolverá información similar a la siguiente:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Desactivar y reactivar
Al desactivar una topología se pone en pausa hasta que se elimine o se reactive. Use lo siguiente para desactivar y reactivar:

    storm Deactivate TOPOLOGYNAME

    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Eliminar una topología de ejecución
Las topologías de Storm, una vez iniciadas, se seguirán ejecutando hasta que se detenga. Para detener una topología, use el siguiente comando:

    storm stop TOPOLOGYNAME

### <a name="rebalance"></a>Reequilibrar
El reequilibrio de una topología permite que el sistema revise el paralelismo de la topología. Por ejemplo, si ha cambiado el tamaño del clúster para agregar más notas, el reequilibrio permitirá una topología en ejecución para usar los nuevos nodos.

> [!WARNING]
> El reequilibrio de una topología desactiva primero la topología, redistribuye los trabajadores de manera uniforme en el clúster y luego devuelve finalmente la topología al estado en el que se encontraba antes de que se produjera el reequilibrio. Por tanto, si la topología estaba activa, se activará de nuevo. Si se ha desactivado, seguirá desactivada.
> 
> 

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-using-the-storm-ui"></a>Supervisar y administrar mediante la interfaz de usuario de Storm
La interfaz de usuario de Storm ofrece una interfaz web para trabajar con topologías en ejecución y se incluye en el clúster de HDInsight. Para ver la IU de Storm, use un explorador web para abrir **https://CLUSTERNAME.azurehdinsight.net/stormui**, donde **CLUSTERNAME** es el nombre del clúster.

> [!NOTE]
> Si se le pide que ofrezca un nombre de usuario y una contraseña, escriba el administrador de clústeres (admin) y la contraseña que usó al crear el clúster.
> 
> 

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
  * **Reequilibrar**: ajusta el paralelismo de la topología. Debe volver a equilibrar las topologías en ejecución después de haber cambiado el número de nodos del clúster. Esto permite que la topología ajuste el paralelismo para compensar el mayor o menor número de nodos del clúster.
    
    Para obtener más información, consulte <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Descripción del paralelismo de una topología de Storm</a>.
  * **Eliminar**: finaliza una topología de Storm tras el tiempo de espera especificado.
* **Estadísticas de topología**: estadísticas sobre la topología. Use los vínculos de la columna **Ventana** para establecer el plazo de tiempo para las entradas restantes en la página.
* **Spouts**: los spouts que usa la topología. Use los vínculos de esta sección para obtener más información acerca de spouts específicos.
* **Bolts**: los bolts que usa la topología. Use los vínculos de esta sección para obtener más información acerca de bolts específicos.
* **Configuración de la topología**: la configuración de la topología seleccionada.

### <a name="spout-and-bolt-summary"></a>Resumen de spouts y bolts
Si se selecciona un spout en la sección **Spouts** o **Bolts**, se muestra la siguiente información sobre el elemento seleccionado:

* **Resumen de componentes**: información básica acerca del spout o bolt.
* **Estadísticas de spouts/bolts**: estadísticas sobre el spout o bolt. Use los vínculos de la columna **Ventana** para establecer el plazo de tiempo para las entradas restantes en la página.
* **Estadísticas de entrada** (solo bolt): información sobre las secuencias de entrada consumidas por el bolt.
* **Estadísticas de salida**: información sobre las secuencias emitidas por este spout o bolt
* **Ejecutores**: información sobre las instancias del spout o bolt. Seleccione la entrada **Puerto** de un ejecutor específico para ver un registro de información de diagnóstico generado por esta instancia.
* **Errores**: cualquier información de error de este spout o bolt.

## <a name="rest-api"></a>API de REST
La interfaz de usuario de Storm se basa en la API de REST, lo que permite realizar una funcionalidad similar de administración y supervisión mediante la API de REST. Puede usar la API de REST para crear herramientas personalizadas para administrar y supervisar las topologías de Storm.

Para más información, vea la [API de REST de la IU de Storm](http://storm.apache.org/releases/0.9.6/STORM-UI-REST-API.html). La siguiente información es específica para usar la API de REST con Apache Storm en HDInsight.

> [!IMPORTANT]
> La API de REST de Storm no está públicamente disponible a través de Internet y debe tener acceso mediante un túnel SSH en el nodo principal del clúster de HDInsight. Para obtener información sobre la creación y el uso de un túnel SSH, vea [Uso de la tunelización SSH para tener acceso a la interfaz de usuario web de Ambari, ResourceManager, JobHistory, NameNode, Oozie y otras interfaces de usuario web](hdinsight-linux-ambari-ssh-tunnel.md).
> 
> 

### <a name="base-uri"></a>URI base
El URI base para la API de REST en clústeres de HDInsight basado en Linux está disponible en el nodo principal en **https://HEADNODEFQDN:8744/api/v1/**; sin embargo, el nombre de dominio del nodo principal se genera durante la creación del clúster y no es estático.

Puede encontrar el nombre de dominio completo (FQDN) del nodo principal del clúster de varias maneras distintas:

* **Desde una sesión de SSH**: use el comando `headnode -f` desde una sesión SSH al clúster.
* **Desde Web de Ambari**: seleccione **Servicios** en la parte superior de la pantalla y, luego, seleccione **Storm**. En la pestaña **Resumen**, seleccione **Storm UI Server** (Servidor de IU de Storm). El FQDN del nodo donde se ejecutan IU de Storm y la API de REST aparecerá en la parte superior de la página.
* **Desde la API de REST de Ambari**: use el comando `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` para recuperar información sobre el nodo en que se ejecutan la IU de Storm y la API de REST. Reemplace **PASSWORD** por la contraseña de administrador del clúster. Reemplace **CLUSTERNAME** por el nombre del clúster. En la respuesta, la entrada "host_name" contiene el FQDN del nodo.

### <a name="authentication"></a>Autenticación
Las solicitudes a la API de REST deben usar la **autenticación básica**; use el nombre y la contraseña de administrador del clúster de HDInsight.

> [!NOTE]
> Dado que la autenticación básica se envía mediante texto no cifrado, **siempre** debe usar HTTPS para proteger las comunicaciones con el clúster.
> 
> 

### <a name="return-values"></a>Valores devueltos
La información que se devuelve de la API de REST solo se puede utilizar desde dentro del clúster o de máquinas virtuales en la misma red virtual de Azure que el clúster. Por ejemplo, no se podrá obtener el acceso desde Internet al nombre de dominio completo (FQDN) devuelto para servidores de Zookeeper.

## <a name="next-steps"></a>Pasos siguientes
Ahora que aprendió a implementar y supervisar topologías mediante el panel de Storm, aprenda a [Desarrollar topologías basadas en Java mediante Maven](hdinsight-storm-develop-java-topology.md).

Para obtener una lista con más topologías de ejemplo, consulte [Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md).




<!--HONumber=Nov16_HO3-->


