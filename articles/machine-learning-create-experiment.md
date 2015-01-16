<properties title="Create a simple experiment in Azure Machine Learning Studio" pageTitle="Creación de un experimento sencillo en el Estudio de aprendizaje automático | Azure" description="Cómo crear un experimento para entrenar y probar un modelo simple en Estudio de aprendizaje automático de Azure" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/22/2014" ms.author="garye" />

#Creación de un experimento sencillo en el Estudio de aprendizaje automático de Azure 
 
Básicamente, un experimento de análisis predictivo consta de componentes para *crear un modelo*, *entrenar el modelo* y *puntuar y probar el modelo*. Estos componentes se pueden combinar para crear un experimento que capture datos, entrene a un modelo con ellos y aplique el modelo a nuevos datos. También se pueden agregar módulos para procesar previamente los datos y seleccionar las características, dividir los datos en conjuntos de prueba y de entrenamiento y evaluar o validar de forma cruzada la calidad del modelo.  

En este artículo, usaremos el Estudio de aprendizaje automático de Microsoft Azure para desarrollar e iterar un experimento de análisis predictivo sencillo.

##Cinco pasos para crear un experimento 

Los cinco pasos básicos que debe seguir para crear un experimento en ML Studio le van a permitir crear, entrenar y puntuar el modelo:  

- Creación de un modelo 
	- [Paso 1: Obtener los datos]
	- [Paso 2: Procesar previamente los datos]
	- [Paso 3: Definir las características]
- Entrenamiento del modelo 
	- [Paso 4: Elegir y aplicar un algoritmo de aprendizaje]
- Puntuación y prueba del modelo 
	- [Paso 5: Predecir sobre nuevos datos] 

[Paso 1: Obtener los datos]: #step-1-get-data
[Paso 2: Procesar previamente los datos]: #step-2-pre-process-data
[Paso 3: Definir las características]: #step-3-define-features
[Paso 4: Elegir y aplicar un algoritmo de aprendizaje]: #step-4-choose-and-apply-a-learning-algorithm
[Paso 5: Predecir sobre nuevos datos]: #step-5-predict-over-new-data 

En este ejemplo, le guiaremos por los pasos para crear un modelo de regresión mediante el uso de datos de automóvil de ejemplo. El objetivo es predecir el precio de un automóvil usando diferentes variables como marca y especificaciones técnicas. 

### Paso 1: Obtener los datos

ML Studio incluye varios conjuntos de datos de ejemplo, y se pueden importar datos de muchos orígenes diferentes. En este ejemplo, usaremos el conjunto de datos de ejemplo incluido, **Automobile price data (Raw)**, que representa los datos de precio de automóviles.

1. Para iniciar un nuevo experimento, haga clic en **+NUEVO** en la parte inferior de la ventana de ML Studio y seleccione **EXPERIMENTO**. Cambie el nombre del experimento "Sin título" por algo descriptivo, como "Predicción de precio de automóvil".

2. A la izquierda del lienzo de experimentos, hay una paleta de conjuntos de datos y modelos. Escriba "automobile" en el cuadro de búsqueda de la parte superior de la paleta para encontrar el conjunto de datos llamado **Automobile price data (Raw)**. 

	![Palette search][screen1a]

3. Arrastre el conjunto de datos al lienzo de experimentos. 

	![Dataset][screen1]

Para ver la apariencia de estos datos, haga clic en el puerto de salida en la parte inferior del conjunto de datos de automóvil y seleccione **Visualizar**. Las variables del conjunto de datos aparecen como columnas, y cada instancia de un automóvil aparece como una fila. La columna de más a la derecha "price" (columna 26) es la variable de destino que vamos a intentar predecir. 

![Dataset visualization][screen1b]

Cierre la ventana de visualización; para ello, haga clic en la "**x**" en la esquina superior derecha.

### Paso 2: Procesar previamente los datos

Normalmente, un conjunto de datos requiere algún procesamiento previo antes de que se pueda analizar. Habrá observado los valores que faltan en las columnas de varias filas; para analizar los datos, es necesario limpiar estos valores que faltan. En nuestro caso, eliminaremos simplemente las filas con valores que faltan. Además, la columna "normalized-losses" tiene una proporción muy grande de valores que faltan, así que simplemente excluiremos esa columna completamente del modelo. 

>**Sugerencia**: limpiar los valores que faltan de los datos de entrada es un requisito previo para usar la mayoría de los módulos. 

En primer lugar, eliminaremos la columna "normalized-losses" y luego las filas que tienen datos que faltan. 

1. Arrastre el módulo **Columnas de proyecto** al lienzo de experimentos y conéctelo al puerto de salida del conjunto de datos **Automobile price data (Raw)**. Este módulo nos permite seleccionar las columnas de datos que queremos incluir o excluir del modelo. 

2. Seleccione el módulo **Columnas de proyecto** y haga clic en **Iniciar el selector de columnas** en el panel de propiedades. 

	- Asegúrese de que la opción **Todas las columnas** esté seleccionada en la lista desplegable de filtros **Comenzar con**. Esto indica a **Columnas de proyecto** que pase todas las columnas (excepto las que vamos a excluir). 
	- En la siguiente fila, seleccione **Excluir** y **nombres de columnas** y, a continuación, haga clic en el cuadro de texto. Se muestra una lista de columnas. Seleccione "normalized-losses" para agregarla al cuadro de texto. 
	- Haga clic en el botón **Aceptar** con la marca de verificación para cerrar el selector de columnas.

    ![Select columns][screen3]
	
	El panel de propiedades de **Columnas de proyecto** indica que se pasarán todas las columnas del conjunto de datos excepto "normalized-losses". 

    ![Project Columns properties][screen4]

    >**Sugerencia:** puede agregar un comentario a un módulo; para ello, haga doble clic en el módulo y escriba texto. Esto puede ayudarle a ver de un vistazo lo que el módulo hace en el experimento. En este caso, haga doble clic en el módulo **Columnas de proyecto** y escriba el comentario "Excluir normalized-losses". 

3. Arrastre el módulo **Limpieza de valores que faltan** al lienzo de experimentos y conéctelo al módulo **Columnas de proyecto**. En el panel de propiedades, seleccione **Quitar la fila entera** en **De valores que faltan** para limpiar los datos eliminando las filas que tienen valores que faltan.  Haga doble clic en el módulo y escriba el comentario "Quitar filas con valores que faltan".

	![Missing Values Scrubber properties][screen4a]

4. Ejecute el experimento; para ello, haga clic en **EJECUTAR** bajo el lienzo de experimentos.

Cuando el experimento finalice, todos los módulos tendrán una marca de verificación verde para indicar que se han completado correctamente. Observe también el estado "Ejecución finalizada" en la esquina superior derecha.

![First experiment run][screen5]

Hasta este momento, todo lo que se ha hecho en el experimento es limpiar los datos. Para ver el conjunto de datos limpiado, haga clic en el puerto de salida del módulo **Limpieza de valores que faltan** y seleccione **Visualizar**. Observe que la columna "normalized-losses" ya no se incluye y que no hay valores que faltan.

Ahora los datos están limpios y ya puede especificar qué características se van a usar en el modelo predictivo.

### Paso 3: Definir las características

En el aprendizaje automático, *características* son propiedades mensurables individuales de algo que le interesa. En nuestro conjunto de datos, cada fila representa un automóvil, y cada columna es una característica de ese automóvil. Encontrar un buen conjunto de características para crear un modelo predictivo requiere experimentar y conocer el problema en cuestión. Algunas características son mejores para predecir el destino que otras. Además, algunas características tienen una fuerte correlación con otras características, por ejemplo city-mpg frente a highway-mpg, así que no agregarán mucha información nueva al modelo y se eliminarán.

Vamos a crear un modelo que usa un subconjunto de las características de nuestro conjunto de datos. Ahora puede volver y seleccionar diferentes características, ejecutar de nuevo el experimento y ver si obtiene mejores resultados. Como primera suposición, seleccionaremos las siguientes características (columnas) con el módulo **Columnas de proyecto**. Tenga en cuenta que para entrenar el modelo debemos incluir el valor *precio* que vamos a predecir.

	make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price

1. Arrastre otro módulo **Columnas de proyecto** al lienzo de experimentos y conéctelo al módulo **Limpieza de valores que faltan**. Haga doble clic en el módulo y escriba "Seleccionar características para predicción".

2. Haga clic en **Iniciar el selector de columnas** en el panel de propiedades. 

3. En el selector de columnas, seleccione **Sin columnas** para **Comenzar con** y, a continuación, seleccionar **Incluir** y **nombres de columnas** en la fila de filtro. Escriba nuestra lista de nombres de columnas. Esto indica al módulo que pase solamente las columnas que hemos especificado.

	>**Sugerencia:** como hemos llegado hasta este punto en nuestro experimento, las definiciones de columna de nuestros datos han pasado desde el conjunto de datos original hasta el módulo **Limpieza de valores que faltan**. Cuando conecta **Columnas de proyecto** a **Limpieza de valores que faltan**, el módulo **Columnas de proyecto** descubre las definiciones de columna de nuestros datos. Cuando hace clic en el cuadro de nombres de columna, se muestra una lista de columnas y puede seleccionar una a una las columnas que quiere agregar a la lista. 

4. Haga clic en **Aceptar**.

![Select columns][screen6]

De esta forma se producirá el conjunto de datos que se usará en el algoritmo de aprendizaje en los próximos pasos. Posteriormente puede volver e intentarlo de nuevo con una selección diferente de características. 

### Paso 4: Elegir y aplicar un algoritmo de aprendizaje

Ahora que los datos están listos, la construcción de un modelo predictivo consiste en entrenar y probar. *Clasificación* y *regresión* son dos tipos de técnicas de aprendizaje automático supervisado. La clasificación se usa para realizar una predicción a partir de un conjunto definido de valores, como el color (rojo, azul o verde). La regresión se usa para realizar una predicción a partir de un conjunto continuo de valores, como la edad de una persona.

Queremos predecir el precio de un automóvil, que puede ser cualquier valor, así que usaremos un modelo de regresión. En este ejemplo, entrenaremos un modelo sencillo de *regresión lineal* y en el siguiente paso lo probaremos. 

1. Divida los datos en conjuntos de entrenamiento y de prueba. Seleccione y arrastre el módulo **Dividir** al lienzo de experimentos y conéctelo a la salida del último módulo **Columnas de proyecto**. Establezca **Fracción de filas del primer conjunto de datos de salida** en 0,75. De esta forma, usaremos el 75 % de los datos para entrenar el modelo y retendremos el 25 % para prueba.

	>**Sugerencia:** al cambiar el parámetro **Valor de inicialización aleatorio**, puede producir muestras aleatorias diferentes para entrenamiento y prueba. Este parámetro controla la inicialización del generador de números pseudoaleatorios.
	
2. Ejecute el experimento. De esta forma los módulos **Columnas de proyecto** y **Dividir** pasan las definiciones de columna a los módulos que vamos a agregar a continuación.  

2. Para seleccionar el algoritmo de aprendizaje, expanda la categoría **Aprendizaje automático** en la paleta de módulos situada a la izquierda del lienzo y luego expanda **Inicializar modelo**. Se muestran varias categorías de módulos que se pueden usar para inicializar un algoritmo de aprendizaje. 

	En este experimento de ejemplo, seleccione el módulo **Regresión lineal** en la categoría **Regresión** (también puede encontrar el módulo si escribe "Regresión lineal" en el cuadro de búsqueda de la paleta) y arrástrelo al lienzo de experimentos.

3. Busque el módulo **Entrenar modelo** y arrástrelo al experimento. Haga clic en **Iniciar selector de columnas** y seleccione la columna *price*. Este es el valor que nuestro modelo va a predecir.

	![Select "price" column][screen7]

4. Conecte el puerto de entrada izquierdo a la salida del módulo **Regresión lineal**, y el puerto de entrada derecho a la salida de datos de entrenamiento (puerto izquierdo) del módulo **Dividir**.  

5. Ejecute el experimento. 

El resultado es un modelo de regresión entrenado que se puede usar para puntuar las nuevas muestras para realizar predicciones. 

![Applying the learning algorithm][screen8]

### Paso 5: Predecir sobre nuevos datos 

Ahora que hemos entrenado el modelo, podemos usarlo para puntuar el otro 25 % de nuestros datos y ver cómo funciona de bien nuestro modelo. 

1. Busque el módulo **Puntuar modelo** y arrástrelo al lienzo de experimentos, conecte el puerto de entrada izquierdo a la salida del módulo **Entrenar modelo** y el puerto de entrada derecho a la salida de datos de prueba (puerto derecho) del módulo **Dividir**.  

	![Score Model module][screen8a]

2. Ejecute el experimento y vea la salida del módulo **Puntuar modelo** (haga clic en el puerto de salida y seleccione **Visualizar**). La salida mostrará los valores de precio previstos junto con los valores conocidos de los datos de prueba.  

3. Por último, para probar la calidad de los resultados, seleccione el módulo **Evaluar modelo** y arrástrelo al lienzo de experimentos y conecte el puerto de entrada izquierdo a la salida del módulo **Puntuar modelo** module (hay dos puertos de entrada porque el módulo **Evaluar modelo** se puede usar para comparar dos modelos).
 
4. Ejecute el experimento y vea la salida del módulo **Evaluar modelo** (haga clic en el puerto de salida y seleccione **Visualizar**). Se muestran las siguientes estadísticas para nuestro modelo:

	- **Desviación media** (MAE): la media de errores absolutos (un *error* es la diferencia entre el valor previsto y el valor real).
	- **Raíz cuadrada de errores** (RMSE): la raíz cuadrada de la media de errores al cuadrado de las predicciones realizadas en el conjunto de datos de prueba.
	- **Error absoluto relativo**: la media de errores absolutos en relación con la diferencia absoluta entre los valores reales y la media de todos los valores reales.
	- **Error al cuadrado relativo**: la media de errores al cuadrado en relación con la diferencia al cuadrado entre los valores reales y la media de todos los valores reales.
	- **Coeficiente de determinación**: conocido también como "valor R cuadrado", es una métrica estadística que indica cómo de buen un modelo se ajusta a los datos.
	
	Cuanto más pequeñas sean estas estadísticas de error mejor. Un valor más pequeño indica que las predicciones se aproximan mucho a los valores reales. En **Coeficiente de determinación**, cuanto más cerca está el valor de uno (1,0), mejores son las predicciones.

	![Evaluation results][screen9]

El experimento final se debería parecer a este:

![Complete experiment][screen10]

### Pasos siguientes

Ahora que tiene preparado el experimento, puede iteracionar para intentar mejorar el modelo. Por ejemplo, puede cambiar las características que usa en la predicción. O bien, puede modificar las propiedades del algoritmo **Regresión lineal** o intentar un algoritmo completamente diferente. Incluso puede agregar varios algoritmos al experimento de una vez y compararlos (dos a la vez) mediante el módulo **Evaluar modelo**. 

>**Sugerencia**: use el botón **GUARDAR COMO** que aparece debajo del lienzo de experimentos para realizar una copia de cualquier iteración del experimento. Puede ver todas las iteraciones del experimento si hace clic en **VER HISTORIAL DE EJECUCIÓN** bajo el lienzo. Consulte el tema de ayuda de ML Studio acerca de cómo **ver el historial de ejecución** para obtener más detalles.

Si está satisfecho con su modelo, puede publicarlo como un servicio web a fin de usarlo para predecir precios de automóviles usando nuevos datos. Consulte el tema de ayuda de ML Studio acerca de cómo **publicar los experimentos** para obtener más detalles.

Para obtener un tutorial más amplio y detallado para crear, entrenar, puntuar y publicar un modelo predictivo, consulte [Tutorial: Desarrollo de una solución predictiva con Aprendizaje automático de Azure](http://azure.microsoft.com/es-es/documentation/articles/machine-learning-walkthrough-develop-predictive-solution/). 


<!-- Images -->
[screen1]:./media/machine-learning-create-experiment/screen1.png
[screen1a]:./media/machine-learning-create-experiment/screen1a.png
[screen1b]:./media/machine-learning-create-experiment/screen1b.png
[screen2]:./media/machine-learning-create-experiment/screen2.png
[screen3]:./media/machine-learning-create-experiment/screen3.png
[screen4]:./media/machine-learning-create-experiment/screen4.png
[screen4a]:./media/machine-learning-create-experiment/screen4a.png
[screen5]:./media/machine-learning-create-experiment/screen5.png
[screen6]:./media/machine-learning-create-experiment/screen6.png
[screen7]:./media/machine-learning-create-experiment/screen7.png
[screen8]:./media/machine-learning-create-experiment/screen8.png
[screen8a]:./media/machine-learning-create-experiment/screen8a.png
[screen9]:./media/machine-learning-create-experiment/screen9.png
[screen10]:./media/machine-learning-create-experiment/screen10.png
