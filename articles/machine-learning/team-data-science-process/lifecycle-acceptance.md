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
ms.date: 09/02/2017
ms.author: bradsev;
ms.openlocfilehash: 953f090f775da5e48b2f4ed36550a5624ae4596b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="customer-acceptance"></a>Aceptación del cliente

En este tema se describen los objetivos, las tareas y los resultados asociados a la **fase de aceptación del cliente** del Proceso de ciencia de datos en equipo. Este proceso proporciona un ciclo de vida recomendado que puede usar para estructurar los proyectos de ciencia de datos. El ciclo de vida describe las fases principales por las que pasan normalmente los proyectos, a menudo de forma iterativa:

* **Conocimiento del negocio**
* **Adquisición y comprensión de los datos**
* **Modelado**
* **Implementación**
* **Aceptación del cliente**

Esta es una representación visual del **ciclo de vida del proceso de ciencia de datos en equipo**. 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Objetivo
* **Finalización de los resultados del proyecto**: confirme que la canalización, el modelo y su implementación en un entorno de producción cumplen los objetivos del cliente.

## <a name="how-to-do-it"></a>Modo de hacerlo
En esta fase se abordan dos tareas principales:

* **Validación del sistema**: confirme que el modelo implementado y la canalización cumplen las necesidades del cliente.
* **Entrega del proyecto**: a la entidad que va a utilizar el sistema en el entorno de producción.

El cliente debe validar que el sistema satisface sus necesidades empresariales y responde a las preguntas con una precisión aceptable para implementarlo en el entorno de producción y usarlo con la aplicación cliente. Se finaliza y revisa toda la documentación. Se lleva a cabo la entrega del proyecto a la entidad responsable de las operaciones. Esta entidad podría ser, por ejemplo, un equipo de ciencia de datos de clientes o de TI o un agente del cliente responsable del funcionamiento del sistema en producción. 

## <a name="artifacts"></a>Artefactos
El artefacto principal que se genera en esta fase final es el **informe de salida del proyecto para el cliente**. Se trata del informe técnico que contiene todos los detalles pertinentes del proyecto que pueden resultar útiles para conocer y utilizar el sistema. TDSP incluye una plantilla de [informe de salida](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) que se puede utilizar tal cual o personalizarse de acuerdo con las necesidades concretas del cliente. 


## <a name="next-steps"></a>Pasos siguientes

Estos son los vínculos a cada uno de los pasos del ciclo de vida del Proceso de ciencia de datos en equipo:

* [1. Conocimiento del negocio](lifecycle-business-understanding.md)
* [2. Adquisición y comprensión de los datos](lifecycle-data.md)
* [3. Modelado](lifecycle-modeling.md)
* [4. Implementación](lifecycle-deployment.md)
* [5. Aceptación del cliente](lifecycle-acceptance.md)

También se proporcionan tutoriales completos que muestran todos los pasos del proceso en **escenarios concretos** . Se enumeran y enlazan con descripciones en miniatura en los [tutoriales de ejemplos](walkthroughs.md). En ellos se ilustra cómo combinar herramientas y servicios locales y en la nube en un flujo de trabajo o canalización para crear una aplicación inteligente. 

Para ver ejemplos de ejecución de los pasos en el Proceso de ciencia de datos en equipo que usan Azure Machine Learning Studio, consulte la ruta de aprendizaje [Con Azure ML](http://aka.ms/datascienceprocess).