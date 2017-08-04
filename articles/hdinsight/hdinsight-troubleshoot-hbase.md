---
title: "Solución de problemas de HBase: Azure HDInsight | Microsoft Docs"
description: "Solución de problemas de la causa de huecos en la cadena de regiones."
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
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 29b1776d6b0c456515738aae3c5fd836c9a0295d
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="hbase-troubleshooting"></a>Solución de problemas de HBASE

En este artículo se describen los principales problemas y sus soluciones para trabajar con cargas de HBASE en Apache Ambari.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Cómo se ejecutan informes de comando hbck con varias regiones sin asignar

Es un problema habitual ver "varias regiones sin asignar o huecos en la cadena de regiones" cuando el usuario de HBase ejecuta el comando "hbase hbck".

El usuario verá el número de regiones sin equilibrar en todos los servidores de regiones en la interfaz de usuario principal de HBase. Después de eso, el usuario puede ejecutar el comando "hbase hbck" y observará huecos en la cadena de regiones.

El usuario debe corregir en primer lugar las asignaciones, ya que es posible que los huecos se deban a esas regiones sin conexión. 

Siga los pasos siguientes para volver a poner las regiones sin asignar en el estado normal:

1. Inicie sesión a través de SSH en el clúster de HBase para HDInsight.
1. Ejecute el comando "hbase zkcli" para conectarse al shell de Zookeeper.
1. Ejecute el comando "rmr /hbase/regions-in-transition" o "rmr /hbase-unsecure/regions-in-transition".
1. Salga del shell "hbase zkcli" con el comando "exit".
1. Abra la interfaz de usuario de Ambari y reinicie el servicio Active HBase Master desde Ambari.
1. Ejecute de nuevo el comando "hbase hbck" (sin ninguna opción adicional).

Compruebe el resultado del comando en el paso 6 y asegúrese de que todas las regiones se están asignando.

---

## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Cómo solucionar problemas de tiempo de espera con comandos hbck para las asignaciones de regiones

### <a name="probable-cause"></a>Causa probable

Aquí la posible causa podría ser que varias regiones estén en el estado "en transición" durante mucho tiempo. Esas regiones pueden verse como sin conexión desde la interfaz de usuario principal de HBase. Debido al gran número de regiones que tratan de realizar la transición, HBase Master podría superar el tiempo de espera y no ser capaz de volver a poner esas regiones en estado en línea.

### <a name="resolution-steps"></a>Pasos de la solución

A continuación se muestran los pasos para solucionar el problema de tiempo de espera de hbck:

1. Inicie sesión a través de SSH en el clúster de HBase para HDInsight.
1. Ejecute el comando "hbase zkcli" para conectarse al shell de Zookeeper.
1. Ejecute el comando "rmr /hbase/regions-in-transition" o "rmr /hbase-unsecure/regions-in-transition".
1. Salga del shell "hbase zkcli" con el comando "exit".
1. Abra la interfaz de usuario de Ambari y reinicie el servicio Active HBase Master desde Ambari.
1. Vuelva a ejecutar el comando "hbase hbck - fixAssignments" y no debería superar el tiempo de espera.

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-an-cluster"></a>Cómo forzar la deshabilitación del modo seguro de HDFS en un clúster

### <a name="issue"></a>Problema:

HDFS local está detenido en modo seguro en el clúster de HDInsight.   

### <a name="detailed-description"></a>Descripción detallada:

Se produce un error al ejecutar un sencillo comando HDFS como el siguiente:

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

El error detectado al intentar ejecutar el comando anterior es el siguiente:

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

### <a name="probable-cause"></a>Causa probable:

El clúster de HDInsight se ha reducido verticalmente a muy pocos nodos por debajo o cerca del factor de replicación de HDFS.

### <a name="resolution-steps"></a>Pasos de la solución: 

- Notifique el estado de HDFS en el clúster de HDInsight con los siguientes comandos:

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
- También puede comprobar la integridad de HDFS en el clúster de HDInsight con los siguientes comandos:

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

- Si no falta ningún bloque, no hay bloques dañados o subreplicados, o esos bloques se pueden ignorar, ejecute el comando siguiente para quitar el nodo de nombre del modo seguro:

```apache
hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Cómo se solucionan problemas de conectividad de JDBC o sqlline con Apache Phoenix

### <a name="resolution-steps"></a>Pasos de la solución:

Para conectar con Apache Phoenix, debe proporcionar la dirección IP del nodo activo de Zookeeper. Asegúrese de que el servicio Zookeeper al que sqlline.py está intentando conectar está en funcionamiento.
1. Inicie sesión con SSH en el clúster de HDInsight.
1. Pruebe el siguiente comando:
        
```apache
        "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
```     
    Note: The IP of Active Zooker node can be identified from Ambari UI, by following the links to HBase -> "Quick Links" -> "ZK* (Active)" -> "Zookeeper Info". 

Si sqlline.py se conecta a Apache Phoenix y no supera el tiempo de espera, ejecute el siguiente comando para validar la disponibilidad y el estado de Apache Phoenix:

```apache
        !tables
        !quit
```      
- Si los comandos anteriores funcionan, no hay ningún problema. Es posible que la dirección IP proporcionada por el usuario sea incorrecta.
   
    Pero si el comando se detiene durante demasiado tiempo y después se produce el error mencionado a continuación, siga la guía de solución de problemas siguiente:

```apache
        Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
```

- Ejecute los siguientes comandos desde el nodo principal (hn0) para diagnosticar la condición de la tabla SYSTEM.CATALOG de Phoenix:

```apache
        hbase shell
        
        count 'SYSTEM.CATALOG'
```        
- El comando debe devolver un error similar al siguiente: 

```apache
        ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
```
- Reinicie el servicio HMaster en todos los nodos de Zookeeper desde la interfaz de usuario de Ambari siguiendo estos pasos:

    a. Vaya al vínculo "HBase -> Active HBase Master" en la sección de resumen de HBase. 
    a. En la sección de componentes, reinicie el servicio HBase Master.
    a. Repita los pasos anteriores para los servicios "Standby HBase Master" restantes. 

Se pueden tardar hasta cinco minutos en estabilizar el servicio HBase Master y finalizar la recuperación. Después de varios minutos de espera, repita los comandos sqlline.py para confirmar que esa tabla de catálogo del sistema está activa y se puede consultar. 

Una vez que la tabla "SYSTEM.CATALOG" vuelva al estado normal, el problema de conectividad a Apache Phoenix debería resolverse automáticamente.


## <a name="what-causes-a-master-server-to-fail-to-start"></a>Qué hace que un servidor maestro no se pueda iniciar

### <a name="error"></a>Error: 

Error de cambio de nombre atómico

### <a name="detailed-description"></a>Descripción detallada:

Durante el proceso de inicio, HMaster realiza muchos pasos de inicialización, incluido el movimiento de datos desde la carpeta temporal (.tmp) a la carpeta de datos; también busca en la carpeta de WAL (Registros de escritura previa) para comprobar si hay algún servidor de regiones inactivo y así sucesivamente. 

En todas estas situaciones, ejecuta un comando "list" básico en estas carpetas. Si en algún momento ve un archivo inesperado en cualquiera de estas carpetas, genera una excepción y, por tanto, no se inicia.  

### <a name="probable-cause"></a>Causa probable:

Intente identificar la escala de tiempo de la creación de archivos y vea si se produjo un bloqueo del proceso en torno a esa hora en los registros del servidor de regiones (póngase en contacto con el equipo de HBase para que le ayuden a hacerlo). Esto nos ayuda a proporcionar mecanismos más sólidos para evitar alcanzar este error y garantizar cierres de proceso correctos.

### <a name="resolution-steps"></a>Pasos de la solución:

En una situación similar, intente comprobar la pila de llamadas y ver qué carpeta podría estar causando el problema (por ejemplo si es la carpeta de WAL o la carpeta .tmp). Después, a través de Cloud Explorer o mediante comandos hdfs, intente localizar el archivo problemático, que normalmente es un archivo *-renamePending.json (un archivo de diario que se usa para implementar la operación de cambio de nombre atómico en el controlador de WASB; debido a errores en esta implementación, es posible que estos archivos se dejen abandonados en casos de bloqueos del proceso etc.) Fuerce la eliminación a través de Cloud Explorer. 

Además, es posible que en ocasiones también haya un archivo temporal de tipo $$$.$$$ en esta ubicación, que no puede verse a través de Cloud Explorer, solo a través del comando ls de hdfs. Puede usar el comando de hdfs "hdfs dfs -rm /<the path>/\$\$\$.\$\$\$" para eliminar este archivo.  

Una vez hecho esto, HMaster debería iniciarse inmediatamente. 

### <a name="error-no-server-address-listed-in"></a>Error: No se enumera ninguna dirección de servidor en 

No se enumera ninguna dirección de servidor en hbase: meta para la región xxx

### <a name="detailed-description"></a>Descripción detallada:

El cliente encontró un problema en su clúster de Linux que indica que la tabla hbase: meta no estaba en línea. Al ejecutar hbck se notificó que "No se encuentra la tabla hbase: meta replicaId 0 en ninguna región". Después de reiniciar HBase, el síntoma era que no se podía inicializar hmaster. En los registros de hmaster, se notificó que "No se enumera ninguna dirección de servidor en hbase: meta para la región hbase: backup <region name>".  

### <a name="resolution-steps"></a>Pasos de la solución:

- Escriba lo siguiente en el shell de HBase (cambie los valores reales según corresponda),  

```apache
> scan 'hbase:meta'  
```

```apache
> delete 'hbase:meta','hbase:backup <region name>','<column name>'  
```

- Elimine la entrada de hbase: espacio de nombres ya que el mismo error puede aparecer al examinar la tabla hbase: espacio de nombres.

- Reinicie el HMaster activo desde la interfaz de usuario de Ambari para que aparezca en estado de ejecución.  

- Ejecute el siguiente comando en el shell de HBase para activar todas las tablas sin conexión:

```apache 
hbase hbck -ignorePreCheckPermission -fixAssignments 
```

### <a name="further-reading"></a>Lecturas adicionales:

[No se puede procesar la tabla HBase](http://stackoverflow.com/questions/4794092/unable-to-access-hbase-table)


### <a name="error"></a>Error:

HMaster agota el tiempo de espera con una excepción grave como java.io.IOException: Timedout 300000ms esperando que se asigne la tabla de espacio de nombres.

### <a name="detailed-description"></a>Descripción detallada:

El cliente encontró este problema cuando aparentemente tenía una gran cantidad de tablas y regiones, y no se habían vaciado al reiniciar sus servicios de HMaster. Se producía un error en el reinicio con el mensaje anterior. No se encontraron otros errores.  

### <a name="probable-cause"></a>Causa probable:

Se trata de un conocido "defecto" de HMaster: las tareas de inicio de clúster generales pueden tardar mucho tiempo y HMaster se apaga debido a que la tabla de espacio de nombres todavía no se ha asignado, lo que únicamente tiene lugar en este escenario cuando existe gran cantidad de datos sin vaciar y un tiempo de espera de cinco minutos no es suficiente
  
### <a name="resolution-steps"></a>Pasos de la solución:

- Obtenga acceso a la interfaz de usuario de Ambari, vaya a HBase -> Configs (Configuraciones) y agregue la siguiente configuración al archivo hbase-site.xml: 

```apache
Key: hbase.master.namespace.init.timeout Value: 2400000  
```

- Reinicie los servicios requeridos (principalmente HMaster y posiblemente otros servicios de HBase).  



## <a name="what-causes-a-restart-failure-on-a-region-server"></a>Qué provoca un error de reinicio en un servidor de regiones

### <a name="probable-cause"></a>Causa probable:

En primer lugar, una situación como esta se podría evitar siguiendo los procedimientos recomendados. Es aconsejable pausar la actividad de las cargas de trabajo pesadas cuando se planea reiniciar los servidores de regiones de HBase. Si la aplicación continúa conectándose a servidores de regiones mientras el cierre esté en curso, ralentizará la operación de reinicio del servidor de regiones durante varios minutos. Además, se recomienda que los usuarios vacíen todas las tablas siguiendo [HBase para HDInsight: cómo mejorar el tiempo de reinicio de clústeres de HBase mediante el vaciado de tablas](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/) como referencia.

Si un usuario inicia la operación de reinicio en el servidor de regiones de HBase desde la interfaz de usuario de Ambari, inmediatamente vería que los servidores de regiones dejaron de funcionar, pero que tardan demasiado en volver a funcionar. 

A continuación se muestra lo que ocurre en segundo plano: 

1. El agente de Ambari enviará una solicitud de detención al servidor de regiones.
1. Después, el agente de Ambari espera 30 segundos a que el servidor de regiones se cierre correctamente. 
1. Si la aplicación del cliente continúa conectándose con el servidor de regiones, no se apagará inmediatamente y, por tanto, el tiempo de espera de 30 segundos expira antes. 
1. Tras la expiración de los 30 segundos, el agente de Ambari envía una terminación forzada (kill -9) al servidor de regiones. Se puede observar en el registro del agente de Ambari (en el directorio /var/log/ del nodo de trabajo correspondiente) como se muestra a continuación:

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
Debido a este cierre inesperado, no se puede liberar el puerto asociado con el proceso, aunque se termine el proceso del servidor de regiones. Esta situación puede provocar la excepción AddressBindException al iniciar el servidor de regiones, como se muestra en los registros siguientes. Se puede comprobar en el registro region-server.log del directorio /var/log/hbase en los nodos de trabajo donde se produce el error de inicio del servidor de regiones. 

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

### <a name="resolution-steps"></a>Pasos de la solución:

En estos casos, se puede probar la solución alternativa siguiente: 

- Intente reducir la carga en los servidores de regiones de HBase antes de iniciar un reinicio. 

- Como alternativa (si el paso anterior no sirve de ayuda), pruebe a reiniciar manualmente los servidores de regiones en los nodos de trabajo con los siguientes comandos:

```apache
      sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
      sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
```



