---
title: "Creación de modelos de análisis de texto en Azure Machine Learning Studio | Microsoft Docs"
description: "Cómo crear modelos de análisis de texto en Azure Machine Learning Studio mediante módulos de preprocesamiento de texto, n-gramas o hash de características"
services: machine-learning
documentationcenter: 
author: rastala
manager: jhubbard
editor: 
ms.assetid: 08cd6723-3ae6-4e99-a924-e650942e461b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: roastala
ms.openlocfilehash: a43dc7162be4b2f3cfd122c6a87921caf2e3e418
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2018
---
# <a name="create-text-analytics-models-in-azure-machine-learning-studio"></a>Creación de modelos de análisis de texto en Azure Machine Learning Studio
Puede usar Azure Machine Learning para generar y aplicar modelos de análisis de texto. Estos modelos pueden ayudarle a resolver, por ejemplo, problemas de clasificación de documentos o de análisis de opinión.

En un experimento de análisis de texto, normalmente debería:

1. Limpiar y preprocesar el conjunto de datos de texto
2. Extraer vectores numéricos de características del texto preprocesado
3. Entrenar un modelo de clasificación o regresión
4. Puntuar y validar el modelo
5. Implementar el modelo en producción

En este tutorial, aprenderá estos pasos a medida que se explique un modelo de análisis de opinión mediante el conjunto de datos de Amazon Book Reviews (consulte el trabajo de investigación, “Biographies, Bollywood, Boom-boxes and Blenders: Domain Adaptation for Sentiment Classification” ("Biografías, Bollywoood, equipos de sonido y licuadoras") de John Blitzer, Mark Dredze y Fernando Pereira; Asociación de Lingüística Informática (ACL), 2007. Este conjunto de datos consta de las puntuaciones de revisión (1-2 o 4-5) y un texto de forma libre. El objetivo es predecir la puntuación de revisión: baja (1 - 2) o alta (4-5).

Puede encontrar los experimentos citados en este tutorial en la Galería de Azure AI:

[Predict Book Reviews](https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-1)

[Predict Book Reviews - Predictive Experiment](https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Paso 1: Limpiar y preprocesar el conjunto de datos de texto
El experimento comienza dividiendo las puntuaciones de revisión en depósitos con las categorías baja y alta para formular el problema como una clasificación de dos clases. Se utilizarán los módulos [Editar metadatos](https://msdn.microsoft.com/library/azure/dn905986.aspx) y [Agrupar valores categóricos](https://msdn.microsoft.com/library/azure/dn906014.aspx).

![Crear etiqueta](./media/text-analytics-module-tutorial/create-label.png)

A continuación, se limpia el texto mediante el módulo de [preprocesamiento de texto](https://msdn.microsoft.com/library/azure/mt762915.aspx) . La limpieza reduce el ruido en el conjunto de datos, lo cual le ayuda a encontrar las características más importantes y a mejorar la precisión del modelo final. Se eliminan las palabras irrelevantes: palabras comunes como "el" o "un", y números, caracteres especiales, caracteres duplicados, direcciones de correo electrónico y direcciones URL. También se convierte el texto a minúsculas, se lematizan las palabras y se detectan los límites de las oraciones que después se indican mediante el símbolo "|||" en el texto preprocesado.

![preprocesamiento de texto](./media/text-analytics-module-tutorial/preprocess-text.png)

¿Qué ocurre si desea utilizar una lista personalizada de palabras irrelevantes? Puede pasarla como entrada opcional. También puede usar expresiones regulares personalizadas de sintaxis de C# para reemplazar subcadenas y quitar palabras en partes del texto: sustantivos, verbos o adjetivos.

Una vez completado el preprocesamiento, se dividen los datos en conjuntos de entrenamiento y de pruebas.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Paso 2: Extraer vectores numéricos de características del texto preprocesado
Para crear un modelo de datos de texto, normalmente es necesario convertir texto de forma libre en vectores numéricos de características. En este ejemplo, se utilizará el módulo de [extracción de características de n-grama del texto](https://msdn.microsoft.com/library/azure/mt762916.aspx) para transformar los datos de texto a este formato. Este módulo toma una columna de palabras separadas por espacios en blanco y procesa un diccionario de palabras, o n-gramas de palabras, que aparecerán en el conjunto de datos. A continuación, cuenta cuántas veces aparece cada palabra, o n-grama, en cada registro y crea los vectores de características a partir de los recuentos. En este tutorial, se establece el tamaño de n-grama en 2, por lo que nuestros vectores de características incluirán palabras individuales o combinaciones de dos palabras correlativas.

![Extraer n-gramas](./media/text-analytics-module-tutorial/extract-ngrams.png)

Se aplica el factor de ponderación TF*IDF (Frecuencia de términos, Frecuencia inversa de documento) a los recuentos de n-gramas. Este método suma la ponderación de las palabras que aparecen frecuentemente en un único registro pero que son poco frecuentes en todo el conjunto de datos. Otras opciones incluyen la ponderación binaria, de TF y de gráfico.

A menudo, estas características de texto tienen una alta dimensionalidad. Por ejemplo, si el conjunto tiene 100 000 palabras únicas, el espacio de características tendrá 100 000 dimensiones o incluso más si se usan n-gramas. El módulo de extracción de características de n-grama ofrece un conjunto de opciones para reducir la dimensionalidad. Puede elegir la exclusión de palabras que sean cortas o largas, o que sean demasiado raras o demasiado frecuentes para tener un valor predictivo significativo. En este tutorial, se excluyen los n-gramas que aparecen en menos de 5 registros o en más del 80 % de los registros.

Además, puede utilizar la selección de características para seleccionar solo las características más correlacionadas con el destino de la predicción. Se usa la selección de características chi cuadrado para seleccionar 1000 características. Puede ver el vocabulario de palabras o n-gramas seleccionadas haciendo clic en el resultado apropiado del módulo Extraer n-gramas.

Como alternativa al uso del módulo de extracción de características de n-grama, puede usar el módulo de hash de características. Tenga en cuenta que el módulo de [hash de características](https://msdn.microsoft.com/library/azure/dn906018.aspx) no tiene funcionalidades de selección de características de compilación ni ponderación TF*IDF.

## <a name="step-3-train-classification-or-regression-model"></a>Paso 3: Entrenar un modelo de clasificación o regresión
El texto se ha transformado ya en columnas de características numéricas. El conjunto de datos todavía contiene columnas de cadenas de las fases anteriores, por lo que se utilizará la opción Select Columns in Dataset (Seleccionar columnas en el conjunto de datos) para excluirlas.

A continuación, se utilizará la [regresión logística de dos clases](https://msdn.microsoft.com/library/azure/dn905994.aspx) para predecir el destino: puntuación de revisión alta o baja. En este punto, el problema de análisis de texto se ha convertido en un problema de clasificación normal. Puede utilizar las herramientas disponibles en Azure Machine Learning para mejorar el modelo. Por ejemplo, puede experimentar con diferentes clasificadores para averiguar el grado de precisión de los resultados que proporcionan o usar el ajuste de hiperparámetros para mejorar la precisión.

![Entrenar y puntuar](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Paso 4: Puntuar y validar el modelo
¿Cómo se valida el modelo entrenado? Se puntúa en relación con el conjunto de datos de prueba y se evalúa la precisión. Sin embargo, el modelo aprendió el vocabulario de n-gramas y sus ponderaciones del conjunto de datos de entrenamiento. Por lo tanto, debemos usar ese vocabulario y esas ponderaciones al extraer características de los datos de prueba en lugar de crear el vocabulario de nuevo. Por consiguiente, se agregará el módulo de extracción de características de n-gramas a la rama de puntuación del experimento, se conectará el vocabulario de salida de la rama de entrenamiento y se establecerá el modo de vocabulario en solo lectura. También se deshabilitará el filtrado de n-gramas según la frecuencia estableciendo el mínimo en 1 instancia y el máximo en el 100 % y se desactivará la selección de características.

Después de que las columnas de texto de los datos de prueba se hayan transformado en columnas de características numéricas, se excluirán las columnas de cadenas de las fases anteriores al igual que en la rama de entrenamiento. A continuación, se usará el módulo de puntuación de modelos para realizar predicciones y el módulo de evaluación de modelos para evaluar la precisión.

## <a name="step-5-deploy-the-model-to-production"></a>Paso 5: Implementar el modelo en producción
El modelo está casi listo para implementarse en producción. Cuando se implementa como servicio web, toma la cadena de texto de forma libre como entrada y devuelve una predicción de puntuación "alta" o "baja". Utiliza el vocabulario de n-gramas aprendido para transformar el texto en características y el modelo entrenado de regresión logística para realizar una predicción de esas características. 

Para configurar el experimento predictivo, primero se guarda el vocabulario de n-gramas como conjunto de datos y el modelo entrenado de regresión logística desde la rama de entrenamiento del experimento. A continuación, se guarda el experimento mediante la opción "Guardar como" para crear un gráfico de experimento para el experimento predictivo. Se quita el módulo de división de datos y la rama de entrenamiento del experimento. A continuación, se conectan el vocabulario y el modelo de n-gramas guardados previamente con los módulos de extracción de características de n-grama y de puntuación de modelos respectivamente. También se quita el módulo de evaluación de modelos.

Se inserta el módulo de selección de columnas en el conjunto de datos antes del módulo de preprocesamiento de texto para quitar las columnas de etiquetas y se anula la selección de la opción "Append score column to dataset" (Anexar columna de puntuaciones al conjunto de datos) en el módulo de puntuación. De este modo, el servicio web no solicitará la etiqueta que está intentando predecir y no devolverá las características de entrada como respuesta.

![Experimento predictivo](./media/text-analytics-module-tutorial/predictive-text.png)

Ahora tenemos un experimento que se puede publicar como servicio web y al que se puede llamar mediante las API de request-response (solicitud-respuesta) o batch excution (ejecución por lotes).

## <a name="next-steps"></a>Pasos siguientes
Obtenga información acerca de los módulos de análisis de texto mediante la [documentación de MSDN](https://msdn.microsoft.com/library/azure/dn905886.aspx).

