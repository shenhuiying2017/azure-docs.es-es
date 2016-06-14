<properties
	pageTitle="Preguntas frecuentes de Aprendizaje automático de Azure | Microsoft Azure"
	description="Introducción a Aprendizaje automático Azure: preguntas más frecuentes sobre facturación, capacidades y limitaciones de un servicio de nube para un modelado de predicción optimizado."
	keywords="introducción de aprendizaje automático, modelo predictivo, qué es el aprendizaje automático"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/18/2016"
	ms.author="garye"/>

# Preguntas más frecuentes de Aprendizaje automático de Azure: facturación, capacidades, limitaciones y compatibilidad

Las preguntas más frecuentes son preguntas y respuestas sobre Aprendizaje automático de Azure, un servicio en la nube para el desarrollo de modelos predictivos y soluciones de puesta en funcionamiento a través de servicios web. Estas preguntas más frecuentes cubre las preguntas acerca de cómo utilizar el servicio, incluido el modelo de facturación, las capacidades, las limitaciones y la compatibilidad.

## Preguntas generales

**¿Qué es el Aprendizaje automático de Azure?**

El Aprendizaje automático de Azure es un servicio completamente administrado que sirve para crear, probar, utilizar y administrar soluciones de análisis predictivo en la nube. Con solo un explorador, puede registrarse, cargar datos e realizar inmediatamente experimentos de aprendizaje automático. Arrastre y colocación del modelado de predicción, una gran paleta de módulos y una biblioteca de plantillas de inicio convierten las tareas de Aprendizaje automático comunes en algo sencillo y rápido. Para obtener más información, consulte [Descripción general del servicio de Aprendizaje automático de Azure](https://azure.microsoft.com/services/machine-learning/). Para obtener una introducción de aprendizaje automático que cubre los conceptos y terminología clave, consulte [Introducción al aprendizaje automático de Azure](machine-learning-what-is-machine-learning.md).


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**¿Qué es Estudio de aprendizaje automático?**

Estudio de aprendizaje automático es un entorno de área de trabajo a la que se accede mediante un explorador web. Estudio de aprendizaje automático dispone de una paleta de módulos con una interfaz de composición visual que permite crear un flujo de trabajo de datos científicos completo en forma de un experimento.

Para más información sobre Estudio de aprendizaje automático, consulte [¿Qué es Estudio de aprendizaje automático de Azure?](machine-learning-what-is-ml-studio.md)

**¿Qué es el servicio API de Aprendizaje automático?**

El servicio API de Aprendizaje automático permite implementar modelos predictivos, como los creados en Estudio de aprendizaje automático, como servicios web escalables con tolerancia a errores. Los servicios web creados con el servicio API de Aprendizaje automático son varias API de REST que proporcionan una interfaz para la comunicación entre las aplicaciones externas y los modelos de análisis predictivo.

Consulte [Conexión a un servicio web de Aprendizaje automático](machine-learning-connect-to-azure-machine-learning-web-service.md) para obtener más información.


## Preguntas sobre facturación

**¿Cómo funciona la facturación de Aprendizaje automático?**

Para obtener información sobre precios y facturación, consulte [Precios de Aprendizaje automático](https://azure.microsoft.com/pricing/details/machine-learning/).

**¿Dispone Aprendizaje automático de una evaluación gratuita?**

 Cuando se registra para una evaluación gratuita de Azure, puede probar cualquier servicio de Azure durante un mes. Para más información sobre la evaluación gratuita de Azure, consulte las [preguntas más frecuentes sobre la evaluación gratuita de Azure](/pricing/free-trial-faq/).

## Preguntas sobre Estudio de aprendizaje automático

### Creación de un experimento

**¿Hay control de versiones o integración Git para experimentar con gráficos?**

No, sin embargo, Estudio de aprendizaje automático conserva cada iteración de un experimento que otros usuarios no pueden modificar. Para más información, consulte [Administrar iteraciones de experimentos en Estudio de aprendizaje automático de Azure](machine-learning-manage-experiment-iterations.md).

### Importación y exportación de datos para Aprendizaje automático

**¿Qué orígenes de datos admite Aprendizaje automático?**

Se pueden cargar datos en un experimento de Estudio de aprendizaje automático de tres formas: mediante la carga de un archivo local como conjunto de datos, mediante un módulo para importar datos de servicios de datos en la nube o mediante la importación de un conjunto de datos guardado desde otro experimento. Consulte [Importar datos de entrenamiento a Estudio de aprendizaje automático](machine-learning-data-science-import-data.md) para obtener más información acerca de los formatos de archivo compatibles.


#### <a id="ModuleLimit"></a>¿Cómo de grande puede ser el conjunto de datos para mis módulos?

Los módulos en Estudio de aprendizaje automático admiten conjuntos de datos de hasta 10 GB de datos numéricos densos para casos de uso comunes. Si un módulo ocupa más de una entrada, los 10 GB serán el total de todos los tamaños de entrada. También puede realizar un muestreo de conjuntos de datos más grandes mediante consultas de Base de datos SQL de Azure o Hive o usando el procesamiento previo de Aprendizaje por recuentos, antes de la ingesta.

Los siguientes tipos de datos se pueden expandir en conjuntos de datos grandes durante la normalización de características. Están limitados a menos de 10 GB:

- Dispersos
- Categorías
- Cadenas
- Datos binarios

Los siguientes módulos solo admiten conjuntos de datos que tengan menos de 10 GB:

- Módulos de recomendación.
- Módulo SMOTE.
- Módulos de script: R, Python y SQL
- Módulos donde el tamaño de los datos de salida puede ser mayor que el tamaño de los datos de entrada, como en la aplicación de hash de característica o unión.
- Validación cruzada, ajuste de los hiperparámetros del modelo, regresión ordinal y multiclase uno contra todos, cuando el número de iteraciones sea muy grande.

En el caso de conjuntos de datos que tengan varios gigas, hay que cargar los datos en el almacenamiento de Azure o en la Base de datos SQL de Azure o usar HDInsight, en lugar de cargarlos directamente desde el archivo local.


####<a id="UploadLimit"></a>¿Cuáles son los límites de carga de datos?
En el caso de conjuntos de datos que tengan más de dos gigas, hay que cargar los datos en el almacenamiento de Azure o en la Base de datos SQL de Azure o usar HDInsight, en lugar de cargarlos directamente desde el archivo local.

**¿Se pueden leer datos de Amazon S3?**

Si tiene una pequeña cantidad de datos y desea exponerlos a través de una dirección URL http, puede usar el módulo de [Importar datos][import-data]. Para transferir grandes cantidades de datos a Almacenamiento de Azure, en primer lugar, hay que realizar la transferencia y, luego, utilizar el módulo [Importar datos][import-data] para incluirlos en el experimento.
<!--
<SEE CLOUD DS PROCESS>
-->

**¿Hay una capacidad integrada para usar una entrada de imagen?**

Puede obtener información sobre la funcionalidad de entrada de imágenes en la referencia [Importar imágenes][image-reader].

### Módulos

**El algoritmo, el origen de datos, el formato de datos o la operación de transformación de datos que busco no están en Estudio de aprendizaje automático de Azure. ¿Qué opciones tengo?**

Puede visitar el [foro de comentarios de los usuarios](http://go.microsoft.com/fwlink/?LinkId=404231) para ver las solicitudes de características cuyo seguimiento estamos realizando. Agregue su voto a esta solicitud si la funcionalidad que busca ya se ha solicitado. Si la funcionalidad que busca no existe, cree una nueva solicitud. El estado de su solicitud puede verlo también en este foro. Seguimos muy de cerca esta lista y actualizamos el estado de la disponibilidad de las características con frecuencia. Además, gracias a la compatibilidad integrada con R y Python, se pueden crear transformaciones personalizadas según sea necesario.


**¿Puedo usar mi código existente en Estudio de aprendizaje automático?**

Sí, puede usar su código R o Python existente en Estudio de aprendizaje automático, ejecutarlo en el mismo experimento con los aprendices de Aprendizaje automático de Azure e implementar la solución como un servicio web a través de Aprendizaje automático de Azure. Para más información, consulte [Extender el experimento con R](machine-learning-extend-your-experiment-with-r.md) y [Ejecución de scripts de Python en Aprendizaje automático de Azure](machine-learning-execute-python-scripts.md).

**¿Se puede usar algo como [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) para definir un modelo?**

No, no es compatible. Sin embargo, sí se puede utilizar código Phyton y R para definir un módulo.

**¿Cuántos módulos puedo ejecutar en paralelo en mi experimento?**

Puede ejecutar hasta 4 módulos en paralelo en un experimento.


### Procesamiento de datos

**¿Se pueden visualizar los datos (más allá de visualizaciones R) interactivamente con el experimento?**

Si hace clic en el resultado de un módulo, puede visualizar los datos y obtener las estadísticas.

**Al obtener una vista previa de los resultados o los datos en el explorador, el número de filas y columnas es limitado, ¿por qué?**

Como los datos se transmiten al explorador y pueden ser grandes, su tamaño está limitado para evitar que Estudio de aprendizaje automático se ralentice. Es mejor descargar los resultados o los datos y utilizar Excel u otra herramienta para verlos en su totalidad.

### Algoritmos

**¿Qué algoritmos existentes se admiten en Estudio de aprendizaje automático?**

Estudio de aprendizaje automático ofrece modernos algoritmos de Aprendizaje automático, como árboles de decisiones incrementados escalables, sistemas de recomendaciones bayesianas, redes neuronales profundas y junglas de decisiones desarrollados en Microsoft Research. También se incluyen paquetes de Aprendizaje automático escalables de código abierto como Vowpal Wabbit. Estudio de aprendizaje automático admite algoritmos de aprendizaje automático para clasificación, regresión y agrupación en clústeres y binarias y multiclase. Consulte la lista completa de [Módulos de aprendizaje automático][machine-learning-modules].

**¿Se sugiere automáticamente el algoritmo de Aprendizaje automático adecuado para utilizarlo con mis datos?**

No. Sin embargo, Estudio de aprendizaje automático ofrece varias maneras de comparar los resultados de cada algoritmo para determinar la opción correcta para su problema.

**¿Hay instrucciones sobre la elección de un algoritmo en lugar de otro de los proporcionados?** Consulte [Elección de un algoritmo](machine-learning-algorithm-choice.md).

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

### Módulo R

**¿Qué paquetes de R están disponibles en Estudio de aprendizaje automático?**

Estudio de aprendizaje automático admite en la actualidad más de 400 paquetes de CRAN R. Esta es la [lista actual](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) de todos los paquetes incluidos. Consulte [Extender el experimentos con R](machine-learning-extend-your-experiment-with-r.md) para averiguar cómo recuperar esta lista. Si el paquete que desea no está en la lista, especifique el nombre del paquete en el [foro de comentarios de los usuarios](http://go.microsoft.com/fwlink/?LinkId=404231).

**¿Es posible crear un módulo personalizado de R?**

Sí. Consulte [Creación de módulos R personalizados en Aprendizaje automático de Azure](machine-learning-custom-r-modules.md) para obtener más información.

**¿Hay un entorno de REPL para R?**

No. No hay ningún entorno de REPL para R en el estudio.

### Módulo de Python

**¿Es posible crear un módulo personalizado de Python?**

No actualmente, pero se pueden utilizar uno o varios módulos [Ejecutar Script de Python][python] para obtener el mismo resultado.

**¿Hay un entorno de REPL para Python?**

Puede usar Jupyter Notebooks en Estudio de aprendizaje automático de Azure. Para más información, consulte [Introducing Jupyter Notebooks in Azure Machine Learning Studio](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx) (Introducción a Jupyter Notebooks en Estudio de aprendizaje automático de Azure).

## Servicio web

###Reentrenamiento de modelos mediante programación

**¿Cómo reentreno los modelos de Aprendizaje automático de Azure mediante programación?**

Use las API de reentrenamiento. Para más información, consulte [Volver a entrenar modelos de aprendizaje automático mediante programación](machine-learning-retrain-models-programmatically.md). También se puede encontrar código de ejemplo en la [demostración de reentrenamiento de Aprendizaje automático de Azure](https://azuremlretrain.codeplex.com/).

### Crear

**¿Puedo implementar el modelo de forma local o en una aplicación sin conexión a internet?**

Nº


**¿Cabe esperar una latencia de línea de base para todos los servicios web?**

Consulte [Límites de la suscripción de Azure](../azure-subscription-service-limits.md).

### Uso

**¿Cuándo podría ejecutar mi modelo predictivo como servicio de ejecución de lotes en lugar de como servicio web de solicitud/respuesta?**

El servicio de solicitud-respuesta (RRS) es un servicio web de alta escala y baja latencia que se usa para proporcionar una interfaz con los modelos sin estado creados e implementados desde el entorno de experimentación. El servicio de ejecución de lotes (BES) es un servicio para la puntuación asincrónica de lotes de registros de datos. La entrada para BES es similar a la entrada de datos que se utiliza en RRS. La diferencia principal radica en que BES lee un bloque de registros de varios orígenes, como servicios de blobs y tablas de Azure, Base de datos SQL de Azure, HDInsight (consultas de Hive) y orígenes HTTP. Para obtener más información, consulte [Consumo de servicios web de Aprendizaje automático](machine-learning-consume-web-services.md).

**¿Cómo se actualiza el modelo para la producción del servicio implementado?**

Actualizar un modelo predictivo para un servicio ya implementado es tan fácil como modificar y volver a ejecutar el experimento usado para crear y guardar el modelo entrenado. Una vez que puede disponer de una nueva versión del modelo entrenado, Estudio de aprendizaje automático le preguntará si quiere actualizar el servicio web. Vea [Implementación de un servicio web de Aprendizaje automático](machine-learning-publish-a-machine-learning-web-service.md) para obtener detalles sobre cómo actualizar un servicio web implementado.

También puede usar las API de reciclaje. Para más información, consulte [Volver a entrenar modelos de aprendizaje automático mediante programación](machine-learning-retrain-models-programmatically.md). También se puede encontrar código de ejemplo en la [demostración de reentrenamiento de Aprendizaje automático de Azure](https://azuremlretrain.codeplex.com/).

**¿Cómo se supervisa el servicio web implementado en producción?**

Cuando el modelo predictivo se ha implementado, puede supervisarlo desde el Portal de Azure clásico. Cada servicio implementado cuenta con su propio panel, donde se puede ver la información de supervisión de ese servicio.

**¿Hay algún lugar donde pueda ver la salida de mi RRS/BES?**

Para RRS, en la respuesta del servicio web normalmente es donde se verá el resultado. También puede escribir el resultado en Almacenamiento de blobs de Azure. Para BES, el resultado se escribe en un blob de manera predeterminada. También puede escribir el resultado en una base de datos o una tabla con el módulo [Exportación de datos][export-data].

**¿Solamente puedo crear servicios web a partir de modelos creados en Estudio de aprendizaje automático?**

No. También puede crear servicios web directamente desde Jupyter Notebooks y RStudio.

**¿Dónde puedo encontrar información sobre los códigos de error?**

Consulte [Códigos de Error del módulo de Aprendizaje automático](https://msdn.microsoft.com/library/azure/dn905910.aspx) para ver una lista de códigos de error y sus descripciones.

## Escalabilidad

**¿Qué es la escalabilidad del servicio web?**

En la actualidad, el punto de conexión predeterminado se ha aprovisionado con 20 solicitudes simultáneas de RRS por punto de conexión. Puede escalar este número hasta 200 solicitudes simultáneas por punto de conexión y puede escalar cada servicio web a 10 000 puntos de conexión por servicio web, como se describe en el artículo sobre [Escalado de puntos de conexión de API](machine-learning-scaling-endpoints.md). En BES, cada punto de conexión permite el procesamiento de 40 solicitudes a la vez y se ponen en cola las solicitudes adicionales que superan este número. Estas solicitudes en cola se ejecutarán automáticamente a medida que avanza la cola.


**¿Los trabajos de R se reparten entre nodos?**

Nº


**¿Cuántos datos se pueden usar para el entrenamiento?**

Los módulos en Estudio de aprendizaje automático admiten conjuntos de datos de hasta 10 GB de datos numéricos densos para casos de uso comunes. Si un módulo ocupa más de una entrada, el tamaño total de todas las entradas juntas es 10 GB. También puede realizar un muestreo de conjuntos de datos más grandes mediante consultas de Base de datos SQL de Azure o Hive o usando el procesamiento previo de [Recuentos de aprendizaje][counts], antes de la ingesta.

Los siguientes tipos de datos se pueden expandir en conjuntos de datos grandes durante la normalización de características. Están limitados a menos de 10 GB:

- dispersos
- categorías
- cadenas
- datos binarios

Los siguientes módulos solo admiten conjuntos de datos que tengan menos de 10 GB:

- Módulos de recomendación.
- Módulo SMOTE.
- Módulos de script: R, Python y SQL
- Módulos donde el tamaño de los datos de salida puede ser mayor que el tamaño de los datos de entrada, como en la aplicación de hash de característica o unión.
- Validación cruzada, ajuste de los hiperparámetros del modelo, regresión ordinal y multiclase uno contra todos, cuando el número de iteraciones sea muy grande.

En el caso de conjuntos de datos que tengan varios gigas, hay que cargar los datos en Almacenamiento de Azure o en Base de datos SQL de Azure, o usar HDInsight, en lugar de cargarlos directamente desde el archivo local.


**¿Hay alguna limitación en el tamaño de los vectores?**

Las filas y las columnas están limitadas a la limitación .NET de Máx. int.: 2,147,483,647.

**¿Se puede ajustar el tamaño de la máquina virtual que se usa para ejecutar el servicio web?**

Nº

## Seguridad y disponibilidad

**¿Quién tiene acceso al punto de conexión http del servicio web de forma predeterminada? ¿Cómo se restringe el acceso al punto de conexión?**

Cuando se implementa un servicio web, se crea un punto de conexión predeterminado para ese servicio. El punto de conexión predeterminado puede llamarse mediante su clave de API. Se pueden agregar puntos de conexión adicionales con sus propias claves desde el Portal de Azure clásico o mediante programación con las API de administración de servicios web. Se necesitan claves de acceso para realizar llamadas al servicio web. Para obtener más información, consulte [Conexión a un servicio web de Aprendizaje automático](machine-learning-connect-to-azure-machine-learning-web-service.md).


**¿Qué sucede si no se encuentra mi cuenta de almacenamiento de Azure?**

Estudio de aprendizaje automático depende de la cuenta de Almacenamiento de Azure suministrada para guardar datos intermediarios al ejecutar el flujo de trabajo. Esta cuenta de almacenamiento se proporciona a Estudio de aprendizaje automático en el momento de crear un área de trabajo. Una vez que se crea el área de trabajo, si se elimina la cuenta de almacenamiento y ya no puede encontrarse, el área de trabajo dejará de funcionar y todos los experimentos que haya en ella darán error.

Si elimina accidentalmente la cuenta de almacenamiento, la única manera de recuperarla es volver a crear la cuenta de almacenamiento con el mismo nombre y en la misma región que la eliminada. Después, vuelva a sincronizar la clave de acceso.


**¿Qué sucede si la clave de acceso de mi cuenta de almacenamiento no está sincronizada?**

Estudio de aprendizaje automático depende de la cuenta de Almacenamiento de Azure suministrada para guardar datos intermediarios al ejecutar el flujo de trabajo. Esta cuenta de almacenamiento se proporciona a Estudio de aprendizaje automático en el momento de crear un área de trabajo y las claves de acceso se asocian a dicha área de trabajo. Una vez que se crea el área de trabajo, si se cambian las claves de acceso, el área de trabajo no podrá acceder a la cuenta de almacenamiento, por lo que dejará de funcionar y todos los experimentos que haya en ella darán error.

Si han cambiado las claves de acceso de la cuenta de almacenamiento, asegúrese de volver a sincronizarlas en el área de trabajo mediante el Portal de Azure clásico.


## Azure Marketplace

Consulte [Publicación y uso de aplicaciones de Aprendizaje automático en Azure Marketplace: preguntas frecuentes](machine-learning-marketplace-faq.md)

## Soporte técnico y entrenamiento

**¿Dónde puedo recibir entrenamiento para Aprendizaje automático de Azure?**

El [Centro de Aprendizaje automático de Azure](https://azure.microsoft.com/services/machine-learning/) contiene tutoriales de vídeo y guías de procedimientos. Estas guías paso a paso proporcionan una introducción a los servicios y un recorrido por el ciclo de vida científico de los datos consistente en importar los datos, limpiarlos, construir modelos predictivos e implementarlos en producción con Aprendizaje automático de Azure.

Iremos agregando continuamente nuevo material al Centro de Aprendizaje automático. Puede enviarnos una solicitud de material de aprendizaje adicional en el Centro de Aprendizaje automático a través del [foro de comentarios de los usuarios](https://windowsazure.uservoice.com/forums/257792-machine-learning).

También puede encontrar cursos en [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning).

**¿Dónde puedo recibir soporte técnico para Aprendizaje automático de Azure?**

Para recibir soporte técnico para Aprendizaje automático de Azure, vaya al [Soporte técnico de Azure](/support/options/) y elija **Aprendizaje automático**.

Aprendizaje automático de Azure cuenta también con un foro de la comunidad en MSDN, donde puede plantear preguntas sobre el tema. El foro está supervisado por el equipo de Aprendizaje automático de Azure. Visite el [foro de Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).


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

<!---HONumber=AcomDC_0608_2016-->