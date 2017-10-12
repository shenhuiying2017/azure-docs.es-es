---
title: Uso del kit de herramientas de Azure para IntelliJ con Hortonworks Sandbox | Microsoft Docs
description: Aprenda a usar las herramientas de HDInsight del kit de herramientas de Azure para IntelliJ con Hortonworks Sandbox.
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
ms.date: 09/06/2017
ms.author: jgao
ms.openlocfilehash: 5b038f73cadc43207d86995a5323db6b5fe9b65d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Uso de las herramientas de HDInsight para IntelliJ con Hortonworks Sandbox

Aprenda a usar las herramientas de HDInsight para IntelliJ para desarrollar aplicaciones de Apache Scala y después probar las aplicaciones en la instancia de [Hortonworks Sandbox](http://hortonworks.com/products/sandbox/) que ejecuta en la estación de trabajo. 

[IntelliJ IDEA](https://www.jetbrains.com/idea/) es un entorno de desarrollo integrado (IDE) de Java para desarrollar software informático. Después de desarrollar y probar las aplicaciones en Hortonworks Sandbox, puede moverlas a [Azure HDInsight](hdinsight-hadoop-introduction.md).

## <a name="prerequisites"></a>Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

- Hortonworks Data Platform (HDP) 2.4 en Hortonworks Sandbox ejecutándose en el equipo local. Para configurar HDP, consulte [Introducción al ecosistema de Hadoop con un espacio aislado de Hadoop en una máquina virtual](hdinsight-hadoop-emulator-get-started.md). 
    > [!NOTE]
    > Las herramientas de HDInsight para IntelliJ solo se han probado con HDP 2.4. Para obtener HDP 2.4, expanda **Hortonworks Sandbox Archive** en [el sitio de descarga de Hortonworks Sandbox](http://hortonworks.com/downloads/#sandbox) para obtenerlo.

- [Kit de desarrolladores de Java (JDK) versión 1.8 o posterior](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). El kit de herramientas de Azure para IntelliJ requiere JDK.

- [Edición de la comunidad de IntelliJ IDEA](https://www.jetbrains.com/idea/download) con el complemento [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) y el complemento [Kit de herramientas de Azure para IntelliJ](../azure-toolkit-for-intellij.md). Las herramientas de HDInsight para IntelliJ están disponibles como parte del kit de herramientas de Azure para IntelliJ. 

Para instalar los complementos, siga estos pasos:

  1. Abra IntelliJ IDEA.
  2. En la pantalla de **bienvenida**, haga clic en **Configure** (Configurar) y luego haga clic en **Plugins** (Complementos).
  3. En la esquina inferior izquierda, seleccione **Install JetBrains plugin** (Instalar el complemento de JetBrains).
  4. Use la función de búsqueda para buscar **Scala** y luego seleccione **Install** (Instalar).
  5. Seleccione **Restart IntelliJ IDEA** (Reiniciar IntelliJ IDEA) para finalizar la instalación.
  6. Repita el paso 4 y 5 para instalar el **kit de herramientas de Azure para IntelliJ**. Para más información, vea [Instalación del kit de herramientas de Azure para IntelliJ](../azure-toolkit-for-intellij-installation.md).

## <a name="create-a-spark-scala-application"></a>Creación de una aplicación de Scala en Spark

En esta sección, creará un proyecto de Scala de ejemplo con IntelliJ IDEA. En la siguiente sección, vinculará IntelliJ IDEA con Hortonworks Sandbox (emulador) antes de enviar el proyecto.

1. Abra IntelliJ IDEA en el equipo. En el cuadro de diálogo **Nuevo proyecto**, siga estos pasos:

   1. Seleccione **HDInsight** > **Spark en HDInsight (Scala)**.
   2. En la lista **Build tool** (herramienta de compilación), seleccione uno de los siguientes valores, en función de su escenario:

    * **Maven**: para agregar compatibilidad con el asistente para crear un proyecto de Scala.
    * **SBT**: para administrar las dependencias y compilar el proyecto de Scala.

   ![Cuadro de diálogo Nuevo proyecto](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Seleccione **Siguiente**.
3. En el cuadro de diálogo **New Project** (Nuevo proyecto), siga estos pasos:

    1. En el cuadro **Nombre del proyecto**, escriba un nombre de proyecto.
    2. En el cuadro **Ubicación del proyecto**, escriba una ubicación del proyecto.
    3. Junto a la lista desplegable **Project SDK** (SDK de proyecto), seleccione **New** (Nuevo), elija **JDK** y, después, especifique la carpeta de Java JDK versión 1.7 o posterior. Seleccione **Java 1.8** para el clúster de Spark 2.x. Seleccione **Java 1.7** para el clúster de Spark 1.x. La ubicación predeterminada es C:\Program Files\Java\jdk1.8.x_xxx.
    4. En la lista desplegable **Spark version** (Versión de Spark), el asistente para la creación de proyectos de Scala integra la versión correcta del SDK de Spark y el SDK de Scala. Si la versión del clúster de Spark es inferior a la 2.0, seleccione **Spark 1.x**. De lo contrario, seleccione **Spark 2.x**. Este ejemplo utiliza Spark 1.6.2 (Scala 2.10.5). Asegúrese de que está usando el repositorio marcado como **Scala 2.10.x**. No utilice el repositorio marcado como Scala 2.11.x.
    
    ![Creación de las propiedades del proyecto de Scala en IntelliJ](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Seleccione **Finalizar**.
5. Si la vista de **proyecto** ya no está abierta, presione **Alt + 1** para abrirla.
6. En **Project Explorer** (Explorador de proyectos), expanda el proyecto y luego seleccione **src**.
7. Haga clic con el botón derecho en **src**, seleccione **New** (Nuevo) y luego seleccione **Scala class** (Clase de Scala).
8. En el cuadro **Name** (Nombre), escriba un nombre. En el cuadro **Kind** (Variante), seleccione **Object** (Objeto). Después, seleccione **Aceptar**.

    ![Cuadro de diálogo de creación de una clase de Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. En el archivo .scala, pegue el código siguiente:

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
                // Enforce that everything has been calculated and in cache.
                pairs1.count

                println(pairs1.groupByKey(numReducers).count)
            }
        }

10. En el menú **Build** (Compilar), seleccione **Build project** (Compilar proyecto). Asegúrese de que la compilación se finalice correctamente.


## <a name="link-to-the-hortonworks-sandbox"></a>Vínculo a Hortonworks Sandbox

Antes de crear un vínculo a Hortonworks Sandbox (emulador), debe tener una aplicación de IntelliJ existente.

Para crear un vínculo a un emulador, siga estos pasos:

1. Abra el proyecto en IntelliJ.
2. En el menú **View** (Ver), seleccione **Tools Windows** (Ventanas de herramientas) y luego seleccione **Azure Explorer** (Explorador de Azure).
3. Expanda **Azure**, haga clic con el botón derecho en **HDInsight** y luego seleccione **Link an Emulator** (Vincular un emulador).
4. En el cuadro de diálogo **Link A New Emulator** (Vincular un nuevo emulador), escriba la contraseña que haya configurado para la cuenta raíz de Hortonworks Sandbox. Después, escriba valores similares a aquellos utilizados en la captura de pantalla siguiente. Después, seleccione **Aceptar**. 

   ![Cuadro de diálogo Link A New Emulator (Vincular un nuevo emulador)](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Para configurar el emulador, haga clic en **Yes** (Sí).

Cuando el emulador está conectado correctamente, el emulador (Hortonworks Sandbox) aparece en el nodo de HDInsight.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Envío de la aplicación Scala de Spark a Hortonworks Sandbox

Después de haber vinculado IntelliJ IDEA al emulador, puede enviar el proyecto.

Para enviar un proyecto a un emulador, siga estos pasos:

1. En el **explorador de proyectos**, haga clic con el botón derecho en el proyecto y seleccione **Submit Spark Application to HDInsight** (Enviar aplicación Spark a HDInsight).
2. Complete los siguientes pasos:

    1. En la lista desplegable **Spark cluster (Linux only)** (Clúster de Spark [solo Linux]), seleccione su instancia local de Hortonworks Sandbox.
    2. En el cuadro **Main class name** (Nombre de clase principal), elija o escriba el nombre de clase principal. En este tutorial, el nombre es **GroupByTest**.

3. Seleccione **Submit** (Enviar). Los registros de envío de trabajo se muestran en la ventana de la herramienta de envío de Spark.

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [utilizar las herramientas de HDInsight del kit de herramientas de Azure para IntelliJ con el fin de crear aplicaciones Spark destinadas al clúster Spark en HDInsight (Linux)](hdinsight-apache-spark-intellij-tool-plugin.md).

- Para obtener información sobre las herramientas de HDInsight para IntelliJ, vea este vídeo de[introducción sobre las herramientas de HDInsight para IntelliJ para desarrollar con Spark](https://www.youtube.com/watch?v=YTZzYVgut6c).

- Obtenga información sobre cómo [depurar aplicaciones de Spark de forma remota en un clúster de HDInsight con el kit de herramientas de Azure para IntelliJ mediante SSH](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md).

- Sepa cómo [utilizar las herramientas de HDInsight del kit de herramientas de Azure para IntelliJ para depurar de forma remota aplicaciones Spark en clústeres Spark de HDInsight (Linux)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).

- Aprenda a [usar las herramientas de HDInsight del kit de herramientas de Azure para Eclipse con el fin de crear aplicaciones Spark](hdinsight-apache-spark-eclipse-tool-plugin.md).

- Si quiere obtener información sobre las herramientas de HDInsight para Eclipse, vea este vídeo sobre cómo [usar las herramientas de HDInsight para Eclipse para crear aplicaciones de Spark](https://mix.office.com/watch/1rau2mopb6fha).
