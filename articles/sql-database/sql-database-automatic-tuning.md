---
title: "SQL Database: ajuste automático | Microsoft Docs"
description: "SQL Database analiza una consulta SQL y se adapta automáticamente a la carga de trabajo del usuario."
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
ms.workload: NA
ms.date: 06/05/2017
ms.author: jovanpop
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: f5552793db2d89542782b7d9e8f996314f62e429
ms.contentlocale: es-es
ms.lasthandoff: 06/17/2017


---
# <a name="automatic-tuning"></a>Ajuste automático

Azure SQL Database es un servicio de datos totalmente administrado que supervisa las consultas que se ejecutan en la base de datos y puede mejorar automáticamente el rendimiento de la carga de trabajo. Azure SQL Database posee un mecanismo integrado de inteligencia que puede ajustar y mejorar automáticamente el rendimiento de las consultas mediante la adaptación dinámica de la base de datos a la carga de trabajo. Es posible que el ajuste automático en Azure SQL Database sea una de las características más importantes que puede habilitar en Azure SQL Database para optimizar el rendimiento de las consultas.

Consulte este artículo para conocer los pasos para [habilitar el ajuste automático](sql-database-automatic-tuning-enable.md) mediante Azure Portal.

## <a name="why-automatic-tuning"></a>¿Por qué el ajuste automático?

Una de las principales tareas en la administración clásica de bases de datos es supervisar la carga de trabajo e identificar consultas SQL esenciales, índices que se deberían agregar para mejorar el rendimiento e índices usados con poca frecuencia. Azure SQL Database proporciona información detallada sobre las consultas y los índices que necesita supervisar. Sin embargo, supervisar constantemente una base de datos es una tarea ardua y tediosa, sobre todo cuando se trabaja con muchas bases de datos. La administración de un número ingente de bases de datos podría ser imposible de realizar eficazmente, ni siquiera con todas las herramientas e informes que proporcionan Azure SQL Database y Azure Portal. En lugar de supervisar y ajustar la base de datos manualmente, puede considerar la posibilidad de delegar algunas de las acciones de supervisión y ajuste en Azure SQL Database con la característica de ajuste automático. 


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-in-the-Enterprise/Enabling-Azure-SQL-Database-Auto-Tuning-at-Scale-for-Microsoft-IT/player]
>

## <a name="how-does-automatic-tuning-work"></a>¿Cómo funciona el ajuste automático?

Azure SQL Database tiene un proceso continuo de supervisión y análisis de rendimiento que obtiene información constante sobre las características de la carga de trabajo e identifica posibles problemas y mejoras.

![Proceso de ajuste automático](./media/sql-database-automatic-tuning/tuning-process.png)

Este proceso permite que Azure SQL Database se adapte de forma dinámica a la carga de trabajo mediante la búsqueda de qué índices y planes pueden mejorar el rendimiento de las cargas de trabajo y qué índices las afectan negativamente. En función de lo que encuentre, el ajuste automático pone en práctica acciones de optimización que mejoran el rendimiento de la carga de trabajo. Además, Azure SQL Database supervisa continuamente el rendimiento después de cualquier cambio realizado por el ajuste automático para asegurarse de que mejora el rendimiento de la carga de trabajo. Cualquier acción que no mejore el rendimiento se revierte automáticamente. Este proceso de comprobación es una característica clave que garantiza que cualquier cambio realizado por el ajuste automático no disminuya el rendimiento de la carga de trabajo.

Hay dos aspectos del ajuste automático disponibles en Azure SQL Database:

 -  **Administración automática de índices** que identifica tanto los índices que se deberían agregar a la base de datos como los que se deberían quitar.
 -  **Corrección automática de planes** (próximamente, ya disponible en SQL Server 2017) que identifica planes problemáticos y corrige los problemas de rendimiento de los planes de SQL.

## <a name="automatic-index-management"></a>Administración automática de índices

En Azure SQL Database, la administración de índices resulta sencilla porque Azure SQL Database obtiene información acerca de la carga de trabajo y se asegura de que los datos estén siempre indexados de forma óptima. El diseño adecuado de índices es fundamental para un rendimiento óptimo de la carga de trabajo y la administración automática de índices puede ayudar a optimizar los índices. La administración automática de índices puede corregir problemas de rendimiento en bases de datos indexadas incorrectamente o mantener y mejorar los índices en el esquema de base de datos existente. 

### <a name="why-do-you-need-index-management"></a>¿Por qué se necesita la administración de índices?

Los índices aceleran algunas de las consultas que leen datos de las tablas; sin embargo, pueden ralentizar las consultas que actualizan datos. Debe analizar cuidadosamente cuándo se debe crear un índice y qué columnas debe incluir en él. Es posible que algunos índices no sean necesarios pasado un tiempo. Por lo tanto, debería identificar y quitar periódicamente los índices que no reporten ventajas. Si pasa por alto los índices no usados, el rendimiento de las consultas que actualizan datos podría reducirse sin ninguna ventaja para las consultas que leen datos. Además, los índices no usados afectan al rendimiento general del sistema porque las actualizaciones adicionales necesitan tareas de registro innecesarias.

Para encontrar el conjunto óptimo de índices que mejore el rendimiento de las consultas que leen datos de las tablas y tenga un impacto mínimo en las actualizaciones, es posible que se requiera un análisis complejo y continuado.

Azure SQL Database usa inteligencia integrada y reglas avanzadas que analizar las consultas e identificar los índices que serían óptimos para las cargas de trabajo actuales y los que se deberían quitar. Azure SQL Database se asegura de que tenga un conjunto mínimo necesario de índices para optimizar las consultas que leen datos, con menor impacto sobre las demás consultas.

### <a name="how-to-identify-indexes-that-need-to-be-changed-in-your-database"></a>¿Cómo se identifican los índices que deben cambiarse en la base de datos?

Azure SQL Database facilita el proceso de administración de índices. En lugar del tedioso proceso de análisis manual de la carga de trabajo y la supervisión de índices, Azure SQL Database analiza la carga de trabajo, identifica las consultas que podrían ejecutarse más rápido con un nuevo índice e identifica los índices no usados o duplicados. Para más información sobre la identificación de los índices que deben cambiarse, consulte cómo [buscar recomendaciones de índices en Azure Portal](sql-database-advisor-portal.md).

### <a name="automatic-index-management-considerations"></a>Consideraciones sobre la administración automática de índices

Si descubre que las reglas integradas mejoran el rendimiento de la base de datos, podría dejar que Azure SQL Database administre automáticamente los índices.

Las acciones necesarias para crear los índices necesarios en instancias de Azure SQL Database podrían consumir recursos y afectar temporalmente al rendimiento de la carga de trabajo. Para minimizar el impacto de la creación de índices sobre el rendimiento de la carga de trabajo, Azure SQL Database busca el período de tiempo adecuado para cualquier operación de administración de índices. Se pospone la acción de ajuste si la base de datos necesita recursos para ejecutar la carga de trabajo, y se inicia cuando la base de datos tenga suficientes recursos no utilizados que estén disponibles para la tarea de mantenimiento. Una característica importante de la administración automática de índices es la comprobación de las acciones. Cuando Azure SQL Database crea o quita un índice, un proceso de supervisión analiza el rendimiento de la carga de trabajo para comprobar que la acción lo mejore. Si no ha reportado una mejora considerable, se revierte la acción de inmediato. De esta manera, Azure SQL Database garantiza que las acciones automáticas no surtan un efecto negativo sobre el rendimiento de la carga de trabajo. Los índices creados por el ajuste automático son transparentes para la operación de mantenimiento en el esquema subyacente. Los cambios de esquema, como quitar columnas o cambiarlas de nombre, no quedan bloqueados por la presencia de índices creados de forma automática. Los índices que Azure SQL Database crea automáticamente se quitan de inmediato cuando se quitan la tabla o las columnas relacionadas.

## <a name="automatic-plan-choice-correction"></a>Corrección automática de la selección de plan

La corrección automática de planes es una nueva característica de ajuste automático que se agregó en SQL Server de 2017 CTP2.0 y que identifica los planes de SQL que no funcionan correctamente. Esta opción de ajuste automático estará disponible en breve en Azure SQL Database. Puede ver más información en el artículo sobre el [ajuste automático en SQL Server 2017](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

## <a name="next-steps"></a>Pasos siguientes

- Para habilitar la característica de ajuste automático en Azure SQL Database y permitir que administre totalmente la carga de trabajo, consulte [Habilitación del ajuste automático](sql-database-automatic-tuning-enable.md).
- Para usar el ajuste manual, puede revisar las [recomendaciones de ajuste en Azure Portal](sql-database-advisor-portal.md) y aplicar manualmente las que mejoren el rendimiento de sus consultas.

