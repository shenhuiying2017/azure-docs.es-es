---
title: "Paso 4: Entrenamiento y evaluación de los modelos de análisis predictivo | Microsoft Docs"
description: "Paso 4 del tutorial Desarrollo de una solución predictiva: entrenamiento, puntuación y evaluación de múltiples modelos en Estudio de aprendizaje automático de Azure."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: d905f6b3-9201-4117-b769-5f9ed5ee1cac
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: garye
ms.openlocfilehash: 5a545b9f9f7f935be91fed743779ebd6b4d930a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="walkthrough-step-4-train-and-evaluate-the-predictive-analytic-models"></a>Paso 4 del tutorial: Entrenamiento y evaluación de los modelos de análisis predictivo
Este tema contiene el cuarto paso del tutorial [Desarrollo de una solución de análisis predictivo en Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Creación de un área de trabajo de Aprendizaje automático](walkthrough-1-create-ml-workspace.md)
2. [Carga de los datos existentes](walkthrough-2-upload-data.md)
3. [Crear un experimento nuevo](walkthrough-3-create-new-experiment.md)
4. **Entrenamiento y evaluación de los modelos**
5. [Implementación del servicio web](walkthrough-5-publish-web-service.md)
6. [Acceso al servicio web](walkthrough-6-access-web-service.md)

- - -
Una de las ventajas del uso de Azure Machine Learning Studio para crear modelos de aprendizaje automático es la capacidad para probar más de un tipo de modelo a la vez en un solo experimento y comparar los resultados. Este tipo de experimentación ayuda a encontrar la mejor solución al problema.

En el experimento que vamos a crear en este tutorial, crearemos dos tipos diferentes de modelos y después compararemos los resultados de su puntuación para decidir cuál de ellos utilizaremos en nuestro experimento final.  

Existen varios modelos entre los que se puede elegir. Para ver cuáles están disponibles, expanda el nodo **Machine Learning** de la paleta de módulos y luego expanda **Initialize Model** (Inicializar modelo) y los nodos que incluye. Teniendo en cuenta el objetivo de este experimento, se seleccionan los módulos [Two-Class Support Vector Machine][two-class-support-vector-machine] (Máquina de vectores de soporte de dos clases, SVM) y [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Árbol de decisión ampliados de dos clases).    

> [!TIP]
> Para obtener ayuda para decidir qué algoritmo de Aprendizaje automático se adapta mejor al problema concreto que trata de solucionar, vea [Cómo elegir algoritmos para Aprendizaje automático de Microsoft Azure](algorithm-choice.md).
> 
> 

## <a name="train-the-models"></a>Entrenamiento de los modelos

Se van a agregar tanto el módulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Árbol de decisión ampliados de dos clases) como el módulo [Two-Class Support Vector Machine][two-class-support-vector-machine] (Máquina de vectores de soporte de dos clases) en este experimento.

### <a name="two-class-boosted-decision-tree"></a>Árbol de decisión ampliado de dos clases

En primer lugar, se va a configurar el modelo del árbol de decisión ampliado.

1. Busque el módulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Árbol de decisión ampliados de dos clases) en la paleta de módulos y arrástrelo al lienzo.

2. Busque el módulo [Train Model][train-model] (Entrenar modelo), arrástrelo al lienzo y conecte la salida del módulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Árbol de decisión ampliados de dos clases) al puerto de entrada izquierdo del módulo [Train Model][train-model] (Entrenar modelo).
   
   El módulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Árbol de decisión ampliados de dos clases) inicializa el modelo genérico, y [Train Model][train-model] (Entrenar modelo) usa los datos de entrenamiento para entrenar el modelo. 

3. Conecte la salida izquierda al módulo [Execute R Script][execute-r-script] (Ejecutar script R) al puerto de entrada de la derecha del módulo [Train Model][train-model] (Entrenar modelo); en el [paso 3](walkthrough-3-create-new-experiment.md) de este tutorial se decidió usar los datos procedentes del lado izquierdo del módulo Split Data (Dividir datos) para el entrenamiento.
   
   > [!TIP]
   > No se necesitan dos de las entradas y una de las salidas del módulo [Execute R Script][execute-r-script] (Ejecutar script R) para este experimento, así que se pueden dejar desconectadas. 
   > 
   > 

Esta parte del experimento tiene ahora un aspecto similar al siguiente:  

![Training a model][1]

Ahora es necesario indicar al módulo [Entrenar modelo][train-model] que se desea que el modelo prediga el valor del riesgo de crédito.

1. Seleccione el módulo [Train Model][train-model] (Entrenar modelo). En el panel **Propiedades**, haga clic en **Launch column selector** (Iniciar el selector de columnas).

2. En el cuadro de diálogo **Select a single column** (Seleccionar una sola columna), escriba "riesgo de crédito" en el campo de búsqueda en **Columnas disponibles**, seleccione "Riesgo de crédito" a continuación y haga clic en el botón de la flecha derecha (**>**) para mover "Riesgo de crédito" a **Columnas seleccionadas**. 

    ![Seleccione la columna de riesgo de crédito para el módulo Entrenar modelo][0]

3. Haga clic en la marca de verificación **Aceptar**.

### <a name="two-class-support-vector-machine"></a>Máquina de vectores de soporte de dos clases

A continuación, se debe configurar el modelo SVM.  

En primer lugar, una breve explicación sobre SVM. Los árboles de decisión ampliados funcionan bien con características de todo tipo. Sin embargo, dado que el módulo SVM genera un clasificador lineal, el modelo que genera tiene el mejor error de prueba cuando todas las características numéricas tienen la misma escala. Para convertir todas las características numéricas a la misma escala, se utiliza una transformación "Tanh", con el módulo [Normalize Data][normalize-data] (Normalizar datos). Esto transforma los números en el intervalo [0,1]. El módulo SVM convierte las características de cadena en características categóricas y luego a características binarias 0/1. Por lo tanto, no hace falta transformar manualmente las características de cadena. Además, no queremos transformar la columna Riesgo de crédito (columna 21): es numérica, pero es el valor sobre cuya predicción estamos entrenando al modelo; por tanto, es necesario dejarla tal cual.  

Para configurar el modelo SVM, realice lo siguiente:

1. Busque el módulo [Two-Class Support Vector Machine][two-class-support-vector-machine] (Máquina de vectores de soporte de dos clases) en la paleta de módulos y arrástrelo al lienzo.

2. Haga clic con el botón derecho en el módulo [Train Model][train-model] (Entrenar modelo), seleccione **Copiar**, haga clic con el botón derecho en el lienzo y seleccione **Pegar**. La copia del módulo [Train Model][train-model] (Entrenar modelo) tiene la misma selección de columnas que el original.

3. Conecte la salida del módulo [Máquina de vectores de soporte de dos clases][two-class-support-vector-machine] al puerto de entrada izquierdo del módulo [Entrenar modelo][train-model].

4. Busque el módulo [Normalizar datos][normalize-data] y arrástrelo al lienzo.

5. Conecte la salida de la izquierda del módulo [Ejecutar script R][execute-r-script] de la izquierda a la entrada de este módulo (tenga en cuenta que el puerto de salida de un módulo puede estar conectado a más de un módulo distinto).

6. Conecte el puerto de salida izquierdo del módulo [Normalize Data][normalize-data] (Normalizar datos) al puerto de salida derecho del segundo módulo [Train Model][train-model] (Entrenar modelo).

Esta parte de nuestro experimento debería tener ahora un aspecto similar al siguiente:  

![Training the second model][2]  

Configure ahora el módulo [Normalize Data][normalize-data] (Normalizar datos):

1. Haga clic para seleccionar el módulo [Normalize Data][normalize-data] (Normalizar datos). En el panel **Propiedades**, seleccione **Tanh** para el parámetro **Transformation method** (Método de transformación).

2. Haga clic en **Launch column selector** (Iniciar el selector de columnas), seleccione "No columns" (Sin columnas) en **Comenzar con**, seleccione **Incluir** en el primer menú desplegable, **Tipo de columna** en el segundo y **Numérica** en el tercero. Esto especifica que todas las columnas numéricas (y solo numéricas) se deben transformar.

3. Haga clic en el signo más (+) a la derecha de esta fila (de esta forma, se crea una fila de menús desplegables). Seleccione **Excluir** en la primera lista desplegable y **Nombres de columna** en la segunda, y escriba "Riesgo de crédito" en el campo de texto. Especifica que se debe ignorar la columna Riesgo de crédito (debemos hacerlo porque se trata de una columna numérica y, de lo contrario, se transformaría).

4. Haga clic en la marca de verificación **Aceptar**.  

    ![Seleccionar columnas para el módulo Normalize Data (Normalizar datos)][5]

El módulo [Normalize Data][normalize-data] (Normalizar datos) está configurado ahora para realizar una transformación Tanh en todas las columnas numéricas excepto en la columna de riesgo de crédito.  

## <a name="score-and-evaluate-the-models"></a>Puntuación y evaluación de modelos

Se utilizan los datos de prueba que se separaron mediante el módulo [Split Data][split] (Dividir datos) para puntuar los modelos entrenados. A continuación podremos comparar los resultados de los dos modelos para ver cuál de ellos generó mejores resultados.  

### <a name="add-the-score-model-modules"></a>Agregar los módulos Score Model (Puntuar modelo)

1. Busque el módulo [Score Model][score-model] (Puntuar modelo) y arrástrelo al lienzo.

2. Conecte el módulo [Train Model][train-model] (Entrenar modelo) que está conectado al módulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Árbol de decisión ampliado de dos clases) al puerto de entrada izquierdo del módulo [Score Model][score-model] (Puntuar modelo).

3. Conecte el módulo derecho [Execute R Script][execute-r-script] (Ejecutar script R) (los datos de prueba) al puerto de entrada derecho del módulo [Score Model][score-model] (Puntuar modelo).

    ![Módulo Score Model (Puntuar modelo) conectado][6]
   
   El módulo [Score Model][score-model] (Puntuar modelo) ahora puede utilizar la información de crédito de los datos de prueba, ejecutarla a través del modelo y comparar las predicciones que el modelo genera con la columna de riesgo de crédito real de los datos de prueba.

4. Copie y pegue el módulo [Score Model][score-model] (Puntuar modelo) para crear una segunda copia.

5. Conecte la salida del modelo SVM; es decir, el puerto de salida del módulo [Train Model][train-model] (Entrenar modelo) que está conectado al módulo [Two-Class Support Vector Machine][two-class-support-vector-machine] (Máquina de vectores de soporte de dos clases) al puerto de entrada del segundo módulo [Score Model][score-model] (Puntuar modelo).

6. En cuanto al modelo SVM, tenemos que realizar la misma transformación en los datos de prueba que la que realizamos con los datos de entrenamiento. Así pues, copie y pegue el módulo [Normalize Data][normalize-data] (Normalizar datos) para crear una segunda copia y conéctelo al módulo derecho [Execute R Script][execute-r-script] (Ejecutar script R).

7. Conecte la salida izquierda del segundo módulo [Normalize Data][normalize-data] (Normalizar datos) al puerto de salida derecho del segundo módulo [Score Model][score-model] (Puntuar modelo).

    ![Ambos módulos Score Model (Puntuar modelo) conectados][7]

### <a name="add-the-evaluate-model-module"></a>Agregar el módulo Evaluate Model (Evaluar modelo)

Para evaluar los dos resultados de puntuación y compararlos, se usa un módulo [Evaluate Model][evaluate-model] (Evaluar modelo).  

1. Busque el módulo [Evaluate Model][evaluate-model] (Evaluar modelo) y arrástrelo al lienzo.

2. Conecte el puerto de salida del módulo [Score Model][score-model] (Puntuar modelo) asociado al modelo del árbol de decisión ampliado al puerto de entrada izquierdo del módulo [Evaluate Model][evaluate-model] (Evaluar modelo).

3. Conecte el otro módulo [Score Model][score-model] (Puntuar modelo) al puerto de entrada derecho.  

    ![Módulo Evaluate Model (Evaluar modelo) conectado][8]

### <a name="run-the-experiment-and-check-the-results"></a>Ejecutar el experimento y comprobar los resultados

Para ejecutar el experimento, haga clic en el botón **EJECUTAR** bajo el lienzo. Esto puede tardar unos minutos. Aparece un indicador giratorio en cada módulo para indicar que está en ejecución y, cuando el módulo acaba, aparece una marca de verificación de color verde. Cuando todos los módulos tengan una marca de verificación, habrá finalizado la ejecución del experimento.

El experimento debería tener ahora un aspecto similar al siguiente:  

![Evaluating both models][3]

Para comprobar los resultados, haga clic en el puerto de salida del módulo [Evaluate Model][evaluate-model] (Evaluar modelo) y seleccione **Visualizar**.  

El módulo [Evaluate Model][evaluate-model] (Evaluar modelo) produce un par de curvas y métricas que permiten comparar los resultados de los dos modelos de puntuación. Puede ver los resultados como curvas de características operativas del receptor (ROC), curvas de precisión/exhaustividad o curvas de elevación. También se muestran otros datos como la matriz de confusión y los valores del área bajo la curva (AUC) acumulados, entre otras métricas. También puede cambiar el valor del umbral moviendo el control deslizante a la izquierda o a la derecha, y comprobar cómo afecta esta acción al conjunto de métricas.  

A la derecha del gráfico, haga clic en **Scored dataset** (Conjunto de datos puntuados) o en **Scored dataset to compare** (Conjunto de datos puntuados para comparar) con el fin de resaltar la curva asociada y mostrar debajo las métricas asociadas. En la leyenda de las curvas, "Conjunto de datos puntuados" corresponde al puerto de entrada izquierdo del módulo [Evaluate Model][evaluate-model] (Evaluar modelo); en este caso, se trata del modelo del árbol de decisión ampliado. "Conjunto de datos puntuados para comparar" corresponde al puerto de entrada derecho (el modelo SVM en nuestro caso). Al hacer clic en una de estas etiquetas, la curva del modelo correspondiente se resalta y muestra las métricas correspondientes tal y como se muestra en el gráfico siguiente.  

![ROC curves for models][4]

Si examina estos valores, podrá decidir cuál es el modelo que más se acerca a ofrecerle los resultados que busca. Puede volver y repetir el experimento cambiando valores de parámetros en los diferentes modelos. 

La ciencia y el arte de interpretar estos resultados y de ajustar el rendimiento del modelo están fuera del ámbito de este tutorial. Para obtener ayuda adicional, puede leer los artículos siguientes:
- [Evaluación del rendimiento de un modelo en Azure Machine Learning](evaluate-model-performance.md)
- [Cómo elegir parámetros para optimizar los algoritmos de Azure Machine Learning](algorithm-parameters-optimize.md)
- [Cómo interpretar los resultados del modelo de Azure Machine Learning](interpret-model-results.md)

> [!TIP]
> Cada vez que ejecute el experimento, se guardará un registro de esa iteración en el Historial de ejecuciones. Puede ver estas iteraciones y volver a cualquiera de ellas haciendo clic en **VER HISTORIAL DE EJECUCIÓN** bajo el lienzo. También puede hacer clic en **Prior Run** (Ejecución anterior) en el panel **Propiedades** para volver a la iteración inmediatamente anterior a la que ha abierto.
> 
> Puede hacer una copia de cualquier iteración de su experimento si hace clic en **GUARDAR COMO** bajo el lienzo. 
> Utilice las propiedades **Resumen** y **Descripción** para mantener un registro de lo que ha tratado de hacer en las iteraciones del experimento.
> 
> Consulte [Administración de iteraciones de experimentos en Estudio de aprendizaje automático de Azure](manage-experiment-iterations.md)para obtener más detalles.  
> 
> 

- - -
**Siguiente: [Implementación del servicio web](walkthrough-5-publish-web-service.md)**

[0]: ./media/walkthrough-4-train-and-evaluate-models/train-model-select-column.png
[1]: ./media/walkthrough-4-train-and-evaluate-models/experiment-with-train-model.png
[2]: ./media/walkthrough-4-train-and-evaluate-models/svm-model-added.png
[3]: ./media/walkthrough-4-train-and-evaluate-models/final-experiment.png
[4]: ./media/walkthrough-4-train-and-evaluate-models/roc-curves.png
[5]: ./media/walkthrough-4-train-and-evaluate-models/normalize-data-select-column.png
[6]: ./media/walkthrough-4-train-and-evaluate-models/score-model-connected.png
[7]: ./media/walkthrough-4-train-and-evaluate-models/both-score-models-added.png
[8]: ./media/walkthrough-4-train-and-evaluate-models/evaluate-model-added.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
