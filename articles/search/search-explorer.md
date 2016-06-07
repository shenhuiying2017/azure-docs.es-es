<properties
    pageTitle="Realización de una consulta en un índice de Búsqueda de Azure mediante el Portal de Azure | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
    description="Emita una consulta de búsqueda en el Explorador de búsqueda del Portal de Azure."
    services="search"
    documentationCenter=""
	authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="05/31/2016"
    ms.author="ashmaka"
/>
# Realización de una consulta en el índice de Búsqueda de Azure mediante el Portal de Azure
> [AZURE.SELECTOR]
- [Información general](search-query-overview.md)
- [Portal](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Esta guía le mostrará cómo consultar en el índice de Búsqueda de Azure en el Portal de Azure.

Antes de comenzar este tutorial, debe haber [creado ya un índice de Búsqueda de Azure](search-what-is-an-index.md) y [haberlo rellenado con datos](search-what-is-data-import.md).

## I. Vaya a la hoja Búsqueda de Azure.
1. Haga clic en "Todos los recursos" en el menú en el lado izquierdo del [Portal de Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Seleccione el servicio Búsqueda de Azure.

## II. Seleccione el índice que desea buscar
1. Seleccione el índice que desea buscar en el icono "Índices".

![](./media/search-explorer/pick-index.png)

## III. Haga clic en el icono "Explorador de búsqueda"
![](./media/search-explorer/search-explorer-tile.png)

## III. Inicio de la búsqueda
1. Para buscar en el índice de Búsqueda de Azure, empiece a escribir en el campo "*Cadena de consulta*" y, después, presione"**Buscar**".
 * Cuando utilice el Explorador de búsqueda, puede especificar cualquiera de los [parámetros de consulta](https://msdn.microsoft.com/library/dn798927.aspx)

2. En la sección "*Resultados*", se presentarán los resultados de la consulta en el archivo JSON sin formato que podría recibir en un cuerpo de respuesta HTTP al emitir solicitudes de búsqueda en la API de REST de Búsqueda de Azure.
3. La cadena de consulta se analiza automáticamente en la dirección URL de solicitud adecuada para enviar una solicitud HTTP con la API de REST de Búsqueda de Azure

![](./media/search-explorer/search-bar.png)

<!---HONumber=AcomDC_0601_2016-->