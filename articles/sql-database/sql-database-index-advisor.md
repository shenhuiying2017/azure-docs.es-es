<properties 
   pageTitle="Asesor de Base de datos SQL de Azure" 
   description="El Asesor de Base de datos SQL de Azure ofrece recomendaciones para las bases de datos SQL existentes que pueden mejorar el rendimiento actual de las consultas." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="04/28/2016"
   ms.author="sstein"/>

# Asesor de Base de datos SQL

Se ha actualizado el Asesor de índices de Base de datos SQL de Azure a fin de ofrecer no solo recomendaciones para crear y quitar índices, sino que ahora incluye recomendaciones para parametrizar consultas y para solucionar problemas del esquema. Con estas recomendaciones adicionales el Asesor de índices es ahora el **Asesor de Base de datos SQL**.

El Asesor de Base de datos SQL evalúa el rendimiento mediante el análisis del historial de uso de la base de datos SQL. Se recomienda usar los índices que sean más adecuados para ejecutar la carga de trabajo habitual de su base de datos.

Las recomendaciones siguientes están disponibles para los servidores V12 (no hay recomendaciones disponibles para servidores V11). Actualmente, puede establecer la aplicación automática de las recomendaciones para crear y quitar índices, vea la sección sobre [administración automática del índice](#enable-automatic-index-management) a continuación para obtener más información.

## Recomendaciones para crear índice 

Las recomendaciones para **crear índice** aparecen cuando el servicio Base de datos SQL detecta que falta un índice que, si se crea, puede beneficiar la carga de trabajo de bases de datos (solo índices no clúster).


## Recomendaciones para quitar índice

Las recomendaciones para **quitar índice** aparecen cuando el servicio Base de datos SQL detecta índices duplicados (actualmente en versión preliminar y solo se aplica a índices duplicados).

## Recomendaciones para parametrizar consultas

Las recomendaciones para **parametrizar consultas** aparecen cuando el servicio Base de datos SQL detecta que tiene una o más consultas que se vuelven a compilar continuamente pero terminan con el mismo plan de ejecución de consulta. Esto abre la posibilidad de aplicar la parametrización forzada, que permite que los planes de consulta se almacenen en caché y se reutilicen en el futuro para mejorar el rendimiento y reducir el uso de recursos.

## Recomendaciones para solucionar problemas del esquema

Las recomendaciones para **solucionar problemas del esquema** aparecen cuando el servicio Base de datos SQL advierte alguna anomalía en el número de errores SQL relacionados con el esquema que se producen en la Base de datos SQL de Azure. Esta recomendación suele aparecer cuando la base de datos encuentra varios errores relacionados con el esquema (nombre de columna no válido, nombre de objeto no válido, etc.) en el curso de una hora.


## Visualización de recomendaciones

En la página de recomendaciones puede ver las principales recomendaciones por su impacto potencial para la mejora del rendimiento. También puede ver el estado de las operaciones históricas. Seleccione una recomendación o estado para ver sus detalles.

Para ver y aplicar recomendaciones, necesita los permisos correctos de [control de acceso basado en rol](../active-directory/role-based-access-control-configure.md) en Azure. Se requieren permisos de **Lector**, **Colaborador de base de datos SQL** para ver recomendaciones, y permisos de **Propietario**, **Colaborador de base de datos SQL** para ejecutar acciones; por ejemplo, crear o quitar índices y cancelar la creación de índices.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Haga clic en **EXAMINAR** > **Bases de datos SQL** y seleccione la base de datos.
5. Haga clic en **Todas las configuraciones** > **Recomendaciones** para ver las **recomendaciones** disponibles para la base de datos seleccionada.

> [AZURE.NOTE] Para obtener recomendaciones, es preciso que una base de datos tenga aproximadamente una semana de uso y, dentro de esa semana, debe haber alguna actividad. También debe haber actividad coherente. El Asesor de Base de datos SQL puede optimizar los patrones de consultas coherentes con más facilidad que en el caso de ráfagas irregulares de actividad. Si no hay recomendaciones disponibles, la página **Recomendaciones** debe ofrecer un mensaje que explique el motivo.

![Recomendaciones](./media/sql-database-index-advisor/recommendations.png)

Las recomendaciones se ordenan en las 4 siguientes categorías, según su impacto potencial en el rendimiento:

| Impacto | Descripción |
| :--- | :--- |
| Alto | Las recomendaciones de alto impacto debe tener el impacto más importante en el rendimiento. |
| Mediano | Las recomendaciones de impacto moderado deben mejorar el rendimiento, pero no de manera significativa. |
| Bajo | Las recomendaciones de bajo impacto deben proporcionar un mejor rendimiento que el que se produciría sin ellas, pero es posible que las mejoras no sean significativas. 


### Eliminación de recomendaciones de la lista

Si la lista de recomendaciones contiene elementos que quiere quitar de la lista, puede descartar la recomendación:

1. Seleccione una recomendación en la lista de **Recomendaciones**.
2. Haga clic en **Descartar** en la hoja **Detalles**.


Si quiere, puede volver a agregar elementos descartados a la lista **Recomendaciones**:

1. En la hoja **Recomendaciones**, haga clic en **View discarded** (Ver elementos descartados).
1. Seleccione un elemento descartado de la lista para ver los detalles.
1. También puede hacer clic en **Deshacer Descartar** para volver a agregar el índice a la lista principal de **Recomendaciones**.



## Aplicación de las recomendaciones

El Asesor de Base de datos SQL tiene el control completo sobre el modo en que se habilitan las recomendaciones mediante una de las tres opciones siguientes.

- Aplicar recomendaciones individuales una a una.
- Habilite el Asesor para que aplique recomendaciones automáticamente (actualmente solo se aplica a las recomendaciones de índices).
- Ejecutar manualmente el script T-SQL recomendado en la base de datos para implementar una recomendación.

Seleccione cualquier recomendación para ver sus detalles y, luego, haga clic en **Ver script** para revisar los detalles exactos del modo en que se creará la recomendación.

La base de datos permanece en línea mientras el asesor aplica la recomendación. Con el Asesor de Base de datos SQL nunca se desconecta una base de datos.

### Aplicar una recomendación individual

Puede revisar y aceptar recomendaciones una a una.

1. En la hoja **Recomendaciones**, haga clic en una recomendación.
2. En la hoja **Detalles**, haga clic en **Aplicar**.

    ![Aplicar recomendaciones](./media/sql-database-index-advisor/apply.png)


### Habilitar la administración de índices automática

Puede establecer que el Asesor de Base de datos SQL implemente las recomendaciones de forma automática. A medida que las recomendaciones estén disponibles, estas se aplicarán de manera automática. Al igual que con todas las operaciones de índice que administra el servicio, si el impacto en el rendimiento es negativo, se revertirá la recomendación.

1. En la hoja **Recomendaciones**, haga clic en **Automatizar**:

    ![Configuración del asesor](./media/sql-database-index-advisor/settings.png)

2. Establezca el asesor para **Crear** o **Quitar** índices automáticamente:

    ![Índices recomendados](./media/sql-database-index-advisor/automation.png)




### Ejecutar manualmente el script T-SQL recomendado

Seleccione cualquier recomendación y haga clic en **Ver script**. Ejecute este script en la base de datos para aplicar la recomendación manualmente.

*El servicio no supervisa ni valida los índices que se ejecutan de manera manual para conocer el impacto en el rendimiento*, por lo que se recomienda supervisar estos índices después de su creación para comprobar que proporcionen ganancias en el rendimiento y, en caso necesario, ajustarlos o eliminarlos. Si desea conocer detalles sobre la creación de índices, consulte [CREAR ÍNDICE (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).


### Cancelación de recomendaciones

Las recomendaciones que se encuentran en estado **Pending**, **Verifying** o **Success** puede cancelarse. Las recomendaciones con estado **Executing** no se pueden cancelar.

1. Seleccione una recomendación en el área **Tuning History** (Historial de optimización) para abrir la hoja de **detalles de recomendaciones**.
2. Haga clic en **Cancelar** para anular el proceso de aplicación de la recomendación.



## Supervisión de operaciones

Puede que una recomendación no se aplique de manera inmediata. El portal brinda detalles sobre el estado de las operaciones de recomendación. A continuación se indican los posibles estados en los que un índice puede encontrarse:

| Estado | Descripción |
| :--- | :--- |
| Pending | El comando de aplicación de recomendaciones se ha recibido y su ejecución está programada. |
| Executing | La recomendación está aplicándose. |
| Correcto | La recomendación se aplicó correctamente. |
| Error | Se produjo un error durante el proceso de aplicación de recomendaciones. Puede tratarse de un problema transitorio, o posiblemente se produjo un cambio de esquema en la tabla y el script ya no es válido. |
| En reversión | La recomendación se aplicó, pero se ha considerado que no tuvo rendimiento y se está revirtiendo automáticamente. |
| Reverted | La recomendación se revirtió. |

Haga clic en una recomendación en proceso de la lista para ver sus detalles:

![Índices recomendados](./media/sql-database-index-advisor/operations.png)



### Reversión de una recomendación

Si usa el asesor para aplicar la recomendación (es decir, no ejecuta manualmente el script T-SQL), revertirá automáticamente la acción si detecta que afecta de manera negativa al rendimiento. Si tan solo quiere revertir una recomendación, por el motivo que sea, realice los siguientes pasos:


1. En el área **Tuning History** (Historial de optimización), seleccione una recomendación que se haya aplicado correctamente.
2. Haga clic en **Revertir** en la hoja de **detalles de recomendaciones**.

![Índices recomendados](./media/sql-database-index-advisor/details.png)


## Supervisión del impacto en el rendimiento de las recomendaciones de índices

Una vez implementadas correctamente las recomendaciones (actualmente, solo recomendaciones para indizar operaciones y parametrizar consultas), puede hacer clic en **Detalles de la consulta** en la hoja de detalles de recomendaciones para abrir [Información de rendimiento de consultas](sql-database-query-performance.md) y ver el impacto en el rendimiento de las consultas principales.

![Supervisar el impacto en el rendimiento](./media/sql-database-index-advisor/query-insights.png)



## Resumen

Asesor de Base de datos SQL ofrece recomendaciones para mejorar el rendimiento de la base de datos SQL. Al proporcionar scripts T-SQL, así como opciones de ejecución individual y completamente automática (actualmente, solo índice), el asesor resulta útil para optimizar la base de datos y, en última instancia, para mejorar el rendimiento de las consultas.



## Pasos siguientes

Supervise las recomendaciones y siga aplicándolas para refinar el rendimiento. Las cargas de trabajo de bases de datos son dinámicas y cambian con frecuencia. El Asesor de Base de datos SQL seguirá supervisando y ofreciendo recomendaciones que podrían mejorar el rendimiento de la base de datos.

<!---HONumber=AcomDC_0504_2016-->