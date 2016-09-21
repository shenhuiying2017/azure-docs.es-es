<properties
	pageTitle="Un experimento sencillo en Estudio de aprendizaje automático | Microsoft Azure"
	description="Este tutorial de aprendizaje automático le guiará a través de un sencillo experimento de ciencia de datos. Podremos predecir el precio de un automóvil mediante un algoritmo de regresión."
	keywords="experimento,regresión lineal,algoritmos de aprendizaje automático,tutorial de aprendizaje automático,técnicas de modelado predictivo,experimento de ciencia de datos"
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
	ms.topic="hero-article"
	ms.date="07/14/2016"
	ms.author="garye"/>

# Tutorial de aprendizaje automático: creación del primer experimento de ciencia de datos en Estudio de aprendizaje automático de Azure

Este tutorial de aprendizaje automático le guiará a través de un sencillo experimento de ciencia de datos. Vamos a crear un modelo de regresión lineal que predecirá el precio de un automóvil en función de diferentes variables, como la marca y las especificaciones técnicas. Para ello, usaremos Estudio de aprendizaje automático de Azure para desarrollar e iterar en un experimento sencillo de análisis predictivo.

*Análisis predictivo* es un tipo de ciencia de datos que utiliza los datos actuales para predecir resultados futuros. Si quiere ver un ejemplo muy sencillo de análisis predictivo, consulte Ciencia de datos para principiantes, vídeo 4: [Predicción de respuestas con un modelo sencillo](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) (tiempo de ejecución: 7:42).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## ¿Cómo funciona la ayuda de Estudio de aprendizaje automático?

Estudio de aprendizaje automático facilita la configuración de un experimento con módulos de arrastrar y colocar programados previamente con técnicas de modelado predictivo. Para ejecutar el experimento y predecir una respuesta, deberá utilizar Estudio de aprendizaje automático para *crear un modelo*, *entrenar el modelo* y *puntuar y probar el modelo*.

Visite Estudio de aprendizaje automático en: [https://studio.azureml.net](https://studio.azureml.net). Si ya se ha registrado en Estudio de aprendizaje automático antes, haga clic en **iniciar sesión aquí**. De lo contrario, haga clic en **Registrarse** y elija entre las opciones gratuitas y las pagadas.

Para más información sobre Estudio de aprendizaje automático, consulte [¿Qué es el Estudio de aprendizaje automático?](machine-learning-what-is-ml-studio.md)

## Cinco pasos para crear un experimento

En este tutorial de Aprendizaje automático se enumeran los cinco pasos básicos que puede llevar a cabo para crear un experimento en Estudio de aprendizaje automático para permitirle crear, formar y puntuar el modelo:

- Crear un modelo
	- [Paso 1: Obtener los datos]
	- [Paso 2: Preprocesar los datos]
	- [Paso 3: Definir las características]
- Formar el modelo
	- [Paso 4: Elegir y aplicar un algoritmo de aprendizaje]
- Puntuar y probar el modelo
	- [Paso 5: Predecir los precios de los automóviles nuevos]

[Paso 1: Obtener los datos]: #step-1-get-data
[Paso 2: Preprocesar los datos]: #step-2-preprocess-data
[Paso 3: Definir las características]: #step-3-define-features
[Paso 4: Elegir y aplicar un algoritmo de aprendizaje]: #step-4-choose-and-apply-a-learning-algorithm
[Paso 5: Predecir los precios de los automóviles nuevos]: #step-5-predict-new-automobile-prices


## Paso 1: Obtener los datos

Hay una serie de conjuntos de datos de ejemplo incluidos en Estudio de aprendizaje automático de Azure entre los que puede elegir y puede importar datos desde varios orígenes. En este ejemplo, usaremos el conjunto de datos de muestra incluido, **Automobile price data (Raw)**. Este conjunto de datos incluye entradas para varios automóviles individuales, incluyendo información como marca, modelo, especificaciones técnicas y precio.

1. Inicie un experimento nuevo haciendo clic en **+NUEVO** en la parte inferior de la ventana de Estudio de aprendizaje automático, seleccione **EXPERIMENTO** y luego **Experimento en blanco**. Seleccione el nombre del experimento predeterminado en la parte superior del lienzo y cámbielo por uno significativo, por ejemplo, **Predicción del precio de automóviles**.

2. A la izquierda del lienzo de experimentos, hay una paleta de conjuntos de datos y módulos. Escriba **automobile** en el cuadro de búsqueda de la parte superior de esta paleta para encontrar el conjunto de datos llamado **Automobile price data (Raw)** (Datos de precios de automóviles (sin formato)).

	![Palette search][screen1a]

3. Arrastre el conjunto de datos al lienzo de experimentos.

	![Dataset][screen1]

Para ver la apariencia de estos datos, haga clic en el puerto de salida, en la parte inferior del conjunto de datos de automóvil, y seleccione **Visualizar**.

![Puerto de salida del módulo][screen1c]

Las variables del conjunto de datos aparecen como columnas, y cada instancia de un automóvil aparece como una fila. La columna de la derecha (columna 26 y la titulada "precio") es la variable de destino que vamos a intentar predecir.

![Dataset visualization][screen1b]

Cierre la ventana de visualización haciendo clic en la "**x**" en la esquina superior derecha.

## Paso 2: Preprocesar los datos

Normalmente, un conjunto de datos requiere algún procesamiento previo antes de que se pueda analizar. Es posible que haya observado los valores que faltan en las columnas de varias filas. Estos valores que faltan se deben limpiar para que el modelo pueda analizar los datos de manera adecuada. En nuestro caso, quitaremos todas las filas que tengan valores ausentes. Además, la columna **normalized-losses** tiene una gran proporción de valores que faltan, por lo que excluiremos esa columna del modelo por completo.

> [AZURE.TIP] Limpiar los valores que faltan de los datos de entrada es un requisito previo para usar la mayoría de los módulos.

En primer lugar, eliminaremos la columna **normalized-losses** y luego las filas que tienen datos que faltan.

1. Escriba **seleccionar columnas** en el cuadro de búsqueda, en la parte superior de la paleta del módulo, para encontrar el módulo [Seleccionar columnas en el conjunto de datos][select-columns]; arrástrelo después hasta el lienzo de experimentos y conéctelo al puerto de salida del conjunto de datos **Datos de precios de automóviles (sin formato)**. Este módulo nos permite seleccionar las columnas de datos que queremos incluir o excluir del modelo.

2. Seleccione el módulo [Seleccionar columnas en el conjunto de datos][select-columns] y haga clic en **Iniciar el selector de columnas** en el panel de **propiedades**.

	- A la izquierda, haga clic en **Con reglas**.
	- En **Empiezan por**, haga clic en **Todas las columnas**. Esto indica a [Seleccionar columnas en el conjunto de datos][select-columns] que pase todas las columnas (excepto las que se van a excluir).
	- En los menús desplegables, seleccione **Excluir** y **nombres de columna** y luego haga clic en el cuadro de texto. A continuación, se mostrará una lista de columnas. Seleccione **normalized-losses** y se agregará al cuadro de texto.
	- Haga clic en el botón Aceptar con la marca de verificación para cerrar el selector de columnas.

    ![Select columns][screen3]

	El panel de propiedades de **Seleccionar columnas en el conjunto de datos** indica que se pasarán todas las columnas del conjunto de datos excepto **normalized-losses**.

    ![Selección de columnas en propiedades del conjunto de datos][screen4]

    > [AZURE.TIP] Puede agregar un comentario a un módulo; para ello, haga doble clic en el módulo y escriba texto. Esto puede ayudarle a ver de un vistazo lo que el módulo hace en el experimento. En este caso, haga doble clic en el módulo [Seleccionar columnas en el conjunto de datos][select-columns] y escriba el comentario "Excluir normalized-losses".

3. Arrastre el módulo [Limpiar valores que faltan][clean-missing-data] al lienzo de experimentos y conéctelo al módulo [Seleccionar columnas en el conjunto de datos][select-columns]. En el panel **Propiedades**, seleccione **Quitar la fila entera** en **Modo de limpieza** para limpiar los datos eliminando las filas que tienen valores que faltan. Haga doble clic en el módulo y escriba el comentario "Quitar las filas sin valor".

	![Propiedades de Limpiar datos que faltan][screen4a]

4. Ejecute el experimento haciendo clic en **EJECUTAR** en el lienzo de experimentos.

Cuando el experimento finalice, todos los módulos tendrán una marca de verificación verde para indicar que se han completado correctamente. Observe también el estado **Ejecución finalizada** en la esquina superior derecha.

![First experiment run][screen5]

Todo lo que hemos hecho en el experimento hasta el momento es limpiar los datos. Si quiere ver el conjunto de datos limpio, haga clic en el puerto de salida izquierdo del módulo [Limpiar datos que faltan][clean-missing-data] \("Conjunto de datos limpiado") y seleccione **Visualizar**. Observe que la columna **normalized-losses** ya no se incluye y que no hay valores que faltan.

Ahora los datos están limpios y ya puede especificar qué características se van a usar en el modelo predictivo.

## Paso 3: Definir las características

En el aprendizaje automático, las *características* son propiedades mensurables individuales de algo que le interesa. En nuestro conjunto de datos, cada fila representa un automóvil y cada columna es una característica de ese automóvil.

Encontrar un buen conjunto de funciones para la creación de un modelo de predicción requiere experimentación y conocimientos acerca del problema que desea resolver. Algunas características son mejores para predecir el destino que otras. Además, algunas funciones tienen una fuerte correlación con otras (por ejemplo, city-mpg frente a highway-mpg), por lo tanto, no agregarán mucha información nueva al modelo y se podrán eliminar.

Creemos un modelo que use un subconjunto de las funciones de nuestro conjunto de datos. Puede volver y seleccionar diferentes funciones, ejecutar de nuevo el experimento y ver si obtiene mejores resultados. Pero, para empezar, vamos a intentar las siguientes características:

	make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price


1. Arrastre otro módulo [Seleccionar columnas en el conjunto de datos][select-columns] al lienzo de experimentos y conéctelo al puerto de salida izquierdo del módulo [Limpiar datos que faltan][clean-missing-data]. Haga doble clic en el módulo y escriba "Seleccionar funciones para la predicción".

2. Haga clic en **Iniciar el selector de columnas** en el panel **Propiedades**.

3. Haga clic en **With rules** (Con reglas).

4. En **Empezar por** haga clic en **No columns** (Ninguna columna) y luego seleccione **Incluir** y **nombres de columna** en la fila de filtro. Escriba nuestra lista de nombres de columnas. Esto indica al módulo que pase solo las columnas que le especificamos.

	> [AZURE.TIP] Mediante la ejecución del experimento, hemos garantizado que las definiciones de columna de nuestros datos han pasado desde el conjunto de datos a través del módulo [Limpiar datos que faltan][clean-missing-data]. Esto significa que otros módulos que se conecten también dispondrán de la información del conjunto de datos.

5. Haga clic en el botón de marca de verificación (Aceptar).

![Select columns][screen6]

De esta forma se produce el conjunto de datos que se usará en el algoritmo de aprendizaje en los próximos pasos. Posteriormente puede volver e intentarlo de nuevo con una selección diferente de características.

## Paso 4: Elegir y aplicar un algoritmo de aprendizaje

Ahora que los datos están listos, la construcción de un modelo predictivo consiste en entrenar y probar. Usaremos nuestros datos para entrenar el modelo y luego probarlo para ver lo que se aproxima en sus predicciones de los precios. Por ahora, no se preocupe de por qué necesitamos entrenar y probar un modelo.

*Clasificación* y *regresión* son dos tipos de técnicas de aprendizaje automático supervisado. La clasificación permite predecir una respuesta a partir de un conjunto definido de categorías, como el color (rojo, azul o verde). La regresión se usa para predecir un número.

Como queremos predecir un precio, que es un número, vamos a usar el modelo de regresión. En este ejemplo, entrenaremos un modelo sencillo de *regresión lineal* y en el siguiente paso lo probaremos.

1. Usaremos nuestros datos para el entrenamiento y la prueba si los dividimos en conjuntos diferentes para entrenamiento y prueba. Seleccione y arrastre el módulo [Dividir datos][split] al lienzo de experimentos y conéctelo a la salida del último módulo [Seleccionar columnas en el conjunto de datos][select-columns]. Establezca **Fracción de filas del primer conjunto de datos de salida** en 0,75. De esta forma, usaremos el 75 % de los datos para entrenar el modelo y retendremos el 25 % para prueba.

	> [AZURE.TIP] Al cambiar el parámetro **Valor de inicialización aleatorio**, puede producir muestras aleatorias diferentes para entrenamiento y prueba. Este parámetro controla la inicialización del generador de números pseudoaleatorios.

2. Ejecute el experimento. De esta forma, los módulos [Seleccionar columnas en el conjunto de datos][select-columns] y [Dividir datos][split] pasan las definiciones de columna a los módulos que se agregarán a continuación.

3. Para seleccionar el algoritmo de aprendizaje, expanda la categoría **Aprendizaje automático** en la paleta de módulos situada a la izquierda del lienzo y luego expanda **Inicializar modelo**. Se muestran varias categorías de módulos que se pueden usar para inicializar algoritmos de Aprendizaje automático.

	En este experimento de ejemplo, seleccione el módulo [Regresión lineal][linear-regression] en la categoría **Regresión** (también puede encontrar el módulo si escribe "regresión lineal" en el cuadro de búsqueda de la paleta) y arrástrelo al lienzo de experimentos.

4. Busque y arrastre el módulo [Train Module][train-model] \(Entrenar modelo) al lienzo del experimento. Conecte el puerto de entrada izquierdo a la salida del módulo [Regresión lineal][linear-regression]. Conecte el puerto de entrada derecho a la salida de datos de entrenamiento (puerto izquierdo) del módulo [Dividir datos][split].

5. Seleccione el módulo [Entrenar modelo][train-model], haga clic en **Iniciar el selector de columnas** en el panel **Propiedades** y luego seleccione la columna **precio**. Este es el valor que nuestro modelo va a predecir.

	![Select "price" column][screen7]

6. Ejecute el experimento.

El resultado es un modelo de regresión entrenado que se puede usar para puntuar las nuevas muestras para realizar predicciones.

![Aplicación del algoritmo de Aprendizaje automático][screen8]

## Paso 5: Predecir los precios de los automóviles nuevos

Ahora que hemos entrenado el modelo usando el 75 % de nuestros datos, podemos usarlo para puntuar el otro 25 % de los datos y ver cómo funciona el modelo.

1. Busque y arrastre el módulo [Puntuar modelo][score-model] al lienzo de experimentos y conecte el puerto de entrada izquierdo a la salida del módulo [Entrenar modelo][train-model]. Conecte el puerto de entrada derecho a la salida de datos de prueba (puerto derecho) del módulo [Dividir datos][split].

	![Score Model module][screen8a]

2. Para ejecutar el experimento y ver el resultado del módulo [Puntuar modelo][score-model], haga clic en el puerto de salida y seleccione **Visualizar**. La salida muestra los valores previstos para el precio y los valores conocidos de los datos de prueba.

3. Finalmente, para probar la calidad de los resultados, seleccione el módulo [Evaluar modelo][evaluate-model] y arrástrelo al lienzo de experimentos, y luego conecte el puerto de entrada izquierdo a la salida del módulo [Puntuar modelo][score-model]. (Hay dos puertos de entrada debido a que el módulo [Evaluar modelo][evaluate-model] puede usarse para comparar dos modelos.)

4. Ejecute el experimento.

Para ver la salida del módulo [Evaluate Model][evaluate-model] \(Evaluar modelo), haga clic en el puerto de salida y seleccione **Visualize** (Visualizar). Se muestran las siguientes estadísticas para nuestro modelo:

- **Desviación media** (MAE): la media de errores absolutos (un *error* es la diferencia entre el valor de predicción y el valor real).
- **Raíz cuadrada de errores** (RMSE): la raíz cuadrada de la media de errores al cuadrado de las predicciones realizadas sobre el conjunto de datos de prueba.
- **Error absoluto relativo**: la media de errores absolutos en relación con la diferencia absoluta entre los valores reales y la media de todos los valores reales.
- **Error al cuadrado relativo**: la media de errores al cuadrado en relación con la diferencia al cuadrado entre los valores reales y la media de todos los valores reales.
- **Coeficiente de determinación**: conocido también como **valor R cuadrado**, es una métrica estadística que indica cómo de bien un modelo se ajusta a los datos.

Para cada una de las estadísticas de errores, cuanto menor sea el valor, mejor. Un valor inferior indica que las predicciones se adaptan más estrechamente a los valores reales. En **Coeficiente de determinación**, cuanto más cerca está el valor de uno (1,0), mejores son las predicciones.

![Evaluation results][screen9]

El experimento final se debería parecer a este:

![Tutorial de Aprendizaje del equipo: Finalización del experimento de regresión lineal que utiliza las técnicas de modelado predictivo.][screen10]

## Pasos siguientes

Ahora que ha completado un primer tutorial de Aprendizaje automático y que tiene el experimento configurado, puede recorrerlo en iteración para intentar mejorar el modelo. Por ejemplo, puede cambiar las características que usa en la predicción. O bien, puede modificar las propiedades del algoritmo [Regresión lineal][linear-regression] o probar un algoritmo completamente diferente. Incluso puede agregar varios algoritmos de Aprendizaje automático a la vez al experimento y comparar los dos mediante el módulo [Evaluar modelo][evaluate-model].

> [AZURE.TIP] Use el botón **GUARDAR COMO** en el lienzo de experimentos para copiar cualquier iteración del experimento. Puede ver todas las iteraciones del experimento haciendo clic en **VER HISTORIAL DE EJECUCIÓN** en el lienzo. Consulte [Administración de iteraciones de experimentos en Estudio de aprendizaje automático de Azure][runhistory] para obtener más detalles.

[runhistory]: machine-learning-manage-experiment-iterations.md

Si está satisfecho con su modelo, puede implementarlo como un servicio web a fin de usarlo para predecir precios de automóviles usando nuevos datos. Consulte [Implementar un servicio web de Aprendizaje automático de Azure][publish] para obtener más detalles.

[publish]: machine-learning-publish-a-machine-learning-web-service.md

Si desea un tutorial más amplio y detallado de las técnicas de modelado predictivo para crear, entrenar, puntuar e implementar un modelo, consulte [Desarrollo de una solución predictiva mediante Aprendizaje automático de Azure][walkthrough].

[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- Images -->
[screen1]: ./media/machine-learning-create-experiment/screen1.png
[screen1a]: ./media/machine-learning-create-experiment/screen1a.png
[screen1b]: ./media/machine-learning-create-experiment/screen1b.png
[screen1c]: ./media/machine-learning-create-experiment/screen1c.png
[screen2]: ./media/machine-learning-create-experiment/screen2.png
[screen3]: ./media/machine-learning-create-experiment/screen3.png
[screen4]: ./media/machine-learning-create-experiment/screen4.png
[screen4a]: ./media/machine-learning-create-experiment/screen4a.png
[screen5]: ./media/machine-learning-create-experiment/screen5.png
[screen6]: ./media/machine-learning-create-experiment/screen6.png
[screen7]: ./media/machine-learning-create-experiment/screen7.png
[screen8]: ./media/machine-learning-create-experiment/screen8.png
[screen8a]: ./media/machine-learning-create-experiment/screen8a.png
[screen9]: ./media/machine-learning-create-experiment/screen9.png
[screen10]: ./media/machine-learning-create-experiment/complete-linear-regression-experiment.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

<!---HONumber=AcomDC_0914_2016-->