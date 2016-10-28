<properties
	pageTitle="Guía de inicio rápido: API de recomendaciones de Aprendizaje automático | Microsoft Azure"
	description="Recomendaciones de Aprendizaje automático de Azure: guía de inicio rápido"
	services="cognitive-services"
	documentationCenter=""
	authors="luiscabrer"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="luisca"/>

#  Tipos de compilación y calidad de los modelos #

<a name="TypeofBuilds"></a>
## Tipos de compilación compatibles ##

Actualmente, Recommendations API admite dos tipos de compilación: *recomendación* y *FBT*. Cada una se genera con algoritmos diferentes y tiene sus propios puntos fuertes. Este documento describe cada una de estas compilaciones, así como técnicas para comparar la calidad de los modelos generados.

Si aún no lo ha hecho, le animamos a que lea la [guía de inicio rápido](cognitive-services-recommendations-quick-start.md).

<a name="RecommendationBuild"></a>
### Tipo de compilación de recomendación ###

El tipo de compilación de recomendación usa la factorización matricial para proporcionar recomendaciones. Generas vectores con [características latentes](https://en.wikipedia.org/wiki/Latent_variable) basados en las transacciones para describir cada elemento y, a continuación, utiliza dichos vectores latentes para comparar elementos similares.

Si entrena el modelo basado en las compras realizadas en su tienda de electrónica y proporciona un teléfono Lumia 650 como entrada para el modelo, este devolverá un conjunto de artículos que tienden a adquirir las personas que es probable que adquieran un teléfono Lumia 650. Los artículos pueden no ser complementarios. En este ejemplo, es posible que el modelo devuelva otros teléfonos, ya que a las personas a las que les gusta el modelo Lumia 650 les pueden gustar otros teléfonos.

La compilación de recomendación tiene dos capacidades que hacen que sea atractiva:

**La compilación de recomendación admite la colocación de *artículos con poca* rotación**.

Los artículos que no tienen un uso significativo se denominan artículos con poca rotación. Por ejemplo, si recibe un envío de un teléfono que nunca ha vendido antes, el sistema no puede inferir recomendaciones para este producto basadas solo en las transacciones. Esto significa que el sistema debe aprender de la información sobre el propio producto.

Si desea utilizar la colocación de elementos con poca rotación, debe proporcionar información de las características para cada uno de los artículos del catálogo. A continuación, se muestra el aspecto que deberían tener las primeras líneas del catálogo (observe el formato clave=valor para las características).

>6CX-00001,Surface Pro2, Surface,, Type=Hardware, Storage=128 GB, Memory=4G, Manufacturer=Microsoft

>73H-00013,Wake Xbox 360,Gaming,, Type=Software, Language=English, Rating=Mature

>WAH-0F05,Minecraft Xbox 360,Gaming,, * Type=Software, Language=Spanish, Rating=Youth

También debe configurar los siguientes parámetros de compilación:

| Parámetro de compilación | Notas
|------------------     |-----------
|*useFeaturesInModel* | Se establece en **true**. Indica si se pueden utilizar características para mejorar el modelo de recomendación.
|*allowColdItemPlacement* | Se establece en **true**. Indica si la recomendación también debería insertar elementos fríos a través de la similitud de características.
| *modelingFeatureList* | Lista de nombres de características separados por coma que se usará en la compilación de recomendación para mejorar la recomendación. Por ejemplo, "Language,Storage" en el ejemplo anterior.

**La compilación de recomendación admite recomendaciones de usuario.**

La compilación de recomendación admite [recomendaciones de usuario](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd). Esto significa que puede proporcionar recomendaciones personalizadas para los usuarios según sus historiales de transacción. Para las recomendaciones de usuario puede proporcionar el identificador de usuario o el historial de las transacciones recientes de dicho usuario.

Un ejemplo clásico de dónde puede desear aplicar recomendaciones de usuario es al iniciar sesión en la página de bienvenida. Ahí puede promocionar contenido que se aplique a cada usuario en concreto.

También puede aplicar un tipo de compilación de recomendación cuando el usuario esté a punto de pagar. En ese momento, tiene la lista de artículos que el cliente está a punto de comprar y puede realizar recomendaciones basadas en la cesta actual.

#### Parámetros de creación de recomendaciones

| Nombre | 	Description |	 Tipo, <br> valores válidos, <br> (valor predeterminado)
|-------|-------------------|------------------
| *NumberOfModelIterations* |	El número de iteraciones que realiza el modelo se refleja en el tiempo de proceso total y la precisión del modelo. Cuanto mayor sea el número, más preciso será el modelo, pero el tiempo de proceso será mayor. |	 Entero, <br> de 10 a 50 <br>(40)
| *NumberOfModelDimensions* |	El número de dimensiones se relaciona con el número de características que el modelo intentará encontrar dentro de los datos. Aumentar el número de dimensiones le permitirá ajustar mejor los resultados en clústeres más pequeños. Sin embargo, demasiadas dimensiones impiden que el modelo encuentre correlaciones entre los elementos. |	Entero, <br> de 10 a 40 <br>(20) |
| *ItemCutOffLowerBound* |	Define el número mínimo de puntos de uso que debe tener un artículo para que se considere parte del modelo. |		Entero, <br> 2 o más<br> (2) |
| *ItemCutOffUpperBound* | 	Define el número máximo de puntos de uso que debe tener un artículo para que se considere parte del modelo. | Entero, <br> 2 o más<br> (2147483647) |
|*UserCutOffLowerBound* |	Define el número mínimo de transacciones que un usuario debe haber realizado para que se considere parte del modelo. |	Entero, <br> 2 o más<br> (2)
| *UserCutOffUpperBound* |	Define el número máximo de transacciones que un usuario debe haber realizado para que se considere parte del modelo. |	Entero, <br> 2 o más<br> (2147483647)|
| *UseFeaturesInModel* |	Indica si se pueden utilizar características para mejorar el modelo de recomendación. | 	 Booleano<br> Valor predeterminado: True.
|*ModelingFeatureList* |	Lista de nombres de características separados por coma que se usará en la compilación de recomendación para mejorar la recomendación. Depende de las características que sean importantes. |	Cadena, hasta 512 caracteres.
| *AllowColdItemPlacement* |	Indica si la recomendación también debería insertar elementos fríos a través de la similitud de características. | Booleano <br> Valor predeterminado: False.
| *EnableFeatureCorrelation* | Indica si se pueden utilizar características en el razonamiento. |	Booleano <br> Valor predeterminado: False.
| *ReasoningFeatureList* |	Lista de nombres de características separados por coma que se usará para el razonamiento de las frases, como las explicaciones de las recomendaciones. Depende de las características que sean importantes para los clientes. | Cadena, hasta 512 caracteres.
| *EnableU2I* |	Permite realizar recomendaciones personalizadas, también denominadas recomendaciones "de usuario para artículo" (U2I). | Booleano<br>Valor predeterminado: True.
|*EnableModelingInsights* |	Define si se debe realizar la evaluación sin conexión con el fin de recopilar datos de modelado (es decir, métricas de precisión y diversidad). Si se establece en True, no se utilizará un subconjunto de los datos para el entrenamiento, ya que tendrá que reservarse para probar el modelo. Lea más sobre las [evaluaciones sin conexión](#OfflineEvaluation). | Booleano <br> Valor predeterminado: False.
| *SplitterStrategy* | Si el valor de la característica para permitir datos de modelado está establecido en *True*, esta es la forma en que se dividirán los datos con fines de evaluación. | Cadena, *RandomSplitter* o *LastEventSplitter* <br>Valor predeterminado: RandomSplitter.


<a name="FBTBuild"></a>
### Tipo de compilación FBT ###

La compilación de "comprados juntos frecuentemente" (FBT por sus siglas en inglés) realiza un análisis que cuenta el número de veces que dos o tres productos distintos se compran juntos. A continuación, ordena los conjuntos basados en una función de similitud (**co-occurrences**, **Jaccard**, **lift**).

**Jaccard** y **lift** son formas de normalizar las apariciones conjuntas. Esto significa que los artículos solo se devolverán si realmente se adquirieron juntos con el artículo inicial.

En el ejemplo del teléfono Lumia 650, se devolverá un teléfono X solo si el teléfono X se adquirió en la misma sesión que el teléfono Lumia 650. Dado que esto puede ser improbable, se espera que se devuelvan artículos complementarios al Lumia 650; por ejemplo, un protector de pantalla o un adaptador de corriente para el Lumia 650.

Actualmente, se considera que dos artículos se adquieren en la misma sesión si aparecen en una transacción con el mismo identificador de usuario y marca de tiempo.

En la actualidad, las compilaciones FBT no admiten artículos con poca rotación, ya que, por definición, se esperan dos elementos que se hayan adquirido en la misma transacción. Aunque las compilaciones FBT pueden devolver conjuntos de artículos (triplos), no admiten recomendaciones personalizadas, ya que aceptan un solo artículo inicial como entrada.


#### Parámetros de compilación FBT

| Nombre | 	Description |		Tipo, <br> valores válidos, <br> (valor predeterminado)
|-------|---------------|-----------------------
| *FbtSupportThreshold* | Cómo es el modelo conservador. Número de concurrencias de elementos que deben tenerse en cuenta para el modelado. | Entero, <br> 3-50 <br> (6)
| *FbtMaxItemSetSize* | Limita el número de elementos en un conjunto frecuente.| Entero <br> 2-3 <br> (2)
| *FbtMinimalScore* | Puntuación mínima que debe tener un conjunto frecuente para incluirlo en los resultados devueltos. Cuanto mayor sea mejor. | Doble <br> 0 y superior <br> (0)
| *FbtSimilarityFunction* | Define la función de similitud que usará la compilación. **Lift** favorece la serendipia, **co-occurrence** favorece la previsibilidad y **Jaccard** es un compromiso entre los dos. | Cadena, <br> <i>cooccurrence, lift, jaccard</i><br> Valor predeterminado: <i>jaccard</i>.

<a name="SelectBuild"></a>
## Evaluación y selección de compilación ##

Esta guía puede ayudarle a determinar si debe usar una compilación de recomendación o una compilación FBT, pero no proporciona una respuesta definitiva en los casos en que puede usar cualquiera de ellas. Además, aunque sepa que desea utilizar un tipo de compilación FBT, puede decidir si desea usar **Jaccard** o **lift** como función de similitud.

La mejor manera de seleccionar entre dos compilaciones distintas es probar ambas en el mundo real (evaluación en línea) y realizar el seguimiento de una tasa de conversión de las diferentes compilaciones. La tasa de conversión se puede medir en función de los clics en las recomendaciones, el número real de las compras realizadas en las recomendaciones mostradas o incluso de las cantidades compradas reales cuando se mostraron distintas recomendaciones. Puede seleccionar la métrica de la tasa de conversión en función de su objetivo de negocio.

En algunos casos, el modelo se puede evaluar sin conexión antes de ponerlo en producción. Aunque la evaluación sin conexión no puede sustituir a la evaluación en línea, puede servir como métrica.

<a name="OfflineEvaluation"></a>
## Evaluación sin conexión  ##

El objetivo de una evaluación sin conexión es predecir la precisión (el número de usuarios que compran uno de los artículos recomendados) y la diversidad de las recomendaciones (el número de artículos que se recomiendan). Como parte de la evaluación de las métricas de precisión y diversidad, el sistema busca una muestra de usuarios y divide las transacciones de dichos usuarios en dos grupos: el conjunto de datos de entrenamiento y el conjunto de datos de prueba.

> [AZURE.NOTE] Para usar las métricas sin conexión, debe tener marcas de tiempo en los datos de uso. Los datos de tiempo son necesarios para dividir correctamente el uso entre los conjuntos de datos de entrenamiento y de prueba.

> Además, es posible que la evaluación sin conexión no produzca resultados para archivos de uso pequeños. Para que la evaluación sea exhaustiva, debe haber un mínimo de 1000 puntos de uso en el conjunto de datos de prueba.

<a name="Precision"></a>
### Precisión en K ###
La siguiente tabla representa el resultado de la evaluación sin conexión de la precisión en K.

| K | 1 | 2 | 3 | 	4 | 	5
|---|---|---|---|---|---|
|Porcentaje |	13,75 |	18,04 | 21 |	24,31 |	26,61
|Usuarios en la prueba |	10\.000 |	10\.000 |	10\.000 |	10\.000 |	10\.000
|Usuarios considerados |	10\.000 |	10\.000 |	10\.000 |	10\.000 |	10\.000
|Usuarios no considerados |	0 |	0 |	0 |	0 |	0

#### K
En la tabla anterior, *K* representa el número de recomendaciones que se muestran al cliente. La tabla indica lo siguiente: "Si durante el período de prueba, solo se hubiera mostrado una recomendación a los clientes; únicamente el 13,75 % de los usuarios habría adquirido el producto recomendado". Esta instrucción se basa en la suposición de que el modelo se ha entrenado con datos de compra. Otra forma de expresarlo es que la precisión en 1 es 13,75.

Observará que cuantos más elementos se muestran al cliente, aumenta la probabilidad de que el cliente compre un artículo recomendado. En el experimento anterior, la probabilidad casi se duplica, hasta llegar a un 26,61 %, cuando se recomiendan cinco artículos.

#### Porcentaje
El porcentaje de usuarios que interactuó con al menos una de las recomendaciones en *K* que se muestran. El porcentaje se calcula dividiendo el número de usuarios que han interactuado con al menos una de las recomendaciones por el número total de usuarios considerados. Consulte Usuarios considerados para más información.

#### Usuarios en la prueba
Los datos de esta fila representan el número total de usuarios en el conjunto de datos de prueba.

#### Usuarios considerados
Un usuario no se toma en consideración hasta que el sistema haya recomendado al menos *K* artículos basándose en el modelo generado mediante el conjunto de datos de entrenamiento.

#### Usuarios no considerados
Los datos de esta fila representan todos los usuarios que no se consideran. Los usuarios que no han recibido al menos *K* artículos recomendados.

Usuario no considerado = usuarios en la prueba - usuarios considerados

<a name="Diversity"></a>
### Diversidad ###
Las métricas de diversidad miden el tipo de artículos recomendados. La siguiente tabla representa la salida de la evaluación sin conexión de la diversidad.

|Depósito de percentil |	0-90| 90-99| 99-100
|------------------|--------|-------|---------
|Porcentaje | 34,258 | 55,127| 10,615


Total de artículos recomendados: 100 000

Artículos únicos recomendados: 954

#### Depósitos de percentil
Cada depósito de percentil se representa mediante un intervalo (valores mínimo y máximo comprendidos entre 0 y 100). Los artículos próximos al 100 son los artículos más populares, mientras que los próximos al 0 son los menos populares. Por ejemplo, si el valor porcentual del depósito de percentil 99-100 es de 10,6, significa que un 10,6 % de las recomendaciones devolvieron solo el 1 % de los artículos más populares. El valor mínimo del depósito de percentil es inclusivo, mientras que el valor máximo es exclusivo, excepto el 100.
#### Artículos únicos recomendados
La métrica de artículos únicos recomendados muestra el número de artículos distintivos que se han devuelto para su evaluación.
#### Total de artículos recomendados
La métrica de artículos recomendados totales muestra el número de artículos recomendados. Algunos pueden estar duplicados.

<a name="ImplementingEvaluation"></a>
### Métricas de evaluación sin conexión ###
Las métricas sin conexión de precisión y diversidad pueden ser útiles al seleccionar la compilación que se usarán. En el momento de la compilación, y como parte de los respectivos parámetros de compilaciones de recomendación o FBT:

-	Establezca el parámetro de compilación *enableModelingInsights* en **true**.
-	Si lo desea, puede seleccionar *splitterStrategy* (*RandomSplitter* o *LastEventSplitter*). *RandomSplitter* divide los datos de uso de los conjuntos de entrenamiento y prueba basándose en valores de inicialización aleatorios y en el porcentaje de la prueba *randomSplitterParameters*. *LastEventSplitter* divide los datos de uso de los conjuntos de entrenamiento y prueba basándose en la última transacción de cada usuario.

Esto desencadenará una compilación que utiliza solo un subconjunto de los datos para el entrenamiento y usa el resto de los datos para calcular las métricas de evaluación. Una vez completada la compilación, para obtener la salida de la evaluación, es necesario llamar a la [API Get build metrics](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/577eaa75eda565095421666f) pasando los respectivos parámetros *modelId* y *buildId*.

 A continuación se muestra la salida de JSON de la evaluación de ejemplo:


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

<!---HONumber=AcomDC_0921_2016-->