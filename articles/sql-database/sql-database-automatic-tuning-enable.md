---
title: "Habilitación del ajuste automático para Azure SQL Database | Microsoft Docs"
description: "Puede habilitar fácilmente el ajuste automático en Azure SQL Database."
services: sql-database
documentationcenter: 
author: veljko-msft
manager: drasumic
editor: vvasic
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 09/19/2016
ms.author: veljko-msft
ms.openlocfilehash: bf8e0203112a42132a80e234964747c550fea284
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2017
---
# <a name="enable-automatic-tuning"></a>Habilitación del ajuste automático

Azure SQL Database es un servicio de datos completamente administrado que supervisa constantemente las consultas e identifica la acción que puede realizar para mejorar el rendimiento de la carga de trabajo. Puede revisar las recomendaciones y aplicarlas manualmente o dejar que Azure SQL Database aplique automáticamente acciones correctoras, lo que se conoce como **modo de ajuste automático**. El ajuste automático se puede habilitar en el servidor o en la base de datos.

## <a name="enable-automatic-tuning-on-server"></a>Habilitación del ajuste automático en servidor
En el nivel de servidor, puede optar por heredar la configuración de ajuste automático de "Valores predeterminados de Azure" o no heredarla. Los valores predeterminados de Azure son FORCE_LAST_GOOD_PLAN habilitado, CREATE_INDEX habilitado y DROP_INDEX deshabilitado.

### <a name="portal"></a>Portal
Para habilitar el ajuste automático en el servidor de Azure SQL Database, vaya al servidor en Azure Portal y seleccione **Ajuste automático** en el menú. Seleccione las opciones de ajuste automático que quiere habilitar y haga clic en **Aplicar**:

![Server](./media/sql-database-automatic-tuning-enable/server.png)

Las opciones de ajuste automático del servidor se aplican a todas las bases de datos que contiene. De forma predeterminada, todas las bases de datos heredan la configuración de su servidor principal; sin embargo, puede invalidar esta opción y especificarla individualmente para cada base de datos.

### <a name="rest-api"></a>API de REST
[Haga clic aquí para obtener más información sobre cómo habilitar el ajuste automático en el nivel de servidor a través de la API de REST.](https://docs.microsoft.com/rest/api/sql/serverautomatictuning)

## <a name="enable-automatic-tuning-on-database"></a>Habilitar el ajuste automático en la base de datos

Azure SQL Database le permite especificar individualmente la configuración de ajuste automático en cada base de datos. En el nivel de base de datos, puede optar por heredar la configuración de ajuste automático del servidor primario o de "Valores predeterminados de Azure", o no heredarla. Los valores predeterminados de Azure son FORCE_LAST_GOOD_PLAN habilitado, CREATE_INDEX habilitado y DROP_INDEX deshabilitado.

> [!NOTE]
> La recomendación general es administrar la configuración de ajuste automático en el nivel de servidor de forma que se pueda aplicar la misma configuración en todas las bases de datos automáticamente. Configure el ajuste automático en una base de datos individual si la base de datos es diferente a otras del mismo servidor.
>

### <a name="portal"></a>Portal

Para habilitar el ajuste automático en una única base de datos, vaya a la base de datos en Azure Portal y luego seleccione **Ajuste automático**. Puede configurar una única base de datos para que herede la configuración del servidor con solo seleccionar la opción; o puede especificar la configuración de una base de datos de forma individual.

![Base de datos](./media/sql-database-automatic-tuning-enable/database.png)

Cuando haya seleccionado la configuración adecuada, haga clic en **Aplicar**.

### <a name="rest-api"></a>API de REST
[Haga clic aquí para obtener más información sobre cómo habilitar el ajuste automático en una sola base de datos a través de la API de REST.](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning)

### <a name="t-sql"></a>T-SQL

Para habilitar el ajuste automático en una sola base de datos mediante T-SQL, conéctese a la base de datos y ejecute la consulta siguiente:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
   ```
   
Si establece el ajuste automático en AUTO, se aplicarán los valores predeterminados de Azure. Si se establece en INHERIT, se heredará la configuración de ajuste automático del servidor primario. Si elige CUSTOM, deberá configurar manualmente el ajuste automático.

Para configurar las opciones individuales de ajuste automático mediante T-SQL, conéctese a la base de datos y ejecute una consulta como esta:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
   ```
   
Si establece la opción de ajuste individual en ON, invalidará todas las configuraciones que la base de datos haya heredado y habilitará la opción de ajuste. Si la establece en OFF, también invalidará todas las configuraciones que la base de datos haya heredado y deshabilitará la opción de ajuste. Las opciones de ajuste automático para las que se especifique DEFAULT heredarán la configuración de ajuste automático del nivel de base de datos.  

## <a name="disabled-by-the-system"></a>Deshabilitado por el sistema
El ajuste automático supervisa todas las acciones que realiza en la base de datos y, en algunos casos, determina que no funciona correctamente en la base de datos. En esta situación, el sistema deshabilitará la opción de ajuste. En la mayoría de los casos, esto sucede porque el Almacén de consultas no está habilitado o se encuentra en estado de solo lectura en una base de datos específica.

## <a name="next-steps"></a>Pasos siguientes
* Para aprender más sobre el ajuste automático y cómo puede ayudarle a mejorar su rendimiento, lea el [artículo sobre este tema](sql-database-automatic-tuning.md).
* Consulte [Recomendaciones de rendimiento](sql-database-advisor.md) para ver información general sobre las recomendaciones de rendimiento de Azure SQL Database.
* Consulte [Query Performance Insight](sql-database-query-performance.md) para más información sobre el impacto en el rendimiento de las principales consultas.
