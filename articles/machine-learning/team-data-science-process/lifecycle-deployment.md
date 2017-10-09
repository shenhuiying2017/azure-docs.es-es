---
title: "Fase de implementación del ciclo de vida del Proceso de ciencia de datos en equipo| Microsoft Docs"
description: "Los objetivos, las tareas y los resultados de la fase de implementación de los proyectos de ciencia de datos."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2017
ms.author: bradsev;
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: b49b3ab696c22928c5f5a4566e059345fd810588
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---
# <a name="deployment"></a>Implementación

En este tema se describen los objetivos, las tareas y los resultados asociados a la **implementación** del Proceso de ciencia de datos en equipo. Este proceso proporciona un ciclo de vida recomendado que puede usar para estructurar los proyectos de ciencia de datos. El ciclo de vida describe las fases principales por las que pasan normalmente los proyectos, a menudo de forma iterativa:

* **Conocimiento del negocio**
* **Adquisición y comprensión de los datos**
* **Modelado**
* **Implementación**
* **Aceptación del cliente**

Esta es una representación visual del **ciclo de vida del proceso de ciencia de datos en equipo**. 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Objetivo
* Los modelos con canalización de datos se implementan en un entorno de producción o similar para que el usuario final los acepte. 

## <a name="how-to-do-it"></a>Modo de hacerlo
La tarea principal que se aborda en esta fase es la siguiente:

* **Uso del modelo**: implemente el modelo y la canalización en un entorno de producción o semejante para el consumo de aplicaciones.

### <a name="41-operationalize-a-model"></a>4.1 Uso del modelo
Cuando ya disponga de un conjunto de modelos que funcionan bien, los puede hacer operativos para que los consuman otras aplicaciones. Dependiendo de los requisitos empresariales, se realizan predicciones en tiempo real o por lotes. Para implementar los modelos se exponen con una interfaz de API abierta. La interfaz permite que el modelo se consuma fácilmente desde diversas aplicaciones, como sitios web en línea, hojas de cálculo, paneles o línea de aplicaciones empresariales y de back-end. Para obtener información sobre cómo crear e implementar un servicio web de Azure Machine Learning, consulte [Implementación de un servicio web Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md). También es un procedimiento recomendado compilar la telemetría y la supervisión en el modelo de producción y la canalización de datos que se implementan. Este procedimiento ayuda con las posteriores tareas de solución de problemas e informes de estado del sistema.  

## <a name="artifacts"></a>Artefactos
* Panel de estado de estado del sistema y métricas clave.
* Informe de modelado final con detalles de implementación.
* Documento de arquitectura de la solución final.


## <a name="next-steps"></a>Pasos siguientes

Estos son los vínculos a cada uno de los pasos del ciclo de vida del Proceso de ciencia de datos en equipo:

* [1. Conocimiento del negocio](lifecycle-business-understanding.md)
* [2. Adquisición y comprensión de los datos](lifecycle-data.md)
* [3. Modelado](lifecycle-modeling.md)
* [4. Implementación](lifecycle-deployment.md)
* [5. Aceptación del cliente](lifecycle-acceptance.md)

También se proporcionan tutoriales completos que muestran todos los pasos del proceso en **escenarios concretos** . Se enumeran y enlazan con descripciones en miniatura en los [tutoriales de ejemplos](walkthroughs.md). En ellos se ilustra cómo combinar herramientas y servicios locales y en la nube en un flujo de trabajo o canalización para crear una aplicación inteligente. 

Para ver ejemplos de ejecución de los pasos en el Proceso de ciencia de datos en equipo que usan Azure Machine Learning Studio, consulte la ruta de aprendizaje [Con Azure ML](http://aka.ms/datascienceprocess).
