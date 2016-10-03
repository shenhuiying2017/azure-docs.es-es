<properties
	pageTitle="¿Qué es el proceso de ciencia de datos en equipos | Microsoft Azure"
	description="El proceso de ciencia de datos en equipos es un método sistemático cuyo objetivo es compilar aplicaciones inteligentes que utilicen el análisis avanzado."
	keywords="proceso de ciencia de datos, equipos de ciencia de datos"
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="jhubbard"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/19/2016"
	ms.author="bradsev" />


# ¿Qué es el proceso de ciencia de datos en equipos (TDSP)?

El proceso de ciencia de datos en equipos (TDSP) ofrece un enfoque sistemático para compilar aplicaciones inteligentes que permiten a los equipos de científicos de datos colaborar de manera eficaz durante todo el ciclo de vida de las actividades necesarias para convertir estas aplicaciones en productos.

En concreto, en estos momentos el TDSP proporciona a los equipos de ciencia de datos lo siguiente:

- **Metodología**: describe una secuencia de pasos que definen el ciclo de vida de desarrollo. Para ello, se ofrecen instrucciones sobre cómo definir el problema, analizar los datos pertinentes, crear y evaluar modelos predictivos y, posteriormente, implementar esos modelos en aplicaciones empresariales.
- **Recursos**: herramientas y tecnologías como la máquina virtual de ciencia de datos que tienen como objetivo simplificar la configuración de entornos para actividades de ciencia de datos, además de ofrecer instrucciones prácticas para incorporar nuevas tecnologías.

Este es el ciclo de vida de desarrollo del proceso de ciencia de datos en equipos.

![Diagrama: Proceso de ciencia de datos para equipos](./media/data-science-process-overview/data-science-process-for-teams-diagram.png)


El proceso es **iterativo**: la comprensión de las mejoras nuevas y existentes en el modelo evoluciona y requiere la adaptación de los pasos realizados anteriormente en la secuencia. El desarrollo de la organización existente y los procesos de planeación del proyecto se **adaptan fácilmente** para que actúen con la secuencia de pasos definida por el TDSP.

Los pasos del proceso se muestran en diagrama y se vinculan en la [ruta de aprendizaje de TDSP](https://azure.microsoft.com/documentation/learning-paths/data-science-process/) (se describen más abajo).


## Pasos de planeación y preparación

## P1. Planeación empresarial y tecnológica

Inicie un proyecto de análisis mediante la definición de los objetivos de negocio y los problemas. Se especifican en términos de **requisitos empresariales**. Un objetivo central de este paso es identificar las variables claves del negocio (previsión de ventas o la probabilidad de que un pedido sea fraudulento, por ejemplo) que el análisis necesita predecir para satisfacer estos requisitos. La planeación adicional suele ser esencial para desarrollar una comprensión de los **orígenes de datos** necesarios para abordar los objetivos del proyecto desde una perspectiva analítica. Por ejemplo, no es raro que descubra que necesitan los sistemas existentes tengan que recopilar y registrar tipos de datos adicionales para solucionar el problema y alcanzar los objetivos del proyecto. Para obtener instrucciones, consulte [Identificación de escenarios y planeamiento del procesamiento analítico avanzado de datos](machine-learning-data-science-plan-your-environment.md) y [Escenarios para análisis avanzado en Aprendizaje automático de Azure](machine-learning-data-science-plan-sample-scenarios.md).


## P2. Planeación y preparación de la infraestructura

En los entornos de análisis para el proceso de ciencia de datos en equipos actúan varios componentes:

- **Las áreas de trabajo de datos**, donde los datos está preparados para el análisis y modelado
- **Una infraestructura de procesamiento** para el procesamiento previo, la exploración y el modelado de datos
- **Una infraestructura de tiempo de ejecución** que controle los modelos analíticos y ejecute las aplicaciones de cliente inteligente que consumen los modelos

La infraestructura de análisis que es necesario configurar suele ser parte de un entorno independiente de los sistemas operativos principales. Pero por lo general aprovecha los datos de distintos sistemas dentro de la empresa, así como de orígenes externos a la empresa. La infraestructura de análisis puede estar exclusivamente en la nube, ser una instalación local o un híbrido de los dos. Para ver las opciones, consulte [Configuración de entornos de ciencia de datos para utilizarse en el proceso de ciencia de datos en equipos](machine-learning-data-science-environment-setup.md).


## Pasos del análisis:  

## 1\. Introducir los datos en la plataforma de datos

El primer paso consiste en llevar los datos pertinentes procedentes de diversos orígenes, ya sea de dentro o fuera de la empresa, a un entorno de análisis donde se puedan procesar los datos. El **formato** de los datos en el origen puede diferir del formato requerido en el destino. Así pues, habría que realizar alguna transformación de datos mediante las herramientas de ingesta. Para ver opciones, consulte [Carga de datos en entornos de almacenamiento para el análisis](machine-learning-data-science-ingest-data.md).

Además de la recopilación inicial de los datos, se necesitan muchas aplicaciones inteligentes para actualizar los datos periódicamente como parte de un proceso de aprendizaje continuo. Puede llevarse a cabo mediante la configuración de una **canalización de datos** o un flujo de trabajo. Esto pertenece a la parte iterativa del proceso, que incluye volver a generar y a evaluar los modelos analíticos usados por la aplicación inteligente que implementa la solución. Vea, por ejemplo, [Mover datos desde un servidor SQL Server local a SQL Azure con la Factoría de datos de Azure](machine-learning-data-science-move-sql-azure-adf.md).


## 2\. Explorar y visualizar los datos

El siguiente paso consiste en obtener un conocimiento más profundo de los datos mediante el análisis de las **estadísticas de resumen**, relaciones y mediante el uso de técnicas como la **visualización**. Aquí también es en donde se tratan los problemas de **calidad de datos** y de su integridad, como la inexistencia de valores, las diferencias de tipos de datos y las relaciones de datos incoherentes. Las transformaciones del procesamiento previo se usan para limpiar los datos sin procesar antes de que tengan lugar el análisis y el modelado. Para obtener una descripción, consulte [Tareas para preparar los datos para el aprendizaje automático mejorado](machine-learning-data-science-prepare-data.md).


## 3\. Crear y seleccionar las características

Los científicos de datos, en colaboración con expertos de dominio, deben identificar las características que capturan las propiedades importantes del conjunto de datos y que mejor se puedan usar para predecir las variables clave del negocio identificadas durante la planeación. Estas nuevas características pueden proceder de los datos existentes o pueden requerir que se recopilen datos adicionales. Este proceso se conoce como **ingeniería de características** y es uno de los pasos fundamentales de la creación de un sistema eficaz de análisis predictivo. Este paso requiere una combinación creativa de experiencia de dominio y de los conocimientos obtenidos en el paso de exploración de datos. Para ver instrucciones, consulte [Ingeniería de características en el proceso de análisis de Cortana](machine-learning-data-science-create-features.md).


## 4\. Crear y entrenar modelos de Machine Learning

Los científicos de datos generan modelos analíticos para predecir las variables clave identificadas por los requisitos de negocio definidos en el paso de planeación, mediante los datos que se limpiaron y caracterizaron. Los sistemas de aprendizaje automático admiten varios **algoritmos de modelado** que se pueden aplicar a una amplia variedad de casos. Para obtener instrucciones, consulte [Cómo elegir algoritmos para Azure Machine Learning](machine-learning-algorithm-choice.md).

Los científicos de datos deben elegir el modelo más adecuado para sus tareas de predicción y no es infrecuente que los resultados de varios modelos deben combinarse para obtener los mejores resultados. Los datos de entrada del modelado se suelen dividir aleatoriamente en tres partes:

- un conjunto de datos de aprendizaje
- un conjunto de datos de validación
- un conjunto de datos de prueba

Los modelos se generan mediante el **conjunto de datos de aprendizaje**. Se selecciona la combinación óptima de modelos (con parámetros ajustados) mediante la ejecución de los modelos y la medición de los errores de predicción del **conjunto de datos de validación**. Finalmente, el **conjunto de datos de prueba** se usa para evaluar el rendimiento del modelo elegido sobre datos independientes que no se usan para entrenar o validar el modelo elegido. Para ver procedimientos, consulte [Evaluación del rendimiento de un modelo en Aprendizaje automático de Azure](machine-learning-evaluate-model-performance.md).


## 5\. Implementar y utilizar los modelos del producto

Cuando ya se disponga de un conjunto de modelos que funcionan bien, pueden ser **operacionalizados** para que los consuman otras aplicaciones. Dependiendo de los requisitos empresariales, se realizan predicciones en **tiempo real** o por **lotes**. Para ser operacionalizados, los modelos tienen que exponerse con una **interfaz API abierta** que se pueda usar fácilmente desde diversas aplicaciones, como un sitio web en línea, hojas de cálculo, paneles o aplicaciones de línea de negocio o de back-end. Consulte [Implementar un servicio web de Aprendizaje automático de Azure](machine-learning-publish-a-machine-learning-web-service.md).


## Resumen y pasos siguientes

El [proceso de ciencia de datos en equipos](https://azure.microsoft.com/documentation/learning-paths/data-science-process/) se modela como una secuencia de pasos repetidos que **ofrecen asesoramiento** sobre las tareas necesarias para usar un análisis avanzado con el fin de compilar una aplicación inteligente. Cada paso también ofrece detalles sobre cómo usar las distintas tecnologías de Microsoft para completar las tareas descritas.

Aunque el TDSP no prescribe tipos específicos de artefactos de **documentación**, se recomienda documentar los resultados de la exploración de datos, el modelado y la evaluación, así como guardar el código correspondiente para que el análisis pueda repetirse cuando sea necesario. Esto también permite la reutilización de los trabajos de análisis cuando se trabaja en otras aplicaciones que implican datos y tareas de predicción similares.

También se proporcionan tutoriales completos que muestran todos los pasos del proceso en **escenarios concretos**. Aparecen con las descripciones en miniatura del tema [Tutoriales del proceso de ciencia de datos en equipos](data-science-process-walkthroughs.md).

<!---HONumber=AcomDC_0921_2016-->