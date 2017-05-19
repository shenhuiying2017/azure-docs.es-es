---
title: "Solución de problemas de rendimiento y optimización de bases de datos | Microsoft Docs"
description: "Aplique las recomendaciones de rendimiento a SQL Database y aprenda a obtener información acerca del rendimiento de las consultas que se ejecutan en la base de datos"
metakeywords: azure sql database performance monitoring recommendation
services: sql-database
documentationcenter: 
manager: jhubbard
author: jan-eng
ms.assetid: 
ms.service: sql-database
ms.custom: performance
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 0bdb111257053e4a57510a72ab683bc9c890d2f7
ms.contentlocale: es-es
ms.lasthandoff: 05/09/2017


---
# <a name="troubleshoot-performance-issues-and-optimize-your-database"></a>Solución de problemas de rendimiento y optimización de bases de datos

Entre los motivos comunes para un rendimiento deficiente de la base de datos se encuentran índices que faltan y consultas mal optimizadas. En este tutorial, aprenderá a:
> [!div class="checklist"]
> * Revisar, aplicar y revertir las recomendaciones para la mejora de rendimiento
> * Buscar consultas con un uso intensivo de los recursos
> * Buscar consultas de ejecución prolongada

> Necesita una carga de trabajo continua en una base de datos con problemas de rendimiento (por ejemplo, falta un índice para obtener una recomendación).
>

## <a name="log-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

Inicie sesión en [Azure Portal](https://portal.azure.com/).

## <a name="review-and-apply-a-recommendation"></a>Revisión y aplicación de una recomendación

Siga estos pasos para aplicar una recomendación del sistema a la base de datos:

1. Haga clic en el menú **Recomendaciones de rendimiento** de la hoja de la base de datos.

    ![recomendación de rendimiento](./media/sql-database-performance-tutorial/perf_recommendations.png)

2. En la lista de recomendaciones, seleccione una recomendación activa. En este ejemplo, Create Index.

    ![seleccionar recomendación](./media/sql-database-performance-tutorial/create_index.png)

3. Aplique la recomendación, para lo que debe hacer clic en el botón **Aplicar**. Opcionalmente, revise los detalles de la recomendación y vea el script de T-SQL que se ejecuta, para lo que debe hacer clic en el botón **Ver script**.

    ![aplicar recomendación](./media/sql-database-performance-tutorial/apply.png)

4. [Opcional] Habilite el ajuste automático para que las recomendaciones se apliquen automáticamente.

    ![ajuste automático](./media/sql-database-performance-tutorial/auto_tuning.png)

## <a name="revert-a-recommendation"></a>Reversión de una recomendación

Database Advisor supervisa todas las recomendaciones implementadas. Si una recomendación no mejora la carga de trabajo, se revertirá automáticamente. Cualquier recomendación se puede revertir, pero en la mayoría de los casos no es necesario. Para revertir una recomendación:

1. Vaya al menú de recomendaciones de rendimiento y seleccione una de las recomendaciones aplicadas.

    ![seleccionar recomendación](./media/sql-database-performance-tutorial/select.png)

2. En la vista de detalles, haga clic en **Revertir**.

    ![revertir recomendación](./media/sql-database-performance-tutorial/revert.png)

## <a name="find-the-query-that-consumes-the-most-resources"></a>Busque la consulta que consume más recursos

Siga estos pasos para buscar la consulta que consume más recursos:

1. Haga clic en el menú **Información de rendimiento de consultas** de la hoja de la base de datos.

    ![información de consultas](./media/sql-database-performance-tutorial/query_perf_insights.png)

2. Seleccione de un tipo de recurso.

    ![información de consultas](./media/sql-database-performance-tutorial/select_resource_type.png)

3. Seleccione la primera consulta de la tabla.

    ![información de consultas](./media/sql-database-performance-tutorial/select_query.png)

4. Revise los detalles de la consulta.

    ![información de consultas](./media/sql-database-performance-tutorial/query_details.png)

## <a name="find-the-longest-running-query"></a>Busque la consulta que más tarda en ejecutarse

1. Vaya a Información de rendimiento de consultas y seleccione la pestaña **Consultas de larga ejecución**.

    ![información de consultas](./media/sql-database-performance-tutorial/long_running.png)

3. Seleccione la primera consulta de la tabla.

    ![información de consultas](./media/sql-database-performance-tutorial/select_first_query.png)

4. Revise los detalles de la consulta.

    ![información de consultas](./media/sql-database-performance-tutorial/review_query_details.png)



## <a name="next-steps"></a>Pasos siguientes 
Entre los motivos comunes para un rendimiento deficiente de la base de datos se encuentran índices que faltan y consultas mal optimizadas. En este tutorial ha aprendido a:
> [!div class="checklist"]
> * Revisar, aplicar y revertir las recomendaciones para la mejora de rendimiento
> * Buscar consultas con un uso intensivo de los recursos
> * Buscar consultas de ejecución prolongada

[Sugerencias para la optimización del rendimiento de Base de datos SQL](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-performance)

