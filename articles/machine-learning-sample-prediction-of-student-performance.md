<properties title="Azure Machine Learning Sample: Prediction of student performance" pageTitle="Machine Learning Sample: Predict student performance | Azure" description="A sample Azure Machine Learning experiment to develop a model that predicts student performance on tests." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Ejemplo de Aprendizaje automático de Azure: predicción del rendimiento estudiantil

*Puede ver el experimento de ejemplo asociado a este modelo en Estudio de aprendizaje automático, en la sección **EXPERIMENTOS** que se encuentra bajo la pestaña **EJEMPLOS**. El nombre del experimento es:*

    Sample Experiment - Student Performance - Development

## Conjunto de datos y descripción del problema

En este experimento, nuestro conjunto de datos es el conjunto de entrenamiento Algebra\_2008\_2009 de la competición KDD Cup 2010. Este conjunto de datos se puede descargar desde [][]<https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp></a>. El conjunto de datos contiene archivos de registro del sistema de tutorización de estudiantes. Las características suministradas incluyen el identificador del problema y una breve descripción, el identificador del alumno, la marca de tiempo y cuántos intentos realizó el estudiante antes de solucionar el problema de la manera correcta. El problema de aprendizaje es predecir si un estudiante solucionará un problema determinado al primer intento. Siguiendo las reglas de KDD Cup, medimos la precisión de los algoritmos de aprendizaje mediante la [raíz cuadrada del error cuadrático medio][raíz cuadrada del error cuadrático medio] (RMSE). El conjunto de datos original cuenta con 8,9 millones de registros. Para acelerar el experimento, redujimos la muestra del conjunto de datos a las primeras 100.000 filas. El conjunto de datos tiene 23 columnas de varios tipos: valores numéricos, valores categóricos y marca de tiempo. Las columnas están separadas por tabulaciones.

## Flujo de trabajo de desarrollo

El conjunto de datos contiene caracteres no pertenecientes a ASCII que no se pueden manipular mediante el módulo **Aplicar operación R**. Antes de utilizar el conjunto de datos en Passau, quitamos los caracteres no pertenecientes a ASCII utilizando los siguientes comandos de Powershell:

    gc algebra_2008_2009_train.txt –head 100000 | sc algebra_train_small.txt
    sc tmp.txt -Encoding ASCII -Value (gc algebra_train_small.txt)
    cat tmp.txt | %{$_ -replace "\?\?sqrt","+sqrt"} | sc algebra_train_small.txt_ascii  

El archivo algebra\_train\_small.txt\_ascii resultante sigue siendo bastante amplio, abarca 36 millones. Almacenamos este archivo en el almacenamiento de blobs de Azure y, a continuación, cargamos el archivo en el experimento por medio del módulo **Lector**. Los comandos Powershell para almacenar el archivo en el almacenamiento de blobs son los siguientes:

    Add-AzureAccount
    $key = Get-AzureStorageKey -StorageAccountName <your storage account name>
    $ctxt = New-AzureStorageContext -StorageAccountName $key.StorageAccountName -StorageAccountKey $key.Primary
    Set-AzureStorageBlobContent –Container <container name in your storage account> -File "algebra_train_small.txt_ascii" –Context $ctxt

![][0]

Los parámetros del módulo **Lector** se muestran arriba. En este ejemplo, el nombre de la cuenta de almacenamiento es "datascience", el archivo del conjunto de datos algebra\_train\_small.txt\_ascii se coloca en el contenedor "sampleexperiments". La clave de cuenta es una clave de acceso de la cuenta de almacenamiento de Azure. Se puede recuperar de la cuenta en el portal de administración de Azure ([][1]<https://manage.windowsazure.com></a>).

![][2]

En los pasos siguientes, que se muestran arriba, realizamos varias transformaciones para convertir el conjunto de datos en un formato apto para nuestros algoritmos de aprendizaje. Utilizamos el **Editor de metadatos** para convertir la columna de marca de tiempo "First Transaction Time" en un formato de cadena. Esto es necesario para generar la división entre entrenamiento y prueba. A continuación, mediante **Columnas de proyecto**, quitamos varias columnas que no se utilizarán en el experimento. Utilizamos la limpieza de valores ausentes para sustituir todos los valores ausentes por 0. En el paso siguiente dividimos la columna "Unit Name, Section Name" en dos columnas: "Unit Name" y "Section Name". Para ello, utilizamos el siguiente código R en el módulo **Ejecutar R**:

    dataset <- maml.mapInputPort(1)
    b <- data.frame(do.call(rbind,strsplit(as.vector(dataset[,3]),",")))
    names(b) <- c("Unit Name","Section Name")
    data.set <- cbind(dataset[,1:2],b,dataset[,4:15])
    maml.mapOutputPort("data.set")  

Los primeros pasos del experimento que incluyen la carga de datos y las transformaciones iniciales se muestran arriba. Después de manipular los datos, dividimos el experimento en 4 ramas. En cada una de ellas probamos un conjunto diferente de características. Algunos de los conjuntos de características se utilizaron previamente en [1][1]. En la primera rama, que se describe a continuación, nuestro conjunto de características es StudentID, Unit.

![][3]

![][4]

Características relacionadas con nombre, nombre de sección, nombre de problema, vista de problema y oportunidad, así como la descripción textual del problema. Las características con valores no enteros se representan como características categóricas. En esta rama, empezamos quitando la columna Hints mediante **Columnas de proyecto**, porque la columna Hints no está disponible al pronosticar el éxito de un nuevo estudiante. A continuación, utilizamos el módulo **Dividir** para dividir el conjunto de datos en un conjunto de entrenamiento y otro de prueba. Dado que todos los ejemplos tienen marcas de tiempo, nuestra división se basará en el factor temporal. Todas las filas con el valor de tiempo de primera transacción (First Transaction Time) en 2008 se encuentran en el conjunto de entrenamiento, los de 2009 en el conjunto de prueba. Los parámetros del módulo **Dividir** se muestran a la derecha. Tras la división, generamos un modelo de clasificación binaria utilizando el árbol de decisión ampliado y puntuamos los datos de prueba. La columna de etiqueta en el módulo **Entrenar modelo** es Correct First Attempt.

![][5]

En la siguiente secuencia de **Aplicar operación matemática** y **Estadísticas elementales**, que se muestran arriba, calculamos la RMSE del modelo utilizando puntuaciones sin formato y etiquetas verdaderas. Tenga en cuenta que para los modelos de regresión esta métrica se calcula mediante el módulo **Evaluador**. Sin embargo, para otros modelos es necesario calcular esta métrica de manera manual. En el primer módulo **Aplicar operación matemática**, calculamos al diferencia entre la columna de etiqueta y la columna de puntuación que se generó por parte del módulo **Puntuar modelo**. En el segundo módulo **Aplicar operación matemática**, calculamos el cuadrado de esta diferencia. Entonces, el módulo **Estadísticas elementales** calculó la media de las diferencias elevadas al cuadrado. Finalmente, el último módulo **Aplicar operación matemática** se utiliza para calcular la raíz cuadrada. Los parámetros de estos cuatro módulos se muestran a continuación.

![][6]

![][7]

![][8]

![][9]

En la segunda rama, que se muestra arriba, aprovechamos también la dimensión temporal. Además de las características de la primera rama, utilizamos nombres de los dos últimos pasos del problema que soluciona actualmente el usuario junto con su descripción. En el conjunto de datos suministrado, todas las actividades del usuario se almacenan en orden ascendente según la marca de tiempo. Además, en el conjunto de datos suministrado las actividades de los usuarios no están intercaladas; es decir, al principio están todas las filas del primer usuario, luego todas las filas del segundo usuario, y así sucesivamente. Por ello, para encontrar los últimos pasos del usuario hacemos uso de la columna RowID. Para un usuario fijo esta columna sirve como eje temporal. Al agregar 1 a esta columna y utilizar Aplicar operación matemática\*\*\*\*, cambiamos cada unidad de la fila en un valor hacia adelante. A continuación, utilizamos el módulo **Unir** para unir el conjunto de datos original con el que hemos cambiado utilizando RowID, StudentID y ProblemName como claves. Como resultado, obtenemos un conjunto de datos ampliado donde cada fila tiene un registro desde las veces t y t-1 para los mismos valores de StudentID y ProblemName. Utilizamos *combinación externa izquierda* para mantener las filas que no tienen pasos anteriores con los mismos valores de StudentID y ProblemID. Aplicamos la combinación de **Aplicar operación matemática** y **Unir** una vez más para obtener las características de hace dos pasos. Los parámetros exactos de **Aplicar operación matemática** y **Unir** se muestran a continuación.

![][10]

![][11]

![][12]

Después de realizar los dos obtenemos un número de columnas que son idénticas. Por ejemplo, como consecuencia del uso que hemos hecho del módulo Unir, la columna ProblemName se ha replicado 3 veces: para los pasos t, t-1 y t-2. Utilizamos el módulo **Columnas de proyecto** para quitar las columnas redundantes. Por último, como utilizamos *combinación externa izquierda*, es posible que falten valores en algunas de las filas generadas por el operador Unir. Utilizamos la **Limpieza de valores que faltan** para reemplazar todos los valores ausentes por la cadena "0". Los parámetros de **Limpieza de valores que faltan** se muestran arriba.

Después de calcular el nuevo conjunto de características, el flujo de trabajo de la segunda rama es idéntico al de la primera rama.

![][13]

En la tercera rama, además de las características utilizadas en la segunda, empleamos características cuadráticas y cúbicas que son concatenaciones de las características originales de la primera rama. Las características cuadráticas y cúbicas se calculan mediante el módulo **Ejecutar operación R** con el código R que se muestra anteriormente. Tras calcular el nuevo conjunto de características, continuamos con el entrenamiento, la puntuación y la evaluación del mismo modo que en las dos primeras ramas.

En la cuarta rama utilizamos características que son completamente diferentes de aquellas de las primeras tres ramas. Para cada StudentID calculamos el valor promedio de primer intento correcto hasta el momento t (pero sin incluirlo). Denominamos a este valor CFA(StudentID,t). Del mismo modo, llamamos Hints(StudentID,t) al valor promedio de indicios para un StudentID fijo hasta el momento t (pero sin incluirlo). Para acelerar el cálculo de estos promedios, en lugar de considerar todo el conjunto de datos, nos fijamos solo en los 10 registros más recientes antes del momento t. Las definiciones de CFA(Problem Name,t), CFA(Step Name,t), CFA(Description,t), Hints(Problem Name,t), Hints(Step Name,t) y Hints(Description,t) son similares. Dado un ejemplo x con First Transaction Time=t(x) y los valores de columna StudentID(x), Problem Name(x), Step Name(x) y Description(x), generamos las anteriores 8 características de indicios y primer intento correcto.

    CFA(StudentID(x),t(x)), CFA(Problem Name(x),t(x)), CFA(Step Name(x),t(x)), CFA(Description(x),t(x)), 
    Hints(StudentID(x),t(x)), Hints(Problem Name(x),t(x)), Hints(Step Name(x),t(x)), Hints(Description(x),t(x))  

![][14]

Del mismo modo, calculamos 8 características de indicios y primer intento correcto adicionales utilizando concatenaciones de StudentID y Problem Name, Problem Name y Step Name, StudentId y UnitName, StudentID y Problem Description. En total, obtenemos 16 características que se utilizan para predecir el valor de la columna Correct First Attempt. El cálculo de estas 16 características se realiza utilizando código R dentro del módulo **Ejecutar operación R**. Este código es largo y tedioso, pero está muy optimizado. Después de calcular estas características, quitamos algunas de las columnas auxiliares agregadas por el código R. Para ello, utilizamos el módulo **Columnas de proyecto**. El flujo de trabajo completo para calcular las características de la cuarta rama se muestra más arriba. Tras calcular el nuevo conjunto de características, continuamos con el entrenamiento, la puntuación y la evaluación del mismo modo que en las tres primeras ramas.

Tras calcular los valores de la RMSE de las cuatro ramas, recopilamos los resultados utilizando el módulo **Agregar fila**. Además, generamos anotaciones mediante el módulo **Ejecutar R**. El flujo de trabajo de esta parte final del experimento se describe a continuación.

![][15]

El resultado final del experimento es la tabla siguiente; la primera columna es el nombre del conjunto de características y la segunda fila es la RSME medida sobre los ejemplos de muestra:

![][16]

Llegamos a la conclusión de que el cuarto conjunto de características proporciona el valor más bajo de RMSE.

## Referencias

H.-F. Yu et al. Feature Engineering and Classifier Ensemble for KDD Cup 2010. KDD Cup 2010 Workshop, 2010.

  [0]: https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp
  [raíz cuadrada del error cuadrático medio]: http://en.wikipedia.org/wiki/Root-mean-square_deviation
  [0]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-1.jpg
  [1]: https://manage.windowsazure.com
  [2]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-2.jpg
  [3]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-3.jpg
  [4]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-4.jpg
  [5]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-5.jpg
  [6]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-6.jpg
  [7]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-7.jpg
  [8]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-8.jpg
  [9]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-9.jpg
  [10]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-10.jpg
  [11]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-11.jpg
  [12]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-12.jpg
  [13]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-13.jpg
  [14]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-14.jpg
  [15]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-15.jpg
  [16]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-16.jpg
