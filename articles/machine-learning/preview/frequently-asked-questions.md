---
title: "Preguntas más frecuentes de la versión preliminar de Azure Machine Learning 2017 | Microsoft Docs"
description: "Este artículo contiene las preguntas más frecuentes y las respuestas para características de la versión preliminar de Azure Machine Learning"
services: machine-learning
author: serinakaye
ms.author: serinak
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 08/30/2017
ms.openlocfilehash: 94c6576f92b5a004e0fd509c1dc6e091c294658a
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="azure-machine-learning-frequently-asked-questions"></a>Preguntas más frecuentes de Azure Machine Learning

## <a name="general-product-questions"></a>Preguntas generales sobre el producto

**¿Qué es Azure Machine Learning?**

Azure Machine Learning es un servicio de Azure totalmente administrado que le permite crear, probar, administrar e implementar modelos de aprendizaje automático y de inteligencia artificial. Nuestros nuevos servicios y la aplicación descargable ofrecen un enfoque de Code First que aprovecha la nube, el entorno local y el borde para proporcionar el entrenamiento, la implementación, la administración y la supervisión de los modelos con eficacia, velocidad y flexibilidad. Como alternativa, Azure Machine Learning Studio ofrece un entorno visual de creación basado en explorador, de arrastrar y colocar, donde no se requiere ningún código. 

**¿Cómo puedo empezar a usar la versión preliminar?**

La manera más fácil de empezar a trabajar es registrarse en el servicio de Experimentación de Azure. Desde el portal, también puede descargar la aplicación gratuita de Workbench o registrarse en una cuenta de Administración de modelos. Además, puede visitar nuestro sitio de documentación para consultar artículos de procedimientos, vídeos, códigos de demostración y mucho más. 

**No tengo una suscripción a Azure. ¿Aún así puedo probar los nuevos servicios?**

Como parte de la cartera de Microsoft Azure, nuestros nuevos servicios requieren una suscripción a Azure. Además, debe tener los permisos adecuados para crear activos, como Grupos de recursos, Máquinas virtuales, etc. 

**¿En qué regiones están disponibles los nuevos servicios?**

Para buscar las regiones de Azure donde se admite el servicio de experimentación y de administración de modelos de Azure Machine Learning, consulte la página [Productos de Azure por región](https://azure.microsoft.com/regions/services/).

Se agregarán más regiones a medida que se desarrolle el producto.  Para ayudarnos a dar prioridad a las regiones donde se implemente el servicio, visite nuestro sitio de [comentarios de Azure Machine Learning](https://feedback.azure.com/forums/257792-machine-learning). 

**¿Qué otros servicios de Azure son necesarios?**

Para conocer a fondo todas las funcionalidades de Azure Machine Learning, Azure Machine Learning usará la cuenta de VSTS de Visual Studio, Azure Blob Storage, Azure Container Registry, Data Science VM o HDInsight compute, y Azure Container Service.

**¿Cómo se relaciona Azure Machine Learning con Microsoft Machine Learning Services en SQL Server 2017?**   

Machine Learning Services en SQL Server 2017 es una plataforma extensible y escalable para integrar tareas de aprendizaje automático en los flujos de trabajo de bases de datos. Es ideal para escenarios donde se necesita una solución local, por ejemplo, donde el movimiento de datos es caro o insostenible. En cambio, las cargas de trabajo en la nube o híbridas son una excelente elección para nuestros nuevos servicios de Azure. 

**¿Se admiten Python y R? ¿Qué ocurre con otros lenguajes de programación, como C++?**

Actualmente solo se admite Python. Estamos trabajando en la integración de R y esperamos que esté disponible pronto. 

**¿Cómo se relaciona Azure Machine Learning con Microsoft Machine Learning para Spark?**

R: MMLSpark proporciona herramientas de aprendizaje profundo y de ciencia de datos para Apache Spark, con especial hincapié en la productividad, la facilidad de experimentación y algoritmos de última generación. MMLSpark ofrece integración de canalizaciones de Spark Machine Learning con el kit de herramientas de Microsoft Cognitive y OpenCV. Puede crear modelos de predicción y análisis eficaces, potentes y altamente escalables para los datos de imagen y texto. MMLSpark está disponible en una licencia de código abierto y se incluye en AML Workbench como un conjunto de algoritmos y modelos consumibles. Para obtener más información sobre MMLSpark, visite nuestra documentación del producto. 

**¿Qué versiones de Spark son compatibles con las nuevas herramientas y servicios? Sección superior**

Workbench actualmente incluye y admite MMLSpark versión 0.8, que es compatible con Apache Spark 2.1. También tiene una opción para usar la imagen de Docker basada en GPU de MMLSpark 0,8 en máquinas virtuales con Linux.

## <a name="experimentation-service"></a>Servicio Experimentación

**¿Qué es el servicio Experimentación de Azure Machine Learning?**

El servicio Experimentación es un servicio administrado de Azure que lleva los experimentos de aprendizaje automático al siguiente nivel. Los experimentos pueden crearse de forma local o en la nube. Cree prototipos rápidamente en un equipo de escritorio y luego escale a máquinas virtuales o clústeres de Spark. Azure VM con la tecnología de GPU más reciente le permite participar en aprendizaje profundo de forma rápida y eficaz. También hemos incluido una fuerte integración con GIT, por lo que puede conectarse fácilmente en flujos de trabajo existentes para el seguimiento del código, la configuración y la colaboración. 

**¿Cómo se cobra el servicio Experimentación?**

Los primeros dos usuarios asociados con el servicio Experimentación de Azure Machine Learning son gratuitas. Los usuarios adicionales se cobrarán a la tarifa de la versión preliminar pública de $50/mes. Para obtener más información sobre precios y facturación, visite nuestra página de precios.

**¿Los cargos van en función del número de experimentos que se ejecuten?**

No, el servicio Experimentación permite tantos experimentos como necesite. Los cargos se realizan únicamente en función del número de usuarios. Los recursos de proceso de Experimentación se cobran aparte. Le recomendamos realizar varios experimentos para poder encontrar el modelo que mejor se ajuste a su solución.   

**¿Qué tipos concretos de recursos de proceso y almacenamiento pueden usarse?**

El servicio Experimentación puede ejecutar los experimentos en equipos locales (directos o basados en Docker), [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) y [HDInsight](https://azure.microsoft.com/services/hdinsight/). El servicio también tiene acceso a una cuenta de [Azure Storage](https://azure.microsoft.com/services/storage/) para almacenar los resultados de ejecución, y puede aprovechar una cuenta de [Visual Studio Team Service](https://azure.microsoft.com/services/visual-studio-team-services/) para el almacenamiento de GIT y control de versiones. Tenga en cuenta que los recursos de proceso y almacenamiento se facturan por separado según los precios de cada uno.  


## <a name="model-management"></a>Administración de modelos

**¿Qué es la Administración de modelos de Azure Machine Learning?**

Administración de modelos de Azure Machine Learning es un servicio administrado de Azure que permite a los científicos de datos y equipos de operaciones de desarrollo implementar modelos predictivos de forma confiable en una amplia variedad de entornos. Los repositorios de GIT y contenedores de Docker proporcionan repetibilidad y rastreabilidad. Los modelos pueden implementarse de forma confiable en el borde, en local o en la nube. Una vez en producción, puede administrar el rendimiento de los modelos y luego reciclar de manera proactiva si se degrada el rendimiento. Puede implementar modelos en equipos locales, en [Azure VM](https://azure.microsoft.com/services/virtual-machines/), en Spark en [HDInsight](https://azure.microsoft.com/services/hdinsight/) o en clústeres de [Azure Container Service](https://azure.microsoft.com/services/container-service/) organizados por Kubernetes.  

**¿Qué es un “modelo”?**

Un modelo es el resultado de una ejecución de experimentación que se ha promovido para la administración de modelos. Un modelo que se registra en la cuenta de hospedaje se cuenta para su plan, incluidos los modelos actualizados a través del reciclaje o la iteración de versiones.

**¿Qué es un “modelo administrado”?**

Un modelo es el resultado de un proceso de entrenamiento y consiste en la aplicación de un algoritmo de aprendizaje automático a los datos de entrenamiento. Administración de modelos permite implementar modelos en forma de servicios web, administrar varias versiones de los modelos y supervisar su rendimiento y sus métricas. Los modelos “administrados” se han registrado en una cuenta de Administración de modelos de Azure Machine Learning. Por ejemplo, imagine que está intentando hacer una previsión de ventas. Durante la fase de experimentación, genera muchos modelos usando diferentes conjuntos de datos y algoritmos. Ha generado cuatro modelos con valores de precisión diferentes, pero opta por registrar solo el modelo que ofrece la precisión más alta. El modelo que se ha registrado se convierte en el primer modelo administrado.
 
**¿Qué es una “implementación”?**

Administración de modelos permite implementar modelos como contenedores de servicios web empaquetados en Azure. Estos servicios web se pueden invocar mediante las API de REST. Cada servicio web cuenta como una sola implementación, y el número total de implementaciones activas cuenta para su plan. Usando el ejemplo de previsión de ventas, al implementar el modelo más preciso, se incrementa el plan en una implementación. Si, a continuación, recicla e implementa otra versión, tiene dos implementaciones. Si determina que el modelo nuevo es mejor y elimina el original, se reduce en uno el número de implementaciones.  

**¿Qué recursos de proceso específicos están disponibles para las implementaciones?** 

Administración de modelos puede ejecutar las implementaciones de contenedores de Docker registradas en [Azure Container Service](https://azure.microsoft.com/services/container-service/), como [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), o en equipos locales. En breve se agregarán destinos de implementación adicionales. Tenga en cuenta que los recursos de proceso se facturan por separado según los precios de cada uno.     

**¿Puedo usar Administración de modelos de Azure Machine Learning para implementar modelos creados con herramientas que no sean el servicio Experimentación?**

Obtiene la mejor experiencia al implementar modelos creados mediante el servicio Experimentación. Sin embargo, puede implementar modelos generados mediante otras herramientas y marcos. Se admite una variedad de modelos, que incluyen MMLSpark, TensorFlow, Microsoft Cognitive Toolkit, scikit-learn, Keras, etc. 

**¿Pueden usarse recursos de Azure propios?**

Sí, el servicio Experimentación y Administración de modelos funcionan en conjunto con varios almacenes de datos de Azure, cargas de trabajo de proceso y otros servicios. Consulte la documentación técnica para obtener más información sobre los servicios de Azure necesarios.

**¿Se admiten escenarios de implementación tanto de forma local como en la nube?**

Sí. Se admiten escenarios de implementación locales y en la nube a través de contenedores Docker. Los destinos de ejecución locales incluyen: implementaciones de Docker de nodo único, [Microsoft SQL Server con servicios de ML](https://docs.microsoft.com/sql/advanced-analytics/r/r-services), Hadoop o Spark. También se admiten implementaciones en la nube a través de Docker, incluidas: implementaciones en clúster a través de Azure Container Service, y clústeres de Kubernetes, HDInsight o Spark. Los escenarios de borde se admiten a través de contenedores de Docker y Azure IoT Edge. 

**¿Es posible ejecutar una imagen de Docker que se creó mediante la CLI de Azure Machine Learning en otro host?**

Sí. Puede usar la imagen como servicio web en cualquier host de Docker siempre y cuando el host tenga suficientes recursos de proceso para hospedar la imagen de Docker.

**¿Se admite el reciclaje de los modelos implementados?**

Sí, puede implementar varias versiones del mismo modelo. Administración de modelos será compatible con las actualizaciones del servicio para todos los modelos e imágenes actualizados.

## <a name="workbench"></a>Workbench

**¿Qué es Azure Machine Learning Workbench?**

Azure Machine Learning Workbench es una aplicación complementaria creada para científicos de datos profesionales. Disponible para Windows y Mac, Machine Learning Workbench proporciona información general, administración y control para las soluciones de aprendizaje automático. Machine Learning Workbench incluye el acceso a marcos de inteligencia artificial de vanguardia de Microsoft y de la comunidad de código abierto. Se han incluido los kits de herramientas más populares para la ciencia de datos, tales como TensorFlow, Microsoft Cognitive Toolkit, Spark ML, scikit-learn y muchos más. También se ha habilitado la integración con IDE populares de la ciencia de datos, como Jupyter Notebook, PyCharm y Visual Studio Code. Machine Learning Workbench tiene funcionalidades integradas de preparación de datos para el rápido muestreo, comprensión y preparación de los datos, estén estructurados o no. La nueva herramienta de preparación de datos, denominada [PROSE](https://microsoft.github.io/prose/), se basa en tecnología de vanguardia de Microsoft Research.  

**¿Es Workbench un IDE?**

Nº Machine Learning Workbench se ha diseñado como complemento de los IDE populares, como Jupyter Notebook, Visual Studio Code y PyCharm, pero no es un IDE totalmente funcional. Machine Learning Workbench ofrece algunas funcionalidades básicas de edición de texto, pero la depuración, IntelliSense y otras funcionalidades de IDE normalmente usadas no son compatibles. Se recomienda usar el IDE favorito para el desarrollo, la edición y la depuración de código. 

**¿Hay un cargo por usar Azure Machine Learning Workbench?**

Nº Azure Machine Learning Workbench es una aplicación gratuita. Puede descargarla en tantas máquinas y para tantos usuarios como necesite. Para usar Azure Machine Learning Workbench, debe tener una cuenta de Experimentación. .  

**¿Se admiten las funcionalidades de línea de comandos?**

R: Sí, Azure Machine Learning ofrece una completa interfaz CLI. La CLI de Machine Learning se instala de manera predeterminada con Azure Machine Learning Workbench. También se proporciona como parte de Data Science Virtual Machine de Linux en Azure y se integrarán en la [CLI de Azure](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest)


**¿Puede usarse Jupyter Notebook con Workbench?**

Sí. Puede ejecutar Jupyter Notebook en Workbench, con Workbench como aplicación de hospedaje cliente, tal como utilizaría un explorador como cliente. 

**¿Qué kernels de Jupyter Notebook se admiten?**

R: La versión actual de Jupyter incluida con Workbench inicia un kernel de Python 3 y un kernel adicional para cada archivo de “runconfig” en la carpeta aml_config. Entre las configuraciones admitidas se incluyen:
- Python local
- Python en Docker local o remoto

## <a name="data-formats-and-capabilities"></a>Formatos de datos y funcionalidades

**¿Qué formatos de archivo se admiten actualmente para la ingesta de datos en Workbench?**

Las herramientas de preparación de datos de Workbench admiten actualmente la ingesta de los siguientes formatos: 
- Archivos delimitados, como CSV, TSV, etc.  
- Archivos con ancho fijo
- Texto sin formato
- Excel (.xls/.xlsx)
- Archivos JSON
- Archivos de Parquet 
- Archivos personalizados (scripts). Si su solución requiere la ingesta de datos de orígenes adicionales, puede utilizarse el código de Python para… 

**¿Actualmente qué ubicaciones de almacenamiento de datos se admiten?**

Para la versión preliminar pública, Workbench admite la ingesta de datos de: 
- Unidad de disco duro local o ubicación de almacenamiento de red asignada
- Azure Blob o Azure Storage (requiere una suscripción a Azure)
- Azure SQL Server
- Microsoft SQL Server


**¿Qué tipos de tratamientos, preparación y transformaciones de datos están disponibles?**

Para la versión preliminar pública, Workbench admite “Derivación de columnas por ejemplos”, “División de columnas por ejemplos”, “Text Clustering”, “Handle Missing Values” y muchos otros.  Workbench también admite la conversión de tipos de datos, la agregación de datos (RECUENTO, MEDIA, VARIANZA, etc.) y combinaciones de datos complejas. Para obtener una lista completa de las funcionalidades admitidas, consulte la documentación del producto. 

**¿Existen límites para el tamaño de datos, exigidos por Azure Machine Learning Workbench, Experimentación o Administración de modelos?**

R: No, los nuevos servicios no imponen ninguna limitación de datos. Sin embargo, existen limitaciones introducidas por el entorno en el que va a realizar la preparación de datos, el entrenamiento del modelo, la experimentación o la implementación. Por ejemplo, si tiene como destino un entorno local para el entrenamiento, está limitado por el espacio disponible en el disco duro. O bien, si el destino es HDInsight, está limitado por cualquier tamaño asociado o restricciones de proceso. 

## <a name="algorithms-and-libraries"></a>Algoritmos y bibliotecas

**¿Qué algoritmos se admiten en Azure Machine Learning Workbench?**

Nuestros productos y servicios en versión preliminar incluyen lo mejor de la comunidad de código abierto. Se admite una amplia variedad de algoritmos y bibliotecas, incluidos TensorFlow, scikit-learn, Apache Spark y Microsoft Cognitive Toolkit. Azure Machine Learning Workbench también empaqueta el paquete [revoscalepy de Microsoft](https://docs.microsoft.com/sql/advanced-analytics/python/what-is-revoscalepy).

**¿Cómo se relaciona Azure Machine Learning con Microsoft Cognitive Toolkit?**

[Microsoft Cognitive Toolkit](https://www.microsoft.com/cognitive-toolkit/) es uno de los muchos marcos compatibles con nuestras nuevas herramientas y servicios. Cognitive Toolkit es un kit de herramientas unificado de aprendizaje profundo que le permite consumir y combinar modelos populares de aprendizaje automático, incluidas redes neuronales profundas feedforward, redes convolucionales, de secuencia a secuencia y redes recurrentes. Para obtener más información sobre Microsoft Cognitive Toolkit, visite nuestra [documentación del producto](https://docs.microsoft.com/cognitive-toolkit/). 

## <a name="pricing-and-billing"></a>Precios y facturación

**¿Se cobra Azure Machine Learning durante la versión preliminar?** 

R: La aplicación Azure Machine Learning Workbench está disponible de forma gratuita para los suscriptores de Azure. El servicio Experimentación y Administración de modelos ofrecen niveles gratuitos, además de niveles de pago, disponibles con un descuento durante la versión preliminar pública.

**¿Los cargos van en función del número de experimentos que se ejecuten?**

No, el servicio Experimentación permite tantos experimentos como necesite. Los cargos se realizan únicamente en función del número de usuarios. Los recursos de proceso de Experimentación se cobran aparte.  Le recomendamos realizar varios experimentos para encontrar el mejor modelo para su solución. 

**¿Se cobrará en función del número de veces que se llame a los servicios web?**

Nº Se puede llamar a los servicios web con la frecuencia que sea necesaria, sin que ello afecte a la facturación de Administración de modelos. Usted tiene el control total para escalar sus implementaciones según la necesidad de sus aplicaciones.

**¿Cómo se puede escalar el número de unidades adquiridas en Administración de modelos de Azure Machine Learning?**

Puede aumentar o reducir el número de unidades usando la CLI o Azure Portal. 

**¿Cómo es una factura?**

Las facturas se crean a diario. En términos de facturación, un día comienza a medianoche (UTC). Las facturas se generan mensualmente. Se incurre en cargos independientes por cualquier servicio de Azure consumido junto con Azure Machine Learning. Los cargos pueden incluir, entre otras cosas: 
- Cargos de proceso
- HDInsight
- Azure Container Service
- Azure Container Registry 
- Azure Blob Storage
- Application Insights
- Azure Key Vault
- Visual Studio Team Services
- Centro de eventos de Azure
- Azure Stream Analytics. Para obtener más información o para ver una factura de ejemplo, visite nuestra página de precios. 

## <a name="support-and-training"></a>Soporte técnico y entrenamiento

**¿Dónde puedo recibir entrenamiento para Azure Machine Learning?**

El [Centro de documentación de Azure Machine Learning](./overview-what-is-azure-ml.md) hospeda tutoriales en vídeo y guías de procedimientos. Estas guías paso a paso presentan los servicios y explican el ciclo de vida de la ciencia de datos. Agregamos continuamente nuevo material al Centro de Machine Learning. Puede solicitar material de aprendizaje adicional en el foro de comentarios de los usuarios.

**¿Dónde puedo recibir soporte técnico para Azure Machine Learning?**

Para recibir soporte técnico, visite el servicio de soporte técnico de Azure y seleccione Machine Learning. Azure Machine Learning también tiene una comunidad activa en [StackOverflow](https://stackoverflow.com/questions/tagged/azure-machine-learning) etiquetada con “Azure-Machine-Learning”, supervisada por el equipo.  Se pueden enviar sugerencias y solicitudes de características al sitio de [comentarios de Azure Machine Learning](https://feedback.azure.com/forums/257792-machine-learning). 
