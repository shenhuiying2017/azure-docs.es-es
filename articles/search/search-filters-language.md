---
title: Filtros de idioma en Azure Search | Microsoft Docs
description: "Puede filtrar los criterios por identidad de seguridad del usuario, idioma, geolocalización o valores numéricos para reducir los resultados de búsqueda en las consultas en Azure Search, un servicio de búsqueda en la nube hospedado de Microsoft Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
ms.assetid: 
ms.service: search
ms.devlang: 
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/23/2017
ms.author: heidist
ms.openlocfilehash: 2c09de74405394d4c385dbbd0535913cf2488744
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="how-to-filter-by-language-in-azure-search"></a>Filtro por idioma en Azure Search 

Un requisito fundamental de una aplicación de búsqueda multilingüe es la posibilidad de buscar y recuperar los resultados en el idioma del usuario. En Azure Search, una manera de satisfacer los requisitos de idioma de una aplicación multilingüe es crear una serie de campos dedicados al almacenamiento de cadenas en un idioma determinado y luego restringir la búsqueda de texto completo a solo esos campos en el momento de la consulta.

Los parámetros de consulta de la solicitud se usan para establecer el ámbito de la operación de búsqueda y para recortar los resultados de los campos que no proporcionan contenido compatible con la experiencia de búsqueda que quiere proporcionar.

| Parámetros | Propósito |
|-----------|--------------|
| **searchFields** | Limita la búsqueda de texto completo a la lista de campos con nombre. |
| **$select** | Recorta la respuesta para incluir solo los campos que especifique. De forma predeterminada, se devuelven todos los campos recuperables. El parámetro **$select** le permite elegir cuáles devolver. |

El éxito de esta técnica depende de la integridad del contenido del campo. Azure Search no traduce las cadenas ni realiza la detección del idioma. Es responsabilidad suya asegurarse de que los campos contienen las cadenas que espera.

## <a name="define-fields-for-content-in-different-languages"></a>Definición de campos para el contenido en distintos idiomas

En Azure Search, las consultas tienen como destino un índice único. Los desarrolladores que quieran proporcionar cadenas específicas del idioma en una única experiencia de búsqueda suele definen campos dedicados para almacenar los valores: un campo para las cadenas en inglés, otro para las de francés, etc. 

En nuestros ejemplos, incluido el de la [inmobiliaria](search-get-started-portal.md) que se muestra a continuación, es posible que haya visto definiciones de campo similares a las de la captura de pantalla siguiente. Observe cómo este ejemplo muestra las asignaciones del analizador de idioma para los campos de este índice. Los campos que contienen cadenas funcionan mejor en la búsqueda de texto completo cuando se emparejan con un analizador diseñado para administrar las reglas lingüísticas del idioma de destino.

  ![](./media/search-filters-language/lang-fields.png)

> [!Note]
> Para ver ejemplos de código que muestran definiciones de campo con analizadores de idiomas, consulte [Definición de un índice (. NET)](https://docs.microsoft.com/azure/search/search-create-index-dotnet#define-your-azure-search-index) y [Definición de un índice (REST)](https://docs.microsoft.com/azure/search/search-create-index-rest-api#define-your-azure-search-index-using-well-formed-json).

## <a name="build-and-load-an-index"></a>Creación y carga de un índice

Un paso intermedio (y, quizás, obvio) es que tiene que [crear y completar el índice](https://docs.microsoft.com/azure/search/search-create-index-dotnet#create-the-index) antes de formular una consulta. Hemos mencionado este paso aquí para mayor integridad. Una manera de determinar si el índice está disponible es comprobar la lista de índices en el [portal](https://portal.azure.com).

## <a name="constrain-the-query-and-trim-results"></a>Restricción de la consulta y recorte de los resultados

Los parámetros de la consulta se usan para limitar la búsqueda a campos específicos y luego recortar los resultados de los campos que no resulten útiles para su escenario. Dado un objetivo de restricción de la búsqueda a los campos que contienen cadenas en francés, usaría **searchFields** como destino de la consulta en los campos que contienen cadenas en ese idioma. 

De forma predeterminada, una búsqueda devuelve todos los campos que están marcados como recuperables. Por lo tanto, puede que quiera excluir los campos que no se ajustan a la experiencia de búsqueda específica del idioma que desea proporcionar. En concreto, si limita la búsqueda a un campo con cadenas en francés, probablemente desee excluir los campos con cadenas en inglés de los resultados. Mediante el parámetro de consulta **$select** puede controlar qué campos se devuelven a la aplicación que realiza la llamada.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> Aunque no hay ningún argumento $filter en la consulta, este caso de uso está fuertemente vinculado a los conceptos de filtro, por lo que se presenta como un escenario de filtrado.

## <a name="see-also"></a>Otras referencias

+ [Filtros de Azure Search](search-filters.md)
+ [Analizadores de idiomas](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [Cómo funciona la búsqueda de texto completo en Azure Search](search-lucene-query-architecture.md)
+ [API de REST de documentos de búsqueda](https://docs.microsoft.com/rest/api/searchservice/search-documents)

