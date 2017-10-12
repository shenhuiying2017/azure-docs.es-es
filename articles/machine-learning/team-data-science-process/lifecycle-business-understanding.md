---
title: "Fase de descripción de negocio del ciclo de vida del Proceso de ciencia de datos en equipo: Azure | Microsoft Docs"
description: "Los objetivos, las tareas y los resultados de la fase de descripción del negocio de los proyectos de ciencia de datos."
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
ms.openlocfilehash: 2adce61f8185bd86a6a870bb5752fe936701b0af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="business-understanding"></a>Conocimiento del negocio

En este tema se describen los objetivos, las tareas y los resultados asociados a la **fase de descripción del negocio** del Proceso de ciencia de datos en equipo. Este proceso proporciona un ciclo de vida recomendado que puede usar para estructurar los proyectos de ciencia de datos. El ciclo de vida describe las fases principales por las que pasan normalmente los proyectos, a menudo de forma iterativa:

* **Conocimiento del negocio**
* **Adquisición y comprensión de los datos**
* **Modelado**
* **Implementación**
* **Aceptación del cliente**

Esta es una representación visual del **ciclo de vida del proceso de ciencia de datos en equipo**. 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Objetivos
* Se especifican las **variables principales** que servirán como **objetivos del modelo** y cuyas métricas asociadas se utilizan para determinar el éxito del proyecto.
* Se identifican los **orígenes de datos** pertinentes a los que tiene acceso la empresa o necesita obtenerlos.

## <a name="how-to-do-it"></a>Modo de hacerlo
En esta fase se abordan dos tareas principales: 

* **Definición de objetivos**: trabaje con el cliente y con otras partes interesadas para comprender e identificar los problemas de la empresa. Formule preguntas que definan los objetivos empresariales y a las que puedan aplicarse las técnicas de ciencia de datos.
* **Identifique los orígenes de datos**: busque los datos pertinentes que lo ayuden a responder a las preguntas que definen los objetivos del proyecto.

### <a name="11-define-objectives"></a>1.1 Definición de objetivos

1. Un objetivo fundamental de este paso consiste en identificar las principales **variables empresariales** que el análisis deberá predecir. Estas variables se denominan **objetivos del modelo** y las métricas asociadas a ellas se utilizan para determinar el éxito del proyecto. Dos ejemplos de estos destinos son la previsión de ventas o la probabilidad de que un pedido sea fraudulento.

2. Para definir los **objetivos del proyecto**, plantee y refine preguntas "certeras" que sean pertinentes, específicas y sin ambigüedad alguna. La ciencia de datos es el proceso de uso de nombres y números para responder a estas preguntas. Para más información sobre cómo hacer preguntas difíciles, consulte el blog [How to do Data Science](https://blogs.technet.microsoft.com/machinelearning/2016/03/28/how-to-do-data-science/) (Realización de procesos de ciencia de datos). La ciencia de datos y el aprendizaje automático suelen utilizarse para responder a cinco tipos de preguntas:
 
   * ¿Cuánto? o ¿cuántos? (regresión)
   * ¿Qué categoría? (clasificación)
   * ¿Qué grupo? (agrupación en clústeres)
   * ¿Es extraño? (detección de anomalías)
   * ¿Qué opción se debe elegir? (recomendación)

    Determine cuál de las siguientes es su pregunta y cómo la respuesta logra sus objetivos empresariales.

3. Defina el **equipo del proyecto** especificando los roles y las responsabilidades de sus miembros. Desarrolle un plan general de hitos que se pueda repetir a medida que se descubra más información.  

4. **Defina las métricas del éxito**. Por ejemplo: Lograr una precisión en la predicción de la pérdida de clientes del X % al final de este proyecto de 3 meses, que nos permita ofrecer promociones que reduzcan esa pérdida. Las métricas deben cumplir los requisitos **SMART**: 
   * **S**pecific (específicas) 
   * **M**easurable (mensurables)
   * **A**chievable (alcanzables) 
   * **R**elevant (pertinentes) 
   * **T**ime-bound (con un límite de tiempo) 

### <a name="12-identify-data-sources"></a>1.2 Identificación de los orígenes de datos
Identifique los orígenes de datos que contienen ejemplos conocidos de respuestas a las preguntas certeras. Busque los siguientes datos:

* Datos **pertinentes** para la pregunta. ¿Tenemos indicadores para medir el objetivo y las características que están relacionados con él?
* Datos que representen una **medida precisa** de nuestro objetivo de modelo y de las características de interés.

Por ejemplo, no es raro que descubra que necesitan los sistemas existentes tengan que recopilar y registrar tipos de datos adicionales para solucionar el problema y alcanzar los objetivos del proyecto. En este caso, puede ser conveniente buscar orígenes de datos externos o actualizar los sistemas para recopilar datos nuevos.

## <a name="artifacts"></a>Artefactos
Estos son los resultados de esta fase:

* [**Documento marco**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): se ofrece una plantilla estándar en la definición de estructura del proyecto de TDSP. Se trata de un documento dinámico que se actualiza a lo largo del proyecto a medida que se realizan nuevos descubrimientos y se modifican los requisitos empresariales. La clave consiste en realizar iteraciones de este documento e incorporarle la información oportuna según se avance a lo largo del proceso de descubrimiento. Es importante que el cliente y las demás partes interesadas se impliquen en la realización de cambios y que se les informe claramente sobre las razones que los motivan.  
* [**Orígenes de datos**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#raw-data-sources): se trata de la sección **orígenes de datos sin procesar** del informe de **definiciones de datos** que se encuentra en la carpeta **informe de datos** del proyecto TDSP. Especifica las ubicaciones originales y de destino para los datos sin procesar. En las fases posteriores, deberá rellenar más detalles, tales como los scripts para mover los datos al entorno de análisis.  
* [**Diccionarios de datos**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/DataDictionaries): este documento proporciona descripciones de los datos proporcionados por el cliente. Estas descripciones incluyen información sobre el esquema (tipos de datos, información sobre las reglas de validación, si hay) y los diagramas de relación de entidades, si están disponibles.

## <a name="next-steps"></a>Pasos siguientes

Estos son los vínculos a cada uno de los pasos del ciclo de vida del Proceso de ciencia de datos en equipo:

* [1. Conocimiento del negocio](lifecycle-business-understanding.md)
* [2. Adquisición y comprensión de los datos](lifecycle-data.md)
* [3. Modelado](lifecycle-modeling.md)
* [4. Implementación](lifecycle-deployment.md)
* [5. Aceptación del cliente](lifecycle-acceptance.md)

También se proporcionan tutoriales completos que muestran todos los pasos del proceso en **escenarios concretos** . Se enumeran y enlazan con descripciones en miniatura en los [tutoriales de ejemplos](walkthroughs.md). En ellos se ilustra cómo combinar herramientas y servicios locales y en la nube en un flujo de trabajo o canalización para crear una aplicación inteligente. 

Para ver ejemplos de ejecución de los pasos en el Proceso de ciencia de datos en equipo que usan Azure Machine Learning Studio, consulte la ruta de aprendizaje [Con Azure ML](http://aka.ms/datascienceprocess).