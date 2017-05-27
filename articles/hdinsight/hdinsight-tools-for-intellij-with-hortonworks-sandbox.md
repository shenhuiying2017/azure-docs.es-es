---
title: Uso del kit de herramientas de Azure para IntelliJ con Hortonworks Sandbox | Microsoft Docs
description: Aprenda a usar las Herramientas de HDInsight del kit de herramientas de Azure para IntelliJ con Hortonworks Sandbox.
keywords: herramientas de hadoop,consulte de hive,intellij,hortonworks sandbox,kit de herramientas de azure para intellij
services: HDInsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: b587cc9b-a41a-49ac-998f-b54d6c0bdfe0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/25/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: bb700c7de96712666bc4be1f8e430a2e94761f69
ms.openlocfilehash: b52e5bcf443afd05bf8cc37ce436cb80ab41412f
ms.contentlocale: es-es
ms.lasthandoff: 01/24/2017


---
#<a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Uso de las herramientas de HDInsight para IntelliJ con Hortonworks Sandbox

Aprenda a usar las Herramientas de HDInsight para IntelliJ para desarrollar aplicaciones de Apache Scala y probar las aplicaciones en [Hortonworks Sandbox](http://hortonworks.com/products/sandbox/) cuando se ejecuta en la estación de trabajo. [IntelliJ IDEA](https://www.jetbrains.com/idea/) es un entorno de desarrollo integrado (IDE) de Java para el desarrollo de software informático. Después de desarrollar y probar las aplicaciones en Hortonworks Sandbox, puede moverlas a [Azure HDInsight](hdinsight-hadoop-introduction.md).

##<a name="prerequisites"></a>Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

- HDP 2.4 en Hortonworks Sandbox ejecutándose en el entorno local. Para realizar la configuración, consulte [Introducción al ecosistema de Hadoop con un espacio aislado de Hadoop en una máquina virtual](hdinsight-hadoop-emulator-get-started.md). Tenga en cuenta que las Herramientas de HDInsight para IntelliJ solo se han probado con HDP 2.4. En el [sitio de descarga de Hortonworks Sandbox](http://hortonworks.com/downloads/#sandbox), expanda **Hortonworks Sandbox Archive** para obtenerlo.
- [Kit de desarrolladores de Java (JDK) versión 1.8 o posterior](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). El kit de herramientas de Azure para IntelliJ requiere JDK.
- [Edición de la comunidad de IntelliJ IDEA](https://www.jetbrains.com/idea/download) con el complemento [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) y el complemento [Kit de herramientas de Azure para IntelliJ](../azure-toolkit-for-intellij.md). Las Herramientas de HDInsight para IntelliJ están disponibles como parte del kit de herramientas de Azure para IntelliJ. 

  **Para instalar los complementos:**

  1. Abra IntelliJ IDEA.
  2. En la pantalla de bienvenida, haga clic en **Configure** (Configurar) y luego haga clic en **Plugins** (Complementos).
  3. En la esquina inferior izquierda, haga clic en **Install JetBrains plugin** (Instalar el complemento de JetBrains).
  4. Use la función de búsqueda para buscar **Scala** y luego haga clic en **Install** (Instalar).
  5. Haga clic en **Restart IntelliJ IDEA** (Reiniciar IntelliJ IDEA) para finalizar la instalación.
  6. Repita el paso 4 y 5 para instalar el **kit de herramientas de Azure para IntelliJ**. Para más información, consulte [Instalación del kit de herramientas de Azure para IntelliJ](../azure-toolkit-for-intellij-installation.md).

## <a name="create-a-spark-scala-application"></a>Creación de una aplicación de Scala en Spark

En esta sección, creará un proyecto de Scala de ejemplo con IntelliJ IDEA. En la siguiente sección, vinculará IntelliJ IDEA con Hortonworks Sandbox (emulador) antes de enviar el proyecto.

1. Abra IntelliJ IDEA desde la estación de trabajo.
2. Haga clic en **Create New Project** (Crear nuevo proyecto).
3. Haga clic en **HDInsight** en el panel izquierdo, haga clic en **Spark on HDInsight(Scala)** (Spark en HDInsight [Scala]) en el panel derecho y luego haga clic en **Next** (Siguiente).

    ![Creación de un proyecto de Scala en IntelliJ](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)
4. Escriba la siguiente información:

    ![Creación de las propiedades del proyecto de Scala en IntelliJ](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)

    - **Project name** (Nombre del proyecto): proporcione un nombre para el proyecto.
    - **Project location** (Ubicación del proyecto): proporcione una ubicación para el proyecto.
    - **Project SDK** (SDK del proyecto): haga clic en **New** (Nuevo), en **JDK** y luego especifique la carpeta del JDK de Java versión 7 o posterior.  La ubicación predeterminada es C:\Program Files\Java\jdk1.8.x_xxx.
    - **Scala SDK** (SDK de Scala): haga clic en **Select** (Seleccionar), seleccione la versión **2.10.6** y luego haga clic en **OK** (Aceptar). Si no se muestra la versión, haga clic en **Download** (Descargar), seleccione **Scala version** (Versión de Scala) y haga clic en **OK** (Aceptar). Asegúrese de no usar la versión 2.11.x. En este artículo se usa la versión 2.10.6.
    - **Spark SDK** (SDK de Spark): descargue el [SDK](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409). También puede omitir este paso y usar en su lugar el repositorio de Spark Maven. Sin embargo, debe comprobar que tiene instalado el repositorio de Maven correcto para poder desarrollar aplicaciones de Spark. Por ejemplo, debe asegurarse de que tiene la parte Spark Streaming instalada si está utilizando Spark Streaming. Asimismo, asegúrese de que utiliza el repositorio marcado como Scala 2.10 y no el repositorio marcado como Scala 2.11.
5. Haga clic en **Finalizar**
6. Presione **[ALT]+1** para abrir la vista Project si no se abre.
7. En **Project Explorer** (Explorador de proyectos), expanda el proyecto y luego haga clic en **src**.
8. Haga clic con el botón derecho en **src**, seleccione **New** (Nuevo) y luego haga clic en **Scala class** (Clase de Scala).
9. Escriba un nombre, seleccione **Object** en **Kind** (Clase) y luego haga clic en **OK** (Aceptar).

    ![Creación de una nueva clase de Scala en IntelliJ](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)
10. En el archivo .scala, pegue el código siguiente:

        import java.util.Random
        import org.apache.spark.{SparkConf, SparkContext}
        import org.apache.spark.SparkContext._

        /**
        * Usage: GroupByTest [numMappers] [numKVPairs] [valSize] [numReducers]
        */
        object GroupByTest {
            def main(args: Array[String]) {
                val sparkConf = new SparkConf().setAppName("GroupBy Test")
                var numMappers = 3
                var numKVPairs = 10
                var valSize = 10
                var numReducers = 2

                val sc = new SparkContext(sparkConf)

                val pairs1 = sc.parallelize(0 until numMappers, numMappers).flatMap { p =>
                val ranGen = new Random
                var arr1 = new Array[(Int, Array[Byte])](numKVPairs)
                for (i <- 0 until numKVPairs) {
                    val byteArr = new Array[Byte](valSize)
                    ranGen.nextBytes(byteArr)
                    arr1(i) = (ranGen.nextInt(Int.MaxValue), byteArr)
                }
                arr1
                }.cache
                // Enforce that everything has been calculated and in cache
                pairs1.count

                println(pairs1.groupByKey(numReducers).count)
            }
        }

11. En el menú **Build** (Compilar), haga clic en **Build project** (Compilar proyecto). Asegúrese de que la compilación ha finalizado correctamente.


## <a name="link-to-the-hortonworks-sandbox"></a>Vínculo a HortonWorks Sandbox

Para poder crear un vínculo a Hortonworks Sandbox (emulador), debe tener una aplicación de IntelliJ existente.

**Para crear un vínculo a un emulador:**

1. Abra el proyecto en IntelliJ si aún no se ha abierto.
2. En el menú **View** (Ver), haga clic en **Tools Windows** (Ventanas de herramientas) y luego haga clic en **Azure Explorer** (Explorador de Azure).
3. Expanda **Azure**, haga clic con el botón derecho en **HDInsight** y luego haga clic en **Link an Emulator** (Vincular un emulador).
4. Escriba la contraseña que ha configurado para la cuenta raíz de Hortonworks Sandbox y el resto de valores como en la siguiente captura de pantalla. A continuación, haga clic en **OK** (Aceptar). 

  ![Vínculo de IntelliJ a un emulador](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Haga clic en **Yes** (Sí) para configurar el emulador.

  Cuando está conectado correctamente, puede ver que el emulador (Hortonworks Sandbox) aparece en el nodo de HDInsight.

## <a name="submit-the-spark-scala-application-to-the-sandbox"></a>Envío de la aplicación Scala de Spark al espacio aislado

Después de haber vinculado IntelliJ IDEA al emulador, puede enviar el proyecto.

**Para enviar un proyecto a un emulador**

1. En **Project Explorer** (Explorador de proyectos), haga clic con el botón derecho en el proyecto y seleccione **Submit Spark Application to HDInsight** (Enviar aplicación Spark a HDInsight).
2. Especifique los siguientes campos:

    - **Spark cluster (Linux only)** (Clúster de Spark [solo Linux]): seleccione su instancia local de Hortonworks Sandbox.
    - **Main class name** (Nombre de la clase principal): elija o escriba el nombre de la clase principal.  En este tutorial, es **GroupByTest**.
3. Haga clic en **Enviar**. Los registros de envío de trabajo se muestran en la ventana de la herramienta de envío de Spark.

##<a name="next-steps"></a>Pasos siguientes:

- Para aprender a crear aplicaciones de Spark para HDInsight mediante Herramientas de HDInsight para IntelliJ, consulte [Uso de las herramientas de HDInsight del kit de herramientas de Azure para IntelliJ con el fin de crear aplicaciones Spark destinadas al clúster Spark en HDInsight (Linux)](hdinsight-apache-spark-intellij-tool-plugin.md).
- Para ver un vídeo sobre las Herramientas de HDInsight para IntelliJ, vea el [vídeo de introducción sobre las Herramientas de HDInsight para IntelliJ para el desarrollo con Spark](https://mix.office.com/watch/1nqkqjt5xonza).
- Para aprender a depurar aplicaciones de Spark usando el kit de herramientas de forma remota en HDInsight, consulte [Uso de las herramientas de HDInsight del kit de herramientas de Azure para IntelliJ para depurar de forma remota aplicaciones Spark en clústeres Spark de HDInsight (Linux)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).
- Para aprender a usar las Herramientas de HDInsight para Eclipse con la finalidad de crear una aplicación Spark, consulte [Uso de las herramientas de HDInsight del kit de herramientas de Azure para Eclipse con el fin de crear aplicaciones Spark destinadas al clúster Spark en HDInsight (Linux)](hdinsight-apache-spark-eclipse-tool-plugin.md).
- Para ver un vídeo sobre las Herramientas de HDInsight para Eclipse, vea el vídeo sobre [cómo usar las Herramientas de HDInsight para Eclipse para crear aplicaciones de Spark](https://mix.office.com/watch/1rau2mopb6fha).
