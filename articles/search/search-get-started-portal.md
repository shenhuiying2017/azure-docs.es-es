---
title: "Tutorial: creación del primer índice de Azure Search en el portal | Microsoft Docs"
description: "En el portal de Azure, use datos de ejemplo predefinidos para generar un índice. Explore la búsqueda de texto completo, los filtros, las facetas, la búsqueda aproximada, la búsqueda geográfica y más."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 21adc351-69bb-4a39-bc59-598c60c8f958
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 06/26/2017
ms.author: heidist
ms.openlocfilehash: c49989058fdd98d623c5517060f725e5f7e436d8
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-create-your-first-azure-search-index-in-the-portal"></a>Tutorial: creación del primer índice de Azure Search en el portal

En Azure Portal, comience con un conjunto de datos de ejemplo predefinido para generar rápidamente un índice mediante el **Asistente para la importación de datos**. Explore la búsqueda de texto completo, los filtros, las facetas, la búsqueda aproximada y la búsqueda geográfica con el **Explorador de búsqueda**.  

Esta introducción sin código le permitirá comenzar a trabajar con datos predefinidos de modo que pueda escribir consultas interesantes de forma inmediata. Si bien las herramientas del portal no sustituyen al código, resultan de utilidad para realizar estas tareas:

+ Aprender de manera práctica con un período de inicialización mínimo
+ Crear un prototipo de un índice antes de escribir código en **Import data** (Importar datos).
+ Probar las consultas y la sintaxis del analizador en el **Explorador de búsqueda**.
+ Ver un índice existente publicado en el servicio y buscar sus atributos.

**Tiempo estimado:** unos 15 minutos, o más si también es necesario registrar la cuenta o el servicio. 

Como alternativa, puede empezar con la [introducción basada en código a la programación de Azure Search en .NET](search-howto-dotnet-sdk.md).

## <a name="prerequisites"></a>Requisitos previos

En este tutorial se supone que tiene una [suscripción de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) y un [servicio de Azure Search](search-create-service-portal.md). 

Si no desea aprovisionar un servicio inmediatamente, puede ver una demostración de 6 minutos de los pasos de este tutorial, a partir del minuto 3 de este [vídeo de introducción a Azure Search](https://channel9.msdn.com/Events/Connect/2016/138).

## <a name="find-your-service"></a>Búsqueda del servicio
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Abra el panel del servicio Búsqueda de Azure. Si no ancló el icono del servicio en el panel, pude encontrar su servicio de esta manera: 
   
   * En la barra de accesos, haga clic en **More services** (Más servicios) en la parte inferior del panel de navegación izquierdo.
   * En el cuadro de búsqueda, escriba *búsqueda* para obtener una lista de servicios de búsqueda de sus suscripción. El servicio debe aparecer en la lista. 

## <a name="check-for-space"></a>Búsqueda de espacio
Muchos clientes comienzan con el servicio gratis. Esta versión está limitada a tres índices, tres orígenes de datos y tres indexadores. Asegúrese de que tiene espacio para elementos adicionales antes de empezar. Este tutorial crea uno de cada objeto. 

> [!TIP] 
> Los iconos del panel de servicio muestran el número de índices, indexadores y orígenes de datos que ya tiene. El icono de indexador muestra indicadores de acierto y error. Haga clic en el icono para ver el recuento de indexadores. 
>
> ![Iconos para indexadores y orígenes de datos][1]
>

## <a name="create-index"></a> Creación de un índice y carga de datos
Las consultas de búsqueda recorren en iteración un *índice* que contiene datos de búsqueda, metadatos y construcciones usados para optimizar determinados comportamientos de búsqueda.

Para mantener esta tarea en el portal, usamos un conjunto de datos de ejemplo integrado que se puede rastrear mediante un indexador con el **Asistente para la importación de datos**. 

#### <a name="step-1-start-the-import-data-wizard"></a>Paso 1: Inicio del Asistente para la importación de datos
1. En el panel del servicio Búsqueda de Azure, haga clic en **Importar datos** en la barra de comandos para iniciar un asistente que crea y rellena un índice.
   
    ![Comando de importación de datos][2]

2. En el asistente, haga clic en **Origen de datos** > **Ejemplos** > **realestate-us-sample**. Este origen de datos está preconfigurado con el nombre, el tipo y la información de conexión. Una vez creado, se convierte en un "origen de datos existente" que se puede reutilizar en otras operaciones de importación.

    ![Selección del conjunto de datos de ejemplo][9]

3. Haga clic en **Aceptar** para usarlo.

#### <a name="step-2-define-the-index"></a>Paso 2: Definición del índice
La creación de un índice es normalmente una tarea manual y basada en código, pero el asistente puede generar un índice para cualquier origen de datos que pueda rastrear. Como mínimo, un índice necesita un nombre y una colección de campos, uno de los cuales debe estar marcado como la clave de documento para identificar de forma única cada documento.

Los campos tienen tipos de datos y atributos. Las casillas de la parte superior son *atributos de índice* que controlan cómo se usa el campo. 

* **Retrievable** significa que se muestra en la lista de resultados de búsqueda. Puede desactivar esta casilla para marcar los campos individuales como fuera de los resultados de búsqueda, por ejemplo, cuando los campos se usan solo en expresiones de filtro. 
* **Filterable**, **Sortable** y **Facetable** determinan si un campo se puede usar en un filtro, una ordenación o una estructura de navegación de facetas. 
* **Searchable** significa que se incluye un campo en la búsqueda de texto completo. Las cadenas permiten realizar búsquedas. Los campos numéricos y los booleanos a menudo se marcan como no utilizables en búsquedas. 

De forma predeterminada, el asistente busca en el origen de datos identificadores únicos como base para el campo de clave. Las cadenas se atribuyen como que se pueden recuperar y permiten realizar búsquedas. Los enteros se atribuyen como que se pueden recuperar, filtrar, ordenar y clasificar.

  ![Índice realestate generado][3]

Haga clic en **Aceptar** para crear el índice.

#### <a name="step-3-define-the-indexer"></a>Paso 3: Definición del indexador
Aún en el **Asistente para la importación de datos**, haga clic en **Indexador** > **Nombre** y escriba un nombre para el indexador. 

Este objeto define un proceso ejecutable. Podría colocarlo en una programación recurrente; sin embargo, por ahora, use la opción predeterminada para ejecutar el indexador enseguida, inmediatamente, cuando haga clic en **Aceptar**.  

  ![indexador realestate][8]

## <a name="check-progress"></a>Comprobación del progreso
Para supervisar la importación de datos, vuelva al panel de servicio, desplácese hacia abajo y haga doble clic en el icono **Indexadores** para abrir la lista de indexadores. Debe aparecer en la lista el indexador recién creado, con el estado "en curso" o correcto, junto con el número de documentos indexados.

   ![Mensaje de progreso del indexador][4]

## <a name="query-index"></a> Consulta del índice
Ahora tiene un índice de búsqueda listo para su consulta. **Explorador de búsqueda** es una herramienta de consulta integrada en el portal. Proporciona un cuadro de búsqueda para que pueda comprobar si los resultados de búsqueda son los esperados. 

> [!TIP]
> En el [vídeo de introducción a Azure Search](https://channel9.msdn.com/Events/Connect/2016/138), los siguientes pasos se demuestran en 6 minutos y 8 segundos.
>

1. Haga clic en **Explorador de búsqueda** en la barra de comandos.

   ![Comando del explorador de búsqueda][5]

2. Haga clic en **Change index** (Cambiar índice) en la barra de comandos para cambiar a *realestate-us-sample*.

   ![Comandos de índice y API][6]

3. Haga clic en **Definir versión de API** en la barra de comandos para ver cuáles son las API de REST que están disponibles. Las API de versión preliminar le proporcionan acceso a nuevas características que aún no se han lanzado a nivel general. Para las siguientes consultas, use la versión disponible con carácter general (2016-09-01), salvo que se indique lo contrario. 

    > [!NOTE]
    > La [API de REST de Azure Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) y la [biblioteca .NET](search-howto-dotnet-sdk.md#core-scenarios) son completamente equivalentes, pero el **Explorador de búsqueda** solo está preparado para administrar llamadas de REST. Acepta [sintaxis de consulta simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) y el [analizador de consultas completo de Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), además de todos los parámetros de búsqueda disponibles en las operaciones de [búsqueda en documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents).
    > 

4. En la barra de búsqueda, escriba las cadenas de consulta siguientes y haga clic en **Buscar**.

  ![Ejemplo de consulta de búsqueda][7]

**`search=seattle`**

+ El parámetro `search` se usa para la entrada de una búsqueda por palabra clave en la búsqueda de texto completo; en este caso, se devuelven listados de King County, en el estado de Washington, que contienen *Seattle* en los campos del documento que permiten búsqueda. 

+ El **Explorador de búsqueda** devuelve resultados en JSON, que es detallado y difícil de leer si los documentos tienen una estructura densa. En función de los documentos, puede que necesite escribir código que administre los resultados de búsqueda para extraer los elementos importantes. 

+ Los documentos se componen de todos los campos marcados como recuperables en el índice. Para ver los atributos del índice en el portal, haga clic en *realestate-us-sample* en el icono **Índices**.

**`search=seattle&$count=true&$top=100`**

+ El símbolo `&` se usa para anexar parámetros de búsqueda, que pueden especificarse en cualquier orden. 

+  El parámetro `$count=true` devuelve un recuento de la suma de todos los documentos devueltos. Puede comprobar las consultas de filtro mediante la supervisión de los cambios notificados por `$count=true`. 

+ El parámetro `$top=100` devuelve los 100 documentos mejor clasificados del total. De forma predeterminada, Azure Search devuelve las primeras 50 mejores coincidencias. Puede aumentar o disminuir la cantidad mediante `$top`.

**`search=*&facet=city&$top=2`**

+ `search=*` es una búsqueda vacía. Las búsquedas vacías buscan en todo. Una de las razones para enviar una búsqueda vacía es filtrar o clasificar el conjunto completo de documentos. Por ejemplo, quiere una estructura de navegación en facetas que conste de todas las ciudades del índice.

+  `facet` devuelve una estructura de navegación que puede pasar a un control de interfaz de usuario. Devuelve categorías y un recuento. En este caso, las categorías se basan en el número de ciudades. No hay ninguna agregación en Azure Search, pero puede aproximarla mediante `facet`, que proporciona un recuento de documentos de cada categoría.

+ `$top=2` devuelve dos documentos, lo que ilustra que puede usar `top` para reducir o aumentar los resultados.

**`search=seattle&facet=beds`**

+ Esta consulta es una faceta de "beds", en una búsqueda de texto de *Seattle*. `"beds"` se puede especificar como una faceta porque el campo está marcado como que se puede recuperar, filtrar y clasificar en el índice, y los valores que contiene (numéricos del 1 al 5), son adecuados para clasificar los listados en grupos (listados con 3 dormitorios, 4 dormitorios). 

+ Solo los campos que se pueden filtrar se pueden clasificar. Solo se pueden devolver en los resultados campos recuperables.

**`search=seattle&$filter=beds gt 3`**

+ El parámetro `filter` devuelve resultados que coincidan con los criterios que proporcionó. En este caso, más de 3 dormitorios. 

+ La sintaxis de filtro es una construcción de OData. Para más información, consulte la [sintaxis de filtro de OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

**`search=granite countertops&highlight=description`**

+ El proceso de resaltado de referencias se refiere al formato en el texto que coincide con la palabra clave, dadas las coincidencias encontradas en un campo determinado. Si el término de búsqueda está profundamente enterrado en una descripción, puede agregar resaltado de referencias para que sea más fácil detectarlo. En este caso, la frase con formato `"granite countertops"` es más fácil de ver en el campo de descripción.

**`search=mice&highlight=description`**

+ La búsqueda de texto completo busca formas de palabras con una semántica parecida. En este caso, los resultados de búsqueda contienen texto resaltado para "mouse", para hogares que tienen infestación de ratones, en respuesta a una búsqueda por palabra clave de "mice". Diferentes formas de la misma palabra pueden aparecer en los resultados debido al análisis lingüístico. 

+ Azure Search admite 56 analizadores de Lucene y Microsoft. El valor predeterminado usado por Azure Search es el analizador de Lucene estándar. 

**`search=samamish`**

+ Palabras mal escritas, como "samamish" en referencia a la meseta de Samammish en la zona de Seattle, no devuelven coincidencias en una búsqueda normal. Para controlar los errores de ortografía, puede usar la búsqueda aproximada, que se describe en el ejemplo siguiente.

**`search=samamish~&queryType=full`**

+ La búsqueda aproximada se habilita cuando se especifica el símbolo `~` y usa el analizador de consultas completo, que interpreta y analiza correctamente la sintaxis `~`. 

+ La búsqueda aproximada está disponible cuando opta por el analizador de consultas completo, que tiene lugar cuando establece `queryType=full`. Para más información sobre los escenarios de consulta habilitados por el analizador de consultas completo, consulte [Lucene query syntax in Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) (Sintaxis de consulta de Lucene en Azure Search).

+ Cuando `queryType` no se especifica, se usa el analizador de consultas sencillo de manera predeterminada. El analizador de consultas sencillo es más rápido, pero si necesita búsqueda aproximada, expresiones regulares, búsqueda de proximidad u otros tipos de consultas avanzadas, necesitará la sintaxis completa. 

**`search=*&$count=true&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5`**

+ Se admite la búsqueda geoespacial mediante el [tipo de dato edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) en un campo que contenga coordenadas. La búsqueda geográfica es un tipo de filtro, que se especifica en la [sintaxis de filtro de OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

+ La consulta de ejemplo filtra todos los resultados por datos de posición, donde los resultados sean inferiores a 5 kilómetros desde un punto dado (especificado como coordenadas de latitud y longitud). Al agregar `$count`, puede ver cuántos resultados se devuelven al cambiar la distancia o las coordenadas. 

+ La búsqueda geoespacial resulta útil si su aplicación de búsqueda tiene una característica "buscar cerca de mí" o utiliza la navegación mediante mapas. No obstante, no es una búsqueda de texto completo. Si tiene requisitos de usuario de buscar en una ciudad o país por nombre, agregue campos que contengan nombres de ciudades o de países, además de coordenadas.

## <a name="next-steps"></a>Pasos siguientes

+ Modifique cualquiera de los objetos que acaba de crear. Después de ejecutar el Asistente una vez, puede volver atrás y ver o modificar componentes individuales: índice, indexador u origen de datos. No se permiten algunas modificaciones en el índice, como cambiar el tipo de datos de campo, pero la mayoría de las propiedades y configuraciones son modificables.

  Para ver los componentes individuales, haga clic en los iconos **Índice**, **Indexador** u **Orígenes de datos** en el panel para mostrar una lista de objetos existentes. Para más información sobre las modificaciones del índice que no requieren generarlo de nuevo, consulte [Update Index (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/update-index) (Actualización del índice [API de REST de Azure Search]).

+ Pruebe las herramientas y los pasos con otros orígenes de datos. El conjunto de datos de ejemplo, `realestate-us-sample`, procede de una instancia de Azure SQL Database que Azure Search puede rastrear. Además de Azure SQL Database, Azure Search puede rastrear e inferir un índice a partir de estructuras de datos planas en Azure Table Storage, Blob Storage, SQL Server en una máquina virtual de Azure y Azure Cosmos DB. Todos estos orígenes de datos se admiten en el asistente. En el código, puede rellenar un índice fácilmente con un *indexador*.

+ Todos los demás orígenes de datos sin indexador se admiten mediante un modelo de inserción, donde el código inserta conjuntos de filas nuevos y modificados de JSON en el índice. Para más información, consulte [Add, update, or delete documents in Azure Search](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) (Agregar, actualizar o eliminar documentos en Azure Search).

Para más información sobre otras características mencionadas en este artículo, visite los vínculos siguientes:

* [Introducción a los indexadores](search-indexer-overview.md)
* [Crear índice (incluye una explicación detallada de los atributos de índice)](https://docs.microsoft.com/rest/api/searchservice/create-index)
* [Explorador de búsqueda](search-explorer.md)
* [Buscar documentos (incluye ejemplos de sintaxis de consulta)](https://docs.microsoft.com/rest/api/searchservice/search-documents)


<!--Image references-->
[1]: ./media/search-get-started-portal/tiles-indexers-datasources2.png
[2]: ./media/search-get-started-portal/import-data-cmd2.png
[3]: ./media/search-get-started-portal/realestateindex2.png
[4]: ./media/search-get-started-portal/indexers-inprogress2.png
[5]: ./media/search-get-started-portal/search-explorer-cmd2.png
[6]: ./media/search-get-started-portal/search-explorer-changeindex-se2.png
[7]: ./media/search-get-started-portal/search-explorer-query2.png
[8]: ./media/search-get-started-portal/realestate-indexer2.png
[9]: ./media/search-get-started-portal/import-datasource-sample2.png