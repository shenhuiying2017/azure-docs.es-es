---
title: "Preguntas más frecuentes (P+F) sobre Azure Machine Learning | Microsoft Docs"
description: "Introducción a Azure Machine Learning: preguntas más frecuentes sobre facturación, capacidades y limitaciones de un servicio de nube para un modelado de predicción optimizado."
keywords: "introducción de aprendizaje automático, modelo predictivo, qué es el aprendizaje automático"
services: machine-learning
documentationcenter: 
author: garyericson
manager: paulettm
editor: cgronlun
ms.assetid: a4a32a06-dbed-4727-a857-c10da774ce66
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/02/2017
ms.author: garye
ms.openlocfilehash: b162d7534eff0d1f79808fc86bacf92dbfc2f84a
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2017
---
# <a name="azure-machine-learning-frequently-asked-questions-billing-capabilities-limitations-and-support"></a>Preguntas más frecuentes sobre Azure Machine Learning: facturación, funcionalidades, limitaciones y soporte técnico
Estas son algunas de las preguntas más frecuentes (P+F) y las respuestas correspondientes sobre Azure Machine Learning, un servicio en la nube para el desarrollo de modelos predictivos y la aplicación de soluciones mediante servicios web. Estas preguntas más frecuentes incluyen cuestiones sobre cómo usar el servicio; por ejemplo, el modelo de facturación, las funcionalidades, las limitaciones y el soporte técnico.

**¿Tiene una pregunta que no se encuentra aquí?**

Azure Machine Learning cuenta con un foro en MSDN, donde los miembros de la comunidad de las ciencias de los datos pueden plantear preguntas sobre Azure Machine Learning. El equipo de Azure Machine Learning supervisa el foro. Vaya al [foro de Azure Machine Learning](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) para buscar respuestas o publicar una pregunta nueva.

## <a name="general-questions"></a>Preguntas generales
**¿Qué es Azure Machine Learning?**

Azure Machine Learning es un servicio completamente administrado que sirve para crear, probar, utilizar y administrar soluciones de análisis predictivo en la nube. Con solo un explorador, puede iniciar sesión, cargar datos y comenzar rápidamente experimentos de aprendizaje automático. Un modelo predictivo basado en la funcionalidad de arrastrar y soltar, una gran paleta de módulos y una biblioteca de plantillas de inicio convierten las tareas de aprendizaje automático comunes en algo sencillo y rápido. Para obtener más información, consulte [Descripción general del servicio de Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/). Para ver una introducción al aprendizaje automático donde se explica terminología y conceptos clave, consulte [Introducción a Azure Machine Learning](what-is-machine-learning.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

**¿Qué es Machine Learning Studio?**

Machine Learning Studio es un entorno de trabajo al que se puede acceder mediante un explorador web. Machine Learning Studio hospeda una gran paleta de módulos en una interfaz de composición visual que ayuda a crear un flujo de trabajo completo de ciencia de los datos en forma de experimento.

Para más información sobre Machine Learning Studio, consulte [¿Qué es Machine Learning Studio?](what-is-ml-studio.md)

**¿Qué es el servicio API de Machine Learning?**

El servicio API de Machine Learning le permite implementar modelos predictivos, igual que los que se crean en Machine Learning Studio, como servicios escalables y tolerantes a errores. Los servicios web creados con el servicio API de Machine Learning son varias API de REST que proporcionan una interfaz para la comunicación entre las aplicaciones externas y los modelos de análisis predictivo.

Para más información, consulte [Consumo de servicios web de Azure Machine Learning](consume-web-services.md).

**¿Dónde se muestran mis servicios web clásicos? ¿Dónde se muestran mis nuevos servicios web basados Azure Resource Manager?**

Los servicios web creados con el modelo de implementación clásica y los servicios web creados mediante el nuevo modelo de implementación de Azure Resource Manager se muestran en el portal de [servicios web Microsoft Azure Machine Learning](https://services.azureml.net/).

Los servicios web clásicos también se muestran en [Machine Learning Studio](http://studio.azureml.net), en la pestaña de **servicios web**.

## <a name="azure-machine-learning-questions"></a>Preguntas sobre Azure Machine Learning
**¿Qué son los servicios web Azure Machine Learning?**

Los servicios web Machine Learning proporcionan una interfaz entre una aplicación y un modelo de puntuación de flujo de trabajo de Machine Learning. Una aplicación externa puede usar Azure Machine Learning para comunicarse con un modelo de puntuación de flujo de trabajo de Machine Learning en tiempo real. Una llamada a un servicio web Machine Learning devuelve resultados de predicción a una aplicación externa. Para realizar una llamada a un servicio web Machine Learning, es necesario pasar una clave de API que se creó al implementar el servicio web. Un servicio web Machine Learning se basa en REST, una conocida opción de arquitectura para proyectos de programación web.

Azure Machine Learning tiene dos tipos de servicios web:

* Servicio de solicitud y respuesta (RRS): servicio de baja latencia y muy escalable que proporciona una interfaz con los modelos sin estado creados e implementados mediante Machine Learning Studio.
* Servicio de ejecución por lotes (BES): servicio asincrónico que puntúa un lote de registros de datos.

Hay varias maneras de usar la API de REST y acceder al servicio web. Por ejemplo, puede escribir una aplicación en C#, R o Python con el código de ejemplo que se genera automáticamente cuando se implementa el servicio web.

El código de ejemplo está disponible en:
- La página de consumo del servicio web en el portal de servicios web Azure Machine Learning.
- La página de ayuda de la API en el panel de Machine Learning Studio.

También puede usar el libro de Microsoft Excel de ejemplo que se crea automáticamente y que está disponible en el panel del servicio web en Machine Learning Studio.

**¿Cuáles son las actualizaciones principales de Azure Machine Learning?**

Puede consultar las actualizaciones más recientes en [Novedades de Azure Machine Learning](whats-new.md).

## <a name="machine-learning-studio-questions"></a>Preguntas sobre Machine Learning Studio
### <a name="import-and-export-data-for-machine-learning"></a>Importación y exportación de datos en Machine Learning
**¿Qué orígenes de datos admite Machine Learning?**

Puede descargar datos en un experimento de Machine Learning Studio de tres maneras:

- Cargar un archivo local como un conjunto de datos
- Usar un módulo para importar datos de servicios de datos en la nube
- Importar un conjunto de datos guardado de otro experimento

Para más información acerca de los formatos de archivo compatibles, consulte [Importación de datos de entrenamiento en Machine Learning Studio desde varios orígenes de datos](import-data.md).

#### <a id="ModuleLimit"></a>¿Cómo de grande puede ser el conjunto de datos para mis módulos?
Los módulos en Machine Learning Studio admiten conjuntos de datos de hasta 10 GB de datos numéricos densos para casos de uso comunes. Si un módulo ocupa más de una entrada, el valor de 10 GB es el total de todos los tamaños de entrada. También puede realizar el muestreo de conjuntos de datos grandes mediante consultas de Hive o Azure SQL Database, o usar el procesamiento previo de aprendizaje por recuentos antes de la ingesta.  

Los siguientes tipos de datos se pueden expandir en conjuntos de datos grandes durante la normalización de características y están limitados a menos de 10 GB:

* Dispersos
* Categorías
* Cadenas
* Datos binarios

Los siguientes módulos están limitados a conjuntos de datos de menos de 10 GB:

* Módulos de recomendación.
* Módulo Synthetic Minority Oversampling Technique (SMOTE)
* Módulos de script: R, Python y SQL
* Módulos donde el tamaño de los datos de salida puede ser mayor que el tamaño de los datos de entrada, como en la aplicación de hash de característica o unión.
* Validación cruzada, ajuste de los hiperparámetros del modelo, regresión ordinal y multiclase uno contra todos, cuando el número de iteraciones sea muy grande.

#### <a id="UploadLimit"></a>¿Cuáles son los límites de carga de datos?
En el caso de conjuntos de datos que tengan más de dos gigas, cargue datos en Azure Storage o en Azure SQL Database, o use Azure HDInsight, en lugar de cargarlos directamente desde el archivo local.

**¿Se pueden leer datos de Amazon S3?**

Si tiene una pequeña cantidad de datos y quiere exponerlos mediante una dirección URL HTTP, puede usar el módulo [Import Data][import-data]. Si la cantidad de datos es mayor, transfiéralos a Azure Storage y luego use el módulo [Import Data][import-data] para incluirlos en el experimento.
<!--

<SEE CLOUD DS PROCESS>
-->

**¿Hay una capacidad integrada para usar una entrada de imagen?**

Puede obtener información sobre la funcionalidad de entrada de imágenes en la referencia de [Import Images][image-reader].

### <a name="modules"></a>Módulos
**El algoritmo, el origen de datos, el formato de datos o la operación de transformación de datos que busco no están en Azure Machine Learning Studio. ¿Qué opciones tengo?**

Puede ir al [foro de comentarios de los usuarios](http://go.microsoft.com/fwlink/?LinkId=404231) para ver solicitudes de características cuyo seguimiento estamos realizando. Agregue su voto a esta solicitud si la funcionalidad que busca ya se ha solicitado. Si la funcionalidad que busca no existe, cree una nueva solicitud. El estado de su solicitud puede verlo también en este foro. Seguimos muy de cerca esta lista y actualizamos el estado de la disponibilidad de las características con frecuencia. Además, puede usar la compatibilidad integrada con R y Python para crear transformaciones personalizadas cuando sea necesario.

**¿Puedo usar mi código existente en Machine Learning Studio?**

Sí, puede usar su código R o Python existente en Machine Learning Studio, ejecutarlo en el mismo experimento con los aprendices de Azure Machine Learning e implementar la solución como un servicio web a través de Azure Machine Learning. Para más información, consulte [Extender el experimento con R](extend-your-experiment-with-r.md) y [Ejecución de scripts de Machine Learning de Python en Azure Machine Learning Studio](execute-python-scripts.md).

**¿Se puede usar algo como [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) para definir un modelo?**

No, no se admite el lenguaje de marcado de modelos de predicción (PMML). Puede usar código personalizado de R y Python para definir un módulo.

**¿Cuántos módulos puedo ejecutar en paralelo en mi experimento?**  

En un experimento se pueden ejecutar hasta cuatro módulos en paralelo.

### <a name="data-processing"></a>Procesamiento de datos
**¿Se pueden visualizar los datos (más allá de visualizaciones R) interactivamente en el experimento?**

Haga clic en la salida de un módulo para visualizar los datos y obtener estadísticas.

**Cuando se obtiene una vista previa de los resultados o de los datos en un explorador, el número de filas y columnas se limita. ¿Por qué?**

Dada la posibilidad de que se envíen grandes cantidades de datos a un explorador, el tamaño de los datos se limita para impedir que Machine Learning Studio funcione más lento. Para visualizar los datos o los resultados en su totalidad, es mejor descargar los datos y usar Excel u otra herramienta.

### <a name="algorithms"></a>Algoritmos
**¿Qué algoritmos existentes se admiten en Machine Learning Studio?**

Machine Learning Studio ofrece los algoritmos mas innovadores, como árboles de decisión incrementados escalables, sistemas de recomendaciones bayesianas, redes neuronales profundas y selvas de decisión, que se han desarrollado en Microsoft Research. También se incluyen paquetes de aprendizaje automático escalables de código abierto, como Vowpal Wabbit. Machine Learning Studio admite algoritmos de aprendizaje automático para clasificación, regresión y agrupación en clústeres y binarias y multiclase. Consulte la lista completa de [módulos de Machine Learning][machine-learning-modules].

**¿Se sugiere automáticamente el algoritmo de Machine Learning adecuado para utilizarlo con mis datos?**

No. Sin embargo, Machine Learning Studio ofrece varias maneras de comparar los resultados de cada algoritmo para determinar el más apropiado para su problema.

**¿Hay instrucciones sobre la elección de un algoritmo en lugar de otro de los proporcionados?**

Consulte [Elección de un algoritmo](algorithm-choice.md).

**¿Se proporcionan algoritmos escritos en R o Python?**

No. Estos algoritmos principalmente se escriben en lenguajes compilados para que ofrezcan un mayor rendimiento.

**¿Se proporcionan algunos detalles de los algoritmos?**

La documentación proporciona alguna información acerca de los algoritmos, y se describen los parámetros para optimizar el algoritmo para su uso.  

**¿Se admite el aprendizaje en línea?**

No. Actualmente solo se admite el reentrenamiento mediante programación.

**¿Puedo visualizar las capas de un modelo de red neuronal con el módulo integrado?**

No.

**¿Puedo crear mis propios módulos en C# o algún otro lenguaje?**

Actualmente, solo puede usar R para crear nuevos módulos personalizados.

### <a name="r-module"></a>Módulo R
**¿Qué paquetes de R están disponibles en Machine Learning Studio?**

En la actualidad, Machine Learning Studio admite más de 400 paquetes de CRAN R y esta es la [lista actual](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) de todos los paquetes incluidos. Consulte también [Extender el experimento con R](extend-your-experiment-with-r.md) para aprender a recuperar esta lista. Si el paquete que desea no está en la lista, especifique el nombre del paquete en el [foro de comentarios de los usuarios](http://go.microsoft.com/fwlink/?LinkId=404231).

**¿Es posible crear un módulo personalizado de R?**

Sí. Para más información, consulte [Creación de módulos R personalizados en Azure Machine Learning](custom-r-modules.md).

**¿Hay un entorno de REPL para R?**

No, no hay ningún entorno Read-Eval-Print-Loop (REPL) para R en Studio.

### <a name="python-module"></a>Módulo de Python
**¿Es posible crear un módulo personalizado de Python?**

Aunque en estos momentos no es posible, puede usar uno o varios módulos [Execute Python Script][python] para obtener el mismo resultado.

**¿Hay un entorno de REPL para Python?**

Puede usar Jupyter Notebooks en Machine Learning Studio. Para más información, consulte [Introducing Jupyter Notebooks in Azure Machine Learning Studio](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx)(Introducción a Jupyter Notebooks en Estudio de aprendizaje automático de Azure).

## <a name="web-service"></a>Servicio web
### <a name="retrain"></a>Reentrenar el modelo
**¿Cómo reentreno los modelos de Azure Machine Learning mediante programación?**

Use las API de reentrenamiento. Para obtener más información, consulte [Volver a entrenar modelos de Machine Learning mediante programación](retrain-models-programmatically.md). También puede encontrar código de ejemplo en [Microsoft Azure Machine Learning Retraining Demo](https://azuremlretrain.codeplex.com/)(Demostración de reciclaje de Microsoft Azure Machine Learning).

### <a name="create"></a>Crear
**¿Puedo implementar el modelo de forma local o en una aplicación sin conexión a Internet?**

No.

**¿Cabe esperar una latencia de línea de base para todos los servicios web?**

Consulte [Límites de la suscripción de Azure](../../azure-subscription-service-limits.md).

### <a name="use"></a>Uso
**¿Cuándo podría ejecutar mi modelo predictivo como servicio de ejecución de lotes en lugar de como servicio web de solicitud/respuesta?**

El servicio de solicitud-respuesta (RRS) es un servicio web de alta escala y baja latencia que se usa para proporcionar una interfaz con los modelos sin estado creados e implementados desde el entorno de experimentación. El servicio de ejecución de lotes (BES) es un servicio para la puntuación asincrónica de lotes de registros de datos. La entrada de BES es similar a la entrada de datos que se emplea en RRS. La diferencia principal radica en que BES lee un bloque de registros de varios orígenes, como Azure Blob Storage, Azure Table Storage, Azure SQL Database, HDInsight (consultas de Hive) y orígenes de HTTP. Para más información, consulte [Consumo de servicios web de Azure Machine Learning](consume-web-services.md).

**¿Cómo se actualiza el modelo para la producción del servicio implementado?**

Para actualizar un modelo predictivo para un servicio ya implementado, modifique y vuelva a ejecutar el experimento usado para crear y guardar el modelo entrenado. Una vez que disponga de una nueva versión del modelo entrenado, Machine Learning Studio le preguntará si desea actualizar el servicio web. Para más información sobre cómo actualizar un servicio web implementado, consulte [Implementar un servicio web Machine Learning](publish-a-machine-learning-web-service.md).

También puede usar las API de reciclaje.
Para obtener más información, consulte [Volver a entrenar modelos de Machine Learning mediante programación](retrain-models-programmatically.md). También puede encontrar código de ejemplo en [Microsoft Azure Machine Learning Retraining Demo](https://azuremlretrain.codeplex.com/)(Demostración de reciclaje de Microsoft Azure Machine Learning).

**¿Cómo se supervisa el servicio web implementado en producción?**

Tras implementar un modelo predictivo, se puede supervisar desde el portal de servicios web Azure Machine Learning. Cada servicio implementado cuenta con su propio panel, donde se puede ver la información de supervisión de ese servicio. Para más información sobre cómo administrar los servicios web implementados, consulte [Administración de un servicio web mediante el portal de servicios web Azure Machine Learning](manage-new-webservice.md) y [Administración de un área de trabajo de Azure Machine Learning](manage-workspace.md).

**¿Hay algún lugar donde pueda ver la salida de mi RRS/BES?**

Para RRS, en la respuesta del servicio web normalmente es donde se verá el resultado. También puede escribirlo en Azure Blob Storage. Para BES, el resultado se escribe en un blob de manera predeterminada. También puede escribir el resultado en una base de datos o en una tabla con el módulo [Export Data][export-data].

**¿Solamente puedo crear servicios web a partir de modelos creados en Machine Learning Studio?**

No. También puede crear servicios web directamente desde Jupyter Notebooks y RStudio.

**¿Dónde puedo encontrar información sobre los códigos de error?**

Consulte [Códigos de error del módulo de Machine Learning](https://msdn.microsoft.com/library/azure/dn905910.aspx) para ver una lista de códigos de error y sus descripciones.

## <a name="scalability"></a>Escalabilidad
**¿Qué es la escalabilidad del servicio web?**

En la actualidad, el punto de conexión predeterminado se ha aprovisionado con 20 solicitudes simultáneas de RRS por punto de conexión. Como se describe en [Escalado de servicios web](scaling-webservice.md), este número se puede escalar hasta alcanzar las 200 solicitudes simultáneas por punto de conexión y escalar cada servicio web hasta los 10 000 puntos de conexión por servicio web. En BES, cada punto de conexión permite el procesamiento de 40 solicitudes a la vez y el resto de solicitudes adicionales que superan este número se ponen en cola. Dichas solicitudes se ejecutan automáticamente a medida que avanza la cola.

**¿Los trabajos de R se reparten entre nodos?**

Nº  

**¿Cuántos datos se pueden usar para el entrenamiento?**

Los módulos en Machine Learning Studio admiten conjuntos de datos de hasta 10 GB de datos numéricos densos para casos de uso comunes. Si un módulo ocupa más de una entrada, el tamaño total de todas las entradas juntas es 10 GB. También puede realizar un muestreo de conjuntos de datos mayores mediante consultas de Hive o Azure SQL Database, o bien mediante el procesamiento previo con los módulos de [aprendizaje con recuentos][counts] antes de la ingesta.  

Los siguientes tipos de datos se pueden expandir en conjuntos de datos grandes durante la normalización de características y están limitados a menos de 10 GB:

* Dispersos
* Categorías
* Cadenas
* Datos binarios

Los siguientes módulos están limitados a conjuntos de datos de menos de 10 GB:

* Módulos de recomendación.
* Módulo Synthetic Minority Oversampling Technique (SMOTE)
* Módulos de script: R, Python y SQL
* Módulos donde el tamaño de los datos de salida puede ser mayor que el tamaño de los datos de entrada, como en la aplicación de hash de característica o unión.
* Validación cruzada, ajuste de los hiperparámetros del modelo, regresión ordinal y multiclase uno contra todos, cuando el número de iteraciones sea muy grande.

En el caso de conjuntos de datos que tengan más que unos cuantos gigas, cargue datos en Azure Storage o en Azure SQL Database, o use HDInsight, en lugar de cargarlos directamente desde el archivo local.

**¿Hay alguna limitación en el tamaño de los vectores?**

Las filas y las columnas están limitadas a la limitación .NET de Máx. int.: 2,147,483,647.

**¿Se puede ajustar el tamaño de la máquina virtual que ejecuta el servicio web?**

No.  

## <a name="security-and-availability"></a>Seguridad y disponibilidad
**¿Quién puede acceder de forma predeterminada al punto de conexión http del servicio web? ¿Cómo se restringe el acceso al punto de conexión?**

Cuando se implementa un servicio web, se crea un punto de conexión predeterminado para ese servicio. El punto de conexión predeterminado puede llamarse mediante su clave de API. Se pueden agregar puntos de conexión adicionales con sus propias claves desde el portal de servicios web o mediante programación con las API de Administración del servicio web. Se necesitan claves de acceso para realizar llamadas al servicio web. Para más información, consulte [Consumo de servicios web de Azure Machine Learning](consume-web-services.md).

**¿Qué sucede si no se encuentra mi cuenta de Azure Storage?**

Machine Learning Studio depende de la cuenta de Azure Storage suministrada para guardar datos intermedios al ejecutar el flujo de trabajo. Esta cuenta de almacenamiento se proporciona a Machine Learning Studio en el momento de crear un área de trabajo. Una vez que se crea el área de trabajo, si se elimina la cuenta de almacenamiento y ya no puede encontrarse, el área de trabajo dejará de funcionar y todos los experimentos que haya en ella darán error.

Si ha eliminado accidentalmente la cuenta, vuelva a crearla con el mismo nombre y en la misma región que la eliminada. Después, vuelva a sincronizar la clave de acceso.

**¿Qué sucede si la clave de acceso de mi cuenta de almacenamiento no está sincronizada?**

Machine Learning Studio usa una cuenta de Azure Storage suministrada por el usuario para guardar los datos intermedios al ejecutar el flujo de trabajo. Esta cuenta se proporciona a Machine Learning Studio en el momento de crear un área de trabajo y las claves de acceso se asocian a dicha área de trabajo. Si se cambian las claves de acceso después de haber creado el área de trabajo, este no podrá volver a acceder a la cuenta de almacenamiento. Dejará de funcionar y se producirá un error en todos los experimentos que haya en esa área de trabajo.

Si ha cambiado las claves de acceso de la cuenta de almacenamiento, vuelva a sincronizarlas en el área de trabajo mediante Azure Portal.  

## <a name="support-and-training"></a>Soporte técnico y entrenamiento
**¿Dónde puedo recibir entrenamiento para Azure Machine Learning?**

El [Centro de documentación de Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) hospeda tutoriales en vídeo y guías de procedimientos. Estas guías paso a paso proporcionan una introducción a los servicios y explican el ciclo de vida de ciencia de datos que consiste en importar los datos, limpiarlos, construir modelos predictivos e implementarlos en producción con Azure Machine Learning.

Agregamos continuamente nuevo material al Centro de Machine Learning. Puede solicitar material de aprendizaje adicional sobre el Centro de Machine Learning en el [foro de comentarios de los usuarios](https://windowsazure.uservoice.com/forums/257792-machine-learning).

También puede buscar cursos en [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning).

**¿Dónde puedo recibir soporte técnico para Azure Machine Learning?**

Para recibir soporte técnico sobre Azure Machine Learning, vaya a la página del [servicio de soporte técnico de Azure](/support/options/) y seleccione **Machine Learning**.

Azure Machine Learning cuenta también con un foro de la comunidad en MSDN, donde puede plantear preguntas sobre el tema. El equipo de Azure Machine Learning supervisa el foro. Vaya al [foro de Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).

## <a name="billing-questions"></a>Preguntas sobre facturación
**¿Cómo funciona la facturación de Machine Learning?**

Azure Machine Learning tiene dos componentes: Machine Learning Studio y servicios web Machine Learning.

Puede usar el nivel de facturación gratuito mientras prueba Machine Learning Studio. El nivel Gratis también le permite implementar un servicio web clásico que tiene una capacidad limitada.

Si decide que Azure Machine Learning se ajusta a sus necesidades, puede suscribirse al nivel Estándar. Para ello, debe tener una suscripción a Microsoft Azure.

En el nivel Estándar se factura mensualmente un importe por cada área de trabajo que se define en Machine Learning Studio. Si ejecuta un experimento en Estudio de aprendizaje automático, se facturarán los recursos de proceso. Si implementa un servicio web clásico, las transacciones y las horas de proceso se facturarán mediante un plan de pago por uso.

En los nuevos servicios web (basados en Resource Manager) se han incorporado planes de facturación que permiten costos más predecibles. Los precios por niveles ofrecen descuentos a los clientes que necesitan una gran cantidad de capacidad.

Al crear un plan, se compromete a pagar un costo fijo que incluye varias horas de proceso y transacciones de API. Si necesita incluir más cantidades, puede agregar otras instancias al plan. Si necesita un volumen de cantidades mucho mayor, puede elegir un plan superior que incluya muchas más cantidades, ya que el porcentaje de descuento resulta más ventajoso.

Una vez que se agotan las cantidades incluidas en las instancias existentes, se carga una cuota de uso por encima del límite correspondiente al nivel del plan de facturación.

> [!NOTE]
Las cantidades incluidas se reasignan cada 30 días y las cantidades incluidas no usadas no se transfieren al período siguiente.

Para más información sobre los precios y la facturación, consulte los [precios de Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

**¿Dispone Machine Learning de una evaluación gratuita?**

 Azure Machine Learning dispone de una opción de suscripción gratuita que se explica en [Precios de Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/). Machine Learning Studio cuenta con una evaluación gratuita de ocho horas que está disponible al iniciar sesión en [Machine Learning Studio](https://studio.azureml.net/?selectAccess=true&o=2).

 Además, al registrarse para obtener una evaluación gratuita de Azure, puede probar cualquier servicio de Azure durante un mes. Para más información sobre la prueba gratuita de Azure, visite [Preguntas más frecuentes sobre la cuenta gratuita de Azure](https://azure.microsoft.com/pricing/free-trial-faq/).

**¿Qué es una transacción?**

Una transacción representa una llamada API a la que responde Azure Machine Learning. Las transacciones de las llamadas del Servicio de solicitud-respuesta (RRS) y del Servicio de ejecución por lotes (BES) se computan y se facturan de acuerdo con el plan de facturación.

**¿Puedo usar las cantidades de transacciones incluidas con transacciones de RRS y BES?**

Sí. Las transacciones de RRS y BES se computan y se facturan en función del plan de facturación.

**¿Qué es una hora de proceso de API?**

Una hora de proceso de API es la unidad de facturación relativa al tiempo que tardan en ejecutarse las llamadas de API mediante recursos de proceso de Machine Learning. Todas las llamadas computan a la hora de facturar.

**¿Cuánto tarda una llamada API de producción típica?**

Los tiempos de llamada a las API de producción pueden variar considerablemente; en general, oscilan desde cientos de milisegundos hasta unos cuantos segundos. En el caso de algunas llamadas de API, pueden ser necesarios minutos según la complejidad del procesamiento de datos y el modelo de aprendizaje automático. La mejor manera de estimar los tiempos de las llamadas API de producción es hacer pruebas comparativas con un modelo del servicio Machine Learning.

**¿Qué es una hora de proceso de Studio?**

Una hora de proceso de Studio es la unidad de facturación relativa al tiempo agregado que los experimentos usan recursos de proceso en Studio.

**¿Qué significa en los nuevos servicios web (basados en Azure Resource Manager) el nivel de desarrollo/pruebas?**

Los servicios web basados en Resource Manager proporcionan varios niveles que puede usar para aprovisionar su plan de facturación. El plan de tarifa de desarrollo/pruebas proporciona cantidades incluidas limitadas que le permiten probar el experimento como un servicio web sin incurrir en costos. Tendrá la oportunidad de ver cómo funciona.

**¿Existen otros cargos de almacenamiento?**

El nivel Gratis de Machine Learning no requiere ni permite otro almacenamiento. El nivel Estándar de Machine Learning requiere que los usuarios tengan una cuenta de Almacenamiento de Azure. Azure Storage se [factura por separado](https://azure.microsoft.com/pricing/details/storage/).

**¿Admite Machine Learning alta disponibilidad?**

Sí. Para más información, consulte [Precios de Machine Learning](https://azure.microsoft.com/en-us/pricing/details/machine-learning/) y obtendrá una descripción del acuerdo de nivel de servicio (SLA).

**¿En qué tipo específico de recursos de proceso se van a ejecutar las llamadas API de producción?**

El servicio Machine Learning es un servicio multiinquilino. Los recursos de proceso reales que se usan en el back-end varían y están optimizados para aumentar el rendimiento y la previsibilidad.

### <a name="management-of-new-resource-manager-based-web-services"></a>Administración de nuevos servicios web (basados en Resource Manager)
**¿Qué ocurre si elimino mi plan?**

El plan se quita de la suscripción y se factura el uso prorrateado.

> [!NOTE]
No se puede eliminar un plan que use un servicio web. Para poder eliminarlos, debe asignar un nuevo plan al servicio web o eliminar dicho servicio web.

**¿Qué es una instancia del plan?**

Una instancia del plan es una unidad de cantidades incluidas que se pueden agregar al plan de facturación. Cuando selecciona un nivel de facturación para el plan, incluye una instancia. Si necesita incluir más cantidades, puede agregar al plan otras instancias del nivel de facturación seleccionado.

**¿Cuántas instancias del plan puedo agregar?**

Puede tener una instancia del plan de tarifa de desarrollo/pruebas en una suscripción.

En los niveles Estándar S1, Estándar S2 y Estándar S3, puede agregar tantas como sean necesarias.

> [!NOTE]
En función del uso anticipado, puede resultar más rentable actualizar a un nivel con más cantidades incluidas que agregar instancias al nivel actual.

**¿Qué ocurre si cambio los niveles del plan (por ejemplo, cambio a un plan superior o inferior)?**

El antiguo plan se elimina y el uso actual se factura prorrateado. Después, se crea un nuevo plan con todas las cantidades incluidas en el nivel superior o inferior durante el tiempo restante del período.

> [!NOTE]
Las cantidades incluidas se asignan cada período y las cantidades no utilizadas no se transfieren al período siguiente.

**¿Qué ocurre cuando aumento las instancias de un plan?**

Las cantidades se incluyen mediante prorrateo y pueden tardar 24 horas en estar en vigor.

**¿Qué ocurre si elimino una instancia de un plan?**

La instancia se quita de la suscripción y se factura el uso prorrateado.

### <a name="sign-up-for-new-resource-manager-based-web-services-plans"></a>Registro en los nuevos planes de servicios web (basados en Resource Manager)
**¿Cómo me puedo suscribir a un plan?**

Existen dos mecanismos para crear planes de facturación.

La primera vez que implemente un servicio web basado en Resource Manager, puede elegir un plan existente o crear uno nuevo.

Los planes que se crean de este modo se encuentran en la región predeterminada, de modo que el servicio web se implementará en esa región.

Si quiere implementar servicios en regiones distintas a la predeterminada, puede que sea recomendable definir los planes de facturación antes de implementar el servicio.

En ese caso, puede iniciar sesión en el portal de servicios web Azure Machine Learning y acceder a la página de planes. Desde allí, puede agregar planes, eliminar planes y modificar los planes existentes.

**¿Qué plan debería elegir para empezar?**

Le recomendamos que empiece por el nivel Estándar S1 y supervise el uso que hace del servicio. Si agota rápidamente las cantidades incluidas, puede agregar más instancias o cambiar a un nivel superior, donde obtendrá mayores descuentos. Puede ajustar el plan de facturación a sus necesidades durante el ciclo de facturación.

**¿Qué regiones están disponibles para los nuevos planes?**

Los nuevos planes de facturación están disponibles en las tres regiones de producción en las que ofertamos los nuevos servicios web:

* Centro-Sur de EE. UU
* Europa occidental
* Sudeste de Asia

**Tengo servicios web en varias regiones. ¿Necesito un plan para cada región?**

Sí. Los precios del plan varían según la región. Cuando un servicio web se implementa en otra región, es preciso asignarlo a un plan específico de dicha región. Para más información, consulte [Productos disponibles por región]( https://azure.microsoft.com/regions/services/).

### <a name="new-web-services-overages"></a>Nuevos servicios web: uso por encima del límite
**¿Cómo puedo comprobar si he superado el uso del servicio web?**

Puede consultar el uso de todos los planes en la página Planes del portal de servicios web de Azure Machine Learning. Inicie sesión en el portal y haga clic en la opción del menú **Planes**.

En las columnas **Transacciones** y **Proceso** de la tabla, puede ver las cantidades incluidas en el plan y el porcentaje usado.

**¿Qué ocurre si agoto las cantidades incluidas en el plan de tarifa de desarrollo/pruebas?**

Los servicios que tienen asignado un plan de tarifa de desarrollo/pruebas se detienen hasta el siguiente período o hasta que los transfiere a un nivel de pago.

**En los servicios web clásicos y en el uso por encima del límite de los nuevos servicios web (basados en Resource Manager), ¿cómo se calculan los precios de las cargas de trabajo de los servicios de solicitud-respuesta (RRS) y de los servicios de ejecución por lotes (BES)?**

En el caso de las cargas de trabajo de RRS, se le facturan tanto las llamadas de las transacciones de API que realice como el tiempo de proceso asociado a esas solicitudes. El costo de las transacciones de API de producción de RRS se calcula como el número total de llamadas de API que se realizan multiplicado por el precio por 1000 transacciones (prorrateado por cada transacción individual). El costo por hora de proceso de API de producción de RRS se calcula como el tiempo necesario para que se ejecute cada llamada de API multiplicado por el total de transacciones de API y por el precio de la hora de proceso de API de producción.

Por ejemplo, en el caso del uso por encima del límite del nivel Estándar S1, si hay 1 000 000 transacciones de API, cada una de las cuales tarda 0,72 segundos en ejecutarse, el resultado será de 500 USD por los costos de transacción de API de producción (1 000 000 \* 0,50 USD/1000 transacciones de API) y de 400 USD por las horas de proceso de API de producción (1 000 000 0 \* 72 seg * 2 USD/h), lo que haría un total de 900 USD.

La carga de trabajo BES se le cobrará de la misma manera. Sin embargo, el costo de las transacciones de API representa el número de trabajos por lotes que se envían, mientras que el costo de proceso representa el tiempo de proceso asociado a dichos trabajos por lotes. El costo por transacciones de API de producción del servicio BES se calcula como el número total de trabajos enviados multiplicado por el precio por 1 000 transacciones (prorrateado por transacción individual). El costo por horas de proceso de API de producción del servicio BES se calcula como la cantidad de tiempo necesario para que se ejecute cada fila del trabajo multiplicado por el número total de filas del trabajo y multiplicado por el número total de trabajos y por el precio de la hora de proceso de API de producción. En la calculadora de Machine Learning, el medidor de transacciones representa el número de trabajos que planea enviar, mientras que el campo de tiempo por transacción representa el tiempo combinado necesario para que se ejecuten todas las filas de cada trabajo.

Por ejemplo, suponga un uso por encima del límite del nivel Estándar S1. Envía 100 trabajos al día, cada uno de los cuales consta de 500 filas, cada una de las cuales tarda 0,72 segundos en ejecutarse. Los costos mensuales de uso por encima del límite serían (100 trabajos al día = 3100 trabajos/mes \* 0,50 USD/1000 transacciones de API) 1,55 USD en costos de transacciones de API de producción y (500 filas 0,72 seg. \* 3100 trabajos \* 2 USD/h) 620 USD en horas de proceso de API de producción, lo que haría un total de 621,55 USD.

### <a name="azure-machine-learning-classic-web-services"></a>Servicios web clásicos de Azure Machine Learning
**¿Sigue estando disponible el plan de pago por uso?**

Sí. Los servicios web clásicos siguen disponibles en Azure Machine Learning.  

### <a name="azure-machine-learning-free-and-standard-tier"></a>Nivel Gratis y Estándar de Azure Machine Learning
**¿Qué incluye el nivel Gratis de Azure Machine Learning?**

El nivel Gratis de Azure Machine Learning Studio está pensado para proporcionar una introducción detallada de Azure Machine Learning Studio. Todo lo que necesita para suscribirse es una cuenta de Microsoft. El nivel Gratis incluye acceso gratuito a un área de trabajo de Azure Machine Learning Studio por cada [cuenta de Microsoft](https://www.microsoft.com/account/default.aspx). En este nivel, puede usar hasta 10 GB de almacenamiento y aplicar modelos como API de ensayo. No hay ningún Acuerdo de Nivel de Servicio que cubra las cargas de trabajo del nivel Gratis, ya que estas cargas de trabajo están destinadas exclusivamente a desarrollo y uso personal. 

Las áreas de trabajo de nivel Gratis tienen las limitaciones siguientes:

* Las cargas de trabajo no pueden acceder a los datos conectándose a un servidor local que ejecute SQL.
* No se pueden implementar nuevos servicios web de base de Resource Manager.


**¿Qué incluyen los planes y el nivel Estándar de Azure Machine Learning?**

El nivel Estándar de Azure Machine Learning es una versión de pago para entornos de producción de Azure Machine Learning Studio. La tarifa mensual del servicio Azure Machine Learning Studio se factura por área de trabajo y mes, y se prorratea por meses parciales. Las horas de experimentación de Azure Machine Learning Studio se facturan por hora de proceso de experimentación activa. La facturación se prorratea por horas parciales.  

El servicio API de Azure Machine Learning se factura dependiendo de si es un servicio web clásico o un nuevo servicio web (basado en Resource Manager).

Los siguientes gastos se agregan por área de trabajo para una suscripción.

* Suscripción a un área de trabajo de Machine Learning: esta suscripción es una cuota mensual que proporciona acceso a un área de trabajo de Machine Learning Studio. La suscripción es necesaria para ejecutar experimentos en Studio y para usar las API de producción.
* Horas de experimentación de Studio: este medidor suma todos los gastos de proceso acumulados por la ejecución de experimentos en Machine Learning Studio y de llamadas de API en el entorno de ensayo.
* Acceda a los datos mediante la conexión a un servidor local que ejecuta SQL Server en sus modelos para entrenarlos y puntuarlos.
* En los servicios web clásicos:
  * Horas de proceso de API de producción: este medidor incluye los gastos de proceso acumulados por los servicios web que se ejecutan en producción.
  * Transacciones de API de producción (en millares): este medidor incluye los gastos acumulados por llamada al servicio web de producción.

Además de los cargos anteriores, en el caso de los servicios web basados en Resource Manager, se agregan cargos al plan seleccionado:

* Plan de API para Estándar S1/S2/S3 API (unidades): este medidor representa el tipo de instancia seleccionada para servicios web basados en Resource Manager.
* Horas de proceso de API para uso por encima del límite en Estándar S1/S2/S3: este medidor incluye los gastos de proceso acumulados por los servicios web basados en Resource Manager que se ejecutan en producción una vez que se han agotado las cantidades incluidas en las instancias existentes. Para facturar el uso adicional, se usa la tarifa de uso por encima del límite asociada al nivel del plan S1/S2/S3.
* Transacciones de API de uso por encima del límite para Estándar S1/S2/S3 (en millares): este medidor incluye los gastos acumulados por cada llamada realizada al servicio web basado en Resource Manager de producción una vez que se han agotado las cantidades incluidas en las instancias existentes. Para facturar el uso adicional, se utiliza la tarifa de uso por encima del límite asociada al nivel del plan S1/S2/S3.
* Cantidad incluida de horas de proceso de API: con los servicios web basados en Resource Manager, este medidor representa la cantidad incluida de horas de proceso de API.
* Cantidad incluida de transacciones de API (en millares): con los servicios web basados en Resource Manager, este medidor representa la cantidad incluida de transacciones de API.

**¿Cómo puedo suscribirme al nivel Gratis de Azure Machine Learning?**

Todo lo que necesita es una cuenta de Microsoft. Vaya a la [página principal de Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) y haga clic en el botón **Start Now** (Empezar ahora). Inicie sesión con su cuenta de Microsoft y se creará un área de trabajo en el nivel Gratis. Puede empezar a explorar y a crear experimentos de Machine Learning inmediatamente.

**¿Cómo puedo suscribirme al nivel Estándar de Azure Machine Learning?**

Para poder crear un área de trabajo de Machine Learning de nivel Estándar, primero debe acceder a una suscripción de Azure. Puede suscribirse a la versión de evaluación gratuita de Azure de 30 días y actualizar después a una suscripción de Azure de pago, o adquirir directamente una suscripción de Azure de pago. Una vez que acceda a la suscripción, podrá crear un área de trabajo de Machine Learning desde 
Microsoft Azure Portal. Consulte [las instrucciones detalladas](https://azure.microsoft.com/trial/get-started-machine-learning-b/).

También existe la opción de que el propietario de un área de trabajo de Machine Learning de nivel Estándar le invite. De este modo, podrá obtener acceso al área de trabajo de dicho propietario.

**¿Puedo especificar mi propia cuenta de Azure Blob Storage para usarla con el nivel Gratis?**

No. El nivel Estándar es equivalente a la versión del servicio Machine Learning que estaba disponible antes de incorporar los niveles.

**¿Puedo implementar mis modelos de aprendizaje automático como API en el nivel Gratis?**

Sí. Puede aplicar modelos de aprendizaje automático a servicios de API de ensayo en el nivel Gratis. Si quiere poner el servicio de API de ensayo en producción y obtener un punto de conexión de producción para el servicio implementado, debe usar el nivel Estándar.

**¿Qué diferencia hay entre la versión de evaluación gratuita de Azure y el nivel Gratis de Azure Machine Learning?**

La [evaluación gratuita de Microsoft Azure](https://azure.microsoft.com/free/) ofrece créditos que pueden aplicarse a cualquier servicio de Azure durante un mes. El nivel Gratis de Azure Machine Learning ofrece acceso continuo específicamente a Azure Machine Learning para cargas de trabajo que no sean de producción.

**¿Cómo transfiero un experimento del nivel Gratis al nivel Estándar?**

Para copiar un experimento del nivel Gratis al nivel Estándar:

1. Inicie sesión en Azure Machine Learning Studio y asegúrese de que puede ver tanto las áreas de trabajo de nivel Gratis como Estándar en el selector de áreas de trabajo situado en la barra de navegación superior.
2. Cambie al área de trabajo del nivel Gratis si se encuentra en el área de trabajo del nivel Estándar.
3. En la vista de la lista de experimentos, seleccione el experimento que quiere copiar y haga clic en el botón de comando **Copiar**.
4. Seleccione el área de trabajo estándar en el cuadro de diálogo que se abre y luego haga clic en el botón **Copiar**.
   Todos los conjuntos de datos asociados, el modelo entrenado, etc. se copian junto con el experimento en el área de trabajo del nivel Estándar.
5. Por tanto, es preciso que volver a ejecutar el experimento y publicar de nuevo el servicio web en el área de trabajo del nivel Estándar.

### <a name="studio-workspace"></a>Área de trabajo de Studio
**¿Voy a tener facturas distintas por cada una de las áreas de trabajo?**

Los gastos de las áreas de trabajo se desglosan por separado por cada medidor aplicable en una sola factura.

**¿En qué tipo específico de recursos de proceso se ejecutarán mis experimentos?**

El servicio Machine Learning es un servicio multiinquilino. Los recursos de proceso reales que se usan en el back-end varían y están optimizados para aumentar el rendimiento y la previsibilidad.

### <a name="guest-access"></a>Acceso de invitado
**¿Qué es el acceso de invitado en Azure Machine Learning Studio?**

El acceso de invitado es una experiencia de evaluación restringida. Puede crear y ejecutar experimentos en Azure Machine Learning Studio sin costo alguno y sin autenticación. Las sesiones de invitado no son persistentes (no se pueden guardar) y están limitadas a 8 horas. Otras limitaciones son la falta de compatibilidad con R y Python, la falta de API de ensayo y la restricción en el tamaño del conjunto de datos y en la capacidad de almacenamiento. En comparación, los usuarios que eligen iniciar sesión con una cuenta de Microsoft tienen acceso total al nivel Gratis de Machine Learning Studio que se ha descrito anteriormente, que incluye un área de trabajo persistente y otras funcionalidades más completas. Para elegir la experiencia gratuita de Machine Learning, haga clic en **Get started** (Introducción) en [https://studio.azureml.net](https://studio.azureml.net) y seleccione **Guess Access** (Acceso de invitado) o inicie sesión con una cuenta de Microsoft.

<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[python]: https://msdn.microsoft.com/library/azure/CDB56F95-7F4C-404D-BDE7-5BB972E6F232
[counts]: https://msdn.microsoft.com/library/azure/dn913056.aspx
