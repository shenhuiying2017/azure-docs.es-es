---
title: "Habilitación del ajuste automático para Azure SQL Database | Microsoft Docs"
description: "Puede habilitar fácilmente el ajuste automático en Azure SQL Database."
services: sql-database
documentationcenter: 
author: vvasic
manager: drasumic
editor: vvasic
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 09/19/2016
ms.author: vvasic
ms.openlocfilehash: 378e5a6a8cea80a551666c3692f8aa650620f66d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="enable-automatic-tuning"></a>Habilitación del ajuste automático

Azure SQL Database es un servicio de datos completamente administrado que supervisa constantemente las consultas e identifica la acción que puede realizar para mejorar el rendimiento de la carga de trabajo. Puede revisar las recomendaciones y aplicarlas manualmente o dejar que Azure SQL Database aplique automáticamente acciones correctoras, lo que se conoce como **modo de ajuste automático**. El ajuste automático se puede habilitar en el servidor o en la base de datos.

## <a name="enable-automatic-tuning-on-server"></a>Habilitación del ajuste automático en servidor

Para habilitar el ajuste automático en el servidor de Azure SQL Database, vaya al servidor en Azure Portal y seleccione **Ajuste automático** en el menú. Seleccione las opciones de ajuste automático que quiere habilitar y haga clic en **Aplicar**:

![Server](./media/sql-database-automatic-tuning-enable/server.png)

Las opciones de ajuste automático del servidor se aplican a todas las bases de datos que contiene. De forma predeterminada, todas las bases de datos heredan la configuración de su servidor principal; sin embargo, puede invalidar esta opción y especificarla individualmente para cada base de datos.

## <a name="configure-automatic-tuning-on-database"></a>Configuración del ajuste automático en la base de datos

Azure Portal le permite especificar individualmente la configuración de ajuste automático en cada base de datos.

> [!NOTE]
> La recomendación general es administrar la configuración de ajuste automático en el nivel de servidor de forma que se pueda aplicar la misma configuración en todas las bases de datos automáticamente. Configure el ajuste automático en una base de datos individual si la base de datos es diferente a otras del mismo servidor.
>

Para habilitar el ajuste automático en una única base de datos, vaya a la base de datos en Azure Portal y luego seleccione **Ajuste automático**. Puede configurar una única base de datos para que herede la configuración del servidor con solo seleccionar la opción; o puede especificar la configuración de una base de datos de forma individual.

![Base de datos](./media/sql-database-automatic-tuning-enable/database.png)

Cuando haya seleccionado la configuración adecuada, haga clic en **Aplicar**.

## <a name="next-steps"></a>Pasos siguientes
* Para aprender más sobre el ajuste automático y cómo puede ayudarle a mejorar su rendimiento, lea el [artículo sobre este tema](sql-database-automatic-tuning.md).
* Consulte [Recomendaciones de rendimiento](sql-database-advisor.md) para ver información general sobre las recomendaciones de rendimiento de Azure SQL Database.
* Consulte [Query Performance Insight](sql-database-query-performance.md) para más información sobre el impacto en el rendimiento de las principales consultas.