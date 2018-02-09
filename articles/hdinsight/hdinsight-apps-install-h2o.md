---
title: "Instalación de aplicación publicada: H2O Sparkling Water - Azure HDInsight | Microsoft Docs"
description: "Instale y use la aplicación de Hadoop de terceros H2O Sparkling Water."
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 8734daa5303aa76e9f8a074b5f709727cabb58b2
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2018
---
# <a name="install-published-application---h2o-sparkling-water"></a>Instalación de aplicación publicada: H2O Sparkling Water

En este artículo se describe cómo instalar y ejecutar la aplicación de Hadoop publicada [H2O Sparkling Water](http://www.h2o.ai/) en Azure HDInsight. Para información general de la plataforma de aplicaciones HDInsight y una lista de las aplicaciones publicadas de fabricantes de software independiente (ISV) disponibles, consulte [Instalación de aplicaciones de Hadoop de terceros](hdinsight-apps-install-applications.md). Para obtener instrucciones sobre cómo instalar su propia aplicación, consulte [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md).

## <a name="about-h2o-sparkling-water"></a>Información sobre H2O Sparkling Water

H2O Sparkling Water es una plataforma de aprendizaje automático en memoria de código abierto completamente distribuida con escalabilidad lineal. H2O Sparkling Water permite combinar los algoritmos de aprendizaje automático escalables y rápidos de H2O con las funcionalidades de Spark. Con Sparkling Water, los usuarios pueden impulsar el cálculo de Scala, R y Python con la interfaz de usuario de H2O Flow.

H2O Sparkling Water ofrece:

* **Interfaces familiares e interfaz de usuario web fácil de usar**: realice la configuración y comience a trabajar rápidamente mediante la GUI intuitiva de Flow basada en web de H2O o mediante programación de entornos como R, Python, Java, Scala, JSON y las API de H2O.
* **Compatibilidad independiente de datos para todos los tipos de archivos y bases de datos comunes**: explore y modele fácilmente los macrodatos desde dentro de Microsoft Excel, R Studio, Tableau, etc. Conéctese a los datos desde los orígenes de datos de HDFS, S3, SQL y NoSQL.
* **Manipulación y análisis de macrodatos escalables a gran escala**: H2O Big Joins se puede ejecutar siete veces más rápido que las operaciones data.table de R y puede escalar de manera lineal a 10 mil millones por 10 mil millones de combinaciones de filas.
* **Puntuación de datos en tiempo real**: implemente rápidamente modelos en el entorno de producción con objetos Java estándar (POJO), objetos Java optimizados para modelo (MOJO) o la API de REST de H2O.

### <a name="resource-links"></a>Vínculos de recursos

* [H2O.ai Engineering Roadmap](https://jira.h2o.ai/) (Mapa de ruta de ingeniería)
* [H2O.ai Home](http://www.h2o.ai/) (Página principal)
* [H2O.ai Documentation](http://docs.h2o.ai/) (Documentación)
* [H2O.ai Support](https://support.h2o.ai/) (Compatibilidad)
* [H2O.ai Open Source Codebase](https://github.com/h2oai/) (Base de código abierto)

## <a name="prerequisites"></a>requisitos previos

Para instalar esta aplicación en un clúster de HDInsight nuevo o en un clúster existente, debe tener la configuración siguiente:

* Niveles de clúster: estándar o Premium
* Tipo de clúster: Spark
* Versiones del clúster: 3.5 o 3.6

## <a name="install-the-h2o-sparkling-water-published-application"></a>Instalación de la aplicación publicada H2O Sparkling Water

Para instrucciones paso a paso sobre cómo instalar esta aplicación y otras aplicaciones de ISV disponibles, lea [Instalación de aplicaciones de Hadoop de terceros](hdinsight-apps-install-applications.md).

## <a name="launch-h2o-sparkling-water"></a>Inicio de H2O Sparkling Water

1. Después de la instalación, puede comenzar a usar H2O Sparkling Water (h2o-sparklingwater) desde su clúster en Azure Portal para abrir Jupyter Notebooks (`https://<ClusterName>.azurehdinsight.net/jupyter`). Para llegar a Jupyter, también puede seleccionar **Cluster dashboard** (panel de clúster) en el panel del clúster del portal y seleccionar **Jupyter Notebook**. Se le pedirá escribir sus credenciales. Escriba las credenciales de Hadoop del clúster tal como se especificó en la creación del clúster.

2. En Jupyter verá tres carpetas: H2O-PySparkling-Examples, PySpark Examples y Scala Examples. Seleccione la carpeta **H2O-PySparkling-Examples**.

    ![Página principal de Jupyter Notebooks](./media/hdinsight-apps-install-h2o/jupyter-home.png)

3. El primer paso al crear un cuaderno nuevo es configurar el entorno Spark. Esta información se incluye en el ejemplo **Sentiment_analysis_with_Sparkling_Water**. Al configurar el entorno Spark, asegúrese de usar el archivo jar correcto y especifique la dirección IP que se proporciona en la salida de la primera celda.

    ![Página principal de Jupyter Notebooks](./media/hdinsight-apps-install-h2o/spark-config.png)

4. Inicie el clúster de H2O.

    ![Inicio del clúster](./media/hdinsight-apps-install-h2o/start-cluster.png)

5. Una vez que el clúster de H2O se esté ejecutando, abra H2O Flow en **`https://<ClusterName>-h2o.apps.azurehdinsight.net:443`**.

    > [!NOTE]
    > Si no puede abrir H2O Flow, intente borrar la caché del explorador. Si todavía no puede abrirlo, es posible que no tenga los recursos suficientes en el clúster. Intente aumentar el número de nodos de trabajo en la opción **Escalar clúster** en el panel del clúster.

    ![Panel de H2O Flow](./media/hdinsight-apps-install-h2o/h2o-flow.png)

6. Seleccione el ejemplo **Million_Songs.flow** en el menú de la derecha. Cuando aparezca una advertencia, haga clic en **Load Notebook** (Cargar cuaderno). Esta demostración está diseñada para ejecutarse en unos minutos con datos reales. El objetivo es, a partir de los datos, predecir si la canción se lanzó antes o durante el año 2004 mediante la clasificación binaria.

    ![Seleccionar Million_Songs.flow](./media/hdinsight-apps-install-h2o/million-songs.png)

7. Busque la ruta de acceso que contiene **milsongs-cls-train.csv.gz** y reemplácela por **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-train.csv.gz**.

8. Busque la ruta de acceso que contiene **milsongs-cls-test.csv.gz** y reemplácela por **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-test.csv.gz**.

9. Para ejecutar todas las instrucciones dentro de las celdas del cuaderno, seleccione el botón **Ejecutar todas** en la barra de herramientas.

    ![Ejecutar todas](./media/hdinsight-apps-install-h2o/run-all.png)

10. Después de unos minutos, debería ver una salida similar a la siguiente.

    ![Salida](./media/hdinsight-apps-install-h2o/output.png)

Eso es todo. Ha usado la inteligencia artificial en Spark en cuestión de minutos. Ahora puede explorar más ejemplos en H2O Flow que demuestran los distintos tipos de algoritmos de aprendizaje automático.

## <a name="next-steps"></a>pasos siguientes

* [Documentación de H2O](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/index.html)
* [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): aprenda a implementar en HDInsight una aplicación de HDInsight no publicada.
* [Publicación de aplicaciones de HDInsight en Azure Marketplace](hdinsight-apps-publish-applications.md): aprenda a publicar aplicaciones de HDInsight personalizadas en Azure Marketplace.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN: instalación de una aplicación de HDInsight): aprenda a definir aplicaciones de HDInsight.
* [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md): aprenda a usar acciones de script para instalar otras aplicaciones.
* [Uso de nodos perimetrales vacíos en HDInsight](hdinsight-apps-use-edge-node.md): aprenda a usar un nodo perimetral vacío para acceder a los clústeres de HDInsight, probar aplicaciones de este y hospedarlas.
