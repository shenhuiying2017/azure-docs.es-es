---
title: "Fase de comprensión y adquisición de los datos del ciclo de vida del Proceso de ciencia de datos en equipo: Azure | Microsoft Docs"
description: "Los objetivos, las tareas y los resultados de la fase de adquisición y comprensión de los datos de los proyectos de ciencia de datos."
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
ms.openlocfilehash: 9618653e8f77c69a3a95fe27ee55c4f05c55a66e
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2017
---
# <a name="data-acquisition-and-understanding"></a>Adquisición y comprensión de los datos

En este artículo se describen los objetivos, las tareas y los resultados asociados a la fase de adquisición y comprensión de los datos del Proceso de ciencia de datos en equipo (TDSP). Este proceso proporciona un ciclo de vida recomendado que puede usar para estructurar los proyectos de ciencia de datos. El ciclo de vida describe las fases principales por las que pasan normalmente los proyectos, a menudo de forma iterativa:

   1. **Conocimiento del negocio**
   2. **Adquisición y comprensión de los datos**
   3. **Modelado**
   4. **Implementación**
   5. **Aceptación del cliente**

Esta es una representación visual del ciclo de vida de TDSP: 

![Ciclo de vida del TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Objetivos
* Genere un conjunto de datos limpio y de alta calidad cuya relación con las variables de destino se entienda. Busque el conjunto de datos en el entorno de análisis de adecuado para prepararse para el modelado.
* Desarrolle una arquitectura de solución de la canalización de datos que actualice y puntúe los datos con regularidad.

## <a name="how-to-do-it"></a>Modo de hacerlo
En esta fase se abordan tres tareas principales:

   * **Introducción de los datos** en el entorno de análisis de destino.
   * **Exploración de los datos** para determinar si su calidad es suficiente para responder a la pregunta. 
   * **Configuración de una canalización de datos** para puntuar los datos nuevos o que se actualizan con regularidad.

### <a name="ingest-the-data"></a>Introducción de los datos
Configure el proceso para mover los datos desde las ubicaciones de origen a las ubicaciones de destino donde se ejecutan las operaciones de análisis, como el entrenamiento y las predicciones. Para obtener detalles técnicos y opciones sobre cómo mover datos con diversos servicios de datos de Azure, vea [Carga de datos en entornos de almacenamiento para el análisis](ingest-data.md). 

### <a name="explore-the-data"></a>Exploración de los datos
Antes de entrenar los modelos, debe desarrollar una comprensión sólida de los datos. A menudo, los conjuntos de datos reales contienen ruido, les faltan datos o presentan un sinfín de discrepancias de otros tipos. Puede utilizar funciones de resumen y visualización de los datos para auditar su calidad y dar la información que se necesita para procesarlos y dejarlos preparados para el modelado. Normalmente, se trata de un proceso iterativo.

TDSP ofrece una utilidad automatizada denominada [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) que facilita la visualización de los datos y la elaboración de informes resumidos de los datos. Se recomienda empezar con IDEAR para explorar la información y contribuir a comprender los datos iniciales interactivamente, sin codificación. A continuación, puede escribir código personalizado para visualizarlos y explorarlos. Para obtener orientación sobre cómo limpiar los datos, consulte [Tareas para preparar los datos para el aprendizaje automático mejorado](prepare-data.md).  

Una vez que esté satisfecho con la calidad de los datos limpios, el siguiente paso es comprender mejor los patrones que son inherentes a los datos. Esto ayuda a elegir y desarrollar un modelo de predicción adecuado para el destino. Busque pruebas que describan la conexión de los datos con el destino. A continuación, determine si hay suficientes datos para avanzar con los siguientes pasos de modelado. Como hemos indicado, normalmente, se trata de un proceso iterativo. Es posible que deba buscar otros orígenes de datos con información más precisa o pertinente con el fin de alimentar el conjunto de datos inicialmente identificado en la fase anterior. 

### <a name="set-up-a-data-pipeline"></a>Configuración de una canalización de datos
Además de la introducción y limpieza iniciales de los datos, suele ser preciso configurar un proceso para puntuar los datos nuevos o actualizarlos con regularidad durante el proceso de aprendizaje continuo. Para ello, puede configurar una canalización de datos o un flujo de trabajo. El artículo [Movimiento de datos desde un servidor SQL Server local hasta SQL Azure con Azure Data Factory](move-sql-azure-adf.md) proporciona un ejemplo de cómo configurar una canalización con [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

En esta fase, desarrolla una arquitectura de solución de la canalización de datos. Desarrolla la canalización en paralelo con la siguiente fase del proyecto de ciencia de datos. En función de las necesidades empresariales y de las limitaciones de los sistemas existentes en los que se integre esta solución, la canalización puede ser de uno de los tipos siguientes: 

   * Basada en lotes
   * Streaming o en tiempo real 
   * Híbrido 

## <a name="artifacts"></a>Artefactos
Estos son los resultados de esta fase:

   * [Informe de la calidad de los datos](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md): este informe contiene resúmenes de los datos, las relaciones entre cada atributo y objetivo, la clasificación de las variables, etc. La herramienta [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) que se incluye como parte del TDSP permite generar rápidamente este informe sobre cualquier conjunto de datos tabular, como un archivo CSV o una tabla relacional. 
   * **Arquitectura de la solución**: la arquitectura de la solución puede ser un diagrama o una descripción de la canalización de datos utilizada para llevar a cabo la tarea de puntuación o las predicciones con los nuevos datos una vez que se ha creado un modelo. También contiene la canalización para volver a entrenar el modelo basándose en los nuevos datos. Almacene el documento en el directorio [Project](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) cuando se usa la plantilla de estructura de directorios de TDSP.
   * **Decisión de punto de comprobación**: antes de comenzar con el proceso completo de diseño de características y con la compilación del modelo, puede volver a evaluar el proyecto para determinar si el valor que está previsto que aporte es suficiente para seguir adelante con él. Por ejemplo, podría estar preparado para continuar, requerir más datos o abandonar el proyecto si no existen datos que respondan a la pregunta.

## <a name="next-steps"></a>Pasos siguientes

Estos son los vínculos a cada uno de los pasos del ciclo de vida del Proceso de ciencia de datos en equipo:

   1. [Conocimiento del negocio](lifecycle-business-understanding.md)
   2. [Adquisición y comprensión de los datos](lifecycle-data.md)
   3. [Modelado](lifecycle-modeling.md)
   4. [Implementación](lifecycle-deployment.md)
   5. [Aceptación del cliente](lifecycle-acceptance.md)

Proporcionamos tutoriales completos que muestran todos los pasos del proceso en escenarios concretos. El artículo con [tutoriales de ejemplo](walkthroughs.md) proporciona una lista de los escenarios con vínculos y descripciones de miniatura. En los tutoriales se muestra cómo combinar servicios y herramientas en la nube y locales en un flujo de trabajo o una canalización con el fin de crear una aplicación inteligente. 

Para obtener ejemplos de cómo ejecutar pasos en TDSP que usan Microsoft Azure Machine Learning Studio, consulte [Uso del Proceso de ciencia de los datos en equipos con Azure Machine Learning](http://aka.ms/datascienceprocess).
