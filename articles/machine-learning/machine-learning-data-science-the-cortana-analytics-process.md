<properties 
	pageTitle="¿Qué es el proceso de análisis de Cortana? | Microsoft Azure" 
	description="El proceso de análisis de Cortana es un método sistemático de ciencia de datos para la creación de aplicaciones inteligentes que se aprovechan del análisis avanzado." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev"
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/05/2015" 
	ms.author="bradsev;gopitk" />


# ¿Qué es el proceso de análisis de Cortana (CAP)?

El proceso de análisis de Cortana (CAP) es un método sistemático de ciencia de datos que describe una secuencia de pasos que aprovecha el análisis avanzado para crear aplicaciones inteligentes. Los pasos del CAP ofrecen **orientación** acerca de cómo definir el problema, analizar los datos pertinentes, compilar y evaluar modelos de predicción y, finalmente, implementar esos modelos en aplicaciones inteligentes.

Estos son los pasos del **proceso de análisis de Cortana**:

![Flujo de trabajo del proceso de análisis de Cortana](./media/machine-learning-data-science-the-cortana-analytics-process/CAP-workflow.png)

El proceso es **iterativo**: la comprensión de las mejoras nuevas y existentes en el modelo evoluciona y requiere la adaptación de los pasos realizados anteriormente en la secuencia. El desarrollo de la organización existente y los procesos de planeación del proyecto se **adaptan fácilmente** para que actúen con la secuencia de pasos definida por el CAP.

A continuación se describen los pasos del proceso.

## Pasos de preparación 

## P1. Planeación del proyecto de análisis 

Inicie un proyecto de análisis mediante la definición de los objetivos de negocio y los problemas. Se especifican en términos de **requisitos empresariales**. Un objetivo central de este paso es identificar las variables claves del negocio (previsión de ventas o la probabilidad de que un pedido sea fraudulento, por ejemplo) que el análisis necesita predecir para satisfacer estos requisitos. La planeación adicional suele ser esencial para desarrollar una comprensión de los **orígenes de datos** necesarios para abordar los objetivos del proyecto desde una perspectiva analítica. Por ejemplo, no es raro que descubra que necesitan los sistemas existentes tengan que recopilar y registrar tipos de datos adicionales para solucionar el problema y alcanzar los objetivos del proyecto.

## P2. Configuración del entorno de análisis 

Un entorno de análisis para el proceso de análisis de Cortana implica varios componentes:

- **Las áreas de trabajo de datos**, donde los datos está preparados para el análisis y modelado 
- **Una infraestructura de procesamiento** para el procesamiento previo, la exploración y el modelado de datos
- **Una infraestructura de tiempo de ejecución** que controle los modelos analíticos y ejecute las aplicaciones de cliente inteligente que consumen los modelos  

La infraestructura de análisis que es necesario configurar suele ser parte de un entorno independiente de los sistemas operativos principales. Pero por lo general aprovecha los datos de distintos sistemas dentro de la empresa, así como de orígenes externos a la empresa. La infraestructura de análisis puede estar exclusivamente en la nube, ser una instalación local o un híbrido de los dos.

## Pasos del análisis:  

## 1\. Introducción de datos en el entorno de análisis 

El primer paso consiste en llevar los datos pertinentes procedentes de diversos orígenes, ya sea de dentro o fuera de la empresa, a los entornos de análisis destinados a procesar los datos. El **formato** de los datos en el origen puede diferir del formato requerido en el destino. Por lo que habría que realizarse alguna transformación de datos mediante las herramientas de recopilación.

Además de la recopilación inicial de los datos, se necesitan muchas aplicaciones inteligentes para actualizar los datos periódicamente como parte de un proceso de aprendizaje continuo. Puede llevarse a cabo mediante la configuración de una **canalización de datos** o flujo de trabajo. Esto pertenece a la parte iterativa del proceso, que incluye volver a generar y a evaluar los modelos analíticos usados por la aplicación inteligente que implementa la solución.


## 2\. Exploración y procesamiento previo de los datos 

El siguiente paso es obtener un conocimiento más profundo de los datos mediante el análisis de las **estadísticas de resumen**, relaciones y mediante el uso de técnicas como la **visualización**. Aquí también es en donde se tratan los problemas de **calidad de datos** y de su integridad, como valores ausentes, diferencias de tipos de datos y relaciones de datos incoherentes. Las transformaciones del procesamiento previo se usan para limpiar los datos sin procesar antes de que tengan lugar el análisis y el modelado.


## 3\. Desarrollo de características 

Los científicos de datos, en colaboración con expertos de dominio, deben identificar las características que capturan las propiedades importantes del conjunto de datos y que mejor se puedan usar para predecir las variables clave del negocio identificadas durante la planeación. Estas nuevas características pueden proceder de los datos existentes o pueden requerir que se recopilen datos adicionales. Este proceso se conoce como **ingeniería de características** y es uno de los pasos fundamentales de la creación de un sistema eficaz de análisis predictivo. Este paso requiere una combinación creativa de experiencia de dominio y de los conocimientos obtenidos en el paso de exploración de datos.


## 4\. Creación de modelos de predicción 

Los científicos de datos generan modelos analíticos para predecir las variables clave identificadas por los requisitos de negocio definidos en el paso de planeación, mediante los datos que se limpiaron y caracterizaron. Los sistemas de aprendizaje automático admiten varios **algoritmos de modelado** que se pueden aplicar a una amplia variedad de casos.

Los científicos de datos deben elegir el modelo más adecuado para sus tareas de predicción y no es infrecuente que los resultados de varios modelos deben combinarse para obtener los mejores resultados. Los datos de entrada del modelado se suelen dividir aleatoriamente en tres partes:

- un conjunto de datos de aprendizaje 
- un conjunto de datos de validación 
- un conjunto de datos de prueba 

Los modelos se generan mediante el **conjunto de datos de aprendizaje**. Se selecciona la combinación óptima de modelos (con parámetros ajustados) mediante la ejecución de los modelos y la medición de los errores de predicción del **conjunto de datos de validación**. Finalmente el **conjunto de datos de prueba** se usa para evaluar el rendimiento del modelo elegido sobre datos independientes que no se usan para formar o validar el modelo elegido.


## 5\. Implementación y consumo de modelos 

Cuando ya se disponga de un conjunto de modelos que funcionan bien, pueden ser **operacionalizados** para que los consuman otras aplicaciones. Dependiendo de los requisitos empresariales, se realizan predicciones en **tiempo real** o por **lotes**. Para ser operacionalizados, los modelos tienen que exponerse con una **interfaz API abierta** que se pueda usar fácilmente desde diversas aplicaciones, como un sitio web en línea, hojas de cálculo, paneles o aplicaciones de línea de negocio o de back-end.

## Resumen y pasos siguientes

El proceso de análisis de Cortana se modela como una secuencia de pasos repetidos que **ofrecen asesoramiento** en las tareas necesarias para usar análisis avanzado para crear aplicaciones inteligentes. Cada paso también ofrece detalles sobre cómo usar las distintas tecnologías de Microsoft para completar las tareas descritas.

Aunque el proceso de análisis de Cortana no prescribe tipos específicos de artefactos de **documentación**, es una práctica recomendada documentar los resultados de la exploración de datos, el modelado y la evaluación y guardar el código correspondiente para el análisis pueda repetirse cuando sea necesario. Esto también permite la reutilización de los trabajos de análisis cuando se trabaja en otras aplicaciones que implican datos y tareas de predicción similares.

También se proporcionan tutoriales completos que muestran todos los pasos del proceso en **escenarios concretos**.

 

<!---HONumber=Oct15_HO2-->