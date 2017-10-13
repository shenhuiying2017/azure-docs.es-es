---
title: "Solución de problemas de HBase mediante Azure HDInsight | Microsoft Docs"
description: "Obtenga respuestas a las preguntas comunes sobre cómo trabajar con HBase y Azure HDInsight."
services: hdinsight
documentationcenter: 
author: nitinver
manager: ashitg
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 7/7/2017
ms.author: nitinver
ms.openlocfilehash: 15412c3853a2b8436c5e96034c9a92a2a1094662
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-hbase-by-using-azure-hdinsight"></a>Solución de problemas de HBase mediante Azure HDInsight

Obtenga información sobre los principales problemas y sus soluciones al trabajar con cargas útiles de HBase en Apache Ambari.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Cómo se ejecutan informes de comando hbck con varias regiones sin asignar

Un mensaje de error común que puede ver al ejecutar el comando `hbase hbck` es "multiple regions being unassigned or holes in the chain of regions" (hay varias regiones no asignadas o hay orificios en la cadena de regiones).

En la interfaz de usuario de HBase Master, puede ver el número de regiones desequilibradas en todos los servidores de las regiones. Luego, puede ejecutar el comando `hbase hbck` para ver los orificios en la cadena de regiones.

Los orificios los pueden causar las regiones sin conexión, así que primero es preciso corregir las asignaciones. 

Para que las regiones sin asignar vuelvan a su estado normal, siga estos pasos:

1. Inicie sesión en el clúster de HDInsight HBase mediante SSH.
2. Para conectar con el shell de ZooKeeper, ejecute el comando `hbase zkcli`.
3. Ejecute los comandos `rmr /hbase/regions-in-transition` o `rmr /hbase-unsecure/regions-in-transition`.
4. Para salir del shell de `hbase zkcli`, use el comando `exit`.
5. Abra la interfaz de usuario de Apache Ambari y, después, reinicie el servicio Active HBase Master.
6. Vuelva a ejecutar el comando `hbase hbck` (sin opciones). Compruebe el resultado de este comando para asegurarse de que se asignan todas las regiones.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Cómo se solucionan los problemas de tiempo de espera cuando se usan comandos hbck para las asignaciones de regiones

### <a name="issue"></a>Problema

Una posible causa de problemas de tiempo de espera cuando se usa el comando `hbck` puede ser que varias regiones estén en el estado "en transición" durante mucho tiempo. En la interfaz de usuario maestra de HBase, dichas regiones se pueden ver como sin conexión. Dado que un elevado número de regiones intentan realizar la transición, HBase Master puede superar el tiempo de espera, lo que impediría que dichas regiones vuelvan a estar en línea.

### <a name="resolution-steps"></a>Pasos de la solución

1. Inicie sesión en el clúster de HDInsight HBase mediante SSH.
2. Para conectar con el shell de ZooKeeper, ejecute el comando `hbase zkcli`.
3. Ejecute los comandos `rmr /hbase/regions-in-transition` o `rmr /hbase-unsecure/regions-in-transition`.
4. Para salir del shell de `hbase zkcli`, use el comando `exit`.
5. En la interfaz de usuario de Ambari, reinicie el servicio Active HBase Master.
6. Vuelva a ejecutar el comando `hbase hbck -fixAssignments`.

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-a-cluster"></a>Cómo forzar la deshabilitación del modo seguro de HDFS en un clúster

### <a name="issue"></a>Problema

El sistema de archivos distribuido Hadoop (HDFS) local está atascado en modo seguro en el clúster de HDInsight.

### <a name="detailed-description"></a>Descripción detallada

Este error puede deberse a un error al ejecutar el siguiente comando de HDFS:

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

El error que puede ver al intentar ejecutar el comando tiene el siguiente aspecto:

```apache
hdiuser@hn0-spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.mkdirs(FSNamesystem.java:4010)
        at org.apache.hadoop.hdfs.server.namenode.NameNodeRpcServer.mkdirs(NameNodeRpcServer.java:1102)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolServerSideTranslatorPB.mkdirs(ClientNamenodeProtocolServerSideTranslatorPB.java:630)
        at org.apache.hadoop.hdfs.protocol.proto.ClientNamenodeProtocolProtos$ClientNamenodeProtocol$2.callBlockingMethod(ClientNamenodeProtocolProtos.java)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine.java:640)
        at org.apache.hadoop.ipc.RPC$Server.call(RPC.java:982)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2313)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2309)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:422)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1724)
        at org.apache.hadoop.ipc.Server$Handler.run(Server.java:2307)
        at org.apache.hadoop.ipc.Client.getRpcResponse(Client.java:1552)
        at org.apache.hadoop.ipc.Client.call(Client.java:1496)
        at org.apache.hadoop.ipc.Client.call(Client.java:1396)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Invoker.invoke(ProtobufRpcEngine.java:233)
        at com.sun.proxy.$Proxy10.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolTranslatorPB.mkdirs(ClientNamenodeProtocolTranslatorPB.java:603)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invokeMethod(RetryInvocationHandler.java:278)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:194)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:176)
        at com.sun.proxy.$Proxy11.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.DFSClient.primitiveMkdir(DFSClient.java:3061)
        at org.apache.hadoop.hdfs.DFSClient.mkdirs(DFSClient.java:3031)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1162)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1158)
        at org.apache.hadoop.fs.FileSystemLinkResolver.resolve(FileSystemLinkResolver.java:81)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirsInternal(DistributedFileSystem.java:1158)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirs(DistributedFileSystem.java:1150)
        at org.apache.hadoop.fs.FileSystem.mkdirs(FileSystem.java:1898)
        at org.apache.hadoop.fs.shell.Mkdir.processNonexistentPath(Mkdir.java:76)
        at org.apache.hadoop.fs.shell.Command.processArgument(Command.java:273)
        at org.apache.hadoop.fs.shell.Command.processArguments(Command.java:255)
        at org.apache.hadoop.fs.shell.FsCommand.processRawArguments(FsCommand.java:119)
        at org.apache.hadoop.fs.shell.Command.run(Command.java:165)
        at org.apache.hadoop.fs.FsShell.run(FsShell.java:297)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:90)
        at org.apache.hadoop.fs.FsShell.main(FsShell.java:350)
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

### <a name="probable-cause"></a>Causa probable

El clúster de HDInsight se ha reducido verticalmente hasta quedar muy pocos nodos. El número de nodos es inferior o próximo al factor de replicación de HDFS.

### <a name="resolution-steps"></a>Pasos de la solución 

1. Obtenga el estado del HDFS en el clúster de HDInsight mediante la ejecución de los siguientes comandos:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   ```

   ```apache
   hdiuser@hn0-spark2:~$ hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   Safe mode is ON
   Configured Capacity: 3372381241344 (3.07 TB)
   Present Capacity: 3138625077248 (2.85 TB)
   DFS Remaining: 3102710317056 (2.82 TB)
   DFS Used: 35914760192 (33.45 GB)
   DFS Used%: 1.14%
   Under replicated blocks: 0
   Blocks with corrupt replicas: 0
   Missing blocks: 0
   Missing blocks (with replication factor 1): 0

   -------------------------------------------------
   Live datanodes (8):

   Name: 10.0.0.17:30010 (10.0.0.17)
   Hostname: 10.0.0.17
   Decommission Status : Normal
   Configured Capacity: 421547655168 (392.60 GB)
   DFS Used: 5288128512 (4.92 GB)
   Non DFS Used: 29087272960 (27.09 GB)
   DFS Remaining: 387172253696 (360.58 GB)
   DFS Used%: 1.25%
   DFS Remaining%: 91.85%
   Configured Cache Capacity: 0 (0 B)
   Cache Used: 0 (0 B)
   Cache Remaining: 0 (0 B)
   Cache Used%: 100.00%
   Cache Remaining%: 0.00%
   Xceivers: 2
   Last contact: Wed Apr 05 16:22:00 UTC 2017
   ...

   ```
2. También puede comprobar la integridad del HDFS en el clúster de HDInsight mediante los siguientes comandos:

   ```apache
   hdiuser@hn0-spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
   ```

   ```apache
   Connecting to namenode via http://hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net:30070/fsck?ugi=hdiuser&path=%2F
   FSCK started by hdiuser (auth:SIMPLE) from /10.0.0.22 for path / at Wed Apr 05 16:40:28 UTC 2017
   ....................................................................................................

   ....................................................................................................
   ..................Status: HEALTHY
   Total size:    9330539472 B
   Total dirs:    37
   Total files:   2618
   Total symlinks:                0 (Files currently being written: 2)
   Total blocks (validated):      2535 (avg. block size 3680686 B)
   Minimally replicated blocks:   2535 (100.0 %)
   Over-replicated blocks:        0 (0.0 %)
   Under-replicated blocks:       0 (0.0 %)
   Mis-replicated blocks:         0 (0.0 %)
   Default replication factor:    3
   Average block replication:     3.0
   Corrupt blocks:                0
   Missing replicas:              0 (0.0 %)
   Number of data-nodes:          8
   Number of racks:               1
   FSCK ended at Wed Apr 05 16:40:28 UTC 2017 in 187 milliseconds

   The filesystem under path '/' is HEALTHY
   ```

3. Si determina que no falta ningún bloque, no hay bloques dañados o subreplicados, o que dichos bloques se pueden ignorar, ejecute el comando siguiente para que el nodo de nombre deje de estar en modo seguro:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
   ```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Cómo se solucionan los problemas de conectividad de JDBC o SQLLine con Apache Phoenix

### <a name="resolution-steps"></a>Pasos de la solución

Para conectar con Phoenix, debe proporcionar la dirección IP del nodo activo de Zookeeper. Asegúrese de que el servicio Zookeeper al que sqlline.py intenta conectarse está en funcionamiento.
1. Inicie sesión en el clúster de HDInsight mediante SSH.
2. Escriba el comando siguiente:
                
   ```apache
           "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
   ```

   > [!Note] 
   > Puede obtener la dirección IP del nodo de ZooKeeper activo de la interfaz de usuario de Ambari. Vaya a **HBase** > **Quick Links (Vínculos rápidos)** > **ZK\* (Active)** > **Zookeeper Info (Información de Zookeeper)**. 

3. Si sqlline.py se conecta a Phoenix y no supera el tiempo de espera, ejecute el siguiente comando para validar la disponibilidad y el estado de Phoenix:

   ```apache
           !tables
           !quit
   ```      
4. Si el comando funciona, no hay ningún problema. La dirección IP que proporciona el usuario puede ser incorrecta. Sin embargo, si el comando se detiene durante un período prolongado y luego muestra el siguiente error, vaya al paso 5.

   ```apache
           Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
   ```

5. Ejecute los siguientes comandos desde el nodo principal (hn0) para diagnosticar la condición de la tabla SYSTEM.CATALOG de Phoenix:

   ```apache
            hbase shell
                
           count 'SYSTEM.CATALOG'
   ```

   El comando debe devolver un error similar al siguiente: 

   ```apache
           ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
   ```
6. En la interfaz de usuario de Ambari, realice los siguientes pasos para reiniciar el servicio HMaster en todos los nodos de ZooKeeper:

    1. En la sección **Summary** (Resumen) de HBase, vaya a **HBase** > **Active HBase Master**. 
    2. En la sección **Components** (Componentes), reinicie el servicio HBase Master.
    3. Repita estos para todos los servicios **Standby HBase Master** restantes. 

El servicio HBase Master puede tener un máximo de cinco minutos en estabilizar y finalizar el proceso de recuperación. Pocos minutos después, repita los comandos de sqlline.py para confirmar que la tabla SYSTEM.CATALOG está activa y que se puede consultar. 

Cuando la tabla SYSTEM.CATALOG vuelva al estado normal, el problema de la conectividad con Phoenix debería resolverse automáticamente.


## <a name="what-causes-a-master-server-to-fail-to-start"></a>Qué hace que un servidor maestro no se pueda iniciar

### <a name="error"></a>Error 

Se produce un error de cambio de nombre atómico.

### <a name="detailed-description"></a>Descripción detallada

Durante el proceso de inicio, HMaster completa muchos pasos de inicialización. Entre ellos se incluye mover datos desde la carpeta temporal (.tmp) a la carpeta de datos. HMaster también examina la carpeta de registros de escritura previa (WAL) para ver si hay servidores de la región que no responden, y así sucesivamente. 

Durante el inicio, HMaster realiza un comando `list` básico en estas carpetas. Si en algún momento HMaster ve un archivo inesperado en cualquiera de estas carpetas, genera una excepción y no se inicia.  

### <a name="probable-cause"></a>Causa probable

En los registros de servidores de la región, intente identificar la escala de tiempo de la creación del archivo y, después, vea si se produjo un bloqueo del proceso aproximadamente a la hora en que se creó el archivo (si necesita ayuda, póngase en contacto con el soporte técnico de HBase). Esto nos ayuda a proporcionar mecanismos más robustos para que pueda evitar toparse con este error y asegurarse apagados de procesos correctos.

### <a name="resolution-steps"></a>Pasos de la solución

Compruebe la pila de llamadas e intente determinar qué carpeta puede ser la causante del problema (por ejemplo, puede ser la carpeta de WAL o la carpeta .tmp). Después, en Cloud Explorer o mediante los comandos de HDFS, pruebe a buscar el archivo problemático. Normalmente, es un archivo \*-renamePending.json. (\*-renamePending.json es un archivo de diario que se utiliza para implementar la operación de cambio de nombre atómico en el controlador WASB. Debido a los errores de esta implementación, estos archivos se pueden dejar después de que el proceso se bloquea, y así sucesivamente). Fuerce la eliminación de este archivo en Cloud Explorer o mediante los comandos de HDFS. 

En ocasiones, en esta ubicación también puede haber un archivo temporal denominado algo así como *$$$. $$$*. Para ver este archivo, tiene que usar el comando `ls` de HDFS; en Cloud Explorer no se puede ver. Para eliminar este archivo, use el comando `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$` de HDFS.  

Una vez que haya ejecutado estos comandos, HMaster debería iniciarse de inmediato. 

### <a name="error"></a>Error

No se enumera ninguna dirección de servidor en *hbase: meta* para la región xxx.

### <a name="detailed-description"></a>Descripción detallada

Puede ver un mensaje en el clúster de Linux que indica que la tabla *hbase: meta* no está en línea. La ejecución de `hbck` puede notificar que "No se encuentra la tabla hbase: meta replicaId 0 en ninguna región". El problema podría ser que HMaster no se pudo inicializar una vez reiniciado HBase. En los registros de HMaster, se puede ver el mensaje: "No se enumera ninguna dirección de servidor en hbase: meta para la región hbase: backup \<nombre de región\>".  

### <a name="resolution-steps"></a>Pasos de la solución

1. En el shell de HBase, especifique los siguientes comandos (cambie los valores reales según corresponda):  

   ```apache
   > scan 'hbase:meta'  
   ```

   ```apache
   > delete 'hbase:meta','hbase:backup <region name>','<column name>'  
   ```

2. Elimine la entrada *hbase: namespace*. Esta entrada puede ser el mismo error que se notifica cuando se examina la tabla *hbase: namespace*.

3. Para poner HBase en estado activo, en la interfaz de usuario de Ambari, reinicie el servicio Active HMaster.  

4. En el shell de HBase, para activar todas las tablas sin conexión, ejecute el siguiente comando:

   ```apache 
   hbase hbck -ignorePreCheckPermission -fixAssignments 
   ```

### <a name="additional-reading"></a>Lecturas adicionales

[No se puede procesar la tabla de HBase](http://stackoverflow.com/questions/4794092/unable-to-access-hbase-table)


### <a name="error"></a>Error

HMaster agota el tiempo de espera con una excepción grave similar a "java.io.IOException: Timedout 300000ms esperando que se asigne la tabla de espacio de nombres".

### <a name="detailed-description"></a>Descripción detallada

Este problema puede aparecer si hay muchas tablas y regiones que no se han vaciado al reiniciar los servicios de HMaster. Podría producirse un error durante el reinicio y verá el mensaje de error anterior.  

### <a name="probable-cause"></a>Causa probable

Se trata de un problema conocido del servicio HMaster. Las tareas de inicio de los clústeres generales pueden tardar mucho tiempo. HMaster se cierra porque la tabla de espacios de nombres todavía no está asignada. Esto sucede solo en aquellos escenarios en los que existe una gran cantidad de datos no vaciados y no es suficiente un tiempo de expiración de cinco minutos.
  
### <a name="resolution-steps"></a>Pasos de la solución

1. En la interfaz de usuario de Ambari, vaya a **HBase** > **Configs** (Configuraciones). En el archivo hbase-site.xml personalizado, agregue la siguiente configuración: 

   ```apache
   Key: hbase.master.namespace.init.timeout Value: 2400000  
   ```

2. Reinicie los servicios requeridos (HMaster y posiblemente otros servicios de HBase).  


## <a name="what-causes-a-restart-failure-on-a-region-server"></a>Qué provoca un error de reinicio en un servidor de regiones

### <a name="issue"></a>Problema

Si se siguen los procedimientos recomendados, se puede evitar un error de reinicio en un servidor de regiones. Se recomienda pausar la actividad de las cargas de trabajo pesadas cuando se planea reiniciar los servidores de regiones de HBase. Si una aplicación continúa conectándose a servidores de regiones mientras el cierre esté en curso, la operación de reinicio del servidor de regiones se ralentizará durante varios minutos. También es aconsejable vaciar primero todas las tablas. Para obtener una referencia acerca de cómo vaciar tablas, consulte [HDInsight HBase: How to Improve HBase cluster restart time by Flushing tables](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/) (HDInsight HBase: cómo mejorar el tiempo de reinicio de los clústeres de HBase vaciando tables).

Si inicia la operación de reinicio en los servidores de regiones de HBase desde la interfaz de usuario de Ambari, verá inmediatamente que los servidores de regiones han dejado de funcionar, pero no se reinician de inmediato. 

Esto es lo que sucede en segundo plano: 

1. El agente de Ambari enviará una solicitud de detención al servidor de regiones.
2. El agente de Ambari espera 30 segundos a que el servidor de regiones se cierre correctamente. 
3. Si su aplicación sigue conectándose con el servidor de regiones, el servidor no apagará de inmediato. El tiempo de expiración de 30 segundos expira antes de que produzca el apagado. 
4. Después de 30 segundos, el agente de Ambari envía un comando force-kill (`kill -9`) al servidor de regiones. Esto se puede ver en el registro del agente de ambari (en el directorio /var/log/ del nodo de trabajo correspondiente):

   ```apache
           2017-03-21 13:22:09,171 - Execute['/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/current/hbase-regionserver/conf stop regionserver'] {'only_if': 'ambari-sudo.sh  -H -E t
           est -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1', 'on_timeout': '! ( ambari-sudo.sh  -H -E test -
           f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H 
           -E cat /var/run/hbase/hbase-hbase-regionserver.pid`', 'timeout': 30, 'user': 'hbase'}
           2017-03-21 13:22:40,268 - Executing '! ( ambari-sudo.sh  -H -E test -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >
           /dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid`'. Reason: Execution of 'ambari-sudo.sh su hbase -l -s /bin/bash -c 'export  
           PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/var/lib/ambari-agent ; /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/curre
           nt/hbase-regionserver/conf stop regionserver was killed due timeout after 30 seconds
           2017-03-21 13:22:40,285 - File['/var/run/hbase/hbase-hbase-regionserver.pid'] {'action': ['delete']}
           2017-03-21 13:22:40,285 - Deleting File['/var/run/hbase/hbase-hbase-regionserver.pid']
   ```
Debido al abrupto apagado, puede que el puerto asociado con el proceso no se libere, aunque se detenga el proceso del servidor de regiones. Esta situación puede provocar una excepción AddressBindException cuando se inicia el servidor de regiones, como se muestra en los registros siguientes. Puede comprobarlo en el archivo region-server.log del directorio /var/log/hbase en los nodos de trabajo en los que el servidor de regiones no se inicia. 

   ```apache

   2017-03-21 13:25:47,061 ERROR [main] regionserver.HRegionServerCommandLine: Region server exiting
   java.lang.RuntimeException: Failed construction of Regionserver: class org.apache.hadoop.hbase.regionserver.HRegionServer
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2636)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.start(HRegionServerCommandLine.java:64)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.run(HRegionServerCommandLine.java:87)
   at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
   at org.apache.hadoop.hbase.util.ServerCommandLine.doMain(ServerCommandLine.java:126)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.main(HRegionServer.java:2651)
        
   Caused by: java.lang.reflect.InvocationTargetException
   at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
   at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:57)
   at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
   at java.lang.reflect.Constructor.newInstance(Constructor.java:526)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2634)
   ... 5 more
        
   Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2497)
   at org.apache.hadoop.hbase.ipc.RpcServer$Listener.<init>(RpcServer.java:580)
   at org.apache.hadoop.hbase.ipc.RpcServer.<init>(RpcServer.java:1982)
   at org.apache.hadoop.hbase.regionserver.RSRpcServices.<init>(RSRpcServices.java:863)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.createRpcServices(HRegionServer.java:632)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.<init>(HRegionServer.java:532)
   ... 10 more
        
   Caused by: java.net.BindException: Address already in use
   at sun.nio.ch.Net.bind0(Native Method)
   at sun.nio.ch.Net.bind(Net.java:463)
   at sun.nio.ch.Net.bind(Net.java:455)
   at sun.nio.ch.ServerSocketChannelImpl.bind(ServerSocketChannelImpl.java:223)
   at sun.nio.ch.ServerSocketAdaptor.bind(ServerSocketAdaptor.java:74)
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2495)
   ... 15 more
   ```

### <a name="resolution-steps"></a>Pasos de la solución

1. Intente reducir la carga en los servidores de regiones de HBase antes de iniciar un reinicio. 
2. Como alternativa (si el paso 1 no sirve de ayuda), pruebe a reiniciar manualmente los servidores de regiones en los nodos de trabajo con los siguientes comandos:

   ```apache
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
   ```

