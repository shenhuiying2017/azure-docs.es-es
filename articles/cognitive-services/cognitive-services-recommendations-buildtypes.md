<properties
	pageTitle="Guía de inicio rápido: API de recomendaciones de Aprendizaje automático | Microsoft Azure"
	description="Recomendaciones de aprendizaje automática de Azure - Guía de inicio rápido"
	services="cognitive-services"
	documentationCenter=""
	authors="luiscabrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/24/2016"
	ms.author="luisca"/>

#  Tipos de compilación y calidad de los modelos #

<a name="TypeofBuilds"></a>
## ¿Qué tipo de compilación de recomendaciones debo utilizar? ##

Actualmente se admiten dos tipos de compilación: las compilaciones de *recomendación* y *FBT*. Cada una de ellos se genera con algoritmos diferentes y tiene sus propios puntos fuertes. Este documento describe cada una de estas compilaciones, así como técnicas para comparar la calidad de los modelos generados.


> Si aún no lo ha hecho, le animamos a que se complete la [guía de inicio rápido](cognitive-services-recommendations-quick-start.md).

<a name="RecommendationBuild"></a>
### Tipo de compilación de recomendación ###

El tipo de compilación de *recomendación* usa la factorización matricial para proporcionar recomendaciones. La versión corta es que utilizará las transacciones del usuario para generar vectores con [características latentes](https://en.wikipedia.org/wiki/Latent_variable) para describir cada elemento y, a continuación, utilizará dichos vectores latentes para comparar elementos similares.

Suponiendo que entrena el modelo basado en las compras realizadas en su tienda de electrónica y que en el momento de la puntuación proporciona un teléfono Lumia 650 como entrada para el modelo, devolverá un conjunto de artículos que tienden a adquirir las personas que es probable que adquieran un teléfono Lumia 650. Tenga en cuenta los elementos pueden no ser complementarios. En este ejemplo, es posible que se devuelvan otros teléfonos, ya que a las personas a las que les gusta el modelo Lumia 650 les pueden gustar otros teléfonos.

La compilación de recomendación tiene dos capacidades que hacen que sea atractiva:

-	Admite la colocación de artículos fríos.

 *Artículos fríos* es el término que se usa para denominar a aquellos artículos que no tienen mucho uso. Por ejemplo, imagine que acaba de recibir un envío del nuevo teléfono Lumia Super-Duper. Dado que nunca lo ha vendido, el sistema no puede realizar recomendaciones para este producto solo por las transacciones realizadas. Esto significa que el sistema debe aprender de la información sobre el propio producto.

 Si desea utilizar la colocación de artículos fríos, es preciso que especifique los datos de las características de todos los artículos del catálogo, así pueden ser las primeras líneas del catálogo (observe el formato clave=valor de las características):

> 6CX-00001,Surface Pro2, Surface,, Type=Hardware, Storage=128GB, Memory=4G, Manufacturer=Microsoft

> 73H-00013,Wake Xbox 360,Gaming,, Type=Software, Language=English, Rating=Mature

> WAH-0F05,Minecraft Xbox 360,Gaming,, * Type=Software, Language=Spanish, Rating=Youth

> ...

 Además, como parte de los parámetros de compilación, es preciso configurar los siguientes parámetros de compilación:

| Parámetro de compilación | Notas
|------------------     |-----------
|useFeaturesInModel | Se establece en true. Indica si se pueden utilizar características para mejorar el modelo de recomendación. 
|allowColdItemPlacement | Se establece en true. Indica si la recomendación también debería insertar elementos fríos a través de la similitud de características.
| modelingFeatureList | Lista de nombres de características separados por coma que se usará en la compilación de recomendación para mejorar la recomendación. Por ejemplo, "Language,Storage" en el ejemplo anterior.

-	Admite recomendaciones de usuario.

 Las compilaciones de Recomendación admiten [recomendaciones de usuario](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd). Esto significa que puede usar el historial de transacciones de un usuario para proporcionar recomendaciones personalizadas a dicho usuario. Para las recomendaciones de usuario puede proporcionar el identificador de usuario o el historial reciente de las transacciones de dicho usuario.

 Un ejemplo clásico en el que puede aplicar recomendaciones de usuario es la primera vez que el usuario inicia sesión en su tienda o sitio, en la página de bienvenida. Ahí puede promocionar contenido que se aplique a cada usuario en concreto.
 
 También puede aplicar un tipo de compilación de recomendaciones cuando el usuario esté a punto de extraer del repositorio. En ese momento tiene la lista de artículos que el cliente está a punto de comprar y es su oportunidad de realizar recomendaciones basadas en la cesta actual.
 
#### Parámetros de creación de recomendaciones 
 
| Nombre | 	Descripción |	 Tipo, <br> valores válidos <br> (valor predeterminado)
|-------|-------------------|------------------
| NumberOfModelIterations |	El número de iteraciones que realiza el modelo se refleja en el tiempo de proceso total y la precisión del modelo. Cuanto mayor sea el número, más precisión se obtendrá, pero el tiempo de proceso tardará más. |	 Entero, <br> de 10 a 50 <br>Valor predeterminado: 40. 
| NumberOfModelDimensions |	El número de dimensiones se relaciona con el número de 'características' que el modelo intentará buscar dentro de los datos. Aumentar el número de dimensiones le permitirá ajustar mejor los resultados en clústeres más pequeños. Sin embargo, demasiadas dimensiones impiden que el modelo encuentre correlaciones entre los elementos. |	Entero, <br> de 10 a 40 <br>Valor predeterminado: 20. |
| ItemCutOffLowerBound |	Define el número mínimo de puntos de uso que debe tener un elemento para que se tenga en cuenta en el modelo. |		Entero, <br> 2 o más. <br> Valor predeterminado: 2. |
| ItemCutOffUpperBound | 	Define el número máximo de puntos de uso que debe tener un elemento para que se tenga en cuenta en el modelo. | Entero, <br>2 o más.<br> Valor predeterminado: 2147483647. |
|UserCutOffLowerBound |	Define el número mínimo de transacciones que un usuario debe haber realizado para que se tenga en cuenta en el modelo. |	Entero, <br> 2 o más. <br> Valor predeterminado: 2. 
| ItemCutOffUpperBound |	Define el número máximo de transacciones que un usuario debe haber realizado para que se tenga en cuenta en el modelo. |	Entero, <br>2 o más. <br> Valor predeterminado: 2147483647.|
| UseFeaturesInModel |	Indica si se pueden utilizar características para mejorar el modelo de recomendación. | 	 Booleano<br> Valor predeterminado: True. 
|ModelingFeatureList |	Lista de nombres de características separados por coma que se usará en la compilación de recomendación para mejorar la recomendación. (en función de las características que sean importantes). |	Cadena, hasta 512 caracteres.
| AllowColdItemPlacement |	Indica si la recomendación también debería insertar elementos fríos a través de la similitud de características. | Booleano <br> Valor predeterminado: False.	
| EnableFeatureCorrelation | Indica si se pueden utilizar características en el razonamiento. |	Booleano <br> Valor predeterminado: False.
| ReasoningFeatureList |	Lista separada por comas de nombres de características que se utilizará para el razonamiento de las oraciones (por ejemplo, explicaciones de recomendación). (en función de las características que sean importantes para los clientes). | Cadena, hasta 512 caracteres.
| EnableU2I |	Permite la recomendación personalizada, también llamada U2I (recomendaciones de usuario a elemento). | Booleano<br> Valor predeterminado: True.
|EnableModelingInsights |	Define si se debe realizar la evaluación sin conexión con el fin de recopilar datos relevantes de modelado (es decir, métricas de precisión y diversidad). Si el valor se establece en True, no se utilizará un subconjunto de los datos para el entrenamiento, ya que tendrá que reservarse para probar el modelo. Obtenga más información sobre las [evaluaciones sin conexión](#OfflineEvaluation). | Booleano <br> Valor predeterminado: False.
| SplitterStrategy | Si el valor de la característica de recopilación de datos relevantes de modelado está establecido en True, se trata de la forma en que se dividirán los datos con fines de evaluación. | Cadena, *RandomSplitter* o *LastEventSplitter* <br>Valor predeterminado: RandomSplitter. 


<a name="FBTBuild"></a>
### Tipo de compilación FBT ###

*FBT* son las siglas de Frequently-Bought-Together (comprados conjuntamente con frecuencia). La compilación FBT realiza un análisis que cuenta el número de veces que dos o tres productos distintos aparecen juntos y, a continuación, ordena los conjuntos basándose en la similitud de su función (Co-occurrences, Jaccard y Lift).

Jaccard y Lift son formas de normalizar las coocurrencias, lo que significa que los artículos solo se devolverán si realmente se adquirieron junto con el artículo del valor de inicialización.

En el ejemplo del teléfono Lumia 650, se devolverá un teléfono X si, y solo si, el teléfono X se adquirió en la misma sesión que el teléfono Lumia 650. Dado que esto puede ser improbable, se espera que se devuelvan artículos complementarios al Lumia 650; por ejemplo, un protector de pantalla o un adaptador de corriente para el Lumia 650.

Actualmente, se asume que dos artículos se adquieren en la misma sesión si aparecen en una transacción con el mismo identificador de usuario y marca de tiempo.

En la actualidad, las compilaciones FBT no admiten artículos fríos, ya que, por definición, se espera que los dos elementos se hayan adquirido en la misma transacción. Aunque las compilaciones FBT pueden devolver conjuntos de elementos (triplos), no admiten recomendaciones personalizadas, ya que aceptan un solo artículo del valor de inicialización como entrada.


#### Parámetros de compilación FBT 
 
| Nombre | 	Descripción |		Tipo, <br> valores válidos <br> (valor predeterminado)
|-------|---------------|-----------------------
| FbtSupportThreshold | Cómo es el modelo conservador. Número de concurrencias de elementos que deben tenerse en cuenta para el modelado. | Entero,<br> de 3 a 50 <br> Valor predeterminado: 6. 
| FbtMaxItemSetSize | Limita el número de elementos en un conjunto frecuente.| Entero,<br> de 2 a 3 <br> Valor predeterminado: 2.
| FbtMinimalScore | Puntuación mínima que debe tener un conjunto frecuente para incluirlo en los resultados devueltos. Cuanto mayor sea mejor. | Doble <br> 0 y superior <br> Valor predeterminado: 0.
| FbtSimilarityFunction | Define la función de similitud que usará la compilación. Lift favorece la serendipia, Co-occurrence favorece la previsibilidad y Jaccard es un estupendo compromiso que comparten. | Cadena, <br> <i>cooccurrence, lift, jaccard</i><br> Valor predeterminado: <i>jaccard</i>. 

<a name="SelectBuild"></a>
## ¿Cómo se selecciona la compilación exacta que hay que usar? ##

Las instrucciones anteriores pueden ser suficiente para que pueda determinar si debe usar una compilación de recomendaciones o una compilación FBT, pero no es una respuesta definitiva para aquellos casos en que se pueda usar cualquiera de ellas. Además, aunque sepa que desea utilizar un tipo de compilación FBT, puede decidir si desea usar Jaccard o Lift como función de similitud.

La mejor manera de seleccionar entre dos compilaciones distintas es probar ambas en el mundo real (evaluación en línea) y realizar el seguimiento de una tasa de conversión de las diferentes compilaciones. La tasa de conversión se puede medir en función de los clics de recomendaciones, del número real de las compras realizadas a partir de las recomendaciones mostradas o incluso de las ventas realizadas cuando se mostraron distintas recomendaciones. Puede seleccionar la métrica de la tasa de conversión en función de su objetivo de negocio.

En algunos casos, el modelo se puede evaluar sin conexión antes de ponerlo en producción. Aunque la evaluación sin conexión no puede sustituir a la evaluación en línea, puede servir como métrica.

<a name="OfflineEvaluation"></a>
## Evaluación sin conexión  ##

El objetivo de una evaluación sin conexión es predecir la precisión (el número de usuarios que realmente compran uno de los artículos recomendados) y diversidad de las recomendaciones (el número real de artículos que se recomiendan). Como parte de la evaluación de las métricas de precisión y diversidad, el sistema busca una muestra de usuarios y, a continuación, las transacciones de dichos usuarios se dividen en dos grupos: el conjunto de datos de entrenamiento y el conjunto de datos de prueba.

> Nota:
>
> Para usar las métricas sin conexión es preciso tener las marcas de tiempo en los datos que se usan (dado que los datos de tiempo son necesarios para dividir correctamente el uso entre los conjuntos de datos de entrenamiento y de prueba).

> Además, la evaluación sin conexión puede no generar resultados para los archivos de poco uso, simplemente porque para que la evaluación sea exhaustiva, se esperan un mínimo de 1000 puntos de uso en el conjunto de datos de prueba.

<a name="Precision"></a>
### Precisión en K ###
La siguiente tabla representa el resultado de la evaluación sin conexión de la precisión en k.

| K | 1 | 2 | 3 | 	4 | 	5
|---|---|---|---|---|---|
|Porcentaje |	13,75 |	18,04 | 21 |	24,31 |	26,61
|Usuarios en la prueba |	10\.000 |	10\.000 |	10\.000 |	10\.000 |	10\.000
|Usuarios considerados |	10\.000 |	10\.000 |	10\.000 |	10\.000 |	10\.000
|Usuarios no considerados |	0 |	0 |	0 |	0 |	0

#### K
En la tabla anterior, *K* representa el número de recomendaciones que se muestran al cliente. Por consiguiente, la tabla dice lo siguiente: "Si durante el período de prueba, se hubiera mostrado solo una recomendación a los clientes; únicamente el 13,75 % de los usuarios habrían adquirido realmente el producto recomendado". (suponiendo que el modelo se hubiera entrenado con datos de compra). Otra forma de expresarlo es que la "precisión en 1" es del 13,75 %.

Observará que cuantos más elementos se muestran al cliente, aumenta la probabilidad de que el cliente compre un artículo recomendado. En el experimento anteriormente, la probabilidad casi se duplica, hasta llegar a un 26,61 %, cuando se recomiendan cinco artículos.

#### Porcentaje
El porcentaje de usuarios que interactuó con al menos una de las K recomendaciones que se muestran. El porcentaje se calcula dividiendo el número de usuarios que han interactuado con al menos una de las recomendaciones entre el número total de usuarios considerados. (vea la definición de usuarios considerados).

#### Usuarios en la prueba
El número total de usuarios en el conjunto de datos de prueba.

#### Usuarios considerados
Un usuario no se toma en consideración hasta que el sistema haya recomendado al menos K artículos basándose en el modelo generado mediante el conjunto de datos de entrenamiento.

#### Usuarios no considerados
Todos los usuarios que no se toman en consideración; los usuarios que no han recibido al menos K artículos recomendados.

Usuario no considerado = Usuario en la prueba - Usuarios considerados

<a name="Diversity"></a>
### Diversidad ###
Las métricas de diversidad miden el tipo de artículos recomendados. La siguiente tabla representa el resultado de la evaluación sin conexión de la diversidad.

|Depósito de percentil |	0-90| 90-99| 99-100
|------------------|--------|-------|---------
|Porcentaje | 34,258 | 55,127| 10,615


Total de artículos recomendados: 100 000

Artículos únicos recomendados: 954

#### Depósitos de percentil
Cada depósito de percentil se representa mediante un intervalo (valores mín./máx. comprendidos entre 0 y 100). Los artículos próximos al 100 son los artículos más populares, mientras que los próximos al 0 son los menos populares. Por ejemplo, si el valor porcentual del depósito de percentil 99-100 es 10,6, significa que un 10.6 % de las recomendaciones devolvieron solo el 1 % de los artículos más populares. El valor mín. del depósito de percentil es inclusivo, mientras que el valor máx. es exclusivo, excepto el 100.
#### Artículos únicos recomendados
Número de artículos distintos devueltos para la evaluación.
#### Total de artículos recomendados
El número total de artículos recomendados (algunos pueden estar duplicados).


<a name="ImplementingEvaluation"></a>
### ¿Cómo obtener evaluaciones sin conexión? ###
Las métricas sin conexión de precisión y diversidad pueden ser útiles a la hora de seleccionar la compilación que se debe usar. Para obtener métricas sin conexión debe seguir estos pasos:

En el momento de la compilación, y como parte de los respectivos parámetros de compilaciones de recomendaciones o FBT:
1.	Establezca el parámetro de compilación enableModelingInsights en true.

2.	Si lo desea, puede seleccionar *splitterStrategy* (*RandomSplitter* o *LastEventSplitter*). *RandomSplitter* divide los datos de uso de los conjuntos de entrenamiento y prueba basándose en valores de inicialización aleatorios y en el porcentaje de la prueba *randomSplitterParameters*. *LastEventSplitter* divide los datos de uso de los conjuntos de entrenamiento y prueba basándose en la última transacción de cada usuario.

Esto desencadenará una compilación que utiliza solo un subconjunto de los datos para el entrenamiento, mientras que el resto de los datos se usan para calcular las métricas de evaluación. Una vez completada la compilación, para obtener el resultado de la evaluación, basta con llamar a la [API Get build metrics](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/573e43bb3e9d4627a8c4bd3e/console) pasando los respectivos parámetros *modelId* y *buildId*.

 A continuación se muestra la salida de JSON de la evaluación de ejemplo que hemos realizado:


    {
     "Result": {
     "precisionItemRecommend": null,
     "precisionUserRecommend": {
      "precisionMetrics": [
        {
          "k": 1,
          "percentage": 13.75,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 2,
          "percentage": 18.04,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 3,
          "percentage": 21.0,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 4,
          "percentage": 24.31,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 5,
          "percentage": 26.61,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        }
      ],
      "error": null
    },
    "diversityItemRecommend": null,
    "diversityUserRecommend": {
      "percentileBuckets": [
        {
          "min": 0,
          "max": 90,
          "percentage": 34.258
        },
        {
          "min": 90,
          "max": 99,
          "percentage": 55.127
        },
        {
          "min": 99,
          "max": 100,
          "percentage": 10.615
        }
      ],
      "totalItemsRecommended": 100000,
      "uniqueItemsRecommended": 954,
      "uniqueItemsInTrainSet": null,
      "error": null
      }
     },
    "Id": 1,
    "Exception": null,
    "Status": 5,
    "IsCanceled": false,
    "IsCompleted": true,
    "CreationOptions": 0,
    "AsyncState": null,
    "IsFaulted": false
    }

<!---HONumber=AcomDC_0706_2016-->