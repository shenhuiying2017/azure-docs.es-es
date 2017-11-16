---
title: Fase de modelado del ciclo de vida del Proceso de ciencia de datos en equipo| Microsoft Docs
description: Los objetivos, las tareas y los resultados de la fase de modelado de los proyectos de ciencia de datos.
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
ms.date: 11/04/2017
ms.author: bradsev;
ms.openlocfilehash: a7fc5f2128e9c13182ca5bd7a6bd61ae41ef775c
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2017
---
# <a name="modeling"></a>Modelado

En este artículo se describen los objetivos, las tareas y los resultados asociados a la fase de modelado del Proceso de ciencia de datos en equipo (TDSP). Este proceso proporciona un ciclo de vida recomendado que puede usar para estructurar los proyectos de ciencia de datos. El ciclo de vida describe las fases principales por las que pasan normalmente los proyectos, a menudo de forma iterativa:

   1. **Conocimiento del negocio**
   2. **Adquisición y comprensión de los datos**
   3. **Modelado**
   4. **Implementación**
   5. **Aceptación del cliente**

Esta es una representación visual del ciclo de vida de TDSP:

![Ciclo de vida del TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Objetivos
* Determinar las características óptimas de los datos para el modelo de aprendizaje automático.
* Crear un modelo de aprendizaje automático informativo que predice el objetivo con la máxima precisión.
* Crear un modelo de aprendizaje automático que es adecuado para entornos de producción.

## <a name="how-to-do-it"></a>Modo de hacerlo
En esta fase se abordan tres tareas principales:

  * **Diseño de características**: cree las características de datos a partir de los datos sin procesar para facilitar el entrenamiento del modelo.
  * **Entrenamiento del modelo**: busque el modelo que responda a la pregunta con la máxima precisión comparando sus métricas de éxito.
  * Determine si el modelo es **adecuado para su uso en producción**.

### <a name="feature-engineering"></a>Ingeniería de características
El diseño de características consiste en incluir, agregar y transformar variables sin procesar para crear las características que se utilizan en el análisis. Si desea conocer con detalle los factores en que se basa un modelo, debe entender cómo se relacionan entre sí las características y cómo deben utilizarlas los algoritmos de aprendizaje automático. 

Este paso requiere una combinación creativa de experiencia de dominio y de los conocimientos obtenidos en el paso de exploración de datos. La ingeniería de características es un acto de equilibrio de buscar e incluir variables informativas evitando al mismo tiempo que se utilicen demasiadas variables no relacionadas. Las variables informativas mejoran los resultados; las variables no relacionadas introducen ruido innecesario en el modelo. También debe generar estas características para los nuevos datos obtenidos durante la puntuación. Así pues, la generación de estas características solamente puede depender de los datos que están disponibles en el momento de la puntuación. 

Para obtener orientación técnica sobre diseño de características cuando se usan diversas tecnologías de datos de Azure, consulte [Ingeniería de características en ciencia de datos](create-features.md). 

### <a name="model-training"></a>Entrenamiento del modelo
Según el tipo de la pregunta que intenta responder, existen muchos algoritmos de modelado disponibles. Para obtener orientación sobre cómo elegir el algoritmo correcto, consulte el artículo sobre [cómo elegir algoritmos para Microsoft Azure Machine Learning](../studio/algorithm-choice.md). Aunque este artículo utiliza Azure Machine Learning, la orientación resulta útil para otros proyectos de aprendizaje automático. 

El proceso de entrenamiento del modelo incluye los pasos siguientes: 

   * **Dividir los datos de entrada** aleatoriamente para el modelado en un conjunto de datos de aprendizaje y un conjunto de datos de prueba.
   * **Compilar los modelos** mediante el conjunto de datos de aprendizaje.
   * **Evaluar** el entrenamiento y el conjunto de datos de prueba. Use una serie de algoritmos de aprendizaje automático paralelos junto con los diversos parámetros de ajuste asociados (lo que se denomina *barrido de parámetros*) que están orientados a responder a la pregunta de interés con los datos actuales.
   * **Determinar la "mejor" solución** para responder a la pregunta mediante la comparación de las métricas de éxito entre los métodos alternativos.

> [!NOTE]
> **Evitar la pérdida de datos**: puede provocar la pérdida de datos si incluye datos desde fuera del conjunto de datos de aprendizaje que permite que un modelo o algoritmo de aprendizaje automático haga predicciones demasiado buenas para ser realistas. La pérdida de datos es uno de los motivos por los que los científicos de datos se preocupan cuando obtienen resultados predictivos que parecen demasiado buenos para ser ciertos. Estas dependencias pueden ser difíciles de detectar. Para evitar fugas, a menudo es preciso efectuar iteraciones entre la generación del conjunto de datos de análisis, la creación del modelo y la evaluación de la precisión de los resultados. 
> 
> 

Ofrecemos una [herramienta de modelado e informes automatizados](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) con TDSP que permite llevar a cabo varios algoritmos y barridos de parámetros para generar un modelo de base de referencia. También genera un informe de modelado de base de referencia que resume el rendimiento de cada combinación de modelo y parámetros, incluida la importancia de las variables. Este proceso también es iterativo, porque así permite avanzar en el diseño de características. 

## <a name="artifacts"></a>Artefactos
Los artefactos que se producen en esta fase incluyen, entre otros:

   * [Conjuntos de características](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets): las características desarrolladas para el modelado se describen en la sección de **conjuntos de características** del informe **Definición de datos**. Contiene punteros al código para generar las características y la descripción de cómo se ha generado cada una de ellas.
   * [Informe del modelo](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): para cada modelo que se prueba, se genera un informe estándar basado en una plantilla que proporciona información detallada sobre cada experimento.
   * **Decisión de punto de comprobación**: evalúe si el modelo funciona lo bastante bien para implementarlo en un sistema de producción. Algunas preguntas clave son:
     * ¿El modelo responde a la pregunta con la confianza suficiente considerando los datos de prueba? 
     * ¿Debe probar algún planteamiento alternativo? ¿Debe recopilar datos adicionales, rediseñar las características o experimentar con otros algoritmos?

## <a name="next-steps"></a>Pasos siguientes

Estos son los vínculos a cada uno de los pasos del ciclo de vida del Proceso de ciencia de datos en equipo:

   1. [Conocimiento del negocio](lifecycle-business-understanding.md)
   2. [Adquisición y comprensión de los datos](lifecycle-data.md)
   3. [Modelado](lifecycle-modeling.md)
   4. [Implementación](lifecycle-deployment.md)
   5. [Aceptación del cliente](lifecycle-acceptance.md)

Proporcionamos tutoriales completos que muestran todos los pasos del proceso en escenarios concretos. El artículo con [tutoriales de ejemplo](walkthroughs.md) proporciona una lista de los escenarios con vínculos y descripciones de miniatura. En los tutoriales se muestra cómo combinar servicios y herramientas en la nube y locales en un flujo de trabajo o una canalización con el fin de crear una aplicación inteligente. 

Para obtener ejemplos de cómo ejecutar pasos en TDSP que usan Microsoft Azure Machine Learning Studio, consulte [Uso del Proceso de ciencia de los datos en equipos con Azure Machine Learning](http://aka.ms/datascienceprocess). 
