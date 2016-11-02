<properties
    pageTitle="Paso 4: Entrenamiento y evaluación de los modelos de análisis predictivo | Microsoft Azure"
    description="Paso 4 del tutorial Desarrollo de una solución predictiva: entrenamiento, puntuación y evaluación de múltiples modelos en Estudio de aprendizaje automático de Azure."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="garye"/>



# <a name="walkthrough-step-4:-train-and-evaluate-the-predictive-analytic-models"></a>Paso 4 del tutorial: Entrenamiento y evaluación de los modelos de análisis predictivo

Este tema contiene el cuarto paso del tutorial [Desarrollo de una solución de análisis predictivo en Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Creación de un área de trabajo de Aprendizaje automático](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Carga de los datos existentes](machine-learning-walkthrough-2-upload-data.md)
3.  [Crear un experimento nuevo](machine-learning-walkthrough-3-create-new-experiment.md)
4.  **Entrenamiento y evaluación de los modelos**
5.  [Implementación del servicio web](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Acceso al servicio web](machine-learning-walkthrough-6-access-web-service.md)

----------

Una de las ventajas del uso de Estudio de aprendizaje automático de Azure para crear modelos de aprendizaje automático es la capacidad para probar más de un tipo de modelo a la vez en un experimento y comparar los resultados. Este tipo de experimentación ayuda a encontrar la mejor solución al problema.

En el experimento que vamos a crear en este tutorial, crearemos dos tipos diferentes de modelos y después compararemos los resultados de su puntuación para decidir cuál de ellos utilizaremos en nuestro experimento final.  

Existen varios modelos entre los que se puede elegir. Para ver cuáles están disponibles, expanda el nodo **Machine Learning** de la paleta de módulos y luego expanda **Initialize Model** (Inicializar modelo) y los nodos que incluye. Teniendo en cuenta el objetivo de este experimento, seleccionaremos la Máquina de vectores de soporte (SVM) y los módulos Árboles de decisión ampliados de dos clases.    

> [AZURE.TIP] Para obtener ayuda para decidir qué algoritmo de Aprendizaje automático se adapta mejor al problema concreto que trata de solucionar, vea [Cómo elegir algoritmos para Aprendizaje automático de Microsoft Azure](machine-learning-algorithm-choice.md).

##<a name="train-the-models"></a>Entrenamiento de los modelos
En primer lugar, vamos a configurar el modelo del árbol de decisión ampliado:  

1.  Busque el módulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Árbol de decisión ampliado de dos clases) en la paleta de módulos y arrástrelo al lienzo.
2.  Busque el módulo [Entrenar modelo][train-model], arrástrelo al lienzo y conecte la salida del módulo del árbol de decisión ampliado al puerto de entrada izquierdo (el "módulo sin entrenar") del módulo [Entrenar modelo][train-model].
    
    El módulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Árbol de decisión ampliado de dos clases) inicializa el modelo genérico, y [Entrenar modelo][train-model] usa los datos de entrenamiento para entrenar el modelo. 
     
3.  Conecte la salida izquierda (el "conjunto de datos de resultados") del módulo [Execute R Script][execute-r-script] (Ejecutar script R) izquierdo al puerto de entrada derecho (el "conjunto de datos") del módulo [Entrenar modelo][train-model].

    > [AZURE.TIP] No necesitaremos dos de las entradas y una de las salidas del módulo [Execute R Script][execute-r-script] (Ejecutar script R) para este experimento, así que las dejaremos desconectadas. 

4.  Seleccione el módulo [Entrenar modelo][train-model]. En el panel **Propiedades**, haga clic en **Launch column selector** (Iniciar el selector de columnas), seleccione **Todos los tipos** en el menú desplegable de **Columnas disponibles** y escriba "Riesgo de crédito" en el campo de texto. Seleccione **Todos los tipos** en el menú desplegable de **Columnas seleccionadas**. Seleccione "Riesgo de crédito" y haga clic en el botón de flecha resaltada para moverla hasta **Columnas seleccionadas**. 
5.  Haga clic en **Guardar**.


Esta parte del experimento tiene ahora un aspecto similar al siguiente:  

![Training a model][1]

A continuación, se debe configurar el modelo SVM.  

En primer lugar, una breve explicación sobre SVM. Los árboles de decisión ampliados funcionan bien con características de todo tipo. Sin embargo, dado que el módulo SVM genera un clasificador lineal, el modelo que genera tiene el mejor error de prueba cuando todas las características numéricas tienen la misma escala. Para convertir todas las características numéricas a la misma escala, se utiliza una transformación "Tanh" (con el módulo [Normalize Data][normalize-data] [Normalizar datos]). Esto transforma los números en el intervalo [0,1]. El módulo SVM convierte las características de cadena en características categóricas y luego a características binarias 0/1. Por lo tanto, no hace falta transformar manualmente las características de cadena. Además, no queremos transformar la columna Riesgo de crédito (columna 21): es numérica, pero es el valor sobre cuya predicción estamos entrenando al modelo; por tanto, es necesario dejarla tal cual.  

Para configurar el modelo SVM, realice lo siguiente:

1.  Busque el módulo [Two-Class Support Vector Machine][two-class-support-vector-machine] (Máquina de vectores de soporte de dos clases) en la paleta de módulos y arrástrelo al lienzo.
2.  Haga clic con el botón derecho en el módulo [Entrenar modelo][train-model], seleccione **Copiar** y luego haga clic con el botón derecho en el lienzo y seleccione **Pegar**. La copia del módulo [Entrenar modelo][train-model] tiene la misma selección de columnas que el original.
3.  Conecte la salida del módulo SVM al puerto de entrada izquierdo (el "módulo sin entrenar") del segundo módulo [Entrenar modelo][train-model].
4.  Busque el módulo [Normalize Data][normalize-data] (Normalizar datos) y arrástrelo al lienzo.
5.  Conecte la entrada de este módulo a la salida de la izquierda del módulo [Execute R Script][execute-r-script] (Ejecutar script R) de la izquierda (tenga en cuenta que el puerto de salida de un módulo puede estar conectado a más de un módulo distinto).
6.  Conecte el puerto de salida izquierdo (el "conjunto de datos transformado") del módulo [Normalize Data][normalize-data] (Normalizar datos) al puerto de entrada derecho (el "conjunto de datos") del segundo módulo [Entrenar modelo][train-model].
7.  En el panel **Propiedades** del módulo [Normalize Data][normalize-data] (Normalizar datos), seleccione **Tanh** para el parámetro **Transformation method** (Método de transformación).
8.  Haga clic en **Launch column selector** (Iniciar el selector de columnas), seleccione "No columns" (Sin columnas) en **Comenzar con**, seleccione **Incluir** en el primer menú desplegable, **Tipo de columna** en el segundo y **Numérica** en el tercero. Esto especifica que todas las columnas numéricas (y solo numéricas) se deben transformar.
9.  Haga clic en el signo más (+) a la derecha de esta fila (de esta forma, se crea una fila de menús desplegables). Seleccione **Excluir** en el primer menú desplegable, **Nombres de columna** en el segundo y "Riesgo de crédito" en la lista de columnas. Especifica que se debe ignorar la columna Riesgo de crédito (debemos hacerlo porque se trata de una columna numérica y, de lo contrario, se transformaría).
10. Haga clic en **Aceptar**.  


El módulo [Normalize Data][normalize-data] (Normalizar datos) ahora estará configurado para realizar una transformación Tanh en todas las columnas numéricas, excepto en la columna de riesgo de crédito.  

Esta parte de nuestro experimento debería tener ahora un aspecto similar al siguiente:  

![Training the second model][2]  

##<a name="score-and-evaluate-the-models"></a>Puntuación y evaluación de modelos

Utilizaremos los datos de prueba que se separaron mediante el módulo [Split Data][split] (Dividir datos) para puntuar nuestros modelos entrenados. A continuación podremos comparar los resultados de los dos modelos para ver cuál de ellos generó mejores resultados.  

1.  Busque el módulo [Score Model][score-model] (Modelo de puntuación) y arrástrelo al lienzo.
2.  Conecte el módulo [Entrenar modelo][train-model] que está conectado al módulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Árbol de decisión ampliado de dos clases) al puerto de entrada izquierdo del módulo [Score Model][score-model] (Modelo de puntuación).
3.  Conecte el puerto de entrada derecho del módulo [Score Model][score-model] (Modelo de puntuación) a la salida izquierda del módulo [Execute R Script][execute-r-script] (Ejecutar script R) derecho.

    El módulo [Score Model][score-model] (Modelo de puntuación) ahora puede utilizar la información de crédito de los datos de prueba, ejecutarla a través del modelo y comparar las predicciones que genera el modelo con la columna de riesgo de crédito real de los datos de prueba.

4.  Copie y pegue el módulo [Score Model][score-model] (Modelo de puntuación) para crear una segunda copia o arrastre un nuevo módulo al lienzo.
5.  Conecte el puerto de entrada izquierdo de este módulo al modelo SVM (es decir, conéctelo al puerto de salida del módulo [Entrenar modelo][train-model] que está conectado al módulo [Two-Class Support Vector Machine][two-class-support-vector-machine] [Máquina de vectores de soporte de dos clases]).
6.  En cuanto al modelo SVM, tenemos que realizar la misma transformación en los datos de prueba que la que realizamos con los datos de entrenamiento. Por tanto, copie y pegue el módulo [Normalize Data][normalize-data] (Normalizar datos) para crear una segunda copia y conéctelo a la salida izquierda del módulo [Execute R Script][execute-r-script] (Ejecutar script R) derecho.
7.  Conecte el puerto de entrada derecho del módulo [Score Model][score-model] (Modelo de puntuación) a la salida izquierda del módulo [Normalize Data][normalize-data] (Normalizar datos).  

Para evaluar los dos resultados de puntuación, usamos un módulo [Evaluate Model][evaluate-model] (Evaluar modelo).  

1.  Busque el módulo [Evaluate Model][evaluate-model] (Evaluar modelo) y arrástrelo al lienzo.
2.  Conecte el puerto de entrada izquierdo al puerto de salida del módulo [Score Model][score-model] (Modelo de puntuación) asociado al modelo del árbol de decisión ampliado.
3.  Conecte el puerto de entrada derecho al otro módulo [Score Model][score-model] (Modelo de puntuación).  

Para ejecutar el experimento, haga clic en el botón **EJECUTAR** bajo el lienzo. Esto puede tardar unos minutos. Aparecerá un indicador giratorio en cada módulo para indicar que está en ejecución y, cuando hayan acabado, aparecerá una marca de verificación de color verde. Cuando todos los módulos tengan una marca de verificación, habrá finalizado la ejecución del experimento.

El experimento debería tener ahora un aspecto similar al siguiente:  

![Evaluating both models][3]

Para consultar los resultados, haga clic en el puerto de salida del módulo [Evaluate Model][evaluate-model] (Evaluar modelo) y seleccione **Visualizar**.  

El módulo [Evaluate Model][evaluate-model] (Evaluar modelo) produce un par de curvas y métricas que le permiten comparar los resultados de los dos modelos puntuados. Puede ver los resultados como curvas de características operativas del receptor (ROC), curvas de precisión/exhaustividad o curvas de elevación. También se muestran otros datos como la matriz de confusión y los valores del área bajo la curva (AUC) acumulados, entre otras métricas. También puede cambiar el valor del umbral moviendo el control deslizante a la izquierda o a la derecha, y comprobar cómo afecta esta acción al conjunto de métricas.  

A la derecha del gráfico, haga clic en **Scored dataset** (Conjunto de datos puntuados) o en **Scored dataset to compare** (Conjunto de datos puntuados para comparar) con el fin de resaltar la curva asociada y mostrar debajo las métricas asociadas. En la leyenda de las curvas, Scored dataset (Conjunto de datos puntuados) corresponde al puerto de entrada izquierdo del módulo [Evaluate Model][evaluate-model] (Evaluar modelo) (en nuestro caso, se trata del modelo del árbol de decisión ampliado). "Conjunto de datos puntuados para comparar" corresponde al puerto de entrada derecho (el modelo SVM en nuestro caso). Al hacer clic en una de estas etiquetas, la curva del modelo correspondiente se resalta y muestra las métricas correspondientes tal y como se ilustra en el gráfico siguiente.  

![ROC curves for models][4]

Si examina estos valores, podrá decidir cuál es el modelo que más se acerca a ofrecerle los resultados que busca. Puede volver y repetir el experimento cambiando valores en los diferentes nodos. 

> [AZURE.TIP] Cada vez que ejecute el experimento, se guardará un registro de esa iteración en el Historial de ejecuciones. Puede ver estas iteraciones y volver a cualquiera de ellas haciendo clic en **VER HISTORIAL DE EJECUCIÓN** bajo el lienzo. También puede hacer clic en **Prior Run** (Ejecución anterior) en el panel **Propiedades** para volver a la iteración inmediatamente anterior a la que ha abierto.
> 
Puede hacer una copia de cualquier iteración de su experimento si hace clic en **GUARDAR COMO** bajo el lienzo. Utilice las propiedades **Resumen** y **Descripción** para mantener un registro de lo que ha tratado de hacer en las iteraciones del experimento.

>  Consulte [Administración de iteraciones de experimentos en Estudio de aprendizaje automático de Azure](machine-learning-manage-experiment-iterations.md)para obtener más detalles.  


----------

**Siguiente: [Implementación del servicio web](machine-learning-walkthrough-5-publish-web-service.md)**

[1]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train1.png
[2]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train2.png
[3]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train3.png
[4]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train4.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/



<!--HONumber=Oct16_HO2-->


