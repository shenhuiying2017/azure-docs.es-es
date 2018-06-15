---
title: 'Uso de la máquina virtual Geo Artificial Intelligence Data Science Virtual Machine: Azure | Microsoft Docs'
description: Uso de una máquina virtual para inteligencia artificial y análisis geoespacial en Azure.
keywords: aprendizaje profundo, IA, herramientas de ciencia de datos, máquina virtual de ciencia de datos, análisis geoespacial
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: gokuma
ms.openlocfilehash: f346b086a0269f247d64edf9346b01849ba3d0ee
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
ms.locfileid: "31408044"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Uso de la máquina virtual Geo Artificial Intelligence Data Science Virtual Machine

Utilice la máquina virtual de ciencia de datos para inteligencia artificial y análisis geoespacial para capturar datos para análisis, realizar doma de datos (wrangling) y generar modelos para aplicaciones de inteligencia artificial que consumen información geoespacial. Una vez que haya aprovisionado la máquina virtual de ciencia de datos para inteligencia artificial y análisis geoespacial, y haya iniciado sesión en ArcGIS Pro con la cuenta de ArcGIS, puede empezar a interactuar con el escritorio de ArcGIS y ArcGis en línea. También puede acceder a ArcGIS desde interfaces de Python y un puente de lenguaje R preconfigurado en la máquina virtual de ciencia de datos para análisis geoespacial. Para crear unas aplicaciones de inteligencia artificial completas, combínelas con las plataformas de aprendizaje automático y aprendizaje profundo, y con cualquier otro software de ciencia de datos disponible en la máquina virtual de ciencia de datos.  


## <a name="configuration-details"></a>Detalles de configuración

La biblioteca de Python [arcpy](http://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), que se utiliza para comunicarse con ArcGIS, está instalada en el entorno de Conda raíz global de la máquina virtual de ciencia de datos que se encuentra en ```c:\anaconda```. 

- Si está ejecutando Python en un símbolo del sistema, ejecute ```activate``` para activar en el entorno de Python de Conda raíz. 
- Si está utilizando IDE o Jupyter Notebook, puede seleccionar el entorno o el kernel para asegurarse de que se encuentra en el entorno de Conda correcto. 

El puente de R para ArcGIS se instala como una biblioteca de R denominada [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) en la instancia independiente principal de Microsoft R Server que se encuentra en ```C:\Program Files\Microsoft\ML Server\R_SERVER```. Visual Studio, RStudio y Jupyter ya están preconfiguradas para usar este entorno de R y tendrán acceso a la biblioteca de R ```arcgisbinding```. 


## <a name="geo-ai-data-science-vm-samples"></a>Ejemplos de máquina virtual de ciencia de datos para inteligencia artificial y análisis geoespacial

Además de ejemplos basados en la plataforma de aprendizaje automático y aprendizaje profundo de la máquina virtual de ciencia de datos base, también se proporciona un conjunto de ejemplos de análisis geoespacial como parte de la máquina virtual de ciencia de datos para inteligencia artificial y análisis geoespacial. Estos ejemplos pueden ayudarle a impulsar el desarrollo de aplicaciones de inteligencia artificial mediante datos geoespaciales y el software de ArcGIS. 


1. [Introducción al análisis geoespacial con Python](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): un ejemplo introductorio muestra cómo trabajar con datos geoespaciales mediante la interfaz de Python para ArcGIS proporcionada por la biblioteca [arcpy](http://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm). También muestra cómo puede combinar el aprendizaje automático tradicional con datos geoespaciales y visualizar el resultado en un mapa en ArcGIS. 

2. [Introducción al análisis geoespacial con R](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): un ejemplo introductorio muestra cómo trabajar con datos geoespaciales mediante la interfaz de R para ArcGIS proporcionada por la biblioteca [arcgisbinding](https://github.com/R-ArcGIS/r-bridge). 

3. [Clasificación de uso del suelo a nivel de píxeles](https://github.com/Azure/pixel_level_land_classification): un tutorial que muestra cómo crear un modelo de red neuronal profunda que acepta una imagen aérea como entrada y devuelve una etiqueta de cobertura del suelo. Ejemplos de etiquetas de cobertura del suelo son "bosque" o "agua". El modelo devuelve este tipo de etiqueta para cada píxel de la imagen. El modelo se crea mediante la plataforma de aprendizaje profundo de código abierto [Cognitive Toolkit (CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) de Microsoft. El ejemplo también muestra cómo escalar horizontalmente el aprendizaje a [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) y consumir las predicciones del modelo en el software ArcGIS Pro. 


## <a name="next-steps"></a>Pasos siguientes

Hay ejemplos adicionales que usan la máquina virtual de ciencia de datos disponibles aquí:

* [Muestras](dsvm-samples-and-walkthroughs.md)

