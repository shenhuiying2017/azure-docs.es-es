---
title: "Diseño y selección de características en Machine Learning de Azure | Microsoft Docs"
description: "Explica el propósito del diseño y la selección de características, además de dar ejemplos de su rol en el proceso de mejora de los datos del aprendizaje automático."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 9ceb524d-842e-4f77-9eae-a18e599442d6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: deprecated
ms.date: 01/18/2017
ms.author: zhangya;bradsev
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: machine-learning-data-science-create-features
translationtype: Human Translation
ms.sourcegitcommit: ba61d00f277af579c87a130336ead9879b82a6de
ms.openlocfilehash: c6b88355df430e78594fc1283c9df01ad6e27e20


---
# <a name="feature-engineering-and-selection-in-azure-machine-learning"></a>Diseño y selección de características en Aprendizaje automático de Azure
En este tema se explica el propósito del diseño y la selección de características en el proceso de mejora de los datos del aprendizaje automático. Este tema muestra lo que implican estos procesos con ejemplos que ofrece Azure Machine Learning Studio.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Los datos de entrenamiento que se usan en el aprendizaje automático a menudo pueden mejorarse gracias a la selección o extracción de características desde los datos sin procesar que se han recopilado. Un ejemplo de una característica diseñada en el contexto de aprender cómo clasificar las imágenes de caracteres manuscritos es un mapa de densidad de bits construido a partir de los datos de distribución de bits sin procesar. Este mapa puede ayudar a ubicar los bordes de los caracteres de manera más eficiente que la distribución sin procesar.

Las funciones diseñadas y seleccionadas aumentan la eficiencia del proceso de entrenamiento, el que intenta extraer la información clave contenida en los datos. También mejoran la eficacia de estos modelos para clasificar los datos de entrada de manera precisa y para predecir resultados de interés de manera más sólida. El diseño y la selección de características también se pueden combinar para que sea posible hacer un mejor seguimiento computacional del aprendizaje. Para ello, mejora y luego reduce el número de características que se necesitan para calibrar o entrenar un modelo. Matemáticamente hablando, las características seleccionadas para entrenar el modelo son un conjunto mínimo de variables independientes que explican los patrones existentes en los datos y, a continuación, predicen correctamente los resultados.

El diseño y la selección de las características es solo una parte de un proceso más grande, el que normalmente consta de cuatro pasos:

* Colección de datos
* Mejora de datos
* Construcción del modelo
* Posprocesamiento

El diseño y la selección componen el paso de mejora de datos del aprendizaje automático. Para nuestros propósitos, es posible distinguir tres aspectos de este proceso:

* **Preprocesamiento de los datos**: este proceso intenta asegurarse de que los datos recopilados estén limpios y sean coherentes. Incluye tareas como la integración de varios conjuntos de datos, el control de los datos que faltan, el control de datos incoherentes y la conversión de los tipos de datos.
* **Diseño de características**: este proceso intenta crear características pertinentes adicionales a partir de características existentes sin procesar en los datos y mejorar la eficacia predictiva del algoritmo de aprendizaje.
* **Selección de características**: este proceso selecciona el subconjunto de claves de las características de datos originales en un intento por reducir la dimensionalidad del problema de entrenamiento.

En este tema solo se abarcan los aspectos de diseño y selección de funciones del proceso de mejora de los datos. Para obtener información adicional sobre el paso de preprocesamiento de los datos, vea el vídeo [Pre-processing data in Azure Machine Learning Studio](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) (Preprocesamiento de datos en Azure Machine Learning Studio).

## <a name="creating-features-from-your-data--feature-engineering"></a>Creación de características a partir de sus datos: diseño de características
Los datos de entrenamiento constan de una matriz compuesta de ejemplos (registros u observaciones almacenados en filas), cada uno de los cuales cuenta con un conjunto de características (variables o campos almacenados en columnas). Se espera que las características especificadas en el diseño experimental caractericen los patrones de los datos. A pesar de que muchos de los campos de datos sin procesar se pueden incluir directamente en el conjunto de características seleccionado que se usa para entrenar un modelo, con frecuencia es preciso construir características adicionales (diseñadas) a partir de las características existentes en los datos sin procesar para generar un conjunto de datos de entrenamiento mejorado.

¿Qué tipo de características se deben crear para mejorar el conjunto de datos cuando se entrena un modelo? Las características diseñadas que mejoran el entrenamiento proporcionan información que ayuda a diferenciar de mejor manera los patrones de los datos. Se espera que las características nuevas den información adicional que no está claramente capturada o no resulta fácilmente reconocible en el conjunto de características original o existente, pero este proceso es casi un arte. Las decisiones acertadas y productivas a menudo requieren cierto conocimiento especializado.

Al comenzar con Azure Machine Learning, la forma más fácil de comprender este proceso es plantearlo de manera concreta, con los ejemplos que ofrece Machine Learning Studio. Aquí se muestran dos ejemplos:

* Un ejemplo de regresión ([predicción del número de bicicletas alquiladas](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4)) en un experimento supervisado en el que se conocen los valores del objetivo
* Un ejemplo de clasificación de minería de texto con [Hash de características][feature-hashing]

### <a name="example-1-adding-temporal-features-for-a-regression-model"></a>Ejemplo 1: Incorporación de características temporales para un modelo de regresión
Usemos el experimento "Previsión de demanda de bicicletas" en Azure Machine Learning Studio para mostrar cómo diseñar funciones para una tarea de regresión. El objetivo de este experimento es predecir la demanda de bicicletas, es decir, el número de bicicletas alquiladas dentro de un mes, día u hora específico. El conjunto de datos **Bike Rental UCI data set** se usa como los datos de entrada sin procesar.

Este conjunto de datos se basa en datos reales provenientes de la empresa Capital Bikeshare que mantiene una red de alquiler de bicicletas en Washington DC, en Estados Unidos. El conjunto de datos representa el número de bicicletas alquiladas dentro de una hora específica durante un día en el año 2011 a 2012 y contiene 17379 filas y 17 columnas. El conjunto de características sin procesar contiene condiciones climáticas (temperatura, humedad, velocidad del viento) y el tipo de día (festivo, día de semana). El campo para la predicción es **cnt**, un contador que representa las bicicletas alquiladas dentro de una hora específica y cuyos intervalos van de 1 a 977.

Para construir características eficaces en los datos de entrenamiento, se crean cuatro modelos de regresión con el mismo algoritmo, pero con cuatro conjuntos de datos de entrenamiento distintos. Los cuatro conjuntos de datos representan los mismos datos de entrada sin procesar, pero con un número creciente del conjunto de características. Estas características se agrupan en cuatro categorías:

1. A = características de clima + festivo + día de semana + fin de semana correspondiente al día de la predicción
2. B = el número de bicicletas alquiladas en cada una de las 12 horas anteriores
3. C = el número de bicicletas alquiladas en cada uno de los 12 días anteriores a la misma hora
4. D = el número de bicicletas arrendadas en cada una de las 12 semanas anteriores a la misma hora y el mismo día

Aparte del conjunto de funciones A, que ya existe en los datos sin procesar originales, los otros tres conjuntos de funciones se crean mediante el proceso de diseño de funciones. El conjunto de funciones B captura la demanda de bicicletas reciente. El conjunto de características C captura la demanda de bicicletas en una hora específica. El conjunto de características D captura la demanda de bicicletas en una hora específica y un día de la semana específico. Cada uno de los cuatro conjuntos de datos de entrenamiento incluye, respectivamente, el conjunto de características A, A+B, A+B+C y A+B+C+D.

En el experimento de Azure Machine Learning, estos cuatro conjuntos de datos de entrenamiento se forman a través de cuatro ramas del conjunto de datos de entrada procesado previamente. Con la excepción de la rama que se encuentra más a la izquierda, cada una de estas ramas contiene un módulo [Ejecutar script R][execute-r-script], en el que un conjunto de características derivadas (conjuntos de características B, C y D) se construye y anexa respectivamente al conjunto de datos importado. En la siguiente figura se muestra el script de R que se usa para crear el conjunto de características B en la segunda rama a la izquierda.

![Creación de un conjunto de características](./media/machine-learning-feature-selection-and-engineering/addFeature-Rscripts.png)

En la tabla siguiente se resume la comparación de los resultados de rendimiento de los cuatro modelos. Las características A+B+C muestran los mejores resultados. Observe que la tasa de errores disminuye cuando se incluyen conjuntos de características adicionales en los datos de entrenamiento. Esto comprueba nuestra presunción con respecto a que los conjuntos de características B y C dan información pertinente adicional para la tarea de regresión. Pero, agregar el conjunto de características D no parece reducir de ningún modo la tasa de errores.

![Comparar los resultados de rendimiento](./media/machine-learning-feature-selection-and-engineering/result1.png)

### <a name="a-nameexample2a-example-2-creating-features-in-text-mining"></a><a name="example2"></a> Ejemplo 2: Creación de características en minería de texto
El diseño de características se aplica ampliamente en las tareas relacionadas con la minería de texto, como la clasificación de documentos y el análisis de opiniones. Por ejemplo, si desea clasificar documentos en varias categorías, una hipótesis típica es que las palabras o frases incluidas que se encuentran en una categoría de documento tienen menos probabilidades de presentarse en otra categoría de documento. Dicho de otro modo, la frecuencia de la distribución de palabras o frases puede caracterizar distintas categorías de documento. En las aplicaciones de minería de texto, es necesario el proceso de diseño de características para crear las características que implican las frecuencias de palabras o frases; esto se debe a que partes individuales de contenidos de texto normalmente sirven como datos de entrada.

Para llevar a cabo esta tarea, se aplica una técnica llamada *hash de características* que permite convertir eficazmente las características arbitrarias de texto en índices. En lugar de asociar cada característica de texto (palabras o frases) a un índice determinado, este método funciona mediante la aplicación de una función de hash a las características y el uso de sus valores de hash como índices directamente.

En Azure Machine Learning, existe un módulo de [Hash de características][feature-hashing] que crea estas características de palabras o frases. La figura siguiente muestra un ejemplo del uso de este módulo. El conjunto de datos de entrada contiene dos columnas: la clasificación de libro, que va de 1 a 5, y el contenido mismo de la reseña. El objetivo de este módulo de [Hash de características][feature-hashing] es recuperar características nuevas que muestran la frecuencia de repetición de las palabras o frases correspondientes dentro de la reseña de ese libro en especial. Para usar este módulo, es necesario realizar los siguientes pasos:

1. Seleccione la columna que contiene el texto de entrada (**Col2** en este ejemplo).
2. Establezca el *Tamaño de bits de hash* en 8, lo que significa que se crearán 2^8=256 características. La palabra o frase en el texto tendrá hash en 256 índices. El parámetro *Tamaño de bits de hash* va de 1 a 31. Si el parámetro se establece en un número mayor, es menos probable que a las palabras o frases se les aplique un algoritmo hash en el mismo índice.
3. Establezca el parámetro *N-gramas* en 2. Con esto se recupera la frecuencia de repetición de unigramas (una característica para cada palabra única) y bigramas (unas características para cada par de palabras adyacentes) a partir del texto de entrada. El parámetro *N-gramas* va de 0 a 10, lo que indica el número máximo de palabras secuenciales que se incluirán en una característica.  

![Módulo de hash de características](./media/machine-learning-feature-selection-and-engineering/feature-Hashing1.png)

La figura siguiente muestra cómo se ven estas nuevas funciones.

![Ejemplo de hash de características](./media/machine-learning-feature-selection-and-engineering/feature-Hashing2.png)

## <a name="filtering-features-from-your-data--feature-selection"></a>Filtrado de características desde sus datos: selección de características
La *selección de características* es un proceso que normalmente se aplica a la construcción de conjuntos de datos de entrenamiento para tareas de modelado predictivo, como las tareas de clasificación o de regresión. El objetivo es seleccionar un subconjunto de las características a partir del conjunto de datos original que reduce sus dimensiones al usar un conjunto de características mínimo para que represente la cantidad máxima de varianza en los datos. Este subconjunto de funciones contiene las únicas funciones que se incluirán para entrenar el modelo. La selección de características tiene dos propósitos principales:

* La selección de características a menudo aumenta la precisión de la clasificación a través de la eliminación de características irrelevantes, redundantes o altamente correlacionadas.
* La selección de características disminuye la cantidad de características, lo que hace que el proceso de entrenamiento del modelo sea más eficiente. Esto resulta especialmente importante cuando se trata de sistemas aprendices que son costosos de entrenar, como las máquinas de vectores de soporte.

A pesar de que la selección de características pretende disminuir la cantidad de estas en el conjunto de datos que se usa para entrenar el modelo, no es frecuente referirse a ella con el término *reducción de la dimensionalidad*. Los métodos de selección de características extraen un subconjunto de las características originales de los datos sin cambiarlas.  Los métodos de reducción de dimensionalidad emplean características diseñadas que pueden transformar las características originales y, de ese modo, modificarlas. Algunos ejemplos de métodos de reducción de dimensionalidad incluyen el análisis del componente principal, el análisis de correlación canónica y la descomposición en valores singulares.

Una categoría ampliamente aplicada de los métodos de selección de características en un contexto supervisado se llama selección de características basada en filtro. Mediante la evaluación de la correlación entre cada característica y el atributo de destino, estos métodos aplican una medida estadística para asignar una puntuación a cada característica. A continuación, las características se clasifican según la puntuación, que puede usar con el fin de establecer el umbral para conservar o eliminar una característica específica. Algunos ejemplos de las medidas estadísticas que se usan en estos métodos incluyen la correlación de Pearson, la información mutua y la prueba de Chi-cuadrado.

Azure Machine Learning Studio incluye módulos para la selección de características. Tal como aparece en la figura siguiente, estos módulos incluyen [Selección de características basada en filtros][filter-based-feature-selection] y [Análisis discriminante lineal de Fisher][fisher-linear-discriminant-analysis].

![Ejemplo de selección de características](./media/machine-learning-feature-selection-and-engineering/feature-Selection.png)

Por ejemplo, use el módulo de [Selección de características basada en filtros][filter-based-feature-selection] con el ejemplo de minería de datos de texto descrito anteriormente. Suponga que desea crear un modelo de regresión una vez creado un conjunto de 256 características mediante el módulo de [Hash de características][feature-hashing] y que la variable de respuesta es la **Col1** y representa una clasificación de las reseñas de libros, que van desde 1 a 5. Establezca el **Método de puntuación de características** en **Correlación de Pearson**, la **Columna de destino** en **Col1** y el **Número de características deseadas** en **50**. A continuación, el módulo de [Selección de funciones basada en filtros][filter-based-feature-selection] generará un conjunto de datos con 50 funciones, junto con el atributo de destino **Col1**. La figura siguiente muestra el flujo de este experimento y los parámetros de entrada.

![Ejemplo de selección de características](./media/machine-learning-feature-selection-and-engineering/feature-Selection1.png)

La figura siguiente muestra los conjuntos de datos resultantes. Cada característica recibe una puntuación según la correlación de Pearson entre sí misma y el atributo de destino **Col1**. Las características con las mayores puntuaciones se conservan.

![Conjuntos de datos de selección de características basada en filtros](./media/machine-learning-feature-selection-and-engineering/feature-Selection2.png)

La figura siguiente muestra las puntuaciones correspondientes de las características seleccionadas.

![Puntuaciones de las características seleccionadas](./media/machine-learning-feature-selection-and-engineering/feature-Selection3.png)

Mediante la aplicación de este módulo de [Selección de características basada en filtros][filter-based-feature-selection], se seleccionan 50 de las 256 características, debido a que tienen más características correlacionadas con la variable de destino **Col1**, según el método de puntuación **Correlación de Pearson**.

## <a name="conclusion"></a>Conclusión
El diseño y la selección de características son dos pasos que se suelen completar para preparar los datos de entrenamiento cuando se crea un modelo de aprendizaje automático. Normalmente, el diseño de características se aplica primero para generar características adicionales y, a continuación, se realiza el paso de selección de características para eliminar aquellas que son irrelevantes, redundantes o altamente correlacionadas.

No siempre es necesario realizar el diseño o la selección de funciones. Que sea o no necesario depende de los datos que se tienen o que hay que recopilar, del algoritmo elegido y del objetivo del experimento.

<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/



<!--HONumber=Dec16_HO2-->


