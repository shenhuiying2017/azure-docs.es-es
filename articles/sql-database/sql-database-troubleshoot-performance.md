---
title: "Optimización de la supervisión y el rendimiento: Azure SQL Database | Microsoft Docs"
description: "Sugerencias para la optimización del rendimiento de Azure SQL Database a través de la evaluación y la mejora."
services: sql-database
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
keywords: "SQL optimización del rendimiento, base de datos optimización del rendimiento, sugerencias para la optimización del rendimiento de SQL, optimización del rendimiento de Base de datos SQL"
ms.assetid: eb7b3f66-3b33-4e1b-84fb-424a928a6672
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: v-shysun
ms.openlocfilehash: 1791c56f86ee1997177daa95638c4f14068f8115
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2017
---
# <a name="monitoring-and-performance-tuning"></a>Optimización de la supervisión y el rendimiento

Azure SQL Database se administra automáticamente y es un servicio de datos flexible donde, de manera sencilla, puede supervisar el uso, agregar o quitar recursos (CPU, memoria, e/s), buscar recomendaciones que pueden mejorar el rendimiento de la base de datos o permitir que la base de datos se adapte a la carga de trabajo y optimice el rendimiento automáticamente.

En este artículo se proporciona información general de las opciones de optimización de la supervisión y el rendimiento disponibles en Azure SQL Database.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="monitoring-and-troubleshooting-database-performance"></a>Supervisión y solución de problemas de rendimiento de base de datos

Azure SQL Database le permite supervisar el uso de la base de datos e identificar las consultas que pueden producir los problemas de rendimiento, todo ello de manera sencilla. Puede supervisar el rendimiento de la base de datos mediante Azure Portal o vistas del sistema. Dispone de las siguientes opciones para supervisar el rendimiento de la base de datos y solucionar problemas relacionados con dicho rendimiento:

1. En [Azure Portal](https://portal.azure.com), haga clic en **Bases de datos SQL**, seleccione la base de datos y luego use el gráfico de supervisión para buscar recursos que se acerquen a su máximo. El consumo de DTU se muestra de manera predeterminada. Haga clic en **Editar** para cambiar el intervalo de tiempo y los valores que se muestran.
2. Use [Información de rendimiento de consultas](sql-database-query-performance.md) para identificar las consultas que consumen la mayoría de los recursos.
3. Puede usar vistas de administración dinámica (DMV), eventos extendidos (`XEvents`) y el almacén de consultas en SSMS para obtener parámetros de rendimiento en tiempo real.

Consulte el [tema de la guía de rendimiento](sql-database-performance-guidance.md) para buscar las técnicas que puede usar para mejorar el rendimiento de Azure SQL Database si identifica algún problema al usar estos informes o vistas.

> [!IMPORTANT] 
> Le recomendamos usar siempre la versión más reciente de Management Studio para que pueda estar siempre al día de las actualizaciones de Microsoft Azure y SQL Database. [Actualice SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
>

## <a name="optimize-database-to-improve-performance"></a>Optimización de la base de datos para mejorar el rendimiento

Azure SQL Database le permite identificar las oportunidades para mejorar y optimizar el rendimiento de las consultas sin cambiar los recursos mediante la revisión de [recomendaciones para la optimización del rendimiento](sql-database-advisor.md). Entre los motivos comunes para un rendimiento deficiente de la base de datos se encuentran índices que faltan y consultas mal optimizadas. Puede aplicar estas recomendaciones de optimización para mejorar el rendimiento de la carga de trabajo.
También puede permitir que Azure SQL Database [optimice el rendimiento de las consultas automáticamente](sql-database-automatic-tuning.md) aplicando todas las recomendaciones identificadas y comprobando que mejoran el rendimiento de la base de datos. Puede usar las siguientes opciones para mejorar el rendimiento de la base de datos:

1. Usar [SQL Database Advisor](sql-database-advisor-portal.md) para ver recomendaciones para crear y quitar índices, parametrizar consultas y solucionar problemas de esquema.
2. [Habilitar la optimización automática](sql-database-automatic-tuning-enable.md) y dejar que SQL Azure Database corrija problemas de rendimiento automáticamente.

## <a name="improving-database-performance-with-more-resources"></a>Mejora del rendimiento de la base de datos con más recursos

Por último, si no hay elementos para procesar que puedan mejorar el rendimiento de la base de datos, puede cambiar la cantidad de recursos disponibles en Azure SQL Database. Puede asignar más recursos cambiando el [nivel de servicio](sql-database-service-tiers.md) de una base de datos independiente o aumentar las eDTU de un grupo de bases de datos elásticas en cualquier momento.
1. Para las bases de datos independientes, puede [cambiar los niveles de servicio](sql-database-service-tiers.md) a petición para mejorar el rendimiento de la base de datos.
2. Para varias bases de datos, considere el uso de [grupos elásticos](sql-database-elastic-pool-guidance.md) para escalar recursos automáticamente.

## <a name="tune-and-refactor-application-or-database-code"></a>Optimización y refactorización del código de la aplicación o base de datos

Puede cambiar el código de la aplicación para usar la base de datos, cambiar índices, forzar planes o usar sugerencias de forma más óptima para adaptar manualmente la base de datos a la carga de trabajo. En el artículo sobre el [tema de la guía de rendimiento](sql-database-performance-guidance.md) puede buscar algunas pautas y sugerencias para la optimización manual y volver a escribir el código.


## <a name="next-steps"></a>Pasos siguientes

- Para habilitar la característica de ajuste automático en Azure SQL Database y permitir que administre totalmente la carga de trabajo, consulte [Habilitación del ajuste automático](sql-database-automatic-tuning-enable.md).
- Para utilizar la optimización manual, puede revisar [SQL Database Advisor mediante Azure Portal](sql-database-advisor-portal.md) y aplicar manualmente las recomendaciones que mejoran el rendimiento de las consultas.
- Cambiar los recursos que están disponibles en la base de datos cambiando los [niveles de servicio de Azure SQL Database](sql-database-performance-guidance.md).
