---
title: "Supervisión del uso y estadísticas en un servicio Azure Search | Microsoft Docs"
description: "Realice el seguimiento del consumo de recursos y el tamaño de índice para Búsqueda de Azure, un servicio de búsqueda hospedado en la nube en Microsoft Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 122948de-d29a-426e-88b4-58cbcee4bc23
ms.service: search
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/29/2016
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6e40acb01d6297aa66090859533fd48c108a9a88


---
# <a name="monitor-usage-and-query-metrics-in-an-azure-search-service"></a>Supervisión de uso y consulta de métricas en un servicio Azure Search
Azure Search recopila estadísticas sobre la ejecución de consultas en el nivel de servicio, lo que incluye consultas por segundo (QPS), latencia y el porcentaje de consultas rechazadas si el volumen supera la capacidad. Las métricas son visibles en el portal mediante la hoja Supervisión.

   ![Captura de pantalla de la actividad de QPS][5]

En este artículo se describen las métricas para el total del servicio. Para más información sobre la actividad en el nivel del índice, habilite el análisis del tráfico de búsqueda y use Power BI para verlo. Visite [Análisis de tráfico de búsqueda para Búsqueda de Azure](search-traffic-analytics.md) para comenzar.

## <a name="view-query-throughput-statistics"></a>Visualización de la estadísticas de rendimiento de consultas
Haga clic en el icono de Supervisión en el panel de servicio para abrir la hoja Supervisión.

   ![Icono de Supervisión][2]

Se registra la actividad de consulta en el nivel de servicio en lo referente a la ejecución de consultas, la latencia y la limitación. Continuamente se recopilan métricas, si bien puede tardar un rato en mostrarse en el portal la actividad más reciente. 

Haga clic en un icono de métrica para ver detalles, agregar alertas, habilitar diagnósticos o editar el gráfico.

  ![Comandos de la hoja Métrica][4]

### <a name="set-up-alerts"></a>Configuración de alertas
En la página de detalles de las métricas, puede configurar alertas para desencadenar una notificación por correo electrónico si la actividad de ejecución de consultas, latencia o limitación supera los criterios definidos para ella.

### <a name="enable-diagnostics"></a>Habilitación de diagnósticos
Al activar el diagnóstico, puede configurar dónde se almacenarán los datos de diagnóstico, si se incluirán registros y métricas de operaciones y cuánto tiempo se conservarán los datos.

### <a name="change-chart-type-and-data-collection-interval"></a>Cambio del tipo de gráfico y del intervalo de recopilación de datos
Para cada métrica, puede hacer clic en **Editar** para cambiar la visualización de gráfico de líneas por un gráfico de barras o modificar el eje x para abarcar un intervalo de tiempo diferente.

  ![Configuración del intervalo de tiempo][3]

## <a name="view-counts-and-resource-usage-in-the-portal"></a>Visualización de recuentos y uso de recursos en el portal
Llevar un seguimiento del crecimiento de los índices y el tamaño de los documentos puede ayudarle a ajustar la capacidad de forma proactiva antes de alcanzar el límite superior que haya establecido para el servicio. 

Para supervisar el uso de recursos, y ver los recuentos y las estadísticas del servicio en el [portal](https://portal.azure.com). Si va a crear una herramienta de administración de servicios personalizada, también puede obtener la información mediante programación.

1. Inicie sesión en el [portal](https://portal.azure.com). 
2. Abra el panel del servicio Búsqueda de Azure. Puede buscar los mosaicos del servicio en la página principal, o puede desplazarse hasta el servicio usando Explorar, en la barra de salto. 

La sección Uso incluye un medidor que indica la parte de los recursos disponibles que está en uso. Para más información sobre los límites de cada servicio en cuanto a índices, documentos y almacenamiento, consulte [Límites de servicio](search-limits-quotas-capacity.md).

  ![Icono de Uso][1]

> [!NOTE]
> La captura de pantalla anterior es del servicio Gratuito, que tiene como máximo una réplica y una partición y solo puede hospedar 3 índices, 10 000 documentos o 50 MB de datos, lo que ocurra primero. Los servicios creados en un nivel Básico o Estándar tienen límites de servicio mucho mayores. Para más información sobre cómo elegir un nivel, consulte [Selección de un nivel o una SKU](search-sku-tier.md).
> 
> 

### <a name="get-index-statistics-using-the-rest-api"></a>Obtención de estadísticas de índice utilizando la API de REST
La API de REST de Búsqueda de Azure y el SDK para .NET proporcionan acceso mediante programación a las métricas del servicio.  Si está utilizando [indizadores](https://msdn.microsoft.com/library/azure/dn946891.aspx) para cargar un índice desde una base de datos SQL de Azure o desde DocumentDB, está disponible una API adicional para obtener las cifras que necesita. 

* [Obtención de estadísticas de índice](https://msdn.microsoft.com/library/azure/dn798942.aspx)
* [Recuento de documentos](https://msdn.microsoft.com/library/azure/dn798924.aspx)
* [Obtención del estado del indizador](https://msdn.microsoft.com/library/azure/dn946884.aspx)

## <a name="next-steps"></a>Pasos siguientes
Examine [Escalado de réplicas y particiones](search-limits-quotas-capacity.md) como guía saber cómo equilibrar la asignación de particiones y réplicas para un servicio existente. 

Visite [Administración de servicios de Azure Search en Microsoft Azure](search-manage.md) para más información sobre la administración de servicios, o [Rendimiento y optimización](search-performance-optimization.md) para que le sirva de guía de ajuste.

<!--Image references-->
[1]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG
[2]: ./media/search-monitor-usage/AzSearch-Monitor-Tile.PNG
[3]: ./media/search-monitor-usage/AzSearch-Monitor-Intervals.PNG
[4]: ./media/search-monitor-usage/AzSearch-Monitor-AlertCmd.PNG
[5]: ./media/search-monitor-usage/AzSearch-Monitor-BarChart.PNG








<!--HONumber=Nov16_HO3-->


