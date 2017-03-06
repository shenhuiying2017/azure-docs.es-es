---
title: "Instalación de R de GNU en HDInsight basado en Linux | Microsoft Docs"
description: "Obtenga información acerca de cómo instalar y usar R para personalizar los clústeres de Hadoop basados en Linux."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 7b758492-87bf-4d82-8b8c-1664e7d177bd
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfaade8249a643b77f3d7fdf466eb5ba38143f18
ms.openlocfilehash: 9dd06f04344c1ea8e4c1511c8ea05b7c17b3329b
ms.lasthandoff: 03/01/2017

---
# <a name="install-and-use-gnu-r-on-linux-based-hdinsight-hadoop-clusters"></a>Instalación y uso de R de GNU en clústeres de Hadoop para HDInsight basado en Linux

Puede instalar R de GNU en cualquier tipo de clúster de Hadoop en HDInsight mediante la personalización de clústeres de **acción de script**. Los analistas y científicos de datos pueden usar R para procesar grandes cantidades de datos en clústeres de Hadoop que se implementan en HDInsight.

> [!WARNING]
> Este documento proporciona instrucciones para instalar R de GNU en HDInsight como una acción personalizada. __No__ proporciona las mismas funcionalidades que __Microsoft R Server en HDInsight__. Se recomienda usar R Server en HDInsight en lugar de realizar los pasos de este documento.
>
> Para información sobre cómo usar R Server en HDInsight, vea [Introducción al uso de R Server en HDInsight](hdinsight-hadoop-r-server-get-started.md). 

## <a name="what-is-r"></a>¿Qué es R?

El <a href="http://www.r-project.org/" target="_blank">proyecto R para el cálculo estadístico</a> es un entorno y lenguaje de código abierto para el cálculo estadístico. R proporciona cientos de de funciones estadísticas integradas y su propio lenguaje de programación que combina aspectos de la programación funcional y orientada a objetos. También proporciona amplias capacidades gráficas. R es el entorno de programación preferido para los científicos y los estadísticos más profesionales de una amplia variedad de campos.

Los scripts de R se pueden ejecutar en clústeres de Hadoop en HDInsight que se han personalizado mediante la acción de script cuando se creó para instalar el entorno de R. R es compatible con el almacenamiento de blobs de Azure (WASB) para que los datos que se almacenan allí se puedan procesar mediante R en HDInsight.

## <a name="what-the-script-does"></a>Funcionamiento del script

La acción de script usada para instalar R en el clúster de HDInsight instala los siguientes paquetes de Ubuntu, que proporcionan una instalación básica de R:

* [r base](http://packages.ubuntu.com/precise/r-base): paquete base de R de GNU.
* [r-base-dev](http://packages.ubuntu.com/precise/r-base-dev): paquetes auxiliares de R de GNU.

También se instalan los siguientes paquetes de RHadoop, que proporcionan integración con MapReduce y HDFS:

* [rmr2](https://github.com/RevolutionAnalytics/rmr2): permite a los desarrolladores de R usar MapReduce de Hadoop.
* [rhdfs](https://github.com/RevolutionAnalytics/rhdfs): permite a los desarrolladores de R usar Hadoop HDFS (WASB para HDInsight).

Además, se instalan los siguientes paquetes de R:

| Paquete de R | Qué proporciona |
| --- | --- |
| [rJava](https://cran.r-project.org/web/packages/rJava/index.html) |Una interfaz de bajo nivel entre R y Java. |
| [Rcpp](https://cran.r-project.org/web/packages/Rcpp/index.html) |Integración de R y C++. |
| [RJSONIO](https://cran.r-project.org/web/packages/RJSONIO/index.html) |Serialización y deserialización de objetos R en JSON. |
| [bitops](https://cran.r-project.org/web/packages/bitops/index.html) |Funciones para las operaciones bit a bit en vectores de entero. |
| [digest](https://cran.r-project.org/web/packages/digest/index.html) |Creación de resúmenes de hash criptográficos de objetos de R. |
| [functional](https://cran.r-project.org/web/packages/functional/index.html) |Curry, Compose y otras funciones de orden superior |
| [reshape2](https://cran.r-project.org/web/packages/reshape2/index.html) |Reestructuración flexible y adición de datos. |
| [stringr](https://cran.r-project.org/web/packages/stringr/index.html) |Contenedores sencillos y coherentes para operaciones comunes de cadena. |
| [plyr](https://cran.r-project.org/web/packages/plyr/index.html) |Herramientas para dividir, aplicar y combinar datos. |
| [caTools](https://cran.r-project.org/web/packages/caTools/index.html) |Herramientas para mover estadísticas de ventana, GIF, Base64, AUC de ROC, etc. |
| [stringdist](https://cran.r-project.org/web/packages/stringdist/index.html) |Funciones de coincidencia aproximada de cadenas y distancia de cadenas. |

## <a name="install-r-using-script-actions"></a>Instalación de R mediante acciones de script

Se usa la acción de script siguiente para instalar R en un clúster de HDInsight. https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh

En esta sección se proporcionan instrucciones sobre cómo usar el script durante la creación de un clúster mediante Azure Portal. 

> [!NOTE]
> También se puede utilizar Azure PowerShell, la CLI de Azure, el SDK de .NET de HDInsight o las plantillas de Azure Resource Manager para aplicar las acciones de script. También puede aplicar acciones de script a clústeres que ya se estén ejecutando. Para obtener más información, consulte [Personalización de clústeres de HDInsight mediante la acción de scripts](hdinsight-hadoop-customize-cluster-linux.md).

1. Inicie el aprovisionamiento de un clúster siguiendo los pasos que se describen en [Aprovisionamiento de clústeres de HDInsight basado en Linux](hdinsight-hadoop-provision-linux-clusters.md), pero no complete la operación.

2. En la hoja **Configuración opcional**, seleccione **Acciones de script** y use la siguiente información para completar el formulario:
   
   * **NOMBRE**: escriba un nombre descriptivo para la acción de script.
   * **URI DE SCRIPT**: https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
   * **PRINCIPAL**: active esta opción.
   * **TRABAJADOR**: active esta opción.
   * **ZOOKEEPER**: active esta opción para instalar en el nodo Zookeeper.
   * **PARÁMETROS**: deje este campo en blanco.

3. En la parte inferior de **Acciones de scripts**, use el botón **Seleccionar** para guardar la configuración. Por último, use el botón **Seleccionar** situado en la parte inferior de la hoja **Configuración opcional** para guardar la información de configuración opcional.

4. Siga aprovisionando el clúster como se describe en [Aprovisionamiento de clústeres de HDInsight basado en Linux](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="run-r-scripts"></a>Ejecutar scripts de R

Cuando termine de aprovisionar el clúster, siga estos pasos para usar R para realizar una operación de MapReduce en él.

1. Conéctese al clúster de HDInsight con SSH:
   
        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
   
    Para más información sobre el uso de SSH con HDInsight, consulte los documentos siguientes:
   
   * [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Bash en Windows 10, Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
   * [Uso de SSH con Hadoop en HDInsight basado en Linux desde Windows con PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md)

2. En el símbolo del sistema de `username@hn0-CLUSTERNAME:~$` , escriba el siguiente comando para iniciar una sesión interactiva de R:
   
        R

3. Escriba el programa de R siguiente. Este código genera los números del 1 al 100 y luego los multiplica por 2.
   
        library(rmr2)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
   
    La primera línea llama a rmr2 de la biblioteca de RHadoop, que se usa en las operaciones de MapReduce.
   
    La segunda línea genera valores del 1 al 100 y luego los almacena en el sistema de archivos de Hadoop mediante `to.dfs`.
   
    La tercera línea crea un proceso de MapReduce mediante la funcionalidad proporcionada por rmr2 y comienza el procesamiento. Debe ver cómo varias líneas se desplazan más allá a medida que comienza el procesamiento.

4. A continuación, use lo siguiente para ver la ruta de acceso temporal en la que se almacenó la salida de MapReduce:
   
        print(calc())
   
    Este comando devuelve un valor similar a `/tmp/file5f615d870ad2`. Para ver el resultado real, use el siguiente comando:
   
        print(from.dfs(calc))
   
    El siguiente texto es un ejemplo de la salida:
   
        [1,]  1 2
        [2,]  2 4
        .
        .
        .
        [98,]  98 196
        [99,]  99 198
        [100,] 100 200

5. Para salir de R, escriba el siguiente comando:
   
        q()

## <a name="next-steps"></a>Pasos siguientes

* [Instalación de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install.md). Use la personalización del clúster para instalar Giraph en clústeres de Hadoop para HDInsight. Giraph permite realizar un procesamiento gráfico con Hadoop y se puede usar con HDInsight de Azure.
* [Instalación de Solr en clústeres de HDInsight](hdinsight-hadoop-solr-install.md). Use la personalización del clúster para instalar Solr en clústeres de Hadoop para HDInsight. Solr le permite realizar potentes operaciones de búsqueda en los datos almacenados.
* [Instalación de Hue en clústeres de HDInsight](hdinsight-hadoop-hue-linux.md). Use la personalización del clúster para instalar Hue en clústeres de Hadoop para HDInsight. Hue es un conjunto de aplicaciones web que usan para interactuar con un clúster de Hadoop.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md

