---
title: Actualización a la última generación de Azure SQL Data Warehouse | Microsoft Docs
description: Pasos para actualizar Azure SQL Data Warehouse a la última generación de arquitectura de almacenamiento y hardware de Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.services: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/02/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 42b716274e655bf91f72c1b3ab207b8a5f1ccee0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="upgrade-to-latest-generation-of-azure-sql-data-warehouse-in-the-azure-portal"></a>Actualización a la última generación de Azure SQL Data Warehouse en Azure Portal

Use Azure Portal para actualizar su Azure SQL Data Warehouse para utilizar la última generación de arquitectura de almacenamiento y hardware de Azure. Mediante la actualización, puede aprovechar un rendimiento más rápido, una mayor escalabilidad y almacenamiento ilimitado para los índices de almacén de columnas.  

## <a name="applies-to"></a>Se aplica a
Esta actualización se aplica a los almacenamientos de datos del nivel de rendimiento optimizado para elasticidad.  Las instrucciones actualizan un almacenamiento de datos del nivel de rendimiento optimizado para elasticidad al nivel de rendimiento optimizado para proceso. 

## <a name="sign-in-to-the-azure-portal"></a>Inicie sesión en el Portal de Azure.

Inicie sesión en el [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Antes de empezar

1. Si el almacenamiento de datos optimizado para elasticidad que se va a actualizar se pausa, [reanude el almacenamiento de datos](pause-and-resume-compute-portal.md).
2. Prepárese para unos minutos de inactividad. 
3. El proceso de actualización termina todas las sesiones y coloca todas las conexiones. Antes de actualizar, asegúrese de que sus consultas se hayan completado. Si inicia una actualización con transacciones en curso, el tiempo de reversión puede ser amplio. 

## <a name="start-the-upgrade"></a>Iniciar la actualización

1. Abra su almacenamiento de datos en Azure Portal y haga clic en la opción de **actualización a optimizado para proceso**.
2. Tenga en cuenta las opciones del nivel de rendimiento optimizado para proceso. La selección predeterminada se puede comparar con el nivel actual antes de la actualización.
3. Elija un nivel de rendimiento. Actualmente, el precio del nivel de rendimiento optimizado para proceso está medio desactivado durante el período de la versión preliminar.
4. Haga clic en **Actualizar**.
5. Compruebe el estado en Azure Portal.
6. Espere a que el almacenamiento de datos cambie a En línea.

## <a name="rebuild-columnstore-indexes"></a>Recompilar índices de almacén de columnas

Una vez que su almacenamiento de datos esté en línea, podrá cargar datos y ejecutar consultas. Sin embargo, el rendimiento puede ser lento en primer lugar, al migrar los datos un proceso en segundo plano al nuevo hardware. 

Para forzar la migración de los datos con la mayor rapidez posible, recomendamos que se recompilen los índices de almacén de columnas. Para ello, consulte instrucciones sobre la [recompilación de índices de almacén de columnas para mejorar la calidad de los segmentos](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality). 

## <a name="next-steps"></a>Pasos siguientes
El almacenamiento de datos está en línea. Para usar las nuevas características de rendimiento, consulte [Clases de recursos para la administración de cargas de trabajo](resource-classes-for-workload-management.md).
 