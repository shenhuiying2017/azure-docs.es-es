---
title: Ciencia de datos con Deep Learning Data Science Virtual Machine en Azure | Microsoft Docs
description: Procedimiento para llevar a cabo varias tareas comunes de ciencia de datos con Deep Learning Data Science VM.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 2053ed8cc420183d493097eeb2cd2ad93c82c70c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32167252"
---
# <a name="using-the-deep-learning-virtual-machine"></a>Usar Deep Learning Virtual Machine

Una vez aprovisionada su instancia de Deep Learning Virtual Machine (DLVM), puede empezar a crear modelos de red neuronal profundos para compilar aplicaciones de AI en ámbitos como Computer Vision o la comprensión de lenguajes. 

Hay una serie de herramientas proporcionadas en DLVM para AI. La página [Marcos de aprendizaje profundo y de AI](dsvm-deep-learning-ai-frameworks.md) contiene información detallada sobre cómo usar estas herramientas. 

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Tutoriales del aprendizaje profundo

Además de los ejemplos basados en marcos, también se proporciona un conjunto de tutoriales completos que se han validado en DLVM, con los que puede iniciar el desarrollo de aplicaciones de aprendizaje profundo en ámbitos como la comprensión de lenguajes o de imágenes y texto. Seguimos agregando más tutoriales completos en distintos ámbitos y tecnologías.   


- [Ejecución de redes neuronales en distintos marcos](https://github.com/ilkarman/DeepLearningFrameworks): se trata de un tutorial completo en el que se muestra cómo migrar código de un marco a otro. También se muestra cómo comparar el rendimiento de los modelos y del tiempo de ejecución en varios marcos. 

- [Guía de procedimientos para crear una solución integral para detectar productos dentro de imágenes](https://github.com/Azure/cortana-intelligence-product-detection-from-images): La detección de imágenes es una técnica con la que se puede buscar y clasificar objetos dentro de imágenes. Esta tecnología tiene el potencial de aportar grandes ventajas a muchos ámbitos comerciales de uso real. Por ejemplo, los minoristas pueden aplicar esta técnica para determinar qué producto ha seleccionado un cliente de una estantería. A su vez, esta información permite que las tiendas administren el inventario de productos. 

- [Extracción de entidades con nombre de resúmenes de PubMed](https://docs.microsoft.com/azure/machine-learning/preview/scenario-tdsp-biomedical-recognition) En este tutorial se muestra como extraer entidades con nombre, tales como nombres de fármacos o de enfermedades, de texto sin estructurar. Se entrena un modelo de incrustación de palabras personalizadas en un corpus de texto de 18 millones de resúmenes de PubMed, se usa ese modelo para compilar un modelo de red neuronal recurrente de memoria a corto y largo plazo (LSTM) para la extracción de entidades y se muestra que el rendimiento del modelo de incrustación de palabras específicas de un dominio puede superar al de incrustación de palabras genéricas en la extracción de entidades.

- [Aprendizaje profundo de audio](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/) En este tutorial se muestra cómo entrenar un modelo de aprendizaje profundo para la detección de eventos de audio en el [conjunto de datos de sonidos urbanos](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html) y se ofrece información general sobre cómo trabajar con datos de audio.

- [Clasificación de documentos de texto](https://github.com/anargyri/lstm_han): en este tutorial se muestra cómo crear y entrenar dos arquitecturas de red neuronal diferentes: red de atención jerárquica y red de memoria a corto y largo plazo (LSTM). Estas redes neurales usan la API de Keras de aprendizaje profundo para clasificar documentos de texto. Keras es un front-end de tres de los marcos de aprendizaje profundo más conocidos: Microsoft Cognitive Toolkit, TensorFlow y Theano.

## <a name="next-steps"></a>Pasos siguientes

En la [página de ejemplos](dsvm-samples-and-walkthroughs.md) se proporcionan punteros a los ejemplos de código cargados previamente en la máquina virtual para cada uno de los marcos, a fin de que pueda empezar a trabajar rápidamente. 
