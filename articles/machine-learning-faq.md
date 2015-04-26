<properties 
	pageTitle="P+F sobre el Aprendizaje automático de Microsoft Azure | Azure" 
	description="Preguntas más frecuentes sobre el Aprendizaje automático de Microsoft Azure" 
	services="machine-learning" 
	documentationCenter="" 
	authors="pablissima" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/17/2015" 
	ms.author="paulettm"/>

# Preguntas más frecuentes (P+F) sobre el Aprendizaje automático de Microsoft Azure

### GENERAL

**1. ¿Qué es el Aprendizaje automático de Microsoft Azure?**

El Aprendizaje automático de Microsoft Azure es un servicio completamente administrado que sirve para crear, probar, hacer operativas y gestionar soluciones de análisis en la nube. Con solo un explorador, ahora puede registrarse en el Aprendizaje automático de Azure, cargar datos e iniciar inmediatamente los experimentos de aprendizaje automático. La composición visual, la gran paleta de módulos y una biblioteca de plantillas de inicio convierten las tareas de aprendizaje automático comunes en algo sencillo y rápido. Transformar un modelo en un servicio web es fácil; con unos cuantos clics, un modelo predictivo compilado en el Estudio de aprendizaje automático se puede convertir en una API REST pública que encapsula la lógica personalizada de transformación de los datos y los sofisticados modelos de aprendizaje automático.

**2. ¿Qué es el Estudio de aprendizaje automático de Microsoft Azure?**

El Estudio de aprendizaje automático de Azure es un entorno de trabajo al que se accede mediante un explorador web. El Estudio de aprendizaje automático aloja una paleta de módulos con una interfaz de composición visual que permite crear un flujo de trabajo científico completo de los datos en forma de un experimento. Existen módulos para la ingesta y transformación de los datos, así como una selección de características para compilar, puntuar y evaluar modelos predictivos. Algunos de los algoritmos más avanzados que potencian el aprendizaje automático en Bing y Xbox se crean en el Estudio de aprendizaje automático. También se incluyen paquetes de aprendizaje automático escalables de código abierto como Vowpal Wabbit. El Estudio de aprendizaje automático es compatible con R, de modo que puede usar sus códigos R existentes e incorporarlos a los experimentos. Además, le permite combinar estos algoritmos con su código R para crear modelos predictivos. El Estudio de aprendizaje automático facilita la colaboración al permitirle invitar a su equipo a sus áreas de trabajo, donde pueden ver y modificar sus experimentos.

**3. ¿Qué es el servicio API de Aprendizaje automático de Azure?**

El servicio API de Aprendizaje automático le permite implementar modelos predictivos creados en el Estudio de aprendizaje automático como servicios web escalables con tolerancia a errores. Los servicios web creados con el servicio API de Aprendizaje automático son API REST que proporcionan una interfaz para la comunicación entre aplicaciones externas y el modelo de análisis predictivo. El servicio web ofrece una forma de comunicarse con un modelo predictivo en tiempo real para recibir los resultados de la predicción e incorporar dichos resultados en una aplicación cliente externa. El servicio API de Aprendizaje automático aprovecha Microsoft Azure para la implementación, hospedaje y administración de las API REST de Aprendizaje automático de Azure. Dos tipos de servicios se crean con el servicio API de Aprendizaje automático de Azure. El servicio de ejecución de lotes para el acceso por lotes asincrónico y el servicio de solicitud-respuesta para respuestas sincrónicas de baja latencia.

Dentro del área de trabajo, un modelo predictivo se puede poner en fase de ensayo. El servicio API de Aprendizaje automático también genera páginas de ayuda para los servicios web. Estas páginas de ayuda proporcionan ejemplos de código para invocar el servicio web en C#, R y Python. Puede probar su servicio web realizando llamadas interactivas al servicio. El servicio web provisional se puede poner luego en producción con tan solo unos clics. Una vez en producción, puede supervisar los servicios implementados, así como realizar el seguimiento del uso y los errores en el Portal de Azure. Actualizar los servicios web es tan sencillo como actualizar el modelo en el Estudio de aprendizaje automático y transferir los cambios al servicio provisional.

**4. ¿Cómo se accede al Aprendizaje automático de Microsoft Azure?**

Para comenzar con el Aprendizaje automático de Azure, visite la [página de inicio][página de inicio]. Visite el [Centro de Aprendizaje automático de Azure][Centro de Aprendizaje automático de Azure] para obtener actualizaciones sobre el servicio, leer el contenido más reciente en el blog de equipo de Aprendizaje automático, participar en los foros de nuestra comunidad de Aprendizaje automático, acceder a la ayuda del producto, ver la galería de modelos y proporcionar comentarios sobre el servicio para que nos ayude a dar forma a la guía del producto.

### FACTURACIÓN

**5. ¿Cómo funciona la facturación del Aprendizaje automático?**

El servicio Aprendizaje automático de Azure se factura por hora de proceso en la experimentación activa y la facturación se prorratea por horas parciales. El servicio se factura por 1000 llamadas a la API de predicción y por hora de proceso cuando una predicción se está ejecutando activamente. La facturación se prorratea para cantidades de predicción inferiores a 1000 y horas de proceso parciales.

Los cargos se agregan por área de trabajo en su suscripción. Dentro de cada área de trabajo verá los cargos por los tres elementos:

-   Horas de experimentación de Estudio de aprendizaje automático: este medidor agrega todos los cargos de proceso acumulados al ejecutar experimentos en el Estudio de aprendizaje automático y ejecutar las predicciones en el entorno de ensayo.
-   Horas de predicción de servicio de API: este medidor incluye los cargos de proceso acumulados por los servicios web en el entorno de producción.
-   Predicciones de servicio de API (en 1000): este medidor incluye los cargos acumulados por llamada al servicio web de producción.

Para obtener más información sobre precios, visite [http://azure.microsoft.com/pricing/details/machine-learning/][http://azure.microsoft.com/pricing/details/machine-learning/].

**6. ¿Dispone el Aprendizaje automático de Azure de una evaluación gratuita?**

El Aprendizaje automático de Azure forma parte de la evaluación gratuita de Azure. Cuando se registra para una evaluación gratuita de Azure, puede probar cualquier servicio de Azure durante un mes. Para obtener más información sobre la evaluación gratuita de Azure, visite [http://azure.microsoft.com/pricing/free-trial-faq/][http://azure.microsoft.com/pricing/free-trial-faq/].

### ESTUDIO DE APRENDIZAJE AUTOMÁTICO

**7. ¿Qué orígenes de datos admite el Aprendizaje automático de Azure?**

Los datos se pueden cargar en el Estudio de aprendizaje automático de dos formas: bien se cargan los archivos locales como un conjunto de datos o se usa el módulo de lectura para importar los datos. Los archivos locales se pueden cargar como conjuntos de datos mediante la adición de nuevos conjuntos de datos al Estudio de aprendizaje automático. Consulte el tema de ayuda sobre **cómo obtener los datos** en el Estudio de aprendizaje automático para obtener más información sobre los formatos de archivo admitidos.

El módulo **Lector** puede leer datos de tablas de Azure, blobs de Azure, Base de datos SQL (Azure) o HDInsight. También puede insertar datos desde un origen de datos a través de http. Para obtener más información, consulte el tema relativo al módulo **Lector** en el Estudio de aprendizaje automático.

**8. ¿Cómo de grandes pueden ser mis datos?**

El Estudio de aprendizaje automático admite conjuntos de datos de entrenamiento de hasta 10 gigas. No hay límite en cuanto al tamaño del conjunto de datos para servicios Web. También es posible tomar una muestra de conjuntos de datos más grandes a través de Hive o de consultas SQL antes de la ingesta. Si trabaja con datos de un tamaño superior a 10 gigas, puede crear varios conjuntos de datos y usar los módulos de partición y muestreo, de división o de unión para recombinar estos conjuntos de datos en el Estudio de aprendizaje automático a fin de crear conjuntos de entrenamiento para construir modelos predictivos. Consulte la ayuda del Estudio de aprendizaje automático para obtener más información sobre estos módulos.

En el caso de conjuntos de datos de más de dos gigas, el enfoque recomendado es actualizar los datos al almacenamiento de Azure o a Base de datos SQL (Azure) o usar HDInsight, en lugar de cargarlos directamente desde el archivo local.

**9. ¿Qué algoritmos existentes de Aprendizaje automático se admiten en el Estudio de aprendizaje automático?**

El Estudio de aprendizaje automático ofrece modernos algoritmos de Aprendizaje automático, como árboles de decisiones incrementadas escalables, sistemas de recomendaciones bayesianas, redes neuronales profundas y junglas de decisiones desarrollados en Microsoft Research. También se incluyen paquetes de aprendizaje automático escalables de código abierto como Vowpal Wabbit. El Estudio de aprendizaje automático admite algoritmos de aprendizaje automático para clasificación, regresión y agrupación en clústeres de varias clases y binarias. En la ayuda del Estudio de aprendizaje automático se puede encontrar una lista completa de los algoritmos de aprendizaje automático.

**10. El algoritmo de aprendizaje automático, el origen de datos, el formato de datos o la operación de transformación de datos que busco no está en el Estudio de aprendizaje automático de Azure. ¿Qué opciones tengo?**

Puede visitar el [foro de comentarios de los usuarios][foro de comentarios de los usuarios] para ver las solicitudes de características cuyo seguimiento estamos realizando. Agregue su voto a esta solicitud si la funcionalidad que busca ya se ha solicitado. Si la funcionalidad que busca no existe, cree una nueva solicitud. El estado de su solicitud puede verlo también en este foro. Seguimos muy de cerca esta lista y actualizamos el estado de la disponibilidad de las características con frecuencia.

**11. ¿Puedo usar mi código existente en el Estudio de aprendizaje automático?**

El Estudio de aprendizaje automático admite en la actualidad R, así que puede usar su código R existente en el Estudio de aprendizaje automático y ejecutarlo en el mismo experimento con el Aprendizaje automático de Azure que han proporcionado los alumnos para luego publicarlo como un servicio web a través del Aprendizaje automático de Azure. El Estudio de aprendizaje automático es la manera más rápida de convertir los activos de análisis de R en servicios web de producción de nivel empresarial. Consulte el tema de ayuda del Estudio de aprendizaje automático sobre la **extensibilidad con R** para obtener información acerca de cómo usar su código R y su visualización en el Estudio de aprendizaje automático.

**12. ¿Qué paquetes de R están disponibles en el Estudio de aprendizaje automático?**

El Estudio de aprendizaje automático admite en la actualidad más de 350 paquetes de R, y la lista sigue creciendo. Consulte el tema de ayuda del Estudio de aprendizaje automático sobre la **extensibilidad con R** para saber cómo obtener una lista de paquetes de R compatibles. Si el paquete que desea no está en la lista, proporcione el nombre del paquete en el [foro de comentarios de los usuarios][foro de comentarios de los usuarios].

### SERVICIO DE API DE APRENDIZAJE AUTOMÁTICO

**13. ¿Cuándo ejecutaría mi modelo predictivo como servicio de ejecución de lotes frente al servicio web de solicitud/respuesta?**

El servicio de solicitud-respuesta (RRS) es un servicio web de alta escala y baja latencia que se usa para proporcionar una interfaz para los modelos sin estado creados y publicados desde el entorno de experimentación. El servicio de ejecución de lotes (BES) es un servicio para la puntuación asincrónica de lotes de registros de datos. La entrada para BES es parecida a la entrada de datos usada en RRS. La diferencia principal radica en que BES lee un bloque de registros de varios orígenes, como blobs, tablas de Azure, Base de datos SQL (Azure), HDInsight (consultas de Hive) y orígenes HTTP. Los resultados de la puntuación se transfieren a un archivo del almacenamiento de blobs de Azure y en respuesta se devuelve el extremo de almacenamiento.

El servicio de ejecución de lotes resulta de utilidad en situaciones en las que es necesario puntuar una gran cantidad de puntos de datos, por lotes, o cuando muchos de los datos ya están en formato de archivo en un almacenamiento de Azure o en un clúster de Hadoop. El servicio web puede transformar los datos que lee antes de enviarlos al modelo, de modo que puede apuntar simplemente los datos de transacción de final de la semana a un servicio de lote, que transformará y proporcionará los resultados.

El servicio de solicitud-respuesta es útil en aquellos casos en los que se necesitan análisis predictivos casi en tiempo real para potenciar un panel dinámico o guiar las acciones de los usuarios o el contenido servido a través de una aplicación móvil o web.

**14. ¿Cómo se actualiza el modelo para la producción del servicio implementado?**

Actualizar un modelo predictivo para un servicio ya implementado es tan fácil como modificar y volver a ejecutar el experimento usado para crear y guardar el modelo entrenado. Una vez que puede disponer de la nueva versión del modelo entrenado, el Estudio de aprendizaje automático le pregunta si quiere actualizar el servicio web provisional. Después de aplicar la actualización al servicio web provisional, la misma actualización se volverá disponible para que la aplique también al servicio web de producción. Consulte el tema de ayuda del Estudio de aprendizaje automático relativo a **cómo actualizar el servicio web** para obtener detalles sobre cómo actualizar un servicio web implementado.

### SEGURIDAD Y DISPONIBILIDAD

**15. ¿Quién tiene acceso al extremo http del servicio web implementado de forma predeterminada en producción? ¿Cómo se restringe el acceso al extremo?**

Una vez que el modelo predictivo se ha puesto en producción, el Portal de Azure muestra la URL de los servicios web implementados. Las URL de servicio provisional son accesibles desde el entorno de Estudio de aprendizaje automático, en la sección de servicios web; mientras que las URL de servicio de producción son accesibles desde el Portal de Azure, en la sección Aprendizaje automático. Se proporcionan claves de acceso para los servicios web tanto de ensayo como de producción desde el panel de servicios web en el entorno del Estudio de aprendizaje automático y del Portal de Azure, respectivamente. Las claves de acceso son necesarias para realizar llamadas al servicio web en producción y ensayo.

**16. ¿Cómo se supervisa el servicio web implementado en producción?**

Cuando el modelo predictivo se ha puesto en producción, lo puede supervisar desde el Portal de Azure. Cada servicio implementado cuenta con su propio panel, donde se puede ver la información de supervisión de ese servicio.

### SOPORTE TÉCNICO Y ENTRENAMIENTO

**17. ¿Dónde puedo recibir entrenamiento para el Aprendizaje automático de Azure?**

El [Centro de Aprendizaje automático de Azure][Centro de Aprendizaje automático de Azure] contiene tutoriales de vídeo así como guías de procedimientos. Estas guías paso a paso proporcionan una introducción a los servicios y un recorrido por el ciclo de vida científico de los datos consistente en importar los datos, limpiarlos, construir modelos predictivos e implementarlos en producción con el Aprendizaje automático de Azure.

Los tutoriales de vídeo ofrecen un recorrido visual por el Estudio de aprendizaje automático y el servicio API de Aprendizaje automático. En ellos se demuestra la amplitud del servicio, la entrada de los datos de uso más común, la limpieza y el procesamiento de los módulos, además de la creación e implementación de los modelos predictivos. Los tutoriales de vídeo cubren también tareas como el aprovisionamiento del área de trabajo y la implementación de los modelos provisionales en producción.

Iremos agregando continuamente nuevo material al Centro de Aprendizaje automático. Puede enviarnos una solicitud de material de aprendizaje adicional en el Centro de Aprendizaje automático a través del [foro de comentarios de los usuarios][1].

**18. ¿Dónde puedo recibir soporte técnico para el Aprendizaje automático de Azure?**

El Aprendizaje automático de Azure forma parte de la oferta de soporte técnico de Azure. Para recibir soporte técnico en el Aprendizaje automático de Azure, seleccione 'Aprendizaje automático' como servicio y se le mostrará una categoría de temas para presentar su incidencia de soporte técnico. Para obtener más información sobre la oferta de soporte técnico de Azure, visite <http://azure.microsoft.com/support/options/>

El Aprendizaje automático de Azure cuenta también con un foro de la comunidad en MSDN, donde puede plantear preguntas sobre el tema. El equipo de Aprendizaje automático de Azure es el encargado de supervisar el foro. Visite el [foro de Azure][foro de Azure].

  [página de inicio]: http://go.microsoft.com/fwlink/?LinkId=404226
  [Centro de Aprendizaje automático de Azure]: http://azure.microsoft.com/documentation/services/machine-learning/
  [http://azure.microsoft.com/pricing/details/machine-learning/]: http://azure.microsoft.com/pricing/details/machine-learning/
  [http://azure.microsoft.com/pricing/free-trial-faq/]: http://azure.microsoft.com/pricing/free-trial-faq/
  [foro de comentarios de los usuarios]: http://go.microsoft.com/fwlink/?LinkId=404231
  [1]: https://windowsazure.uservoice.com/forums/257792-machine-learning
  [foro de Azure]: http://social.msdn.microsoft.com/Forums/windowsazure/es-es/home?forum=MachineLearning

<!--HONumber=46--> 

<!--HONumber=46--> 
