---
title: "Información sobre el rendimiento de Azure SQL Database | Microsoft Docs"
description: "La Base de datos SQL de Azure ofrece herramientas de rendimiento para ayudarle a identificar áreas que pueden mejorar el rendimiento actual de las consultas."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: a60b75ac-cf27-4d73-8322-ee4d4c448aa2
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 07/19/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 38416deeb583c78bca9ded2e514bf3ddff716c52


---
# <a name="sql-database-performance-insight"></a>SQL Database Performance Insight (Información de rendimiento de Base de datos SQL)
Base de datos SQL de Azure brinda herramientas de rendimiento para ayudarle a identificar y mejorar el rendimiento de las bases de datos mediante acciones y recomendaciones de optimización inteligentes. 

1. Vaya a la base de datos en [Azure Portal](http://portal.azure.com) y haga clic en **All settings** (Toda la configuración)  > **Rendimiento** > **Información general** para abrir la página **Rendimiento**. 
2. Haga clic en **Recomendaciones** para abrir [SQL Database Advisor](#sql-database-advisor) y, luego, en **Consultas** para abrir [Información de rendimiento de consultas](#query-performance-insight).
   
    ![Vista Rendimiento](./media/sql-database-performance/entries.png)

## <a name="performance-overview"></a>Información general de rendimiento
Al hacer clic en **Información general** o en el icono de **Rendimiento**, accederá al panel de rendimiento de la base de datos. Esta vista ofrece un resumen del rendimiento de la base de datos y le ayuda con la optimización y solución de problemas de rendimiento. 

![Rendimiento](./media/sql-database-performance/performance.png)

* El icono de **Recomendaciones** proporciona un desglose de recomendaciones de optimización para la base de datos (se muestran las tres primeras recomendaciones si hay más). Haga clic en este icono para acceder al **Asesor de Base de datos SQL**. 
* El icono de **Tuning activity** (Actividad de optimización) proporciona un resumen de las acciones de optimización en curso y finalizadas para la base de datos, lo que le brinda una vista rápida del historial de la actividad de optimización. Si hace clic en este icono irá a la vista completa del historial de optimización de la basa de datos.
* El icono de **Auto-tuning** (optimización automática) muestra la configuración de optimización automática de la base de datos (qué acciones de optimización están configuradas para que se apliquen automáticamente a la base de datos). Al hacer clic en este icono se abre el cuadro de diálogo de configuración de automatización.
* El icono de **Consultas de bases de datos** muestra el resumen del rendimiento de consultas de la base de datos (uso general de DTU y consultas que consumen más recursos). Haga clic en este icono para acceder a **Información de rendimiento de consultas**.

## <a name="sql-database-advisor"></a>Asesor de Base de datos SQL
[Asesor de Base de datos SQL](sql-database-advisor.md) ofrece recomendaciones de optimización inteligentes que pueden ayudar a mejoran el rendimiento de la base de datos. 

* Recomendaciones sobre los índices que se deben crear o quitar (y una opción para aplicar las recomendaciones de índices automáticamente sin intervención del usuario y revertir automáticamente las recomendaciones que tienen un impacto negativo en el rendimiento).
* Recomendaciones cuando se identifican problemas del esquema en la base de datos.
* Recomendaciones en caso de consultas que puedan beneficiarse de consultas parametrizadas.

## <a name="query-performance-insight"></a>Información de rendimiento de consultas
[Información de rendimiento de consultas](sql-database-query-performance.md) permite dedicar menos tiempo a la solución de problemas de rendimiento de bases de datos, ya que proporciona:

* Información más detallada sobre el consumo de recursos (DTU) de las bases de datos. 
* Las consultas que más CPU consumen, que potencialmente pueden ajustarse para mejorar el rendimiento. 
* La posibilidad de profundizar en los detalles de una consulta. 

## <a name="additional-resources"></a>Recursos adicionales
* [Guía de rendimiento de Base de datos SQL de Azure](sql-database-performance-guidance.md)
* [¿Cuándo se debe utilizar un grupo de bases de datos elásticas?](sql-database-elastic-pool-guidance.md)




<!--HONumber=Nov16_HO3-->


