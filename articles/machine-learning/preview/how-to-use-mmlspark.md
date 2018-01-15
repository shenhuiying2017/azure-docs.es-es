---
title: Uso de MMLSpark con Machine Learning | Microsoft Docs
description: "Guía de cómo usar la biblioteca de MMLSpark con Azure Machine Learning."
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 01/12/2018
ms.openlocfilehash: f978805f800a35908629a6febb59d7db50d14023
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/13/2018
---
# <a name="how-to-use-microsoft-machine-learning-library-for-apache-spark"></a>Uso de la biblioteca de Microsoft Machine Learning para Apache Spark

## <a name="introduction"></a>Introducción

[La biblioteca de Microsoft Machine Learning para Apache Spark](https://github.com/Azure/mmlspark) (MMLSpark) proporciona herramientas que le permiten crear fácilmente modelos de aprendizaje automático escalables para grandes conjuntos de datos. Incluye la integración de canalizaciones de SparkML con el [kit de herramientas de Microsoft Cognitive ](https://github.com/Microsoft/CNTK) y [OpenCV](http://www.opencv.org/), lo que le permite: 
 * La entrada y el procesamiento previo de datos de imagen
 * La caracterización de imágenes y texto mediante modelos de Deep Learning entrenados previamente
 * El entrenamiento y la puntuación de los modelos de clasificación y regresión mediante características implícitas.

## <a name="prerequisites"></a>Requisitos previos

Para seguir esta guía, necesita:
- [Instalar Azure Machine Learning Workbench](quickstart-installation.md)
- [Configurar el clúster de Spark para Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql)

## <a name="run-your-experiment-in-docker-container"></a>Ejecución del experimento en el contenedor de Docker

Azure Machine Learning Workbench está configurado para usar MMLSpark cuando ejecuta experimentos en un contenedor de Docker, bien de manera local o en una máquina virtual remota. Esta funcionalidad le permite depurar y experimentar fácilmente con los modelos de PySpark, antes de ejecutarlos en una escala en un clúster. 

Para empezar a usar un ejemplo, cree un nuevo proyecto y seleccione el ejemplo de la galería "MMLSpark on Adult Census". Seleccione "Docker" como contexto de proceso en el panel de proyecto y haga clic en "Ejecutar". Azure Machine Learning Workbench crea el contenedor de Docker para ejecutar el programa de PySpark y, a continuación, ejecuta el programa.

Una vez finalizada la ejecución, puede ver los resultados en la vista de historial de ejecución del área de trabajo de Azure Machine Learning Workbench.

## <a name="install-mmlspark-on-azure-hdinsight-spark-cluster"></a>Instalación de MMLSpark en un clúster de Azure HDInsight Spark.

Para completar este paso y el siguiente, primero debe [crear un clúster de Azure HDInsight Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql).

De forma predeterminada, Azure Machine Learning Workbench instala el paquete MMLSpark en el clúster cuando se ejecuta el experimento. Puede controlar este comportamiento e instalar otros paquetes de Spark mediante la edición de un archivo denominado _aml_config/spark_dependencies.yml_ en la carpeta del proyecto.

```
# Spark configuration properties.
configuration:
  "spark.app.name": "Azure ML Experiment"
  "spark.yarn.maxAppAttempts": 1

repositories:
  - "https://mmlspark.azureedge.net/maven"
packages:
  - group: "com.microsoft.ml.spark"
    artifact: "mmlspark_2.11"
    version: "0.9.9"
```

También puede instalar MMLSpark directamente en el clúster de HDInsight Spark con [Acción de script](https://github.com/Azure/mmlspark#hdinsight).

## <a name="set-up-azure-hdinsight-spark-cluster-as-compute-target"></a>Configuración de un clúster de Azure HDInsight Spark como destino de proceso

Abra la ventana de la CLI desde Azure Machine Learning Workbench; para ello, vaya al menú "Archivo" y haga clic en "Open Command Prompt" (Abrir símbolo del sistema).

En la ventana de la CLI, ejecute el siguiente comando:

```
az ml computetarget attach cluster --name <myhdi> --address <myhdi-ssh.azurehdinsight.net> --username <sshusername> --password <sshpwd> 
```

```
az ml experiment prepare -c <myhdi>
```

Ahora el clúster está disponible como destino de proceso para el proyecto.

## <a name="run-experiment-on-azure-hdinsight-spark-cluster"></a>Ejecute el experimento en el clúster de Azure HDInsight Spark.

Vuelva al panel de proyecto del ejemplo "MMLSpark on Adult Census". Seleccione el clúster como destino de proceso y haga clic en Ejecutar.

Azure Machine Learning Workbench envía el trabajo de Spark al clúster. Puede supervisar el progreso y ver los resultados en la vista de historial de ejecución.

## <a name="next-steps"></a>pasos siguientes
Para más información sobre la biblioteca de MMLSpark y ejemplos, consulte el [repositorio de GitHub para MMLSpark](https://github.com/Azure/mmlspark).

*Apache®, Apache Spark y Spark® son marcas comerciales registradas o marcas comerciales de Apache Software Foundation en los Estados Unidos u otros países.*
