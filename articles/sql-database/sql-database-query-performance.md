<properties 
   pageTitle="Query Performance Insight de Base de datos SQL de Azure" 
   description="La supervisión del rendimiento de las consultas identifica las consultas que más CPU consumen en una base de datos SQL de Azure." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="12/02/2015"
   ms.author="sstein"/>

# Query Performance Insight de Base de datos SQL de Azure


La administración y ajuste del rendimiento de las bases de datos relacionales son tareas difíciles que requieren una gran inversión de tiempo y muchos conocimientos. Query Performance Insight permite dedicar menos tiempo a la solución de problemas de rendimiento de bases de datos, ya que proporciona:

- Información más detallada sobre el consumo de recursos (DTU) de las bases de datos. 
- Las consultas que más CPU consumen, que potencialmente pueden ajustarse para mejorar el rendimiento. 
- La capacidad de profundizar en los detalles de una consulta.

## Requisitos previos

- Query Performance Insight solo está disponible con Base de datos de SQL Azure V12.
- Query Performance Insight requiere que el [almacén de consultas](https://msdn.microsoft.com/library/dn817826.aspx) se ejecuta en la base de datos. El portal le pedirá que active el almacén de consultas si no se está ejecutando todavía.

 
## Permisos

Los siguientes permisos de [control de acceso basado en roles](role-based-access-control-configure.md) se requieren para usar Query Performance Insight:

- Los permisos de **lector**, **propietario**, **colaborador**, **colaborador de base de datos de SQL** o **colaborador de SQL Server** se requieren para ver las consultas y gráficos que más recursos consumen. 
- Los permisos de **propietario**, **colaborador**, **colaborador de base de datos de SQL** o **colaborador de SQL Server** se requieren para ver el texto de la consulta.



## Uso de Query Performance Insight

Query Performance Insight es fácil de usar:

- Revisar la lista de consultas que más recursos consumen. 
- Seleccionar una consulta individual para ver sus detalles.
- Haga clic en **Configuración** para personalizar la forma en que se muestran los datos o para mostrar otro período.



> [AZURE.NOTE]Para proporcionar información sobre el rendimiento de las consultas, es preciso que Almacén de consultas para Base de datos SQL capture un par de horas de datos. Si la base de datos no tiene actividad o Almacén de consultas no estuvo activo durante un período determinado, los gráficos estará vacíos al mostrar dicho período. Puede habilitar el almacén de consulta en cualquier momento si no se está ejecutando.



## Revisión de las consultas que más CPU consumen

En el [portal](https://portal.azure.com), realice estas acciones:

1. Navegue a una base de datos SQL y haga clic en **Query Performance Insight**. 

    ![Query Performance Insight][1]

    Se abre la vista de las principales consultas y aparece la lista de las consultas que más CPU consumen.

1. Para obtener más información, haga clic alrededor del gráfico.<br>La línea superior muestra el porcentaje general de DTU de la base de datos, mientras que las barras muestran el porcentaje de CPU consumido por las consultas seleccionadas. Seleccione o anule la selección de las consultas individuales para incluirlas o excluirlas del gráfico.

    ![principales consultas][2]

1. Opcionalmente, haga clic en **Editar gráfico** para personalizar la forma en que se muestran los datos de consumo de CPU o para mostrar otro período.

## Visualización de los detalles de las consultas individuales

Para ver los detalles de una consulta:

1. Haga clic en cualquiera de las consultas de la lista de las principales consultas.<br>Se abre la vista de detalles y el consumo de CPU de las consultas se desglosa por tiempo.
3. Para más información, haga clic alrededor del gráfico.<br>La línea superior muestra el porcentaje general de CPU, mientras que las barras muestran el porcentaje de CPU consumido por la consulta seleccionada.
4. Revise los datos para ver métricas detalladas, entre las que se incluyen la duración, el número de ejecuciones y el porcentaje de uso de los recursos de cada intervalo en los que se ejecutó la consulta.
    
    ![detalles de consulta][3]

1. Opcionalmente, haga clic en **Configuración** para personalizar la forma en que se muestran los datos de consumo de CPU o para mostrar otro período.




## Resumen

Query Performance Insight le ayuda a comprender el impacto de la carga de trabajo de las consultas y su relación con el consumo de recursos de base de datos. Con esta característica, conocerá las consultas que más consumen e identificará fácilmente las que deben corregirse antes de que conviertan en un problema. Haga clic en el icono **Query Performance Insight** de una hoja de base de datos para ver las consultas que más recursos (CPU) consumen.




## Pasos siguientes

Las cargas de trabajo de bases de datos son dinámicas y cambian con frecuencia. Supervise las consultas y ajústelas para mejorar el rendimiento.

Vea el [Asesor de índices](sql-database-index-advisor.md) para más recomendaciones para mejorar el rendimiento de bases de datos SQL.

<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png

<!---HONumber=AcomDC_1210_2015-->