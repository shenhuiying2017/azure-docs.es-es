<properties pageTitle="API de REST del Servicio Búsqueda de Azure versión 2014-10-20-Preview" description="API de REST del Servicio Búsqueda de Azure versión 2014-10-20-Preview" services="search" documentationCenter="" authors="HeidiSteen" manager="mblythe" editor="" />

<tags ms.service="search" ms.devlang="rest-api" ms.workload="search" ms.topic="article"  ms.tgt_pltfrm="na" ms.date="07/22/2015" ms.author="heidist" />

# API de REST del Servicio Búsqueda de Azure versión 2014-10-20-Preview #

En este documento se describe la versión preliminar **2014-10-20-Preview** de la API de REST del servicio Búsqueda de Azure, publicada como actualización de la versión preliminar pública de Búsqueda de Azure. Dado que esta versión quedará pronto obsoleta, se recomienda encarecidamente que use en su lugar la versión asociada a la versión de disponibilidad general. Para obtener instrucciones sobre migración de código, consulte [Transición de la versión preliminar a la versión de la API de disposición general](search-transition-from-preview.md).

Entre otros contenidos de la API relacionados con la versión **2014-10-20-Preview** se incluyen los siguientes:

- [Perfiles de puntuación (API de REST del servicios de Búsqueda de Azure: 2014-10-20-Preview)](search-api-scoring-profiles-2014-10-20-preview.md)

La documentación de la versión actual disponible con carácter general de la API de REST de Búsqueda de Azure puede encontrarse en MSDN. Consulte [API de REST del servicio Búsqueda de Azure](http://msdn.microsoft.com/library/azure/dn798935.aspx) para obtener más información.

##Acerca de la API de REST del servicio##

Búsqueda de Azure es un servicio basado en la nube que puede usar para crear aplicaciones de búsqueda personalizadas. Búsqueda de Azure tiene los conceptos de *Servicios de búsqueda* e *índices*, en los que un servicio de búsqueda contiene uno o más índices. El servicio de búsqueda se identifica mediante un nombre de dominio completo (por ejemplo: `mysearchservice.search.windows.net`). Cuando el servicio se aprovisiona, se genera una clave de API, y se usa para autenticar las solicitudes al servicio de Búsqueda de Azure.

Hay dos tipos de acciones que se pueden ejecutar en el servicio de Búsqueda de Azure:

- **Administración de índices**: aquí se incluyen tareas administrativas que se ejecutan en un servicio de búsqueda o en un índice de búsqueda.

- **Acciones de documentos**: estas acciones consultan y administran el corpus de un índice determinado.

Las API documentadas en esta sección proporcionan acceso a operaciones en los datos de búsqueda, como la creación y rellenado de índices, la carga de documentos y las consultas. Al llamar a la API, tenga en cuenta los puntos siguientes:

- Todas las API se definen en términos de solicitudes HTTP y respuestas en formato JSON de OData.

- Todas las API deben ir acompañadas de un `api-key` en el encabezado o en la cadena de consulta, como se describe en las notas siguientes.

- Todas las API deben emitirse a través de HTTPS (en el puerto predeterminado: 443).

- Todas las solicitudes de API deben incluir el parámetro de la cadena de consulta `api-version`. Su valor debe establecerse en la versión del servicio actual, que se muestra en el ejemplo siguiente:

    GET /indexes?api-version=2014-10-20-Preview

- Opcionalmente, todas las solicitudes de API pueden establecer el `Accept` encabezado HTTP. Si no se establece, el valor predeterminado se supone que será `application/json`.

Para la administración del servicio se proporciona una API independiente. Entre los ejemplos de operaciones de administración de servicios se incluye el aprovisionamiento del servicio o la capacidad de modificación. Para obtener más información acerca de esta API, consulte la API de REST de administración de Búsqueda de Azure.

### Extremo ###

El extremo de las operaciones de servicio es la dirección URL del servicio de Búsqueda de Azure aprovisionado por usted: https://*your-service-name*.search.windows.net.


### Versiones ###

Hay varias versiones de API para la Búsqueda de Azure. Consulte [Versiones del servicio de búsqueda](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener una lista de las versiones disponibles.


<a name="Authentication"></a>
### Autenticación y control de acceso###

La autenticación en un servicio de Búsqueda de Azure requiere dos fragmentos de información: una dirección URL del servicio de búsqueda y un `api-key`. Los `api-keys` se generan cuando se crea el servicio y pueden regenerarse a petición después de aprovisionar el servicio. Una `api-key` es siempre una de las siguientes:

- una clave de administración que concede acceso a todas las operaciones, incluidas las operaciones de escritura como crear y eliminar índices.
- una clave de consulta que autentica las solicitudes de solo lectura.

Puede tener 2 de claves de administración y hasta 50 claves de consulta por cada servicio. Tener 2 claves de administración resulta de utilidad cuando tiene que sustituir una de las claves.

Control de acceso está limitado a la administración de servicios a través de controles de acceso basado en roles (RBAC) proporcionados en el Portal de vista previa de Azure. Los roles se utilizan para establecer niveles de acceso para la administración de servicios. Por ejemplo, ver la clave de administración está restringido a los roles de Colaborador y Propietario, mientras que la visualización del estado del servicio es visible para los miembros de cualquier rol.

A las operaciones de datos realizadas en un extremo de servicio de búsqueda, incluidas la administración de índices, el rellenado del índice y las consultas, se obtiene acceso a través de `api-keys` exclusivamente. RBAC no se aplica al índice ni a operaciones relacionadas con documentos. Para obtener más información sobre `api-keys` o RBAC en Búsqueda de Azure, consulte [Administrar el servicio de búsqueda en Microsoft Azure](search-manage.md).

**Nota**: En general se considera una práctica de seguridad deficiente transmitir datos confidenciales como `api-key` en el URI de solicitud. Por este motivo, Búsqueda de Azure solo aceptará una clave de consulta como un `api-key` en la cadena de consulta, y debe evitar hacerlo a menos que el contenido del índice deba estar disponible públicamente. En su lugar, se recomienda pasar su `api-key` como un encabezado de solicitud.

###Resumen de API###

La API del servicio de Búsqueda de Azure admite dos sintaxis para la búsqueda de entidades: sintaxis de OData [simple](https://msdn.microsoft.com/library/dn798920.aspx) y alternativa (consulte [Compatibilidad con OData (API de Búsqueda de Azure)](http://msdn.microsoft.com/library/azure/dn798932.aspx) para obtener más información). La lista siguiente muestra la sintaxis simple.

[Crear índice](#CreateIndex)

    POST /indexes?api-version=2014-10-20-Preview

[Actualizar índice](#UpdateIndex)

    PUT /indexes/[index name]?api-version=2014-10-20-Preview

[Obtener índice](#GetIndex)

    GET /indexes/[index name]?api-version=2014-10-20-Preview

[Enumerar índices](#ListIndexes)

    GET /indexes?api-version=2014-10-20-Preview

[Obtención de estadísticas de índice](#GetIndexStats)

    GET /indexes/[index name]/stats?api-version=2014-10-20-Preview

[Eliminar un índice](#DeleteIndex)

    DELETE /indexes/[index name]?api-version=2014-10-20-Preview

[Agregar, eliminar y actualizar datos en un índice](#AddOrUpdateDocuments)

    POST /indexes/[index name]/docs/index?api-version=2014-10-20-Preview

[Buscar en documentos](#SearchDocs)

    GET /indexes/[index name]/docs?[query parameters]

[Buscar documento](#LookupAPI)

     GET /indexes/[index name]/docs/[key]?[query parameters]

[Documentos de recuento](#CountDocs)

    GET /indexes/[index name]/docs/$count?api-version=2014-10-20-Preview

[Sugerencias](#Suggestions)

    GET /indexes/[index name]/docs/suggest?[query parameters]

________________________________________
<a name="IndexOps"></a>
## Operaciones de índice

Puede crear y administrar índices en el servicio de Búsqueda de Azure a través de solicitudes HTTP sencillas (POST, GET, PUT, DELETE) en un recurso de índice determinado. Para crear un índice, primero debe PUBLICAR un documento JSON que describa el esquema de índice. El esquema define los campos de índice, sus tipos de datos y cómo pueden utilizarse (por ejemplo, en las búsquedas de texto completo, filtros, ordenación, faceting o sugerencias). También define los perfiles de puntuación, los proveedores de sugerencias y otros atributos para configurar el comportamiento del índice.

En el ejemplo siguiente se proporciona una ilustración de un esquema que se utiliza para buscar información sobre hoteles con el campo de descripción definido en dos idiomas. Observe de qué modo controlan los atributos cómo se utiliza el campo. Por ejemplo, el `hotelId` se utiliza como clave de documento (`"key": true`) y se excluye de búsquedas de texto completo (`"searchable": false`).

    {
    "name": "hotels",  
    "fields": [
      {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
      {"name": "baseRate", "type": "Edm.Double"},
      {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
	  {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, analyzer: "fr.lucene"},
      {"name": "hotelName", "type": "Edm.String"},
      {"name": "category", "type": "Edm.String"},
      {"name": "tags", "type": "Collection(Edm.String)"},
      {"name": "parkingIncluded", "type": "Edm.Boolean"},
      {"name": "smokingAllowed", "type": "Edm.Boolean"},
      {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
      {"name": "rating", "type": "Edm.Int32"},
      {"name": "location", "type": "Edm.GeographyPoint"}
     ],
     "suggesters": [
      {
       "name": "sg",
       "searchMode": "analyzingInfixMatching",
       "sourceFields": ["hotelName"]
      }
     ]
    }

Una vez creado el índice, podrá cargar documentos que rellenen el índice. Consulte [Agregar o actualizar documentos](#AddOrUpdateDocuments) para este siguiente paso.

Para obtener una introducción de vídeo sobre la indexación en Búsqueda de Azure, consulte el [episodio de portada de nube del canal 9 en Búsqueda de Azure](http://go.microsoft.com/fwlink/p/?LinkId=511509).


<a name="CreateIndex"></a>
## Crear índice ##

Puede crear un índice nuevo dentro de un servicio de Búsqueda de Azure mediante una solicitud HTTP POST: El cuerpo de la solicitud es un documento JSON que especifica el nombre del índice, campos, atributos para controlar el comportamiento de la consulta, puntuación de resultados, proveedores de sugerencias y opciones de CORS.

    POST https://[service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Como alternativa, puede usar PUT y especificar el nombre del índice en el URI. Si el índice no existe, se creará.

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]

**Nota**: El número máximo de índices permitido varía según el nivel de precios. El servicio gratuito permite hasta tres índices. El servicio estándar permite 50 índices por servicio de búsqueda. Consulte [Límites y restricciones](http://msdn.microsoft.com/library/azure/dn798934.aspx) para obtener detalles.

**Solicitud**

HTTPS es necesario para todas las solicitudes de servicio. La solicitud **Crear índice** se puede construir con un método POST o PUT. Al usar POST, debe proporcionar un nombre de índice en el cuerpo de la solicitud junto con la definición de esquema de índice. Con PUT, el nombre del índice es parte de la dirección URL. Si el índice no existe, se crea. Si ya existe, se actualiza a la nueva definición.

El nombre del índice debe estar en minúsculas, comenzar por una letra o un número, no tener ninguna barra o punto y tener menos de 128 caracteres. Después de iniciar el nombre del índice por una letra o un número, el resto del nombre puede incluir cualquier letra, número y guiones, siempre que los guiones no aparezcan de manera consecutiva.

`api-version` es obligatorio Entre los valores válidos se incluyen `2014-07-31-Preview` o `2014-10-20-Preview`. Puede especificar cuál desea usar en cada solicitud para obtener comportamientos específicos de la versión, pero como práctica recomendada, use la misma versión en todo el código. La versión recomendada es `2014-07-31-Preview` para uso general. También puede usar `2014-10-20-Preview` para evaluar las funciones experimentales, como la compatibilidad con analizadores de lenguaje expresados a través del atributo de índice del analizador. Consulte [Versiones del servicio de búsqueda](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener más información acerca de las versiones de API. Consulte [Compatibilidad con idiomas](#LanguageSupport) para obtener más información acerca de los analizadores de lenguaje.

**Encabezados de solicitud**

En la lista siguiente se describen los encabezados de solicitud obligatorios y opcionales.

- `Content-Type`: obligatorio. Establézcalo en `application/json`
- `api-key`: obligatorio. El `api-key` se usa para
- autenticar la solicitud al servicio de búsqueda. Es un valor de cadena único para el servicio. La solicitud **Crear índice** debe incluir un encabezado `api-key` establecido en su clave de administración (en lugar de una clave de consulta).

También necesitará el nombre del servicio para construir la dirección URL de la solicitud. Puede obtener el nombre de servicio y `api-key` desde el panel de servicio en el Portal de vista previa de Azure. Consulte [Introducción a Búsqueda de Azure](search-get-started.md) para obtener ayuda acerca de la navegación por páginas.

<a name="RequestData"></a> **Sintaxis del cuerpo de la solicitud**

El cuerpo de la solicitud contiene una definición de esquema, que incluye la lista de campos de datos de los documentos que se introducirán en este índice, tipos de datos, atributos, así como una lista opcional de perfiles de puntuación que se utilizan para puntuar documentos de coincidencias de puntuación en el momento de las consultas.

Tenga en cuenta que para una solicitud POST, debe especificar el nombre del índice en el cuerpo de la solicitud.

Solo puede haber un campo de clave en el índice. Debe ser un campo de cadena. Este campo representa el identificador único de cada documento almacenado en el índice.

La sintaxis para estructurar la carga de la solicitud es la siguiente. En este tema se proporciona una solicitud de ejemplo.

    {
      "name": (optional on PUT; required on POST) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "suggestions": true | false (default, only Edm.String and Collection(Edm.String) fields can be used for suggestions),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false,
		  "analyzer": "name of text analyzer"
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
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
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
			  "tag": {
				"tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }

Nota: El tipo de datos `Edm.Int64` se admite a partir de la versión de la API 2014-10-20-Preview.

**Atributos de índice**

Es posible establecer los siguientes atributos para crear un índice. Para obtener más información sobre la puntuación y los perfiles de puntuación, consulte [Perfiles de puntuación (API de REST del servicio Búsqueda de Azure: 2014-10-20-Preview)](search-api-scoring-profiles-2014-10-20-preview.md).

`name` : establece el nombre del campo.

`type` : establece el tipo de datos del campo. Consulte [Tipos de datos admitidos](#DataTypes) para obtener una lista de tipos admitidos.

`searchable`: marca el campo como de búsqueda de texto completo. Esto significa que se someterá a análisis como la separación de palabras durante la indexación. Si establece un campo `searchable` en un valor como "día soleado", internamente, se dividirá en los tokens individuales "soleado" y "día". Esto permite realizar búsquedas de texto completo de estos términos. Los campos de tipo `Edm.String` o `Collection(Edm.String)` son `searchable` de manera predeterminada. Los campos de otros tipos no pueden ser `searchable`.

  - **Nota**: Los campos `searchable` consumen espacio adicional en el índice, ya que Búsqueda de Azure almacenará una versión con tokens adicional del valor del campo para las búsquedas de texto completo. Si desea ahorrar espacio en el índice y no necesita incluir un campo en las búsquedas, establezca `searchable` en `false`.

`filterable`: permite hacer referencia al campo en consultas de `$filter`. `filterable` difiere de `searchable` en cómo se controlan las cadenas. Los campos de tipo `Edm.String` o `Collection(Edm.String)` que son `filterable` no sufren separación de palabras, por lo que las comparaciones son solo para las coincidencias exactas. Por ejemplo, si establece este campo `f` en "día soleado", `$filter=f eq 'sunny'` no encontrará ninguna coincidencia, pero `$filter=f eq 'sunny day'` sí. Todos los campos son `filterable` de forma predeterminada.

`sortable`: de forma predeterminada el sistema ordena los resultados por calificación, pero en muchas experiencias los usuarios desearán ordenar por los campos de los documentos. Los campos de tipo `Collection(Edm.String)` no pueden ser `sortable`. El resto de campos son `sortable` de forma predeterminada.

`facetable`: suele utilizarse en una presentación de resultados de búsqueda que incluya el número de resultados por categoría (por ejemplo, busque cámaras digitales y consulte los resultados divididos por marca, por megapíxeles, por precio, etc.). Esta opción no puede utilizarse con campos de tipo `Edm.GeographyPoint`. El resto de campos son `facetable` de forma predeterminada.

  - **Nota**: los campos de tipo `Edm.String` que son `filterable`, `sortable` o `facetable` solo pueden ocupar una longitud de 32 KB como máximo. Esto se debe a que esos campos se tratan como un término de búsqueda único y la longitud máxima de un término de Búsqueda de Azure es de 32 KB. Si necesita almacenar más texto que este en un campo de cadena único, deberá establecer explícitamente `filterable`, `sortable` y `facetable` en `false` en la definición del índice.

`suggestions`: establece si el campo se puede utilizar para autocompletar. Esto solo se puede establecer para campos del tipo `Edm.String` o `Collection(Edm.String)`. `suggestions` es `false` de forma predeterminada, puesto que requiere un espacio adicional en el índice. **Nota**: Use la `suggesters` propiedad introducida en la versión 2014-10-20-Preview en lugar de esta opción para obtener sugerencias. En una versión futura, la propiedad `suggestions` dejará de usarse para usar una especificación `suggesters` independiente.

  - **Nota**: Si un campo no tiene ninguno de los atributos anteriores establecidos en `true` (`searchable`, `filterable`, `sortable`, `facetable` o `suggestions`) el campo se excluirá eficazmente del índice invertido. Esta opción es útil para los campos que no se utilizan en las consultas, pero que son necesarios en los resultados de la búsqueda. La exclusión de esos campos del índice mejora el rendimiento.

`key`: marca el campo como que contiene identificadores únicos para los documentos del índice. Es necesario elegir exactamente un campo como campo `key` y debe ser de tipo `Edm.String`. Los campos de clave pueden usarse para buscar documentos directamente a través de la [API de búsqueda](#LookupAPI).

`retrievable`: establece si el campo se puede devolver un resultado de búsqueda. Esto resulta útil cuando desea usar un campo (por ejemplo, margen) como filtro, ordenación o mecanismo de puntuación, pero no desea que el campo sea visible para el usuario final. Este atributo debe ser `true` para los campos `key`.

`scoringProfiles`: define comportamientos de puntuación personalizados que permiten influir en los elementos que aparecen más arriba en los resultados de la búsqueda. Los perfiles de puntuación se componen de funciones y campos ponderados. Consulte [Perfiles de puntuación (API de REST del servicio Búsqueda de Azure: 2014-10-20-Preview)](search-api-scoring-profiles-2014-10-20-preview.md) para obtener más información acerca de los atributos utilizados en un perfil de puntuación.

`analyzer`: establece el nombre del analizador de texto que se utilizará para el campo. Para obtener información acerca del conjunto de valores permitido, consulte [Compatibilidad con idiomas](#LanguageSupport). Esta opción solo puede utilizarse con campos `searchable`. Una vez que se elige el analizador, no se podrá cambiar para el campo.


<a name="LanguageSupport"></a> **Compatibilidad con idioma**

Los campos localizables se someten a análisis que con frecuencia implican la separación de palabras, la normalización de texto y el filtrado de términos. De forma predeterminada, los campos localizables de la Búsqueda de Azure se analizan con el [Analizador Apache Lucene estándar](http://lucene.apache.org/core/4_9_0/analyzers-common/index.html) que divide el texto en elementos siguiendo las reglas de ["Segmentación de texto Unicode"](http://unicode.org/reports/tr29/). Además, el analizador estándar convierte todos los caracteres en minúsculas. Los documentos indexados y lo términos de búsqueda son sometidos a análisis durante la indexación y el procesamiento de consultas.

Búsqueda de Azure permite indexar los campos en una variedad de idiomas. Cada uno de esos idiomas requiere un analizador de texto no estándar que representa las características de un idioma determinado. Por ejemplo, el analizador de francés aplica un [lematizador de francés suave](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/fr/FrenchLightStemmer.html) para reducir palabras en sus [raíces de palabras](http://en.wikipedia.org/wiki/Stemming). Además, elimina las [omisiones](http://en.wikipedia.org/wiki/Elision) y las palabras no significativas del francés del texto analizado. El analizador de inglés amplía el analizador estándar. Elimina los posesivos (los ’s finales) de las palabras, aplica la lematización conforme al [Algoritmo de lematización Porter](http://tartarus.org/~martin/PorterStemmer/) y elimina las [palabras no significativas](http://en.wikipedia.org/wiki/Stop_words) del inglés.

El analizador puede configurarse por separado para cada campo en la definición del índice estableciendo la propiedad `analyzer`. Por ejemplo, puede tener campos separados para descripciones de hoteles en inglés, francés y español que existen en paralelo dentro del mismo índice. La consulta especifica qué campo específico del idioma devolver en las consultas de búsqueda.

A continuación se muestra la lista de analizadores admitidos junto con una breve descripción de sus características:

<table style="font-size:12">
    <tr>
		<th>Idioma</th>
		<th>Nombre del analizador</th>
		<th>Descripción</th>
	</tr>
    <tr>
		<td>Árabe</td>
		<td>ar.lucene</td>
		<td>
		<ul>
			<li>Implementa la normalización ortográfica árabe</li>
			<li>Aplica una lematización algorítmica suave</li>
			<li>Filtra las palabras no significativas del árabe</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Portugués brasileño</td>
		<td>pt-Br.lucene</td>
		<td>
		<ul>
			<li>Aplica una lematización suave</li>
			<li>Filtra las palabras irrelevantes del portugués brasileño</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Chino simplificado</td>
		<td>zh-Hans.lucene</td>
		<td>
		<ul>
			<li>Utiliza los modelos de conocimiento probabilístico para encontrar la segmentación de palabras óptima</li>
			<li>Filtra las palabras no significativas del chino</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Chino tradicional</td>
		<td>zh-Hant.lucene</td>
		<td>
		<ul>
			<li>Bigramas de índices (grupos superpuestos de dos caracteres chinos adyacentes)</li>
			<li>Normaliza las diferencias de ancho de caracteres</li>
		</ul>
		</td>
	<tr>
    <tr>
		<td>Checo</td>
		<td>cs.lucene</td>
		<td>
		<ul>
			<li>Aplica una lematización suave</li>
			<li>Filtra las palabras no significativas del checo</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Danés</td>
		<td>da.lucene</td>
		<td>
		<ul>
			<li>Aplica una lematización suave</li>
			<li>Filtra las palabras no significativas del danés</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Neerlandés</td>
		<td>nl.lucene</td>
		<td>
		<ul>
			<li>Aplica una lematización suave</li>
			<li>Filtra las palabras no significativas del neerlandés</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Alemán</td>
		<td>de.lucene</td>
		<td>
		<ul>
			<li>Aplica una lematización suave</li>
			<li>Filtra las palabras no significativas del alemán</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Griego</td>
		<td>el.lucene</td>
		<td>
		<ul>
			<li>Aplica una lematización suave</li>
			<li>Filtra las palabras no significativas del griego</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>English</td>
		<td>en.lucene</td>
		<td>
		<ul>
			<li>Aplica una lematización suave</li>
			<li>Filtra las palabras no significativas del inglés</li>
			<li>Elimina los posesivos</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Finés</td>
		<td>fi.lucene</td>
		<td>
		<ul>
			<li>Aplica una lematización suave</li>
			<li>Filtra las palabras no significativas del finés</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Francés</td>
		<td>fr.lucene</td>
		<td>
		<ul>
			<li>Aplica una lematización suave</li>
			<li>Filtra las palabras no significativas del francés</li>
			<li>Elimina las omisiones</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Hindi</td>
		<td>hi.lucene</td>
		<td>
		<ul>
			<li>Aplica una lematización suave</li>
			<li>Filtra las palabras no significativas del hindi</li>
			<li>Elimina algunas diferencias en las variaciones ortográficas</li>
			<li>Normaliza la representación Unicode de texto en idiomas de la India.</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Húngaro</td>
		<td>hu.lucene</td>
		<td>
		<ul>
			<li>Aplica una lematización suave</li>
			<li>Filtra las palabras no significativas del húngaro</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Indonesio (Bahasa)</td>
		<td>id.lucene</td>
		<td>
		<ul>
			<li>Aplica una lematización suave</li>
			<li>Filtra las palabras no significativas del indonesio</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Italiano</td>
		<td>it.lucene</td>
		<td>
		<ul>
			<li>Aplica una lematización suave</li>
			<li>Filtra las palabras no significativas del italiano</li>
			<li>Elimina las omisiones</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Japonés</td>
		<td>ja.lucene</td>
		<td>
		<ul>
			<li>Utiliza el análisis morfológico</li>
			<li>Normaliza las variaciones ortográficas de katakana comunes</li>
			<li>Eliminación suave de palabras/etiquetas no significativas</li>
			<li>Normalización de ancho de caracteres</li>
			<li>Lematización: reduce los adjetivos y verbos conjugados a su forma base</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Coreano</td>
		<td>ko.lucene</td>
		<td>
		<ul>
			<li>Bigramas de índices (grupos superpuestos de dos caracteres chinos adyacentes)</li>
			<li>Normaliza las diferencias de ancho de caracteres</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Letón</td>
		<td>lv.lucene</td>
		<td>
		<ul>
			<li>Aplica una lematización suave</li>
			<li>Filtra las palabras no significativas del letón</li>
		</ul>
		</td>
	</tr>

    <tr>
		<td>Noruego</td>
		<td>no.lucene</td>
		<td>
		<ul>
			<li>Aplica una lematización suave</li>
			<li>Filtra las palabras no significativas del noruego</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Polaco</td>
		<td>pl.lucene</td>
		<td>
		<ul>
			<li>Aplica una lematización algorítmica (Stempel)</li>
			<li>Filtra las palabras no significativas del polaco</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Portugués</td>
		<td>pt-Pt.lucene</td>
		<td>
		<ul>
			<li>Aplica una lematización suave</li>
			<li>Filtra las palabras no significativas del portugués</li>
		</ul>
		</td>
	</tr>

    <tr>
		<td>Rumano</td>
		<td>ro.lucene</td>
		<td>
		<ul>
			<li>Aplica una lematización suave</li>
			<li>Filtra las palabras no significativas del rumano</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Ruso</td>
		<td>ru.lucene</td>
		<td>
		<ul>
			<li>Aplica una lematización suave</li>
			<li>Filtra las palabras no significativas del ruso</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Español</td>
		<td>es.lucene</td>
		<td>
		<ul>
			<li>Aplica una lematización suave</li>
			<li>Filtra las palabras no significativas del español</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Sueco</td>
		<td>sv.lucene</td>
		<td>
		<ul>
			<li>Aplica una lematización suave</li>
			<li>Filtra las palabras no significativas del sueco</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Turco</td>
		<td>tr.lucene</td>
		<td>
		<ul>
			<li>Elimina todos los caracteres situados después de un apóstrofo (incluido el propio apóstrofo)</li>
			<li>Aplica una lematización suave</li>
			<li>Filtra las palabras no significativas del turco</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Tailandés</td>
		<td>th.lucene</td>
		<td>
		<ul>
			<li>Aplica una lematización suave</li>
			<li>Filtra las palabras no significativas del tailandés</li>
		</ul>
		</td>
	</tr>
	<td colspan="3">Además, Búsqueda de Azure proporciona las configuraciones del analizador de lenguaje válido</td>
    <tr>
		<td>Plegamiento de ASCII estándar</td>
		<td>standardasciifolding.lucene</td>
		<td>
		<ul>
			<li>Segmentación de texto Unicode (Tokenizer estándar)</li>
			<li>Filtro de plegamiento de ASCII: convierte los caracteres Unicode que no pertenecen al conjunto de los primeros 127 caracteres ASCII en sus valores equivalentes ASCII. Esto es útil para quitar los signos diacríticos.</li>
		</ul>
		</td>
	</tr>
</table>

Todos los analizadores con nombres anotados con <i>lucene</i> disponen de tecnología de [analizadores de idioma de Apache Lucene](http://lucene.apache.org/core/4_9_0/analyzers-common/overview-summary.html). Puede encontrar más información sobre el filtro de plegamiento de ASCII [aquí](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html).

**Opciones de CORS**

Javascript del lado cliente no puede llamar a las API de forma predeterminada debido a que el explorador evitará todas las solicitudes entre orígenes. Habilite CORS (uso compartido recursos entre orígenes) estableciendo el atributo `corsOptions` para que permita consultas de origen cruzado en su índice. Tenga en cuenta que solamente las API de consulta admiten CORS por motivos de seguridad. Se pueden establecer las opciones siguientes para CORS:

- `allowedOrigins` (obligatorio): se trata de una lista de orígenes a los que se le concederá acceso a su índice. Esto significa que cualquier código Javascript que se suministre desde esos orígenes podrá consultar el índice (suponiendo que proporcione la clave de API correcta). Cada origen suele ser de formato `protocol://fully-qualified-domain-name:port`, aunque a menudo se omite el puerto. Consulte [este artículo](http://go.microsoft.com/fwlink/?LinkId=330822) para obtener más detalles.
 - Si desea permitir el acceso a todos los orígenes, incluya `*` como elemento único en la matriz `allowedOrigins`. Tenga en cuenta que **esta no es una práctica recomendada para los servicios de búsqueda de producción.** Sin embargo, puede ser útil para el desarrollo o con fines de depuración.
- `maxAgeInSeconds` (opcional): los exploradores usan este valor para determinar la duración (en segundos) para almacenar en la memoria caché las respuestas preparatorias de CORS. Esto debe ser un entero no negativo. Cuanto mayor sea este valor es, mejor será el rendimiento, pero más tiempo tardarán en surtir efecto los cambios en la directiva CORS. Si no se establece, se usará una duración predeterminada de 5 minutos.

<a name="Suggesters"></a> **Proveedores de sugerencias**

Los proveedores de sugerencias permiten la función de completar automáticamente en las búsquedas. Normalmente las cadenas de búsqueda parcial se envían a las API de sugerencias mientras el usuario está escribiendo y la API devuelve un conjunto de frases sugeridas.

Búsqueda de Azure está realizando la transición a una nueva API de proveedores de sugerencias. La versión 2014-07-31-Preview tenía una API más estrecha para obtener sugerencias en la que podía marcarse un campo mediante `"suggestions": true` y, a continuación, se podrían realizar sugerencias de prefijo para cadenas cortas (3-25 caracteres). A partir de versión 2014-10-20-Preview, Búsqueda de Azure tiene una versión más eficaz de sugerencias basadas en "proveedores de sugerencias", tal y como se describe en esta sección. Esta nueva implementación puede buscar coincidencias de prefijo e infijo y tiene una mejor tolerancia a errores en las cadenas de búsqueda. A partir de versión 2014-10-20-Preview se recomienda el uso de la nueva API de proveedores de sugerencias.

La compatibilidad con proveedores de sugerencias actual funciona mejor cuando se usa para sugerir documentos específicos en lugar de términos o frases flexibles. Resultan buenos campos de origen válida para este tipo de proveedores de sugerencias los títulos, nombres y demás frases relativamente cortas que pueden identificar un elemento. Como ejemplos de tipos de campos que tienden a ser menos efectivos están los campos muy repetitivos como categorías y etiquetas o campos muy largos como campos de descripciones o comentarios.

Como parte de la definición del índice puede agregar un único proveedor de sugerencias a la colección `suggesters`. Las propiedades que definen a un proveedor de sugerencias son:

- `name`: el nombre del proveedor de sugerencias. Use el nombre del proveedor de sugerencias para llamar a la API de `suggest`.
- `searchMode`: la estrategia que se usa para buscar las frases candidatas. El único modo que se admite actualmente es `analyzingInfixMatching`, que establece una correspondencia flexible de frases al principio o en medio de las oraciones.
- `sourceFields`: lista de uno o más campos que son el origen del contenido para obtener sugerencias. Solo los campos de tipo `Edm.String` y `Collection(Edm.String)` pueden ser orígenes para obtener sugerencias. En la versión de vista previa actual de los proveedores de sugerencias solo pueden usarse los campos que no tienen un analizador personalizado establecido.

Actualmente solo tiene un proveedor de sugerencias en las colecciones de proveedores de sugerencias de la versión actual de la API.

<a name="CreateUpdateIndexExample"></a> **Ejemplo de cuerpo de solicitud**

    {
      "name": "hotels",  
      "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
	    {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, analyzer="fr.lucene"},
        {"name": "hotelName", "type": "Edm.String"},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean"},
        {"name": "smokingAllowed", "type": "Edm.Boolean"},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["hotelName"]
        }
      ]
    }

**Respuesta**

Para obtener una solicitud correcta: "201 Creado".

De forma predeterminada, el cuerpo de la respuesta contendrá el JSON de la definición del índice que se creó. Si se establece el encabezado de la solicitud `Prefer` en `return=minimal`, el cuerpo de respuesta quedará vacío y el código de estado correcto será "204 Sin contenido" en lugar de "201 creado". Esto es cierto independientemente de si se utiliza PUT o POST para crear el índice.

<a name="UpdateIndex"></a>
## Actualizar índice ##

Puede actualizar un índice existente en Búsqueda de Azure mediante una solicitud HTTP PUT. En la vista previa pública, las actualizaciones pueden incluir agregar nuevos campos al esquema existente, modificar las opciones de CORS y modificar perfiles de puntuación (consulte [Perfiles de puntuación (API de REST del servicio Búsqueda de Azure: 2014-10-20-Preview](search-api-scoring-profiles-2014-10-20-preview.md)). Especifique el nombre del índice que se va a actualizar en el URI de solicitud:

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Importante:** la compatibilidad con las actualizaciones del esquema de índice se limita a las operaciones que no requieren volver a generar el índice de búsqueda. Actualmente no se admiten las actualizaciones del esquema que requieran volver a indexar, como el cambio de tipos de campo. Pueden agregarse nuevos campos en cualquier momento, aunque no se pueden modificar ni eliminar los campos existentes. Lo mismo se aplica a los proveedores de sugerencias; es posible agregar nuevos campos a un proveedor de sugerencias en el momento en que se agregan los campos, pero no se puede quitar campos de proveedores de sugerencias y no se pueden agregar campos existentes a los proveedores de sugerencias.

Al agregar un nuevo campo a un índice, todos los documentos existentes en el índice tendrá un valor null para ese campo automáticamente. No se consumirá espacio de almacenamiento adicional hasta que se agreguen nuevos documentos al índice.

**Solicitud**

HTTPS es necesario para todas las solicitudes de servicio. La solicitud **Actualizar índice** se construye utilizando HTTP PUT. Con PUT, el nombre del índice es parte de la dirección URL. Si el índice no existe, se crea. Si ya existe el índice, se actualiza a la nueva definición.

El nombre del índice debe estar en minúsculas, comenzar por una letra o un número, no tener ninguna barra o punto y tener menos de 128 caracteres. Después de iniciar el nombre del índice por una letra o un número, el resto del nombre puede incluir cualquier letra, número y guiones, siempre que los guiones no aparezcan de manera consecutiva.

El parámetro `api-version` es obligatorio. Entre los valores válidos se incluyen `2014-07-31-Preview` o `2014-10-20-Preview`. Puede especificar cuál desea usar en cada solicitud para obtener comportamientos específicos de la versión, pero como práctica recomendada, use la misma versión en todo el código. La versión recomendada es `2014-07-31-Preview` para uso general. También puede usar `2014-10-20-Preview` para evaluar las funciones experimentales. Consulte [Versiones del servicio de búsqueda](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener más información.

**Encabezados de solicitud**

En la lista siguiente se describen los encabezados de solicitud obligatorios y opcionales.

- `Content-Type`: obligatorio. Establézcalo en `application/json`
- `api-key`: obligatorio. `api-key` se usa para autenticar la solicitud en su servicio de búsqueda. Es un valor de cadena único para el servicio. La solicitud **Actualizar índice** debe incluir un encabezado `api-key` establecido en su clave de administración (en lugar de una clave de consulta).

También necesitará el nombre del servicio para construir la dirección URL de la solicitud. Puede obtener el nombre de servicio y `api-key` desde el panel de servicio en el Portal de vista previa de Azure. Consulte [Introducción a Búsqueda de Azure](search-get-started.md) para obtener ayuda acerca de la navegación por páginas.

**Sintaxis del cuerpo de la solicitud**

Al actualizar un índice existente, el cuerpo debe incluir la definición de esquema original, además de los nuevos campos que se van a agregar, así como los perfiles de puntuación modificados, los proveedores de sugerencias y las opciones de CORS, si existen. Si no va a modificar los perfiles de puntuación y las opciones de CORS, deberá incluir los originales de cuando se creó el índice. En general, el mejor patrón que se puede utilizar para las actualizaciones consiste en recuperar la definición del índice con un comando GET, modificarlo y luego actualizarlo con PUT.

A continuación se reproduce la sintaxis del esquema usada para crear un índice por motivos de comodidad. Consulte [Crear índice](#CreateIndex) para obtener más detalles.

    {
      "name": (optional) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "suggestions": true | false (default, only Edm.String and Collection(Edm.String) fields can be used for suggestions),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false,
		  "analyzer": "name of text analyzer"
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
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
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
			  "tag": {
				"tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }

Nota: El tipo de datos `Edm.Int64` se admite a partir de la versión de la API 2014-10-20-Preview.

**Respuesta**

Para obtener una solicitud correcta: "204 Sin contenido".

De forma predeterminada, el cuerpo de respuesta estará vacío. No obstante, si el encabezado de la solicitud `Prefer` está establecido en `return=representation`, el cuerpo de la respuesta contendrá el JSON de la definición del índice que se actualizó. En este caso, el código de estado correcto será "200 Correcto".

<a name="ListIndexes"></a>
## Índices de la lista ##

La operación **Índices de la lista** devuelve una lista de los índices que se encuentran actualmente en el servicio de Búsqueda de Azure.

    GET https://[service name].search.windows.net/indexes?api-version=[api-version]
    api-key: [admin key]

**Solicitud**

HTTPS es necesario para todas las solicitudes de servicio. La solicitud **Índices de la lista** puede crearse mediante el método GET.

El parámetro `api-version` es obligatorio. Entre los valores válidos se incluyen `2014-07-31-Preview` o `2014-10-20-Preview`. Puede especificar cuál desea usar en cada solicitud para obtener comportamientos específicos de la versión, pero como práctica recomendada, use la misma versión en todo el código. La versión recomendada es `2014-07-31-Preview` para uso general. También puede usar `2014-10-20-Preview` para evaluar las funciones experimentales. Consulte [Versiones del servicio de búsqueda](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener más información.

**Encabezados de solicitud**

En la lista siguiente se describen los encabezados de solicitud obligatorios y opcionales.

- `api-key`: obligatorio. `api-key` se usa para autenticar la solicitud en su servicio de búsqueda. Es un valor de cadena único para el servicio. La solicitud **Índices de la lista** debe incluir un `api-key` establecido en una clave de administración (en lugar de una clave de consulta).

También necesitará el nombre del servicio para construir la dirección URL de la solicitud. Puede obtener el nombre de servicio y `api-key` desde el panel de servicio en el Portal de vista previa de Azure. Consulte [Introducción a Búsqueda de Azure](search-get-started.md) para obtener ayuda acerca de la navegación por páginas.

**Cuerpo de la solicitud**

Ninguno.

**Respuesta**

Código de estado: al obtener una respuesta correcta, se visualiza 200 Correcto.

A continuación se proporciona un cuerpo de respuesta de ejemplo:

    {
      "value": [
        {
          "name": "Books",
          "fields": [
            {"name": "ISBN", ...},
            ...
          ]
        },
        {
          "name": "Games",
          ...
        },
        ...
      ]
    }

Tenga en cuenta que puede filtrar la respuesta a solo las propiedades que le interesan. Por ejemplo, si solamente desea una lista de los nombres de índices, use la opción de consulta OData `$select`:

    GET /indexes?api-version=2014-10-20-Preview&$select=name

En este caso, la respuesta del ejemplo anterior podría aparecer como sigue:

    {
      "value": [
        {"name": "Books"},
        {"name": "Games"},
        ...
      ]
    }

Se trata de una técnica útil para ahorrar ancho de banda, si tiene una gran cantidad de índices en el servicio de búsqueda.

<a name="GetIndex"></a>
## Obtener índice ##

La operación **Obtener índice** obtiene la definición del índice de Búsqueda de Azure.

    GET https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Solicitud**

HTTPS es necesario para las solicitudes de servicio. La solicitud **Obtener índices** puede crearse mediante el método GET.

El [nombre de índice] del URI de la solicitud especifica qué índice se va a obtener de la colección de índices.

El parámetro `api-version` es obligatorio. Entre los valores válidos se incluyen `2014-07-31-Preview` o `2014-10-20-Preview`. Puede especificar cuál desea usar en cada solicitud para obtener comportamientos específicos de la versión, pero como práctica recomendada, use la misma versión en todo el código. La versión recomendada es `2014-07-31-Preview` para uso general. También puede usar `2014-10-20-Preview` para evaluar las funciones experimentales. Consulte [Versiones del servicio de búsqueda](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener más información.

**Encabezados de solicitud**

En la lista siguiente se describen los encabezados de solicitud obligatorios y opcionales.

- `api-key`: `api-key` se usa para autenticar la solicitud en su servicio de búsqueda. Es un valor de cadena único para el servicio. La solicitud **Obtener índice** debe incluir un `api-key` establecido en una clave de administración (en lugar de una clave de consulta).

También necesitará el nombre del servicio para construir la dirección URL de la solicitud. Puede obtener el nombre de servicio y `api-key` desde el panel de servicio en el Portal de vista previa de Azure. Consulte [Introducción a Búsqueda de Azure](search-get-started.md) para obtener ayuda acerca de la navegación por páginas.

**Cuerpo de la solicitud**

Ninguno.

**Respuesta**

Código de estado: al obtener una respuesta correcta, se visualiza 200 Correcto.

Consulte el JSON de ejemplo en [creación y actualización de un índice](#CreateUpdateIndexExample) para obtener un ejemplo de la carga de respuesta.

<a name="DeleteIndex"></a>
## Eliminar índice ##

La operación **Eliminar índice** quita un índice y los documentos asociados del servicio de Búsqueda de Azure. Puede obtener el nombre del índice del panel de servicio en el Portal de vista previa de Azure o de la API. Consulte [Índices de la lista](#ListIndexes) para obtener más información.

    DELETE https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Solicitud**

HTTPS es necesario para las solicitudes de servicio. La solicitud **Eliminar índice** puede crearse mediante el método DELETE.

El [nombre de índice] del URI de la solicitud especifica qué índice se va a eliminar de la colección de índices.

El parámetro `api-version` es obligatorio. Entre los valores válidos se incluyen `2014-07-31-Preview` o `2014-10-20-Preview`. Puede especificar cuál desea usar en cada solicitud para obtener comportamientos específicos de la versión, pero como práctica recomendada, use la misma versión en todo el código. La versión recomendada es `2014-07-31-Preview` para uso general. También puede usar `2014-10-20-Preview` para evaluar las funciones experimentales. Consulte [Versiones del servicio de búsqueda](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener más información.

**Encabezados de solicitud**

En la lista siguiente se describen los encabezados de solicitud obligatorios y opcionales.

- `api-key`: obligatorio. `api-key` se usa para autenticar la solicitud en su servicio de búsqueda. Es un valor de cadena, único en su URL de servicio. La solicitud **Eliminar índice** debe incluir un encabezado `api-key` establecido en su clave de administración (en lugar de una clave de consulta).

También necesitará el nombre del servicio para construir la dirección URL de la solicitud. Puede obtener el nombre de servicio y `api-key` desde el panel de servicio en el Portal de vista previa de Azure. Consulte [Introducción a Búsqueda de Azure](search-get-started.md) para obtener ayuda acerca de la navegación por páginas.

**Cuerpo de la solicitud**

Ninguno.

**Respuesta**

Código de estado: al obtener una respuesta correcta, se visualiza 204 Sin contenido.

<a name="GetIndexStats"></a>
## Obtención de estadísticas de índice ##

La operación **Obtener estadísticas de índice** obtiene de Búsqueda de Azure un recuento de documentos para el índice actual, más el uso del almacenamiento.

	GET https://[service name].search.windows.net/indexes/[index name]/stats?api-version=[api-version]
    api-key: [admin key]

**Solicitud**

HTTPS es necesario para todas las solicitudes de servicio. La solicitud **Obtener estadísticas de índices** puede crearse mediante el método GET.

El [nombre de índice] del URI de la solicitud indica al servicio que devuelva estadísticas de índice para el índice especificado.

El parámetro `api-version` es obligatorio. Entre los valores válidos se incluyen `2014-07-31-Preview` o `2014-10-20-Preview`. Puede especificar cuál desea usar en cada solicitud para obtener comportamientos específicos de la versión, pero como práctica recomendada, use la misma versión en todo el código. La versión recomendada es `2014-07-31-Preview` para uso general. También puede usar `2014-10-20-Preview` para evaluar las funciones experimentales. Consulte [Versiones del servicio de búsqueda](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener más información.

**Encabezados de solicitud**

En la lista siguiente se describen los encabezados de solicitud obligatorios y opcionales.

- `api-key`: `api-key` se usa para autenticar la solicitud en su servicio de búsqueda. Es un valor de cadena único para el servicio. La solicitud **Obtener estadísticas de índice** debe incluir un `api-key` establecido en una clave de administración (en lugar de una clave de consulta).

También necesitará el nombre del servicio para construir la dirección URL de la solicitud. Puede obtener el nombre de servicio y `api-key` desde el panel de servicio en el Portal de vista previa de Azure. Consulte [Introducción a Búsqueda de Azure](search-get-started.md) para obtener ayuda acerca de la navegación por páginas.

**Cuerpo de la solicitud**

Ninguno.

**Respuesta**

Código de estado: al obtener una respuesta correcta, se visualiza 200 Correcto.

El cuerpo de la respuesta está en el formato siguiente:

    {
      "documentCount": number,
	  "storageSize": number (size of the index in bytes)
    }

________________________________________
<a name="DocOps"></a>
## Operaciones del documento

En Búsqueda de Azure, se almacena un índice en la nube y se rellena con documentos JSON que se cargan en el servicio. Todos los documentos que se cargan comprenden el corpus de los datos de búsqueda. Los documentos contienen campos, algunos de los cuales se acortan en términos de búsqueda cuando se cargan. El segmento de URL `/docs` de la API de Búsqueda de Azure representa la colección de documentos en un índice. Todas las operaciones realizadas en la colección, como cargar, combinar, eliminar o consultar documentos se producen en el contexto de un índice único, por lo que las direcciones URL de estas operaciones siempre se iniciarán mediante `/indexes/[index name]/docs` para un nombre de índice especificado.

El código de aplicación debe generar documentos JSON para cargar en la búsqueda de Azure. Normalmente, los índices se rellenan desde un único conjunto de datos que suministre.

Debe planear disponer de un documento para cada elemento que desee buscar. Una aplicación de alquiler de películas puede disponer de un documento por película, una aplicación de escaparate podría tener un documento por SKU, una aplicación de software con fines pedagógicos en línea podría tener un documento por curso, una empresa de investigación podría tener un documento para cada documento académico de su repositorio, y así sucesivamente.

Los documentos constan de uno o varios campos. Los campos pueden contener texto acortado por Búsqueda de Azure a términos de búsqueda, así como los valores no acortados o que no sean de texto que pueden utilizarse en filtros o perfiles de puntuación. Los nombres, tipos de datos y funciones de búsqueda admitidos para cada campo están determinados por el esquema de índice. Uno de los campos de cada esquema de índice debe designarse como identificador, y cada documento debe tener un valor para el campo de identificador que identifica ese documento de manera única en el índice. Todos los demás campos de documento son opcionales y se establecerán de manera predeterminada en un valor null si no se especifican. Tenga en cuenta que los valores null no ocupan espacio en el índice de búsqueda.

Para poder cargar documentos, debe haber creado el índice en el servicio. Consulte [Crear índice](#CreateIndex) para obtener más información acerca de este primer paso.

**Nota**: esta versión de la API ofrece la búsqueda de texto completo solo en inglés.

<a name="AddOrUpdateDocuments"></a>
## Agregar, actualizar o eliminar documentos ##

Puede cargar, combinar, combinar o cargar o eliminar documentos en un índice especificado mediante HTTP POST. Para números elevados de actualizaciones, se recomienda efectuar el procesamiento por lotes de documentos (hasta 1.000 documentos por lote o aproximadamente 16 MB por lote).

    POST https://[service name].search.windows.net/indexes/[index name]/docs/index?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Solicitud**

HTTPS es necesario para todas las solicitudes de servicio. Puede cargar, combinar, combinar o cargar o eliminar documentos en un índice especificado mediante HTTP POST.

El URI de solicitud incluye eI [nombre de índice], y especifica en qué índice publicar los documentos. Solo se pueden publicar documentos en un índice de cada vez.

El parámetro `api-version` es obligatorio. Entre los valores válidos se incluyen `2014-07-31-Preview` o `2014-10-20-Preview`. Puede especificar cuál desea usar en cada solicitud para obtener comportamientos específicos de la versión, pero como práctica recomendada, use la misma versión en todo el código. La versión recomendada es `2014-07-31-Preview` para uso general. También puede usar `2014-10-20-Preview` para evaluar las funciones experimentales. Consulte [Versiones del servicio de búsqueda](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener más información.

**Encabezados de solicitud**

En la lista siguiente se describen los encabezados de solicitud obligatorios y opcionales.

- `Content-Type`: obligatorio. Establézcalo en `application/json`
- `api-key`: obligatorio. `api-key` se usa para autenticar la solicitud en su servicio de búsqueda. Es un valor de cadena único para el servicio. La solicitud **Agregar documentos** debe incluir un encabezado `api-key` establecido en su clave de administración (en lugar de una clave de consulta).

También necesitará el nombre del servicio para construir la dirección URL de la solicitud. Puede obtener el nombre de servicio y `api-key` desde el panel de servicio en el Portal de vista previa de Azure. Consulte [Introducción a Búsqueda de Azure](search-get-started.md) para obtener ayuda acerca de la navegación por páginas.

**Cuerpo de la solicitud**

El cuerpo de la solicitud contiene uno o más documentos para indexar. Los documentos se identifican mediante una clave única. Cada documento está asociado a una acción: carga, combinación, combinación o carga o eliminación. Las solicitudes de carga deben incluir los datos del documento como un conjunto de pares de clave/valor.

    {
      "value": [
        {
          "@search.action": "upload (default) | merge | mergeOrUpload | delete",
          "key_field_name": "unique_key_of_document", (key/value pair for key field from index schema)
          "field_name": field_value (key/value pairs matching index schema)
            ...
        },
        ...
      ]
    }

**Acciones de documentos**

- `upload`: una acción de carga es similar a un "upsert" donde se insertará el documento si es nuevo y se actualizará/reemplazará si existe. Tenga en cuenta que se reemplazarán todos los campos en el caso de la actualización.
- `merge`: la combinación actualiza un documento existente con los campos especificados. Si el documento no existe, se producirá un error en la combinación. Cualquier campo que se especifica en una combinación reemplazará al campo existente en el documento. Aquí se incluyen los campos de tipo `Collection(Edm.String)`. Por ejemplo, si el documento contiene un campo "etiquetas" con el valor `["budget"]` y ejecuta una combinación con el valor `["economy", "pool"]` para "etiquetas", el valor final del campo "etiquetas" será `["economy", "pool"]`. **No** será `["budget", "economy", "pool"]`.
- `mergeOrUpload`: se comporta como `merge` si ya existe un documento con la clave especificada en el índice. Si el documento no existe, se comporta como `upload` con un nuevo documento.
- `delete`: la eliminación quita el documento especificado del índice. Tenga en cuenta que puede especificar solo el valor del campo de clave en una operación `delete`. Si se intenta especificar otros campos se producirá un error HTTP 400. Si desea quitar un campo individual de un documento, use `merge` en su lugar y simplemente establezca el campo explícitamente en `null`.

**Respuesta**

Código de estado: se obtendrá 200 Correcto con una respuesta correcta, lo que significa que todos los elementos se han indexado correctamente (como se indica en el campo "status" establecido en true para todos los elementos):

    {
      "value": [
        {
          "key": "unique_key_of_document",
          "status": true,
          "errorMessage": null
        }
      ]
    }  

Código de estado: se obtendrá 207 cuando no se haya indexado correctamente al menos un elemento (tal y como se indica en el campo "status" establecido en false para los elementos que no se hayan indexado):

    {
      "value": [
        {
          "key": "unique_key_of_document",
          "status": false,
          "errorMessage": "The search service is too busy to process this document. Please try again later."
        }
      ]
    }  

La propiedad `errorMessage` indicará el motivo del error de indexación si es posible.

**Nota**: Si el código de cliente encuentra con frecuencia una respuesta 207, una razón posible es que el sistema está bajo carga. Puede confirmar esto comprobando la propiedad `errorMessage`. En tal caso, es recomendable ***limitar solicitudes de indexación***. De lo contrario, si el tráfico de indexación de tráfico no se reduce, es posible que el sistema comience a rechazar todas las solicitudes mediante errores 503.

Código de estado: 429 indica que se ha superado la cuota del número de documentos por índice. Debe crear un nuevo índice o actualizar para obtener límites de capacidad superiores.

**Ejemplo:**

    {
      "value": [
        {
          "@search.action": "upload",
          "hotelId": "1",
          "baseRate": 199.0,
          "description": "Best hotel in town",
		  "description_fr": "Meilleur hôtel en ville",
          "hotelName": "Fancy Stay",
		  "category": "Luxury",
          "tags": ["pool", "view", "wifi", "concierge"],
          "parkingIncluded": false,
		  "smokingAllowed": false,
          "lastRenovationDate": "2010-06-27T00:00:00Z",
          "rating": 5,
          "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
          "@search.action": "upload",
          "hotelId": "2",
          "baseRate": 79.99,
          "description": "Cheapest hotel in town",
	      "description_fr": "Hôtel le moins cher en ville",
          "hotelName": "Roach Motel",
		  "category": "Budget",
          "tags": ["motel", "budget"],
          "parkingIncluded": true,
		  "smokingAllowed": true,
          "lastRenovationDate": "1982-04-28T00:00:00Z",
          "rating": 1,
          "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
          "@search.action": "merge",
          "hotelId": "3",
          "baseRate": 279.99,
          "description": "Surprisingly expensive",
          "lastRenovationDate": null
        },
        {
          "@search.action": "delete",
          "hotelId": "4"
        }
      ]
    }
________________________________________
<a name="SearchDocs"></a>
## Buscar en documentos ##

Las operaciones de **búsqueda** se emiten como solicitudes GET y especifica parámetros de consulta que ofrecen los criterios necesarios para seleccionar los documentos coincidentes.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
    api-key: [admin key]

**Solicitud**

HTTPS es necesario para las solicitudes de servicio. La solicitud **Búsqueda** puede crearse mediante el método GET.

El URI de solicitud especifica qué índice consultar para todos los documentos que coinciden con los parámetros de consulta.

**Parámetros de consulta**

`search=[string]` (opcional): el texto que se debe buscar. Se busca en los campos `searchable` de forma predeterminada a menos que se especifique `searchFields`. Al realizar búsquedas en campos `searchable`, se limita el propio texto de la búsqueda, por lo que los distintos términos pueden separarse mediante un espacio en blanco (por ejemplo: `search=hello world`). Para encontrar un término, use `*` (esto puede ser útil para las consultas de filtro booleano). Omitir este parámetro tiene el mismo efecto que establecerlo en `*`. Para obtener información específica sobre la sintaxis de búsqueda, vea [Sintaxis de consulta simple](https://msdn.microsoft.com/library/dn798920.aspx).

  - **Nota**: Los resultados a veces pueden ser sorprendentes al consultar sobre campos `searchable`. El tokenizer incluye una lógica para controlar los casos comunes en texto en inglés como apóstrofos, comas en números, etc. Por ejemplo, `search=123,456` hallará el único término 123,456 en lugar de los términos individuales 123 y 456, ya que en los números grandes en inglés se usan comas como separadores de miles. Por este motivo, se recomienda usar espacios en blanco en lugar de signos de puntuación para separar los términos en el parámetro `search`.

`searchMode=any|all` (opcional, tiene como valor predeterminado `any`): si alguno o todos los términos de búsqueda deben coincidir con el fin de contar el documento como una coincidencia.

`searchFields=[string]` (opcional): la lista separada por comas de nombres de campo para buscar el texto especificado. Los campos de destino deben estar marcados como `searchable`.

`moreLikeThis=[key]` (opcional): busca documentos que son similares al documento especificado por la clave del documento. Se considera el contenido de los campos `searchable` de forma predeterminada a menos que se especifique `searchFields`. Esta opción no se puede usar en una consulta que contenga el parámetro de búsqueda de texto 'search=[string]'.

`$skip=#` (opcional): el número de resultados de búsqueda que se omiten; no puede ser superior a 100.000. Si necesita examinar documentos en secuencia pero no puede usar `$skip` debido a esta limitación, utilice `$orderby` en una clave totalmente ordenada y `$filter` con una consulta por rango en su lugar.

`$top=#` (opcional): número de resultados de búsqueda para recuperar. El valor predeterminado es 50. Si se especifica un valor superior a 1.000 y hay más de 1.000 resultados, solo se devolverán los 1.000 primeros resultados, junto con un vínculo a la página siguiente de resultados (consulte `@odata.nextLink` en [el ejemplo siguiente](#SearchResponse)).

`$count=true|false` (opcional, tiene como valor predeterminado `false`): si se va a obtener el número total de resultados. Establecer este valor en `true` puede afectar al rendimiento. Tenga en cuenta que el número devuelto será una aproximación.

`$orderby=[string]` (opcional): lista de expresiones separadas por comas por la que ordenar los resultados. Cada expresión puede ser un nombre de campo o una llamada a la función `geo.distance()`. Cada expresión puede ir seguida de `asc` para indicar el orden ascendente y de `desc` para indicar el orden descendente. El valor predeterminado es ascendente. Los empates se resolverán por la puntuación de coincidencia de los documentos. Si no se especifica ningún `$orderby`, el orden predeterminado será descendente por puntuación de coincidencia del documento. Hay un límite de 32 cláusulas para `$orderby`.

`$select=[string]` (opcional): lista de campos separados por comas para recuperar. Si no se especifica nada, se incluirán todos los campos marcados como recuperables en el esquema. También se pueden solicitar explícitamente todos los campos estableciendo este parámetro en `*`.

`facet=[string]` (cero o más): un campo por el que establecer facetas. Es posible que la cadena contenga parámetros para personalizar la faceta expresada como pares `name:value` separados por comas. Los parámetros válidos son:

- `count` (número máximo de términos de faceta; el valor predeterminado es 10). No hay ningún máximo, pero los valores más altos incurren en una penalización de rendimiento correspondiente, especialmente si el campo con facetas contiene un gran número de términos únicos.
  - Por ejemplo: `facet=category,count:5` obtiene las cinco categorías principales en los resultados de la faceta.  
  - **Nota**: Si el parámetro `count` es menor que el número de términos únicos, es posible que los resultados no sean precisos. Esto es debido a la manera en que se distribuyen las consultas de facetas entre las particiones. Aumentar `count` generalmente aumenta la precisión de los recuentos de términos, pero ello afecta al rendimiento.
- `sort` (uno de `count` para ordenar de manera *descendente* por número, `-count` para ordenar de manera *ascendente* por número, `value` para ordenar de manera *ascendente* por valor o `-value` para ordenar de manera *descendente* por valor)
  - Por ejemplo: `facet=category,count:3,sort:count` obtiene las tres categorías principales en los resultados de la faceta en orden descendente por el número de documentos con el nombre de cada ciudad. Por ejemplo, si las tres categorías principales son Presupuesto, Motel y Lujo, y Presupuesto tiene 5 resultados, Motel tiene 6 y Lujo tiene 4, a continuación, los depósitos se colocarán en el orden siguiente: Motel, Presupuesto, Lujo.
  - Por ejemplo: `facet=rating,sort:-value` genera depósitos para todas las clasificaciones posibles en orden descendente por valor. Por ejemplo, si las clasificaciones son de 1 a 5, los depósitos se ordenarán como 5, 4, 3, 2, 1 independientemente de cuántos documentos coincidan con cada clasificación.
- `values` (valores numéricos delimitados por canalización o `Edm.DateTimeOffset` que especifican un conjunto dinámico de valores de entrada de faceta)
  - Por ejemplo: `facet=baseRate,values:10|20` genera tres depósitos: uno para la tarifa base 0 hasta, pero sin incluir la tarifa 10, uno para 10 hasta pero sin incluir 20 y uno para 20 o superiores.
  - Por ejemplo: `facet=lastRenovationDate,values:2010-02-01T00:00:00Z` genera dos depósitos: uno para hoteles reformados antes de febrero de 2010 y otro para hoteles reformados desde el 1 de febrero de 2010 en adelante.
- `interval` (intervalo de número entero mayor que 0 para números, o `minute`, `hour`, `day`, `week`, `month`, `quarter`, `year` para los valores de fecha y hora)
  - Por ejemplo: `facet=baseRate,interval:100` genera depósitos basados en intervalos de tarifas base de tamaño de 100. Por ejemplo, si las tarifas base se encuentran entre 60 y 600 dólares, habrá depósitos para 0-100, 100-200, 300 200, 300-400, 400-500 y 500-600.
  - Por ejemplo: `facet=lastRenovationDate,interval:year` genera un depósito para cada año en que se han reformado los hoteles.
- **Nota**: `count` y `sort` se pueden combinar en la misma especificación de faceta, pero no se pueden combinar con `interval` o `values`, y `interval` y `values` no se pueden combinar entre sí.

`$filter=[string]` (opcional): expresión de búsqueda estructurada en la sintaxis estándar de OData. Consulte [Sintaxis de expresiones de OData](#ODataExpressionSyntax) para obtener detalles sobre el subconjunto de la gramática de expresiones de OData que admite la Búsqueda de Azure.

`highlight=[string]` (opcional): conjunto de nombres de campos delimitado por comas usado para los resaltados de referencias. Solo se pueden usar `searchable` campos para resaltar las referencias.

  `highlightPreTag=[string]` (opcional, se establece de forma predeterminada en `<em>`): una etiqueta de cadena que se antepone al resaltado de referencias. Debe establecerse con `highlightPostTag`. Los caracteres reservados en la dirección URL deben estar codificados con porcentaje (por ejemplo, %23 en vez de #).

  `highlightPostTag=[string]` (opcional, se establece de forma predeterminada en `</em>`): una etiqueta de cadena que se antepone al resaltado de referencias. Debe establecerse con `highlightPreTag`. Los caracteres reservados en la dirección URL deben estar codificados con porcentaje (por ejemplo, %23 en vez de #).

`scoringProfile=[string]` (opcional): nombre de un perfil de puntuación para evaluar puntuaciones de coincidencias de documentos coincidentes con el fin de ordenar los resultados.

`scoringParameter=[string]` (cero o más): indica el valor para cada parámetro definido en una función de puntuación (por ejemplo, `referencePointParameter`) con el formato nombre: valor. Por ejemplo, si el perfil de puntuación define una función con un parámetro denominado "mylocation" la opción de cadena de consulta sería & scoringParameter = mylocation:-122.2,44.8

`api-version=[string]` (obligatorio). Entre los valores válidos se incluyen `2014-07-31-Preview` o `2014-10-20-Preview`. Puede especificar cuál desea usar en cada solicitud para obtener comportamientos específicos de la versión, pero como práctica recomendada, use la misma versión en todo el código. La versión recomendada es `2014-07-31-Preview` para uso general. También puede usar `2014-10-20-Preview` para evaluar las funciones experimentales. Consulte [Versiones del servicio de búsqueda](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener más información.

Nota: Para esta operación, `api-version` se especifica como parámetro de consulta.

**Encabezados de solicitud**

En la lista siguiente se describen los encabezados de solicitud obligatorios y opcionales.

- `api-key`: `api-key` se usa para autenticar la solicitud en su servicio de búsqueda. Es un valor de cadena, único en su URL de servicio. La solicitud de **Búsqueda** puede especificar una clave de administración o una clave de consulta para `api-key`.

También necesitará el nombre del servicio para construir la dirección URL de la solicitud. Puede obtener el nombre de servicio y `api-key` desde el panel de servicio en el Portal de vista previa de Azure. Consulte [Introducción a Búsqueda de Azure](search-get-started.md) para obtener ayuda acerca de la navegación por páginas.

**Cuerpo de la solicitud**

Ninguno.

<a name="SearchResponse"></a> **Respuesta**

Código de estado: al obtener una respuesta correcta, se visualiza 200 Correcto.

    {
      "@odata.count": # (if $count=true was provided in the query),
      "@search.facets": { (if faceting was specified in the query)
        "facet_field": [
          {
            "value": facet_entry_value (for non-range facets),
            "from": facet_entry_value (for range facets),
            "to": facet_entry_value (for range facets),
            "count": number_of_documents
          }
        ],
        ...
      },
      "value": [
        {
          "@search.score": document_score (if a text query was provided),
          "@search.highlights": {
            field_name: [ subset of text, ... ],
            ...
          },
          key_field_name: document_key,
          field_name: field_value (retrievable fields or specified projection),
          ...
        },
        ...
      ],
      "@odata.nextLink": (URL to fetch the next page of results if $top is greater than 1000)
    }

**Ejemplos:**

Puede encontrar ejemplos adicionales en la página [Sintaxis de expresiones de OData para la Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn798921.aspx).

1) Busque en el índice por fecha en orden descendente.

    GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2014-10-20-Preview

NOTA: La precisión de los campos de fecha y hora se limita a milisegundos. Si se inserta una marca de tiempo que especifica valores más pequeños (por ejemplo, observe la parte de segundos de esta marca de tiempo: 10:30:09.7552052), el valor obtenido se redondeará (o 10:30:09.7550000 según el ejemplo).

(2) En una búsqueda con facetas, busque en el índice y recupere las facetas de categorías, clasificación, etiquetas, así como elementos con baseRate en intervalos específicos:

    GET /indexes/hotels/docs?search=test&facet=category&facet=rating&facet=tags&facet=baseRate,values:80|150|220&api-version=2014-10-20-Preview

3) Utilizando un filtro, restrinja los resultados de la consulta con facetas anterior después de que el usuario haga clic en la tarifa 3 y en la categoría "Motel":

    GET /indexes/hotels/docs?search=test&facet=tags&facet=baseRate,values:80|150|220&$filter=rating eq 3 and category eq 'Motel'&api-version=2014-10-20-Preview

4) En una búsqueda con facetas, establezca un límite superior en términos únicos devueltos en una consulta. El valor predeterminado es 10, pero se puede aumentar o disminuir este valor utilizando el parámetro `count` en el atributo `facet`:

    GET /indexes/hotels/docs?search=test&facet=city,count:5&api-version=2014-10-20-Preview

5) Busque en el índice en campos específicos; por ejemplo, un campo específico del idioma:

    GET /indexes/hotels/docs?search=hôtel&searchFields=description_fr&api-version=2014-10-20-Preview

6) Busque en el índice en varios campos. Por ejemplo, puede almacenar y consultar los campos de búsqueda en varios idiomas, todo ello en el mismo índice. Si las descripciones de inglés y francés coexisten en el mismo documento, puede devolver cualquiera en los resultados de la consulta:

	GET /indexes/hotels/docs?search=hotel&searchFields=description,description_fr&api-version=2014-10-20-Preview

Tenga en cuenta que solo puede consultar un índice de cada vez. No cree varios índices para cada idioma a menos que planee consultar una de cada vez.

7) Paginación: obtenga la primera página de los elementos (el tamaño de la página es 10):

    GET /indexes/hotels/docs?search=*&$skip=0&$top=10&api-version=2014-10-20-Preview

8) Paginación: obtenga la segunda página de los elementos (el tamaño de la página es 10):

    GET /indexes/hotels/docs?search=*&$skip=10&$top=10&api-version=2014-10-20-Preview

9) Recupere un conjunto específico de campos:

    GET /indexes/hotels/docs?search=*&$select=hotelName,description&api-version=2014-10-20-Preview

10) Recupere documentos que coincidan con una expresión de consulta específica

    GET /indexes/hotels/docs?$filter=(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'&api-version=2014-10-20-Preview

11) Busque en el índice y obtenga fragmentos con resaltado de referencias

    GET /indexes/hotels/docs?search=something&highlight=description&api-version=2014-10-20-Preview

12) Busque en el índice y obtenga documentos ordenados de más próximos a más alejados de una ubicación de referencia

    GET /indexes/hotels/docs?search=something&$orderby=geo.distance(location, geography'POINT(-122.12315 47.88121)')&api-version=2014-10-20-Preview

13) Busque en el índice suponiendo que hay un perfil de puntuaciones denominado "geográfico" con dos funciones de puntuación de distancia, una para definir un parámetro llamado "currentLocation" y otra para definir un parámetro llamado "lastLocation"

    GET /indexes/hotels/docs?search=something&scoringProfile=geo&scoringParameter=currentLocation:-122.123,44.77233&scoringParameter=lastLocation:-121.499,44.2113&api-version=2014-10-20-Preview


<a name="LookupAPI"></a>
##Buscar documento##

La operación **Buscar documento** permite recuperar un documento de Búsqueda de Azure. Esto resulta útil cuando un usuario hace clic en un resultado de búsqueda específico y desea buscar detalles específicos acerca de ese documento.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/[key]?[query parameters]
    api-key: [admin key]

**Solicitud**

HTTPS es necesario para las solicitudes de servicio. La solicitud **Buscar documento** se puede generar del modo indicado a continuación.

    GET /indexes/[index name]/docs/key?[query parameters]

También puede usar la sintaxis de OData tradicional para la búsqueda de claves:

    GET /indexes('[index name]')/docs('[key]')?[query parameters]

El URI de solicitud incluye un [nombre de índice] y una [clave], que especifica qué documento para recuperar del índice correspondiente. Solamente se puede obtener un documento de cada vez. Utilice **Búsqueda** para obtener varios documentos en una única solicitud.

**Parámetros de consulta**

`$select=[string]` (opcional): lista de campos separados por comas para recuperar. Si no se especifica nada o se establece en `*`, se incluirán en la proyección todos los campos marcados como recuperables en el esquema.

`api-version=[string]` (obligatorio). Entre los valores válidos se incluyen `2014-07-31-Preview` o `2014-10-20-Preview`. Puede especificar cuál desea usar en cada solicitud para obtener comportamientos específicos de la versión, pero como práctica recomendada, use la misma versión en todo el código. La versión recomendada es `2014-07-31-Preview` para uso general. También puede usar `2014-10-20-Preview` para evaluar las funciones experimentales. Consulte [Versiones del servicio de búsqueda](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener más información.

Nota: Para esta operación, `api-version` se especifica como parámetro de consulta.

**Encabezados de solicitud**

En la lista siguiente se describen los encabezados de solicitud obligatorios y opcionales.

- `api-key`: `api-key` se usa para autenticar la solicitud en su servicio de búsqueda. Es un valor de cadena, único en su URL de servicio. La solicitud **Buscar documento** puede especificar una clave de administración o una clave de consulta para `api-key`.

También necesitará el nombre del servicio para construir la dirección URL de la solicitud. Puede obtener el nombre de servicio y `api-key` desde el panel de servicio en el Portal de vista previa de Azure. Consulte [Introducción a Búsqueda de Azure](search-get-started.md) para obtener ayuda acerca de la navegación por páginas.

**Cuerpo de la solicitud**

Ninguno.

**Respuesta**

Código de estado: al obtener una respuesta correcta, se visualiza 200 Correcto.

    {
      field_name: field_value (fields matching the default or specified projection)
    }

**Ejemplo**

Busque el documento que tiene la clave "2"

    GET /indexes/hotels/docs/2?api-version=2014-10-20-Preview

Busque el documento que tiene la clave "3" con la sintaxis de OData:

    GET /indexes('hotels')/docs('3')?api-version=2014-10-20-Preview

<a name="CountDocs"></a>
##Documentos de recuento##

La operación **Documentos de recuento** recupera un recuento del número de documentos en un índice de búsqueda. La sintaxis `$count` forma parte del protocolo OData.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/$count?api-version=[api-version]
    Accept: text/plain
    api-key: [admin key]

**Solicitud**

HTTPS es necesario para las solicitudes de servicio. La solicitud **Documentos de recuento** puede crearse mediante el método GET.

El [nombre de índice] del URI de la solicitud indica al servicio que devuelva un recuento de todos los elementos de la colección de documentos del índice especificado.

El parámetro `api-version` es obligatorio. Entre los valores válidos se incluyen `2014-07-31-Preview` o `2014-10-20-Preview`. Puede especificar cuál desea usar en cada solicitud para obtener comportamientos específicos de la versión, pero como práctica recomendada, use la misma versión en todo el código. La versión recomendada es `2014-07-31-Preview` para uso general. También puede usar `2014-10-20-Preview` para evaluar las funciones experimentales. Consulte [Versiones del servicio de búsqueda](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener más información.

**Encabezados de solicitud**

En la lista siguiente se describen los encabezados de solicitud obligatorios y opcionales.

- `Accept`: este valor debe establecerse en `text/plain`.
- `api-key`: `api-key` se usa para autenticar la solicitud en su servicio de búsqueda. Es un valor de cadena, único en su URL de servicio. La solicitud **Documentos de recuento** puede especificar una clave de administración o una clave de consulta para `api-key`.

También necesitará el nombre del servicio para construir la dirección URL de la solicitud. Puede obtener el nombre de servicio y `api-key` desde el panel de servicio en el Portal de vista previa de Azure. Consulte [Introducción a Búsqueda de Azure](search-get-started.md) para obtener ayuda acerca de la navegación por páginas.

**Cuerpo de la solicitud**

Ninguno.

**Respuesta**

Código de estado: al obtener una respuesta correcta, se visualiza 200 Correcto.

El cuerpo de la respuesta contiene el valor de recuento como un entero con formato de texto sin formato.

<a name="Suggestions"></a>
##Sugerencias##

La operación **Sugerencias** recupera sugerencias basadas en la entrada de búsqueda parcial. Se suele usar en los cuadros de búsqueda para proporcionar sugerencias anticipadas cuando los usuarios están introduciendo términos de búsqueda.

Las solicitudes de sugerencias están destinadas a sugerir documentos de destino, por lo que el texto sugerido puede repetirse si varios documentos candidatos coinciden con la misma entrada de búsqueda. Puede usar `$select` para recuperar otros campos del documento (incluida la clave del documento) para que pueda indicar qué documento es el origen para cada sugerencia.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/suggest?[query parameters]
    api-key: [admin key]


**Solicitud**

HTTPS es necesario para las solicitudes de servicio. La solicitud **Sugerencias** puede crearse mediante el método GET.

El URI de la solicitud especifica el nombre del índice que se consulta. También incluye el término de búsqueda introducido parcialmente en la cadena de consulta.

**Parámetros de consulta**

`search=[string]`: texto de búsqueda que se utiliza para sugerir las consultas. Debe contener entre 3 y 25 caracteres.

`highlightPreTag=[string]` (opcional): una etiqueta de cadena que se antepone a resultados de búsquedas. Debe establecerse con `highlightPostTag`. Los caracteres reservados en la dirección URL deben estar codificados con porcentaje (por ejemplo, %23 en vez de #).

`highlightPostTag=[string]` (opcional): una etiqueta de cadena que se adjunta a resultados de búsquedas. Debe establecerse con `highlightPreTag`. Los caracteres reservados en la dirección URL deben estar codificados con porcentaje (por ejemplo, %23 en vez de #).

`suggesterName=[string]` -(opcional) el nombre del proveedor de sugerencias tal y como se especifica en la colección `suggesters` que forma parte de la definición del índice. Si no se utiliza esta opción, las sugerencias se basan en la implementación de la versión anterior que funciona en los campos marcados con `"suggestions": true` y sólo admite el establecimiento de coincidencias de prefijo.

`fuzzy=[boolean]` (opcional, valor predeterminado = false): si se establece en true esta API encontrará sugerencias incluso si hay un carácter sustituido o no encontrado en el texto de búsqueda. Si bien esto proporciona una mejor experiencia en algunos escenarios, ello afecta al rendimiento, ya que las búsquedas de sugerencias aproximadas son más lentas y consumen más recursos.

`searchFields=[string]` (opcional): lista separada por comas de nombres de campo para buscar el texto de búsqueda especificado. Los campos de destino deben estar habilitados para obtener sugerencias.

`$top=#` (opcional, valor predeterminado = 5): número de sugerencias para recuperar. Debe ser un número entre 1 y 100.

`$filter=[string]` (opcional): expresión que filtra los documentos que se consideran para obtener sugerencias.

`$orderby=[string]` (opcional): lista de expresiones separadas por comas por la que ordenar los resultados. Cada expresión puede ser un nombre de campo o una llamada a la función `geo.distance()`. Cada expresión puede ir seguida de `asc` para indicar el orden ascendente y de `desc` para indicar el orden descendente. El valor predeterminado es ascendente. Hay un límite de 32 cláusulas para `$orderby`.

`$select=[string]` (opcional): lista de campos separados por comas para recuperar. Si no se especifica, solo se devolverá la clave del documento y el texto de la sugerencia.

`api-version=[string]` (obligatorio). Entre los valores válidos se incluyen `2014-07-31-Preview` o `2014-10-20-Preview`. Puede especificar cuál desea usar en cada solicitud para obtener comportamientos específicos de la versión, pero como práctica recomendada, use la misma versión en todo el código. La versión recomendada es `2014-07-31-Preview` para uso general. También puede usar `2014-10-20-Preview` para evaluar las funciones experimentales. Consulte [Versiones del servicio de búsqueda](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener más información.

Nota: Para esta operación, `api-version` se especifica como parámetro de consulta.

**Encabezados de solicitud**

En la lista siguiente se describen los encabezados de solicitud obligatorios y opcionales.

- `api-key`: `api-key` se usa para autenticar la solicitud en su servicio de búsqueda. Es un valor de cadena, único en su URL de servicio. La solicitud **Sugerencias** puede especificar una clave de administración o una clave de consulta como `api-key`.

  También necesitará el nombre del servicio para construir la dirección URL de la solicitud. Puede obtener el nombre de servicio y `api-key` desde el panel de servicio en el Portal de vista previa de Azure. Consulte [Introducción a Búsqueda de Azure](search-get-started.md) para obtener ayuda acerca de la navegación por páginas.

**Cuerpo de la solicitud**

Ninguno.

**Respuesta**

Código de estado: al obtener una respuesta correcta, se visualiza 200 Correcto.

    {
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
        },
        ...
      ]
    }

Si se utiliza la opción de proyección para recuperar campos que están incluidos en cada elemento de la matriz:

    {
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
          <other projected data fields>
        },
        ...
      ]
    }

 **Ejemplo**

Recupere 5 sugerencias en las que la entrada de búsqueda parcial sea "lux"

    GET /indexes/hotels/docs/suggest?search=lux&$top=5&suggesterName=sg&api-version=2014-10-20-Preview

<!---HONumber=August15_HO6-->