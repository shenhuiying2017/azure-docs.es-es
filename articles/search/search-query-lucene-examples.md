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
ms.date: 10/27/2016
ms.author: liamca
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: bfb6fc04d3aa4937336083d04137e22694e5146c
ms.contentlocale: es-es
ms.lasthandoff: 06/09/2017

---

# <a name="lucene-query-syntax-examples-for-building-queries-in-azure-search"></a>Ejemplos de sintaxis de consulta de Lucene para la creación de consultas en Búsqueda de Azure
Al construir consultas para Azure Search, puede usar la [sintaxis de consulta simple](https://msdn.microsoft.com/library/azure/dn798920.aspx) predeterminada o el [analizador de consultas Lucene alternativo de Azure Search](https://msdn.microsoft.com/library/azure/mt589323.aspx). El Analizador de consultas de Lucene admite las construcciones de consultas más complejas, como las consultas de ámbito de campo, la búsqueda aproximada, la búsqueda de proximidad, la priorización de términos y las expresiones regulares.

En este artículo, puede avanzar por los ejemplos que muestran la sintaxis de consulta de Lucene y los resultados en paralelo. Ejemplos que se ejecutan en un índice de búsqueda cargado previamente en [JSFiddle](https://jsfiddle.net), un editor de código en línea para probar el script y HTML. Para obtener información preliminar sobre el procesamiento de consultas, vea [Cómo funciona la búsqueda de texto completo en Azure Search](search-lucene-query-architecture.md).

Haga doble clic en las direcciones URL de ejemplo de consulta para abrir JSFiddle en otra ventana del explorador.

> [!NOTE]
> Los ejemplos siguientes aprovechan un índice de búsqueda que consta de trabajos disponibles según un conjunto de datos proporcionado por la iniciativa [City of New York OpenData](https://nycopendata.socrata.com/) . Estos datos no deben considerarse actuales o completos. El índice está en un servicio de espacio aislado proporcionado por Microsoft. No es necesaria una suscripción de Azure o Azure Search para probar estas consultas.
>
>

## <a name="viewing-the-examples-in-this-article"></a>Visualización de los ejemplos de este artículo
Todos los ejemplos de este artículo especifican el analizador de consultas Lucene mediante el parámetro de búsqueda**queryType** . Si se usa el analizador de consultas Lucene desde el código, deberá especificar el valor **queryType** en cada solicitud.  Los valores válidos son **simple**|**full**, siendo **simple** el valor predeterminado y **full** el valor para el analizador de consultas Lucene. Consulte [Búsqueda de documentos (API de REST del servicio Azure Search)](https://msdn.microsoft.com/library/azure/dn798927.aspx) para especificar los parámetros de consulta.

**Ejemplo 1** : haga clic con el botón derecho en el siguiente fragmento de código de consulta para abrirlo en una nueva página del explorador que carga JSFiddle y ejecuta la consulta:

* [&queryType=full&search=*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

Esta consulta devuelve los documentos de nuestro índice de trabajos (cargado en un servicio de espacio aislado)

En la nueva ventana del explorador, verá el origen de JavaScript y la salida de HTML en paralelo. El script hace referencia a una consulta, que se proporciona mediante las direcciones URL de ejemplo en este artículo. Por ejemplo, en **Ejemplo 1**, la consulta subyacente es la siguiente:

    http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

Tenga en cuenta que la consulta utiliza un índice de Búsqueda de Azure preconfigurado denominado nycjobs. El parámetro **searchFields** restringe la búsqueda solo al campo de puesto. El parámetro **queryType** está establecido en **full**, lo que indica a Azure Search que deberá usar el analizador de consultas Lucene para esta consulta.

### <a name="fielded-query-operation"></a>Operación de consulta clasificada por campos
Para modificar los ejemplos de este artículo, especifique una construcción **fieldname:searchterm** para definir una operación de consulta clasificada por campos, donde el campo es una sola palabra y el término de búsqueda también es una sola palabra o frase, opcionalmente con operadores booleanos. Estos son algunos ejemplos:

* business_title:(senior NOT junior)
* state:("New York" AND "New Jersey")

Asegúrese de colocar varias cadenas entre comillas si desea que las dos cadenas se evalúen como una sola entidad, como en este caso buscando dos ciudades distintas en el campo de ubicación. Además, asegúrese del operador está en mayúsculas como puede ver en NOT y AND.

El campo especificado en **fieldname:searchterm** debe ser un campo que permita búsquedas. Consulte [Creación de un índice (API de REST del servicio Azure Search)](https://msdn.microsoft.com/library/azure/dn798941.aspx) para más información sobre cómo se usan los atributos de índice en las definiciones de campo.

## <a name="fuzzy-search"></a>Búsqueda aproximada
Una búsqueda aproximada busca coincidencias en términos que tienen una construcción similar. Según la [documentación de Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), las búsquedas aproximadas se basan en la [distancia Levenshtein-Damerau](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance).

Para realizar una búsqueda aproximada, utilice el símbolo "~" de tilde de la Ñ al final de una sola palabra con un parámetro opcional, un valor entre 0 y 2, que especifica la distancia de edición. Por ejemplo, "blue~" o "blue~1" devolvería blue, blues y glue.

**Ejemplo 2** : haga clic con el botón derecho en el siguiente fragmento de código de consulta para probarlo. Esta consulta busca los puestos de empresa con el término "senior" en ellos, pero no "junior":

* [&queryType=full&search= business_title:senior NOT junior](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## <a name="proximity-search"></a>Búsqueda de proximidad
Las búsquedas de proximidad se utilizan para buscar términos que están cerca entre sí en un documento. Inserte un símbolo "~" de la tilde de la Ñ al final de una frase seguido del número de palabras que crea el límite de proximidad. Por ejemplo, "hotel airport"~5 buscará los términos "hotel" y "airport" dentro de 5 palabras en un documento.

**Ejemplo 3** : haga clic con el botón derecho en el siguiente fragmento de consulta. Esta consulta busca los trabajos con el asociado de término (donde se está mal escrito):

* [&queryType=full&search= business_title:asosiate~](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

**Ejemplo 4** : haga clic con el botón derecho en la consulta. Busque trabajos con el término "senior analyst" no separado por más de una palabra:

* [&queryType=full&search=business_title:"senior analyst"~1](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**Ejemplo 5** : pruebe a quitar las palabras entre el término "senior analyst".

* [&queryType=full&search=business_title:"senior analyst"~0](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## <a name="term-boosting"></a>Priorización de términos
"Priorización de términos" hace referencia a la valoración de un documento superior si contiene el término prioritario con respecto a los documentos que no contienen el término. Esto difiere de los perfiles de puntuación en los que aumentan ciertos campos, en lugar de términos específicos. En el siguiente ejemplo se muestran las diferencias.

Considere la posibilidad de usar un perfil de puntuación que dé prioridad a las coincidencias en un campo determinado, como **genre** en el ejemplo musicstoreindex. La priorización de términos podría utilizarse para dar mayor prioridad a determinados términos de búsqueda frente a otros. Por ejemplo, "rock^2 electronic" dará prioridad a los documentos que contengan los términos de búsqueda del campo **genre** frente a otros campos que permitan búsquedas en el índice. Además, los documentos que contienen el término de búsqueda "rock" tendrán una clasificación mayor que los del término de búsqueda "electronic" como resultado del valor de la priorización de términos (2).

Para dar prioridad a un término, use el símbolo de intercalación, "^", un símbolo con un factor de prioridad (un número) al final del término que desee buscar. Cuanto mayor sea el factor de prioridad, más relevante será el término en relación con otros términos de búsqueda. De forma predeterminada, el factor de prioridad es 1. Aunque el factor de prioridad debe ser positivo, puede ser inferior a 1 (por ejemplo, 0,2).

**Ejemplo 6**: haga clic con el botón derecho en la consulta. Busque trabajos con el término "computer analyst", donde vemos que no se generan resultados con las palabras "computer" y "analyst"; "analyst jobs" sigue encabezando los resultados.

* [&queryType=full&search=business_title:computer analyst](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**Ejemplo 7**: inténtelo de nuevo. En este caso, se da prioridad al término "computer" sobre el término "analyst" si no existen ambas palabras.

* [&queryType=full&search=business_title:computer^2 analyst](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## <a name="regular-expression"></a>Expresión regular
Una búsqueda de expresión regular encuentra una coincidencia en función del contenido entre barras diagonales "/", como se documentó en la [clase RegExp](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

**Ejemplo 8** : haga clic con el botón derecho en la consulta. Busque trabajos con el término "Senior" o "Junior".

* `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

La dirección URL de este ejemplo no aparecerá correctamente en la página. Como alternativa, copie la dirección URL siguiente y péguela en la dirección URL del explorador:     `http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`

## <a name="wildcard-search"></a>Búsqueda con caracteres comodín
Puede usar la sintaxis generalmente reconocida para búsquedas con caracteres comodín únicas (?) o múltiples (\*). Tenga en cuenta que el Analizador de consultas de Lucene admite el uso de estos símbolos con un único término y no una frase.

**Ejemplo 9**: haga clic con el botón derecho en la consulta. Busque los trabajos que contengan el prefijo "prog" que incluiría los títulos de empresa con los términos "programming" y "programmer" en él.

* [&queryType=full&$select=business_title&search=business_title:prog*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26queryType=full%26$select=business_title%26search=business_title:prog*)

¿No puede utilizar un símbolo * o ? como primer carácter de la búsqueda.

## <a name="next-steps"></a>Pasos siguientes
Intente especificar el Analizador de consultas de Lucene en el código. Los vínculos siguientes explican cómo configurar las consultas de búsqueda para la API de REST y .NET. Los vínculos usan la sintaxis simple de forma predeterminada, por lo que deberá aplicar lo aprendido en este artículo para especificar el valor de **queryType**.

* [Consultas del índice de Búsqueda de Azure con el SDK de .NET](search-query-dotnet.md)
* [Realización de una consulta al índice de Búsqueda de Azure con la API de REST](search-query-rest-api.md)

