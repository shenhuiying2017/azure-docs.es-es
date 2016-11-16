---
title: "Realización de una consulta en el índice de Azure Search mediante Azure Portal | Microsoft Docs"
description: "Emita una consulta de búsqueda en el Explorador de búsqueda del Portal de Azure."
services: search
manager: jhubbard
documentationcenter: 
author: ashmaka
ms.assetid: 8e524188-73a7-44db-9e64-ae8bf66b05d3
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a23372112e17703a3399e1bdc9eaf73b85a1f80d


---
# <a name="query-your-azure-search-index-using-the-azure-portal"></a>Realización de una consulta en el índice de Búsqueda de Azure mediante el Portal de Azure
> [!div class="op_single_selector"]
> * [Información general](search-query-overview.md)
> * [Portal](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

Esta guía le mostrará cómo consultar en el índice de Búsqueda de Azure en el Portal de Azure.

Antes de comenzar este tutorial, debe haber [creado ya un índice de Azure Search](search-what-is-an-index.md) y [haberlo rellenado con datos](search-what-is-data-import.md).

## <a name="i-go-to-your-azure-search-blade"></a>I. Vaya a la hoja Búsqueda de Azure.
1. Haga clic en "Todos los recursos" en el menú en el lado izquierdo del [Portal de Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Seleccione el servicio Búsqueda de Azure.

## <a name="ii-select-the-index-you-would-like-to-search"></a>II. Seleccione el índice que desea buscar
1. Seleccione el índice que desea buscar en el icono "Índices".

![](./media/search-explorer/pick-index.png)

## <a name="iii-click-on-the-search-explorer-tile"></a>III. Haga clic en el icono "Explorador de búsqueda"
![](./media/search-explorer/search-explorer-tile.png)

## <a name="iii-start-searching"></a>III. Inicio de la búsqueda
1. Para buscar en el índice de Búsqueda de Azure, empiece a escribir en el campo "*Cadena de consulta*" y, después, presione"**Buscar**".
   
   * Cuando utilice el Explorador de búsqueda, puede especificar cualquiera de los [parámetros de consulta](https://msdn.microsoft.com/library/dn798927.aspx)
2. En la sección "*Resultados*", se presentarán los resultados de la consulta en el archivo JSON sin formato que podría recibir en un cuerpo de respuesta HTTP al emitir solicitudes de búsqueda en la API de REST de Búsqueda de Azure.
3. La cadena de consulta se analiza automáticamente en la dirección URL de solicitud adecuada para enviar una solicitud HTTP con la API de REST de Búsqueda de Azure

![](./media/search-explorer/search-bar.png)




<!--HONumber=Nov16_HO2-->


