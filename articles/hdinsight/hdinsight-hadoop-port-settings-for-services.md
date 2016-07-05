<properties
pageTitle="Puertos utilizados por HDInsight | Azure"
description="Una lista de puertos utilizados por los servicios Hadoop que se ejecutan en HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="06/14/2016"
ms.author="larryfr"/>

# Puertos e identificadores URI usados en HDInsight

En este documento se proporciona una lista de puertos que se usan con los servicios de Hadoop que se ejecutan en clústeres de HDInsight basados en Linux. También se proporciona información sobre los puertos utilizados para conectarse al clúster mediante SSH.

## Puertos públicos frente a puertos no públicos

Los clústeres de HDInsight basados en Linux solo exponen tres puertos públicamente en Internet: 22, 23 y 443. Estos puertos se usan para el acceso seguro al clúster mediante SSH y a los servicios expuestos a través del protocolo HTTPS seguro.

Internamente, HDInsight se implementa mediante varias máquinas virtuales de Azure (los nodos del clúster) que se ejecutan en una red virtual de Azure. Desde dentro de la red virtual, puede acceder a los puertos no expuestos a través de Internet. Por ejemplo, si se conecta a uno de los nodos principales con SSH, desde él puede acceder directamente a los servicios que se ejecutan en los nodos del clúster.

> [AZURE.IMPORTANT] Al crear un clúster de HDInsight, si no especifica una red virtual de Azure como opción de configuración, se crea una; sin embargo, no puede unir otros equipos (por ejemplo, otras máquinas virtuales de Azure o el equipo de desarrollo de cliente) a esta red virtual creada automáticamente.

Para unir equipos adicionales a la red virtual, debe crear primero la red virtual y luego especificarla al crear el clúster de HDInsight. Para más información, consulte [Extensión de las funcionalidades de HDInsight con Red virtual de Azure](hdinsight-extend-hadoop-virtual-network.md).

## Puertos públicos

Todos los nodos de un clúster de HDInsight se encuentran en una red virtual de Azure y no son accesibles directamente desde Internet. Una puerta de enlace pública proporciona acceso desde Internet a los puertos siguientes, que son comunes a todos los tipos de clúster de HDInsight.

| Servicio | Port | Protocolo | Descripción |
| ---- | ---------- | -------- | ----------- | ----------- |
| sshd | 22 | SSH | Conecta los clientes a sshd en el nodo principal 0. Consulte [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md). |
| sshd | 22 | SSH | Conecta los clientes a sshd en el nodo de borde (solo HDInsight Premium). Consulte [Introducción al uso de R Server en HDInsight (versión preliminar)](hdinsight-hadoop-r-server-get-started.md). |
| sshd | 23 | SSH | Conecta los clientes a sshd en el nodo principal 1. Consulte [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md). |
| Ambari | 443 | HTTPS | Interfaz de usuario web de Ambari. Consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md). |
| Ambari | 443 | HTTPS | API de REST de Ambari. Consulte [Administración de clústeres de HDInsight con la API de REST de Ambari](hdinsight-hadoop-manage-ambari-rest-api.md). |
| WebHCat | 443 | HTTPS | API de REST de HCatalog. Consulte [Uso de Hive con Curl](hdinsight-hadoop-use-Pig-curl.md), [Uso de Pig con Curl](hdinsight-hadoop-use-Pig-curl.md) y [Uso de MapReduce con Curl](hdinsight-hadoop-use-mapreduce-curl.md). |
| HiveServer2 | 443 | ODBC | Conecta a Hive mediante ODBC. Consulte [Conexión de Excel en HDInsight con el controlador ODBC de Microsoft](hdinsight-connect-excel-hive-odbc-driver). |
| HiveServer2 | 443 | JDBC | Conecta a Hive mediante JDBC. Consulte [Conexión a Hive en HDInsight de Azure con el controlador JDBC de Hive](hdinsight-connect-hive-jdbc-driver.md). |

Las siguientes opciones están disponibles para determinados tipos de clúster:

| Servicio | Port | Protocolo |Tipo de clúster | Descripción |
| ------------ | ---- |  ----------- | --- | ----------- |
| Stargate | 443 | HTTPS | HBase | API de REST de HBase. Consulte [Introducción al uso de HBase](hdinsight-hbase-tutorial-get-started-linux.md). |
| Livy | 443 | HTTPS | Spark | API de REST de Spark. Consulte [Envío de trabajos de Spark remotamente a un clúster de Apache Spark en HDInsight Linux mediante Livy](hdinsight-apache-spark-livy-rest-interface.md). |
| Storm | 443 | HTTPS | Storm | La interfaz de usuario web de Storm. Consulte [Implementación y administración de topologías de Apache Storm en HDInsight basado en Linux](hdinsight-storm-deploy-monitor-topology-linux.md).

### Autenticación

Todos los servicios expuestos públicamente en Internet se deben autenticar:

| Port | Credenciales |
| ---- | ----------- |
| 22 o 23 | Las credenciales de usuario de SSH especificadas durante la creación del clúster. |
| 443 | El nombre de inicio de sesión (predeterminado: admin) y la contraseña que se establecieron durante la creación del clúster. |

## Puertos no públicos

### Puertos HDFS

| Servicio | Nodo(s) | Port | Protocolo | Descripción |
| ------- | ------- | ---- | -------- | ----------- | 
| Interfaz de usuario web de NameNode | Nodos principales | 30070 | HTTPS | Interfaz de usuario web para ver el estado actual |
| Servicio de metadatos de NameNode | nodos principales | 8020 | IPC | Metadatos del sistema de archivos 
| DataNode | Todos los nodos de trabajo | 30075 | HTTPS | Interfaz de usuario web para ver el estado, los registros, etc. |
| DataNode | Todos los nodos de trabajo | 30010 | &nbsp; | Transferencia de datos |
| DataNode | Todos los nodos de trabajo | 30020 | IPC | Operaciones de metadatos |
| NameNode secundario | Nodos principales | 50090 | HTTP | Punto de control para metadatos de NameNode |
### Puertos de YARN

| Servicio | Nodo(s) | Port | Protocolo | Descripción |
| ------- | ------- | ---- | -------- | ----------- |
| Interfaz de usuario web de Resource Manager | Nodos principales | 8088 | HTTP | Interfaz de usuario web para Resource Manager |
| Interfaz de usuario web de Resource Manager | Nodos principales | 8090 | HTTPS | Interfaz de usuario web para Resource Manager |
| Interfaz de administración de Resource Manager | nodos principales | 8141 | IPC | Para envíos de aplicaciones (Hive, servidor de Hive, Pig, etc.) |
| Programador de Resource Manager | nodos principales | 8030 | HTTP | Interfaz administrativa |
| Interfaz de aplicación de Resource Manager | nodos principales | 8050 | HTTP |Dirección de la interfaz del administrador de aplicaciones |
| NodeManager | Todos los nodos de trabajo | 30050 | &nbsp; | La dirección del administrador de contenedores |
| Interfaz de usuario web de NodeManager | Todos los nodos de trabajo | 30060 | HTTP | Interfaz de Resource Manager |
| Dirección de escala de tiempo | Nodos principales | 10200 | RPC | El servicio RPC del servicio de escala de tiempo. |
| Interfaz de usuario web de escala de tiempo | Nodos principales | 8181 | HTTP | La interfaz de usuario web del servicio de escala de tiempo |

### Puertos de Hive

| Servicio | Nodo(s) | Port | Protocolo | Descripción |
| ------- | ------- | ---- | -------- | ----------- |
| HiveServer2 | Nodos principales | 10001 | Thrift | Servicio para conectarse mediante programación a Hive (Thrift/JDBC) |
| HiveServer | Nodos principales | 10000 | Thrift | Servicio para conectarse mediante programación a Hive (Thrift/JDBC) |
| Tienda de metadatos Hive | Nodos principales | 9083 | Thrift | Servicio para conectarse mediante programación a metadatos de Hive (Thrift/JDBC) |

### Puertos de WebHCat

| Servicio | Nodo(s) | Port | Protocolo | Descripción |
| ------- | ------- | ---- | -------- | ----------- |
| Servidor de WebHCat | Nodos principales | 30111 | HTTP | Web API encima de HCatalog y otros servicios de Hadoop |

### Puertos de MapReduce

| Servicio | Nodo(s) | Port | Protocolo | Descripción |
| ------- | ------- | ---- | -------- | ----------- |
| Historial de trabajos | Nodos principales | 19888 | HTTP | Interfaz de usuario web del historial de trabajos de MapReduce |
| Historial de trabajos | Nodos principales | 10020 | &nbsp; | Servidor de historial de trabajos de MapReduce |
| ShuffleHandler | &nbsp; | 13562 | &nbsp; | Transfiere las salidas de mapa intermedias a los reductores solicitantes |

### Oozie

| Servicio | Nodo(s) | Port | Protocolo | Descripción |
| ------- | ------- | ---- | -------- | ----------- |
| Servidor de Oozie | Nodos principales | 11000 | HTTP | Dirección URL del servicio de Oozie |
| Servidor de Oozie | Nodos principales | 11001 | HTTP | Puerto de administración de Oozie |

### Métricas de Ambari

| Servicio | Nodo(s) | Port | Protocolo | Descripción |
| ------- | ------- | ---- | -------- | ----------- |
| Escala de tiempo (historial de aplicaciones) | Nodos principales | 6188 | HTTP | La interfaz de usuario web del servicio de escala de tiempo |
| Escala de tiempo (historial de aplicaciones) | Nodos principales | 30200 | RPC | La interfaz de usuario web del servicio de escala de tiempo |

### Puertos de HBase

| Servicio | Nodo(s) | Port | Protocolo | Descripción |
| ------- | ------- | ---- | -------- | ----------- |
| HMaster | Nodos principales | 16000 | &nbsp; | &nbsp; |
| Interfaz de usuario web de información de HMaster | Nodos principales | 16010 | HTTP | El puerto de la interfaz de usuario web de HBase Master |
| Servidor de región | Todos los nodos de trabajo | 16020 | &nbsp; | &nbsp; |
| &nbsp; | &nbsp; | 2181 | &nbsp; | El puerto que los clientes utilizan para conectarse a ZooKeeper |

<!---HONumber=AcomDC_0622_2016-->