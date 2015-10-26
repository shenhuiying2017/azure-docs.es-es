<properties
   pageTitle="Implementación y administración de topologías de Apache Storm en HDInsight basado en Linux | Microsoft Azure"
   description="Aprenda a implementar, supervisar y administrar topologías de Apache Storm mediante el panel de Storm en HDInsight basado en Linux. Utilice herramientas de Hadoop para Visual Studio"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/04/2015"
   ms.author="larryfr"/>

# Implementación y administración de topologías de Apache Storm en HDInsight basado en Linux

En este documento, conozca los aspectos básicos de administración y supervisión de las topologías de Storm que se ejecutan en clústeres de Storm basado en Linux en HDInsight.

> [AZURE.IMPORTANT]Para realizar los pasos que se describen en este artículo se requiere un clúster de Storm basado en Linux en HDInsight. Para obtener información sobre la implementación y la supervisar de topologías en HDInsight basado en Windows, vea [Implementar y administrar topologías de Apache Storm en HDInsight basado en Windows](hdinsight-storm-deploy-monitor-topology.md)

## Requisitos previos

- **Clúster de Storm basado en Linux en HDInsight**: consulte [Introducción a Apache Storm en HDInsight](hdinsight-storm-get-started-linux.md) para conocer los pasos para crear un clúster.

- **Familiaridad con SSH y SCP**: para obtener más información sobre el uso de SSH y SCP con HDInsight, vea lo siguiente:
    - **Clientes Linux, Unix u OS X**: vea [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X (vista previa)](hdinsight-hadoop-linux-use-ssh-unix.md).
    - **Clientes Windows**: vea [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows (vista previa)](hdinsight-hadoop-linux-use-ssh-windows.md)

- **Cliente de SCP**: se ofrece con todos los sistemas Linux, Unix y OS X. Para clientes Windows, se recomienda PSCP, que está disponible en la [página de descarga de PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

## Iniciar una topología de Storm

1. Use SSH para conectarse al clúster de HDInsight. Reemplace **USERNAME** por el nombre de su inicio de sesión de SSH. Reemplace **CLUSTERNAME** por el nombre del clúster de HDInsight.

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Para obtener más información sobre el uso de SSH para conectarse a su clúster de HDInsight, vea los siguientes documentos:
    
        - **Linux, Unix or OS X clients**: See [Use SSH with Linux-based Hadoop on HDInsight from Linux, OS X or Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
        
        - **Windows clients**: See [Use SSH with Linux-based Hadoop on HDInsight from Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Use el comando siguiente para iniciar una topología de ejemplo:

        storm jar storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.9.3.2.2.4.9-1.jar storm.starter.WordCountTopology WordCount

    Esto iniciará la topología WordCount de ejemplo en el clúster. Generará frases aleatoriamente y contará la aparición de cada palabra en las oraciones.

    > [AZURE.NOTE]Al enviar la topología al clúster, primero debe copiar el archivo jar que contiene el clúster antes de usar el comando `storm`. Esto se puede lograr mediante el comando `scp` desde el cliente donde se encuentra el archivo. Por ejemplo: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`.
    >
    > El ejemplo de WordCount, y otros ejemplos de inicio de Storm, ya están incluidos en el clúster en `/usr/hdp/current/storm-client/contrib/storm-starter/`.

##Supervisar y administrar mediante el comando de Storm

La utilidad `storm` le permite trabajar con topologías en ejecución desde la línea de comandos. La siguiente es una lista de comandos usados habitualmente. Use `storm -h` para obtener una lista completa de comandos.

###Topologías de lista

Use el siguiente comando para enumerar todas las topologías en ejecución:

    storm list
    
Devolverá información similar a la siguiente:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

###Desactivar y reactivar

Al desactivar una topología se pone en pausa hasta que se elimine o se reactive. Use lo siguiente para desactivar y reactivar:

    storm Deactivate TOPOLOGYNAME
    
    storm Activate TOPOLOGYNAME

###Eliminar una topología de ejecución

Las topologías de Storm, una vez iniciadas, se seguirán ejecutando hasta que se detenga. Para detener una topología, use el siguiente comando:

    storm stop TOPOLOGYNAME

###Reequilibrar

El reequilibrio de una topología permite que el sistema revise el paralelismo de la topología. Por ejemplo, si ha cambiado el tamaño del clúster para agregar más notas, el reequilibrio permitirá una topología en ejecución para usar los nuevos nodos.

> [AZURE.WARNING]El reequilibrio de una topología desactiva primero la topología, redistribuye los trabajadores de manera uniforme en el clúster y luego devuelve finalmente la topología al estado en el que se encontraba antes de que se produjera el reequilibrio. Por tanto, si la topología estaba activa, se activará de nuevo. Si se ha desactivado, seguirá desactivada.

    storm rebalance TOPOLOGYNAME

##Supervisar y administrar mediante la interfaz de usuario de Storm

La interfaz de usuario de Storm ofrece una interfaz web para trabajar con topologías en ejecución y se incluye en el clúster de HDInsight.

> [AZURE.IMPORTANT]La interfaz de usuario de Storm no está disponible públicamente a través de Internet y se debe tener acceso a ella mediante un túnel de SSH en el nodo principal del clúster de HDInsight. Para obtener información sobre la creación y el uso de un túnel SSH, vea [Uso de la tunelización SSH para tener acceso a la interfaz de usuario web de Ambari, ResourceManager, JobHistory, NameNode, Oozie y otras interfaces de usuario web](hdinsight-linux-ambari-ssh-tunnel.md).

Lleve a cabo los pasos siguientes para ver la interfaz de usuario de Storm:

1. Abra un explorador web en Web de Ambari para el clúster de HDInsight. La dirección URL Web de Ambari es https://CLUSTERNAME.azurehdinsight.net, donde __CLUSTERNAME__ es el nombre del clúster.

2. En la lista de servicios de la izquierda de la página, selecciona __Storm__. Después, selecciona __Interfaz de usuario de Storm__ de __Vínculos rápidos__.

    ![Entrada de la interfaz de usuario de Storm en vínculos rápidos](./media/hdinsight-storm-deploy-monitor-topology-linux/ambari-storm.png)

    Esta acción mostrará la página web de la interfaz de usuario de Storm:

    ![la interfaz de usuario de storm](./media/hdinsight-storm-deploy-monitor-topology-linux/storm-ui.png)

> [AZURE.NOTE]Al trabajar con la interfaz de usuario de Storm, puede que observe que algunas versiones de Internet Explorer no actualicen correctamente la interfaz de usuario después de que haberla visitado por primera vez. Por ejemplo, puede que no se muestren las topologías nuevas que haya enviado o puede que se muestre una topología como activa, aunque anteriormente la desactivase. Microsoft conoce este problema y está trabajando para conseguir una solución.

### Página principal

La página principal de la interfaz de usuario de Storm ofrece la siguiente información: - **Resumen de clúster**: información básica sobre el clúster de Storm. - **Resumen de la topología**: lista de las topologías de ejecución. Use los vínculos de esta sección para ver más información sobre topologías específicas. - **Resumen de supervisor**: información sobre el supervisor de Storm. - **Configuración de Nimbus**: configuración de Nimbus para el clúster.

### Resumen de la topología

Al seleccionar un vínculo en la sección **Resumen de la topología** se muestra la siguiente información sobre la topología: - **Resumen de la topología**: información básica sobre la topología. - **Acciones de topología**: acciones de administración que puede realizar para la topología. - **Activar**:reanuda el procesamiento de una topología desactivada. - **Desactivar**: pausa una topología en ejecución. - **Reequilibrar**: ajusta el paralelismo de la topología. Debe volver a equilibrar las topologías en ejecución después de haber cambiado el número de nodos del clúster. Esto permite que la topología ajuste el paralelismo para compensar el mayor o menor número de nodos del clúster.

      For more information, see <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Understanding the parallelism of a Storm topology</a>.

  - **Eliminar**: finaliza una topología de Storm tras el tiempo de espera especificado.

- **Estadísticas de topología**: estadísticas sobre la topología. Use los vínculos de la columna **Ventana** para establecer el plazo de tiempo para las entradas restantes en la página.
- **Spouts**: los spouts que usa la topología. Use los vínculos de esta sección para obtener más información acerca de spouts específicos.
- **Bolts**: los bolts que usa la topología. Use los vínculos de esta sección para obtener más información acerca de bolts específicos.
- **Configuración de la topología**: la configuración de la topología seleccionada.

### Resumen de spouts y bolts

Al seleccionar un spout desde las secciones **Spouts** o **Bolts** se muestra la siguiente información sobre el elemento seleccionado: - **Resumen de componentes**: información básica sobre el spout o bolt. - **Estadísticas de spout o bolt**: estadísticas sobre el spout o bolt. Use los vínculos de la columna **Ventana** para establecer el período de tiempo para las entradas restantes de la página. - **Estadísticas de entrada** (solo bolt): información sobre los flujos de entrada consumidos por el bolt. - **Estadísticas de salida**: información sobre de los flujos emitidos por este spout o bolt. - **Ejecutores**: información sobre las instancias del spout o bolt. Seleccione la entrada **Puerto** de un ejecutor específico para ver un registro de información de diagnóstico generado por esta instancia. - **Errores**: cualquier información de error para este spout o bolt.

## API de REST

La interfaz de usuario de Storm se basa en la API de REST, lo que permite realizar una funcionalidad similar de administración y supervisión mediante la API de REST. Puede usar la API de REST para crear herramientas personalizadas para administrar y supervisar las topologías de Storm.

Para obtener más información, consulte la <a href="https://github.com/apache/storm/blob/master/STORM-UI-REST-API.md" target="_base">API de REST de la IU de Storm</a>. La siguiente información es específica para usar la API de REST con Apache Storm en HDInsight.

> [AZURE.IMPORTANT]La API de REST de Storm no está públicamente disponible a través de Internet y debe tener acceso mediante un túnel SSH en el nodo principal del clúster de HDInsight. Para obtener información sobre la creación y el uso de un túnel SSH, vea [Uso de la tunelización SSH para tener acceso a la interfaz de usuario web de Ambari, ResourceManager, JobHistory, NameNode, Oozie y otras interfaces de usuario web](hdinsight-linux-ambari-ssh-tunnel.md).

### URI base

El URI base para la API de REST en clústeres de HDInsight basado en Linux es **https://headnode0:8744/api/v1/</a>**.

### Autenticación

Las solicitudes a la API de REST deben usar la **autenticación básica**; use el nombre y la contraseña de administrador del clúster de HDInsight.

> [AZURE.NOTE]Dado que la autenticación básica se envía mediante texto no cifrado, **siempre** debe usar HTTPS para proteger las comunicaciones con el clúster.

### Valores devueltos

La información que se devuelve de la API de REST solo se puede utilizar desde dentro del clúster o de máquinas virtuales en la misma red virtual de Azure que el clúster. Por ejemplo, no se podrá obtener el acceso desde Internet al nombre de dominio completo (FQDN) devuelto para servidores de Zookeeper.

## Pasos siguientes

Ahora que ha aprendido cómo implementar y supervisar topologías mediante el panel de Storm, aprenda a [Desarrollar topologías basadas en Java mediante Maven](hdinsight-storm-develop-java-topology.md).

Para obtener una lista con más topologías de ejemplo, consulte [Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md).

<!---HONumber=Oct15_HO3-->