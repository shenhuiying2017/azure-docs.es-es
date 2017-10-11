---
title: "Administración de la potencia de proceso en Azure SQL Data Warehouse (introducción) | Microsoft Docs"
description: Funcionalidades de escalado horizontal del rendimiento en Almacenamiento de datos SQL de Azure. Realice el escalado horizontal ajustando las DWU o pause y reanude los recursos de proceso para ahorrar costos.
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: e13a82b0-abfe-429f-ac3c-f2b6789a70c6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 03/22/2017
ms.author: elbutter
ms.openlocfilehash: abe22f542a79714f6e894870872ee6b76ffe7633
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-overview"></a>Administración de la potencia de proceso en Almacenamiento de datos SQL de Azure (información general)
> [!div class="op_single_selector"]
> * [Información general](sql-data-warehouse-manage-compute-overview.md)
> * [Portal](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>

La arquitectura del Almacenamiento de datos SQL separa el proceso y el almacenamiento, lo que permite a cada uno escalar de manera independiente. Por consiguiente, el proceso se puede escalar para satisfacer las demandas de rendimiento independientemente de la cantidad de datos. Una consecuencia natural de esta arquitectura es que la [facturación][billed] para el proceso y almacenamiento es independiente. 

En esta introducción se describe cómo funciona el escalado horizontal con SQL Data Warehouse y cómo utilizar las funcionalidades de pausa, reanudación y escala de SQL Data Warehouse. Consulte la página de [unidades de almacenamiento de datos (DWU)][data warehouse units (DWUs)] para obtener información sobre la relación existente entre DWU y el rendimiento. 

## <a name="how-compute-management-operations-work-in-sql-data-warehouse"></a>Funcionamiento de las operaciones de administración de proceso en SQL Data Warehouse
La arquitectura de SQL Data Warehouse está formada por un nodo del control, nodos de proceso y la capa de almacenamiento que se reparten a lo largo de 60 distribuciones. 

Durante una sesión activa normal en SQL Data Warehouse, el nodo principal del sistema administra los metadatos y contiene el optimizador de consultas distribuidas. Debajo de este nodo principal se encuentran los nodos de proceso y el nivel de almacenamiento. Para un 400 DWU, el sistema tiene un nodo principal, cuatro nodos de proceso y la capa de almacenamiento, que consta de 60 distribuciones. 

Cuando se lleva a cabo una operación de escala o pausa, el sistema primero elimina todas las consultas entrantes y luego revierte las transacciones para garantizar un estado coherente. Para las operaciones de escala, el escalado solo se producirá una vez completada esta reversión transaccional. Para una operación de escalado vertical, el sistema aprovisiona el número deseado adicional de nodos de proceso y luego comienza a asociar de nuevo los nodos de proceso con el nivel de almacenamiento. Para una operación de escalado horizontal, se liberan los nodos que no necesita y el resto de nodos de proceso se vuelven a asociar ellos mismos con el número apropiado de distribuciones. Para una operación de pausa, se liberan todos los nodos de proceso y el sistema se somete a una serie de operaciones de metadatos para dejar el sistema final en un estado estable.

| DWU  | \# de nodos de ejecución | \# de distribuciones por nodo |
| ---- | ------------------ | ---------------------------- |
| 100  | 1                  | 60                           |
| 200  | 2                  | 30                           |
| 300  | 3                  | 20 |                           |
| 400  | 4                  | 15                           |
| 500  | 5                  | 12                           |
| 600  | 6                  | 10                           |
| 1000 | 10                 | 6                            |
| 1200 | 12                 | 5                            |
| 1.500 | 15                 | 4                            |
| 2000 | 20 |                 | 3                            |
| 3000 | 30                 | 2                            |
| 6000 | 60                 | 1                            |

Las tres funciones principales para administrar el proceso son:

1. Pausar
2. Reanudación
3. Escala

Cada una de estas operaciones puede tardar varios minutos en completarse. Si está realizando una operación de escalado, pausa o reanudación automáticamente, puede que desee implementar la lógica para asegurarse de que ciertas operaciones se completaron antes de pasar a realizar otra acción. 

La comprobación del estado de la base de datos a través de varios puntos de conexión le permitirá implementar correctamente la automatización de tales operaciones. El portal le proporcionará una notificación tras la finalización de una operación y el estado actual de las bases de datos, pero no permitirá la comprobación programática del estado. 

>  [!NOTE]
>
>  La funcionalidad de administración del proceso no existe en todos los puntos de conexión.
>
>  

|              | Pausar y reanudar | Escala | Comprobar el estado de la base de datos |
| ------------ | ------------ | ----- | -------------------- |
| Portal de Azure | Sí          | Sí   | **No**               |
| PowerShell   | Sí          | Sí   | Sí                  |
| API de REST     | Sí          | Sí   | Sí                  |
| T-SQL        | **No**       | Sí   | Sí                  |



<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>Escalado de proceso

El rendimiento en SQL Data Warehouse se mide en [unidades de almacenamiento de datos (DWU)][data warehouse units (DWUs)], que es una medida de recursos de proceso abstracta, como la CPU, la memoria y el ancho de banda de E/S. Un usuario que desea escalar el rendimiento del sistema puede hacerlo a través de varios medios, como pueden ser el portal, T-SQL y API de REST. 

### <a name="how-do-i-scale-compute"></a>¿Cómo se puede escalar el proceso?
La energía de proceso se administra para SQL Data Warehouse cambiando el ajuste de DWU. El rendimiento aumenta [linealmente][linearly] a medida que agrega más DWU para determinadas operaciones.  Proporcionamos ofertas de DWU que garantizan que el rendimiento cambiará considerablemente al escalar o reducir el sistema verticalmente. 

Para ajustar las DWU, puede utilizar cualquiera de estos métodos individuales.

* [Escalado de la potencia de proceso con Azure Portal][Scale compute power with Azure portal]
* [Escalado de la potencia de proceso con PowerShell][Scale compute power with PowerShell]
* [Escalado de la potencia de proceso con API de REST][Scale compute power with REST APIs]
* [Escalado de la potencia de proceso con TSQL][Scale compute power with TSQL]

### <a name="how-many-dwus-should-i-use"></a>¿Cuántas DWU debería usar?

Para entender el valor ideal de su DWU, pruebe a escalar y reducir verticalmente y a ejecutar algunas consultas después de cargar los datos. Dado que el escalado se realiza rápidamente, puede probar varios niveles de rendimiento en una hora o menos. 

> [!Note] 
> SQL Data Warehouse está diseñado para procesar grandes cantidades de datos. Para ver sus funcionalidades verdaderas para escalado, especialmente en DWU mayores, desea utilizar un conjunto de datos grande cuyo tamaño se aproxima o supera 1 TB.

Recomendaciones para encontrar la mejor DWU para la carga de trabajo:

1. Para un almacenamiento de datos en desarrollo, comience seleccionando un número pequeño de DWU.  Un buen punto de partida es DW400 o DW200.
2. Supervise el rendimiento de su aplicación, observando el número de DWU seleccionados en comparación con el rendimiento que observe.
3. Determine en qué grado debería ser el rendimiento más rápido o más lento para poder alcanzar el nivel óptimo de rendimiento para sus requerimientos suponiendo una escala lineal.
4. Aumente o disminuya el número de DWU en proporción a la rapidez con la que quiere que funcione la carga de trabajo. 
5. Continúe realizando ajustes hasta llegar a un nivel de rendimiento adecuado para sus requerimientos empresariales.

> [!NOTE]
>
> El rendimiento de las consultas solo aumenta con más paralelización si el trabajo se puede dividir entre nodos de proceso. Si observa que el escalado no cambia el rendimiento, consulte nuestros artículos de ajuste de rendimiento para comprobar si los datos no se distribuyen uniformemente o si introduce una gran cantidad de movimiento de datos. 

### <a name="when-should-i-scale-dwus"></a>¿Cuándo debo realizar un escalado de DWU?
El escalado de DWU modifica los siguientes escenarios importantes:

1. El cambio lineal del rendimiento del sistema para exámenes, agregaciones e instrucciones CTAS
2. Aumento del número de lectores y escritores cuando se carga con PolyBase
3. Número máximo de consultas simultáneas y ranuras de simultaneidad

Recomendaciones de cuándo realizar el escalado de DWU:

1. Para realizar una operación de transformación o carga de una gran cantidad de datos, realice el escalado vertical de DWU para que los datos estén disponibles con mayor rapidez.
2. Durante las horas punta comerciales, escale para dar cabida a un mayor número de consultas simultáneas. 

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Pausa del proceso
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Para pausar una base de datos, use cualquiera de estos métodos individuales.

* [Pausa del proceso con Azure Portal][Pause compute with Azure portal]
* [Pausa del proceso con PowerShell][Pause compute with PowerShell]
* [Pausa del proceso con las API de REST][Pause compute with REST APIs]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Reanudación del proceso
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Para reanudar una base de datos, use cualquiera de estos métodos individuales.

* [Reanudación del proceso con Azure Portal][Resume compute with Azure portal]
* [Reanudación del proceso con PowerShell][Resume compute with PowerShell]
* [Reanudación del proceso con las API de REST][Resume compute with REST APIs]

<a name="check-compute-bk"></a>

## <a name="check-database-state"></a>Comprobar el estado de la base de datos 

Para reanudar una base de datos, use cualquiera de estos métodos individuales.

- [Comprobar el estado de la base de datos con T-SQL][Check database state with T-SQL]
- [Comprobar el estado de la base de datos con PowerShell][Check database state with PowerShell]
- [Comprobar el estado de la base de datos con API de REST][Check database state with REST APIs]

## <a name="permissions"></a>Permisos

Para escalar la base de datos, se requieren los permisos descritos en [ALTER DATABASE][ALTER DATABASE].  Para pausar y reanudar, se requiere el permiso [Colaborador de base de datos SQL][SQL DB Contributor], específicamente Microsoft.Sql/servers/databases/action.

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Pasos siguientes
Consulte los artículos siguientes para comprender mejor algunos conceptos fundamentales adicionales sobre rendimiento:

* [Administración de cargas de trabajo y simultaneidad][Workload and concurrency management]
* [Introducción al diseño de tablas][Table design overview]
* [Distribución de tablas][Table distribution]
* [Indexación de tablas][Table indexing]
* [Partición de tabla][Table partitioning]
* [Estadísticas de tabla][Table statistics]
* [Prácticas recomendadas][Best practices]

<!--Image reference-->

<!--Article references-->
[data warehouse units (DWUs)]: ./sql-data-warehouse-overview-what-is.md#predictable-and-scalable-performance-with-data-warehouse-units
[billed]: https://azure.microsoft.com/en-us/pricing/details/sql-data-warehouse/
[linearly]: ./sql-data-warehouse-overview-what-is.md#predictable-and-scalable-performance-with-data-warehouse-units
[Scale compute power with Azure portal]: ./sql-data-warehouse-manage-compute-portal.md#scale-compute-power
[Scale compute power with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#scale-compute-bk
[Scale compute power with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#scale-compute-bk
[Scale compute power with TSQL]: ./sql-data-warehouse-manage-compute-tsql.md#scale-compute-bk

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md

[Pause compute with Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md#pause-compute-bk
[Pause compute with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#pause-compute-bk
[Pause compute with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#pause-compute-bk

[Resume compute with Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md#resume-compute-bk
[Resume compute with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#resume-compute-bk
[Resume compute with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#resume-compute-bk

[Check database state with T-SQL]: ./sql-data-warehouse-manage-compute-tsql.md#check-database-state-and-operation-progress
[Check database state with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#check-database-state
[Check database state with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#check-database-state

[Workload and concurrency management]: ./sql-data-warehouse-develop-concurrency.md
[Table design overview]: ./sql-data-warehouse-tables-overview.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexing]: ./sql-data-warehouse-tables-index.md
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Table statistics]: ./sql-data-warehouse-tables-statistics.md
[Best practices]: ./sql-data-warehouse-best-practices.md
[development overview]: ./sql-data-warehouse-overview-develop.md

[SQL DB Contributor]: ../active-directory/role-based-access-built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Azure portal]: http://portal.azure.com/
