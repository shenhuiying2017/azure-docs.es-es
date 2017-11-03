---
title: Analizadores en Azure Search | Microsoft Docs
description: "Asigne analizadores a los campos de texto que permiten búsquedas de un índice para reemplazar la API Lucene estándar personalizada por otras alternativas personalizadas, predefinidas o específicas del lenguaje."
services: search
manager: jhubbard
author: HeidiSteen
documentationcenter: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 09/11/2017
ms.author: heidist
ms.openlocfilehash: 1b9dea2978c11955da3ea4df8b90dc10a866d3f1
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="analyzers-in-azure-search"></a>Analizadores de Azure Search

Un *analizador* es un componente de la [búsqueda de texto completo](search-lucene-query-architecture.md) responsable del procesamiento de texto en las cadenas de consulta y de los documentos indexados. Las siguientes transformaciones son típicas durante el análisis:

+ Se quitan las palabras no esenciales (palabras irrelevantes) y los signos de puntuación.
+ Las frases y las palabras con guiones se dividen en las partes que los componen.
+ Las palabras en mayúsculas se ponen en minúsculas.
+ Las palabras se reducen a sus formas raíz, con el fin de que se puedan encontrar coincidencias independientemente del tiempo verbal.

Los analizadores lingüísticos convierten una entrada de texto en formularios primitivos o raíz que son eficaces para la recuperación y el almacenamiento de información. La conversión se produce durante la indexación, cuando se crea el índice y, de nuevo, durante la búsqueda cuando se lee el índice. Es más probable que obtenga los resultados de búsqueda que espera si usa el mismo analizador de texto para ambas operaciones.

Azure Search usa el [analizador Estándar Lucene](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) como predeterminado. Puede invalidar el valor predeterminado campo por campo. En este artículo se describe el intervalo de opciones y se ofrecen procedimientos recomendados para el análisis personalizado. También se proporcionan configuraciones de ejemplo para escenarios clave.

## <a name="supported-analyzers"></a>Analizadores compatibles

En la lista siguiente se describen los analizadores compatibles con Azure Search.

| Categoría | Descripción |
|----------|-------------|
| [Analizador Lucene estándar](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Predeterminada. No se requieren ninguna especificación ni configuración. Este analizador de uso general funciona bien en la mayoría de lenguajes y escenarios.|
| Analizadores predefinidos | Se ofrece como un producto acabado diseñado para utilizarse tal cuál, con una poca personalización. <br/>Hay dos tipos: especializados y de lenguaje. Lo que hace que sean "predefinidos" es que se hace referencia a ellos por su nombre, sin personalización alguna. <br/><br/>Los [analizadores especializados (independientes del lenguaje)](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable) se usan cuando las entradas de texto requieren procesamiento especializado o un procesamiento mínimo. Los analizadores predefinidos sin lenguaje incluyen **Asciifolding**, **Keyword**, **Pattern**, **Simple**, **Stop** y **Whitespace**.<br/><br/>Los [analizadores de lenguaje](https://docs.microsoft.com/rest/api/searchservice/language-support) se utilizan cuando se necesita compatibilidad lingüística enriquecida con lenguajes individuales. Azure Search admite 35 analizadores de lenguaje de Lucene y 50 analizadores de procesamiento de lenguaje natural de Microsoft. |
|[Analizadores personalizados](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Una configuración definida por el usuario de una combinación de los elementos existentes, que consta de un tokenizador (obligatorio) y filtros opcionales (char o token).|

Puede personalizar un analizador predefinido, como **Pattern** o **Stop**, para que use otras opciones que se documentan en [Predefined Analyzers Reference](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable) (Referencia de analizadores predefinidos). Solo algunos de los analizadores predefinidos tienen opciones que se pueden configurar. Como en cualquier personalización, asigne un nombre a la configuración nueva, como *myPatternAnalyzer* para distinguirlo del analizador Lucene Pattern.

## <a name="how-to-specify-analyzers"></a>Especificación de analizadores

1. Solo para analizadores personalizados, cree una sección de **analizador** en la definición de índice. Para más información, consulte [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) (Creación de un índice) y también [Create a custom analyzer](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search#create-a-custom-analyzer) (Creación de un analizador personalizado).

2. En una [definición de campo](https://docs.microsoft.com/rest/api/searchservice/create-index) del índice, establezca la propiedad **analyzer** en el nombre de un analizador de destino (por ejemplo, `"analyzer" = "keyword"`. Los valores válidos incluyen el nombre de un analizador predefinido, un analizador del lenguaje o un analizador personalizado también definido en el esquema de índice.

3. Opcionalmente, en lugar de una propiedad **analyzer**, puede establecer diferentes analizadores para la indexación y la consulta mediante los parámetros de campo **indexAnalyzer** y **searchAnalyzer`**. 

3. La adición de un analizador a una definición de campo provoca una operación de escritura en el índice. Si agrega un **analizador** a un índice existente, observe los siguientes pasos:
 
 | Escenario | Impacto | Pasos |
 |----------|--------|-------|
 | Adición de un nuevo campo | mínimo | Si el campo no existe todavía en el esquema, no hay ninguna revisión del campo que realizar porque el campo no tiene todavía una presencia física en el índice. Use [Actualizar índice](https://docs.microsoft.com/rest/api/searchservice/update-index) y [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) para esta tarea.|
 | Agregue un analizador a un campo indexado ya existente. | recompilar | El índice invertido para ese campo debe volver a crearse desde el principio y se debe volver a indexar el contenido de esos campos. <br/> <br/>En el caso de índices en desarrollo activo, [elimine](https://docs.microsoft.com/rest/api/searchservice/delete-index) y [cree](https://docs.microsoft.com/rest/api/searchservice/create-index) el índice para que recoja la nueva definición del campo. <br/> <br/>En el caso de índices en producción, debe crear un nuevo campo para proporcionar la definición revisada y empezar a usarlo. Use [Actualizar índice](https://docs.microsoft.com/rest/api/searchservice/update-index) y [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) para incorporar el nuevo campo. Más adelante, como parte de un mantenimiento planeado del índice, puede limpiarlo para quitar campos obsoletos. |

## <a name="tips-and-best-practices"></a>Sugerencias y procedimientos recomendados

En esta sección se proporcionan consejos para trabajar con los analizadores.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Un analizador para lectura y escritura, salvo que tenga requisitos específicos

Azure Search le permite especificar diferentes analizadores para la indexación y búsqueda a través de parámetros de campo `indexAnalyzer` y `searchAnalyzer` adicionales. Si no se especifica, el analizador establecido con la propiedad `analyzer` se utiliza para la indexación y la búsqueda. Si `analyzer` no se especifica, se utiliza el analizador Lucene estándar predeterminado.

Una regla general es usar el mismo analizador de para los índices y las consultas, a menos que los requisitos específicos indiquen lo contrario. Asegúrese de realizar pruebas exhaustivas. Si el procesamiento de texto es diferente en el momento de la búsqueda y la indexación, corre el riesgo de que se produzca una falta de coincidencia entre los términos de la consulta y los términos indexados si las configuraciones del analizador de búsqueda y el de indexación no están alineadas.

### <a name="test-during-active-development"></a>Prueba durante el desarrollo activo

El reemplazo del analizador estándar requiere que se reconstruya el índice. Si es posible, decida qué analizadores se van a usar durante el desarrollo activo antes de poner un índice en producción.

### <a name="inspect-tokenized-terms"></a>Inspección de términos con tokens

Si se produce un error en una búsqueda para devolver los resultados esperados, el escenario más probable es que se creen discrepancias de tokens entre las entradas de término en la consulta y términos con tokens en el índice. Si los tokens no son los mismos, las coincidencias no pueden materializarse. Para inspeccionar la salida del tokenizador recomendamos usar la [API de análisis](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) como herramienta de investigación. La respuesta consta de los tokens que genera un analizador concreto.

### <a name="compare-english-analyzers"></a>Comparación de los analizadores en inglés

La [versión de demostración de Search Analyzer](http://alice.unearth.ai/) es una aplicación de demostración de terceros que muestra una comparación del analizador de Lucene estándar, el analizador del lenguaje para inglés de Lucene y el procesador de lenguaje natural para inglés de Microsoft. El índice es fijo: contiene texto de un caso popular. Para cada entrada de búsqueda que proporcione, se muestran los resultados de cada analizador en paneles adyacentes, lo que le ofrece una idea de la forma en la que cada analizador procesa la misma cadena. 

## <a name="examples"></a>Ejemplos

Los ejemplos siguientes muestran las definiciones del analizador en algunos escenarios claves.

<a name="Example1"></a>
### <a name="example-1-custom-options"></a>Ejemplo 1: opciones personalizadas

Este ejemplo ilustra una definición del analizador con opciones personalizadas. Las opciones personalizadas de los filtros de char, tokenizadores y filtros de token se especifican por separado como construcciones con nombre y, después, se hace referencia a ellas en la definición del analizador. Los elementos predefinidos se usan tal cual y se hace referencia a ellos por su nombre.

Descripción de este ejemplo:

* Los analizadores son una propiedad de la clase de campo de un campo que permite la búsqueda.
* Un analizador personalizado forma parte de una definición de índice. Su personalización puede ser escasa (por ejemplo, la personalización de una sola opción de un filtro) o puede estar personalizado en varios lugares.
* En este caso, el analizador personalizado es "my_analyzer", que a su vez utiliza un tokenizador estándar personalizado, "my_standard_tokenizer", y dos filtros de token: lowercase y el filtro de asciifolding personalizado "my_asciifolding".
* También define un filtro char personalizado "map_dash" que reemplaza todos los guiones por caracteres de subrayado antes de tokenización (el tokenizador estándar se para en el guión, pero no en el carácter de subrayado).

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizer",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
~~~~

<a name="Example2"></a>
### <a name="example-2-override-the-default-analyzer"></a>Ejemplo 2: reemplazar el analizador predeterminado

El analizador estándar es el predeterminado. Imagine que desea reemplazar el analizador predeterminado por otro analizador predefinido, como el analizador de patrón. Si no va a establecer opciones personalizadas, basta con que lo especifique por nombre en la definición del campo.

El elemento "analizador" reemplaza el analizador estándar campo a campo. No se produce un reemplazo global. En este ejemplo, `text1` utiliza el analizador de patrón y `text2`, que no especifica un analizador, usa el predeterminado.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
~~~~

<a name="Example3"></a>
### <a name="example-3-different-analyzers-for-indexing-and-search-operations"></a>Ejemplo 3: analizadores diferentes para las operaciones de indexación y búsqueda

Las API incluyen atributos de índice adicionales para especificar diferentes analizadores para las operaciones de indexación y búsqueda. Los atributos `searchAnalyzer` y `indexAnalyzer` deben especificarse como un par, en lugar del atributo `analyzer` individual.


~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
~~~~

<a name="Example4"></a>
### <a name="example-4-language-analyzer"></a>Ejemplo 4: analizador del lenguaje

Los campos que contienen cadenas en diferentes idiomas pueden utilizar un analizador del lenguaje, mientras que otros campos conservan el predeterminado (o usan otro analizador predefinido o personalizado). Si utiliza un analizador del lenguaje, debe hacerlo tanto para las operaciones de indexación como para las de búsqueda. Los campos que usan un analizador del lenguaje no pueden tener un analizador para la indexación y otro para la búsqueda.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
~~~~

## <a name="next-steps"></a>Pasos siguientes

+ Revise la explicación detallada de [Cómo funciona la búsqueda de texto completo en Azure Search](search-lucene-query-architecture.md). En este artículo se usan ejemplos que explican comportamientos que, a simple vista, podrían parecer contradictorios.

+ Probar la sintaxis de consulta adicional de la sección de ejemplo [Buscar documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents#examples) o desde [Sintaxis de consulta simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) en el explorador de búsqueda en el portal.

+ Obtener información sobre cómo aplicar [analizadores léxicos específicos del idioma](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Configure analizadores personalizados](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) para un procesamiento mínimo o un procesamiento especializado en los campos individuales.

+ [Compare los analizador estándar y de inglés](http://alice.unearth.ai/) en paneles adyacentes en este sitio web de demostración. 

## <a name="see-also"></a>Otras referencias

 [API de REST de documentos de búsqueda](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Sintaxis de consulta simplificada](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

 [Sintaxis de consulta completa de Lucene ](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 
 [Control de los resultados de la búsqueda](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
