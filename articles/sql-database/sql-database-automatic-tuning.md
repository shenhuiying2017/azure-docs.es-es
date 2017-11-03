---
title: "Azure SQL Database: ajuste automático | Microsoft Docs"
description: "Azure SQL Database analiza una consulta SQL y se adapta automáticamente a la carga de trabajo del usuario."
services: sql-database
documentationcenter: 
author: jovanpop-msft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 09/19/2017
ms.author: jovanpop
ms.openlocfilehash: 10f8cca8e519b28f0fe29605419b860f73e04a87
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Ajuste automático en Azure SQL Database

Azure SQL Database es un servicio de datos totalmente administrado que supervisa las consultas que se ejecutan en la base de datos y mejora automáticamente el rendimiento de la carga de trabajo de la base de datos. Azure SQL Database posee un mecanismo integrado de inteligencia de [ajuste automático](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) que puede ajustar y mejorar automáticamente el rendimiento de las consultas mediante la adaptación dinámica de la base de datos a la carga de trabajo. Es posible que el ajuste automático en Azure SQL Database sea una de las características más importantes que puede habilitar en Azure SQL Database para optimizar el rendimiento de las consultas.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-in-the-Enterprise/Enabling-Azure-SQL-Database-Auto-Tuning-at-Scale-for-Microsoft-IT/player]
>

## <a name="automatic-tuning-options"></a>Opciones de ajuste automático

Las opciones de [ajuste automático](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) disponibles en Azure SQL Database son:
 1. **CREATE INDEX**, que identifica los índices que pueden mejorar el rendimiento de la carga de trabajo, crea los índices y comprueba que mejoran el rendimiento de las consultas.
 2. **DROP INDEX**, que identifica los índices duplicados y redundantes y aquellos que no se han usado durante un período largo de tiempo.
 3. **PLAN REGRESSION CORRECTION**, que identifica consultas SQL que usan un plan de ejecución más lento que el plan correcto anterior, y utiliza el último plan correcto conocido en lugar del plan revertido.

Azure SQL Database identifica recomendaciones de **CREATE INDEX**, **DROP INDEX** y **PLAN REGRESSION CORRECTION** que pueden optimizar su base de datos y las muestra en Azure Portal. Para más información sobre la identificación de los índices que deben cambiarse, consulte cómo [buscar recomendaciones de índices en Azure Portal](sql-database-advisor-portal.md). Puede aplicar las recomendaciones manualmente mediante el portal o puede dejar que Azure SQL Database las aplique automáticamente, supervise la carga de trabajo después del cambio y compruebe que la recomendación ha mejorado el rendimiento de la carga de trabajo.

Las opciones de ajuste automático pueden activarse o desactivarse de forma independiente por base de datos. También se pueden configurar en el servidor lógico y aplicarse en cada base de datos que herede la configuración del servidor. Se recomienda configurar las opciones de [ajuste automático](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) en el servidor de forma que se herede la configuración en las bases de datos del servidor para configurar el ajuste automático, ya que este método simplifica la administración de las opciones de ajuste automático en un gran número de bases de datos.

Consulte este artículo para conocer los pasos para [habilitar el ajuste automático](sql-database-automatic-tuning-enable.md) mediante Azure Portal.

## <a name="next-steps"></a>Pasos siguientes

- Para habilitar la característica de ajuste automático en Azure SQL Database y permitir que administre totalmente la carga de trabajo, consulte [Habilitación del ajuste automático](sql-database-automatic-tuning-enable.md).
- Para usar el ajuste manual, puede revisar las [recomendaciones de ajuste en Azure Portal](sql-database-advisor-portal.md) y aplicar manualmente las que mejoren el rendimiento de sus consultas.
- Obtenga más información sobre la inteligencia integrada para optimizar [Azure SQL Database](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Obtenga más información sobre el [ajuste automático](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) en Azure SQL Database y SQL Server 2017.
