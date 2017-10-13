---
title: Ejemplos de consultas de Lucene para Azure Search | Microsoft Docs
description: "Sintaxis de consulta de Lucene para la búsqueda aproximada, la búsqueda de proximidad, la priorización de términos, la búsqueda de expresiones regulares y la búsqueda con caracteres comodín"
services: search
documentationcenter: 
author: LiamCa
manager: pablocas
editor: 
tags: Lucene query analyzer syntax
ms.assetid: 147f360d-a5ce-4d7b-a909-c8b65bfb748c
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/21/2017
ms.author: liamca
ms.openlocfilehash: 1faed621039ecd04064cb074e6b9011418e6ec47
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="lucene-query-syntax-examples-for-building-queries-in-azure-search"></a>Ejemplos de sintaxis de consulta de Lucene para la creación de consultas en Búsqueda de Azure
Al construir consultas para Azure Search, puede usar la [sintaxis de consulta simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) predeterminada o el [analizador de consultas Lucene alternativo de Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). El Analizador de consultas de Lucene admite las construcciones de consulta más complejas, como las consultas de ámbito de campo, la búsqueda aproximada, la búsqueda por proximidad, la priorización de términos y las expresiones regulares.

En este artículo, puede ir a través de ejemplos de operaciones de consulta disponibles cuando se usa la sintaxis completa.

## <a name="viewing-the-examples-in-jsfiddle"></a>Visualización de los ejemplos en JSFiddle

Todos los ejemplos de este artículo son consultas ejecutables que se ejecutan en un índice de búsqueda cargado previamente en [JSFiddle](https://jsfiddle.net), un editor de código en línea para probar el script y HTML. 

Para ejecutarlos, haga clic con el botón derecho en las direcciones URL de ejemplo de consulta para abrir JSFiddle en otra ventana del explorador.

> [!NOTE]
> Los ejemplos siguientes aprovechan un índice de búsqueda que consta de trabajos disponibles según un conjunto de datos proporcionado por la iniciativa [City of New York OpenData](https://nycopendata.socrata.com/) . Estos datos no deben considerarse actuales o completos. El índice está en un servicio de espacio aislado proporcionado por Microsoft. No es necesaria una suscripción de Azure o Azure Search para probar estas consultas.
>


## <a name="how-to-invoke-full-lucene-parsing"></a>Invocación del análisis completo de Lucene

Todos los ejemplos de este artículo especifican el parámetro de búsqueda **queryType=full**, que indica que toda la sintaxis se controla mediante el Analizador de consultas de Lucene. 

**Ejemplo 1** : haga clic con el botón derecho en el siguiente fragmento de código de consulta para abrirlo en una nueva página del explorador que carga JSFiddle y ejecuta la consulta:

* [&amp;queryType=full&amp;search=*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

En la nueva ventana del explorador se muestran el origen de JavaScript y la salida de HTML en paralelo. El script hace referencia a una consulta completa (no solo al fragmento de código, tal y como se muestra en el vínculo). La consulta completa aparece en las direcciones URL de cada ejemplo. 

Esta consulta devuelve los documentos de nuestro índice de trabajos de la ciudad de Nueva York (nycjobs, cargados en un servicio de espacio aislado). Para mayor brevedad, la consulta especifica que solo se devuelven puestos de empresa. La consulta subyacente completa es como sigue:

    http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

El parámetro **searchFields** restringe la búsqueda solo al campo de puesto. El parámetro **queryType** está establecido en **full**, lo que indica a Azure Search que deberá usar el analizador de consultas Lucene para esta consulta.

> [!NOTE]
> Para obtener información preliminar sobre el procesamiento de consultas, vea [Cómo funciona la búsqueda de texto completo en Azure Search](search-lucene-query-architecture.md). Para más información sobre los parámetros de búsqueda, consulte [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) [Búsqueda de documentos (API de REST del servicio Azure Search)].
>

### <a name="fielded-query-operation"></a>Operación de consulta clasificada por campos
Para modificar los ejemplos de este artículo, especifique una construcción **fieldname:searchterm** para definir una operación de consulta clasificada por campos, donde el campo es una sola palabra y el término de búsqueda también es una sola palabra o frase, opcionalmente con operadores booleanos. Estos son algunos ejemplos:

* business_title:(senior NOT junior)
* state:("New York" AND "New Jersey")

Asegúrese de colocar varias cadenas entre comillas si desea que las dos cadenas se evalúen como una sola entidad, como en este caso buscando dos ciudades distintas en el campo de ubicación. Además, asegúrese del operador está en mayúsculas como puede ver en NOT y AND.

El campo especificado en **fieldname:searchterm** debe ser un campo que permita búsquedas. Consulte [Creación de un índice (API de REST del servicio Azure Search)](https://docs.microsoft.com/rest/api/searchservice/create-index) para más información sobre cómo se usan los atributos de índice en las definiciones de campo.

**Ejemplo 2**: haga clic con el botón derecho en el fragmento de código de consulta siguiente. Esta consulta busca los puestos de empresa con el término "senior" en ellos, pero no "junior":

* [&amp;queryType=full&amp;search= business_title:senior NOT junior](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## <a name="fuzzy-search-example"></a>Ejemplo de búsqueda aproximada
Una búsqueda aproximada busca coincidencias en términos que tienen una construcción similar. Según la [documentación de Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), las búsquedas aproximadas se basan en la [distancia Levenshtein-Damerau](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance).

Para realizar una búsqueda aproximada, agregue la virgulilla, "~", al final de una sola palabra con un parámetro opcional y un valor entre 0 y 2 para especificar la distancia de edición. Por ejemplo, "blue~" o "blue~1" devolvería blue, blues y glue.

**Ejemplo 3** : haga clic con el botón derecho en el siguiente fragmento de consulta. Esta consulta busca los trabajos con el asociado de término (donde se está mal escrito):

* [&amp;queryType=full&amp;search= business_title:asosiate~](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

> [!Note]
> Las consultas aproximadas no se [analizan](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis), lo cual puede resultar sorprendente si espera lematización. Solo se realizan análisis léxicos en términos completos (consulta de término o de expresión). Los tipos de consulta con términos incompletos (consulta de prefijo, de carácter comodín, de expresión regular o aproximada) se agregan directamente en el árbol de la consulta, omitiéndose la fase de análisis. La única transformación que se realiza en los términos de consulta incompletos es el establecimiento de minúsculas.
>

## <a name="proximity-search-example"></a>Ejemplo de búsqueda por proximidad
Las búsquedas de proximidad se utilizan para buscar términos que están cerca entre sí en un documento. Inserte un símbolo "~" de la tilde de la Ñ al final de una frase seguido del número de palabras que crea el límite de proximidad. Por ejemplo, "hotel airport"~5 buscará los términos "hotel" y "airport" dentro de 5 palabras en un documento.

**Ejemplo 4** : haga clic con el botón derecho en la consulta. Busque trabajos con el término "senior analyst" no separado por más de una palabra:

* [&amp;queryType=full&amp;search=business_title:"senior analyst"~1](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**Ejemplo 5** : pruebe a quitar las palabras entre el término "senior analyst".

* [&amp;queryType=full&amp;search=business_title:"senior analyst"~0](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## <a name="term-boosting-examples"></a>Ejemplos de priorización de términos
"Priorización de términos" hace referencia a la valoración de un documento superior si contiene el término prioritario con respecto a los documentos que no contienen el término. Esto difiere de los perfiles de puntuación en los que aumentan ciertos campos, en lugar de términos específicos. En el siguiente ejemplo se muestran las diferencias.

Considere la posibilidad de usar un perfil de puntuación que dé prioridad a las coincidencias en un campo determinado, como **genre** en el ejemplo musicstoreindex. La priorización de términos podría utilizarse para dar mayor prioridad a determinados términos de búsqueda frente a otros. Por ejemplo, "rock^2 electronic" dará prioridad a los documentos que contengan los términos de búsqueda del campo **genre** frente a otros campos que permitan búsquedas en el índice. Además, los documentos que contienen el término de búsqueda "rock" tendrán una clasificación mayor que los del término de búsqueda "electronic" como resultado del valor de la priorización de términos (2).

Para dar prioridad a un término, use el símbolo de intercalación, "^", un símbolo con un factor de prioridad (un número) al final del término que desee buscar. Cuanto mayor sea el factor de prioridad, más relevante será el término en relación con otros términos de búsqueda. De forma predeterminada, el factor de prioridad es 1. Aunque el factor de prioridad debe ser positivo, puede ser inferior a 1 (por ejemplo, 0,2).

**Ejemplo 6**: haga clic con el botón derecho en la consulta. Busque trabajos con el término "computer analyst", donde vemos que no se generan resultados con las palabras "computer" y "analyst"; "analyst jobs" sigue encabezando los resultados.

* [&amp;queryType=full&amp;search=business_title:computer analyst](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**Ejemplo 7**: inténtelo de nuevo. En este caso, se da prioridad al término "computer" sobre el término "analyst" si no existen ambas palabras.

* [&amp;queryType=full&amp;search=business_title:computer^2 analyst](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## <a name="regular-expression-example"></a>Ejemplo de expresión regular
Una búsqueda de expresión regular encuentra una coincidencia en función del contenido entre barras diagonales "/", como se documentó en la [clase RegExp](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

**Ejemplo 8** : haga clic con el botón derecho en la consulta. Busque trabajos con el término "Senior" o "Junior".

* `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

La dirección URL de este ejemplo no aparecerá correctamente en la página. Como alternativa, copie la dirección URL siguiente y péguela en la dirección URL del explorador: `http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`

## <a name="wildcard-search-example"></a>Ejemplo de búsqueda con caracteres comodín
Puede usar la sintaxis generalmente reconocida para búsquedas con caracteres comodín únicas (?) o múltiples (\*). Tenga en cuenta que el Analizador de consultas de Lucene admite el uso de estos símbolos con un único término y no una frase.

**Ejemplo 9**: haga clic con el botón derecho en la consulta. Busque los trabajos que contengan el prefijo "prog" que incluiría los títulos de empresa con los términos "programming" y "programmer" en él.

* [&amp;queryType=full&amp;$select=business_title&amp;search=business_title:prog*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26queryType=full%26$select=business_title%26search=business_title:prog*)

¿No puede utilizar un símbolo * o ? como primer carácter de la búsqueda.

## <a name="next-steps"></a>Pasos siguientes
Intente especificar el Analizador de consultas de Lucene en el código. Los vínculos siguientes explican cómo configurar las consultas de búsqueda para la API de REST y .NET. Los vínculos usan la sintaxis simple de forma predeterminada, por lo que deberá aplicar lo aprendido en este artículo para especificar el valor de **queryType**.

* [Consultas del índice de Búsqueda de Azure con el SDK de .NET](search-query-dotnet.md)
* [Realización de una consulta al índice de Búsqueda de Azure con la API de REST](search-query-rest-api.md)

## <a name="see-also"></a>Otras referencias

 [Cómo funciona la búsqueda de texto completo en Azure Search](search-lucene-query-architecture.md)