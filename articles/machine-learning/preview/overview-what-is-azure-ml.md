---
title: ¿Qué es Azure Machine Learning? | Microsoft Docs
description: 'Explica los conceptos básicos del aprendizaje automático en la nube, describe para qué puede utilizarlo y define los términos de aprendizaje automático. Introducción a Azure Machine Learning: una solución de integrada y completa de ciencia de datos que permite a los científicos de datos profesionales desarrollar, experimentar e implementar aplicaciones de análisis avanzado a escala de nube.'
services: machine-learning
author: mwinkle
ms.author: mwinkle
manager: cgronlun
ms.service: machine-learning
ms.workload: data-services
ms.topic: get-started-article
ms.date: 09/21/2017
ms.openlocfilehash: 8a92f42ecee926042e9e0662f6b0bd9438024248
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/29/2018
---
# <a name="what-is-machine-learning"></a>¿Qué es Machine Learning?

El aprendizaje automático es una técnica de ciencia de datos que permite a los equipos utilizar datos existentes para prever tendencias, resultados y comportamientos futuros. Mediante el aprendizaje automático, los equipos aprenden si necesidad de programarlos explícitamente.

Las previsiones o predicciones del aprendizaje automático pueden hacer que las aplicaciones y los dispositivos sean más inteligentes. Cuando compra en línea, el aprendizaje automático ayuda a recomendar otros productos según lo que haya adquirido. Al pasar su tarjeta de crédito, el aprendizaje automático compara la transacción con una base de datos de transacciones y ayuda a detectar fraudes. Cuando la aspiradora robot aspira una sala, el aprendizaje automático le ayuda a decidir si se ha terminado el trabajo.

## <a name="what-is-azure-machine-learning"></a>¿Qué es Azure Machine Learning?
Azure Machine Learning es una solución de análisis avanzado y ciencia de datos integrada y completa. Permite a los científicos de datos preparar datos, desarrollar experimentos e implementar modelos a escala de nube.

Los componentes principales de Azure Machine Learning son:
- Azure Machine Learning Workbench
- Servicio Experimentación de Azure Machine Learning
- Servicio Administración de modelos de Azure Machine Learning
- Biblioteca de Microsoft Machine Learning para Apache Spark (Biblioteca MMLSpark)
- Herramientas de Visual Studio Code para AI

Juntas, estas aplicaciones y servicios ayudan a acelerar considerablemente el desarrollo y la implementación de proyectos de ciencia de datos. 

![Conceptos de Azure Machine Learning](media/overview-what-is-azure-ml/aml-concepts.png)


## <a name="open-source-compatible"></a>Compatible con el código abierto

Azure Machine Learning es totalmente compatible con las tecnologías de código abierto. Puede usar decenas de miles de paquetes de Python de código abierto, como los siguientes marcos de aprendizaje automático:

- [scikit-learn](http://scikit-learn.org/)
- [TensorFlow](https://www.tensorflow.org/)
- [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)
- [Spark ML](https://spark.apache.org/docs/2.1.1/ml-pipeline.html)

Puede ejecutar sus experimentos en entornos administrados, como contenedores de Docker y clústeres de Spark. También puede usar hardware avanzado, como [máquinas virtuales habilitadas para GPU en Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu) para acelerar la ejecución.

Azure Machine Learning se basa en las siguientes tecnologías de código abierto:

- [Jupyter Notebook](http://jupyter.org/)
- [Apache Spark](https://spark.apache.org/)
- [Docker](https://www.docker.com/)
- [Kubernetes](https://kubernetes.io/)
- [Python](https://www.python.org/)
- [Conda](https://conda.io/docs/)

También incluye las tecnologías de código abierto de Microsoft, como la [biblioteca de Microsoft Machine Learning para Apache Spark](https://github.com/Azure/mmlspark) y Cognitive Toolkit.

Además, también podrá beneficiarse de algunas de las tecnologías de aprendizaje automático más avanzadas y probadas que ha desarrollado Microsoft para resolver problemas a gran escala. Se han sometido a pruebas extremas en muchos productos de Microsoft, como:

- Windows
- Bing
- Xbox
- Office
- SQL Server

A continuación se especifican algunas de las tecnologías de aprendizaje automático de Microsoft incluidas con Azure Machine Learning:

- [PROSE](https://microsoft.github.io/prose/) (PROgram Synthesis using Examples)
- [microsoftml](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)
- [revoscalepy](https://docs.microsoft.com/r-server/python-reference/revoscalepy/revoscalepy-package)

## <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench
Azure Machine Learning Workbench es una aplicación de escritorio más herramientas de línea de comandos que se admite en Windows y macOS. Permite administrar soluciones de aprendizaje automático a lo largo de todo el ciclo de vida de ciencia de datos:

- Ingesta y preparación de datos
- Desarrollo de modelos y administración de experimentos
- Implementación de modelos en distintos entornos de destino

Estas son las funcionalidades principales que ofrece Azure Machine Learning Workbench:

- Herramienta de preparación de datos que puede aprender la lógica de transformación de datos a través de ejemplos.
- Abstracción del origen de datos accesible a través de código de Python y experiencia de usuario.
- SDK de Python para invocar paquetes de preparación de datos construidos visualmente.
- Servicio Jupyter Notebook integrado y experiencia de usuario de cliente.
- Supervisión y administración de experimentos a través de vistas de historial de ejecución.
- Control de acceso basado en roles que permite el uso compartido y la colaboración mediante Azure Active Directory.
- Instantáneas de proyecto automáticas para cada ejecución y control de versiones explícito habilitado por integración de GIT nativa. 
- Integración con el popular IDE de Python.

Para obtener más información, consulte los siguientes artículos:
- [Guía de usuario sobre preparación de los datos](data-prep-user-guide.md)
- [Uso de GIT con Azure Machine Learning](using-git-ml-project.md)
- [Uso de Jupyter Notebook en Azure Machine Learning](how-to-use-jupyter-notebooks.md)
- Movilidad y uso compartido
- [Guía del historial de ejecución](how-to-use-run-history-model-metrics.md)
- [Integración del IDE](how-to-configure-your-IDE.md)

## <a name="azure-machine-learning-experimentation-service"></a>Servicio Experimentación de Azure Machine Learning
El servicio Experimentación controla la ejecución de experimentos de aprendizaje automático. También es compatible con Workbench, ya que proporciona administración de proyectos, integración de GIT, control de acceso, movilidad y uso compartido. 

A través de una configuración sencilla, puede ejecutar sus experimentos en una amplia gama de opciones de entorno de proceso:

- Local nativo
- Contenedor de Docker local
- Contenedor de Docker en VM remota
- Escalar horizontalmente el clúster de Spark en Azure

El servicio de experimentación construye entornos virtuales para asegurarse de que el script se pueda ejecutar de forma aislada con resultados reproducibles. Registra la información de historial de ejecución y presenta el historial visualmente. Puede seleccionar fácilmente el mejor modelo de las ejecuciones de experimento. 

Para más información, consulte [Configuración de la experimentación](experimentation-service-configuration.md).

## <a name="azure-machine-learning-model-management-service"></a>Servicio Administración de modelos de Azure Machine Learning

El servicio Administración de modelos permite a los científicos de datos y equipos de operaciones de desarrollo implementar modelos predictivos en una amplia variedad de entornos. Se hace el seguimiento del linaje y las versiones de modelo desde las ejecuciones de entrenamiento hasta las implementaciones. Los modelos se almacenan, registran y administran en la nube. 

Use comandos de CLI simples para crear contenedores en el modelo y puntuar scripts y dependencias en imágenes de Docker. Estas imágenes se registran en su propio registro de Docker hospedado en Azure (Azure Container Registry). Se pueden implementar forma confiable en los destinos siguientes:

- Equipos locales
- Servidores locales
- La nube
- Dispositivos perimetrales de IoT

Kubernetes en ejecución en Azure Container Service (ACS) se usa para implementaciones escaladas en la nube. La telemetría de ejecución del modelo se captura en AppInsights para el análisis visual. 

Para obtener más información sobre el servicio Administración de modelos, consulte [Introducción a Administración de modelos](model-management-overview.md)


## <a name="microsoft-machine-learning-library-for-apache-spark"></a>Biblioteca de Microsoft Machine Learning para Apache Spark

[MMLSpark](https://github.com/Azure/mmlspark)(Biblioteca de Microsoft Machine Learning para Apache Spark) es un paquete de Spark de código abierto que proporciona herramientas de ciencia de datos y aprendizaje profundo para Apache Spark. Integra [canalizaciones de Machine Learning de Spark](https://spark.apache.org/docs/2.1.1/ml-pipeline.html) con [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) y la biblioteca [OpenCV](http://opencv.org/) . Permite crear rápidamente modelos de predicción y análisis eficaces y altamente escalables para grandes conjuntos de datos de imágenes y texto. Estos son algunos de los aspectos destacados:

- Ingesta de imágenes de HDFS a trama de datos de Spark fácilmente
- Preprocesamiento de datos de imagen mediante transformaciones de OpenCV
- Caracterización de imágenes con redes neurales profundas previamente entrenadas con Microsoft Cognitive Toolkit 
- Uso de LSTM bidireccionales previamente entrenadas de Keras para la extracción de entidades médicas
- Entrenar modelos de clasificación de imagen basados en DNN en VM de GPU de N-Series en Azure
- Caracterización de datos de texto sin formato mediante cómodas API sobre primitivos en SparkML a través de un transformador único
- Entrenamiento sencillo de modelos de clasificación y regresión mediante la caracterización implícita de datos
- Proceso de conjunto avanzado de métricas de evaluación, incluidas las métricas por instancia

Para obtener más información, consulte [Uso de MMLSpark en Azure Machine Learning](how-to-use-mmlspark.md).

## <a name="visual-studio-code-tools-for-ai"></a>Herramientas de Visual Studio Code para AI
Herramientas de Visual Studio Code para AI es una extensión de Visual Studio Code para compilar, probar e implementar soluciones de aprendizaje profundo/AI. Ofrece muchos puntos de integración con Azure Machine Learning, como los siguientes:
- Una vista del historial de ejecución que muestra el rendimiento de las series de aprendizaje y las métricas registradas.
- Una vista de galería que permite a los usuarios examinar y arrancar nuevos proyectos con Microsoft Cognitive Toolkit, TensorFlow y muchos otros marcos de trabajo de aprendizaje profundo. 
- Una vista de explorador para seleccionar destinos de proceso para que los ejecuten los scripts.
 

## <a name="what-are-the-machine-learning-options-from-microsoft"></a>¿Cuáles son las opciones de aprendizaje automático de Microsoft?
Además de Azure Machine Learning, hay una amplia variedad de opciones en Azure para generar, implementar y administrar modelos de aprendizaje automático. [Puede encontrar más información sobre ellas aquí.](overview-more-machine-learning.md)

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>Pasos siguientes
* [Instalar y crear instancias de Azure Machine Learning](quickstart-installation.md).
