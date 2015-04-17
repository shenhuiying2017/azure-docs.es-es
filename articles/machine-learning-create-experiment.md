<properties 
	pageTitle="Creación de un experimento sencillo en el Estudio de aprendizaje automático | Azure" 
	description="Cómo crear un experimento para entrenar y probar un modelo simple en Estudio de aprendizaje automático de Azure" 
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
	ms.date="01/07/2015" 
	ms.author="garye"/>

#Creación de un experimento sencillo en el Estudio de aprendizaje automático de Azure 
 
Un experimento de análisis predictivo, en esencia, consta de componentes para *Crear un modelo*, *Entrenamiento del modelo* y *Puntuación y prueba del modelo*. Estos componentes se pueden combinar para crear un experimento que capture datos, entrene a un modelo con ellos y aplique el modelo a nuevos datos. También se pueden agregar módulos para procesar previamente los datos y seleccionar las funciones, dividir los datos en conjuntos de formación y prueba y evaluar o validar de forma cruzada la calidad del modelo.  

En este artículo, usaremos el Azure Machine Learning Studio para desarrollar e iterar un experimento de análisis predictivo sencillo. Para abrir Machine Learning Studio, haga clic en este vínculo: [https://studio.azureml.net/Home](https://studio.azureml.net/Home). Para obtener más información acerca de cómo empezar a utilizar Machine Learning Studio, consulte [Inicio de Microsoft Azure Machine Learning Studio](https://studio.azureml.net/). 
 

##Cinco pasos para crear un experimento 

A continuación se enumeran los cinco pasos que puede llevar a cabo para crear un experimento en Machine Learning Studio para permitirle crear, formar y puntuar el modelo:  

- Crear un modelo 
	- [Paso 1: Obtener los datos]
	- [Paso 2: Preprocesar los datos]
	- [Paso 3: Definir las características]
- Formar el modelo 
	- [Paso 4: Elegir y aplicar un algoritmo de aprendizaje]
- Puntuar y probar el modelo 
	- [Paso 5: Predecir nuevos datos] 

[Paso 1: Obtener los datos]: #step-1-get-data
[Paso 2: Preprocesar los datos]: #step-2-preprocess-data
[Paso 3: Definir las características]: #step-3-define-features
[Paso 4: Elegir y aplicar un algoritmo de aprendizaje]: #step-4-choose-and-apply-a-learning-algorithm
[Paso 5: Predecir nuevos datos]: #step-5-predict-over-new-data 

En este ejemplo, le guiaremos a través de la creación de un modelo de regresión que utiliza datos de automóviles de ejemplo. El objetivo es predecir el precio de un automóvil usando diferentes variables como marca y especificaciones técnicas. 

### Paso 1: Obtener los datos

Hay una serie de conjuntos de datos de ejemplo incluidos en Machine Learning Studio y puede importar datos desde varios orígenes. En este ejemplo, usaremos el conjunto de datos de ejemplo incluido, **Automobile price data (Raw)**, que representa los datos de precios de automóviles.

1. Inicie un experimento nuevo haciendo clic en **+ NUEVO** en la parte inferior de la ventana de Machine Learning Studio, seleccione **EXPERIMENTO**y, a continuación, seleccione "Experimento en blanco". Seleccione el nombre del experimento predeterminado en la parte superior del lienzo y asígnele un nombre significativo, por ejemplo, **Predicción del precio de automóviles**.

2. A la izquierda del lienzo de experimentos, hay una paleta de conjuntos de datos y módulos. Escriba **automobile** en el cuadro de búsqueda de la parte superior de la paleta para encontrar el conjunto de datos llamado **Automobile price data (Raw)**. 

	![Palette search][screen1a]

3. Arrastre el conjunto de datos al lienzo de experimentos. 

	![Dataset][screen1]

Para ver la apariencia de estos datos, haga clic en el puerto de salida en la parte inferior del conjunto de datos de automóvil y seleccione **Visualizar**. Las variables del conjunto de datos aparecen como columnas, y cada instancia de un automóvil aparece como una fila. La columna de la derecha (columna 26 y la titulada "precio") es la variable de destino que vamos a intentar predecir. 

![Dataset visualization][screen1b]

Cierre la ventana de visualización haciendo clic en la "**x**" en la esquina superior derecha.

### Paso 2: Preprocesar los datos

Normalmente, un conjunto de datos requiere algún procesamiento previo antes de que se pueda analizar. Es posible que haya observado los valores que faltan en las columnas de varias filas. Para analizar los datos, es necesario limpiar estos valores que faltan. En nuestro caso, quitaremos todas las filas que tengan valores ausentes. Además, la columna **pérdidas normalizadas** tiene una gran proporción de valores que faltan, por lo que excluiremos esa columna del modelo por completo. 

> [AZURE.TIP] Limpiar los valores que faltan de los datos de entrada es un requisito previo para usar la mayoría de los módulos. 

En primer lugar, eliminaremos la columna **normalized-losses** y luego las filas que tienen datos que faltan. 

1. Escriba **columnas de proyecto** en el cuadro de búsqueda en la parte superior de la paleta del módulo para encontrar el módulo **Columnas de proyecto** y, a continuación, arrástrelo hasta el lienzo del experimento y conéctelo al puerto de salida del conjunto de datos **Automobile price data (Raw)**. Este módulo nos permite seleccionar las columnas de datos que queremos incluir o excluir del modelo. 

2. Seleccione el módulo **Columnas de proyecto** y haga clic en **Iniciar el selector de columnas** en el panel de propiedades. 

	- Asegúrese de que **Todas las columnas** esté seleccionado en la lista desplegable del filtro **Comenzar con**. Esto indica a **Columnas del proyecto** que pase todas las columnas (excepto las que se van a excluir). 
	- En la siguiente fila, seleccione **Excluir** y **nombres de columnas** y luego haga clic en el cuadro de texto. A continuación, se mostrará una lista de columnas. Seleccione **Pérdidas normalizadas** y se agregará al cuadro de texto. 
	- Haga clic en el botón Aceptar con la marca de verificación para cerrar el selector de columnas.

    ![Select columns][screen3]
	
	El panel de propiedades de **Columnas de proyecto** indica que se pasarán todas las columnas del conjunto de datos excepto **normalized-losses**. 

    ![Project Columns properties][screen4]

    > [AZURE.TIP] Puede agregar un comentario a un módulo; para ello, haga doble clic en el módulo y escriba texto. Esto puede ayudarle a ver de un vistazo lo que el módulo hace en el experimento. En este caso, haga doble clic en el módulo **Columnas del proyecto** y escriba el comentario "Excluir pérdidas normalizadas". 

3. Arrastre el módulo **Limpieza de valores que faltan** al lienzo de experimentos y conéctelo al módulo **Columnas de proyecto**. En el panel de **Propiedades**, seleccione **Quitar la fila entera** en **De valores que faltan** para limpiar los datos eliminando las filas que tienen valores que faltan. Haga doble clic en el módulo y escriba el comentario "Quitar las filas sin valor".

	![Missing Values Scrubber properties][screen4a]

4. Ejecute el experimento haciendo clic en **EJECUTAR** en el lienzo del experimento.

Cuando el experimento finalice, todos los módulos tendrán una marca de verificación verde para indicar que se han completado correctamente. Observe también el estado **Ejecución finalizada** en la esquina superior derecha.

![First experiment run][screen5]

Todo lo que hemos hecho en el experimento hasta el momento es limpiar los datos. Para ver el conjunto de datos limpiado, haga clic en el puerto de salida del módulo **Limpieza de valores que faltan** y seleccione **Visualizar**. Observe que la columna **normalized-losses** ya no se incluye y que no hay valores que faltan.

Ahora los datos están limpios y ya puede especificar qué características se van a usar en el modelo predictivo.

### Paso 3: Definir las características

En el aprendizaje automático,  *features* son propiedades mensurables individuales de algo que le interesa. En nuestro conjunto de datos, cada fila representa un automóvil, y cada columna es una característica de ese automóvil. Encontrar un buen conjunto de funciones para la creación de un modelo de predicción requiere experimentación y conocimientos acerca del problema que desea resolver. Algunas características son mejores para predecir el destino que otras. Además, algunas funciones tienen una fuerte correlación con otras (por ejemplo, city-mpg frente a highway-mpg), por lo tanto, no agregarán mucha información nueva al modelo y se podrán eliminar.

Creemos un modelo que use un subconjunto de las funciones de nuestro conjunto de datos. Puede volver y seleccionar diferentes funciones, ejecutar de nuevo el experimento y ver si obtiene mejores resultados. Como primera suposición, seleccionaremos las siguientes características (columnas) con el módulo **Columnas de proyecto**. Tenga en cuenta que para entrenar el modelo debemos incluir el valor  *price* que vamos a predecir.

	make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price

1. Arrastre otro módulo **Columnas de proyecto** al lienzo de experimentos y conéctelo al módulo **Limpieza de valores que faltan**. Haga doble clic en el módulo y escriba "Seleccionar funciones para la predicción".

2. Haga clic en **Iniciar el selector de columnas** en el panel de **propiedades**. 

3. En el selector de columnas, seleccione **Sin columnas** para **Comenzar con** y luego seleccione **Incluir** y **nombres de columnas** en la fila de filtro. Escriba nuestra lista de nombres de columnas. Esto indica al módulo que pase solo las columnas que le especificamos.

	> [AZURE.TIP] Debido a que hemos ejecutado el experimento, las definiciones de columna de nuestros datos han pasado del conjunto de datos original al módulo **Limpieza de valores que faltan**. Cuando conecta **Columnas de proyecto** a **Limpieza de valores que faltan**, el módulo **Columnas de proyecto** descubre las definiciones de columna de nuestros datos. Cuando hace clic en el cuadro de **nombres de columna**, se muestra una lista de columnas y puede seleccionar las columnas que quiere agregar a la lista. 

4. Haga clic en el botón de marca de verificación (Aceptar).

![Select columns][screen6]

De esta forma se produce el conjunto de datos que se usará en el algoritmo de aprendizaje en los próximos pasos. Posteriormente puede volver e intentarlo de nuevo con una selección diferente de características. 

### Paso 4: Elegir y aplicar un algoritmo de aprendizaje

Ahora que los datos están listos, la creación de un modelo de predicción se compone de los pasos de formación y de prueba. *Classification* y *regression* son dos tipos de técnicas de aprendizaje automático supervisado. La clasificación se usa para realizar una predicción a partir de un conjunto definido de valores, como el color (rojo, azul o verde). La regresión se usa para realizar una predicción a partir de un conjunto continuo de valores, como la edad de una persona.

Queremos predecir el precio de un automóvil, que puede ser cualquier valor, así que usaremos un modelo de regresión. En este ejemplo, formaremos un modelo simple *linear regression*, y en el paso siguiente, lo probaremos. 

1. Divida los datos en conjuntos de entrenamiento y de prueba. Seleccione y arrastre el módulo **Dividir** al lienzo de experimentos y conéctelo a la salida del último módulo **Columnas de proyecto**. Establezca **Fracción de filas del primer conjunto de datos de salida** en 0,75. De esta forma, usaremos el 75 % de los datos para entrenar el modelo y retendremos el 25 % para prueba.

	> [AZURE.TIP] Al cambiar el parámetro **Valor de inicialización aleatorio**, puede producir muestras aleatorias diferentes para entrenamiento y prueba. Este parámetro controla la inicialización del generador de números pseudoaleatorios.
	
2. Ejecute el experimento. De esta forma los módulos **Columnas de proyecto** y **Dividir**pasan las definiciones de columna a los módulos que vamos a agregar a continuación.  

3. Para seleccionar el algoritmo de aprendizaje, expanda la categoría **Aprendizaje automático** en la paleta de módulos situada a la izquierda del lienzo y luego expanda **Inicializar modelo**. Se muestran varias categorías de módulos que se pueden usar para inicializar un algoritmo de aprendizaje. 

	En este experimento de ejemplo, seleccione el módulo **Regresión lineal** en la categoría **Regresión** (también puede encontrar el módulo si escribe "Regresión lineal" en el cuadro de búsqueda de la paleta) y arrástrelo al lienzo de experimentos.

4. Busque y arrastre el módulo **Entrenar modelo** al experimento. Conecte el puerto de entrada de izquierdo a la salida del módulo **Regresión lineal**. Conecte el puerto de entrada derecho a la salida de datos de formación (puerto izquierdo) del módulo **División**.

5. Ejecute el experimento para pasar las definiciones de columna al módulo **Formar modelo**.
 
6. Seleccione el módulo **Formar modelo**, haga clic en **Lanzar selector de columna** en el panel **Propiedades** y, a continuación, seleccione la columna **Precio**. Este es el valor que nuestro modelo va a predecir.

	![Select "price" column][screen7]

7. Ejecute el experimento. 

El resultado es un modelo de regresión entrenado que se puede usar para puntuar las nuevas muestras para realizar predicciones. 

![Applying the learning algorithm][screen8]

### Paso 5: Predecir nuevos datos 

Ahora que hemos entrenado el modelo, podemos usarlo para puntuar el otro 25 % de nuestros datos y ver cómo funciona de bien nuestro modelo. 

1. Busque y arrastre el módulo **Puntuar modelo** al lienzo del experimento y conecte el puerto de entrada de izquierdo a la salida del módulo **Modelo de formación**. Conecte el puerto de entrada derecho a la salida de datos de prueba (puerto derecho) del módulo **División**.  

	![Score Model module][screen8a]

2. Para ejecutar el experimento y ver el resultado del módulo **Puntuar modelo**, haga clic en el puerto de salida y seleccione **Visualizar**. La salida muestra los valores previstos para el precio y los valores conocidos de los datos de prueba.  

3. Por último, para probar la calidad de los resultados, seleccione y arrastre el módulo **Evaluar modelo** al lienzo del experimento y conecte el puerto de entrada izquierdo a la salida del módulo **Puntuar modelo**. (Hay dos puertos de entrada debido a que el módulo **Evaluar modelo** puede utilizarse para comparar dos modelos.)
 
4. Para ejecutar el experimento y ver la salida del módulo **Evaluar modelo** (haga clic en el puerto de salida y seleccione **Visualizar**). Se muestran las siguientes estadísticas para nuestro modelo:

	- **Desviación Media** (MAE): El promedio de errores absolutos (un *error* es la diferencia entre el valor de predicción y el valor real).
	- **Raíz cuadrada de errores** (RMSE): la raíz cuadrada de la media de errores al cuadrado de las predicciones realizadas en el conjunto de datos de prueba.
	- **Error absoluto relativo**: la media de errores absolutos en relación con la diferencia absoluta entre los valores reales y la media de todos los valores reales.
	- **Error al cuadrado relativo**: la media de errores al cuadrado en relación con la diferencia al cuadrado entre los valores reales y la media de todos los valores reales.
	- **Coeficiente de determinación**: conocido también como **valor R cuadrado**, es una métrica estadística que indica cómo de buen un modelo se ajusta a los datos.
	
	Para cada una de las estadísticas de errores, cuanto menor sea el valor, mejor. Un valor inferior indica que las predicciones se adaptan más estrechamente a los valores reales. En **Coeficiente de determinación**, cuanto más cerca está el valor de uno (1,0), mejores son las predicciones.

	![Evaluation results][screen9]

El experimento final se debería parecer a este:

![Complete experiment][screen10]

### Pasos siguientes

Ahora que tiene preparado el experimento, puede iteracionar para intentar mejorar el modelo. Por ejemplo, puede cambiar las características que usa en la predicción. O bien, puede modificar las propiedades del algoritmo **Regresión lineal** o intentar un algoritmo completamente diferente. Incluso puede agregar a la vez varios algoritmos al experimento y comparar dos mediante el uso del módulo **Evaluar modelo**. 

> [AZURE.TIP] Use el botón **GUARDAR COMO** en el lienzo del experimento para copiar cualquier iteración del experimento. Puede ver todas las iteraciones del experimento haciendo clic en **VER HISTORIAL DE EJECUCIONES** en el lienzo. Consulte [Administrar iteraciones de experimentos en Aprendizaje automático de Azure][runhistory] para obtener más detalles.

[runhistory]: machine-learning-manage-experiment-iterations.md

Si está satisfecho con su modelo, puede publicarlo como un servicio web a fin de usarlo para predecir precios de automóviles usando nuevos datos. Consulte [Publicar un servicio web de Aprendizaje automático de Azure][publicar] para obtener más detalles.

[publicar]: machine-learning-publish-a-machine-learning-web-service.md

Para obtener un tutorial más amplio y detallado para la creación, formación y publicación de un modelo de predicción, consulte [Desarrollar una solución de predicción mediante Aprendizaje automático de Azure ][tutorial]. 

[tutorial]: machine-learning-walkthrough-develop-predictive-solution.md

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

<!--HONumber=49-->