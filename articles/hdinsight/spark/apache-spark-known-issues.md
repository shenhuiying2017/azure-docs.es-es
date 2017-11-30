---
title: "Solución de problemas con el clúster de Apache Spark en Azure HDInsight | Microsoft Docs"
description: "Obtenga información sobre problemas relacionados con los clústeres de Apache Spark en Azure HDInsight y cómo evitarlos."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 610c4103-ffc8-4ec0-ad06-fdaf3c4d7c10
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: bb5557eb0672b9ad137bc5817e47bf4f89e1c34d
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/29/2017
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Problemas conocidos de clústeres de Apache Spark en HDInsight

En este documento se hace un seguimiento de todos los problemas conocidos de la versión preliminar pública de HDInsight Spark.  

## <a name="livy-leaks-interactive-session"></a>Sesión interactiva con pérdidas de Livy
Cuando se reinicia Livy (desde Ambari o debido al reinicio de la máquina virtual del nodo principal 0) con una sesión interactiva activa, se perderá una sesión de trabajo interactiva. Por este motivo, los nuevos trabajos pueden bloquearse en el estado Aceptado y no se pueden iniciar.

**Mitigación:**

Utilice el procedimiento siguiente para encontrar una solución alternativa para el problema:

1. SSH en el nodo principal. Para obtener más información, consulte [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Ejecute el siguiente comando para buscar los identificadores de aplicación de los trabajos interactivos iniciados mediante Livy. 
   
        yarn application –list
   
    Los nombres predeterminados de los trabajos serán Livy si se han iniciado con una sesión interactiva de Livy sin nombres explícitos especificados; en caso de que la sesión de Livy se haya iniciado mediante el cuaderno de Jupyter, el nombre del trabajo empezará por remotesparkmagics_*. 
3. Ejecute el comando siguiente para terminar esos trabajos. 
   
        yarn application –kill <Application ID>

Los nuevos trabajos empezarán a ejecutarse. 

## <a name="spark-history-server-not-started"></a>No se ha iniciado el servidor de historial de Spark
El servidor de historial de Spark no se inicia automáticamente después de crear un clúster.  

**Mitigación:** 

Inicie el servidor de historial manualmente desde Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Problemas con permisos en el directorio de registros de Spark
Cuando hdiuser envía un trabajo con spark-submit, hay un error java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (permiso denegado) y el registro del controlador no se ha escrito. 

**Mitigación:**

1. Agregue hdiuser al grupo de Hadoop. 
2. Proporcione 777 permisos en /var/log/spark después de crear el clúster. 
3. Actualice la ubicación del registro spark con Ambari para que sea un directorio con 777 permisos.  
4. Ejecute spark-submit como sudo.  

## <a name="spark-phoenix-connector-is-not-supported"></a>No se admite el conector Spark-Phoenix

En este momento, el conector Spark-Phoenix no es compatible con un clúster de Spark de HDInsight.

**Mitigación:**

Debe usar el conector Spark-HBase en su lugar. Para obtener instrucciones al respecto, consulte [Utilización del conector Spark-HBase](https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/).

## <a name="issues-related-to-jupyter-notebooks"></a>Problemas relacionados con los cuadernos de Jupyter Notebook
A continuación, se muestran algunos problemas conocidos relacionados con los cuadernos de Jupyter Notebook.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Cuadernos que tienen caracteres no ASCII en los nombres de archivo
Los cuadernos de Jupyter Notebook que pueden utilizarse en clústeres Spark de HDInsight no deben tener caracteres que no sean ASCII en los nombres de archivo. Si trata de cargar un archivo a través de la interfaz de usuario de Jupyter con un nombre de archivo que incluye caracteres que no son ASCII, se producirá un error silenciosamente (es decir, Jupyter no permitirá cargar el archivo y tampoco se mostrará un error). 

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Error al cargar cuadernos con tamaños mayores
Es posible que aparezca un error **`Error loading notebook`** al cargar cuadernos de mayor tamaño.  

**Mitigación:**

El hecho de recibir este error no implica que los datos estén dañados o perdidos.  Los cuadernos siguen aún en el disco en `/var/lib/jupyter`y se puede conectar mediante SSH al clúster para acceder ellos. Para obtener más información, consulte [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Cuando se haya conectado al clúster mediante SSH, puede copiar sus cuadernos desde el clúster en la máquina local (mediante SCP o WinSCP) como copia de seguridad para impedir la pérdida de datos importantes en el cuaderno. A continuación, puede aplicar túneles SSH al nodo principal del puerto 8001 para tener acceso a Jupyter sin pasar por la puerta de enlace.  Desde ahí, puede borrar la salida del bloc de notas y volver a guardarla para minimizar el tamaño del bloc de notas.

Para evitar que este error ocurra en el futuro, debe seguir algunos procedimientos recomendados:

* Es importante mantener reducido el tamaño del bloc de notas. Las salidas de los trabajos de Spark que se envían a Jupyter de vuelta se conservan en el bloc de notas.  En general, con Jupyter se recomienda evitar ejecutar `.collect()` en RDD o tramas de datos de gran tamaño; en su lugar, si desea inspeccionar el contenido de un RDD, considere la posibilidad de ejecutar `.take()` o `.sample()` para que la salida no sea demasiado grande.
* Además, cuando guarde un bloc de notas, desactive todas las celdas de la salida para reducir el tamaño.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>El primer inicio del cuaderno tarda más de lo esperado
La primera instrucción del cuaderno de Jupyter con la instrucción mágica de Spark podría tardar más de un minuto.  

**Explicación:**

Esto sucede cuando se ejecuta la primera celda de código. En segundo plano se inicia la configuración de la sesión y se establecen los contextos de Spark, SQL y Hive. Una vez establecidos estos contextos, se ejecuta la primera instrucción y esto da la impresión de que la instrucción tardó mucho tiempo en completarse.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Tiempo de espera del cuaderno de Jupyter en la creación de la sesión
Cuando el clúster Spark se está quedando sin recursos, el tiempo de espera de los kernels Spark y Pyspark del cuaderno de Jupyter se agotará al tratar de crear la sesión. 

**Mitigaciones:** 

1. Libere algunos recursos del clúster Spark de la siguiente forma:
   
   * Detenga otros cuadernos de Spark en el menú Cerrar y detener o haga clic en Apagar en el explorador del cuaderno.
   * Detenga otras aplicaciones de Spark desde YARN.
2. Reinicie el cuaderno que trataba de iniciar. Debe haber suficientes recursos para crear una sesión ahora.

## <a name="see-also"></a>Otras referencias
* [Introducción a Apache Spark en HDInsight de Azure](apache-spark-overview.md)

### <a name="scenarios"></a>Escenarios
* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](apache-spark-use-bi-tools.md)
* [Creación de aplicaciones de Machine Learning con Apache Spark en HDInsight de Azure](apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Machine Learning: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](apache-spark-eventhub-streaming.md)
* [Análisis del registro del sitio web con Spark en HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creación y ejecución de aplicaciones
* [Crear una aplicación independiente con Scala](apache-spark-create-standalone-application.md)
* [Ejecutar trabajos de forma remota en un clúster de Spark mediante Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Herramientas y extensiones
* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applications (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar aplicaciones Scala Spark)](apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para depurar aplicaciones de Spark de forma remota)](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](apache-spark-job-debugging.md)

