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
ms.date: 09/03/2017
ms.author: heidist
ms.translationtype: HT
ms.sourcegitcommit: ce0189706a3493908422df948c4fe5329ea61a32
ms.openlocfilehash: 6e6c4491b8f66011340d1246495dbded7caf2903
ms.contentlocale: es-es
ms.lasthandoff: 09/05/2017

---

# <a name="analyzers-in-azure-search"></a>Analizadores de Azure Search

Un *analizador* es el componente del [procesamiento de búsquedas de texto completo](search-lucene-query-architecture.md) responsable de las conversiones texto a token para las cargas de trabajo tanto de indexación como de consulta. Durante la indexación, un analizador transforma el texto en términos acortados, que se escriben en el índice. En el momento de la consulta, un analizador realiza las mismas transformaciones (texto en términos acortados), pero esta vez en las operaciones de lectura durante las consultas. 

Las siguientes transformaciones son típicas durante el análisis:

+ Se quitan las palabras no esenciales (palabras irrelevantes) y los signos de puntuación.
+ Las frases y los términos con guiones se dividen en las partes que los componen.
+ Los términos están en minúscula.
+ Las palabras se reducen a sus formas raíz, con el fin de que se puedan encontrar coincidencias independientemente del tiempo verbal.

Azure Search proporciona un analizador predeterminado que se puede invalidar campo a campo con opciones alternativas. El fin de este artículo es describir el conjunto de opciones e indicar procedimientos recomendados para agregar un analizador a las operaciones de búsqueda. También muestra configuraciones de analizador de ejemplo para escenarios clave.

## <a name="how-analysis-fits-into-full-text-search-processing"></a>Cómo encaja el análisis en el procesamiento de búsquedas de texto completo

Los analizadores operan en las entradas de términos que pasa el analizador de consultas y devuelven los términos analizados que se agregan a un objeto de árbol de consulta.

 ![Diagrama de la arquitectura de la consulta de Lucene en Azure Search][1]

Los analizadores solo se utilizan en consultas de términos individuales o en consultas de frases. Los analizadores no se utilizan para los tipos de consulta con términos incompletos (consulta de prefijo, consulta de comodín, consulta de regex) o en consultas parciales. En dichos tipos de consulta, los términos se agregan directamente al árbol de consulta, por lo que se omite la fase de análisis. La única transformación realizada en los términos de consulta de esos tipos es el establecimiento de minúsculas.

## <a name="supported-analyzers"></a>Analizadores compatibles

En la lista siguiente se describen los analizadores compatibles con Azure Search.

| Categoría | Descripción |
|----------|-------------|
| [Analizador Lucene estándar](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Predeterminada. Se usa automáticamente para la indexación y las consultas. No se requieren ninguna especificación ni configuración. Este analizador de uso general funciona bien en la mayoría de lenguajes y escenarios.|
| Analizadores predefinidos | Se ofrece como un producto acabado diseñado para utilizarse tal cuál, con una poca personalización. <br/>Hay dos tipos: especializados y de lenguaje. Lo que hace que sean "predefinidos" es que se hace referencia a ellos por su nombre, sin personalización alguna. <br/><br/>[Analizadores especializados (independientes del lenguaje)](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable) para las entradas de texto que requieren procesamiento especializado o un procesamiento mínimo. Los analizadores predefinidos sin lenguaje incluyen **Asciifolding**, **Keyword**, **Pattern**, **Simple**, **Stop** y **Whitespace**.<br/><br/>Los [analizadores del lenguaje](https://docs.microsoft.com/rest/api/searchservice/language-support) proporciona una amplia compatibilidad lingüística para los lenguajes individuales. Azure Search admite 35 analizadores de lenguaje de Lucene y 50 analizadores de procesamiento de lenguaje natural de Microsoft. |
|[Analizadores personalizados](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Una configuración definida por el usuario de una combinación de los elementos existentes, que consta de un tokenizador (obligatorio) y filtros opcionales (char o token).|

Puede personalizar un analizador predefinido, como **Pattern** o **Stop**, para que use otras opciones que se documentan en [Predefined Analyzers Reference](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable) (Referencia de analizadores predefinidos). Solo algunos de los analizadores predefinidos tienen opciones que se pueden configurar. Como en cualquier personalización, asigne un nombre a la configuración nueva, como *myPatternAnalyzer* para distinguirlo del analizador Lucene Pattern.

## <a name="how-to-specify-analyzer"></a>Especificación de analizadores

1. Solo en analizadores personalizados, cree una sección `analyzer` en la definición del índice. Para más información, consulte [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) (Creación de un índice) y también [Create a custom analyzer](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search#create-a-custom-analyzer) (Creación de un analizador personalizado).

2. En todos los campos en que desee usar el analizador, establezca la propiedad `analyzer` como el nombre de un analizador de destino de una [definición de campo del índice](https://docs.microsoft.com/rest/api/searchservice/create-index). Los valores válidos incluyen un analizador predefinido, un analizador del lenguaje o un analizador personalizado definido previamente en el esquema del índice.

 Como alternativa, en lugar de una propiedad `analyzer`, puede establecer diferentes analizadores para la realización de indexaciones y consultas mediante los parámetros de campo `indexAnalyzer` y `searchAnalyzer`. 

3. Vuelva a compilar el índice para invocar los nuevos comportamientos de procesamiento de texto.

## <a name="best-practices"></a>Prácticas recomendadas

En esta sección se proporcionan consejos para trabajar con los analizadores de forma más eficaz.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Un analizador para lectura y escritura, salvo que tenga requisitos específicos

Azure Search le permite especificar diferentes analizadores para la indexación y búsqueda a través de parámetros de campo `indexAnalyzer` y `searchAnalyzer` adicionales. Si no se especifica, el analizador establecido con la propiedad `analyzer` se utiliza para la indexación y la búsqueda. Si `analyzer` no se especifica, se utiliza el analizador Lucene estándar predeterminado.

Una regla general es usar el mismo analizador de para los índices y las consultas, a menos que los requisitos específicos indiquen lo contrario. Por lo general, la eficacia aumenta si el analizador que crea el token es el mismo que se utiliza para buscar tokens más adelante, durante el tiempo de consulta. 

### <a name="test-during-active-development"></a>Prueba durante el desarrollo activo

El reemplazo del analizador estándar requiere que se reconstruya el índice. Si es posible, decida qué analizadores se van a usar durante el desarrollo activo antes de poner un índice en producción.

### <a name="compare-analyzers-side-by-side"></a>Comparación de analizadores

Se recomienda utilizar la [API de Analyze](https://docs.microsoft.com/rest/api/searchservice/test-analyzer). La respuesta consta de los términos acortados, que genera un analizador concreto para el texto que proporcione. 

> [!Tip]
> La [versión de demostración de Search Analyzer](http://alice.unearth.ai/) muestra una comparación del analizador de Lucene estándar, el analizador del lenguaje para inglés de Lucene y el procesador de lenguaje natural para inglés de Microsoft. Cada vez que se realiza una entrada de búsqueda, los resultados de cada analizador se muestran en los paneles adyacentes.

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

Las API de la versión preliminar incluyen atributos de índice adicionales para especificar diferentes analizadores para las operaciones de indexación y búsqueda. Los atributos `searchAnalyzer` y `indexAnalyzer` deben especificarse como un par, en lugar del atributo `analyzer` individual.


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
           "IndexAnalyzer":"whitespace",
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

+ Pruebe la sintaxis de consulta adicional de la sección de ejemplo [Buscar documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents#examples) o desde [Sintaxis de consulta simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) en el explorador de búsqueda en el portal.

+ Aprenda a aplicar [analizadores léxicos específicos del idioma](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Configure analizadores personalizados](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) para un procesamiento mínimo o un procesamiento especializado en los campos individuales.

+ [Compare los analizador estándar y de inglés](http://alice.unearth.ai/) en paneles adyacentes en este sitio web de demostración. 

## <a name="see-also"></a>Otras referencias

 [API de REST de documentos de búsqueda](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Sintaxis de consulta simplificada](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

 [Sintaxis de consulta completa de Lucene ](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 
 [Control de los resultados de la búsqueda](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png

