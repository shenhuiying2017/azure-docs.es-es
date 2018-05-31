---
title: Clasificación de imágenes con Azure Machine Learning (AML) Package for Computer Vision (AMLPCV) y con el Proceso de ciencia de datos en equipo (TDSP) | Microsoft Docs
description: Describe el uso de TDSP (Proceso de ciencia de datos en equipo) y de AMLPCV para la clasificación de imágenes.
services: machine-learning, team-data-science-process
documentationcenter: ''
author: xibingao
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 18/05/2018
ms.author: xibingao
ms.openlocfilehash: cfb49f08a245fc08ba3fe78333e801ea2d358c4a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34367947"
---
# <a name="skin-cancer-image-classification-with-azure-machine-learning-aml-package-for-computer-vision-amlpcv-and-team-data-science-process-tdsp"></a>Clasificación de imágenes de cáncer de piel con Azure Machine Learning (AML) Package for Computer Vision (AMLPCV) y con el Proceso de ciencia de datos en equipo (TDSP)

## <a name="introduction"></a>Introducción

En este artículo, se muestra cómo usar [Azure Machine Learning Package for Computer Vision (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) para entrenar, probar e implementar un modelo de **clasificación de imágenes**. En el ejemplo se usan las plantillas y la estructura de TDSP en [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation). El ejemplo completo se proporciona en este tutorial. Usa [CNTK](https://www.microsoft.com/en-us/cognitive-toolkit/) como marco de aprendizaje profundo y el entrenamiento se realiza en una máquina GPU de [VM de ciencia de datos](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview). La implementación usa la CLI de operacionalización de Azure ML.

Muchas aplicaciones del dominio Computer Vision se pueden enmarcar como problemas de clasificación de imágenes. Entre ellas se incluyen modelos de creación que responden a preguntas como "¿Hay un objeto presente en la imagen?" (el objeto podría ser un *perro*, un *coche* o un *barco*) y a otras más complejas, como "¿Qué gravedad de enfermedad ocular revela el escáner de retina de este paciente?". AMLPCV simplifica la canalización de modelado y el procesamiento de datos de clasificación de imágenes. 

## <a name="link-to-github-repository"></a>Vínculo al repositorio de GitHub
A continuación, se proporciona documentación de resumen sobre el ejemplo. Puede encontrar una documentación más extensa en el [sitio de GitHub](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification).

## <a name="team-data-science-process-tdsp-walkthrough-with-amlpcv"></a>Tutorial del Proceso de ciencia de datos en equipo (TDSP) con AMLPCV

Este tutorial usa el ciclo de vida del [Proceso de ciencia de datos en equipo (TDSP)](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview).

En el tutorial se describen los siguientes pasos del ciclo de vida:

### <a name="1-data-acquisionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1. Adquisición de datos](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
El conjunto de datos de ISIC se utiliza para la tarea de clasificación de imágenes. ISIC (el proyecto de Colaboración internacional de imagen de la piel) es una asociación entre las instituciones académicas y el sector industrial que para facilita la aplicación de creación de imágenes digitales de la piel para estudiar la mortalidad por melanoma, así como para ayudar a reducirla. El [archivo de ISIC](https://isic-archive.com/#images) contiene más de 13 000 imágenes de lesiones cutáneas con etiquetas que indican si son benignas o malignas. Hemos descargado un ejemplo de las imágenes del archivo de ISIC.

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2. Modelado](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling)
En el paso de modelado, se llevan a cabo los pasos siguientes. 

<b>2.1 Creación del conjunto de datos</b><br>

Para generar un objeto de conjunto de datos en AMLPCV, proporcione un directorio raíz de imágenes en el disco local. 

<b>2.2 Visualización y anotación de imágenes</b><br>

Visualice las imágenes del objeto de conjunto de datos y corrija algunas de las etiquetas en caso de que sea necesario.

<b>2.3 Aumento de imágenes</b><br>

Aumente un objeto de conjunto de datos con las transformaciones que se describen en la biblioteca [imgaug](https://github.com/aleju/imgaug).

<b>2.4 Definición del modelo de DNN</b><br>

Defina la arquitectura del modelo que se utiliza en el paso de entrenamiento. En AMLPCV, se admiten seis modelos diferentes de redes neuronales profundas entrenadas previamente: AlexNet, Resnet-18, Resnet-34, and Resnet-50, Resnet-101 y Resnet-152.

<b>2.5 Entrenamiento de clasificadores</b><br>

Entrene las redes neurales con parámetros predeterminados o personalizados.

<b>2.6 Evaluación y visualización</b><br>

El subpaso proporciona funcionalidad para evaluar el rendimiento del modelo entrenado en un conjunto de datos de prueba independiente. Las métricas de evaluación incluyen precisión y recuperación, así como la curva ROC.

Dichos subpasos se explican con detalle en la aplicación Jupyter Notebook correspondiente. También se proporcionan instrucciones para activar los parámetros, como la velocidad de aprendizaje, el tamaño de lote mini y la tasa de retirada, para mejorar aún más el rendimiento del modelo.

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3. Implementación](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment)

En este paso, se pone en marcha el modelo generado en el paso de modelado. Introduce los requisitos previos de puesta en marcha y el programa de instalación. Por último, también se explica el consumo del servicio web. A través de este tutorial, aprenderá a generar modelos de aprendizaje profundo con AMLPCV, así como a poner en funcionamiento el modelo en Azure.

## <a name="next-steps"></a>Pasos siguientes
Para empezar, lea la documentación adicional sobre [Azure Machine Learning Package for Computer Vision (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) y el [Proceso de ciencia de datos en equipo (TDSP)](https://aka.ms/tdsp).


## <a name="references"></a>Referencias

* [Azure Machine Learning Package for Computer Vision (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation)
* [VM de ciencia de datos](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)

