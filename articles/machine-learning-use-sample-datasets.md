<properties title="Use the sample datasets in Azure Machine Learning Studio" pageTitle="Use the sample datasets in Machine Learning Studio | Azure" description="Use the sample datasets in Azure Machine Learning Studio" metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Uso de conjuntos de datos de ejemplo en Estudio de aprendizaje automático de Azure

Estudio de aprendizaje automático incluye algunos conjuntos de datos de ejemplo que puede utilizar. Se trata de conjuntos de datos de aprendizaje automático estándar de dominio público.

Puede utilizar esos conjuntos de datos en sus experimentos mientras explora diversos tipos de modelos analíticos de aprendizaje automático en Estudio de aprendizaje automático.

-   Para ver una lista de estos conjuntos de datos estándar en Estudio de aprendizaje automático, haga clic en la pestaña **CONJUNTOS DE DATOS**. En cada uno de los conjuntos de datos verá información como el nombre del conjunto de datos, quién lo envió y una breve descripción.

    Para ordenar los conjuntos de datos, haga clic en el encabezado de cualquier columna. Por ejemplo, haga clic en **ENVIADO POR** para agrupar todos los conjuntos de datos de ejemplo suministrados por Microsoft Corporation. Esto puede servir también para ver fácilmente los conjuntos de datos que usted y otros usuarios de su área de trabajo han importado.

-   Para utilizar un conjunto de datos en un experimento, expanda la sección **Conjuntos de datos guardados** de la paleta de módulos a la izquierda del lienzo del experimento, o busque un conjunto de datos determinado escribiendo su nombre en el cuadro de búsqueda que se encuentra encima de la paleta. Arrastre el conjunto de datos al lienzo para agregarlo al experimento.

## Conjuntos de datos de ejemplo

La tabla siguiente proporciona información adicional acerca de los conjuntos de datos que podrían resultarle de utilidad al explorar las características de aprendizaje automático de Estudio de aprendizaje automático. La tabla muestra los siguientes elementos para cada conjunto de datos:

-   El nombre y el origen de cada conjunto de datos.
-   Una descripción de los posibles usos del conjunto de datos y citas a la investigación de aprendizaje automático que utilizó dicho conjunto de datos.
-   Un resumen de las columnas importantes incluidas en el conjunto de datos y otros atributos útiles.

Algunas descripciones están adaptadas a partir de la documentación suministrada por la fuente (normalmente, el [Repositorio de aprendizaje automático de UC Irvine][]), mientras que otras descripciones se basan en investigaciones adicionales o reflejan los cambios realizados en estos ejemplos.

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Nombre del conjunto de datos</th>
<th align="left">Descripción del conjunto de datos</th>
<th align="left">Información relacionada</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Conjunto de datos de clasificación binaria de ingresos en el censo de adultos</td>
<td align="left"><p>Subconjunto de la base de datos del censo de 1994 en el que se utilizan adultos trabajadores a partir de 16 años con un índice de ingresos ajustado &gt; 100.</p>
<p><strong>Uso:</strong> clasificar personas utilizando valores demográficos para predecir si una persona gana más de 50.000 al año.</p></td>
<td align="left"><p>Kohavi, R., Becker, B., (1996). Repositorio de aprendizaje automático de UCI [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine (California): Universidad de California, Facultad de Ciencias de la Computación y de la Información</p></td>
</tr>
<tr class="even">
<td align="left"><p>Información sobre precios de automóviles (datos sin procesar)</p></td>
<td align="left"><p>Información acerca de automóviles por marca y modelo, incluido el precio, características como el número de cilindros y el consumo en relación a la distancia recorrida, así como una puntuación de riesgo para aseguradoras.</p>
<p>La puntuación de riesgo se asocia inicialmente con el precio del automóvil y, a continuación, se ajusta para el riesgo real en un proceso que los actuarios conocen como <legacyitalic>simbología</legacyitalic>. Un valor de +3 indica que es arriesgado, y un valor de -3 indica que probablemente es bastante seguro.</p>
<p><strong>Uso:</strong> predecir la puntuación de riesgo por características, utilizando una clasificación de regresión o de múltiples variantes.</p></td>
<td align="left"><p>Schlimmer, J.C. (1987). Repositorio de aprendizaje automático de UCI [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine (California): Universidad de California, Facultad de Ciencias de la Computación y de la Información</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Datos sobre donación de sangre</p></td>
<td align="left"><p>Subconjunto de datos de la base de datos de donantes de sangre del Centro de servicios de transfusiones de sangre de Hsin-Chu City (Taiwán).</p>
<p>Los datos de los donantes incluyen los meses transcurridos desde la última donación y la frecuencia, o el número total de donaciones, el tiempo transcurrido desde la última donación y la cantidad de sangre donada.</p>
<p><strong>Uso:</strong> el objetivo es predecir mediante la clasificación si el donante donó sangre en marzo de 2007, donde 1 hace referencia a un donante durante el período objetivo y 0 a un no donante.</p></td>
<td align="left"><list class="bullet"> <listitem>
<p>Yeh, I.C., (2008). Repositorio de aprendizaje automático de UCI [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine (California): Universidad de California, Facultad de Ciencias de la Computación y de la Información</p>
</listitem> <listitem>
<p>Yeh, I-Cheng, Yang, King-Jang, y Ting, Tao-Ming, &quot;Knowledge discovery on RFM model using Bernoulli sequence, &quot;Expert Systems with Applications, 2008, [<a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018" class="uri">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>]</p>
</listitem> </list></td>
</tr>
<tr class="even">
<td align="left"><p>Datos sobre cáncer de mama</p></td>
<td align="left"><p>Uno de los tres conjuntos de datos relacionados con el cáncer facilitados por el Instituto de oncología que aparece frecuentemente en la literatura sobre aprendizaje automático. Combina información de diagnóstico con características de análisis de laboratorio de unas 300 muestras de tejido.</p>
<p><strong>Uso:</strong> clasificar el tipo de cáncer con base en 9 atributos, algunos de los cuales son lineales y otros categóricos.</p></td>
<td align="left"><p>Wohlberg, W.H., Street, W.N., &amp; Mangasarian, O.L. (1995). Repositorio de aprendizaje automático de UCI [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine (California): Universidad de California, Facultad de Ciencias de la Computación y de la Información</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Datos de regresión de eficiencia energética</p></td>
<td align="left"><p>Colección de perfiles energéticos simulados, con base en 12 formas de edificios diferentes. Los edificios se diferencian tomando como base 8 características, como la zona acristalada, la distribución de la zona acristalada y la orientación.</p>
<p><strong>Uso:</strong> uso de la regresión o la clasificación para predecir la clasificación de eficiencia energética en función de una de las dos respuestas con valores reales. En el caso de una clasificación de varias clases, la variable de respuesta se redondea al entero más cercano.</p></td>
<td align="left"><p>Xifara, A. &amp; Tsanas, A. (2012). Repositorio de aprendizaje automático de UCI [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine (California): Universidad de California, Facultad de Ciencias de la Computación y de la Información</p></td>
</tr>
<tr class="even">
<td align="left"><p>Información sobre puntualidad de vuelos (datos sin procesar)</p></td>
<td align="left"><p>Registros de llegadas y salidas de aviones dentro de Estados Unidos desde octubre de 2011.</p>
<p><strong>Uso:</strong> predecir retrasos en los vuelos.</p></td>
<td align="left"><p>Datos tomados del Departamento de Transporte de EE. UU. [<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&amp;DB_Short_Name=On-Time" class="uri">http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&amp;DB_Short_Name=On-Time</a>]</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Datos de incendios forestales</p></td>
<td align="left"><p>Contiene información meteorológica (como índices de temperatura y humedad y velocidad del viento) de una zona del noreste de Portugal combinada con registros de incendios forestales.</p>
<p><strong>Uso:</strong> se trata de una tarea de regresión difícil, cuyo objetivo es predecir el área quemada por incendios forestales.</p></td>
<td align="left"><list class="bullet"> <listitem>
<p>Cortez, P., &amp; Morais, A. (2008). Repositorio de aprendizaje automático de UCI [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine (California): Universidad de California, Facultad de Ciencias de la Computación y de la Información</p>
</listitem> <listitem>
<p>[Cortez y Morais, 2007] P. Cortez y A. Morais. A Data Mining Approach to Predict Forest Fires using Meteorological Data. In J. Neves, M. F. Santos y J. Machado Eds., New Trends in Artificial Intelligence, Actas del 13.er encuentro EPIA 2007 - Conferencia portuguesa sobre inteligencia artificial Diciembre, Guimarães, Portugal, pp. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Disponible en: [<a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf" class="uri">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>]</p>
</listitem> </list></td>
</tr>
<tr class="even">
<td align="left"><p>Datos sobre iris de dos clases</p></td>
<td align="left"><p>Esta es quizá la base de datos más conocida entre la literatura de reconocimiento de modelos. El conjunto de datos es relativamente pequeño, con 50 ejemplos de cada una de las mediciones de pétalos de tres variedades de iris.</p>
<p><strong>Uso:</strong> predecir el tipo de iris a partir de las mediciones. <!-- I believe the following doesn't apply anymore, but I'm not sure so I'll remove it for now. One class is linearly separable from the other two; but the latter are not linearly separable from each other. --></p></td>
<td align="left"><p>Fisher, R.A. (1988). Repositorio de aprendizaje automático de UCI [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine (California): Universidad de California, Facultad de Ciencias de la Computación y de la Información</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Datos sobre consumo de combustible por distancia recorrida para varios automóviles</p></td>
<td align="left"><p>Este conjunto de datos es una versión ligeramente diferente del conjunto de datos ofrecido por la biblioteca StatLib de la Universidad Carnegie Mellon. El conjunto de datos se utilizó en la exposición de la asociación americana de estadística de 1983.</p>
<p>Los datos enumeran el consumo de combustible de varios automóviles en millas por galón, junto con información como número de cilindros, cilindrada, caballos de potencia, peso total y aceleración.</p>
<p><strong>Uso:</strong> predecir la economización de combustible en función de 3 atributos discretos multivalores y 5 atributos continuos.</p></td>
<td align="left"><p>StatLib, Universidad Carnegie Mellon, (1993). Repositorio de aprendizaje automático de UCI [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine (California): Universidad de California, Facultad de Ciencias de la Computación y de la Información</p></td>
</tr>
<tr class="even">
<td align="left"><p>Conjunto de datos de clasificación binaria sobre diabetes en indios pima</p></td>
<td align="left"><p>Subconjunto de datos de la base de datos del Instituto Nacional de la Diabetes y las Enfermedades Digestivas y Renales. El conjunto de datos se filtró para centrarse en pacientes femeninos con herencia de indios pima. Los datos incluyen información médica como niveles de glucosa e insulina y factores de estilo de vida.</p>
<p><strong>Uso:</strong> predecir si el sujeto tiene diabetes (clasificación binaria)</p></td>
<td align="left"><p>Sigillito, V. (1990). Repositorio de aprendizaje automático de UCI [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine (California): Universidad de California, Facultad de Ciencias de la Computación y de la Información</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Datos de clientes de restaurantes</p></td>
<td align="left"><p>Conjunto de metadatos sobre clientes que incluye información demográfica y preferencias.</p>
<p><strong>Uso:</strong> uso de este conjunto de datos, en combinación con los otros dos conjuntos de datos de restaurantes, para entrenar y probar un sistema de recomendaciones.</p></td>
<td align="left"><p>Bache, K. y Lichman, M. (2013). Repositorio de aprendizaje automático de UCI [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine (California): Universidad de California, Facultad de Ciencias de la Computación y de la Información.</p></td>
</tr>
<tr class="even">
<td align="left"><p>Datos de características de restaurantes</p></td>
<td align="left"><p>Un conjunto de metadatos acerca de restaurantes y sus características, como el tipo de comida, el estilo de comedor y la ubicación.</p>
<p><strong>Uso:</strong> uso de este conjunto de datos, en combinación con los otros dos conjuntos de datos de restaurantes, para entrenar y probar un sistema de recomendaciones.</p></td>
<td align="left"><p>Bache, K. y Lichman, M. (2013). Repositorio de aprendizaje automático de UCI [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine (California): Universidad de California, Facultad de Ciencias de la Computación y de la Información.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Valoraciones de restaurantes</p></td>
<td align="left"><p>Contiene valoraciones que los usuarios realizan sobre restaurantes en una escala de 0 a 2.</p>
<p><strong>Uso:</strong> uso de este conjunto de datos, en combinación con los otros dos conjuntos de datos de restaurantes, para entrenar y probar un sistema de recomendaciones.</p></td>
<td align="left"><p>Bache, K. y Lichman, M. (2013). Repositorio de aprendizaje automático de UCI [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine (California): Universidad de California, Facultad de Ciencias de la Computación y de la Información.</p></td>
</tr>
<tr class="even">
<td align="left"><p>Conjunto de datos de varias clases de recocido de acero</p></td>
<td align="left"><p>Este conjunto de datos contiene una serie de registros de pruebas de recocido de acerco con los atributos físicos , como grosor o tipo (rollo, lámina, etc.) de los tipos de acero resultantes.</p>
<p><strong>Uso:</strong> predecir uno de los dos atributos de clase numéricos: rigidez o solidez. También puede analizar las correlaciones entre atributos.</p>
<p>Los grados del acero siguen una norma establecida, definida por la SAE y otras organizaciones. Está buscando un &quot;grado&quot; determinado (la variable de clase) y desea conocer los valores requeridos.</p></td>
<td align="left"><p>Sterling, D. &amp; Buntine, W., (NA). Repositorio de aprendizaje automático de UCI [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine (California): Universidad de California, Facultad de Ciencias de la Computación y de la Información</p>
<p>Una guía útil sobre grados del acero:</p>
<p>[<a href="http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf">http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf&gt;]</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Datos de telescopio</p></td>
<td align="left"><p>Registro de explosiones de partículas gamma de alta energía junto con ruido de fondo, ambos simulados utilizando un proceso de Monte Carlo.</p>
<p>El objetivo de la simulación era mejorar la precisión de los telescopios gamma Cherenkov atmosféricos terrestres, utilizando métodos estadísticos para diferenciar entre la señal deseada (duchas de radiación de Cherenkov) y el ruido de fondo (duchas hadrónicas iniciadas por los rayos cósmicos en la atmósfera superior).</p>
<p>Los datos se sometieron a un procesado previo para crear un grupo alargado con el eje central orientado hacia el centro de la cámara. Las características de esta elipse (llamada en ocasiones parámetros de Hillas) se encuentran entre los parámetros de imagen que se pueden utilizar para la discriminación.</p>
<p><strong>Uso:</strong> predecir si la imagen de una ducha representa señal o ruido de fondo.</p>
<p><strong>Notas:</strong> una precisión de clasificación sencilla no es significativa para estos datos, ya que clasificar un evento de fondo como señal es peor que clasificar un evento de señal como fondo. Para comparar los diferentes clasificadores se debe utilizar el gráfico ROC. La probabilidad de aceptar un evento de fondo como señar debe estar por debajo de uno de los umbrales siguientes: 0,01 , 0,02 , 0,05 , 0,1 , o 0,2.</p>
<p>Además, tenga en cuenta que el número de eventos de fondo (h, para las duchas hadrónicas) está subestimado, mientras que en las medidas reales, la clase h o ruido representa la mayoría de eventos.</p></td>
<td align="left"><p>Bock, R.K. (1995). Repositorio de aprendizaje automático de UCI [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine (California): Universidad de California, Facultad de Ciencias de la Computación y de la Información</p></td>
</tr>
</tbody>
</table>

A continuación se enumeran los tipos de conjuntos de datos que puede encontrar.

| Formato de datos                                   | Descripción                                                                                                                                                                                                                                                                                                    |
|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Valores separados por comas (CSV)                  | Formato de intercambio bien conocido para todas las plataformas de datos. Estudio de aprendizaje automático carga estos datos e incorpora una función de reconocimiento automático de tipo con una distribución en columnas que aplica metadatos en función de los datos tomados como muestra de cada columna. |
| Formato de archivo de relación de atributos (ARFF) | Formato de datos de aprendizaje automático definido por WEKA. Contiene metadatos (para tipos nominales, numéricos y de cadena) que se convierten directamente en tablas de datos de Estudio de aprendizaje automático.                                                                                         |
| Texto sin formato                                  | El texto sin formato puede leerse y luego dividirse en columnas con la ayuda de módulos de procesado previo que se aplican a continuación. El principal formato de texto sin formato compatible es JSON.                                                                                                       |
| DotNetTable                                        | La versión serializada del contenedor primario pasada entre módulos de Estudio de aprendizaje automático (el *conjunto de datos*).                                                                                                                                                                             |

  [Repositorio de aprendizaje automático de UC Irvine]: http://archive.ics.uci.edu/ml/ "UC Irvine Machine Learning Repository"
