---
title: "Aplicación de recomendaciones de rendimiento: Azure SQL Database | Microsoft Docs"
description: Puede usar Azure Portal para buscar recomendaciones de rendimiento que pueden optimizar el rendimiento de su instancia de Azure SQL Database o corregir un problema identificado en la carga de trabajo.
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: cda8a646-0584-4368-b28a-85cdd9b54fcd
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 07/05/2017
ms.author: sstein
ms.openlocfilehash: 018afaa8b08bd001e55693390e80c8e2c4f33a30
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="find-and-apply-performance-recommendations"></a>Búsqueda y aplicación de recomendaciones de rendimiento

Puede usar Azure Portal para buscar recomendaciones de rendimiento que pueden optimizar el rendimiento de su instancia de Azure SQL Database o corregir un problema identificado en la carga de trabajo. La página **Recomendaciones de rendimiento** de Azure Portal permite buscar las principales recomendaciones en función de su impacto potencial. 

## <a name="viewing-recommendations"></a>Visualización de recomendaciones

Para ver y aplicar recomendaciones de rendimiento, necesita los permisos correctos de [control de acceso basado en rol](../active-directory/role-based-access-control-what-is.md) en Azure. Se requieren permisos de **Lector** y **Colaborador de base de datos SQL** para ver recomendaciones, y permisos de **Propietario** y **Colaborador de base de datos SQL** para ejecutar acciones; por ejemplo, crear o quitar índices y cancelar la creación de índices.

Use los pasos siguientes para buscar recomendaciones de rendimiento en Azure Portal:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Vaya a **Más servicios** > **Bases de datos SQL** y seleccione la base de datos.
3. Vaya a **Recomendaciones de rendimiento** para ver las recomendaciones disponibles para la base de datos seleccionada.

Las recomendaciones de rendimiento se muestran en una tabla similar a la de la siguiente ilustración:

![Recomendaciones](./media/sql-database-advisor-portal/recommendations.png)

Las recomendaciones se ordenan en las siguientes categorías, según su impacto potencial sobre el rendimiento:

| Impacto | Descripción |
|:--- |:--- |
| Alto |Las recomendaciones de alto impacto debe tener el impacto más importante en el rendimiento. |
| Mediano |Las recomendaciones de impacto moderado deben mejorar el rendimiento, pero no de manera significativa. |
| Bajo |Las recomendaciones de bajo impacto deben proporcionar un mejor rendimiento que el que se produciría sin ellas, pero es posible que las mejoras no sean significativas. |


> [!NOTE]
> Azure SQL Database necesita supervisar actividades durante al menos un día para identificar algunas recomendaciones. Azure SQL Database puede optimizar con patrones de consultas coherentes con más facilidad que en el caso de ráfagas irregulares de actividad. Si no hay recomendaciones correctas disponibles, la página **Recomendaciones de rendimiento** proporciona un mensaje explicativo.
> 

También puede ver el estado de las operaciones históricas. Seleccione una recomendación o estado para ver sus detalles.

Este es un ejemplo de la recomendación "Crear índice" de Azure Portal.

![Creación de índice](./media/sql-database-advisor-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Aplicación de las recomendaciones
Azure SQL Database le proporciona un control total sobre el modo en que se habilitan las recomendaciones mediante una de las tres opciones siguientes: 

* Aplicar recomendaciones individuales una a una.
* Habilitar el ajuste automático para aplicar automáticamente recomendaciones.
* Para implementar manualmente una recomendación, ejecute el script T-SQL recomendado en la base de datos.

Seleccione cualquier recomendación para ver sus detalles y, luego, haga clic en **Ver script** para revisar los detalles exactos del modo en que se crea la recomendación.

La base de datos permanece en línea mientras se aplica la recomendación; cuando se usa la recomendación de rendimiento o el ajuste automático, no se desconecta nunca una base de datos.

### <a name="apply-an-individual-recommendation"></a>Aplicar una recomendación individual
Puede revisar y aceptar recomendaciones una a una.

1. En la hoja **Recomendaciones**, seleccione una recomendación.
2. En la hoja **Detalles**, haga clic en el botón **Aplicar**.
   
    ![Aplicar recomendaciones](./media/sql-database-advisor-portal/apply.png)

Se aplicará la recomendación seleccionada en la base de datos.

### <a name="removing-recommendations-from-the-list"></a>Eliminación de recomendaciones de la lista
Si la lista de recomendaciones contiene elementos que quiere quitar de la lista, puede descartar la recomendación:

1. Seleccione una recomendación en la lista **Recomendaciones** para abrir los detalles.
2. Haga clic en **Descartar** en la hoja **Detalles**.

Si quiere, puede volver a agregar elementos descartados a la lista **Recomendaciones** :

1. En la hoja **Recomendaciones**, haga clic en **Ver descartados**.
2. Seleccione un elemento descartado de la lista para ver los detalles.
3. También puede hacer clic en **Deshacer Descartar** para volver a agregar el índice a la lista principal de **Recomendaciones**.


### <a name="enable-automatic-tuning"></a>Habilitación del ajuste automático
Puede establecer que Azure SQL Database implemente las recomendaciones de forma automática. A medida que las recomendaciones estén disponibles, estas se aplicarán de manera automática. Al igual que con todas las recomendaciones que administra el servicio, si el impacto en el rendimiento es negativo, se revertirá la recomendación.

1. En la hoja **Recomendaciones**, haga clic en **Automatizar**:
   
    ![Configuración del asesor](./media/sql-database-advisor-portal/settings.png)
2. Seleccione las acciones que desee automatizar:
   
    ![Índices recomendados](./media/sql-database-advisor-portal/automation.png)

### <a name="manually-run-the-recommended-t-sql-script"></a>Ejecutar manualmente el script T-SQL recomendado
Seleccione cualquier recomendación y haga clic en **Ver script**. Ejecute este script en la base de datos para aplicar la recomendación manualmente.

*El servicio no supervisa ni valida los índices que se ejecutan de manera manual para conocer el impacto en el rendimiento* , por lo que se recomienda supervisar estos índices después de su creación para comprobar que proporcionen mejoras en el rendimiento y, en caso necesario, ajustarlos o eliminarlos. Si desea conocer detalles sobre la creación de índices, consulte [CREAR ÍNDICE (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).

### <a name="canceling-recommendations"></a>Cancelación de recomendaciones
Las recomendaciones que se encuentran en estado **Pending**, **Verifying** o **Success** pueden cancelarse. Las recomendaciones con estado **Executing** no se pueden cancelar.

1. Seleccione una recomendación en el área **Historial de ajuste** para abrir la hoja de **detalles de recomendaciones**.
2. Haga clic en **Cancelar** para anular el proceso de aplicación de la recomendación.

## <a name="monitoring-operations"></a>Supervisión de operaciones
Puede que una recomendación no se aplique de manera inmediata. El portal proporciona detalles sobre el estado de la recomendación. A continuación se indican los posibles estados en los que un índice puede encontrarse:

| Estado | Descripción |
|:--- |:--- |
| Pending |El comando de aplicación de recomendaciones se ha recibido y su ejecución está programada. |
| Executing |La recomendación está aplicándose. |
| Comprobando |La recomendación se aplicó correctamente y el servicio está midiendo las ventajas. |
| Correcto |La recomendación se aplicó correctamente y se han medido ventajas. |
| Error |Se produjo un error durante el proceso de aplicación de recomendaciones. Puede tratarse de un problema transitorio, o posiblemente se produjo un cambio de esquema en la tabla y el script ya no es válido. |
| En reversión |La recomendación se aplicó, pero se ha considerado que no tuvo rendimiento y se está revirtiendo automáticamente. |
| Reverted |La recomendación se revirtió. |

Haga clic en una recomendación en proceso de la lista para ver más detalles:

![Índices recomendados](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Reversión de una recomendación
Si usó las recomendaciones de rendimiento para aplicar la recomendación (es decir, no ejecutó manualmente el script T-SQL), se revertirá automáticamente si detecta que afecta de manera negativa al rendimiento. Si tan solo quiere revertir una recomendación, por el motivo que sea, realice los siguientes pasos:

1. En el área **Historial de ajuste** , seleccione una recomendación que se haya aplicado correctamente.
2. Haga clic en **Revertir** en la hoja de **detalles de recomendaciones**.

![Índices recomendados](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Supervisión del impacto en el rendimiento de las recomendaciones de índices
Una vez implementadas correctamente las recomendaciones (actualmente, solo recomendaciones para indizar operaciones y parametrizar consultas), puede hacer clic en **Detalles de la consulta** en la hoja de detalles de recomendaciones para abrir [Información de rendimiento de consultas](sql-database-query-performance.md) y ver el impacto en el rendimiento de las consultas principales.

![Supervisar el impacto en el rendimiento](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>Resumen
Azure SQL Database ofrece recomendaciones para mejorar el rendimiento de la base de datos SQL. Al proporcionar scripts T-SQL, así como opciones de ejecución individual y completamente automática, consigue ayuda útil para optimizar la base de datos y, en última instancia, para mejorar el rendimiento de las consultas.

## <a name="next-steps"></a>Pasos siguientes
Supervise las recomendaciones y siga aplicándolas para refinar el rendimiento. Las cargas de trabajo de bases de datos son dinámicas y cambian con frecuencia. Azure SQL Database seguirá supervisando y ofreciendo recomendaciones que podrían mejorar el rendimiento de la base de datos. 

* Consulte [Ajuste automático](sql-database-automatic-tuning.md) para más información sobre el ajuste automático en Azure SQL Database.
* Consulte [Recomendaciones de rendimiento](sql-database-advisor.md) para ver información general sobre las recomendaciones de rendimiento de Azure SQL Database.
* Consulte [Query Performance Insight](sql-database-query-performance.md) para más información sobre el impacto en el rendimiento de las principales consultas.

## <a name="additional-resources"></a>Recursos adicionales
* [Almacén de consultas](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [Control de acceso basado en rol](../active-directory/role-based-access-control-what-is.md)

