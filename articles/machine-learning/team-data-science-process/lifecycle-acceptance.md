---
title: "Fase de aceptación del cliente del ciclo de vida del Proceso de ciencia de datos en equipo: Azure | Microsoft Docs"
description: "Los objetivos, las tareas y los resultados de la fase de aceptación del cliente de los proyectos de ciencia de datos."
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
ms.openlocfilehash: e150b3e7c7e98443264dd5b8aaeda1bfe6047b2c
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2017
---
# <a name="customer-acceptance"></a>Aceptación del cliente

En este artículo se describen los objetivos, las tareas y los resultados asociados a la fase de aceptación del cliente del Proceso de ciencia de datos en equipo (TDSP). Este proceso proporciona un ciclo de vida recomendado que puede usar para estructurar los proyectos de ciencia de datos. El ciclo de vida describe las fases principales por las que pasan normalmente los proyectos, a menudo de forma iterativa:

   1. **Conocimiento del negocio**
   2. **Adquisición y comprensión de los datos**
   3. **Modelado**
   4. **Implementación**
   5. **Aceptación del cliente**

Esta es una representación visual del ciclo de vida de TDSP: 

![Ciclo de vida del TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Objetivo
**Finalización de los resultados del proyecto**: confirme que la canalización, el modelo y su implementación en un entorno de producción cumplen los objetivos del cliente.

## <a name="how-to-do-it"></a>Modo de hacerlo
En esta fase se abordan dos tareas principales:

   * **Validación del sistema**: confirme que el modelo implementado y la canalización cumplen las necesidades del cliente.
   * **Entrega del proyecto**: entregue el proyecto a la entidad que va a ejecutar el sistema en producción.

El cliente debe validar que el sistema satisface sus necesidades empresariales y responde a las preguntas con una precisión aceptable para implementarlo en el entorno de producción y usarlo con la aplicación cliente. Se finaliza y revisa toda la documentación. El proyecto se entrega a la entidad responsable de las operaciones. Esta entidad podría ser, por ejemplo, un equipo de ciencia de datos de clientes o de TI o un agente del cliente responsable del funcionamiento del sistema en producción. 

## <a name="artifacts"></a>Artefactos
El artefacto principal que se genera en esta fase final es el **informe de salida del proyecto para el cliente**. Este informe técnico contiene todos los detalles del proyecto que son útiles para obtener información acerca de cómo hacer funcionar el sistema. TDSP proporciona una plantilla de [informe de salida](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md). Puede usar la plantilla tal cual o puede personalizarla para las necesidades específicas del cliente. 


## <a name="next-steps"></a>Pasos siguientes

Estos son los vínculos a cada uno de los pasos del ciclo de vida del Proceso de ciencia de datos en equipo:

   1. [Conocimiento del negocio](lifecycle-business-understanding.md)
   2. [Adquisición y comprensión de los datos](lifecycle-data.md)
   3. [Modelado](lifecycle-modeling.md)
   4. [Implementación](lifecycle-deployment.md)
   5. [Aceptación del cliente](lifecycle-acceptance.md)

Proporcionamos tutoriales completos que muestran todos los pasos del proceso en escenarios concretos. El artículo con [tutoriales de ejemplo](walkthroughs.md) proporciona una lista de los escenarios con vínculos y descripciones de miniatura. En los tutoriales se muestra cómo combinar servicios y herramientas en la nube y locales en un flujo de trabajo o una canalización con el fin de crear una aplicación inteligente. 

Para obtener ejemplos de cómo ejecutar pasos en TDSP que usan Microsoft Azure Machine Learning Studio, consulte [Uso del Proceso de ciencia de los datos en equipos con Azure Machine Learning](http://aka.ms/datascienceprocess).
