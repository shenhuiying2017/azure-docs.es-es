---
title: Varios lenguajes para Azure Search | Microsoft Docs
description: "Búsqueda de Azure admite 56 idiomas y aprovecha los analizadores de idiomas Lucene y la tecnología de procesamiento de lenguaje natural de Microsoft."
services: search
documentationcenter: 
author: yahnoosh
manager: pablocas
editor: 
ms.assetid: 55a00b44-804d-41bb-9c96-e6ea498616f5
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/23/2017
ms.author: jlembicz
ms.openlocfilehash: dbbab31bac66ce73dbf9883992713a2c16581e19
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="create-an-index-for-documents-in-multiple-languages-in-azure-search"></a>Creación de un índice para documentos en varios idiomas en Búsqueda de Azure
> [!div class="op_single_selector"]
>
> * [Portal](search-language-support.md)
> * [REST](https://msdn.microsoft.com/library/azure/dn879793.aspx)
> * [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.analyzername.aspx)
>
>

Desatar la potencia de los analizadores de lenguajes es tan fácil como establecer una propiedad en un campo de búsqueda en la definición del índice. Ahora puede realizar este paso en el portal.

A continuación, se muestran capturas de pantalla de las hojas del Portal de Azure para Búsqueda de Azure que permiten a los usuarios definir un esquema de índice. En esta hoja, los usuarios pueden crear todos los campos y establecer la propiedad de analizador para cada uno de ellos.

> [!IMPORTANT]
> Solo puede establecer un analizador de lenguaje durante la definición de campo, como en al crear un nuevo índice desde el principio de o al agregar un nuevo campo a un índice existente. Asegúrese de especificar completamente todos los atributos, incluido el analizador, al crear el campo. No podrá editar los atributos ni cambiar el tipo de analizador una vez guardado los cambios.
>
>

## <a name="define-a-new-field-definition"></a>Definición de una nueva definición de campo
1. Inicie sesión en [Azure Portal](https://portal.azure.com) y abra la hoja de servicio del servicio de búsqueda.
2. Haga clic en **Agregar un índice** en la barra de comandos de la parte superior del panel de servicio para iniciar un nuevo índice, o abra un índice existente para establecer un analizador de nuevos campos que se va a agregar a un índice existente.
3. Aparece la hoja Campos, que proporciona opciones para definir el esquema del índice, incluida la pestaña Analizador usada para elegir un analizador de lenguaje.
4. En Campos, inicie una definición de campo y proporcione un nombre, seleccione el tipo de datos y establezca los atributos para marcar el campo como texto completo que se puede buscar, recuperar resultados de búsqueda, que se pueden usar en estructuras de navegación de facetas, se pueden ordenar, etc.
5. Antes de pasar al siguiente campo, abra la pestaña **Analizador** .

![][1]
*Para seleccionar un analizador, haga clic en la pestaña Analizador en la hoja Campos*.

## <a name="choose-an-analyzer"></a>Selección de un analizador
1. Desplácese hasta encontrar el campo que va a definir.
2. Si no ha marcado el campo como de búsqueda, haga clic en la casilla para marcarla como **Permite búsqueda**.
3. Haga clic en el área Analizador para mostrar la lista de analizadores disponibles.
4. Elija el analizador que desea usar.

![][2]
*Seleccione uno de los analizadores compatibles para cada campo*.

De forma predeterminada, todos los campos en los que se puede buscar usan el [analizador Standard Lucene](http://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) , que no depende del idioma. Para ver la lista completa de los analizadores compatibles, consulte [Compatibilidad de idioma (API de REST de servicio de Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn879793.aspx).

Una vez que el analizador de idioma está activado para un campo, se usará con cada solicitud de búsqueda e indexación para ese campo. Cuando se emite una consulta en varios campos con diferentes analizadores, la consulta se procesará de manera independiente por los analizadores correctos para cada campo.

Muchas aplicaciones web y móviles dan servicio a usuarios de todo el mundo que usan diferentes idiomas. Es posible definir un índice para un escenario similar al siguiente al crear un campo para cada idioma admitido.

![][3]
*Definición de índice con un campo de descripción para cada idioma admitido*

Si se conoce el idioma del agente que emite una consulta, una solicitud de búsqueda se puede aplicar a un campo específico con el parámetro de consulta **searchFields** . La siguiente consulta solo se emitirá en relación a la descripción en polaco:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=description_pl&api-version=2016-09-01`

Puede consultar el índice desde el portal con el **Explorador de búsqueda** para pegar una consulta similar a la mostrado anteriormente. El Explorador de búsqueda está disponible en la barra de comandos de la hoja del servicio. Para obtener más información, consulte [Consultas en Búsqueda de Azure.](search-explorer.md)

A veces se desconoce el idioma del agente que emite una consulta, en cuyo caso la consulta puede emitir en todos los campos al mismo tiempo. Si es necesario, se pueden definir una preferencia de resultados en un determinado idioma mediante [perfiles de puntuación](https://msdn.microsoft.com/library/azure/dn798928.aspx). En el ejemplo siguiente, las coincidencias encontradas en la descripción en inglés tendrán una puntuación mayor que las coincidencias en polaco y francés:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2016-09-01`

Si es desarrollador de. NET, tenga en cuenta que puede configurar los analizadores de idioma mediante el [SDK de Azure Search para .NET](http://www.nuget.org/packages/Microsoft.Azure.Search). La última versión incluye compatibilidad con los analizadores de idioma de Microsoft.

<!-- Image References -->
[1]: ./media/search-language-support/AnalyzerTab.png
[2]: ./media/search-language-support/SelectAnalyzer.png
[3]: ./media/search-language-support/IndexDefinition.png
