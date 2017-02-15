---
title: Azure SQL Database Advisor mediante Azure Portal | Microsoft Docs
description: Puede utilizar el Asesor de Base de datos SQL de Azure en el Portal de Azure para revisar e implementar las recomendaciones para las Bases de datos SQL existentes que pueden mejorar el rendimiento de la consulta actual.
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: cda8a646-0584-4368-b28a-85cdd9b54fcd
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/30/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e16587c3eb7366c92297455e8a175ab118174526


---
# <a name="sql-database-advisor-using-the-azure-portal"></a>SQL Database Advisor mediante Azure Portal
> [!div class="op_single_selector"]
> * [Información general del Asesor de Base de datos SQL](sql-database-advisor.md)
> * [Portal](sql-database-advisor-portal.md)
> 
> 

Puede utilizar el Asesor de Base de datos SQL de Azure en el Portal de Azure para revisar e implementar las recomendaciones para las Bases de datos SQL existentes que pueden mejorar el rendimiento de la consulta actual.

## <a name="viewing-recommendations"></a>Visualización de recomendaciones
En la página de recomendaciones puede ver las principales recomendaciones por su impacto potencial para la mejora del rendimiento. También puede ver el estado de las operaciones históricas. Seleccione una recomendación o estado para ver sus detalles.

Para ver y aplicar recomendaciones, necesita los permisos correctos de [control de acceso basado en rol](../active-directory/role-based-access-control-configure.md) en Azure. Se requieren permisos de **Lector** y **Colaborador de base de datos SQL** para ver recomendaciones, y permisos de **Propietario** y **Colaborador de base de datos SQL** para ejecutar acciones; por ejemplo, crear o quitar índices y cancelar la creación de índices.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Haga clic en **Más servicios** > **Bases de datos SQL** y seleccione la base de datos.
3. Haga clic en **Recomendación de rendimiento** para ver las recomendaciones disponibles para la base de datos seleccionada.

> [!NOTE]
> Para obtener recomendaciones, es preciso que una base de datos tenga aproximadamente un día de uso y debe haber alguna actividad. También debe haber una actividad coherente. El Asesor de Base de datos SQL puede optimizar los patrones de consultas coherentes con más facilidad que en el caso de ráfagas irregulares de actividad. Si no hay recomendaciones disponibles, la página **Recomendaciones de rendimiento** debe ofrecer un mensaje que explique el motivo.
> 
> 

![Recomendaciones](./media/sql-database-advisor-portal/recommendations.png)

Este es un ejemplo de la recomendación "Corregir el problema de esquema" del Portal de Azure.

![Corregir problema de esquema](./media/sql-database-advisor-portal/sql-database-advisor-schema-issue.png)

Las recomendaciones se ordenan en las cuatro siguientes categorías, según su impacto potencial en el rendimiento:

| Impacto | Descripción |
|:--- |:--- |
| Alto |Las recomendaciones de alto impacto debe tener el impacto más importante en el rendimiento. |
| Mediano |Las recomendaciones de impacto moderado deben mejorar el rendimiento, pero no de manera significativa. |
| Bajo |Las recomendaciones de bajo impacto deben proporcionar un mejor rendimiento que el que se produciría sin ellas, pero es posible que las mejoras no sean significativas. |

### <a name="removing-recommendations-from-the-list"></a>Eliminación de recomendaciones de la lista
Si la lista de recomendaciones contiene elementos que quiere quitar de la lista, puede descartar la recomendación:

1. Seleccione una recomendación en la lista de **Recomendaciones**.
2. Haga clic en **Descartar** en la hoja **Detalles**.

Si quiere, puede volver a agregar elementos descartados a la lista **Recomendaciones** :

1. En la hoja **Recomendaciones**, haga clic en **Ver descartados**.
2. Seleccione un elemento descartado de la lista para ver los detalles.
3. También puede hacer clic en **Deshacer Descartar** para volver a agregar el índice a la lista principal de **Recomendaciones**.

## <a name="applying-recommendations"></a>Aplicación de las recomendaciones
SQL Database Advisor le proporciona un control total sobre el modo en que se habilitan las recomendaciones mediante una de las tres opciones siguientes: 

* Aplicar recomendaciones individuales una a una.
* Habilite el Asesor para que aplique recomendaciones automáticamente (actualmente solo se aplica a las recomendaciones de índices).
* Para ejecutar manualmente una recomendación, ejecute el script de T-SQL recomendado en la base de datos.

Seleccione cualquier recomendación para ver sus detalles y, luego, haga clic en **Ver script** para revisar los detalles exactos del modo en que se crea la recomendación.

La base de datos permanece en línea mientras el asesor aplica la recomendación. Con SQL Database Advisor nunca se desconecta una base de datos.

### <a name="apply-an-individual-recommendation"></a>Aplicar una recomendación individual
Puede revisar y aceptar recomendaciones una a una.

1. En la hoja **Recomendaciones**, haga clic en una recomendación.
2. En la hoja **Detalles**, haga clic en **Aplicar**.
   
    ![Aplicar recomendaciones](./media/sql-database-advisor-portal/apply.png)

### <a name="enable-automatic-index-management"></a>Habilitar la administración de índices automática
Puede establecer que el Asesor de Base de datos SQL implemente las recomendaciones de forma automática. A medida que las recomendaciones estén disponibles, estas se aplicarán de manera automática. Al igual que con todas las operaciones de índice que administra el servicio, si el impacto en el rendimiento es negativo, se revertirá la recomendación.

1. En la hoja **Recomendaciones**, haga clic en **Automatizar**:
   
    ![Configuración del asesor](./media/sql-database-advisor-portal/settings.png)
2. Establezca el asesor para **Crear** o **Quitar** índices automáticamente:
   
    ![Índices recomendados](./media/sql-database-advisor-portal/automation.png)

### <a name="manually-run-the-recommended-t-sql-script"></a>Ejecutar manualmente el script T-SQL recomendado
Seleccione cualquier recomendación y haga clic en **Ver script**. Ejecute este script en la base de datos para aplicar la recomendación manualmente.

*El servicio no supervisa ni valida los índices que se ejecutan de manera manual para conocer el impacto en el rendimiento* , por lo que se recomienda supervisar estos índices después de su creación para comprobar que proporcionen mejoras en el rendimiento y, en caso necesario, ajustarlos o eliminarlos. Si desea conocer detalles sobre la creación de índices, consulte [CREAR ÍNDICE (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).

### <a name="canceling-recommendations"></a>Cancelación de recomendaciones
Las recomendaciones que se encuentran en estado **Pending**, **Verifying** o **Success** pueden cancelarse. Las recomendaciones con estado **Executing** no se pueden cancelar.

1. Seleccione una recomendación en el área **Historial de ajuste** para abrir la hoja de **detalles de recomendaciones**.
2. Haga clic en **Cancelar** para anular el proceso de aplicación de la recomendación.

## <a name="monitoring-operations"></a>Supervisión de operaciones
Puede que una recomendación no se aplique de manera inmediata. El portal brinda detalles sobre el estado de las operaciones de recomendación. A continuación se indican los posibles estados en los que un índice puede encontrarse:

| Estado | Descripción |
|:--- |:--- |
| Pending |El comando de aplicación de recomendaciones se ha recibido y su ejecución está programada. |
| Executing |La recomendación está aplicándose. |
| Success |La recomendación se aplicó correctamente. |
| Error |Se produjo un error durante el proceso de aplicación de recomendaciones. Puede tratarse de un problema transitorio, o posiblemente se produjo un cambio de esquema en la tabla y el script ya no es válido. |
| En reversión |La recomendación se aplicó, pero se ha considerado que no tuvo rendimiento y se está revirtiendo automáticamente. |
| Reverted |La recomendación se revirtió. |

Haga clic en una recomendación en proceso de la lista para ver más detalles:

![Índices recomendados](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Reversión de una recomendación
Si usa el asesor para aplicar la recomendación (es decir, no ejecuta manualmente el script T-SQL), revertirá automáticamente la acción si detecta que afecta de manera negativa al rendimiento. Si tan solo quiere revertir una recomendación, por el motivo que sea, realice los siguientes pasos:

1. En el área **Historial de ajuste** , seleccione una recomendación que se haya aplicado correctamente.
2. Haga clic en **Revertir** en la hoja de **detalles de recomendaciones**.

![Índices recomendados](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Supervisión del impacto en el rendimiento de las recomendaciones de índices
Una vez implementadas correctamente las recomendaciones (actualmente, solo recomendaciones para indizar operaciones y parametrizar consultas), puede hacer clic en **Detalles de la consulta** en la hoja de detalles de recomendaciones para abrir [Información de rendimiento de consultas](sql-database-query-performance.md) y ver el impacto en el rendimiento de las consultas principales.

![Supervisar el impacto en el rendimiento](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>Resumen
Asesor de Base de datos SQL ofrece recomendaciones para mejorar el rendimiento de la base de datos SQL. Al proporcionar scripts T-SQL, así como opciones de ejecución individual y completamente automática (actualmente, solo índice), el asesor resulta útil para optimizar la base de datos y, en última instancia, para mejorar el rendimiento de las consultas.

## <a name="next-steps"></a>Pasos siguientes
Supervise las recomendaciones y siga aplicándolas para refinar el rendimiento. Las cargas de trabajo de bases de datos son dinámicas y cambian con frecuencia. El Asesor de Base de datos SQL seguirá supervisando y ofreciendo recomendaciones que podrían mejorar el rendimiento de la base de datos. 

* Consulte [Asesor de Base de datos SQL](sql-database-advisor.md) para obtener información general del Asesor de Base de datos SQL.
* Consulte [Query Performance Insight](sql-database-query-performance.md) para más información sobre el impacto en el rendimiento de las principales consultas.

## <a name="additional-resources"></a>Recursos adicionales
* [Almacén de consultas](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [Control de acceso basado en rol](../active-directory/role-based-access-control-configure.md)




<!--HONumber=Nov16_HO3-->


