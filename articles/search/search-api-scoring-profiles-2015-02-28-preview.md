---
title: "Perfiles de puntuación (API de REST de Azure Search: 2015-02-28-Preview) | Microsoft Docs"
description: "Búsqueda de Azure es un servicio de búsqueda hospedado en la nube que admite la optimización de resultados basados en perfiles de puntuación definidos por el usuario."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
ms.assetid: bfd62649-13d7-40b3-a8fa-85521a15084d
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.author: heidist
ms.date: 10/27/2016
ms.openlocfilehash: a67637d149a84313270c03d21acf8a9c1870be05
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="scoring-profiles-azure-search-rest-api-version-2015-02-28-preview"></a>Perfiles de puntuación (API de REST de Búsqueda de Azure: 2015-02-28-Preview)
> [!NOTE]
> En este artículo se describen los perfiles puntuación de la versión [2015-02-28-Preview](search-api-2015-02-28-preview.md). Actualmente, no hay ninguna diferencia entre la versión `2016-09-01` documentada en [MSDN](http://msdn.microsoft.com/library/azure/mt183328.aspx) y la versión `2015-02-28-Preview` que se describe aquí, pero ofrecemos este documento de todos modos para proporcionar cobertura de documentación en toda la API.
>
>

## <a name="overview"></a>Información general
Puntuación hace referencia al cálculo de una puntuación de búsqueda para todos los elementos devueltos en los resultados de la búsqueda. La puntuación es un indicador de la importancia de un elemento en el contexto de la operación de búsqueda actual. Cuanto mayor sea la puntuación, mayor importancia tendrá el elemento. En los resultados de búsqueda, los elementos están ordenados de alto a bajo, según la puntuación de la búsqueda calculada para cada artículo.

Búsqueda de Azure usa la puntuación predeterminada para calcular una puntuación inicial, pero puede personalizar el cálculo a través de un perfil de puntuación. Los perfiles de puntuación proporcionan un mayor control sobre la clasificación de los elementos en los resultados de búsqueda. Por ejemplo, desea aumentar los elementos según su potencial de ingresos, promover elementos más recientes o quizás aumentar los elementos que han permanecido en inventario demasiado largo.

Un perfil de puntuación es parte de la definición del índice que se compone de campos, funciones y parámetros.

Para darle una idea del aspecto de un perfil de puntuación, el ejemplo siguiente muestra un perfil simple denominado «geográfico». Este aumenta los elementos que tengan el término de búsqueda en el campo `hotelName` . También usa la función `distance` para dar prioridad a elementos que están a menos de diez kilómetros de la ubicación actual. Si alguien busca el término «inn» y este término forma parte del nombre del hotel, los documentos que incluyan hoteles con «inn» aparecerán primero en los resultados de búsqueda.

    "scoringProfiles": [
      {
        "name": "geo",
        "text": {
          "weights": { "hotelName": 5 }
        },
        "functions": [
          {
            "type": "distance",
            "boost": 5,
            "fieldName": "location",
            "interpolation": "logarithmic",
            "distance": {
              "referencePointParameter": "currentLocation",
              "boostingDistance": 10
            }
          }
        ]
      }
    ]

Para usar este perfil de puntuación, se formula la consulta para especificar el perfil en la cadena de consulta. En la siguiente consulta, observe el parámetro de consulta `scoringProfile=geo` en la solicitud.

    GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2015-02-28-Preview

Esta consulta busca el término «inn» y pasa la ubicación actual. Tenga en cuenta que esta consulta incluye otros parámetros, como `scoringParameter`. Los parámetros de consulta se describen en [Buscar documentos (API de Búsqueda de Azure)](search-api-2015-02-28-preview.md#SearchDocs).

Haga clic en [Ejemplo](#example) para revisar un ejemplo más detallado de un perfil de puntuación.

## <a name="what-is-default-scoring"></a>¿Qué es la puntuación predeterminada?
La puntuación calcula una puntuación de la búsqueda para cada elemento de un conjunto de resultados ordenado por rango. A cada elemento de un conjunto de resultados de búsqueda se le asigna una puntuación de búsqueda, a continuación, se ordena de mayor a menor. Elementos con las puntuaciones más altas se devuelven a la aplicación. De forma predeterminada, se devuelven los 50 mejores, pero puede usar el parámetro `$top` para devolver un número mayor o menor de elementos (hasta 1000 en una sola respuesta).

De forma predeterminada, una puntuación de búsqueda se calcula en función de las propiedades estadísticas de los datos y la consulta. Búsqueda de Azure busca documentos que contengan los términos de búsqueda de la cadena de consulta (algunas o todas, dependiendo de `searchMode`), lo cual favorece a los documentos que contienen muchas instancias del término de búsqueda. La puntuación de búsqueda aumenta todavía más si el término es poco frecuente en el corpus de datos, pero común dentro del documento. La base de este enfoque de relevancia informática se denomina TF-IDF o (frecuencia de documento inverso de la frecuencia del término).

Suponiendo que no haya ninguna ordenación personalizada, los resultados se clasifican por el resultado de la búsqueda antes de que se devuelvan a la aplicación de llamada. Si `$top` no se especifica, se devolverán 50 elementos con la puntuación de búsqueda mayor.

Los valores de puntuación de búsqueda pueden repetirse a lo largo de un conjunto de resultados. Por ejemplo, puede tener 10 elementos con una puntuación de 1,2, 20 elementos con una puntuación de 1,0 y 20 elementos con una puntuación de 0,5. Cuando varios resultados tienen la misma puntuación de búsqueda, el orden de estos elementos puntuados no se define y no es estable. Vuelva a ejecutar la consulta verá cómo los elementos cambian de posición. Si dos elementos disponen de la misma puntuación, no hay ninguna garantía de cuál aparecerá en primer lugar.

## <a name="when-to-use-custom-scoring"></a>Cuándo usar la puntuación personalizada
Debe crear uno o más perfiles de puntuación cuando el comportamiento de clasificación predeterminado no logre cumplir los objetivos de su empresa. Por ejemplo, podría decidir que la relevancia de la búsqueda debe favorecer a los elementos recién agregados. Asimismo, podría tener un campo que contenga el margen de beneficio, o algún otro campo que indique los ingresos potenciales. Aumentar los resultados que ofrecen beneficios a su empresa puede ser un factor importante a la hora de decidir usar perfiles de puntuación.

El orden basado en relevancia también se implementa a través de perfiles de puntuación. Tenga en cuenta los resultados de búsqueda que ha usado en el pasado que le permiten ordenar por relevancia, fecha, clasificación o precio. En la Búsqueda de Azure, los perfiles de puntuación determinan la opción «relevancia». La definición de relevancia está controlada por usted, se afirma en los objetivos empresariales y en el tipo de experiencia de búsqueda que desee ofrecer.

<a name="example"></a>

## <a name="example"></a>Ejemplo
Tal y como se ha indicado, la puntuación personalizada se implementa mediante los perfiles de puntuación definidos en un esquema de índice.

En este ejemplo se muestra el esquema de un índice con dos perfiles de puntuación (`boostGenre`, `newAndHighlyRated`). Las consultas sobre este índice que incluyen cualquiera de los perfiles como parámetro de consulta usará el perfil para puntuar el conjunto de resultados.

    {
      "name": "musicstoreindex",
      "fields": [
        { "name": "key", "type": "Edm.String", "key": true },
        { "name": "albumTitle", "type": "Edm.String" },
        { "name": "albumUrl", "type": "Edm.String", "filterable": false },
        { "name": "genre", "type": "Edm.String" },
        { "name": "genreDescription", "type": "Edm.String", "filterable": false },
        { "name": "artistName", "type": "Edm.String" },
        { "name": "orderableOnline", "type": "Edm.Boolean" },
        { "name": "rating", "type": "Edm.Int32" },
        { "name": "tags", "type": "Collection(Edm.String)" },
        { "name": "price", "type": "Edm.Double", "filterable": false },
        { "name": "margin", "type": "Edm.Int32", "retrievable": false },
        { "name": "inventory", "type": "Edm.Int32" },
        { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }
      ],
      "scoringProfiles": [
        {
          "name": "boostGenre",
          "text": {
            "weights": {
              "albumTitle": 1.5,
              "genre": 5,
              "artistName": 2
            }
          }
        },
        {
          "name": "newAndHighlyRated",
          "functions": [
            {
              "type": "freshness",
              "fieldName": "lastUpdated",
              "boost": 10,
              "interpolation": "quadratic",
              "freshness": {
                "boostingDuration": "P365D"
              }
            },
            {
              "type": "magnitude",
              "fieldName": "rating",
              "boost": 10,
              "interpolation": "linear",
              "magnitude": {
                "boostingRangeStart": 1,
                "boostingRangeEnd": 5,
                "constantBoostBeyondRange": false
              }
            }
          ]
        }
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["albumTitle", "artistName"]
        }
      ]
    }


## <a name="workflow"></a>Flujo de trabajo
Para implementar el comportamiento personalizado de puntuación, agregue un perfil de puntuación al esquema que define el índice. Puede tener hasta 16 perfiles de puntuación en un índice, (vea [Límites de servicio en Azure Search](search-limits-quotas-capacity.md)), pero solo puede especificar un perfil de cada vez en una consulta concreta.

Comience con la [Plantilla](#bkmk_template) incluida en este tema.

Proporcione un nombre. Los perfiles de puntuación son opcionales, pero si agrega uno, se requerirá el nombre. Asegúrese de seguir las convenciones de nomenclatura de los campos (empieza con una letra, evita caracteres especiales y palabras reservadas). Consulte [Reglas de nomenclatura](http://msdn.microsoft.com/library/azure/dn857353.aspx) para obtener más información.

El cuerpo del perfil de puntuación se construye a partir de campos ponderados y funciones.

### <a name="weights"></a>Pesos
La propiedad `weights` de un perfil de puntuación especifica los pares de nombre-valor que asignan una ponderación relativa a un campo. En el [ejemplo](#example), los campos albumTitle, genre y artistName se aumentan a 1.5, 5 y 2 respectivamente. ¿Por qué se aumenta el género mucho mayor que los demás? Si la búsqueda se realiza en datos que son en cierto modo homogéneos (como es el caso con «género» en el `musicstoreindex`), es posible que necesite una mayor variación en la ponderación relativa. Por ejemplo, en `musicstoreindex`, «rock» aparece como género y en las descripciones de género expresadas de forma idéntica. Si desea que el género supere en ponderación a la descripción del género, el campo del género necesitará una ponderación relativa mucho mayor.

### <a name="functions"></a>Funciones
Funciones usadas cuando se requieren cálculos adicionales para contextos concretos. Los tipos de función válidos son `freshness`, `magnitude`, `distance` y `tag`. Cada función tiene parámetros que son exclusivos de ella.

* `freshness` debe usarse cuando desea aumentar según cómo de nuevo o de antiguo es un elemento. Esta función solo puede usarse con campos datetime (`Edm.DataTimeOffset`). Tenga en cuenta que el atributo `boostingDuration` solo se usa con la función de actualización.
* `magnitude` debe usarse cuando desea aumentar en función de cómo de alto o de bajo es un valor numérico. Entre los escenarios que requieren esta función se incluyen aumentar por margen de beneficio, precio máximo, precio mínimo o recuento de descargas. Puede invertir el rango, de alto a bajo, si desea que el patrón inverso potencie, por ejemplo, los elementos de precio inferior más que los de precio superior. Dado un intervalo de precios de 100 $ a 1 $, establezca `boostingRangeStart` en 100 y `boostingRangeEnd` en 1 para potenciar los elementos de menor precio. Esta función solo puede usarse con campos doble y de número entero.
* `distance` debe usarse cuando quiera potenciar según proximidad o ubicación geográfica. Esta función solo puede usarse con campos `Edm.GeographyPoint` .
* `tag` debe usarse cuando quiera potenciar por etiquetas en común entre documentos y consultas de búsqueda. Esta función solo puede usarse con campos `Edm.String` y `Collection(Edm.String)`.

#### <a name="rules-for-using-functions"></a>Reglas de uso de funciones
* El tipo de función (actualización, magnitud, distancia, etiqueta) debe aparecer en minúsculas.
* Las funciones no pueden incluir valores nulos ni estar vacías. En concreto, si incluye un nombre de campo, deberá establecerlo en un valor.
* Las funciones solo pueden aplicarse a los campos que se pueden filtrar. Consulte [Crear índice](search-api-2015-02-28-preview.md#CreateIndex) para más información sobre los campos que se pueden filtrar.
* Las funciones solo pueden aplicarse a los campos que se definen en la colección de campos de un índice.

Una vez definido el índice, genere el índice mediante la carga del esquema de índice, seguido de documentos. Consulte [Crear índice](search-api-2015-02-28-preview.md#CreateIndex) y [Agregar, actualizar o eliminar documentos](search-api-2015-02-28-preview.md#AddOrUpdateDocuments) para obtener instrucciones sobre estas operaciones. Una vez creado el índice, debe tener un perfil de puntuación funcional que funcione con los datos de búsqueda.

<a name="bkmk_template"></a>

## <a name="template"></a>Plantilla
En esta sección se muestra la sintaxis y la plantilla de perfiles de puntuación. Consulte [Referencia del atributo de índice](#bkmk_indexref) en la sección siguiente para obtener descripciones de los atributos.

    ...
    "scoringProfiles": [
      {
        "name": "name of scoring profile",
        "text": (optional, only applies to searchable fields) {
          "weights": {
            "searchable_field_name": relative_weight_value (positive #'s),
            ...
          }
        },
        "functions": (optional) [
          {
            "type": "magnitude | freshness | distance | tag",
            "boost": # (positive number used as multiplier for raw score != 1),
            "fieldName": "...",
            "interpolation": "constant | linear (default) | quadratic | logarithmic",

            "magnitude": {
              "boostingRangeStart": #,
              "boostingRangeEnd": #,
              "constantBoostBeyondRange": true | false (default)
            }

            // (- or -)

            "freshness": {
              "boostingDuration": "..." (value representing timespan over which boosting occurs)
            }

            // (- or -)

            "distance": {
              "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
              "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
            }

            // (- or -)

            "tag": {
              "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field)
            }
          }
        ],
        "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
      }
    ],
    "defaultScoringProfile": (optional) "...",
    ...

<a name="bkmk_indexref"></a>

## <a name="scoring-profile-property-reference"></a>Referencia de propiedad de perfil de puntuación
> [!NOTE]
> Una función de puntuación solo puede aplicarse a campos filtrables.
>
>

| Propiedad | Description |
| --- | --- |
| `name` |Obligatorio. Este es el nombre del perfil de puntuación. Sigue las mismas convenciones de nomenclatura de un campo. Debe comenzar por una letra, no puede contener puntos, dos puntos o símbolos @ y no puede comenzar con la frase «Búsquedaazure» (distingue entre mayúsculas y minúsculas). |
| `text` |Contiene la propiedad Weights. |
| `weights` |Opcional. Par de nombre-valor que especifica un nombre de campo y un peso relativo. El peso relativo debe ser un número entero positivo o un número de punto flotante. Puede especificar el nombre del campo sin un peso correspondiente. Weights se usa para indicar la importancia de un campo en relación con otro. |
| `functions` |Opcional. Tenga en cuenta que las funciones de puntuación solo pueden aplicarse a campos filtrables. |
| `type` |Obligatorio para las funciones de puntuación. Indica el tipo de función que se usará. Entre los valores válidos se incluyen `magnitude`, `freshness`, `distance` y `tag`. Puede incluir más de una función en cada perfil de puntuación. El nombre de la función debe estar en minúsculas. |
| `boost` |Obligatorio para las funciones de puntuación. Número positivo usado como multiplicador para el resultado sin formato. No puede ser igual a 1. |
| `fieldName` |Obligatorio para las funciones de puntuación. Una función de puntuación solo puede aplicarse a los campos que forman parte de la colección de campos del índice y que son filtrables. Además, cada tipo de función introduce restricciones adicionales (índice de actualización se usa con campos de fecha y hora, magnitud con número entero o campos dobles, distancia con campos de ubicación y etiqueta con cadena o campos de colección de cadenas). Solo puede especificar un campo único por cada definición de función. Por ejemplo, para usar magnitud dos veces en el mismo perfil, necesitaría incluir dos magnitudes de definiciones, una para cada campo. |
| `interpolation` |Obligatorio para las funciones de puntuación. Define la pendiente con la que la potenciación de la puntuación aumenta desde el inicio del rango hasta su final. Entre los valores válidos se incluyen `linear` (predeterminado), `constant`, `quadratic` y `logarithmic`. Consulte [Establecer interpolaciones](#bkmk_interpolation) para más información. |
| `magnitude` |La función de puntuación de la magnitud se usa para modificar las clasificaciones según el intervalo de valores de un campo numérico. Algunos de los ejemplos de uso más habituales sobre esto son:<ul><li>Clasificaciones por estrellas: modifique la puntuación en función del valor del campo "Clasificación por estrellas". Cuando dos elementos son pertinentes, en primer lugar se mostrará el elemento con la clasificación superior.</li><li>Margen: cuando dos documentos son relevantes, es posible que un distribuidor aumente los documentos que tengan mayores márgenes a las primeras posiciones.</li><li>Recuentos de clics: en las aplicaciones que hacen un seguimiento de los clics en productos o páginas, puede usar la magnitud para aumentar elementos que tienden a atraer el máximo de tráfico.</li><li>Recuentos de descargas: para las aplicaciones que hacen el seguimiento de descargas, la función magnitud le permite aumentar los elementos que tienen más descargas.</li></ul> |
| `magnitude:boostingRangeStart` |Establece el valor inicial del intervalo en el que se puntúa la magnitud. El valor debe ser un número entero o un número de punto flotante. En cuanto a las clasificaciones por estrellas de 1 a 4, esto equivaldría a 1. Para los márgenes superiores al 50%, esto equivaldría a 50. |
| `magnitude:boostingRangeEnd` |Establece el valor final del intervalo en el que se puntúa la magnitud. El valor debe ser un número entero o un número de punto flotante. En cuanto a las clasificaciones por estrellas de 1 a 4, esto equivaldría a 4. |
| `magnitude:constantBoostBeyondRange` |Los valores válidos son true o false (valor predeterminado). Cuando se establece en true, la potenciación completa continuará aplicándose a los documentos que tienen un valor para el campo de destino superior al extremo superior del intervalo. Si es false, la potenciación de esta función no se aplicará a los documentos que tengan un valor para el campo de destino que se encuentre fuera del intervalo. |
| `freshness` |La función de puntuación del índice de actualización se usa para modificar las puntuaciones de clasificación de los elementos basados en valores de campos de DateTimeOffset. Por ejemplo, un elemento con una fecha más reciente puede clasificarse por encima de los elementos más antiguos. (Tenga en cuenta que también es posible clasificar elementos, como eventos del calendario con fechas futuras, de manera que los elementos más cercanos al presente tengan una clasificación más alta que los elementos más alejados en el futuro). En la versión actual del servicio, uno de los extremos del intervalo se corregirá a la hora actual. El otro extremo es un momento en el pasado basado en el elemento `boostingDuration`. Para potenciar un intervalo de horas en el futuro use una `boostingDuration`negativa. La velocidad a la que cambia la potenciación desde un intervalo máximo y mínimo viene determinada por la interpolación aplicada al perfil de puntuación (consulte la figura siguiente). Para invertir el factor de potenciación aplicado, seleccione un factor de potenciación de menos de 1. |
| `freshness:boostingDuration` |Establece un período de caducidad después del que se detendrá la potenciación de un documento determinado. Consulte [Establecer boostingDuration](#bkmk_boostdur) en la sección siguiente para obtener información sobre la sintaxis y ejemplos. |
| `distance` |La función de puntuación de la distancia se usa para afectar a la puntuación de documentos en función de la cercanía o distancia respecto a una ubicación geográfica de referencia. La ubicación de referencia se proporciona como parte de la consulta en un parámetro (mediante el parámetro de consulta `scoringParameter` ) como argumento lon, lat. |
| `distance:referencePointParameter` |Parámetro que se pasarán en las consultas a usar como ubicación de referencia. scoringParameter es un parámetro de consulta. Consulte [Búsqueda de documentos](search-api-2015-02-28-preview.md#SearchDocs) para obtener descripciones de los parámetros de consulta. |
| `distance:boostingDistance` |Número que indica la distancia en kilómetros desde la ubicación de referencia donde finaliza el intervalo de potenciación. |
| `tag` |La función de puntuación de etiquetas se usa para afectar a la puntuación de documentos basados en las etiquetas de documentos y las consultas de búsqueda. Los documentos que tienen etiquetas en común con la consulta de búsqueda se potenciarán. Las etiquetas de la consulta de búsqueda se proporcionan como un parámetro de puntuación en cada solicitud de búsqueda (mediante el parámetro de consulta `scoringParameter` ). |
| `tag:tagsParameter` |Parámetro que se pasará en las consultas para especificar etiquetas para una solicitud en particular. `scoringParameter` es un parámetro de consulta. Consulte [Búsqueda de documentos](search-api-2015-02-28-preview.md#SearchDocs) para obtener descripciones de los parámetros de consulta. |
| `functionAggregation` |Opcional. Se aplica solo cuando se especifican las funciones. Entre los valores válidos se incluyen `sum` (predeterminado), `average`, `minimum`, `maximum` y `firstMatching`. Una puntuación de búsqueda es un valor único que se calcula a partir de varias variables, incluidas varias funciones. Estos atributos indican cómo se combinan la potenciación de todas las funciones en una única potenciación total que, a continuación, se aplica a la puntuación del documento base. La puntuación base se basa en el valor tf-idf calculado a partir del documento y la consulta de búsqueda. |
| `defaultScoringProfile` |Al ejecutar una solicitud de búsqueda, si no se especifica ningún perfil de puntuación, se usará la puntuación predeterminada (tf-idf solamente). Aquí puede establecerse un nombre de perfil de puntuación predeterminado, que provocará que la Búsqueda de Azure use ese perfil cuando no se proporcione ningún perfil específico en la solicitud de búsqueda. |

<a name="bkmk_interpolation"></a>

## <a name="set-interpolations"></a>Establecer interpolaciones
Las interpolaciones le permiten definir la pendiente con la que la potenciación de la puntuación aumenta desde el inicio del rango hasta su final. Es posible usar las siguientes interpolaciones:

* `Linear`: Para los elementos que están dentro del intervalo máximo y mínimo, la potenciación aplicada al elemento se realizará en un grado que va decreciendo de manera constante. Lineal es la interpolación predeterminada de un perfil de puntuación.
* `Constant`: En los elementos que se encuentran dentro del intervalo de inicio y finalización, se aplicará una potenciación constante a los resultados de la clasificación.
* `Quadratic`: En comparación con una interpolación lineal que tiene una potenciación en reducción constante, Cuadrático provocará inicialmente una reducción a un ritmo inferior y, a continuación, a medida que se aproxima el final del intervalo, se reduce a un intervalo muy superior. Esta opción de interpolación no se permite en funciones de puntuación de etiquetas.
* `Logarithmic`: En comparación con una interpolación lineal que tiene una potenciación en reducción constante, Logarítmico provocará inicialmente una reducción a un ritmo superior y, a continuación, a medida que se aproxima el final del intervalo, se reduce a un intervalo muy inferior. Esta opción de interpolación no se permite en funciones de puntuación de etiquetas.

<a name="Figure1"></a> ![][1]

<a name="bkmk_boostdur"></a>

## <a name="set-boostingduration"></a>Establecer boostingDuration
`boostingDuration` es un atributo de la función de índice de actualización. Se usa para establecer un período de caducidad después del que se detendrá la potenciación de un documento determinado. Por ejemplo, para potenciar una línea de productos o marca durante un período de promoción de 10 días, debe especificar el período de 10 días como "P10D" para dichos documentos. O bien, para elevar próximos eventos en la próxima semana, especifique "-P7D".

`boostingDuration` debe tener el formato de un valor "dayTimeDuration" XSD (subconjunto restringido de un valor de duración ISO 8601). El patrón de este es: `[-]P[nD][T[nH][nM][nS]]`.

La tabla siguiente proporciona varios ejemplos.

| Duración | boostingDuration |
| --- | --- |
| 1 día |"P1D" |
| 2 días, 12 horas |"P2DT12H" |
| 15 minutos |"PT15M" |
| 30 días, 5 horas, 10 minutos y 6,334 segundos |"P30DT5H10M6.334S" |

Para obtener más ejemplos, consulte [Esquema XML: tipos de datos (sitio web de W3.org)](http://www.w3.org/TR/xmlschema11-2/).

**Consulte también**
[API de REST del servicio Azure Search](http://msdn.microsoft.com/library/azure/dn798935.aspx) en MSDN <br/>
[Crear índice (API de Azure Search)](http://msdn.microsoft.com/library/azure/dn798941.aspx) en MSDN<br/>
[Agregar un perfil de puntuación a un índice de búsqueda](http://msdn.microsoft.com/library/azure/dn798928.aspx) en MSDN<br/>

<!--Image references-->
[1]: ./media/search-api-scoring-profiles-2015-02-28-Preview/scoring_interpolations.png
