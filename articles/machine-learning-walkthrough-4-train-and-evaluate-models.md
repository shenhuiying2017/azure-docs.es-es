<properties title="Step 4: Train and evaluate the predictive analytic models" pageTitle="Step 4: Train and evaluate the predictive analytic models | Azure" description="Step 4: Train, score, and evaluate multiple models in Azure Machine Learning Studio" metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

Este es el cuarto paso del tutorial [Desarrollo de una solución predictiva con el Aprendizaje automático de Azure][Desarrollo de una solución predictiva con el Aprendizaje automático de Azure]:

1.  [Creación de un área de trabajo de Aprendizaje automático][Creación de un área de trabajo de Aprendizaje automático]
2.  [Carga de los datos existentes][Carga de los datos existentes]
3.  [Creación de un nuevo experimento][Creación de un nuevo experimento]
4.  **Entrenamiento y evaluación de los modelos**
5.  [Publicación del servicio web][Publicación del servicio web]
6.  [Acceso al servicio web][Acceso al servicio web]

------------------------------------------------------------------------

# Paso 4: Entrenamiento y evaluación de los modelos de análisis predictivo

En este experimento deseamos probar diferentes algoritmos para nuestro modelo predictivo. Crearemos dos tipos diferentes de modelos y, a continuación, compararemos los resultados de su puntuación para decidir cuál de ellos utilizaremos en nuestro experimento final.

Podemos elegir entre diversos modelos. Para ver cuáles están disponibles, expanda el nodo **Aprendizaje automático** de la paleta de módulos y, a continuación, expanda **Inicializar modelo** y el nodo que incluye. Teniendo en cuenta el objetivo de este experimento, seleccionaremos la Máquina de vectores de soporte (SVM) y los árboles de decisión ampliados de dos clases. Utilizaremos los módulos apropiados para inicializar los algoritmos de aprendizaje y utilizar los módulos **Entrenar modelo** para entrenar los módulos.

## Entrenamiento de los modelos

En primer lugar, vamos a configurar el modelo del árbol de decisión ampliado:

1.  Busque el módulo **Árbol de decisión ampliado de dos clases** en la paleta de módulos y arrástrelo al lienzo.
2.  Busque el módulo **Entrenar modelo**, arrástrelo al lienzo y, a continuación, conecte la salida del módulo del árbol de decisión ampliado al puerto de entrada izquierdo ("Módulo sin entrenar") del módulo **Entrenar modelo**.
3.  Conecte la salida del módulo **Ejecutar script R** izquierdo al puerto de entrada derecho ("Conjunto de datos") del módulo **Entrenar modelo**.

    > Sugerencia: no necesitamos dos de las entradas y una de las salidas del módulo **Ejecutar script R** para este experimento; así pues, las dejaremos desconectadas. Esto es bastante habitual para algunos módulos.

4.  Seleccione el módulo **Entrenar modelo**. En el panel **Propiedades**, haga clic en **Iniciar selector de columnas**, seleccione "Column indices" en el primer desplegable y escriba "21" en el campo de texto (también puede seleccionar "Column name" y escribir "Credit risk"). Esto identifica la columna 21, el valor de riesgo de crédito y la columna para el modelo que se va a predecir.

Esta parte del experimento tiene ahora un aspecto similar al siguiente:

![Training a model][Training a model]

A continuación, vamos a configurar el modelo SVM.

Los árboles de decisión ampliados funcionan bien con características de todo tipo. Sin embargo, dado que el módulo SVM genera un clasificador lineal, el modelo que genera tiene el mejor error de prueba cuando todas las características numéricas tienen la misma escala. Para convertir todas las características numéricas a la misma escala, utilizamos el módulo **Transformar datos mediante escalado** con una transformación tanh, que transforma características al rango [0,1]. Tenga en cuenta que las características de cadena se convierten por parte del módulo SVM a características categóricas y, a continuación, a características binarias 0/1; así pues, no es necesario transformar manualmente las características de cadena. Además, no deseamos transformar la columna Credit Risk (columna 21); es numérica, pero es el valor sobre cuya predicción estamos entrenando al modelo; por tanto, es necesario dejarla tal cual.

1.  Busque el módulo **Máquina de vectores de soporte de dos clases** en la paleta de módulos y arrástrelo al lienzo.
2.  Haga clic en el módulo **Entrenar modelo**, seleccione **Copiar** y, a continuación, haga clic con el botón secundario en el lienzo y seleccione **Pegar**. Observe que la copia del módulo **Entrenar modelo** tiene la misma selección de columnas que el original.
3.  Conecte la salida del módulo SVM al puerto de entrada izquierdo ("Modelo sin entrenar") del módulo **Entrenar modelo**.
4.  Busque el módulo **Transformar datos mediante escalado** y arrástrelo al lienzo.
5.  Conecte la entrada de este módulo de transformación a la salida del módulo **Ejecutar script R** de la izquierda.
6.  Conecte el puerto de salida izquierdo ("Conjunto de datos transformados") del módulo de transformación al puerto de entrada derecho ("Conjunto de datos") del módulo **Entrenar modelo**.
7.  En el panel **Propiedades** del módulo de transformación, seleccione "Tanh" como parámetro del **Método de transformación**.
8.  Haga clic en **Iniciar selector de columnas**. En el primer desplegable, seleccione "Column types" y en el segundo seleccione "Numeric" (deje el tercer desplegable con la opción "All" seleccionada). Esto especifica que todas las columnas numéricas (y solo numéricas) se transformarán.
9.  Haga clic en el signo más (+), que crea una nueva fila de desplegables. Seleccione "Exclude column indices" en el primer desplegable y escriba "21" en el campo de texto. Esto especifica que se ignorará la columna 21 (la columna "Credit risk").
10. Haga clic en **OK**.

El módulo **Transformar datos mediante escalado** está configurado ahora para realizar una transformación tanh en todas las columnas numéricas excepto en la columna relacionada con el riesgo de crédito.

Esta parte de nuestro experimento debería tener ahora un aspecto similar al siguiente:

![Training the second model][Training the second model]

## Puntuación y evaluación de modelos

Utilizaremos los datos de puntuación que se separaron mediante el módulo **Dividir** para puntuar nuestros modelos entrenados. A continuación podremos comparar los resultados de los dos modelos para ver cuál de ellos generó mejores resultados.

1.  Busque el módulo **Puntuar modelo** y arrástrelo al lienzo.
2.  Conecte el puerto de entrada izquierdo de este módulo al modelo del árbol de decisión ampliado (esto es, conéctelo al puerto de salida del módulo **Entrenar modelo** que está conectado al módulo **Árbol de decisión ampliado de dos clases**).
3.  Conecte el puerto de entrada derecho del módulo **Puntuar modelo** a la salida del módulo **Ejecutar script R** derecho. Tenga en cuenta que es correcto hacer que la salida de un módulo vaya a varios lugares.
4.  Copie y pegue el módulo **Puntuar modelo** para crear una segunda copia, o arrastre un nuevo módulo al lienzo.
5.  Conecte el puerto de entrada izquierdo de este módulo al modelo SVM (esto es, conéctelo al puerto de salida del módulo **Entrenar modelo** que está conectado al módulo **Máquina de vectores de soporte de dos clases**).
6.  En cuanto al modelo SVM, tenemos que realizar la misma transformación en los datos de prueba que la que realizamos con los datos de entrenamiento. Así pues, copie y pegue el módulo **Transformar datos mediante escalado** para crear una segunda copia y conéctelo a la salida del módulo **Ejecutar script R** derecho.
7.  Conecte el puerto de entrada derecho del módulo **Puntuar modelo** a la salida del módulo **Transformar datos mediante escalado**.

Para evaluar los dos resultados de puntuación, utilizaremos el módulo **Evaluar modelo**.

1.  Busque el módulo **Evaluar modelo** y arrástrelo al lienzo.
2.  Conecte el puerto de entrada izquierdo al puerto de salida de **Puntuar modelo** asociado al modelo del árbol de decisión ampliado.
3.  Conecte el puerto de entrada derecho al otro módulo **Puntuar modelo**.

El experimento debería tener ahora un aspecto similar al siguiente:

![Evaluating both models][Evaluating both models]

Haga clic en el botón **EJECUTAR** bajo el lienzo para ejecutar el experimento. Esto puede tardar unos minutos. Verá un indicador giratorio en cada módulo que indica que está en ejecución; cuando haya acabado, aparecerá una marca de verificación de color verde.

Cuando todos los módulos tengan una marca de verificación, habrá finalizado la ejecución del experimento. Para consultar los resultados, haga clic con el botón secundario en el puerto de salida del módulo **Evaluar modelo** y seleccione **Visualizar**.

El módulo **Evaluar modelo** produce un par de curvas y métricas que le permiten comparar los resultados de los dos modelos de puntuación. Puede ver los resultados como curvas de características operativas del receptor (ROC), curvas de precisión/exhaustividad o curvas de elevación. También se muestran otros datos como la matriz de confusión y los valores de AUC acumulados, entre otras métricas. También puede cambiar el valor del umbral moviendo el control deslizante a la izquierda o la derecha y comprobar cómo afecta esta acción al conjunto de métricas.

Haga clic en "Conjunto de datos puntuados" o "Conjunto de datos puntuados para comparar" a fin de resaltar la curva asociada y mostrar debajo las métricas asociadas. En la leyenda de las curvas, "Conjunto de datos puntuados" corresponde al puerto de entrada izquierdo del módulo **Evaluar modelo** (en nuestro caso, se trata del modelo del árbol de decisión ampliado). "Conjunto de datos puntuados para comparar" corresponde al puerto de entrada derecho (el modelo SVM en nuestro caso). Al hacer clic en una de estas etiquetas, se destaca la curva de ese modelo y aparecen debajo las métricas correspondientes.

![ROC curves for models][ROC curves for models]

Al examinar estos valores, puede decidir cuál modelo se acerca más a los resultados que busca. Puede volver y repetir su experimento cambiando valores en los diferentes nodos.

> Sugerencia: cada vez que ejecute el experimento, se guardará un registro de esa iteración en el Historial de ejecuciones. Puede ver estas iteraciones y volver a cualquiera de ellas haciendo clic en **VER HISTORIAL DE EJECUCIÓN** bajo el lienzo. También puede hacer clic en **Anterior ejecución** en el panel **Propiedades** para volver a la iteración inmediatamente anterior a la que ha abierto.

También puede hacer una copia de cualquier iteración de su experimento si hace clic en **GUARDAR COMO** bajo el lienzo. Así se realiza un duplicado del experimento, creando un nuevo Historial de ejecuciones para realizar un seguimiento de las iteraciones de esta versión. Esta nueva copia aparece en la lista **EXPERIMENTOS** junto al original. Puede resultar útil si desea iniciar una nueva rama de iteraciones del experimento.

Como ayuda adicional para realizar un seguimiento de los cambios realizados en los parámetros del módulo, puede agregar comentarios a cualquier módulo en el lienzo de experimentos. Simplemente haga doble clic en el módulo o haga clic con el botón secundario y seleccione **Editar comentario**. Estos comentarios se guardan con las iteraciones del experimento y pueden ayudarle a glosar su trabajo.

------------------------------------------------------------------------

**A continuación: [Publicación del servicio web][Publicación del servicio web]**

  [Desarrollo de una solución predictiva con el Aprendizaje automático de Azure]: ../machine-learning-walkthrough-develop-predictive-solution/
  [Creación de un área de trabajo de Aprendizaje automático]: ../machine-learning-walkthrough-1-create-ml-workspace/
  [Carga de los datos existentes]: ../machine-learning-walkthrough-2-upload-data/
  [Creación de un nuevo experimento]: ../machine-learning-walkthrough-3-create-new-experiment/
  [Publicación del servicio web]: ../machine-learning-walkthrough-5-publish-web-service/
  [Acceso al servicio web]: ../machine-learning-walkthrough-6-access-web-service/
  [Training a model]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train1.png
  [Training the second model]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train2.png
  [Evaluating both models]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train3.png
  [ROC curves for models]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train4.png
