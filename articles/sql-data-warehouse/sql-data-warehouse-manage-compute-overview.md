---
title: "Administración de la potencia de proceso en Azure SQL Data Warehouse (introducción) | Microsoft Docs"
description: Funcionalidades de escalado horizontal del rendimiento en Almacenamiento de datos SQL de Azure. Realice el escalado horizontal ajustando las DWU o pause y reanude los recursos de proceso para ahorrar costos.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: e13a82b0-abfe-429f-ac3c-f2b6789a70c6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 5d3bcc3c1434b16279778573ccf3034f9ac28a4d
ms.openlocfilehash: 6871ab3bc25ab3ec7b3c60852aa06bee047d8e9a
ms.lasthandoff: 12/06/2016


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

La arquitectura del Almacenamiento de datos SQL separa el proceso y el almacenamiento, lo que permite a cada uno escalar de manera independiente. Como resultado, puede realizar el escalado horizontal del rendimiento mientras ahorra costos al pagar solo el rendimiento cuando lo necesite.

Esta información general describe las siguientes capacidades de escalabilidad horizontal de rendimiento de Almacenamiento de datos SQL y proporciona recomendaciones sobre cómo y cuándo utilizarlas.

* Escalado de la potencia de proceso mediante el ajuste de unidades de [almacenamiento de datos (DWU)][data warehouse units (DWUs)]
* Pausa o reanudación de recursos de proceso

<a name="scale-performance-bk"></a>

## <a name="scale-performance"></a>Rendimiento a escala
En Almacenamiento de datos SQL, puede escalar horizontalmente o de nuevo de forma rápida el rendimiento mediante el aumento o disminución de los recursos de proceso de la CPU, la memoria y el ancho de banda de E/S. Para escalar el rendimiento, todo lo que necesita hacer es ajustar el número de [unidades de almacenamiento de datos (DWU)][data warehouse units (DWUs)] que asigna SQL Data Warehouse. Almacenamiento de datos SQL realiza el cambio y controla todos los cambios subyacentes al hardware o software rápidamente.

Lejos quedan los días en que había que investigar qué tipo de procesadores, qué cantidad de memoria o qué tipo de almacenamiento era necesario para obtener un rendimiento excelente en el almacenamiento de datos. Al colocar el Almacenamiento de datos en la nube, ya no es necesario preocuparse por los problemas de hardware de bajo nivel. En su lugar, Almacenamiento de datos SQL formula esta pregunta: ¿con qué rapidez se desean analizar los datos?

### <a name="how-do-i-scale-performance"></a>¿Cómo se realiza el escalado del rendimiento?
Para ampliar aumentar o disminuir de manera elástica la capacidad de proceso, basta con cambiar la configuración de las [unidades de almacenamiento de datos (DWU)][data warehouse units (DWUs)] para la base de datos. El rendimiento aumentará linealmente a medida que agregue más DWU.  En niveles superiores de DWU, debe agregar más de 100 DWU para observar una mejora considerable del rendimiento. Para ayudarle a seleccionar saltos significativos en las DWU, le ofrecemos los niveles DWU que proporcionarán los mejores resultados.

Para ajustar las DWU, puede utilizar cualquiera de estos métodos individuales.

* [Escalado de la potencia de proceso con Azure Portal][Scale compute power with Azure portal]
* [Escalado de la potencia de proceso con PowerShell][Scale compute power with PowerShell]
* [Escalado de la potencia de proceso con API de REST][Scale compute power with REST APIs]
* [Escalado de la potencia de proceso con TSQL][Scale compute power with TSQL]

### <a name="how-many-dwus-should-i-use"></a>¿Cuántas DWU debería usar?
El rendimiento en el Almacenamiento de datos SQL se amplía de manera lineal y el cambio de una escala de proceso a otra (por ejemplo, de 100 DWU a 2000 DWU) se produce en segundos. Esto le ofrece la flexibilidad necesaria para experimentar con distintas configuraciones de DWU hasta que determine el escenario que mejor se ajuste.

Para entender el valor ideal de su DWU, pruebe a escalar y reducir verticalmente y a ejecutar algunas consultas después de cargar los datos. Dado que el escalado se realiza rápidamente, puede intentar un número de distintos niveles de rendimiento en una hora o menos. Tenga en cuenta que Almacenamiento de datos SQL está diseñado para procesar grandes cantidades de datos y para ver sus verdaderas funcionalidades de escala, especialmente en las escalas más grandes que ofrecemos, es conveniente utilizar un conjunto grande de datos que se aproxime o supere 1 TB.

Recomendaciones para encontrar la mejor DWU para la carga de trabajo:

1. Para un almacenamiento de datos en desarrollo, comience seleccionando un número pequeño de DWU.  Un buen punto de partida es DW400 o DW200.
2. Supervise el rendimiento de su aplicación, observando el número de DWU seleccionados en comparación con el rendimiento que observe.
3. Determine en qué grado debería ser el rendimiento más rápido o más lento para poder alcanzar el nivel óptimo de rendimiento para sus requerimientos suponiendo una escala lineal.
4. Aumente o disminuya el número de DWU en proporción a la rapidez con la que quiere que funcione la carga de trabajo. El servicio responderá rápidamente y ajustará los recursos de proceso para satisfacer los nuevos requisitos de DWU.
5. Continúe realizando ajustes hasta llegar a un nivel de rendimiento adecuado para sus requerimientos empresariales.

### <a name="when-should-i-scale-dwus"></a>¿Cuándo debo realizar un escalado de DWU?
Cuando necesite resultados más rápidos, aumente el número de DWU y pague por un mayor rendimiento.  Cuando necesite menos capacidad de proceso, reduzca el número de DWU y pague solo por lo que necesita.

Recomendaciones de cuándo realizar el escalado de DWU:

1. Si su aplicación tiene una carga de trabajo que varía, realice un escalado vertical y una reducción vertical de los niveles de DWU para responder a los valores máximos y los puntos más bajos. Por ejemplo, si por lo general la carga de trabajo aumenta considerablemente a final de mes, planee agregar más DWU durante esos días de máxima actividad y luego reduzca su número verticalmente cuando termine este período.
2. Para realizar una operación de transformación o carga de una gran cantidad de datos, realice el escalado vertical de DWU para que los datos estén disponibles con mayor rapidez.

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

## <a name="permissions"></a>Permisos
Para escalar la base de datos, se requieren los permisos descritos en [ALTER DATABASE][ALTER DATABASE].  Para pausar y reanudar, se requiere el permiso [Colaborador de base de datos SQL][SQL DB Contributor], específicamente Microsoft.Sql/servers/databases/action.

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Pasos siguientes
Consulte los artículos siguientes para comprender mejor algunos conceptos fundamentales adicionales sobre rendimiento:

* [Administración de cargas de trabajo y simultaneidad][Workload and concurrency managment]
* [Introducción al diseño de tablas][Table design overview]
* [Distribución de tablas][Table distribution]
* [Indexación de tablas][Table indexing]
* [Partición de tabla][Table partitioning]
* [Estadísticas de tabla][Table statistics]
* [Prácticas recomendadas][Best practices]

<!--Image reference-->

<!--Article references-->
[data warehouse units (DWUs)]: ./sql-data-warehouse-overview-what-is.md

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

[Workload and concurrency managment]: ./sql-data-warehouse-develop-concurrency.md
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

