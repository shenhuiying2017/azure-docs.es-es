---
title: Ciclo de vida del proceso de ciencia de datos en equipo de Azure | Microsoft Docs
description: Pasos necesarios para ejecutar los proyectos de ciencia de datos.
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: bradsev;
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 9b8ef4f1165a89fa6ed1b64b44d58bb45f08f232
ms.contentlocale: es-es
ms.lasthandoff: 08/19/2017

---
# <a name="team-data-science-process-lifecycle"></a>Ciclo de vida del proceso de ciencia de datos en equipo

El proceso de ciencia de datos en equipo (TDSP) ofrece un ciclo de vida recomendado que se puede usar para estructurar los proyectos de ciencia de datos. El ciclo de vida describe el proceso, de principio a fin, que suelen seguir los proyectos al ejecutarlos. Aunque esté usando otro ciclo de vida de ciencia de datos, como [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) o el proceso personalizado de su organización, puede usar también el TDSP basado en tareas con estos ciclos de vida de desarrollo. 

Este ciclo de vida se ha diseñado para los proyectos de ciencia de datos que se enviarán como parte de aplicaciones inteligentes. Estas aplicaciones implementan modelos de aprendizaje o inteligencia artificial de máquina para realizar un análisis predictivo. Los proyectos de ciencia de datos exploratorios y los proyectos de análisis ad hoc también se pueden beneficiar del uso de este proceso. Pero, en estos casos, puede que algunos de los pasos descritos no sean necesarios.    

Esta es una representación visual del **ciclo de vida del proceso de ciencia de datos en equipo**. 

![Ciclo de vida de TDSP](./media/data-science-process-overview/tdsp-lifecycle.png) 

El ciclo de vida de TDSP se compone de cinco fases principales que se ejecutan de forma iterativa. Entre ellos se incluyen los siguientes:

* **Conocimiento del negocio**
* **Adquisición y comprensión de los datos**
* **Modelado**
* **Implementación**
* **Aceptación del cliente**

Para cada fase, damos la siguiente información:

* **Objetivos**: Los objetivos específicos.
* **Modo de hacerlo**: se describen las tareas específicas y se facilita orientación para llevarlas a cabo.
* **Artefactos**: las entregas y la asistencia para producirlas.


## <a name="1-business-understanding"></a>1. Conocimiento del negocio

### <a name="goals"></a>Objetivos
* Se especifican las **variables principales** que servirán como **objetivos del modelo** y cuyas métricas asociadas se utilizan para determinar el éxito del proyecto.
* Se identifican los **orígenes de datos** pertinentes a los que tiene acceso la empresa o necesita obtenerlos.

### <a name="how-to-do-it"></a>Modo de hacerlo
En esta fase se abordan dos tareas principales: 

* **Definición de objetivos**: trabaje con el cliente y con otras partes interesadas para comprender e identificar los problemas de la empresa. Formule preguntas que definan los objetivos empresariales y a las que puedan aplicarse las técnicas de ciencia de datos.
* **Identifique los orígenes de datos**: busque los datos pertinentes que lo ayuden a responder a las preguntas que definen los objetivos del proyecto.

#### <a name="11-define-objectives"></a>1.1 Definición de objetivos

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

#### <a name="12-identify-data-sources"></a>1.2 Identificación de los orígenes de datos
Identifique los orígenes de datos que contienen ejemplos conocidos de respuestas a las preguntas certeras. Busque los siguientes datos:

* Datos **pertinentes** para la pregunta. ¿Tenemos indicadores para medir el objetivo y las características que están relacionados con él?
* Datos que representen una **medida precisa** de nuestro objetivo de modelo y de las características de interés.

Por ejemplo, no es raro que descubra que necesitan los sistemas existentes tengan que recopilar y registrar tipos de datos adicionales para solucionar el problema y alcanzar los objetivos del proyecto. En este caso, puede ser conveniente buscar orígenes de datos externos o actualizar los sistemas para recopilar datos nuevos.

### <a name="artifacts"></a>Artefactos
Estos son los resultados de esta fase:

* [**Documento marco**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): se ofrece una plantilla estándar en la definición de estructura del proyecto de TDSP. Se trata de un documento dinámico que se actualiza a lo largo del proyecto a medida que se realizan nuevos descubrimientos y se modifican los requisitos empresariales. La clave consiste en realizar iteraciones de este documento e incorporarle la información oportuna según se avance a lo largo del proceso de descubrimiento. Es importante que el cliente y las demás partes interesadas se impliquen en la realización de cambios y que se les informe claramente sobre las razones que los motivan.  
* [**Orígenes de datos**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#raw-data-sources): se trata de la sección **orígenes de datos sin procesar** del informe de **definiciones de datos** que se encuentra en la carpeta **informe de datos** del proyecto TDSP. Especifica las ubicaciones originales y de destino para los datos sin procesar. En las fases posteriores, deberá rellenar más detalles, tales como los scripts para mover los datos al entorno de análisis.  
* [**Diccionarios de datos**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/DataDictionaries): este documento proporciona descripciones de los datos proporcionados por el cliente. Estas descripciones incluyen información sobre el esquema (tipos de datos, información sobre las reglas de validación, si hay) y los diagramas de relación de entidades, si están disponibles.


## <a name="2-data-acquisition-and-understanding"></a>2. Adquisición y comprensión de los datos

### <a name="goals"></a>Objetivos
* Un conjunto de datos limpio y de gran calidad cuyas relaciones con las variables de destino que se encuentran en el entorno de análisis apropiado se comprendan y esté listo para el modelado.
* Se ha desarrollado una arquitectura de la solución de la canalización de datos para actualizar y puntuar datos con regularidad.

### <a name="how-to-do-it"></a>Modo de hacerlo
En esta fase se abordan tres tareas principales:

* **Introducción de los datos** en el entorno de análisis de destino.
* **Exploración de los datos** para determinar si su calidad es suficiente para responder a la pregunta. 
* **Configuración de una canalización de datos** para puntuar los datos nuevos o que se actualizan con regularidad.

#### <a name="21-ingest-the-data"></a>2.1 Introducción de los datos
Configure el proceso para mover los datos desde las ubicaciones de origen a las ubicaciones de destino donde se llevarán a cabo las operaciones de análisis, como el entrenamiento y las predicciones. Para obtener detalles técnicos y opciones sobre cómo hacer esto con diversos servicios de datos de Azure, vea [Carga de datos en entornos de almacenamiento para el análisis](machine-learning-data-science-ingest-data.md). 

#### <a name="22-explore-the-data"></a>2.2 Exploración de los datos
Antes de entrenar los modelos, debe desarrollar una comprensión sólida de los datos. A menudo, los conjuntos de datos reales contienen ruido, les faltan datos o presentan un sinfín de discrepancias de otros tipos. Se pueden utilizar funciones de resumen y visualización de los datos para auditar su calidad y dar la información que se necesita para procesarlos y dejarlos preparados para el modelado. Normalmente, se trata de un proceso iterativo.

TDSP ofrece una utilidad automatizada denominada [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) que facilita la visualización de los datos y la elaboración de informes resumidos de los datos. Se recomienda empezar con IDEAR para explorar la información y contribuir a comprender los datos iniciales interactivamente, sin codificación, y, a continuación, escribir el código personalizado para visualizarlos y explorarlos. Para obtener orientación sobre cómo limpiar los datos, consulte [Tareas para preparar los datos para el aprendizaje automático mejorado](machine-learning-data-science-prepare-data.md).  

Cuando esté satisfecho con la calidad de los datos limpios, el paso siguiente consiste en comprender mejor los patrones inherentes a los datos que lo ayudarán a elegir y desarrollar un modelo de predicción adecuado para el objetivo. Busque pruebas que indiquen en qué medida están conectados los datos al objetivo y si hay suficientes para avanzar a los siguientes pasos de modelado. Como hemos indicado, normalmente, se trata de un proceso iterativo. Es posible que deba buscar otros orígenes de datos con información más precisa o pertinente con el fin de alimentar el conjunto de datos inicialmente identificado en la fase anterior.  

#### <a name="23-set-up-a-data-pipeline"></a>2.3 Configuración de una canalización de datos
Además de la introducción y limpieza iniciales de los datos, suele ser preciso configurar un proceso para puntuar los datos nuevos o actualizarlos con regularidad durante el proceso de aprendizaje continuo. Para ello, puede configurar una canalización de datos o un flujo de trabajo. A continuación, se muestra un [ejemplo](machine-learning-data-science-move-sql-azure-adf.md) de cómo configurar una canalización con [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

En esta fase se desarrolla a una arquitectura de la solución de la canalización de datos. La canalización se desarrolla también paralelamente a las siguientes fases del proyecto de ciencia de datos. La canalización puede realizarse por lotes, mediante una transmisión por secuencias en tiempo real o bien utilizando una combinación de ambos métodos, en función de las necesidades empresariales y de las limitaciones de los sistemas existentes en los que se integre esta solución. 

### <a name="artifacts"></a>Artefactos
Estos son los resultados de esta fase:

* [**Informe de la calidad de los datos**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md): este informe contiene resúmenes de los datos, las relaciones entre cada atributo y objetivo, la clasificación de las variables, etc. La herramienta [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) que se incluye como parte del TDSP permite generar rápidamente este informe sobre cualquier conjunto de datos tabular, como un archivo CSV o una tabla relacional. 
* **Arquitectura de la solución**: puede ser un diagrama o una descripción de la canalización de datos utilizada para llevar a cabo la tarea de puntuación o las predicciones con los nuevos datos una vez que se ha creado un modelo. También contiene la canalización para volver a entrenar el modelo basándose en los nuevos datos. El documento se almacena en el directorio [Project](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) cuando se usa la plantilla de estructura de directorios de TDSP.
* **Decisión de punto de comprobación**: antes de comenzar con el proceso completo de diseño de características y con la compilación del modelo, puede volver a evaluar el proyecto para determinar si el valor que está previsto que aporte es suficiente para seguir adelante con él. Por ejemplo, podría estar preparado para continuar, requerir más datos o abandonar el proyecto si no existen datos que respondan a la pregunta.


## <a name="3-modeling"></a>3. Modelado

### <a name="goals"></a>Objetivos
* Características óptimas de los datos para el modelo de aprendizaje automático.
* Un modelo de aprendizaje automático informativo que predice el objetivo con la máxima precisión.
* Un modelo de aprendizaje automático que es adecuado para entornos de producción.

### <a name="how-to-do-it"></a>Modo de hacerlo
En esta fase se abordan tres tareas principales:

* **Diseño de características**: cree las características de datos a partir de los datos sin procesar para facilitar el entrenamiento del modelo.
* **Entrenamiento del modelo**: busque el modelo que responda a la pregunta con la máxima precisión comparando sus métricas de éxito.
* Determine si el modelo es **adecuado para su uso en producción**.

#### <a name="31-feature-engineering"></a>3.1 Diseño de características
El diseño de características consiste en incluir, agregar y transformar variables sin procesar para crear las características que se utilizan en el análisis. Si desea conocer con detalle los factores en que se basa un modelo, debe entender cómo se relacionan entre sí las características y cómo deben utilizarlas los algoritmos de aprendizaje automático. Este paso requiere una combinación creativa de experiencia en la materia y conocimientos obtenidos durante el paso de exploración de datos. Se trata hallar el equilibrio: por un lado, buscar e incluir variables informativas y, por otro, evitar que se utilicen demasiadas variables no relacionadas. Las variables informativas mejoran los resultados; las variables no relacionadas introducen ruido innecesario en el modelo. También debe generar estas características para los nuevos datos obtenidos durante la puntuación. Así pues, la generación de estas características solamente puede depender de los datos que está disponibles en el momento de la puntuación. Para obtener orientación técnica sobre diseño de características cuando se usan diversas tecnologías de datos de Azure, consulte [Feature engineering in the Data Science Process](machine-learning-data-science-create-features.md) (Diseño de características en el proceso de ciencia de datos). 

#### <a name="32-model-training"></a>3.2 Entrenamiento del modelo
Según el tipo de la pregunta que intenta responder, existen muchos algoritmos de modelado disponibles. Para obtener orientación sobre cómo elegir el algoritmo correcto, consulte el artículo sobre [cómo elegir algoritmos para Microsoft Azure Machine Learning](machine-learning-algorithm-choice.md). Aunque este artículo se escribió para Azure Machine Learning, la orientación resulta útil para otros proyectos de aprendizaje automático. 

El proceso de entrenamiento del modelo incluye los pasos siguientes: 

* **Dividir los datos de entrada** aleatoriamente para el modelado en un conjunto de datos de aprendizaje y un conjunto de datos de prueba.
* **Compilar los modelos** mediante el conjunto de datos de aprendizaje.
* **Evaluar** (conjunto de datos de aprendizaje y de prueba) una serie de algoritmos de aprendizaje automático paralelos junto con los diversos parámetros de ajuste asociados (lo que se denomina barrido de parámetros) que están orientados a responder a la pregunta de interés con los datos actuales.
* **Determinar la "mejor" solución** para responder a la pregunta mediante la comparación de las métricas de éxito entre los métodos alternativos.

> [!NOTE]
> **Evitar la pérdida de datos**: la pérdida de datos puede deberse a la inclusión de datos desde fuera del conjunto de datos de aprendizaje que permite que un modelo o algoritmo de aprendizaje automático haga predicciones demasiado buenas para ser realistas. La pérdida de datos es uno de los motivos por los que los científicos de datos se preocupan cuando obtienen resultados predictivos que parecen demasiado buenos para ser ciertos. Estas dependencias pueden ser difíciles de detectar. Para evitar fugas, a menudo es preciso efectuar iteraciones entre la generación del conjunto de datos de análisis, la creación del modelo y la evaluación de la precisión. 
> 
> 

Ofrecemos una [herramienta de modelado e informes automatizados](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) con TDSP que permite llevar a cabo varios algoritmos y barridos de parámetros para generar un modelo de base de referencia. También genera un informe de modelado de base de referencia que resume el rendimiento de cada combinación de modelo y parámetros, incluida la importancia de las variables. Este proceso también es iterativo, porque así permite avanzar en el diseño de características. 

### <a name="artifacts"></a>Artefactos
Los artefactos que se producen en esta fase incluyen, entre otros:

* [**Conjuntos de características**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets): las características desarrolladas para el modelado se describen en la sección de **conjuntos de características** del informe **Definición de datos**. Contiene punteros al código para generar las características y la descripción de cómo se ha generado cada una de ellas.
* [**Informe del modelo**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): para cada modelo que se prueba, se genera un informe estándar basado en una plantilla que proporciona información detallada sobre cada experimento.
* **Decisión de punto de comprobación**: evalúe si el modelo funciona lo bastante bien para implementarlo en un sistema de producción. Algunas preguntas clave son:
  * ¿El modelo responde a la pregunta con la confianza suficiente considerando los datos de prueba? 
  * ¿Debe probar algún enfoque alternativo, como recopilar datos adicionales, rediseñar las características o experimentar con otros algoritmos?


## <a name="4-deployment"></a>4. Implementación

### <a name="goal"></a>Objetivo
* Los modelos con canalización de datos se implementan en un entorno de producción o similar para que el usuario final los acepte. 

### <a name="how-to-do-it"></a>Modo de hacerlo
La tarea principal que se aborda en esta fase es la siguiente:

* **Uso del modelo**: implemente el modelo y la canalización en un entorno de producción o semejante para el consumo de aplicaciones.

#### <a name="41-operationalize-a-model"></a>4.1 Uso del modelo
Cuando ya disponga de un conjunto de modelos que funcionan bien, los puede hacer operativos para que los consuman otras aplicaciones. Dependiendo de los requisitos empresariales, se realizan predicciones en tiempo real o por lotes. Para hacerlos operativos, los modelos tienen que exponerse con una interfaz API abierta que se pueda usar fácilmente desde diversas aplicaciones, tales como sitios web en línea, hojas de cálculo, paneles o aplicaciones de línea de negocio o de back-end. Para obtener información sobre cómo crear e implementar un servicio web de Azure Machine Learning, consulte [Implementación de un servicio web Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md). También es un procedimiento recomendado integrar la telemetría y la supervisión en el modelo de producción y la canalización de datos, con el fin de ayudar a generar informes de estado del sistema y solucionas problemas posteriormente.  

### <a name="artifacts"></a>Artefactos
* Panel de estado de estado del sistema y métricas clave.
* Informe de modelado final con detalles de implementación.
* Documento de arquitectura de la solución final.

## <a name="5-customer-acceptance"></a>5. Aceptación del cliente

### <a name="goal"></a>Objetivo
* **Finalización de los resultados del proyecto**: confirme que la canalización, el modelo y su implementación en un entorno de producción cumplen los objetivos del cliente.

### <a name="how-to-do-it"></a>Modo de hacerlo
En esta fase se abordan dos tareas principales:

* **Validación del sistema**: confirme que el modelo implementado y la canalización cumplen las necesidades del cliente.
* **Entrega del proyecto**: a la entidad que va a utilizar el sistema en el entorno de producción.

El cliente debe validar que el sistema satisface sus necesidades empresariales y responde a las preguntas con una precisión aceptable para implementarlo en el entorno de producción y usarlo con la aplicación cliente. Se finaliza y revisa toda la documentación. Se lleva a cabo la entrega del proyecto a la entidad responsable de las operaciones. Esta entidad podría ser, por ejemplo, un equipo de ciencia de datos de clientes o de TI o un agente del cliente responsable del funcionamiento del sistema en producción. 

### <a name="artifacts"></a>Artefactos
El artefacto principal que se genera en esta fase final es el **informe de salida del proyecto para el cliente**. Se trata del informe técnico que contiene todos los detalles pertinentes del proyecto que pueden resultar útiles para conocer y utilizar el sistema. TDSP incluye una plantilla de [informe de salida](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) que se puede utilizar tal cual o personalizarse de acuerdo con las necesidades concretas del cliente. 

## <a name="summary"></a>Resumen
El [ciclo de vida del proceso de ciencia de datos en equipo](http://aka.ms/datascienceprocess) se modela como una secuencia de pasos repetidos que le orientan respecto a las tareas necesarias para usar modelos predictivos. Estos modelos se pueden implementar en un entorno de producción y utilizarse para crear aplicaciones inteligentes. El objetivo del ciclo de vida del proceso consiste en hacer avanzar un proyecto de ciencia de datos hacia un punto final de interacción claro. Si bien es cierto que la ciencia de datos es un ejercicio de investigación y detección, la posibilidad de comunicar estas tareas al equipo y los clientes mediante un conjunto bien definido de artefactos que usan plantillas estándar puede ayudar a evitar malentendidos y a aumentar la probabilidad de llevar a término correctamente los proyectos de ciencia de datos complejos.

## <a name="next-steps"></a>Pasos siguientes
También se proporcionan tutoriales completos que muestran todos los pasos del proceso en **escenarios concretos** . Se enumeran y enlazan con descripciones en miniatura en el tema [Tutoriales del proceso de ciencia de datos en equipos](data-science-process-walkthroughs.md).


