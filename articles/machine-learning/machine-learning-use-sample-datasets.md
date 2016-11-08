---
title: Uso de los conjuntos de datos de muestra en Estudio de aprendizaje automático | Microsoft Docs
description: Descripciones de los conjuntos de datos que se usan en los modelos de muestras incluidos en Estudio de aprendizaje automático. Puede usar estos conjuntos de datos de ejemplo para los experimentos.
services: machine-learning
documentationcenter: ''
author: garyericson
manager: jhubbard
editor: cgronlun

ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2016
ms.author: garye

---
# Uso de los conjuntos de datos de muestra en Estudio de aprendizaje automático de Azure
[top]: #machine-learning-sample-datasets

Cuando se crea un área de trabajo nueva en Aprendizaje automático de Azure, de manera predeterminada se incluyen diversos conjuntos de datos y experimentos de muestra. Muchos de estos conjuntos de datos de muestra se usan en los modelos de ejemplo de la [Galería de Cortana Intelligence](http://gallery.cortanaintelligence.com/), y otros se incluyen como ejemplos de diversos tipos de datos que se emplean normalmente en el aprendizaje automático.

Algunos de estos conjuntos de datos están disponibles en el almacenamiento de blobs de Azure. Para dichos conjuntos de datos, la tabla siguiente proporciona un vínculo directo. Puede usar estos conjuntos de datos en sus experimentos mediante el módulo [Importar datos][import-data].

El resto de estos conjuntos de datos de muestra aparece en **Conjuntos de datos guardados** en la paleta de módulos a la izquierda del lienzo de experimentos al abrir o crear un experimento nuevo en Estudio de aprendizaje automático. Para usar cualquiera de estos conjuntos de datos en su propio experimento, arrástrelo a su lienzo de experimentos.

<!--
For a list of sample experiments available in ML Studio, see [Machine Learning Sample Experiments][sample-experiments].

[sample-experiments]: machine-learning-sample-experiments.md
-->

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

<table>

<tr>
  <th align=left>Nombre del conjunto de datos</th>
  <th align=left>Descripción del conjunto de datos</th>
</tr>

<tr>
  <td valign=top>Conjunto de datos de clasificación binaria de ingresos en el censo de adultos</td>
  <td valign=top>
Subconjunto de la base de datos del censo de 1994 en el que se utilizan adultos trabajadores a partir de 16 años con un índice de ingresos ajustado > 100.<p> </p><b>Uso:</b> clasificar personas utilizando valores demográficos para predecir si una persona gana más de 50 000 al año.<p> </p><b>Investigación relacionada:</b> Kohavi, R., Becker, B., (1996). Repositorio de Machine Learning de UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: Universidad de California, Facultad de Ciencias de la Computación y de la Información
  </td>
</tr>

<tr ID=airport-codes-dataset>
  <td valign=top>Conjunto de datos de códigos de aeropuerto</td>
  <td valign=top>
Códigos para aeropuertos de EE.&#160;UU.<p> </p>Este conjunto de datos contiene una fila para cada aeropuerto de EE.&#160;UU., donde se indica el identificador y el nombre del aeropuerto, además de la ciudad y el estado en que se encuentra.
  </td>
</tr>

<tr>
  <td valign=top>Información sobre los precios de los automóviles (datos sin procesar)</td>
  <td valign=top>
Información acerca de automóviles por marca y modelo, incluido el precio, características como el número de cilindros y el consumo en relación a la distancia recorrida, así como una puntuación de riesgo para aseguradoras.<p> </p>La puntuación de riesgo se asocia inicialmente con el precio del automóvil y, a continuación, se ajusta para el riesgo real en un proceso que los actuarios conocen como simbología. Un valor de +3 indica que es arriesgado, y un valor de -3 indica que probablemente es bastante seguro.<p> </p><b>Uso:</b> predecir la puntuación de riesgo por características, utilizando una clasificación de regresión o de múltiples variantes. <p> </p><b>Investigación relacionada:</b> Schlimmer, J.C. (1987). Repositorio de Machine Learning de UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: Universidad de California, Facultad de Ciencias de la Computación y de la Información
  </td>
</tr>

<tr ID=bike-rental-uci-dataset>
  <td valign=top>Conjunto de datos UCI de alquiler de bicicletas</td>
  <td valign=top>
Conjunto de datos UCI Bike Rental basados en datos reales tomados de la empresa Capital Bikeshare, que mantiene una red de alquiler de bicicletas en Washington DC.<p> </p>El conjunto de datos tiene una fila para cada hora de cada día en 2011 y 2012, totalizando 17&#160;379 filas. El rango de alquileres de bicicletas por horas va de 1 a 977.

  </td>
</tr>

<tr ID=bill-gates-rgb-image>
  <td valign=top>Imagen RGB de Bill Gates</td>
  <td valign=top>
Archivo de imagen público convertido en datos CSV.<p> </p>El código para convertir la imagen se proporciona en la página de detalles de modelos sobre la <strong>cuantificación de color mediante la agrupación en clústeres K-Means</strong>.
  </td>
</tr>

<tr>
  <td valign=top>Datos sobre donación de sangre</td>
  <td valign=top>
Subconjunto de datos de la base de datos de donantes de sangre del Centro de servicios de transfusiones de sangre de Hsin-Chu City (Taiwán).<p> </p>Los datos de los donantes incluyen los meses transcurridos desde la última donación y la frecuencia, o el número total de donaciones, el tiempo transcurrido desde la última donación y la cantidad de sangre donada.<p> </p><b>Uso:</b> el objetivo es predecir mediante la clasificación si el donante donó sangre en marzo de 2007, donde 1 hace referencia a un donante durante el período objetivo y 0 a un no donante. <p> </p><b>Investigación relacionada:</b> Yeh, I.C., (2008). Repositorio de Machine Learning de UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: Universidad de California, Facultad de Ciencias de la Computación y de la Información <p> </p>Yeh, I-Cheng, Yang, King-Jang, and Ting, Tao-Ming, "Knowledge discovery on RFM model using Bernoulli sequence", Expert Systems with Applications, 2008, <a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr ID=book-reviews-from-amazon>
  <td valign=top>Reseñas de libros de Amazon</td>
  <td valign=top>
Comentarios de libros de Amazon, extraídos del sitio web amazon.com por investigadores de la Universidad de Pensilvania (<a href="http://www.cs.jhu.edu/~mdredze/datasets/sentiment/">opinión</a>). Consulte el trabajo de investigación, “Biographies, Bollywood, Boom-boxes and Blenders: Domain Adaptation for Sentiment Classification” ("Biografías, Bollywoood, equipos de sonido y licuadoras") de John Blitzer, Mark Dredze y Fernando Pereira; Asociación de Lingüística Informática (ACL), 2007.<p> </p>El conjunto de datos original tiene 975.000 comentarios con una puntuación de 1, 2, 3, 4 o 5. Los comentarios están redactados en inglés y se corresponden con el período 1997-2007. Este conjunto de datos se ha muestreado para 10.000 comentarios.
  </td>
</tr>

<tr>
  <td valign=top>Datos sobre cáncer de mama</td>
  <td valign=top>
Uno de los tres conjuntos de datos relacionados con el cáncer facilitados por el Instituto de oncología que aparece frecuentemente en la literatura sobre aprendizaje automático. Combina información de diagnóstico con características de análisis de laboratorio de unas 300 muestras de tejido.<p> </p><b>Uso:</b> clasificar el tipo de cáncer basándose en nueve atributos, algunos de los cuales son lineales y otros categóricos. <p> </p><b>Investigación relacionada:</b> Wohlberg, W.H., Street, W.N., &amp; Mangasarian, O.L. (1995). Repositorio de Machine Learning de UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: Universidad de California, Facultad de Ciencias de la Computación y de la Información
  </td>
</tr>

<tr ID=breast-cancer-features>
  <td valign=top>Características de cáncer de mama
  <td valign=top>
El conjunto de datos contiene información de 102.000 regiones sospechosas (candidatas) de imágenes de rayos X, cada una de las cuales está descrita por 117 características. Las características son privadas y los creadores del conjunto de datos no revelan su significado (Siemens Healthcare). 
  </td>
</tr>

<tr ID=breast-cancer-info>
  <td valign=top>Información sobre cáncer de mama</td>
  <td valign=top>
El conjunto de datos contiene información adicional para cada región sospechosa de imagen de rayos X. Cada ejemplo proporciona información (por ejemplo, etiqueta, identificador de paciente,coordenadas de la zona a tratar en relación con toda la imagen) sobre el número de fila correspondiente en el conjunto de datos Características de cáncer de mama. Cada paciente tiene una serie de ejemplos. Para los pacientes con cáncer, algunos ejemplos son positivos y otros son negativos. Para los pacientes que no tienen cáncer, todos los ejemplos son negativos. El conjunto de datos tiene 102&#160;000 ejemplos. El conjunto de datos está sesgado, el 0,6% de los puntos es positivo y el resto es negativo. Siemens Healthcare proporcionó el conjunto de datos.
  </td>
</tr>

<tr ID=crm-appetency-labels-shared>
  <td valign=top>Etiquetas de apetencia CRM compartidas</td>
  <td valign=top>
Etiquetas del concurso sobre la previsión de relaciones con los clientes de KDD Cup 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr ID=crm-churn-labels-shared>
  <td valign=top>Etiquetas de rotación de clientes de CRM compartidas</td>
  <td valign=top>
Etiquetas del concurso sobre la previsión de relaciones con los clientes de KDD Cup 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr ID=crm-dataset-shared>
  <td valign=top>Conjunto de datos CRM compartido</td>
  <td valign=top>
Estos datos están relacionados con el concurso sobre la previsión de relaciones con los clientes de KDD Cup 2009 (<a href="http://www.sigkdd.org/kdd-cup-2009-customer-relationship-prediction - orange_small_train.data.zip">orange_small_train.data.zip</a>).<p> </p>El conjunto de datos contiene 50.000 clientes de la empresa de telecomunicaciones francesa Orange. Cada cliente tiene 230 características anónimas, 190 de las cuales son numéricas y 40, categóricas. Las características están muy dispersas.
  </td>
</tr>

<tr ID=crm-upselling-labels-shared>
  <td valign=top>Etiquetas de mejora de ventas de CRM compartidas</td>
  <td valign=top>
Etiquetas del concurso sobre la previsión de relaciones con los clientes de KDD Cup 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td valign=top>Datos de regresión de eficiencia energética</td>
  <td valign=top>
Colección de perfiles energéticos simulados, con base en 12 formas de edificios diferentes. Los edificios se diferencian tomando como base 8 características, como la zona acristalada, la distribución de la zona acristalada y la orientación.<p> </p><b>Uso:</b> usar la regresión o la clasificación para predecir la clasificación de eficiencia energética en función de una de las dos respuestas con valores reales. En el caso de una clasificación de varias clases, la variable de respuesta se redondea al entero más cercano. <p> </p><b>Investigación relacionada:</b> Xifara, A. &amp; Tsanas, A. (2012). Repositorio de Machine Learning de UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: Universidad de California, Facultad de Ciencias de la Computación y de la Información
  </td>
</tr>

<tr ID=flight-delays-data>
  <td valign=top>Datos de retrasos de vuelos</td>
  <td valign=top>
Datos de rendimiento sobre puntualidad en vuelos de pasajeros recopilados en TranStats por el Departamento  de Transporte de EE.&#160;UU. (<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">On-Time</a>).<p> </p>El conjunto de datos abarca el periodo comprendido entre abril y octubre de 2013. Antes de cargarlo a Estudio de aprendizaje automático de Microsoft Azure, el conjunto de datos se ha procesado como sigue:<ul><li>Se filtró el conjunto de datos para centrarse en los 70 aeropuertos con más tráfico del territorio continental de EE.&#160;UU.</li><li>Los vuelos cancelados se etiquetaron como retrasados más de 15 minutos.</li><li>Los vuelos desviados se quitaron de la muestra.</li><li>Se seleccionaron las siguientes columnas: Year, Month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Cancelled</li></ul>
</td>
</tr>

<tr>
  <td valign=top>Información sobre puntualidad de vuelos (datos sin procesar)</td>
  <td valign=top>
Registros de llegadas y salidas de aviones dentro de Estados Unidos desde octubre de 2011.<p> </p><b>Uso:</b> predecir retrasos en los vuelos. <p> </p><b>Investigación relacionada:</b> datos tomados del Departamento de Transporte de EE.&#160;UU. <a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&amp;DB_Short_Name=On-Time</a>.
  </td>
</tr>

<tr>
  <td valign=top>Datos de incendios forestales</td>
  <td valign=top>
Contiene información meteorológica (como índices de temperatura y humedad y velocidad del viento) de una zona del noreste de Portugal combinada con registros de incendios forestales.<p> </p><b>Uso:</b> se trata de una tarea de regresión difícil, cuyo objetivo es predecir el área quemada por incendios forestales. <p> </p><b>Investigación relacionada:</b> Cortez, P., &amp; Morais, A. (2008). Repositorio de Machine Learning de UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: Universidad de California, Facultad de Ciencias de la Computación y de la Información <p> </p>[Cortez y Morais, 2007] P. Cortez y A. Morais. A Data Mining Approach to Predict Forest Fires using Meteorological Data. En J. Neves, M. F. Santos and J. Machado Eds., Trends in Artificial Intelligence, Proceedings of the 13th EPIA 2007 - Portuguese Conference on Artificial Intelligence, December, Guimarães, Portugal, pp. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Disponible en: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>.
  </td>
</tr>

<tr ID=german-credit-card-uci-dataset>
  <td valign=top>Conjunto de datos UCI de tarjeta de crédito alemana</td>
  <td valign=top>
El conjunto de datos Statlog de UCI (tarjeta de crédito alemana) (<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog+German+Credit+Data</a>), con el archivo german.data.<p> </p>El conjunto de datos clasifica a las personas, descritas por un conjunto de atributos, según si su riesgo de crédito es bajo o alto. Cada ejemplo representa a una persona. Hay 20 características, tanto numéricas como categóricas, y una etiqueta binaria (el valor del riesgo de crédito). Las partidas de riesgo de crédito alto tienen la etiqueta = 2, mientras que las partidas con riesgo de crédito bajo tienen la etiqueta = 1. El coste derivado de clasificar incorrectamente como alto un ejemplo de riesgo bajo es 1, mientras que, en el caso de clasificar incorrectamente como bajo un ejemplo de riesgo alto, el coste es 5.
  </td>
</tr>

<tr ID=imdb-movie-titles>
  <td valign=top>Títulos de películas en IMDB</td>
  <td valign=top>
El conjunto de datos contiene información sobre películas que se han valorado en tweets de Twitter: Id. de película en IMDB, nombre, género y año de producción. Hay 17.000 películas en el conjunto de datos. El conjunto de datos se introdujo en el documento "S. Dooms, T. De Pessemier y L. Martens". MovieTweetings: un conjunto de datos sobre valoración de películas recopilado de Twitter. Taller de micromecenazgo y cálculo humano para sistemas de recomendación, CrowdRec en RecSys 2013."
  </td>
</tr>

<tr>
  <td valign=top>Datos sobre iris de dos clases</td>
  <td valign=top>
Esta es quizá la base de datos más conocida entre la literatura de reconocimiento de modelos. El conjunto de datos es relativamente pequeño, con 50 ejemplos de cada una de las mediciones de pétalos de tres variedades de iris.<p> </p><b>Uso:</b> predecir el tipo de iris a partir de las mediciones.  <p> </p><b>Investigación relacionada:</b> Fisher, R.A. (1988). Repositorio de Machine Learning de UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: Universidad de California, Facultad de Ciencias de la Computación y de la Información
  </td>
</tr>

<tr ID=movie-tweets>
  <td valign=top>Tweets sobre películas</td>
  <td valign=top>
El conjunto de datos es una versión extendida del conjunto de datos Tweets sobre películas. El conjunto de datos incluye 170&#160;000 valoraciones de películas extraídas de tweets bien estructurados en Twitter. Cada instancia representa un tweet y es una tupla: Id. de usuario, Id. de película IMDB, valoración, marca de tiempo, número de favoritos para ese tweet y número de retweets de ese tweet. A. Said, S. Dooms, B. Loni y D. Tikk proporcionaron el conjunto de datos para el Concurso de sistemas de recomendación 2014.
  </td>
</tr>

<tr>
  <td valign=top>Datos sobre consumo de combustible por distancia recorrida para varios automóviles</td>
  <td valign=top>
Este conjunto de datos es una versión ligeramente diferente del conjunto de datos ofrecido por la biblioteca StatLib de la Universidad Carnegie Mellon. El conjunto de datos se utilizó en la exposición de la asociación americana de estadística de 1983.<p> </p>Los datos enumeran el consumo de combustible de varios automóviles en millas por galón, junto con información como número de cilindros, cilindrada, caballos de potencia, peso total y aceleración.<p> </p><b>Uso:</b> predecir el ahorro de combustible en función de tres atributos discretos multivalor y cinco atributos continuos. <p> </p><b>Investigación relacionada:</b> StatLib, Carnegie Mellon University, (1993). Repositorio de Machine Learning de UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: Universidad de California, Facultad de Ciencias de la Computación y de la Información
  </td>
</tr>

<tr>
  <td valign=top>Conjunto de datos de clasificación binaria sobre diabetes en indios pima</td>
  <td valign=top>
Subconjunto de datos de la base de datos del Instituto Nacional de la Diabetes y las Enfermedades Digestivas y Renales. El conjunto de datos se filtró para centrarse en pacientes femeninos con herencia de indios pima. Los datos incluyen información médica como niveles de glucosa e insulina y factores de estilo de vida.<p> </p><b>Uso:</b> predecir si el sujeto tiene diabetes (clasificación binaria). <p> </p><b>Investigación relacionada:</b> Sigillito, V. (1990). Repositorio de Machine Learning de UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: Universidad de California, Facultad de Ciencias de la Computación y de la Información
  </td>
</tr>

<tr>
  <td valign=top>Datos de clientes de restaurantes</td>
  <td valign=top>
Conjunto de metadatos sobre clientes que incluye información demográfica y preferencias.<p> </p><b>Uso:</b> uso de este conjunto de datos, en combinación con los otros dos conjuntos de datos de restaurantes, para entrenar y probar un sistema de recomendaciones. <p> </p><b>Investigación relacionada:</b> Bache, K. y Lichman, M. (2013). Repositorio de Machine Learning de UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: Universidad de California, Facultad de Ciencias de la Computación y de la Información
  </td>
</tr>

<tr>
  <td valign=top>Datos de características de restaurantes</td>
  <td valign=top>
Un conjunto de metadatos acerca de restaurantes y sus características, como el tipo de comida, el estilo de comedor y la ubicación.<p> </p><b>Uso:</b> uso de este conjunto de datos, en combinación con los otros dos conjuntos de datos de restaurantes, para entrenar y probar un sistema de recomendaciones. <p> </p><b>Investigación relacionada:</b> Bache, K. y Lichman, M. (2013). Repositorio de Machine Learning de UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: Universidad de California, Facultad de Ciencias de la Computación y de la Información
  </td>
</tr>

<tr>
  <td valign=top>Valoraciones de restaurantes</td>
  <td valign=top>
Contiene valoraciones que los usuarios realizan sobre restaurantes en una escala de 0 a 2.<p> </p><b>Uso:</b> uso de este conjunto de datos, en combinación con los otros dos conjuntos de datos de restaurantes, para entrenar y probar un sistema de recomendaciones. <p> </p><b>Investigación relacionada:</b> Bache, K. y Lichman, M. (2013). Repositorio de Machine Learning de UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: Universidad de California, Facultad de Ciencias de la Computación y de la Información
  </td>
</tr>

<tr>
  <td valign=top>Conjunto de datos de varias clases de recocido de acero</td>
  <td valign=top>
Este conjunto de datos contiene una serie de registros de pruebas de recocido de acerco con los atributos físicos , como grosor o tipo (rollo, lámina, etc.) de los tipos de acero resultantes.<p> </p><b>Uso:</b> predecir cualquiera de los dos atributos de clase numéricos: rigidez o solidez. También puede analizar las correlaciones entre atributos.<p> </p>Los grados del acero siguen una norma establecida, definida por la SAE y otras organizaciones. Está buscando un "grado" determinado (la variable de clase) y desea conocer los valores requeridos. <p> </p><b>Investigación relacionada:</b> Sterling, D. &amp; Buntine, W., (NA). Repositorio de Machine Learning de UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: Universidad de California, Facultad de Ciencias de la Computación y de la Información <p> </p>Aquí se puede encontrar una guía útil para los grados del acero: <a href="http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf">http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf</a>.
  </td>
</tr>

<tr>
  <td valign=top>Datos de telescopio</td>
  <td valign=top>
Registro de explosiones de partículas gamma de alta energía junto con ruido de fondo, ambos simulados utilizando un proceso de Monte Carlo.<p> </p>El objetivo de la simulación era mejorar la precisión de los telescopios gamma Cherenkov atmosféricos terrestres, utilizando métodos estadísticos para diferenciar entre la señal deseada (duchas de radiación de Cherenkov) y el ruido de fondo (duchas hadrónicas iniciadas por los rayos cósmicos en la atmósfera superior).<p> </p>Los datos se sometieron a un procesado previo para crear un grupo alargado con el eje central orientado hacia el centro de la cámara. Las características de esta elipse (llamada en ocasiones parámetros de Hillas) se encuentran entre los parámetros de imagen que se pueden utilizar para la discriminación.<p> </p><b>Uso:</b> predecir si la imagen de una ducha representa señal o ruido de fondo.<p> </p><b>Notas:</b> la precisión de clasificación sencilla no es significativa para estos datos, ya que clasificar un evento de fondo como señal es peor que clasificar un evento de señal como fondo. Para comparar los diferentes clasificadores se debe utilizar el gráfico ROC. La probabilidad de aceptar un evento de fondo como señal debe estar por debajo de uno de los umbrales siguientes: 0.01, 0.02, 0.05, 0.1 o 0.2.<p> </p>Además, tenga en cuenta que el número de eventos de fondo (h, para las duchas hadrónicas) está subestimado, mientras que en las medidas reales, la clase h o ruido representa la mayoría de eventos. <p> </p><b>Investigación relacionada:</b> Bock, R.K. (1995). Repositorio de Machine Learning de UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: Universidad de California, Facultad de Ciencias de la Computación y de la Información
  </td>
</tr>

<tr ID=weather-dataset>
  <td valign=top>Conjunto de datos del tiempo</td>
  <td valign=top>
Observaciones meteorológicas en tierra por hora de la NOAA (<a href="http://cdo.ncdc.noaa.gov/qclcd_ascii/, merged data from 201304 to 201310">datos combinados del 201304 al 201310</a>).<p> </p>Los datos meteorológicos abarcan las observaciones de las estaciones meteorológicas de los aeropuertos, durante el período que comprende desde abril hasta octubre de 2013. Antes de cargarlo a Estudio de aprendizaje automático de Microsoft Azure, el conjunto de datos se ha procesado como sigue:<ul><li>Se asignaron los identificadores de las estaciones meteorológicas a los identificadores de aeropuerto correspondientes.</li><li>Se excluyeron las estaciones meteorológicas no asociadas a los 70 aeropuertos con mayor tráfico.</li><li>La columna Date se separó en columnas independientes con los valores Year, Month y Day.</li><li>Se seleccionaron las siguientes columnas: AirportID, Year, Month, Day, Time, TimeZone, SkyCondition, Visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter</li></ul>
  </td>
</tr>

<tr ID=wikipedia-sp-500-dataset>
  <td valign=top>Conjunto de datos de SP 500 de Wikipedia</td>
  <td valign=top>
Los datos se han extraído de Wikipedia (<a href="http://www.wikipedia.org/">http://www.wikipedia.org/</a>) y se basan en artículos de cada empresa del índice S&amp;P 500, almacenados como datos XML.<p> </p>Antes de cargarlo a Estudio de aprendizaje automático de Microsoft Azure, el conjunto de datos se ha procesado como sigue:<ul><li>Se extrajo el contenido de texto para cada empresa específica.</li><li>Se eliminó el formato wiki.</li><li>Se eliminaron los caracteres no alfanuméricos</li><li>Se convirtió todo el texto a minúscula.</li><li>Se agregaron las categorías de empresas conocidas.</li></ul><p> </p>Tenga en cuenta que no se ha encontrado ningún artículo para algunas empresas, por lo que el número de registros es inferior a 500.
  </td>
</tr>





<tr ID=direct-marketing>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td valign=top>
El conjunto de datos contiene datos de los clientes e indicaciones sobre sus respuestas a una campaña de correo directa. Cada fila representa a un cliente. El conjunto de datos contiene 9 características sobre datos demográficos del usuario y conducta en el pasado, y 3 columnas de etiqueta (visita, conversión y gasto). Visita es una columna binaria que indica que un cliente ha hecho una visita después de la campaña de marketing, conversión indica que un cliente adquirió algún artículo y gasto es el importe que se gastó. Kevin Hillstrom proporcionó el conjunto de datos para el Reto de minería de datos y análisis de correo electrónico de MineThatData.
  </td>
</tr>

<tr ID=lyrl2004-tokens-test>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td valign=top>
Características de los ejemplos de prueba del conjunto de datos de noticias de Reuters RCV1 V2. El conjunto de datos tiene 781&#160;000 artículos nuevos junto con sus identificadores (primera columna del conjunto de datos). Los artículos están acortados, excluyen palabras reservadas y su contenido se reduce a la raíz de cada palabra. David proporcionó el conjunto de datos. D. Lewis.
  </td>
</tr>

<tr ID=lyrl2004-tokens-train>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td valign=top>
Características de ejemplos de aprendizaje en el conjunto de datos de noticias de Reuters RCV1 V2. El conjunto de datos tiene 23&#160;000 artículos nuevos junto con sus identificadores (primera columna del conjunto de datos). Los artículos están acortados, excluyen palabras reservadas y su contenido se reduce a la raíz de cada palabra. David proporcionó el conjunto de datos. D. Lewis.
  </td>
</tr>

<tr ID=intrusion-detection>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td valign=top>
Conjunto de datos de la competición de minería de datos y detección de conocimientos KDD Cup 1999 (<a href="http://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>).<p> </p>El conjunto de datos se descargó y almacenó en Azure Blob Storage (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) y comprende conjuntos de datos de entrenamiento y pruebas. El conjunto de datos de formación tiene aproximadamente 126.000 filas y 43 columnas, incluidas las etiquetas; 3 columnas forman parte de la información de etiquetas, y 40 columnas, compuestas de características numéricas y de cadena/categóricas, se encuentran disponibles para el entrenamiento del modelo. Los datos de prueba tienen aproximadamente 22.500 ejemplos de prueba (con las mismas 43 columnas en datos de entrenamiento).

  </td>
</tr>

<tr ID=rcv1-v2-topics-qrels>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1 v2.topics.qrels.csv</a></td>
  <td valign=top>
Asignaciones de tema para artículos de prensa en el conjunto de datos de noticias de Reuters RCV1 V2. Un artículo de prensa se puede asignar a varios temas. El formato de cada fila es "<topic name> <document id> 1". El conjunto de datos contiene 2,6 millones de asignaciones de tema. David proporcionó el conjunto de datos. D. Lewis.
  </td>
</tr>

<tr ID=student-performance>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td valign=top>
Los datos se han extraído del reto de evaluación del rendimiento de los estudiantes de KDD Cup 2010 (<a href="http://www.kdd.org/kdd-cup-2010-student-performance-evaluation">evaluación del rendimiento de estudiantes</a>). Los datos utilizados se corresponden con el conjunto de formación de álgebra 2008 y 2009 (Stamper, J., Niculescu-Mizil, A., Ritter, S., Gordon, G.J., &amp; Koedinger, K.R. (2010). Álgebra I 2008-2009. Conjunto de datos obtenidos de la minería de datos educativos de KDD Cup 2010. Puede encontrarlos en <a href="http://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a> o <a href="http://www.kdd.org/sites/default/files/kddcup/site/2010/files/algebra_2008_2009.zip">algebra_2008_2009.zip</a>.<p> </p>El conjunto de datos se ha descargado y almacenado en Azure Blob Storage (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) y contiene archivos de registro de un sistema de tutoría de estudiantes. Las características suministradas incluyen el identificador del problema y una breve descripción, el identificador del alumno, la marca de tiempo y cuántos intentos realizó el estudiante antes de solucionar el problema de la manera correcta. El conjunto de datos original contiene 8,9 millones de registros; este conjunto de datos se ha muestreado para las primeras 100.000 filas. El conjunto de datos tiene 23 columnas de varios tipos separadas por tabulaciones: numéricas, categóricas y con marcas de tiempo.

  </td>
</tr>




</table>


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=AcomDC_0921_2016-->