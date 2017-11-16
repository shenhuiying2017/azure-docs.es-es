---
title: 'Ciclo de vida del Proceso de ciencia de datos en equipo: Azure | Microsoft Docs'
description: Pasos necesarios para ejecutar los proyectos de ciencia de datos.
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev;
ms.openlocfilehash: 5d69d4d2371b42b3595cf3dc71d99d913e225c59
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2017
---
# <a name="the-team-data-science-process-lifecycle"></a>El ciclo de vida del proceso de ciencia de datos en equipo

El Proceso de ciencia de datos en equipo (TDSP) ofrece un ciclo de vida recomendado que se puede usar para estructurar los proyectos de ciencia de datos. El ciclo de vida describe el proceso, de principio a fin, que suelen seguir los proyectos al ejecutarlos. Si usa otro ciclo de vida de la ciencia de datos, como Cross Industry Standard Process for Data Mining [(CRISP-DM)](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), Knowledge Discovery in Databases [(KDD)](https://wikipedia.org/wiki/Data_mining#Process) o propio proceso personalizado de la organización, puede seguir usando el TDSP basado en tareas. 

Este ciclo de vida está diseñado para los proyectos de ciencia de datos que se enviarán como parte de aplicaciones inteligentes. Estas aplicaciones implementan modelos de aprendizaje o inteligencia artificial de máquina para realizar un análisis predictivo. Los proyectos de ciencia de datos exploratorios y los proyectos de análisis ad hoc también se pueden beneficiar del uso de este proceso. Pero para esos proyectos, algunos de los pasos descritos a continuación pueden no ser necesarios. 

El ciclo de vida de TDSP se compone de cinco fases principales que se ejecutan de forma iterativa. Estas fases incluyen:

   1. [Conocimiento del negocio](lifecycle-business-understanding.md)
   2. [Adquisición y comprensión de los datos](lifecycle-data.md)
   3. [Modelado](lifecycle-modeling.md)
   4. [Implementación](lifecycle-deployment.md)
   5. [Aceptación del cliente](lifecycle-acceptance.md)

Esta es una representación visual del ciclo de vida de TDSP: 

![Ciclo de vida del TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


El ciclo de vida del TDSP se modela como una secuencia de pasos repetidos que le orientan respecto a las tareas necesarias para usar modelos predictivos. El usuario implementa modelos de predicción en el entorno de producción que tiene previsto utilizar para compilar las aplicaciones inteligentes. El objetivo del ciclo de vida del proceso consiste en hacer avanzar un proyecto de ciencia de datos hacia un punto final de interacción claro. La ciencia de datos es un ejercicio de investigación y detección. La posibilidad de comunicar tareas a su equipo y sus clientes mediante un conjunto bien definido de artefactos que utilizan plantillas estandarizadas ayuda a evitar malos entendidos. El uso de estas plantillas también incrementa la posibilidad de finalizar correctamente un proyecto de ciencia de datos complejo.

Para cada fase, damos la siguiente información:

   * **Objetivos**: los objetivos específicos.
   * **Cómo hacerlo**: un esquema de las tareas específicas y orientación sobre cómo realizarlas.
   * **Artefactos**: las entregas y la asistencia para producirlas.

## <a name="next-steps"></a>Pasos siguientes

Proporcionamos tutoriales completos que muestran todos los pasos del proceso en escenarios concretos. El artículo con [tutoriales de ejemplo](walkthroughs.md) proporciona una lista de los escenarios con vínculos y descripciones de miniatura. En los tutoriales se muestra cómo combinar servicios y herramientas en la nube y locales en un flujo de trabajo o una canalización con el fin de crear una aplicación inteligente. 

Para obtener ejemplos de cómo ejecutar pasos en TDSP que usan Microsoft Azure Machine Learning Studio, consulte [Uso del Proceso de ciencia de los datos en equipos con Azure Machine Learning](http://aka.ms/datascienceprocess).
