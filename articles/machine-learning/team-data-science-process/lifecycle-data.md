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
ms.date: 09/02/2017
ms.author: bradsev;
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: eea3c357ebf6ad920c0ddebdb979aa07aece4794
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---
# <a name="data-acquisition-and-understanding"></a>Adquisición y comprensión de los datos

En este tema se describen los objetivos, las tareas y los resultados asociados a la **fase de adquisición y comprensión de los datos** del Proceso de ciencia de datos en equipo. Este proceso proporciona un ciclo de vida recomendado que puede usar para estructurar los proyectos de ciencia de datos. El ciclo de vida describe las fases principales por las que pasan normalmente los proyectos, a menudo de forma iterativa:

* **Conocimiento del negocio**
* **Adquisición y comprensión de los datos**
* **Modelado**
* **Implementación**
* **Aceptación del cliente**

Esta es una representación visual del **ciclo de vida del proceso de ciencia de datos en equipo**. 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Objetivos
* Un conjunto de datos limpio y de gran calidad cuyas relaciones con las variables de destino que se encuentran en el entorno de análisis apropiado se comprendan y esté listo para el modelado.
* Se ha desarrollado una arquitectura de la solución de la canalización de datos para actualizar y puntuar datos con regularidad.

## <a name="how-to-do-it"></a>Modo de hacerlo
En esta fase se abordan tres tareas principales:

* **Introducción de los datos** en el entorno de análisis de destino.
* **Exploración de los datos** para determinar si su calidad es suficiente para responder a la pregunta. 
* **Configuración de una canalización de datos** para puntuar los datos nuevos o que se actualizan con regularidad.

### <a name="21-ingest-the-data"></a>2.1 Introducción de los datos
Configure el proceso para mover los datos desde las ubicaciones de origen a las ubicaciones de destino donde se llevarán a cabo las operaciones de análisis, como el entrenamiento y las predicciones. Para obtener detalles técnicos y opciones sobre cómo hacer esto con diversos servicios de datos de Azure, vea [Carga de datos en entornos de almacenamiento para el análisis](ingest-data.md). 

### <a name="22-explore-the-data"></a>2.2 Exploración de los datos
Antes de entrenar los modelos, debe desarrollar una comprensión sólida de los datos. A menudo, los conjuntos de datos reales contienen ruido, les faltan datos o presentan un sinfín de discrepancias de otros tipos. Se pueden utilizar funciones de resumen y visualización de los datos para auditar su calidad y dar la información que se necesita para procesarlos y dejarlos preparados para el modelado. Normalmente, se trata de un proceso iterativo.

TDSP ofrece una utilidad automatizada denominada [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) que facilita la visualización de los datos y la elaboración de informes resumidos de los datos. Se recomienda empezar con IDEAR para explorar la información y contribuir a comprender los datos iniciales interactivamente, sin codificación, y, a continuación, escribir el código personalizado para visualizarlos y explorarlos. Para obtener orientación sobre cómo limpiar los datos, consulte [Tareas para preparar los datos para el aprendizaje automático mejorado](prepare-data.md).  

Cuando esté satisfecho con la calidad de los datos limpios, el paso siguiente consiste en comprender mejor los patrones inherentes a los datos que lo ayudarán a elegir y desarrollar un modelo de predicción adecuado para el objetivo. Busque pruebas que indiquen en qué medida están conectados los datos al objetivo y si hay suficientes para avanzar a los siguientes pasos de modelado. Como hemos indicado, normalmente, se trata de un proceso iterativo. Es posible que deba buscar otros orígenes de datos con información más precisa o pertinente con el fin de alimentar el conjunto de datos inicialmente identificado en la fase anterior.  

### <a name="23-set-up-a-data-pipeline"></a>2.3 Configuración de una canalización de datos
Además de la introducción y limpieza iniciales de los datos, suele ser preciso configurar un proceso para puntuar los datos nuevos o actualizarlos con regularidad durante el proceso de aprendizaje continuo. Para ello, puede configurar una canalización de datos o un flujo de trabajo. A continuación, se muestra un [ejemplo](move-sql-azure-adf.md) de cómo configurar una canalización con [Data Factory de Azure](https://azure.microsoft.com/services/data-factory/). 

En esta fase se desarrolla a una arquitectura de la solución de la canalización de datos. La canalización se desarrolla también paralelamente a las siguientes fases del proyecto de ciencia de datos. La canalización puede realizarse por lotes, mediante una transmisión por secuencias en tiempo real o bien utilizando una combinación de ambos métodos, en función de las necesidades empresariales y de las limitaciones de los sistemas existentes en los que se integre esta solución. 

## <a name="artifacts"></a>Artefactos
Estos son los resultados de esta fase:

* [**Informe de la calidad de los datos**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md): este informe contiene resúmenes de los datos, las relaciones entre cada atributo y objetivo, la clasificación de las variables, etc. La herramienta [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) que se incluye como parte del TDSP permite generar rápidamente este informe sobre cualquier conjunto de datos tabular, como un archivo CSV o una tabla relacional. 
* **Arquitectura de la solución**: puede ser un diagrama o una descripción de la canalización de datos utilizada para llevar a cabo la tarea de puntuación o las predicciones con los nuevos datos una vez que se ha creado un modelo. También contiene la canalización para volver a entrenar el modelo basándose en los nuevos datos. El documento se almacena en el directorio [Project](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) cuando se usa la plantilla de estructura de directorios de TDSP.
* **Decisión de punto de comprobación**: antes de comenzar con el proceso completo de diseño de características y con la compilación del modelo, puede volver a evaluar el proyecto para determinar si el valor que está previsto que aporte es suficiente para seguir adelante con él. Por ejemplo, podría estar preparado para continuar, requerir más datos o abandonar el proyecto si no existen datos que respondan a la pregunta.

## <a name="next-steps"></a>Pasos siguientes

Estos son los vínculos a cada uno de los pasos del ciclo de vida del Proceso de ciencia de datos en equipo:

* [1. Conocimiento del negocio](lifecycle-business-understanding.md)
* [2. Adquisición y comprensión de los datos](lifecycle-data.md)
* [3. Modelado](lifecycle-modeling.md)
* [4. Implementación](lifecycle-deployment.md)
* [5. Aceptación del cliente](lifecycle-acceptance.md)

También se proporcionan tutoriales completos que muestran todos los pasos del proceso en **escenarios concretos** . Se enumeran y enlazan con descripciones en miniatura en los [tutoriales de ejemplos](walkthroughs.md). En ellos se ilustra cómo combinar herramientas y servicios locales y en la nube en un flujo de trabajo o canalización para crear una aplicación inteligente.  

Para ver ejemplos de ejecución de los pasos en el Proceso de ciencia de datos en equipo que usan Azure Machine Learning Studio, consulte la ruta de aprendizaje [Con Azure ML](http://aka.ms/datascienceprocess).
