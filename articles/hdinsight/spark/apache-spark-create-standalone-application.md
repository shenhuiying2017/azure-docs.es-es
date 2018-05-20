---
title: 'Tutorial: Creación de una aplicación de Scala Maven para Spark en HDInsight mediante IntelliJ | Microsoft Docs'
description: Cree una aplicación de Spark escrita en Scala con Apache Maven como sistema de compilación y con un arquetipo existente de Maven para Scala proporcionado por IntelliJ IDEA.
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: b2467a40-a340-4b80-bb00-f2c3339db57b
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: jgao
ms.openlocfilehash: c72f513c7134c556afa5fa5d0b94c17b1142be54
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-create-a-scala-maven-application-for-spark-in-hdinsight-using-intellij"></a>Tutorial: Creación de una aplicación de Scala Maven para Spark en HDInsight mediante IntelliJ

En este tutorial aprenderá a crear una aplicación de Spark escrita en Scala mediante Maven con IntelliJ IDEA. En el artículo se utiliza Apache Maven como el sistema de compilación y comienza con un arquetipo existente de Maven para Scala proporcionado por IntelliJ IDEA.  Crear una aplicación de Scala en IntelliJ IDEA conlleva los pasos siguientes:

* Use Maven como el sistema de compilación.
* Actualice el archivo del modelo de objetos de proyectos (POM) para resolver las dependencias de módulo de Spark.
* Escriba la aplicación con Scala.
* Genere un archivo jar que se pueda enviar a los clústeres de HDInsight Spark.
* Ejecute la aplicación en un clúster Spark mediante Livy.

> [!NOTE]
> HDInsight también proporciona una herramienta de complemento IntelliJ IDEA para facilitar el proceso de creación y envío de aplicaciones a un clúster de HDInsight Spark en Linux. Para más información, vea [Uso del complemento Herramientas de HDInsight para IntelliJ IDEA para crear aplicaciones Spark en Scala para clúster HDInsight Spark Linux](apache-spark-intellij-tool-plugin.md).
> 

En este tutorial, aprenderá a:
> [!div class="checklist"]
> * Usar IntelliJ para desarrollar una aplicación de Scala Maven

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.


## <a name="prerequisites"></a>requisitos previos

* Un clúster de Apache Spark en HDInsight. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](apache-spark-jupyter-spark-sql.md).
* Kit de desarrollo de Oracle Java. Se puede instalar desde [aquí](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* Un IDE de Java. En este artículo se usa IntelliJ IDEA 18.1.1. Se puede instalar desde [aquí](https://www.jetbrains.com/idea/download/).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instale el complemento de Scala para IntelliJ IDEA.
Para instalar el complemento Scala, siga estos pasos:

1. Abra IntelliJ IDEA.
2. En la pantalla de bienvenida, seleccione **Configure** (Configurar) y, luego, **Plugins** (Complementos).
   
    ![Habilitar complemento de Scala](./media/apache-spark-create-standalone-application/enable-scala-plugin.png)
3. En la esquina inferior izquierda, seleccione **Install JetBrains plugin** (Instalar el complemento de JetBrains). 
4. En el cuadro de diálogo **Browse JetBrains Plugins** (Examinar complementos de JetBrains), busque **Scala** y seleccione **Install** (Instalar).
   
    ![Instalar complemento de Scala](./media/apache-spark-create-standalone-application/install-scala-plugin.png)
5. Después de que el complemento se instale correctamente, debe reiniciar el IDE.

## <a name="create-a-standalone-scala-project"></a>Creación de un proyecto de Scala independiente
1. Abra IntelliJ IDEA.
2. En el menú **File** (Archivo), seleccione **New > Project** (Nuevo > Proyecto) para crear un proyecto nuevo.
3. En el cuadro de diálogo New project (Nuevo proyecto), elija lo siguiente:
   
    ![Crear proyecto de Maven](./media/apache-spark-create-standalone-application/create-maven-project.png)
   
   * Seleccione **Maven** como el tipo de proyecto.
   * Especifique un **SDK de proyecto**. Seleccione **New** (Nuevo) y vaya al directorio de instalación de Java, normalmente `C:\Program Files\Java\jdk1.8.0_66`.
   * Seleccione la opción **Create from archetype** (Crear desde arquetipo).
   * En la lista de arquetipos, seleccione **org.scala-tools.archetypes:scala-archetype-simple**. Este arquetipo crea la estructura de directorios adecuada y se descargarán las dependencias predeterminadas necesarias para escribir el programa con Scala.
4. Seleccione **Next** (Siguiente).
5. Proporcione los valores correspondientes para **GroupId**, **ArtifactId** y **Version**. Los siguientes valores se utilizan en este tutorial:

    - GroupId: com.microsoft.spark.example
    - ArtifactId: SparkSimpleApp
6. Seleccione **Next** (Siguiente).
7. Compruebe la configuración y seleccione **Next** (Siguiente).
8. Compruebe el nombre y la ubicación del proyecto, y seleccione **Finish**(Finalizar).
9. En el panel izquierdo, seleccione **src > test > scala > com > microsoft > spark > example**, haga clic con el botón derecho en **MySpec** y, después, seleccione **Delete** (Eliminar). Este archivo no es necesario para la aplicación.
  
10. En los pasos siguientes, actualizará el archivo pom.xml para definir las dependencias de la aplicación en Scala para Spark. Para que tales dependencias se descarguen y resuelvan automáticamente, debe configurar Maven según corresponda.
   
    ![Configurar Maven para descargas automáticas](./media/apache-spark-create-standalone-application/configure-maven.png)
   
   1. En el menú **File** (Archivo), seleccione **Settings** (Configuración).
   2. En el cuadro de diálogo **Settings** (Configuración), vaya a **Build, Execution, Deployment (Compilación, ejecución, implementación)** > **Build Tools (Herramientas de compilación)** > **Maven** > **Importing** (Importar).
   3. Seleccione la opción **Import Maven projects automatically**(Importar proyectos de Maven automáticamente).
   4. Seleccione **Aplicar** y luego **Aceptar**.
11. En el panel izquierdo, seleccione **src > main > scala > com.microsoft.spark.example** y haga doble clic en **App** para abrir App.scala.

12. Reemplace el código de ejemplo existente por el código siguiente y guarde los cambios. Este código lee los datos de HVAC.csv (disponible en todos los clústeres de HDInsight Spark), recupera las filas que solo tienen un dígito en la sexta columna y escribe el resultado en **/HVACOut** bajo el contenedor de almacenamiento predeterminado para el clúster.

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
13. En el panel izquierdo, haga doble clic en **pom.xml**.
   
   1. En `<project>\<properties>`, agregue los siguientes segmentos:
      
          <scala.version>2.10.4</scala.version>
          <scala.compat.version>2.10.4</scala.compat.version>
          <scala.binary.version>2.10</scala.binary.version>
   2. En `<project>\<dependencies>`, agregue los siguientes segmentos:
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>1.4.1</version>
           </dependency>
      
      Guarde los cambios en pom.xml.
10. Cree el archivo .jar. IntelliJ IDEA permite crear JAR como un artefacto de un proyecto. Lleve a cabo los siguiente pasos.
    
    1. En el menú **Archivo**, seleccione **Estructura del proyecto**.
    2. En el cuadro de diálogo **Project Structure** (Estructura de proyecto), seleccione **Artifacts** (Artefactos) y, después, el signo más. En el cuadro de diálogo emergente, seleccione **JAR** y, después, **From modules with dependencies** (Desde módulos con dependencias).
       
        ![Crear archivo JAR](./media/apache-spark-create-standalone-application/create-jar-1.png)
    3. En el cuadro de diálogo **Create JAR from Modules** (Crear archivo JAR desde módulos), seleccione el botón de puntos suspensivos (![puntos suspensivos](./media/apache-spark-create-standalone-application/ellipsis.png)) en la **clase principal**.
    4. En el cuadro de diálogo **Select Main Class** (Seleccionar clase principal), seleccione la clase que aparece de forma predeterminada y seleccione **OK** (Aceptar).
       
        ![Crear archivo JAR](./media/apache-spark-create-standalone-application/create-jar-2.png)
    5. En el cuadro de diálogo **Create JAR from Modules** (Crear archivo JAR desde módulos), asegúrese de que está seleccionada la opción **Extract to the target JAR** (Extraer al archivo JAR de destino) y, después, seleccione **OK** (Aceptar).  Este valor crea un archivo JAR único con todas las dependencias.
       
        ![Crear archivo JAR](./media/apache-spark-create-standalone-application/create-jar-3.png)
    6. La pestaña Output Layout (Diseño de salida) enumera todos los archivos JAR que forman parte del proyecto Maven. Puede seleccionar y eliminar aquellos de los que la aplicación de Scala no tenga ninguna dependencia directa. Para la aplicación que va a crear aquí puede quitar todos, salvo el último (**SparkSimpleApp compile output**). Seleccione los archivos JAR que va a eliminar y, después, seleccione el icono **Delete** (Eliminar).
       
        ![Crear archivo JAR](./media/apache-spark-create-standalone-application/delete-output-jars.png)
       
        Asegúrese de que la casilla **Include in project build** (Incluir en compilación del proyecto) está activada, lo que garantiza que el archivo jar se crea cada vez que el proyecto se compila o actualiza. Seleccione **Apply** (Aplicar) y, después, **OK** (Aceptar).
    7. En el menú **Build** (Compilar), seleccione **Build Artifacts** (Compilar artefactos) para crear el archivo jar. El archivo JAR de salida se crea en **\out\artifacts**.
       
        ![Crear archivo JAR](./media/apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-spark-cluster"></a>Ejecución de la aplicación en el clúster Spark
Para ejecutar la aplicación en el clúster, puede usar los siguientes enfoques:

* **Copie el archivo jar de la aplicación en el blob de almacenamiento de Azure** asociado con el clúster. Puede usar [**AzCopy**](../../storage/common/storage-use-azcopy.md), una utilidad de línea de comandos, para hacerlo. Hay muchos otros clientes que se pueden utilizar también para cargar datos. Puede encontrar más información al respecto en [Carga de datos para trabajos de Hadoop en HDInsight](../hdinsight-upload-data.md).
* **Use Livy para enviar un trabajo de la aplicación de manera remota** al clúster Spark. Los clústeres Spark en HDInsight incluye Livy, que expone los puntos de conexión REST para enviar trabajos de Spark de forma remota. Para obtener más información, vea [Envío remoto de trabajos de Spark mediante la utilización de Livy con clústeres Spark en HDInsight](apache-spark-livy-rest-interface.md).

## <a name="next-step"></a>Paso siguiente

En este artículo ha aprendido a crear una aplicación en Scala para Spark. Vaya al siguiente artículo para aprender a ejecutar esta aplicación en un clúster de HDInsight Spark con Livy.

> [!div class="nextstepaction"]
>[Ejecutar trabajos de forma remota en un clúster de Spark mediante Livy](./apache-spark-livy-rest-interface.md)

