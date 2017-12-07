---
title: "Habilitación de volcados de montón de los servicios de Hadoop en HDInsight - Azure | Microsoft Docs"
description: "Habilitar los volcados de montón de los servicios de Hadoop en los clústeres de HDInsight basado en Linux para la depuración y el análisis."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 8f151adb-f687-41e4-aca0-82b551953725
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: larryfr
ms.openlocfilehash: dcc04e5bba28d0cb32e8633542ab8d3c125003ec
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="enable-heap-dumps-for-hadoop-services-on-linux-based-hdinsight"></a>Habilitación de los volcados de montón de los servicios de Hadoop en HDInsight basado en Linux

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Los volcados de montón contienen una instantánea de la memoria de la aplicación, incluidos los valores de variables en el momento en el que se creó el volcado de memoria. Por ello, estos volcados resultan útiles a la hora de diagnosticar cualquier problema que ocurra en tiempo de ejecución.

> [!IMPORTANT]
> Los pasos de este documento solo funcionan con clústeres de HDInsight que usan Linux. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="whichServices"></a>Servicios

Puede habilitar los volcados de montón en los siguientes servicios:

* **hcatalog** - tempelton
* **hive** - hiveserver2, metastore, derbyserver
* **mapreduce** - jobhistoryserver
* **yarn** - resourcemanager, nodemanager, timelineserver
* **hdfs** - datanode, secondarynamenode, namenode

También puede habilitar los volcados de montón para los procesos de asignación y ejecución que ejecuta HDInsight.

## <a name="configuration"></a>Información sobre cómo configurar el volcado de montón

Son las opciones de paso (también conocidas como opciones o parámetros) las que habilitan los volcados de montón en JVM cuando se inicia un servicio. En la mayoría de los servicios de Hadoop, puede modificar el script de shell empleado para iniciar el servicio para pasar estas opciones.

En cada script hay una exportación de **\*\_OPTS** que contiene las opciones que se pasan a JVM. Por ejemplo, en el script **hadoop env.sh**, la línea que comienza con `export HADOOP_NAMENODE_OPTS=` contiene las opciones del servicio NameNode.

La asignación y reducción de procesos son tareas ligeramente diferentes, ya que se tratan de procesos secundarios del servicio MapReduce. Cada proceso de asignación o reducción se ejecuta en un contenedor secundario y existen dos entradas que contienen las opciones de JVM. Ambos están en **mapred-site.xml**:

* **mapreduce.admin.map.child.java.opts**
* **mapreduce.admin.reduce.child.java.opts**

> [!NOTE]
> Se recomienda usar Ambari para modificar los scripts y la configuración de mapred-site.xml, puesto que Ambari controla la replicación de los cambios en los nodos del clúster. Consulte la sección [Uso de Ambari](#using-ambari) para obtener los pasos específicos que debe dar.

### <a name="enable-heap-dumps"></a>Habilitar los volcados de montón

La siguiente opción habilita los volcados del montón cuando se produce un OutOfMemoryError:

    -XX:+HeapDumpOnOutOfMemoryError

El símbolo **+** indica que esta opción está habilitada, ya que está deshabilitada de forma predeterminada.

> [!WARNING]
> Los volcados de montón no están habilitados para los servicios Hadoop en HDInsight, ya que el tamaño de los archivos de volcado puede ser grande. Si los habilita para solucionar problemas, no olvide deshabilitarlos una vez haya reproducido el problema y recopilado los archivos de volcado.

### <a name="dump-location"></a>Ubicación de volcado

La ubicación predeterminada del archivo de volcado es el directorio en el cual está trabajando. Puede decidir dónde guardar el archivo con la siguiente opción:

    -XX:HeapDumpPath=/path

Por ejemplo, al usar `-XX:HeapDumpPath=/tmp`, los volcados se almacenarán en el directorio /tmp.

### <a name="scripts"></a>Scripts

También puede desencadenar un script cuando se produzca un error **OutOfMemoryError** . Por ejemplo, puede desencadenar una notificación que le avise de que el error se ha producido. Utilice la siguiente opción para desencadenar un script en un error __OutOfMemoryError__:

    -XX:OnOutOfMemoryError=/path/to/script

> [!NOTE]
> Puesto que Hadoop es un sistema distribuido, debe colocar cualquier script que use en todos los nodos del clúster que ejecute el servicio.
> 
> Asimismo, el script debe estar en una ubicación que sea accesible para la cuenta con la cual se ejecuta el servicio y deberá proporcionar permisos de ejecución. A modo de ejemplo, es posible que desee almacenar los scripts en `/usr/local/bin` y usar `chmod go+rx /usr/local/bin/filename.sh` para conceder permisos de ejecución y lectura.

## <a name="using-ambari"></a>Uso de Ambari

Para modificar la configuración de un servicio, siga estos pasos:

1. Abra la interfaz de usuario web Ambari del clúster. La dirección URL es https://YOURCLUSTERNAME.azurehdinsight.net.

    Cuando se le solicite, deberá autenticarse en el sitio mediante el nombre de cuenta HTTP (nombre predeterminado: admin) y la contraseña del clúster.

   > [!NOTE]
   > Es posible que Ambari le pida de nuevo que escriba el nombre de usuario y la contraseña. Si es así, escriba el mismo nombre de cuenta y la contraseña.

2. En la lista de la izquierda, seleccione el área de servicio que desea modificar. Por ejemplo, **HDFS**. En el área central, seleccione la ficha **Configuraciones** .

    ![Imagen de la web de Ambari web con la ficha de configuración HDFS seleccionada](./media/hdinsight-hadoop-heap-dump-linux/serviceconfig.png)

3. Mediante la entrada **Filtrar...**, escriba **opciones**. Solo se muestran los elementos que contienen este texto.

    ![Lista filtrada](./media/hdinsight-hadoop-heap-dump-linux/filter.png)

4. Busque la entrada **\*\_OPTS** del servicio en el cual desea habilitar los volcados de montón y agregue las opciones que desee habilitar. En la siguiente imagen, he agregado `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` a la entrada **HADOOP\_NAMENODE\_OPTS**:

    ![HADOOP_NAMENODE_OPTS con -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/](./media/hdinsight-hadoop-heap-dump-linux/opts.png)

   > [!NOTE]
   > Al habilitar los volcados de montón para el proceso secundario de asignación o reducción, busque los campos con los nombres **mapreduce.admin.map.child.java.opts** y **mapreduce.admin.reduce.child.java.opts**.

    Presione el botón **Guardar** para guardar los cambios. Puede elaborar una nota breve en la que se describan los cambios.

5. Una vez que se hayan aplicado los cambios, aparecerá el icono **Es necesario reiniciar** junto a uno o más servicios.

    ![icono «Es necesario reiniciar» y botón de reinicio](./media/hdinsight-hadoop-heap-dump-linux/restartrequiredicon.png)

6. Seleccione cada uno de los servicios que necesite reiniciar y pulse el botón **Acciones de servicio** para seleccionar la opción **Activar modo de mantenimiento**. El modo de mantenimiento evita que se generen alertas desde el servicio al reiniciarlo.

    ![Activar el menú del modo de mantenimiento](./media/hdinsight-hadoop-heap-dump-linux/maintenancemode.png)

7. Una vez habilitado el modo de mantenimiento, pulse el botón **Reiniciar** para que el servicio pueda **Reiniciar todos los afectados**

    ![Reiniciar todas las entradas afectadas](./media/hdinsight-hadoop-heap-dump-linux/restartbutton.png)

   > [!NOTE]
   > es posible que las entradas del botón **Reiniciar** botón sean diferentes en otros servicios.

8. Una vez haya reiniciado los servicios, pulse el botón **Acciones de servicio** para **Desactivar el modo de mantenimiento**. Esto hará que Ambari reanude la supervisión de alertas para el servicio.

