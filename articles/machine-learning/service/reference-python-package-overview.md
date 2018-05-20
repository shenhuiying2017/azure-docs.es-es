---
title: Paquetes Python para Azure Machine Learning
description: Obtenga información sobre los paquetes Python disponibles para los usuarios de Azure Machine Learning.
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jmartens
author: j-martens
ms.date: 05/07/2018
ms.openlocfilehash: 7ff80a812ebf704315524ffb5ea518704e472429
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="python-packages-for-azure-machine-learning"></a>Paquetes Python para Azure Machine Learning

Más información sobre los paquetes Python propiedad de Microsoft para Azure Machine Learning. Puede usar estas bibliotecas y funciones en combinación con otros paquetes de código abierto o de terceros, pero para usar los paquetes de propiedad, el código Python se debe ejecutar en un servicio o en un equipo que proporciona los intérpretes.

Los paquetes de Azure Machine Learning son **extensiones que se pueden instalar con el programa pip de Python para Azure Machine Learning** que permiten que los científicos de datos y los desarrolladores de inteligencia artificial compilen e implementen de manera rápida modelos de aprendizaje profundo y aprendizaje automático de gran precisión para diversos dominios.

<a name="amlpcv"></a>
## <a name="azure-ml-package-for-computer-vision"></a>Azure ML Package for Computer Vision

Con Azure ML Package for Computer Vision, puede compilar, ajustar e implementar modelos de aprendizaje profundo para la clasificación de imágenes, la detección de objetos y la similitud de imágenes.

Pruebe estos pasos para este paquete:
1. [Descargue](https://aka.ms/aml-packages/vision/download) el paquete.
1. Lea los [documentos de instalación](https://aka.ms/aml-packages/vision).
1. [Compile e implemente un modelo de Computer Vision](how-to-build-deploy-image-classification-models.md) con una instancia de Jupyter Notebook.
1. Explore la [documentación de referencia](https://aka.ms/aml-packages/vision) del paquete.

<a name="amlpf"></a>
## <a name="azure-ml-package-for-forecasting"></a>Azure ML Package for Forecasting

Con Azure ML Package for Forecasting, puede crear e implementar modelos de previsión de serie temporal para escenarios de previsión de demanda y finanzas.

Pruebe estos pasos para este paquete:
1. [Descargue](https://aka.ms/aml-packages/forecasting/download) el paquete.
1. Lea los [documentos de instalación](https://aka.ms/aml-packages/forecasting).
1. [Compile e implemente un modelo de previsión](how-to-build-deploy-forecast-models.md) con una instancia de Jupyter Notebook.
1. Explore la [documentación de referencia](https://aka.ms/aml-packages/forecasting) del paquete.

<a name="amlpta"></a>
## <a name="azure-ml-package-for-text-analytics"></a>Azure ML Package for Text Analytics

Con Azure ML Package for Text Analytics, puede compilar modelos de aprendizaje profundo de texto para la clasificación de texto, la extracción personalizada de entidades y la incrustación de palabras.

Pruebe estos pasos para este paquete:
1. [Descargue](https://aka.ms/aml-packages/text/download) el paquete.
1. Lea los [documentos de instalación](https://aka.ms/aml-packages/text).
1. [Compile e implemente un modelo de clasificación de texto](how-to-build-deploy-text-classification-models.md) con una instancia de Jupyter Notebook.
1. Explore la [documentación de referencia](https://aka.ms/aml-packages/text) del paquete.

<a name="amlrealtimeai"></a>
## <a name="amlrealtimeai-fpga-acceleration"></a>amlrealtimeai (aceleración de FPGA)

Con el paquete Azure Machine Learning Hardware Acceleration, los científicos de datos y los desarrolladores de inteligencia artificial pueden caracterizar las imágenes con una versión cuantizada de ResNet 50, entrenar los clasificadores según esas características y luego implementar los modelos en [matrices de puertas programables por campos (FPGA)](concept-accelerate-with-fpgas.md) en Azure para obtener una inferencia de latencia muy baja.

Pruebe estos pasos para este paquete:
1. [Descargue](https://aka.ms/aml-real-time-ai-package) el paquete.
1. Lea los [documentos de instalación](reference-fpga-package-overview.md).
1. [Implementación de un modelo en una FPGA](how-to-deploy-fpga-web-service.md).

