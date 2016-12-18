---
title: "Supervisión del almacenamiento en memoria de XTP | Microsoft Docs"
description: Estimar y supervisar el uso y la capacidad del almacenamiento en memoria de XTP; resolver el error de capacidad 41823
services: sql-database
documentationcenter: 
author: jodebrui
manager: jhubbard
editor: 
ms.assetid: b617308e-692c-4938-8fa2-070034a3ecef
ms.service: sql-database
ms.custom: monitor and tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: jodebrui
translationtype: Human Translation
ms.sourcegitcommit: 5a101aa78dbac4f1a0edb7f414b44c14db392652
ms.openlocfilehash: 475af239e4c77626d4c069f1238bc32d3b440a0e


---
# <a name="monitor-in-memory-oltp-storage"></a>Supervisión del almacenamiento OLTP en memoria
Cuando se usa [OLTP en memoria](sql-database-in-memory.md), los datos de tablas con optimización de memoria y las variables de tabla residen en el almacenamiento OLTP en memoria. Cada nivel de servicio Premium tiene un tamaño máximo de almacenamiento OLTP en memoria, que se documenta en el artículo [Niveles de servicio de SQL Database](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels). Una vez que se supera ese límite, las operaciones de inserción y actualización pueden empezar a producir errores (con el error 41823). En ese momento se necesita eliminar los datos para reclamar memoria o actualizar el nivel de rendimiento de la base de datos.

## <a name="determine-whether-data-will-fit-within-the-in-memory-storage-cap"></a>Determinación de si los datos se ajustan dentro del límite de almacenamiento en memoria
Determine el extremo de almacenamiento: consulte en el artículo [Niveles de servicio de SQL Database](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels) los extremos de almacenamiento de los diferentes niveles de servicio Premium.

La estimación de los requisitos de memoria para una tabla optimizada en memoria funciona de la misma manera para SQL Server tal y como se hace en la base de datos SQL de Azure. Dedique algunos minutos a revisar ese tema en [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Tenga en cuenta que la tabla y las filas de variables de tabla, así como los índices, se contarán para el tamaño de datos de usuario máx. Además, ALTER TABLE necesita suficiente espacio para crear una nueva versión de toda la tabla y sus índices.

## <a name="monitoring-and-alerting"></a>Supervisión y alertas
Puede supervisar el uso del almacenamiento en memoria como un porcentaje del [extremo de almacenamiento para su nivel de rendimiento](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels) en [Azure Portal](https://portal.azure.com/): 

* En la hoja Base de datos, busque el cuadro de uso de recursos y haga clic en Editar.
* A continuación, seleccione la métrica `In-Memory OLTP Storage percentage`.
* Para agregar una alerta, haga clic en el cuadro Uso de recursos para abrir la hoja de métricas y después haga clic en Agregar alerta.

O bien, use la siguiente consulta para mostrar el uso del almacenamiento en memoria:

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-memory-situations---error-41823"></a>Corrección de situaciones de falta de memoria (error 41823)
Si el sistema se queda sin memoria, las operaciones INSERT, UPDATE y CREATE no se llevarán a cabo, y se mostrará el mensaje de error 41823.

El mensaje de error 41823 indica que las variables de tabla y las tablas optimizadas para memoria han superado el tamaño máximo.

Para resolver este error, haga uno de los siguientes:

* Elimine datos de las tablas optimizadas para memoria, lo que potencialmente descarga los datos a tablas tradicionales, basadas en disco; o bien,
* Actualice el nivel de servicio a uno con suficiente espacio de almacenamiento en memoria para los datos que necesita mantener en tablas optimizadas en memoria.

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre la [supervisión de Base de datos SQL de Azure con vistas de administración dinámica](sql-database-monitoring-with-dmvs.md)



<!--HONumber=Nov16_HO3-->


