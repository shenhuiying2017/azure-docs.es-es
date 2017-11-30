---
title: "Creación de una aplicación de Scala para su ejecución en clústeres de Spark - Azure HDInsight | Microsoft Docs"
description: "Cree una aplicación de Spark escrita en Scala con Apache Maven como sistema de compilación y con un arquetipo existente de Maven para Scala proporcionado por IntelliJ IDEA."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b2467a40-a340-4b80-bb00-f2c3339db57b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 24becbf5f539ac031d6f5b217a4c5a6b59f572f8
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/29/2017
---
# <a name="create-a-scala-maven-application-to-run-on-apache-spark-cluster-on-hdinsight"></a>Creación de una aplicación de Scala Maven para su ejecución en un clúster de Apache Spark en HDInsight

Obtenga información acerca de cómo crear una aplicación Spark escrita en Scala mediante Maven con IntelliJ IDEA. En el artículo se utiliza Apache Maven como el sistema de compilación y comienza con un arquetipo existente de Maven para Scala proporcionado por IntelliJ IDEA.  Crear una aplicación de Scala en IntelliJ IDEA conlleva los pasos siguientes:

* Use Maven como el sistema de compilación.
* Actualice el archivo del modelo de objetos de proyectos (POM) para resolver las dependencias de módulo de Spark.
* Escriba la aplicación con Scala.
* Genere un archivo jar que se pueda enviar a los clústeres de HDInsight Spark.
* Ejecute la aplicación en un clúster Spark mediante Livy.

> [!NOTE]
> HDInsight también proporciona una herramienta de complemento IntelliJ IDEA para facilitar el proceso de creación y envío de aplicaciones a un clúster de HDInsight Spark en Linux. Para más información, vea [Uso del complemento Herramientas de HDInsight para IntelliJ IDEA para crear aplicaciones Spark en Scala para clúster HDInsight Spark Linux](apache-spark-intellij-tool-plugin.md).
> 
> 

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un clúster de Apache Spark en HDInsight. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](apache-spark-jupyter-spark-sql.md).
* Kit de desarrollo de Oracle Java. Se puede instalar desde [aquí](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* Un IDE de Java. En este artículo se usa IntelliJ IDEA 15.0.1. Se puede instalar desde [aquí](https://www.jetbrains.com/idea/download/).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instale el complemento de Scala para IntelliJ IDEA.
Si la instalación de IntelliJ IDEA no pide habilitar el complemento de Scala, inicie IntelliJ IDEA y siga los pasos siguientes para instalar el complemento:

1. Inicie IntelliJ IDEA y, en la pantalla de bienvenida, haga clic en **Configure** (Configurar) y luego en **Plugins** (Complementos).
   
    ![Habilitar complemento de Scala](./media/apache-spark-create-standalone-application/enable-scala-plugin.png)
2. En la siguiente pantalla, haga clic en **Install JetBrains plugin** (Instalar complemento de JetBrains) en la esquina inferior izquierda. En el cuadro de diálogo **Browse JetBrains Plugins** (Examinar complementos de JetBrains) que se abre, busque Scala y luego haga clic en **Install** (Instalar).
   
    ![Instalar complemento de Scala](./media/apache-spark-create-standalone-application/install-scala-plugin.png)
3. Después de que el complemento se instala correctamente, haga clic en el **botón Restart IntelliJ IDEA** (Reiniciar IntelliJ IDEA) para reiniciar el IDE.

## <a name="create-a-standalone-scala-project"></a>Creación de un proyecto de Scala independiente
1. Inicie IntelliJ IDEA y cree un nuevo proyecto. En el cuadro de diálogo del nuevo proyecto, realice las siguientes selecciones y después haga clic en **Next**(Siguiente).
   
    ![Crear proyecto de Maven](./media/apache-spark-create-standalone-application/create-maven-project.png)
   
   * Seleccione **Maven** como el tipo de proyecto.
   * Especifique un **SDK de proyecto**. Haga clic en New (Nuevo) y navegue hasta el directorio de instalación de Java, normalmente `C:\Program Files\Java\jdk1.8.0_66`.
   * Seleccione la opción **Create from archetype** (Crear desde arquetipo).
   * En la lista de arquetipos, seleccione **org.scala-tools.archetypes:scala-archetype-simple**. De esta forma, se creará la estructura de directorios adecuada y se descargarán las dependencias predeterminadas necesarias para escribir el programa con Scala.
2. Proporcione los valores correspondientes para **GroupId**, **ArtifactId** y **Version**. Haga clic en **Siguiente**.
3. En el cuadro de diálogo siguiente, donde puede especificar el directorio principal de Maven y otras configuraciones de usuario, acepte los valores predeterminados y haga clic en **Next**(Siguiente).
4. En el último cuadro de diálogo, especifique un nombre de proyecto y una ubicación y luego haga clic en **Finish**(Finalizar).
5. Elimine el archivo **MySpec.Scala** en **src\test\scala\com\microsoft\spark\example**. No es necesario para la aplicación.
6. Si es necesario, cambie el nombre de los archivos de origen y de prueba predeterminados. En el panel izquierdo de IntelliJ IDEA, vaya a **src\main\scala\com.microsoft.spark.example**. Haga clic con el botón derecho en **App.scala**, haga clic en **Refactor** (Refactorizar), haga clic en Rename file (Cambiar nombre del archivo) y, en el cuadro de diálogo, proporcione el nombre nuevo para la aplicación y haga clic en **Refactor** (Refactorizar).
   
    ![Cambiar el nombre de los archivos](./media/apache-spark-create-standalone-application/rename-scala-files.png)  
7. En los pasos siguientes, se actualizará el archivo pom.xml para definir las dependencias de la aplicación Spark escrita con Scala. Para que tales dependencias se descarguen y resuelvan automáticamente, debe configurar Maven según corresponda.
   
    ![Configurar Maven para descargas automáticas](./media/apache-spark-create-standalone-application/configure-maven.png)
   
   1. En el menú **File** (Archivo), haga clic en **Settings** (Configuración).
   2. En el cuadro de diálogo **Settings** (Configuración), vaya a **Build, Execution, Deployment (Compilación, ejecución, implementación)** > **Build Tools (Herramientas de compilación)** > **Maven** > **Importing** (Importar).
   3. Seleccione la opción **Import Maven projects automatically**(Importar proyectos de Maven automáticamente).
   4. Haga clic en **Apply** (Aplicar) y en **OK** (Aceptar).
8. Actualice el archivo de origen de Scala para incluir el código de aplicación. Abra y reemplace el código de ejemplo existente con el código siguiente y guarde los cambios. Este código lee los datos de HVAC.csv (disponible en todos los clústeres de HDInsight Spark), recupera las filas que solo tienen un dígito en la sexta columna y escribe el resultado en **/HVACOut** bajo el contenedor de almacenamiento predeterminado para el clúster.
   
        package com.microsoft.spark.example
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        /**
          * Test IO to wasb
          */
        object WasbIOTest {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("WASBIOTest")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACout")
          }
        }
9. Actualice el archivo pom.xml.
   
   1. En `<project>\<properties>`, agregue lo siguiente:
      
          <scala.version>2.10.4</scala.version>
          <scala.compat.version>2.10.4</scala.compat.version>
          <scala.binary.version>2.10</scala.binary.version>
   2. En `<project>\<dependencies>`, agregue lo siguiente:
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>1.4.1</version>
           </dependency>
      
      Guarde los cambios en pom.xml.
10. Cree el archivo .jar. IntelliJ IDEA permite crear JAR como un artefacto de un proyecto. Lleve a cabo los siguiente pasos.
    
    1. En el menú **File** (Archivo), haga clic en **Project Structure** (Estructura del proyecto).
    2. En el cuadro de diálogo **Project Structure** (Estructura de proyecto), haga clic en **Artifacts** (Artefactos) y en el signo más. En el cuadro de diálogo emergente, haga clic en **JAR** y en **From modules with dependencies** (Desde módulos con dependencias).
       
        ![Crear archivo JAR](./media/apache-spark-create-standalone-application/create-jar-1.png)
    3. En el cuadro de diálogo **Create JAR** from Modules (Crear archivo JAR desde módulos), haga clic en el botón de puntos suspensivos (![puntos suspensivos](./media/apache-spark-create-standalone-application/ellipsis.png)) en la **clase principal**.
    4. En el cuadro de diálogo **Select Main Class** (Seleccionar clase principal), seleccione la clase que aparece de forma predeterminada y haga clic en **OK** (Aceptar).
       
        ![Crear archivo JAR](./media/apache-spark-create-standalone-application/create-jar-2.png)
    5. En el cuadro de diálogo **Create JAR from Modules** (Crear archivo JAR desde módulos), asegúrese de que está seleccionada la opción para **extraer al archivo JAR de destino** y haga clic en **OK** (Aceptar). Así se crea un archivo JAR único con todas las dependencias.
       
        ![Crear archivo JAR](./media/apache-spark-create-standalone-application/create-jar-3.png)
    6. La pestaña Output Layout (Diseño de salida) enumera todos los archivos JAR que forman parte del proyecto Maven. Puede seleccionar y eliminar aquellos de los que la aplicación de Scala no tenga ninguna dependencia directa. En el caso de la aplicación que creamos aquí, puede quitar todas las instancias de (**salida de compilación de SparkSimpleApp**) menos la última. Seleccione los archivos JAR que va a eliminar y, a continuación, haga clic en el icono **Delete** (Eliminar).
       
        ![Crear archivo JAR](./media/apache-spark-create-standalone-application/delete-output-jars.png)
       
        Asegúrese de que la casilla **Build on make** (Compilar al crear) está activada, lo que garantiza que el archivo jar se crea cada vez que el proyecto se compila o actualiza. Haga clic en **Apply** (Aplicar) y en **OK** (Aceptar).
    7. En la barra de menús, haga clic en **Build** (Compilar) y en **Make Project** (Crear proyecto). También puede hacer clic en **Build Artifacts** (Compilar artefactos) para crear el archivo JAR. El archivo JAR de salida se crea en **\out\artifacts**.
       
        ![Crear archivo JAR](./media/apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-spark-cluster"></a>Ejecución de la aplicación en el clúster Spark
Para ejecutar la aplicación en el clúster, debe hacer lo siguiente:

* **Copie el archivo jar de la aplicación en el blob de almacenamiento de Azure** asociado con el clúster. Puede usar [**AzCopy**](../../storage/common/storage-use-azcopy.md), una utilidad de línea de comandos, para hacerlo. Hay muchos otros clientes que se pueden utilizar también para cargar datos. Puede encontrar más información al respecto en [Carga de datos para trabajos de Hadoop en HDInsight](../hdinsight-upload-data.md).
* **Use Livy para enviar un trabajo de la aplicación de manera remota** al clúster Spark. Los clústeres Spark en HDInsight incluye Livy, que expone los puntos de conexión REST para enviar trabajos de Spark de forma remota. Para obtener más información, vea [Envío remoto de trabajos de Spark mediante la utilización de Livy con clústeres Spark en HDInsight](apache-spark-livy-rest-interface.md).

## <a name="next-step"></a>Paso siguiente

En este artículo ha aprendido a crear una aplicación de Scala en Spark. Vaya al siguiente artículo para aprender a ejecutar esta aplicación en un clúster de HDInsight Spark con Livy.

> [!div class="nextstepaction"]
>[Ejecutar trabajos de forma remota en un clúster de Spark mediante Livy](apache-spark-livy-rest-interface.md)

