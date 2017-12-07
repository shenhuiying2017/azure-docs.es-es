---
title: Ejecutar tareas de ciencia de datos - Azure Machine Learning | Microsoft Docs
description: "Describe cómo un científico de datos puede ejecutar un proyecto de ciencia de datos de forma controlada por versiones, de colaboración y cuyo seguimiento se puede realizar."
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
ms.date: 11/28/2017
ms.author: bradsev;
ms.openlocfilehash: 1ad4e8c117f93f2f085c01fae2a5ab38cdd10d2f
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2017
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Ejecutar tareas de ciencia de datos: exploración, modelado e implementación

Entre las tareas de ciencia de datos habituales se incluyen la implementación, el modelado y la exploración de datos. En este artículo se muestra cómo usar las utilidades **Interactive Data Exploration, Analysis, and Reporting (IDEAR)** y **Automated Modeling and Reporting (AMAR)** para completar varias tareas de ciencia de datos comunes como la exploración interactiva de datos, el análisis de datos, la creación de informes y la creación de modelos. Asimismo, se describen las opciones existentes para implementar un modelo en un entorno de producción mediante diversos kits de herramientas y plataformas de datos, como los siguientes:

- [Azure Machine Learning](../preview/index.yml)
- [SQL-Server con servicios de ML](https://docs.microsoft.com/sql/advanced-analytics/r/r-services#in-database-analytics-with-sql-server)
- [Servidor de Microsoft Machine Learning](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## 1. <a name='DataQualityReportUtility-1'></a> Exploración 

Un científico de datos puede realizar la exploración y los informes de diversas formas: mediante bibliotecas y paquetes disponibles para Python (matplotlib por ejemplo) o con R (ggplot o lattice por ejemplo). Los científicos de datos pueden personalizar dicho código para satisfacer las necesidades de exploración de datos para escenarios específicos. Las necesidades de lidiar con datos estructurados son distintas que en el caso de los datos no estructurados, como texto o imágenes. 

Productos como Azure Machine Learning Workbench también proporcionan una [preparación avanzada de datos](../preview/tutorial-bikeshare-dataprep.md) para la exploración y la doma de datos (wrangling), incluida la creación de características. El usuario debe decidir sobre las herramientas, las bibliotecas y los paquetes que mejor se adapten a sus necesidades. 

Al final de este fase debe entregarse un informe de exploración de datos. El informe debe proporcionar una vista bastante completa de los datos que se van a usar para el modelado y una evaluación de si los datos son adecuados para continuar con el paso de modelado. Las utilidades del Proceso de ciencia de los datos en equipos explicadas en las siguientes secciones para la exploración parcialmente automatizada, el modelado y la creación de informes también proporcionan informes de modelado y exploración de datos estandarizados. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Creación de informes, análisis y exploración interactiva de datos mediante la utilidad IDEAR

Esta utilidad de R basado en marcado o en bloc de notas Python proporciona una herramienta flexible e interactiva para evaluar y explorar conjuntos de datos. Los usuarios pueden generar rápidamente informes desde el conjunto de datos con codificación mínima. Los usuarios pueden hacer clic en los botones para exportar los resultados de la exploración de la herramienta interactiva a un informe final, que se puede entregar a los clientes, o bien utilizarse para tomar decisiones sobre qué variables hay que incluir en el paso de modelado siguiente.

En este momento, la herramienta solo funciona en tramas de datos en memoria. Se necesita un archivo YAML para especificar los parámetros del conjunto de datos que se van a explorar. Para más información, consulte [IDEAR en utilidades de ciencia de datos de TDSP](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


## 2. <a name='ModelingUtility-2'></a> Modelado

Hay diversos kits de herramientas y paquetes para entrenar modelos en diversos idiomas. Los científicos de datos no deben tener reparos en usar aquellos con los cuales se sientan cómodos, siempre que se cumplan las consideraciones de rendimiento relacionadas con la precisión y la latencia para los escenarios de producción y los casos de uso empresariales pertinentes.

En la siguiente sección se muestra cómo usar una utilidad TDSP basada en R para el modelado parcialmente automatizado. Esta utilidad AMAR se puede usar para generar modelos de línea base rápidamente, así como los parámetros que es necesario ajustar para proporcionar un mejor modelo de rendimiento.
En la siguiente sección de administración de modelos se muestra cómo tener un sistema para registrar y administrar varios modelos.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Entrenamiento de modelos: modelado y creación de informes mediante la utilidad AMAR

La [utilidad Automated Modeling and Reporting (AMAR)](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) proporciona una herramienta personalizable y parcialmente automatizada para realizar la creación de modelos con barrido de hiperparámetros y comparar la precisión de los modelos. 

La utilidad de creación de modelos es un archivo R Markdown que se puede ejecutar para producir una salida HTML autónoma con una tabla de contenido para facilitar la navegación a través de sus diferentes secciones. Se realizan tres algoritmos cuando el archivo Markdown se ejecuta (convierte): regresión regularizada con el paquete glmnet, bosque aleatorio mediante el paquete randomForest y potenciación de árboles mediante el paquete xgboost). Cada uno de estos algoritmos produce un modelo entrenado. Después se compara la precisión de estos modelos y se notifican los trazados de la importancia relativa de la característica. Actualmente, hay dos utilidades: uno para una tarea de clasificación binaria y otra para una tarea de regresión. Las principales diferencias entre ellos es la forma cómo los parámetros de control y las métricas de precisión se especifican para estas tareas de aprendizaje. 

Se usa un archivo YAML para especificar:

- la entrada de datos (un origen de SQL o un archivo de datos de R) 
- qué parte de los datos se usa para el aprendizaje y qué parte para la prueba
- qué algoritmos se van a ejecutar 
- la elección de los parámetros de control para la optimización de modelo:
    - validación cruzada 
    - arranque
    - subconjuntos de la validación cruzada
- los conjuntos de hiperparámetros para cada algoritmo. 

También es posible modificar en el archivo Yaml para ejecutar los modelos rápidamente el número de algoritmos, el número de subconjuntos para la optimización, los hiperparámetros y el número de conjuntos de hiperparámetros que se itera. Por ejemplo, se pueden ejecutar con un número menor de subconjuntos de CV, un número menor de conjuntos de parámetros. Si está justificado, también se pueden ejecutar de manera más completa con un número mayor de subconjuntos de CV o un número mayor de conjuntos de parámetros.

Para más información, consulte la [utilidad de modelado y servicio de informes automatizada en las utilidades de ciencia de datos de TDSP](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).

### <a name="model-management"></a>Administración de modelos
Tras la creación de varios modelos, suele ser necesario tener un sistema para registrar y administrar los modelos. Normalmente, necesita una combinación de scripts o API y una base de datos back-end o un sistema de control de versiones. Algunas opciones que puede tener en cuenta para estas tareas de administración son:

1. [Azure Machine Learning: servicio Administración de modelos](../preview/index.yml)
2. [ModelDB de MIT](https://mitdbg.github.io/modeldb/) 
3. [SQL-Server como sistema de administración de modelos](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Servidor de Microsoft Machine Learning](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## 3. <a name='Deployment-3'></a> Implementación

La implementación de producción permite que un modelo desempeñe un rol activo en una empresa. Las predicciones a partir de un modelo implementado pueden usarse en las decisiones empresariales.

### <a name="production-platforms"></a>Plataformas de producción
Hay varios enfoques y plataformas para poner los modelos en producción. Estas son algunas opciones:


- [Implementación de modelos en Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/model-management-overview)
- [Implementación de un modelo en SQL-Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Servidor de Microsoft Machine Learning](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>
>
>NOTA: Antes de la implementación, uno debe asegurarse de que la latencia de la puntuación de los modelos es lo suficientemente baja para usarse en producción.
>

Hay más ejemplos disponibles en tutoriales que muestran todos los pasos del proceso en **escenarios concretos**. Se enumeran y enlazan con descripciones en miniatura en los [tutoriales de ejemplo](walkthroughs.md). En ellos se ilustra cómo combinar herramientas y servicios locales y en la nube en un flujo de trabajo o canalización para crear una aplicación inteligente.

NOTA: Para implementar mediante Azure Machine Learning Studio, consulte [Implementar un servicio web de Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md).

### <a name="ab-testing"></a>Pruebas A/B
Cuando hay varios modelos en producción, puede resultar útil realizar [pruebas A/B](https://en.wikipedia.org/wiki/A/B_testing) para comparar el rendimiento de los modelos. 

 
## <a name="next-steps"></a>Pasos siguientes

En [Track progress of data science projects](track-progress.md) (Realizar un seguimiento del progreso de los proyectos de ciencia de datos) se muestra cómo puede un científico de datos realizar un seguimiento del progreso de un proyecto de ciencia de datos.
 


