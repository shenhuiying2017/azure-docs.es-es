<properties 
	pageTitle="¿Qué es el Estudio de aprendizaje automático de Azure? | Microsoft Azure" 
	description="Información general de Estudio de aprendizaje automático, una herramienta de arrastrar y colocar para crear rápidamente modelos desde una biblioteca lista para usar de algoritmos y módulos." 
	keywords="azure machine learning,azure ml, ml studio"	
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
	ms.topic="article" 
	ms.date="04/22/2015" 
	ms.author="garye"/>

# ¿Qué es Estudio de aprendizaje automático de Azure?

Estudio de aprendizaje automático de Microsoft Azure es una herramienta de arrastrar y colocar que le permite crear, probar e implementar soluciones de análisis predictivo en sus datos. Estudio de aprendizaje automático publica modelos como servicios web que pueden utilizarse fácilmente en aplicaciones personalizadas o herramientas de BI como Excel.

Estudio de aprendizaje automático es el lugar en el que confluyen la ciencia de datos, el análisis predictivo, los recursos en la nube y sus datos.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## El área de trabajo interactivo de Estudio de aprendizaje automático

Para desarrollar un modelo de análisis predictivo, normalmente se utilizan datos de una o varias fuentes, se transforman y analizan los datos a través de diversas funciones estadísticas y de manipulación de datos y se genera un conjunto de resultados. Desarrollar un modelo como este es un proceso iterativo: a medida que se modifican las diversas funciones y sus parámetros, sus resultados convergen hasta que esté satisfecho con un modelo entrenado y efectivo.

**Estudio de aprendizaje automático de Azure** le proporciona un área de trabajo visual e interactiva para generar, probar e iterar con toda facilidad sobre un modelo de análisis predictivo. Se arrastran y colocan ***conjuntos de datos*** y ***módulos*** de análisis en un ***lienzo*** interactivo, conectándolos todos para formar un ***experimento*** que se ***ejecuta*** en Estudio de aprendizaje automático. Para iterar su diseño de modelo, se puede ***editar*** el experimento, ***guardar*** una copia si así se desea y ejecutarlo de nuevo. Cuando esté listo, puede ***publicar*** el experimento como un ***servicio web*** para que otros usuarios puedan tener acceso al modelo.

No se requiere ningún tipo de programación, basta con conectar visualmente conjuntos de datos y módulos para construir el modelo de análisis predictivo.

![Diagrama de Estudio de aprendizaje automático de Azure: crear experimentos, leer datos de muchos orígenes, escribir datos puntuados, modelos de escritura.][ml-studio-overview]

## Introducción a Estudio de aprendizaje automático

La primera vez que entre en Estudio de aprendizaje automático, verá las siguientes pestañas a la izquierda:

- **Página principal de Estudio**: un conjunto de vínculos a documentación y otros recursos.
- **EXPERIMENTOS**: los experimentos que se han creado, ejecutado y guardado como borradores. 
- **SERVICIOS WEB**: una lista de los experimentos publicados. 
- **CONFIGURACIÓN**: una colección de ajustes que puede utilizar para configurar la cuenta y los recursos. 

>[AZURE.NOTE]Al crear un experimento, se muestra una lista de trabajo de los conjuntos de datos y módulos disponibles a la izquierda del lienzo. Esta es la lista de componentes que se utiliza para construir el modelo.

## Componentes de un experimento

Un experimento consta de conjuntos de datos que proporcionan datos a módulos analíticos, que se conectan en conjunto para construir un modelo de análisis predictivo. En concreto, un experimento válido tiene estas características:

- Tiene al menos un conjunto de datos y un módulo. 
- Los conjuntos de datos pueden estar conectados solo a módulos. 
- Los módulos se pueden conectar a los conjuntos de datos o a otros módulos. 
- Todos los puertos de entrada de los módulos deben tener alguna conexión al flujo de datos. 
- Deben establecerse todos los parámetros requeridos para un módulo. 

Para obtener un ejemplo de creación de un experimento simple, consulte [Creación de un experimento sencillo en el Estudio de aprendizaje automático de Azure](machine-learning-create-experiment.md). Para obtener un tutorial más completo de la creación de una solución de análisis predictivos, consulte [Desarrollo de una solución predictiva con Aprendizaje automático de Azure](machine-learning-walkthrough-develop-predictive-solution.md).

### Conjuntos de datos

Un conjunto de datos son datos que se han cargado en Estudio de aprendizaje automático para utilizarse en el proceso de modelado. Estudio de aprendizaje automático incluye varios conjuntos de datos de ejemplo con los que puede experimentar, y puede cargar más a medida que los necesite. A continuación se muestran algunos ejemplos de los conjuntos de datos incluidos:

- **Datos sobre consumo de combustible por distancia recorrida para varios automóviles**: valores sobre consumo de combustible por distancia recorrida para automóviles identificados por el número de cilindros, los caballos de potencia, etc. 
- **Datos sobre cáncer de mama**: datos de diagnóstico de cáncer de mama. 
- **Datos de incendios forestales**: magnitud de los incendios forestales en el noreste de Portugal. 

A medida que genera un experimento, la lista de trabajo de conjuntos de datos está disponible a la izquierda del lienzo.

### Módulos

Un módulo es un algoritmo que puede aplicar sobre sus datos. Estudio de aprendizaje automático cuenta con diversos módulos que van desde las funciones de incorporación de datos hasta procesos de entrenamiento, puntuación y validación. A continuación se muestran algunos ejemplos de los módulos incluidos:

- [Convertir a ARFF][convert-to-arff]: convierte un conjunto de datos serializados de .NET a formato ARFF. 
- [Estadísticas elementales][elementary-statistics]: calcula estadísticas elementales como la media, la desviación estándar, etc. 
- [Regresión lineal][linear-regression]: crea un modelo de regresión lineal basado en un descenso de gradiente en línea. 
- [Puntuar modelo][score-model]: puntúa un modelo entrenado de clasificación o regresión. 

A medida que genera un experimento, la lista de trabajo de módulos está disponible a la izquierda del lienzo.

Un módulo puede tener un conjunto de parámetros que puede utilizar para configurar los algoritmos internos del módulo. Al seleccionar un módulo en el lienzo, los parámetros del módulo se muestran en el panel a la derecha del lienzo. Puede modificar los parámetros en ese panel para ajustar su modelo.


[ml-studio-overview]: ./media/machine-learning-what-is-ml-studio/azure-ml-studio-diagram.jpg


<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
 

<!---HONumber=58_postMigration-->