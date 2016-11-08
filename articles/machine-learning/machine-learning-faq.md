---
title: Preguntas frecuentes de Aprendizaje automático de Azure | Microsoft Docs
description: 'Introducción a Aprendizaje automático Azure: preguntas más frecuentes sobre facturación, capacidades y limitaciones de un servicio de nube para un modelado de predicción optimizado.'
keywords: introducción de aprendizaje automático, modelo predictivo, qué es el aprendizaje automático
services: machine-learning
documentationcenter: ''
author: garyericson
manager: paulettm
editor: cgronlun

ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/14/2016
ms.author: garye

---
# <a name="azure-machine-learning-frequently-asked-questions-(faq):-billing,-capabilities,-limitations,-and-support"></a>Preguntas más frecuentes de Aprendizaje automático de Azure: facturación, capacidades, limitaciones y compatibilidad
Las preguntas más frecuentes son preguntas y respuestas sobre Aprendizaje automático de Azure, un servicio en la nube para el desarrollo de modelos predictivos y soluciones de puesta en funcionamiento a través de servicios web. Estas preguntas más frecuentes cubre las preguntas acerca de cómo utilizar el servicio, incluido el modelo de facturación, las capacidades, las limitaciones y la compatibilidad.

## <a name="general-questions"></a>Preguntas generales
**¿Qué es el Aprendizaje automático de Azure?**

El Aprendizaje automático de Azure es un servicio completamente administrado que sirve para crear, probar, utilizar y administrar soluciones de análisis predictivo en la nube. Con solo un explorador, puede registrarse, cargar datos e realizar inmediatamente experimentos de aprendizaje automático. Arrastre y colocación del modelado de predicción, una gran paleta de módulos y una biblioteca de plantillas de inicio convierten las tareas de Aprendizaje automático comunes en algo sencillo y rápido.  Para obtener más información, consulte [Descripción general del servicio de Aprendizaje automático de Azure](https://azure.microsoft.com/services/machine-learning/). Para obtener una introducción de aprendizaje automático que cubre los conceptos y terminología clave, consulte [Introducción al aprendizaje automático de Azure](machine-learning-what-is-machine-learning.md).

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**¿Qué es Estudio de aprendizaje automático?**

Estudio de aprendizaje automático es un entorno de área de trabajo a la que se accede mediante un explorador web. Estudio de aprendizaje automático dispone de una paleta de módulos con una interfaz de composición visual que permite crear un flujo de trabajo de datos científicos completo en forma de un experimento.

Para más información sobre Estudio de aprendizaje automático, consulte [¿Qué es Estudio de aprendizaje automático de Azure?](machine-learning-what-is-ml-studio.md)

**¿Qué es el servicio API de Aprendizaje automático?**

El servicio API de Aprendizaje automático permite implementar modelos predictivos, como los creados en Estudio de aprendizaje automático, como servicios web escalables con tolerancia a errores. Los servicios web creados con el servicio API de Aprendizaje automático son varias API de REST que proporcionan una interfaz para la comunicación entre las aplicaciones externas y los modelos de análisis predictivo.

Para obtener más información, consulte [Conexión a un servicio web de Aprendizaje automático](machine-learning-connect-to-azure-machine-learning-web-service.md).

**¿Dónde puedo ver los servicios web clásicos? ¿Donde se enumeran mis nuevos servicios web basados Azure Resource Manager?**

Los servicios web clásicos se encuentran en [Estudio de aprendizaje automático](http://studio.azureml.net) , en la pestaña de servicios web. Los nuevos servicios web basados en Azure Resource Manager se encuentran en el portal de [servicios web Microsoft Azure Machine Learning](https://services.azureml.net/) . No existen listados con datos cruzados.

## <a name="microsoft-azure-machine-learning-web-service-questions"></a>Preguntas sobre los servicios web de Aprendizaje automático de Microsoft Azure
**¿Qué son los servicios web de Aprendizaje automático de Azure?**

Con el servicio web de Aprendizaje automático de Azure, una aplicación externa se comunica con un modelo de puntuación de flujo de trabajo de Aprendizaje automático en tiempo real. Una llamada al servicio web de Aprendizaje automático devuelve resultados de predicción a una aplicación externa. Para llamar a un servicio web Machine Learning, es necesario pasar una clave de API que se haya creado al implementar el servicio web. El servicio web de Aprendizaje automático se basa en REST, una opción popular de arquitectura para proyectos de programación web.

Aprendizaje automático de Azure tiene dos tipos de servicios:

* Servicio de solicitud-respuesta (RRS): servicio de baja latencia altamente escalable que proporciona una interfaz a los modelos sin estado creados e implementados desde Estudio de aprendizaje automático.
* Servicio de ejecución por lotes (BES): servicio asincrónico que puntúa un lote de registros de datos.

Hay varias maneras de usar la API de REST y acceder al servicio web. Por ejemplo, puede escribir una aplicación en C#, R o Python con el código de ejemplo que se genera automáticamente cuando se implementa el servicio web.

Dicho código está disponible en: la página de consumo del servicio web del portal de servicios web Azure Machine Learning, la página de ayuda de la API en el panel del servicio web Machine Learning Studio.

O bien, puede usar el libro de Microsoft Excel de ejemplo creado automáticamente (también disponible en el panel del servicio web en Studio).

**¿Cuáles son las principales actualizaciones aplicables a los servicios web de Aprendizaje automático de Azure?**

Para más información acerca de los nuevos servicios web Azure Machine Learning, consulte la [documentación relacionada](machine-learning-whats-new.md).

## <a name="machine-learning-studio-questions"></a>Preguntas sobre Estudio de aprendizaje automático
### <a name="creating-an-experiment"></a>Creación de un experimento
**¿Hay control de versiones o integración Git para experimentar con gráficos?**

No, sin embargo, Estudio de aprendizaje automático conserva cada iteración de un experimento que otros usuarios no pueden modificar.
Para más información, consulte [Administrar iteraciones de experimentos en Estudio de aprendizaje automático de Azure](machine-learning-manage-experiment-iterations.md).

### <a name="deploying-an-experiment"></a>Implementación de un experimento
**¿Puedo implementar un experimento predictivo como un nuevo servicio web (basado en Azure Resource Manager) si ya lo ha implementado como un servicio web clásico?**

No. No puede implementar un experimento que previamente se haya implementado como un servicio web clásico. Debe crear un nuevo experimento predictivo e implementarlo.

### <a name="importing-and-exporting-data-for-machine-learning"></a>Importación y exportación de datos para Aprendizaje automático
**¿Qué orígenes de datos admite Aprendizaje automático?**

Se pueden cargar datos en un experimento de Estudio de aprendizaje automático de tres formas: mediante la carga de un archivo local como conjunto de datos, mediante un módulo para importar datos de servicios de datos en la nube o mediante la importación de un conjunto de datos guardado desde otro experimento. Para más información acerca de los formatos de archivo compatibles, consulte [Importación de datos de entrenamiento en Machine Learning Studio desde varios orígenes de datos](machine-learning-data-science-import-data.md).

#### <a name="<a-id="modulelimit"></a>how-large-can-the-data-set-be-for-my-modules?"></a><a id="ModuleLimit"></a>¿Cómo de grande puede ser el conjunto de datos para mis módulos?
Los módulos en Estudio de aprendizaje automático admiten conjuntos de datos de hasta 10 GB de datos numéricos densos para casos de uso comunes. Si un módulo ocupa más de una entrada, los 10 GB serán el total de todos los tamaños de entrada. También puede realizar un muestreo de conjuntos de datos más grandes mediante consultas de Base de datos SQL de Azure o Hive o usando el procesamiento previo de Aprendizaje por recuentos, antes de la ingesta.  

Los siguientes tipos de datos se pueden expandir en conjuntos de datos grandes durante la normalización de características. Están limitados a menos de 10 GB:

* Dispersos
* Categorías
* Cadenas
* Datos binarios

Los siguientes módulos solo admiten conjuntos de datos que tengan menos de 10 GB:

* Módulos de recomendación.
* Módulo SMOTE.
* Módulos de script: R, Python y SQL
* Módulos donde el tamaño de los datos de salida puede ser mayor que el tamaño de los datos de entrada, como en la aplicación de hash de característica o unión.
* Validación cruzada, ajuste de los hiperparámetros del modelo, regresión ordinal y multiclase uno contra todos, cuando el número de iteraciones sea muy grande.

En el caso de conjuntos de datos que tengan varios gigas, hay que cargar los datos en el almacenamiento de Azure o en la Base de datos SQL de Azure o usar HDInsight, en lugar de cargarlos directamente desde el archivo local.

#### <a name="<a-id="uploadlimit"></a>what-are-the-limits-for-data-upload?"></a><a id="UploadLimit"></a>¿Cuáles son los límites de carga de datos?
En el caso de conjuntos de datos que tengan más de dos gigas, hay que cargar los datos en el almacenamiento de Azure o en la Base de datos SQL de Azure o usar HDInsight, en lugar de cargarlos directamente desde el archivo local.

**¿Se pueden leer datos de Amazon S3?**

Si tiene una pequeña cantidad de datos y desea exponerlos a través de una dirección URL http, puede usar el módulo [Importar datos][import-data]. Si la cantidad de datos es mayor, primero debe transferirlos a Azure Storage y luego utilizar el módulo [Importar datos][import-data] para incluirlos en el experimento.
<!--

<SEE CLOUD DS PROCESS>
-->

**¿Hay una capacidad integrada para usar una entrada de imagen?**

Puede obtener información sobre la funcionalidad de introducción de imágenes en la referencia de [Import images](Importar imágenes.md)[image-reader].

### <a name="modules"></a>Módulos
**El algoritmo, el origen de datos, el formato de datos o la operación de transformación de datos que busco no están en Estudio de aprendizaje automático de Azure. ¿Qué opciones tengo?**

Puede visitar el [foro de comentarios de los usuarios](http://go.microsoft.com/fwlink/?LinkId=404231) para consultar solicitudes de características cuyo seguimiento estamos realizando. Agregue su voto a esta solicitud si la funcionalidad que busca ya se ha solicitado. Si la funcionalidad que busca no existe, cree una nueva solicitud. El estado de su solicitud puede verlo también en este foro. Seguimos muy de cerca esta lista y actualizamos el estado de la disponibilidad de las características con frecuencia. Además, gracias a la compatibilidad integrada con R y Python, se pueden crear transformaciones personalizadas según sea necesario.

**¿Puedo usar mi código existente en Estudio de aprendizaje automático?**

Sí, puede usar su código R o Python existente en Estudio de aprendizaje automático, ejecutarlo en el mismo experimento con los aprendices de Aprendizaje automático de Azure e implementar la solución como un servicio web a través de Aprendizaje automático de Azure. Para más información, consulte [Extender el experimento con R](machine-learning-extend-your-experiment-with-r.md) y [Ejecución de scripts de Machine Learning de Python en Azure Machine Learning Studio](machine-learning-execute-python-scripts.md).

**¿Se puede usar algo como [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) para definir un modelo?**

No, no es compatible. Sin embargo, sí se puede utilizar código Phyton y R para definir un módulo.

**¿Cuántos módulos puedo ejecutar en paralelo en mi experimento?**  

En un experimento se pueden ejecutar hasta cuatro módulos en paralelo.

### <a name="data-processing"></a>Procesamiento de datos
**¿Se pueden visualizar los datos (más allá de visualizaciones R) interactivamente en el experimento?**

Si hace clic en el resultado de un módulo, puede visualizar los datos y obtener las estadísticas.

**Al obtener una vista previa de los resultados o los datos en el explorador, el número de filas y columnas es limitado, ¿por qué?**

Como los datos se transmiten al explorador y pueden ser grandes, su tamaño está limitado para evitar que Estudio de aprendizaje automático se ralentice. Es mejor descargar los resultados o los datos y utilizar Excel u otra herramienta para verlos en su totalidad.

### <a name="algorithms"></a>Algoritmos
**¿Qué algoritmos existentes se admiten en Machine Learning Studio?**

Machine Learning Studio ofrece los algoritmos mas innovadores, como árboles de decisión incrementados escalables, sistemas de recomendaciones bayesianas, redes neuronales profundas y selvas de decisión, que se han desarrollado en Microsoft Research. También se incluyen paquetes de Aprendizaje automático escalables de código abierto como Vowpal Wabbit. Estudio de aprendizaje automático admite algoritmos de aprendizaje automático para clasificación, regresión y agrupación en clústeres y binarias y multiclase. Consulte la lista completa de [Módulos de Machine Learning][machine-learning-modules].

**¿Se sugiere automáticamente el algoritmo de Machine Learning adecuado para utilizarlo con mis datos?**

No. Sin embargo, Machine Learning Studio ofrece varias maneras de comparar los resultados de cada algoritmo para determinar el más apropiado para su problema.

**¿Hay instrucciones sobre la elección de un algoritmo en lugar de otro de los proporcionados?**
Consulte [Elección de un algoritmo](machine-learning-algorithm-choice.md).

**¿Se proporcionan algoritmos escritos en R o Python?**

No. Estos algoritmos principalmente se escriben en lenguajes compilados para que ofrezcan un mayor rendimiento.

**¿Se proporcionan algunos detalles de los algoritmos?**

La documentación proporciona alguna información acerca de los algoritmos, y se describen los parámetros proporcionados para optimizar el algoritmo para su uso.  

**¿Se admite el aprendizaje en línea?**

No. Actualmente solo se admite el reentrenamiento mediante programación.

**¿Puedo visualizar las capas de un modelo de red neuronal con el módulo integrado?**

Nº

**¿Puedo crear mis propios módulos en C# o algún otro lenguaje?**

Actualmente, solo se pueden crear nuevos módulos personalizados en R.

### <a name="r-module"></a>Módulo R
**¿Qué paquetes de R están disponibles en Estudio de aprendizaje automático?**

En la actualidad, Machine Learning Studio admite más de 400 paquetes de CRAN R y esta es la [lista actual](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) de todos los paquetes incluidos. Consulte también [Extender el experimento con R](machine-learning-extend-your-experiment-with-r.md) para aprender a recuperar esta lista. Si el paquete que desea no está en la lista, especifique el nombre del paquete en el [foro de comentarios de los usuarios](http://go.microsoft.com/fwlink/?LinkId=404231).

**¿Es posible crear un módulo personalizado de R?**

Sí. Para más información, consulte [Creación de módulos R personalizados en Azure Machine Learning](machine-learning-custom-r-modules.md).

**¿Hay un entorno de REPL para R?**

No. No hay ningún entorno de REPL para R en el estudio.

### <a name="python-module"></a>Módulo de Python
**¿Es posible crear un módulo personalizado de Python?**

En la actualidad no es posible. Sin embargo, puede utilizar uno o varios módulos de [ejecución de scripts de Python][python] para obtener el mismo resultado.

**¿Hay un entorno de REPL para Python?**

Puede usar Jupyter Notebooks en Estudio de aprendizaje automático de Azure. Para más información, consulte [Introducing Jupyter Notebooks in Azure Machine Learning Studio (Introducción de Jupyter Notebooks en Azure Machine Learning Studio)](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).

## <a name="web-service"></a>Servicio web
### <a name="retraining-models-programmatically"></a>Reciclaje de modelos mediante programación
**¿Cómo reentreno los modelos de Aprendizaje automático de Azure mediante programación?**

Use las API de reentrenamiento. for more information, see [Volver a entrenar modelos de aprendizaje automático mediante programación](machine-learning-retrain-models-programmatically.md). También puede encontrar código de ejemplo en [Microsoft Azure Machine Learning Retraining Demo](https://azuremlretrain.codeplex.com/)(Demostración de reciclaje de Microsoft Azure Machine Learning).

### <a name="create"></a>Crear
**¿Puedo implementar el modelo de forma local o en una aplicación sin conexión a internet?**

Nº

**¿Cabe esperar una latencia de línea de base para todos los servicios web?**

Consulte [Límites de la suscripción de Azure](../azure-subscription-service-limits.md)

### <a name="use"></a>Uso
**¿Cuándo podría ejecutar mi modelo predictivo como servicio de ejecución de lotes en lugar de como servicio web de solicitud/respuesta?**

El servicio de solicitud-respuesta (RRS) es un servicio web de alta escala y baja latencia que se usa para proporcionar una interfaz con los modelos sin estado creados e implementados desde el entorno de experimentación. El servicio de ejecución de lotes (BES) es un servicio para la puntuación asincrónica de lotes de registros de datos. La entrada para BES es similar a la entrada de datos que se utiliza en RRS. La diferencia principal radica en que BES lee un bloque de registros de varios orígenes, como servicios de blobs y tablas de Azure, Base de datos SQL de Azure, HDInsight (consultas de Hive) y orígenes HTTP. Para obtener más información, consulte [Consumo de servicios web de Aprendizaje automático](machine-learning-consume-web-services.md).

**¿Cómo se actualiza el modelo para la producción del servicio implementado?**

Para actualizar de un modelo predictivo para un servicio ya implementado solo es preciso modificar y volver a ejecutar el experimento usado para crear y guardar el modelo entrenado. Una vez que disponga de una nueva versión del modelo entrenado, Machine Learning Studio le preguntará si desea actualizar el servicio web. Para obtener información acerca de cómo actualizar un servicio web implementado, consulte [Implementación de un servicio web Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md).

También puede usar las API de reciclaje.
Para obtener más información, consulte [Volver a entrenar modelos de aprendizaje automático mediante programación](machine-learning-retrain-models-programmatically.md). También puede encontrar código de ejemplo en [Microsoft Azure Machine Learning Retraining Demo](https://azuremlretrain.codeplex.com/)(Demostración de reciclaje de Microsoft Azure Machine Learning).

**¿Cómo se supervisa el servicio web implementado en producción?**

Cuando el modelo predictivo se ha implementado, puede supervisarlo desde el Portal de Azure clásico. Cada servicio implementado cuenta con su propio panel, donde se puede ver la información de supervisión de ese servicio. Para más información acerca de cómo administrar los servicios web implementados, consulte [Administración de un área de trabajo de Aprendizaje automático de Azure](machine-learning-manage-workspace.md).

**¿Hay algún lugar donde pueda ver la salida de mi RRS/BES?**

Para RRS, en la respuesta del servicio web normalmente es donde se verá el resultado. También puede escribir el resultado en Almacenamiento de blobs de Azure. Para BES, el resultado se escribe en un blob de manera predeterminada. El resultado también se puede escribir en una base de datos o en una tabla con el módulo [Exportar datos][export-data].

**¿Solamente puedo crear servicios web a partir de modelos creados en Estudio de aprendizaje automático?**

No. También puede crear servicios web directamente desde Jupyter Notebooks y RStudio.

**¿Dónde puedo encontrar información sobre los códigos de error?**

Consulte [Códigos de error del módulo de Aprendizaje automático](https://msdn.microsoft.com/library/azure/dn905910.aspx) para ver una lista de códigos de error y sus descripciones.

## <a name="scalability"></a>Escalabilidad
**¿Qué es la escalabilidad del servicio web?**

En la actualidad, el punto de conexión predeterminado se ha aprovisionado con 20 solicitudes simultáneas de RRS por punto de conexión. Como se describe en [Escalado de servicios web](machine-learning-scaling-webservice.md), este número se puede escalar hasta alcanzar las 200 solicitudes simultáneas por punto de conexión y escalar cada servicio web hasta los 10 000 puntos de conexión por servicio web. En BES, cada punto de conexión permite el procesamiento de 40 solicitudes a la vez y se ponen en cola las solicitudes adicionales que superan este número. Dichas solicitudes se ejecutan automáticamente a medida que avanza la cola.

**¿Los trabajos de R se reparten entre nodos?**

Nº  

**¿Cuántos datos se pueden usar para el entrenamiento?**

Los módulos en Estudio de aprendizaje automático admiten conjuntos de datos de hasta 10 GB de datos numéricos densos para casos de uso comunes. Si un módulo ocupa más de una entrada, el tamaño total de todas las entradas juntas es 10 GB. También puede realizar un muestreo de conjuntos de datos mayores mediante consultas de Azure SQL Database o de Hive, o bien mediante el procesamiento previo con los módulos de [Learning with Counts][counts] antes de la ingesta.  

Los siguientes tipos de datos se pueden expandir en conjuntos de datos grandes durante la normalización de características. Están limitados a menos de 10 GB:

* dispersos
* categorías
* cadenas
* datos binarios

Los siguientes módulos están limitados a conjuntos de datos de menos de 10 GB:

* Módulos de recomendación.
* Módulo SMOTE.
* Módulos de script: R, Python y SQL
* Módulos donde el tamaño de los datos de salida puede ser mayor que el tamaño de los datos de entrada, como en la aplicación de hash de característica o unión.
* Validación cruzada, ajuste de los hiperparámetros del modelo, regresión ordinal y multiclase uno contra todos, cuando el número de iteraciones sea muy grande.

En el caso de conjuntos de datos que tengan varios gigas, hay que cargar los datos en Almacenamiento de Azure o en Base de datos SQL de Azure, o usar HDInsight, en lugar de cargarlos directamente desde el archivo local.

**¿Hay alguna limitación en el tamaño de los vectores?**

Las filas y las columnas están limitadas a la limitación .NET de Máx. int.: 2,147,483,647.

**¿Se puede ajustar el tamaño de la máquina virtual que se usa para ejecutar el servicio web?**

Nº  

## <a name="security-and-availability"></a>Seguridad y disponibilidad
**¿Quién tiene acceso de forma predeterminada al punto de conexión http del servicio web? ¿Cómo se restringe el acceso al punto de conexión?**

Cuando se implementa un servicio web, se crea un punto de conexión predeterminado para ese servicio. El punto de conexión predeterminado puede llamarse mediante su clave de API. Se pueden agregar puntos de conexión adicionales con sus propias claves desde el Portal de Azure clásico o mediante programación con las API de administración de servicios web. Se necesitan claves de acceso para realizar llamadas al servicio web. Para obtener más información, consulte [Conexión a un servicio web de Aprendizaje automático](machine-learning-connect-to-azure-machine-learning-web-service.md).

**¿Qué sucede si no se encuentra mi cuenta de Azure Storage?**

Machine Learning Sutdio depende de la cuenta de Azure Storage suministrada para guardar datos intermediarios al ejecutar el flujo de trabajo. Esta cuenta de almacenamiento se proporciona a Machine Learning Studio en el momento de crear un área de trabajo. Una vez que se crea el área de trabajo, si se elimina la cuenta de almacenamiento y ya no puede encontrarse, el área de trabajo dejará de funcionar y todos los experimentos que haya en ella darán error.

Si ha eliminado accidentalmente la cuenta, vuelva a crearla con el mismo nombre y en la misma región que la eliminada. Después, vuelva a sincronizar la clave de acceso.

**¿Qué sucede si la clave de acceso de mi cuenta de almacenamiento no está sincronizada?**

Machine Learning Studio usa una cuenta de Azure Storage suministrada por el usuario para guardar los datos intermedios al ejecutar el flujo de trabajo. Esta cuenta se proporciona a Machine Learning Studio en el momento de crear un área de trabajo y las claves de acceso se asocian a dicha área de trabajo. Si se cambian las claves de acceso después de haber creado el área de trabajo, este no podrá volver a acceder a la cuenta. Dejará de funcionar y se producirá un error en todos los experimentos que haya en esa área de trabajo.

Si ha cambiado las claves de acceso de la cuenta, vuelva a sincronizarlas en el área de trabajo mediante el Portal de Azure clásico.  

## <a name="azure-marketplace"></a>Azure Marketplace
Consulte [Publicación y uso de aplicaciones de Aprendizaje automático en Azure Marketplace: preguntas frecuentes](machine-learning-marketplace-faq.md).

## <a name="support-and-training"></a>Soporte técnico y entrenamiento
**¿Dónde puedo recibir entrenamiento para Aprendizaje automático de Azure?**

[Centro de documentación de Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) hospeda tutoriales en vídeo y guías de procedimientos. Estas guías paso a paso proporcionan una introducción a los servicios y un recorrido por el ciclo de vida científico de los datos consistente en importar los datos, limpiarlos, construir modelos predictivos e implementarlos en producción con Aprendizaje automático de Azure.

Agregamos continuamente nuevo material al Centro de Machine Learning. Puede solicitar material de aprendizaje adicional sobre el Centro de Aprendizaje automático en el [foro de comentarios de los usuarios](https://windowsazure.uservoice.com/forums/257792-machine-learning).

También puede buscar cursos en [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning).

**¿Dónde puedo recibir soporte técnico para Aprendizaje automático de Azure?**

Para obtener soporte técnico sobre Aprendizaje automático de Azure, acceda a la página del [servicio de soporte técnico de Azure](/support/options/) y seleccione **Aprendizaje automático**.

Aprendizaje automático de Azure cuenta también con un foro de la comunidad en MSDN, donde puede plantear preguntas sobre el tema. El foro está supervisado por el equipo de Aprendizaje automático de Azure. Visite el [foro de Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).

## <a name="billing-questions"></a>Preguntas sobre facturación
**¿Cómo funciona la facturación de Aprendizaje automático?**

El servicio de Aprendizaje automático de Azure consta de dos componentes: Estudio de aprendizaje automático y Servicios web de Aprendizaje automático.

Puede utilizar el nivel de facturación gratuito mientras prueba Estudio de aprendizaje automático.  Este nivel también le permite implementar un servicio web clásico con capacidad limitada.

Si decide que Aprendizaje automático de Azure se ajusta a sus necesidades, puede suscribirse en el nivel Estándar. Para ello, debe tener una suscripción a Microsoft Azure.

En el nivel estándar se factura mensualmente un importe mensual por cada área de trabajo que se define en Machine Learning Studio. Si ejecuta un experimento en Estudio de aprendizaje automático, se facturarán los recursos de proceso. Si implementa un servicio web clásico, las transacciones y las horas de proceso se facturarán mediante un plan de pago por uso (PAYG).

En los nuevos servicios web de Aprendizaje automático, se han incorporado planes de facturación que hacen que los costos sean más predecibles. Los precios por niveles ofrecen descuentos a los clientes que necesitan una gran cantidad de capacidad.

Al crear un plan, se compromete a pagar un costo fijo que incluye varias horas de proceso y transacciones de API. Si necesita incluir más cantidades, puede agregar otras instancias al plan. Si necesita un volumen de cantidades mucho mayor, puede elegir un plan superior que incluya muchas más cantidades, ya que el porcentaje de descuento resulta más ventajoso.

Una vez que se agotan las cantidades incluidas en las instancias existentes, se carga una cuota de uso por encima del límite correspondiente al nivel del plan de facturación.

Nota: las cantidades incluidas se reasignan cada 30 días y las cantidades que no utilizadas no se transfieren al período siguiente.

Para más información sobre los precios y la facturación, consulte los [precios de Aprendizaje automático](https://azure.microsoft.com/pricing/details/machine-learning/).

**¿Dispone Aprendizaje automático de una evaluación gratuita?**

 Azure Machine Learning tiene una opción de suscripción gratuita (para más información, consulte los [precios de Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/)), mientras que Machine Learning Studio dispone de una versión de evaluación rápida de ocho horas (para acceder a esta versión, inicie sesión en [Machine Learning Studio](https://studio.azureml.net/?selectAccess=true&o=2)).

 Además, al registrarse para obtener una evaluación gratuita de Azure, puede probar cualquier servicio de Azure durante un mes. Para más información sobre la prueba gratuita de Azure, visite [Preguntas más frecuentes sobre la evaluación gratuita de Azure](/pricing/free-trial-faq/).

**¿Qué es una transacción?**

Una transacción representa una llamada API a la que responde Aprendizaje automático de Azure. Las transacciones de las llamadas del Servicio de solicitud-respuesta (RRS) y del Servicio de ejecución por lotes (BES) se computan y se facturan de acuerdo con el plan de facturación.

**¿Puedo usar las cantidades de transacciones incluidas con transacciones de RRS y BES?**

Sí. Las transacciones de RRS y BES se computan y se facturan en función del plan de facturación.

**¿Qué es una hora de proceso de API?**

Una hora de proceso de API es la unidad de facturación que mide el tiempo que las llamadas API tardan en ejecutarse utilizando los recursos de proceso de Aprendizaje automático. Todas las llamadas computan a la hora de facturar.

**¿Cuánto tarda una llamada API de producción típica?**

Los tiempos de las llamadas API de producción pueden variar notablemente. Por lo general, oscilan entre cientos de milisegundos y algunos segundos, aunque pueden llegar a durar minutos, según la complejidad del procesamiento de datos y del modelo de aprendizaje automático. La mejor manera de estimar los tiempos de las llamadas API de producción es hacer pruebas comparativas con un modelo del servicio Aprendizaje automático.

**¿Qué es una hora de proceso de Estudio?**

Una hora de proceso de Estudio es la unidad de facturación con la que se mide el tiempo total que los experimentos utilizan los recursos de proceso de Estudio.

**¿Para qué sirve el nivel de desarrollo/pruebas de los nuevos servicios web?**

Los nuevos servicios web de Aprendizaje automático de Azure disponen de muchos niveles que pueden utilizarse para aprovisionar el plan de facturación. El nivel de desarrollo o pruebas incluye cantidades limitadas, que permiten probar los experimentos como nuevos servicios web de forma gratuita. Esto le brinda la oportunidad de probar sus experimentos y comprobar su funcionamiento.

**¿Existen otros cargos de almacenamiento?**

El nivel Gratis de Aprendizaje automático no requiere ni permite otro almacenamiento. El nivel Estándar de Aprendizaje automático requiere que los usuarios tengan una cuenta de Almacenamiento de Azure. Almacenamiento de Azure [se factura por separado](https://azure.microsoft.com/pricing/details/storage/).

**¿Cómo funciona la compatibilidad de Aprendizaje automático con la alta disponibilidad?**

Los tiempos de las llamadas API de producción pueden variar notablemente. Por lo general, oscilan entre cientos de milisegundos y algunos segundos, aunque pueden llegar a durar minutos, según la complejidad del procesamiento de datos y del modelo de aprendizaje automático. La mejor manera de estimar los tiempos de las llamadas API de producción es hacer pruebas comparativas con un modelo del servicio Aprendizaje automático.

**¿En qué tipo específico de recursos de proceso se van a ejecutar las llamadas API de producción?**

Machine Learning es un servicio multiinquilino y los recursos de proceso reales que se utilizan en el back-end varían y se optimizan tanto su rendimiento como su capacidad de previsión.

### <a name="management-of-new-web-services"></a>Administración de nuevos servicios web
**¿Qué ocurre si elimino mi plan?**

El plan se quita de la suscripción y se factura el uso prorrateado.

Nota: Los planes que estén siendo utilizados por un servicio web no se pueden eliminar. Para poder eliminarlos, debe asignar un nuevo plan al servicio web o eliminar dicho servicio web.

**¿Qué es una instancia del plan?**

Una instancia del plan es una unidad de cantidades incluidas que se pueden agregar al plan de facturación. Cuando selecciona un nivel de facturación para el plan, incluye una instancia. Si necesita incluir más cantidades, puede agregar al plan otras instancias del nivel de facturación seleccionado.

**¿Cuántas instancias del plan puedo agregar?**

Una suscripción solo puede incluir una única instancia del nivel de desarrollo/pruebas.

En los niveles S1, S2 y S3, se pueden agregar todas las instancias necesarias.

Nota: En función del uso previsto, puede resultar más rentable actualizar a un nivel con más cantidades incluidas que agregar instancias al nivel actual.

**¿Qué ocurre si cambio los niveles del plan (por ejemplo, cambio a un plan superior o inferior)?**

El antiguo plan se elimina y el uso actual se factura prorrateado. Después, se crea un nuevo plan con todas las cantidades incluidas en el nivel superior o inferior durante el tiempo restante del período.

Nota: Las cantidades incluidas se asignan cada período y las cantidades no utilizadas no se transfieren al período siguiente.

**¿Qué ocurre cuando aumento las instancias de un plan?**

Las cantidades se incluyen mediante prorrateo y pueden tardar 24 horas en estar en vigor.

**¿Qué ocurre si elimino una instancia de un plan?**

La instancia se quita de la suscripción y se factura el uso prorrateado.

### <a name="signing-up-for-new-web-services-plans"></a>Suscripción a planes de nuevos servicios web
**¿Cómo me puedo suscribir a un plan?**

Existen dos mecanismos para crear planes de facturación.

La primera vez que implemente un servicio web, puede elegir un plan existente o crear uno nuevo.

Los planes que se crean de este modo se encuentran en la región predeterminada, de modo que el servicio web se implementará.

Si desea implementar servicios en regiones distintas que no sean la predeterminada, puede que sea recomendable definir los planes de facturación antes de implementar el servicio.

En ese caso, puede iniciar sesión en el portal de servicios web de Azure Machine Learning y acceder a la página de planes. Desde dicha página puede agregar y eliminar planes, así como modificar los planes existentes.

**¿Qué plan debería elegir para empezar?**

Le recomendamos que empiece por el nivel Estándar S1 y analice el uso que hace del servicio. Si agota rápidamente las cantidades incluidas, puede agregar más instancias o cambiar a un nivel superior, donde obtendrá mayores descuentos. Puede ajustar el plan de facturación a sus necesidades durante el ciclo de facturación.

**¿Qué regiones están disponibles para los nuevos planes?**

Los nuevos planes de facturación están disponibles en las tres regiones de producción en las que ofertamos los nuevos servicios web:

* Centro-Sur de EE. UU
* Europa occidental
* Sudeste de Asia

**Tengo servicios web en varias regiones. ¿Necesito un plan para cada región?**

Sí. Los precios del plan varían según la región. Cuando un servicio web se implementa en otra región, es preciso asignarlo a un plan específico de dicha región.

### <a name="new-web-services---overages"></a>Nuevos servicios web - Uso por encima del límite
**¿Cómo puedo saber si el uso de mi servicio web está por encima del límite?**

Puede consultar el uso de todos los planes en la página Planes del portal de servicios web de Aprendizaje automático de Azure. Inicie sesión en el portal y haga clic en la opción del menú Planes.

En las columnas Transacciones y Proceso de la tabla, puede ver las cantidades incluidas en el plan y el porcentaje de uso.

**¿Qué ocurre si agoto las cantidades incluidas en el nivel de desarrollo/pruebas?**

Los servicios que tienen asignado un nivel de desarrollo/pruebas se detienen hasta el siguiente período o hasta que los transfiera a un nivel de pago.

**En los servicios web clásicos y cuando se supera el límite de los nuevos servicios web, ¿cómo se calculan los precios de las cargas de trabajo de los servicios de solicitud-respuesta (RRS) y de los servicios de ejecución por lotes (BES)?**

En el caso de las cargas de trabajo de RRS, se le facturan tanto las llamadas de las transacciones de API que realice como el tiempo de proceso asociado a las solicitudes. El costo de las transacciones de API de producción de RRS se calcula como el número total de llamadas de API que se realizan multiplicado por el precio por 1 000 transacciones (prorrateado por cada transacción individual). El costo por hora de proceso de API de producción de RRS se calcula como el tiempo necesario para que se ejecute cada llamada de API multiplicado por el total de transacciones de API y por el precio de la hora de proceso de API de producción.

Por ejemplo, en el caso del uso por encima del límite del nivel Estándar S1, si hay 1 000 000 transacciones de API, cada una de las cuales tarda 0,72 segundos en ejecutarse, el resultado será de 500 USD por los costos de transacción de API de producción (1 000 000 * 0,50 USD/1000 transacciones de API) y de 400 USD por las horas de proceso de API de producción (1 000 000 * 0,72 seg * 2 USD/h), lo que haría un total de 900 USD.

En el caso de las cargas de trabajo de BES, se facturan de la misma forma; sin embargo, el costo de las transacciones de API representa el número de trabajos por lotes que se envían, mientras que el costo de proceso representa el tiempo de proceso asociado a dichos trabajos por lotes. El costo por transacciones de API de producción del servicio BES se calcula como el número total de trabajos enviados multiplicado por el precio por 1 000 transacciones (prorrateado por transacción individual). El costo por horas de proceso de API de producción del servicio BES se calcula como la cantidad de tiempo necesario para que se ejecute cada fila del trabajo multiplicado por el número total de filas del trabajo y multiplicado por el número total de trabajos y por el precio de la hora de proceso de API de producción. En la calculadora de Aprendizaje automático, el medidor de transacciones representa el número de trabajos que planea enviar, mientras que el campo de tiempo por transacción representa el tiempo combinado necesario para que se ejecuten todas las filas de cada trabajo.

Por ejemplo, en el caso del uso por encima del límite del nivel Estándar S1, si envía 100 trabajos diarios, cada uno de ellos tiene 500 filas y cada fila tarda 0,72 segundos, el costo mensual del uso por encima del límite sería (100 trabajos al día = 3100 trabajos/mes * 0,50 USD/1000 transacciones de API) 1,55 USD en costos de las transacciones de la API de producción y (500 filas * 0,72 s * 3100 trabajos * 2 USD/h) 620 USD en horas de proceso de la API de producción, lo que haría un total de 621,55 USD.

### <a name="azure-ml-classic-web-services"></a>Servicios web clásicos de Aprendizaje automático de Azure
**¿Sigue estando disponible el plan de pago por uso?**
Sí. Los servicios web clásicos siguen disponibles en Aprendizaje automático de Azure.  

### <a name="azure-machine-learning-free-and-standard-tier"></a>Nivel Gratis y nivel Estándar de Aprendizaje automático de Azure
**¿Qué incluye el nivel Gratis de Aprendizaje automático de Azure?**

El nivel Gratis de Aprendizaje automático de Azure está pensado para proporcionar una introducción detallada de Estudio de aprendizaje automático de Azure. Todo lo que necesita para suscribirse es una cuenta de Microsoft. El nivel Gratis incluye acceso gratuito a un área de trabajo de Estudio de aprendizaje automático de Azure por cada [cuenta de Microsoft](https://www.microsoft.com/account/default.aspx). También permite usar hasta 10 GB de almacenamiento y utilizar modelos como API provisionales. No hay ningún Acuerdo de Nivel de Servicio que cubra las cargas de trabajo del nivel Gratis, ya que estas cargas de trabajo están destinadas exclusivamente a desarrollo y uso personal. Las cargas de trabajo del nivel Gratis no pueden acceder a los datos conectándose a un servidor SQL Server local.

**¿Qué incluyen los planes y el nivel Estándar de Aprendizaje automático de Azure?**

El nivel Estándar de Aprendizaje automático de Azure es una versión de pago para entornos de producción de Estudio de aprendizaje automático de Azure. La tarifa mensual del servicio Azure Machine Learning Studio se factura por área de trabajo y mes, y se prorratea por meses parciales. Las horas de experimento de Estudio de aprendizaje automático de Azure se facturan por hora de proceso de experimentación activa. La facturación se prorratea por horas parciales.  

La facturación del servicio de API de Aprendizaje automático de Azure varía en función de si se utilizan los servicios web clásicos o un nuevo servicio web.

Los siguientes gastos se agregan por área de trabajo para una suscripción.

* Suscripción por área de trabajo de Machine Learning: la suscripción por área de trabajo de Machine Learning consiste en una tarifa mensual que proporciona acceso a un área de trabajo de ML Studio y que se requiere para ejecutar experimentos tanto en ML Studio como al usar las API de producción.
* Horas de experimentación de Estudio de aprendizaje automático: este medidor suma todos los gastos de proceso acumulados por la ejecución de experimentos en Estudio de aprendizaje automático y de llamadas API en el entorno de ensayo.
* Acceso a los datos a través de una conexión a un servidor SQL Server local de los modelos para su entrenamiento y puntuación.
* Servicios web clásicos:
  * Horas de proceso de API de producción: este medidor incluye los gastos de proceso acumulados por los servicios web que se ejecutan en producción.
  * Transacciones de API de producción (en miles): este medidor incluye los gastos acumulados por llamadas al servicio web de producción.

Además de los gastos anteriores, en el caso de los nuevos servicios web, estos otros gastos se agregarán al plan seleccionado:

* Plan de API para Estándar S1/S2/S3 API (unidades): este medidor representa el tipo de instancia seleccionada para los nuevos servicios web.
* Horas de proceso de API para uso por encima del límite para Estándar S1/S2/S3: este medidor incluye los gastos de proceso acumulados por los nuevos servicios web que se ejecutan en producción una vez que se han agotado las cantidades incluidas en las instancias existentes. Para facturar el uso adicional, se utiliza la tarifa de uso por encima del límite asociada al nivel del plan S1/S2/S3.
* Transacciones de API por encima del límite para Estándar S1/S2/S3 (en millares): este medidor incluye los gastos acumulados por cada llamada realizada al nuevo servicio web de producción una vez que se han agotado las cantidades incluidas en las instancias existentes. Para facturar el uso adicional, se utiliza la tarifa de uso por encima del límite asociada al nivel del plan S1/S2/S3.
* Cantidad incluida de horas de proceso de API: con los nuevos servicios web, este medidor representa la cantidad incluida de horas de proceso de API.
* Cantidad incluida de transacciones de API (en millares): con los nuevos servicios web, este medidor representa la cantidad incluida de transacciones de API.

**¿Cómo me suscribo al nivel Gratis de Aprendizaje automático de Azure?**

Todo lo que necesita es una cuenta de Microsoft. Vaya a la [página principal de Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)y haga clic en el botón **Empezar ahora**. Inicie sesión con su cuenta de Microsoft y se creará un área de trabajo en el nivel Gratis. Puede empezar a explorar y a crear experimentos de Aprendizaje automático inmediatamente.

**¿Cómo me suscribo al nivel Estándar de Aprendizaje automático de Azure?**

Para poder crear un área de trabajo de Machine Learning de nivel Estándar, primero debe tener acceso a un área de trabajo de ML. Puede suscribirse a la versión de evaluación gratuita de Azure de 30 días y actualizar después a una suscripción de Azure de pago, o adquirir directamente una suscripción de Azure de pago. Una vez que acceda a la suscripción, puede crear un área de trabajo de Machine Learning desde el Portal de Microsoft Azure clásico. Consulte [las instrucciones detalladas](https://azure.microsoft.com/trial/get-started-machine-learning-b/).

También existe la opción de que el propietario de un área de trabajo de Aprendizaje automático de nivel Estándar le invite. De este modo, podrá obtener acceso al área de trabajo de dicho propietario.

**¿Puedo especificar mi propia cuenta de Almacenamiento de blobs de Azure para usarla con el nivel Gratis?**

No. El nivel Estándar es equivalente a la versión del servicio Aprendizaje automático que estaba disponible antes de incorporar los niveles.

**¿Puedo implementar mis modelos de aprendizaje automático como API en el nivel Gratis?**

Sí. Puede utilizar modelos de aprendizaje automático en servicios de API de ensayo en el nivel Gratis. Si desea poner el servicio de API provisional en producción y obtener un punto de conexión de producción para el servicio implementado, debe usar el nivel Estándar.

**¿Qué diferencia hay entre la versión de evaluación gratuita de Azure y el nivel Gratis de Aprendizaje automático de Azure?**

La [versión de evaluación gratuita de Microsoft Azure](https://azure.microsoft.com/free/) ofrece créditos que pueden aplicarse a cualquier servicio de Azure durante un mes. El nivel Gratis de Azure Machine Learning ofrece acceso continuo específicamente al servicio Azure Machine Learning para cargas de trabajo que no sean de producción.

**¿Cómo transfiero un experimento del nivel Gratis al nivel Estándar?**

Para copiar un experimento del nivel Gratis al nivel Estándar:

1. Inicie sesión en Estudio de aprendizaje automático de Azure y asegúrese de que puede ver tanto el área de trabajo del nivel Gratis como el área de trabajo del nivel Estándar en el selector de áreas de trabajo situado en la barra de navegación superior.
2. Cambie al área de trabajo del nivel Gratis si se encuentra en el área de trabajo del nivel Estándar.
3. En la vista de la lista de experimentos, seleccione el experimento que desee copiar y haga clic en el botón de comando Copiar.
4. Seleccione el área de trabajo Estándar en el cuadro de dialogo emergente y haga clic en el botón Copiar.
   Todos los conjuntos de datos asociados, el modelo entrenado, etc. se copian junto con el experimento en el área de trabajo del nivel Estándar.
5. Por tanto, es preciso que volver a ejecutar el experimento y publicar de nuevo el servicio web en el área de trabajo del nivel Estándar.

### <a name="studio-workspace"></a>Área de trabajo de Estudio
**¿Voy a tener facturas distintas por cada una de las áreas de trabajo?**

Los gastos de las áreas de trabajo se desglosan por separado por cada medidor aplicable en una sola factura.

**¿En qué tipo específico de recursos de proceso se ejecutarán mis experimentos?**

Machine Learning es un servicio multiinquilino y los recursos de proceso reales que se utilizan en el back-end varían y se optimizan tanto su rendimiento como su capacidad de previsión.

### <a name="guest-access"></a>Acceso de invitado
**¿Qué es el acceso de invitado en Estudio de aprendizaje automático de Azure?**

El acceso de invitado es una funcionalidad de evaluación restringida que permite crear y ejecutar experimentos en Estudio de aprendizaje automático de Azure sin costo alguno y sin autenticación. Las sesiones de invitado no son persistentes (no se pueden guardar) y están limitadas a 8 horas. Otras limitaciones son la falta de compatibilidad con R y Python, la falta de API de ensayo y la restricción en el tamaño del conjunto de datos y en la capacidad de almacenamiento. En comparación, los usuarios que eligen iniciar sesión con una cuenta de Microsoft tienen acceso total al nivel Gratis de Machine Learning Studio que se ha descrito, que incluye un área de trabajo persistente y otras funcionalidades más completas. Para elegir la experiencia gratuita de Machine Learning, haga clic en **Get started** (Introducción) en [https://studio.azureml.net](https://studio.azureml.net)y seleccione Guess Access (Acceso de invitado) o Sign In with a Microsoft account (Iniciar sesión con una cuenta de Microsoft).

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



<!--HONumber=Oct16_HO2-->


