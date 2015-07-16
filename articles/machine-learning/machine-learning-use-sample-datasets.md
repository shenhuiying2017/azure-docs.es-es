<properties 
	pageTitle="Uso de los conjuntos de datos de muestra en Estudio de aprendizaje automático | Microsoft Azure" 
	description="Descripciones de los conjuntos de datos que se usan en los modelos de muestras incluidos en Estudio de aprendizaje automático. Puede usar estos conjuntos de datos de ejemplo para los experimentos." 
	keywords="data sets,datasets,ml studio,sample data sets"
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


# Uso de los conjuntos de datos de muestra en Estudio de aprendizaje automático de Azure

[top]: #machine-learning-sample-datasets

Cuando se crea un área de trabajo nueva en Aprendizaje automático de Azure, de manera predeterminada se incluyen diversos conjuntos de datos y experimentos de muestra. Los modelos de muestra de la [Galería de modelos de Aprendizaje automático de Azure](http://azure.microsoft.com/documentation/services/machine-learning/models/) usan muchos de estos conjuntos de datos de muestra, mientras que otros se incluyen como ejemplos de los diversos tipos de datos usados en el aprendizaje automático.

Algunos de estos conjuntos de datos están disponibles en el almacenamiento de blobs de Azure. Para dichos conjuntos de datos, las tablas siguientes proporcionan un vínculo directo. Puede usar estos conjuntos de datos en los experimentos mediante el módulo [Lector][reader] (si desea ver un ejemplo de cómo tener acceso a un conjunto de datos, consulte las propiedades del módulo [Lector][reader] del experimento de muestra asociado con el conjunto de datos).

El resto de estos conjuntos de datos de muestra aparece en **Conjuntos de datos guardados** en la paleta de módulos a la izquierda del lienzo de experimentos al abrir o crear un experimento nuevo en Estudio de aprendizaje automático. Para usar cualquiera de estos conjuntos de datos en su propio experimento, arrástrelo a su lienzo de experimentos.


<!--
For a list of sample experiments available in ML Studio, see [Machine Learning Sample Experiments][sample-experiments].

[sample-experiments]: machine-learning-sample-experiments.md
-->

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Conjuntos de datos de ejemplo asociados con modelos de la Galería de aprendizaje automático

Los modelos de ejemplo de la [Galería de modelos de Aprendizaje automático de Azure](http://azure.microsoft.com/documentation/services/machine-learning/models/) usan los siguientes conjuntos de datos. Para obtener más información acerca del modelo y su experimento asociado, siga el vínculo que aparece en la tabla para ver los detalles del modelo.

<table>

<tr>
  <th align=left>Modelo de ejemplo</th>
  <th align=left>Experimento asociado</th>
  <th align=left>Nombre del conjunto de datos</th>
  <th align=left>Descripción del conjunto de datos</th>
</tr>

<tr ID=bill-gates-rgb-image>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-color-quantization-using-k-means-clustering/">Cuantificación del color mediante la agrupación en clústeres de K-Means</a></td>
  <td valign=top>Experimento de ejemplo - Compresión de imágenes basada en colores mediante agrupaciones en clústeres de K-Means - Desarrollo</td>
  <td valign=top>Imagen RGB de Bill Gates</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-bill-gates-rgb-image](../../includes/machine-learning-sample-dataset-bill-gates-rgb-image.md)]
  </td>
</tr>

<tr ID=german-credit-card-uci-dataset>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-credit-risk-prediction/">Predicción de riesgo de crédito</a></td>
  <td valign=top>Experimento de ejemplo - Crédito alemán - Desarrollo</td>
  <td valign=top>Conjunto de datos UCI de tarjeta de crédito alemana</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-german-credit-card-uci-dataset](../../includes/machine-learning-sample-dataset-german-credit-card-uci-dataset.md)]
  </td>
</tr>

<tr ID=crm-dataset-shared>
  <td rowspan=4 valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-crm-task/">Tarea de CRM</a></td>
  <td rowspan=4 valign=top>Experimentos de ejemplo - CRM - Desarrollo</td>
  <td valign=top>Conjunto de datos CRM compartido</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-dataset-shared](../../includes/machine-learning-sample-dataset-crm-dataset-shared.md)]
  </td>
</tr>
<tr ID=crm-appetency-labels-shared>
  <td valign=top>Etiquetas de apetencia CRM compartidas</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-appetency-labels-shared](../../includes/machine-learning-sample-dataset-crm-appetency-labels-shared.md)]
  </td>
</tr>
<tr ID=crm-churn-labels-shared>
  <td valign=top>Etiquetas de rotación de clientes de CRM compartidas</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-churn-labels-shared](../../includes/machine-learning-sample-dataset-crm-churn-labels-shared.md)]
  </td>
</tr>
<tr ID=crm-upselling-labels-shared>
  <td valign=top>Etiquetas de mejora de ventas de CRM compartidas</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-upselling-labels-shared](../../includes/machine-learning-sample-dataset-crm-upselling-labels-shared.md)]
  </td>
</tr>

<tr ID=wikipedia-sp-500-dataset>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-finding-similar-companies/">Búsqueda de empresas similares</a></td>
  <td valign=top>Experimento de ejemplo - Agrupación de empresas del índice S &amp; P 500 - Desarrollo</td>
  <td valign=top>Conjunto de datos de SP 500 de Wikipedia</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-wikipedia-sp-500](../../includes/machine-learning-sample-dataset-wikipedia-sp-500.md)]
  </td>
</tr>

<tr ID=airport-codes-dataset>
  <td rowspan=3 valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-flight-delay-prediction/">Predicción del retraso de los vuelos</a></td>
  <td rowspan=3 valign=top>Experimento de ejemplo - Predicción del retraso de los vuelos - Desarrollo</td>
  <td valign=top>Conjunto de datos de códigos de aeropuerto</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-airport-codes](../../includes/machine-learning-sample-dataset-airport-codes.md)]
  </td>
</tr>

<tr ID=flight-delays-data>
  <td valign=top>Datos de retrasos de vuelos</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-flight-delays-data](../../includes/machine-learning-sample-dataset-flight-delays-data.md)]
</td>
</tr>

<tr ID=weather-dataset>
  <td valign=top>Conjunto de datos del tiempo</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-weather-dataset](../../includes/machine-learning-sample-dataset-weather-dataset.md)]
  </td>
</tr>

<tr ID=intrusion-detection>
  <td valign=top> <a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-network-intrusion-detection/">Detección de intrusiones de red</a></td>
  <td valign=top>Experimentos de ejemplo - Detección de intrusiones de red - Desarrollo</td>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-network-intrusion](../../includes/machine-learning-sample-dataset-network-intrusion.md)]
  </td>
</tr>

<tr ID=bike-rental-uci-dataset>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-prediction-of-number-of-bike-rentals/">Previsión de la demanda de bicicletas</a></td>
  <td valign=top>Experimento de ejemplo - Previsión de la demanda de bicicletas</td>
  <td valign=top>Conjunto de datos UCI de alquiler de bicicletas</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-bike-rental-uci-dataset](../../includes/machine-learning-sample-dataset-bike-rental-uci-dataset.md)]
  </td>
</tr>

<tr ID=student-performance>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-prediction-of-student-performance/">Predicción del rendimiento estudiantil</a></td>
  <td valign=top>Experimento de ejemplo - Rendimiento estudiantil - Desarrollo</td>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-student-performance](../../includes/machine-learning-sample-dataset-student-performance.md)]
  </td>
</tr>

<tr ID=book-reviews-from-amazon>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-sentiment-analysis/">Análisis de opiniones</a></td>
  <td valign=top>Experimento de ejemplo - Clasificación de opiniones - Desarrollo</td>
  <td valign=top>Reseñas de libros de Amazon</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-book-reviews-from-amazon](../../includes/machine-learning-sample-dataset-book-reviews-from-amazon.md)]
  </td>
</tr>

</table>



## Conjuntos de datos utilizados en experimentos de ejemplo de Estudio de aprendizaje automático

Los experimentos de muestra incluidos en [Estudio de aprendizaje automático](https://studio.azureml.net/Home) Los usan los siguientes conjuntos de datos.

Estos conjuntos de datos se puede encontrar en **Conjuntos de datos guardados** en la paleta de módulos a la izquierda del lienzo de experimentos al abrir o crear un experimento nuevo en Estudio de aprendizaje automático. Para usar cualquiera de estos conjuntos de datos en su propio experimento, arrástrelo a su lienzo de experimentos.

Puede encontrar todos los experimentos de ejemplo mencionados aquí en la pestaña **Ejemplos** de la página **Experimentos** en Estudio de aprendizaje automático. Para crear una copia de un experimento de ejemplo que puede editar, abra el experimento en Estudio de aprendizaje automático y, a continuación, haga clic en **Guardar como**.


<table>

<tr>
  <th align=left>Nombre del conjunto de datos</th>
  <th align=left>Experimento de ejemplo</th>
  <th align=left>Descripción del conjunto de datos</th>
</tr>

<tr ID=breast-cancer-features>
  <td valign=top>Características de cáncer de mama
  <td valign=top>Experimento de ejemplo - Cáncer de mama - Desarrollo</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-breast-cancer-features](../../includes/machine-learning-sample-dataset-breast-cancer-features.md)]
  </td>
</tr>

<tr ID=breast-cancer-info>
  <td valign=top>Información sobre cáncer de mama</td>
  <td valign=top><i>same</i></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-breast-cancer-info](../../includes/machine-learning-sample-dataset-breast-cancer-info.md)]
  </td>
</tr>

<tr ID=direct-marketing>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td valign=top>Experimento de ejemplo - Marketing directo - Desarrollo - Entrenamiento</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-direct-marketing](../../includes/machine-learning-sample-dataset-direct-marketing.md)]
  </td>
</tr>

<tr ID=imdb-movie-titles>
  <td valign=top>Títulos de películas en IMDB</td>
  <td valign=top>Experimento de ejemplo - Recomendador de películas - Desarrollo</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-imdb-movie-titles](../../includes/machine-learning-sample-dataset-imdb-movie-titles.md)]
  </td>
</tr>

<tr ID=movie-tweets>
  <td valign=top>Tweets sobre películas</td>
  <td valign=top>Experimento de ejemplo - Clasificación de tweets sobre películas según su popularidad a futuro - Desarrollo</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-movie-tweets](../../includes/machine-learning-sample-dataset-movie-tweets.md)]
  </td>
</tr>

<tr ID=lyrl2004-tokens-test>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td valign=top>Experimentación de ejemplo - Categorización de noticias - Desarrollo</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-lyrl2004-tokens-test](../../includes/machine-learning-sample-dataset-lyrl2004-tokens-test.md)]
  </td>
</tr>

<tr ID=lyrl2004-tokens-train>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td valign=top><i>same</i></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-lyrl2004-tokens-train](../../includes/machine-learning-sample-dataset-lyrl2004-tokens-train.md)]
  </td>
</tr>

<tr ID=rcv1-v2-topics-qrels>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1 v2.topics.qrels.csv</a></td>
  <td valign=top><i>same</i></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-rcv1-v2-topics](../../includes/machine-learning-sample-dataset-rcv1-v2-topics.md)]
  </td>
</tr>

</table>



## Conjuntos de datos de muestra varios de Estudio de aprendizaje automático

Los siguientes conjuntos de datos se incluyen en [Estudio de aprendizaje automático](https://studio.azureml.net/Home) para proporcionar ejemplos de los distintos tipos de datos que se usan en el aprendizaje automático. Puede encontrarlos en **Conjuntos de datos guardados** en la paleta de módulos a la izquierda del lienzo del experimento cuando abre o crea un experimento nuevo en Estudio de aprendizaje automático. Para usar cualquiera de estos conjuntos de datos en su propio experimento, arrástrelo a su lienzo de experimentos.

<table>
<tr>
  <th align=left>Nombre del conjunto de datos</th>
  <th align=left>Descripción</th>
</tr>

<tr>
  <td valign=top>Conjunto de datos de clasificación binaria de ingresos en el censo de adultos</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-adult-census-income](../../includes/machine-learning-sample-dataset-adult-census-income.md)]
  </td>
</tr>

<tr>
  <td valign=top>Información sobre los precios de los automóviles (datos sin procesar)</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-automobile-price](../../includes/machine-learning-sample-dataset-automobile-price.md)]
  </td>
</tr>

<tr>
  <td valign=top>Datos sobre donación de sangre</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-blood-donation](../../includes/machine-learning-sample-dataset-blood-donation.md)]
  </td>
</tr>

<tr>
  <td valign=top>Datos sobre cáncer de mama</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-breast-cancer-data](../../includes/machine-learning-sample-dataset-breast-cancer-data.md)]
  </td>
</tr>

<tr>
  <td valign=top>Datos de regresión de eficiencia energética</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-energy-efficiency-regression](../../includes/machine-learning-sample-dataset-energy-efficiency-regression.md)]
  </td>
</tr>

<tr>
  <td valign=top>Información sobre puntualidad de vuelos (datos sin procesar)</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-flight-on-time-performance](../../includes/machine-learning-sample-dataset-flight-on-time-performance.md)]
  </td>
</tr>

<tr>
  <td valign=top>Datos de incendios forestales</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-forest-fires](../../includes/machine-learning-sample-dataset-forest-fires.md)]
  </td>
</tr>

<tr>
  <td valign=top>Datos sobre iris de dos clases</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-iris-two-class](../../includes/machine-learning-sample-dataset-iris-two-class.md)]
  </td>
</tr>

<tr>
  <td valign=top>Datos sobre consumo de combustible por distancia recorrida para varios automóviles</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-automobile-mpg](../../includes/machine-learning-sample-dataset-automobile-mpg.md)]
  </td>
</tr>

<tr>
  <td valign=top>Conjunto de datos de clasificación binaria sobre diabetes en indios pima</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-pima-indians-diabetes](../../includes/machine-learning-sample-dataset-pima-indians-diabetes.md)]
  </td>
</tr>

<tr>
  <td valign=top>Datos de clientes de restaurantes</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-restaurant-customer-data](../../includes/machine-learning-sample-dataset-restaurant-customer-data.md)]
  </td>
</tr>

<tr>
  <td valign=top>Datos de características de restaurantes</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-restaurant-feature-data](../../includes/machine-learning-sample-dataset-restaurant-feature-data.md)]
  </td>
</tr>

<tr>
  <td valign=top>Valoraciones de restaurantes</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-restaurant-ratings](../../includes/machine-learning-sample-dataset-restaurant-ratings.md)]
  </td>
</tr>

<tr>
  <td valign=top>Conjunto de datos de varias clases de recocido de acero</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-steel-annealing](../../includes/machine-learning-sample-dataset-steel-annealing.md)]
  </td>
</tr>

<tr>
  <td valign=top>Datos de telescopio</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-telescope-data](../../includes/machine-learning-sample-dataset-telescope-data.md)]
  </td>
</tr>


</table>


<!-- Module References -->
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 

<!---HONumber=July15_HO1-->