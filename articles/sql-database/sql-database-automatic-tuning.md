---
title: "Azure SQL Database: ajuste automático | Microsoft Docs"
description: "Azure SQL Database analiza una consulta SQL y se adapta automáticamente a la carga de trabajo del usuario."
services: sql-database
documentationcenter: 
author: jovanpop-msft
manager: drasumic
editor: danimir
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 11/08/2017
ms.author: jovanpop
ms.openlocfilehash: 50d1548637ecc6862f680f73e333e8d7f8d20e39
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Ajuste automático en Azure SQL Database

El ajuste automático de Azure SQL Database proporciona un rendimiento óptimo y cargas de trabajo estables gracias al ajuste de rendimiento continuo mediante la inteligencia artificial.

El ajuste automático es un servicio completamente administrado que usa la inteligencia integrada para supervisar continuamente las consultas ejecutadas en una base de datos y mejora automáticamente su rendimiento. Esto se logra mediante la adaptación dinámica de la base de datos a las cargas de trabajo cambiantes y la aplicación de las recomendaciones de ajuste. El ajuste automático aprende horizontalmente a partir de todas las bases de datos de Azure mediante la inteligencia artificial y mejora dinámicamente sus acciones de ajuste. Cuanto más tiempo se ejecute una base de datos de Azure SQL Database con el ajuste automático activado, mejor rendimiento tendrá.

El ajuste automático de Azure SQL Database podría convertirse en su mejor aliado para proporcionar cargas de trabajo estables y de rendimiento óptimo.

## <a name="what-can-automatic-tuning-do-for-you"></a>¿Cómo le puede ayudar el ajuste automático?

- Ajuste del rendimiento automatizado de bases de datos de Azure SQL Database
- Comprobación automática de mejoras de rendimiento
- Corrección y reversión automatizadas
- Registro del historial de ajustes
- Scripts T-SQL de acción de ajuste para implementaciones manuales
- Supervisión proactiva del rendimiento de las cargas de trabajo
- Funcionalidad de escalabilidad horizontal de cientos de miles de bases de datos
- Impacto positivo para los recursos de DevOps y el costo total de propiedad

## <a name="safe-reliable-and-proven"></a>Seguro, confiable y probado

Las operaciones de ajuste aplicadas a bases de datos de Azure SQL Database son totalmente seguras para el rendimiento de las cargas de trabajo más intensas. El sistema se ha diseñado cuidadosamente para que no interfiera con las cargas de trabajo del usuario. Las recomendaciones de ajuste automático se aplican solo durante momentos de uso escaso. El sistema también puede deshabilitar temporalmente las operaciones de ajuste automático para proteger el rendimiento de la carga de trabajo. En este caso, se mostrará el mensaje "Disabled by the system" (Deshabilitado por el sistema) en Azure Portal. El ajuste automático tiene en cuenta las cargas de trabajo con la máxima prioridad de recursos.

Los mecanismos de ajuste automático están muy desarrollados y se han perfeccionado en cientos de miles de bases de datos ejecutadas en Azure. Las operaciones de ajuste automatizado aplicadas se comprueban automáticamente para asegurarse de que hay una mejora en el rendimiento de la carga de trabajo. Las recomendaciones de rendimiento con regresión se detectan de forma dinámica y se revierten rápidamente. A través del registro del historial de ajustes se lleva a cabo un seguimiento claro de las mejoras de ajuste realizadas en cada base de datos de Azure SQL Database. 

![¿Cómo funciona el ajuste automático?](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

El ajuste automático de Azure SQL Database comparte su lógica básica con el motor de ajuste automático de SQL Server. Para obtener información técnica adicional sobre el mecanismo de inteligencia integrada, vea el artículo sobre el [ajuste automático de SQL Server](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

## <a name="use-automatic-tuning"></a>Uso del ajuste automático

El ajuste automático debe habilitarse manualmente en la suscripción. Para habilitar el ajuste automático mediante Azure Portal, vea [Habilitación del ajuste automático](sql-database-automatic-tuning-enable.md).

El ajuste automático puede funcionar de forma autónoma mediante la aplicación automática de las recomendaciones de ajuste, incluida la comprobación automática de las mejoras de rendimiento. 

Para tener un mayor control, se puede desactivar la aplicación automática de las recomendaciones de ajuste para aplicarlas manualmente a través de Azure Portal. También es posible usar la solución que permite solo ver las recomendaciones de ajuste automático y aplicarlas manualmente a través de los scripts y las herramientas que prefiera. 

Para obtener información general sobre cómo funciona el ajuste automático y los escenarios de uso habituales, vea el vídeo insertado:


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>Opciones de ajuste automático

Las opciones de ajuste automático disponibles en Azure SQL Database son:
 1. **CREATE INDEX**, que identifica los índices que pueden mejorar el rendimiento de la carga de trabajo, crea los índices y comprueba que mejoran el rendimiento de las consultas.
 2. **DROP INDEX**, que identifica los índices duplicados y redundantes y aquellos que no se han usado durante un período largo de tiempo.
 3. **FORCE LAST GOOD PLAN**, que identifica consultas SQL que usan un plan de ejecución más lento que el plan correcto anterior, y usa el último plan correcto conocido en lugar del plan revertido.

Azure SQL Database identifica recomendaciones de **CREATE INDEX**, **DROP INDEX** y **FORCE LAST GOOD PLAN** que pueden optimizar su base de datos y las muestra en Azure Portal. Para más información sobre la identificación de los índices que deben cambiarse, consulte cómo [buscar recomendaciones de índices en Azure Portal](sql-database-advisor-portal.md). Puede aplicar las recomendaciones manualmente mediante el portal o puede dejar que Azure SQL Database las aplique automáticamente, supervise la carga de trabajo después del cambio y compruebe que la recomendación ha mejorado el rendimiento de la carga de trabajo.

Las opciones de ajuste automático pueden activarse o desactivarse de forma independiente por base de datos. También se pueden configurar en el servidor lógico y aplicarse en cada base de datos que herede la configuración del servidor. Se recomienda configurar las opciones de ajuste automático en el servidor de forma que se herede la configuración en las bases de datos del servidor, ya que este método simplifica la administración de las opciones de ajuste automático en un gran número de bases de datos.

## <a name="next-steps"></a>Pasos siguientes

- Para habilitar la característica de ajuste automático en Azure SQL Database para administrar la carga de trabajo, vea [Habilitación del ajuste automático](sql-database-automatic-tuning-enable.md).
- Para revisar manualmente y aplicar las recomendaciones de ajuste automático, vea [Búsqueda y aplicación de recomendaciones de rendimiento](sql-database-advisor-portal.md).
- Para obtener más información sobre la inteligencia integrada que se usa en el ajuste automático, vea [Artificial Intelligence tunes Azure SQL Databases](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/) (La inteligencia artificial ajusta las bases de datos de Azure SQL Database).
- Para obtener más información sobre cómo funciona el ajuste automático en Azure SQL Database y SQL Server 2017, vea [SQL Server automatic tuning](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) (Ajuste automático de SQL Server).
