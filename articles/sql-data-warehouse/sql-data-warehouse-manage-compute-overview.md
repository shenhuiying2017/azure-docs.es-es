---
title: "Administración de recursos de proceso en Azure SQL Data Warehouse | Microsoft Docs"
description: "Conozca las funcionalidades de escalado horizontal del rendimiento en Azure SQL Data Warehouse. Puede escalar horizontalmente mediante el ajuste de las DWU o detener el almacén de datos para reducir los costos."
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
ms.date: 02/20/2018
ms.author: elbutter
ms.openlocfilehash: 7e6ae6e59b53dd79dab5e2504cf7a43a30e55353
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="manage-compute-in-azure-sql-data-warehouse"></a>Administración de proceso en Azure SQL Data Warehouse
Aprenda sobre la administración de recursos de proceso en Azure SQL Data Warehouse. Para reducir los costos detenga el almacén de datos o escálelo para satisfacer las demandas de rendimiento. 

## <a name="what-is-compute-management"></a>¿Qué es la administración de proceso?
La arquitectura de SQL Data Warehouse separa el proceso y el almacenamiento, lo que permite a cada uno escalar de manera independiente. Como resultado, puede escalar el proceso para satisfacer las demandas de rendimiento con independencia del almacenamiento de datos. También puede pausar y reanudar los recursos de proceso. Una consecuencia natural de esta arquitectura es que la [facturación](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) por proceso y almacenamiento está separada. Si no es necesario usar el almacenamiento de datos durante un tiempo, puede detener el proceso para ahorrar costos de proceso. 

## <a name="scaling-compute"></a>Escalado de proceso
Para escalar horizontalmente o reducir la escala del proceso, ajuste el valor de las [unidades de almacenamiento de datos](what-is-a-data-warehouse-unit-dwu-cdwu.md) de su almacenamiento de datos. La carga y el rendimiento de las consultas pueden aumentar linealmente a medida que agrega más unidades de almacenamiento de datos. SQL Data Warehouse ofrece [niveles de servicio](performance-tiers.md#service-levels) para unidades de almacenamiento de datos que garantizan un cambio significativo en el rendimiento al escalar horizontalmente o reducir la escala. 

Para conocer los pasos de escalado horizontal, consulte las guías de inicio rápido de [Azure Portal](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md) o [T-SQL](quickstart-scale-compute-tsql.md). También puede realizar operaciones de escalado horizontal con una [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Para realizar una operación de escalado, SQL Data Warehouse termina primero con todas las solicitudes de entrada y, luego, revierte las transacciones para garantizar un estado coherente. El escalado solo se produce una vez finalizada la reversión de la transacción. En una operación de escalado, el sistema desasocia la capa de almacenamiento de los nodos de proceso, agrega nodos de proceso y, luego, vuelve a asociar la capa de almacenamiento a la capa de proceso. Cada almacenamiento de datos se almacena como 60 distribuciones, que se distribuyen de manera uniforme a los nodos de proceso. Al agregar más nodos de proceso, se agrega más capacidad de proceso. A medida que aumenta el número de nodos de proceso, se reduce el número de distribuciones por nodo de proceso, de forma que hay más capacidad de proceso para las consultas. De igual manera, al reducir las unidades de almacenamiento de datos se reduce el número de nodos de proceso, de forma que se reducen los recursos de proceso para las consultas.

En la tabla siguiente se muestra cómo cambia el número de distribuciones por nodo de proceso a medida que cambian las unidades de almacenamiento de datos.  DWU6000 proporciona 60 nodos de proceso y consigue un rendimiento mucho mayor que DWU100. 

| Unidades de almacenamiento de datos  | \# de nodos de proceso | \# de distribuciones por nodo |
| ---- | ------------------ | ---------------------------- |
| 100  | 1                  | 60                           |
| 200  | 2                  | 30                           |
| 300  | 3                  | 20                            |
| 400  | 4                  | 15                           |
| 500  | 5                  | 12                           |
| 600  | 6                  | 10                           |
| 1000 | 10                 | 6                            |
| 1200 | 12                 | 5                            |
| 1.500 | 15                 | 4                            |
| 2000 | 20                  | 3                            |
| 3000 | 30                 | 2                            |
| 6000 | 60                 | 1                            |


## <a name="finding-the-right-size-of-data-warehouse-units"></a>Búsqueda del tamaño adecuado de las unidades de almacenamiento de datos

Para ver las ventajas de rendimiento del escalado horizontal, en especial para unidades de almacenamiento de datos más grandes, querrá usar al menos un conjunto de datos de 1 TB. Para encontrar el mejor número de unidades de almacenamiento de datos para el almacenamiento de datos, intente escalar y reducir verticalmente. Ejecute algunas consultas con distintos números de unidades de almacenamiento de datos después de cargar los datos. Dado que el escalado se realiza rápidamente, puede probar varios niveles de rendimiento en una hora o menos. 

Recomendaciones para encontrar el mejor número de unidades de almacenamiento de datos:

- Para un almacenamiento de datos en desarrollo, comience por seleccionar un número más pequeño de unidades de almacenamiento de datos.  Un buen punto de partida es DW400 o DW200.
- Supervise el rendimiento de la aplicación y observe el número de unidades de almacenamiento de datos seleccionadas en comparación con el rendimiento observado.
- Suponga una escala lineal y determine cuánto debe aumentar o reducir las unidades de almacenamiento de datos. 
- Continúe realizando ajustes hasta llegar a un nivel de rendimiento adecuado para sus requerimientos empresariales.

## <a name="when-to-scale-out"></a>Cuándo escalar horizontalmente
El escalado horizontal de unidades de almacenamiento de datos afecta a los siguientes aspectos de rendimiento:

- Mejora linealmente el rendimiento del sistema en lo referente a exámenes, agregaciones e instrucciones CTAS.
- Aumenta el número de lectores y escritores para cargar los datos.
- Número máximo de consultas simultáneas y ranuras de simultaneidad.

Recomendaciones para saber cuándo escalar horizontalmente las unidades de almacenamiento de datos:

- Para realizar una operación de transformación o carga de con muchos datos, realice el escalado horizontal para que los datos estén disponibles con mayor rapidez.
- Durante las horas punta comerciales, escale horizontalmente para dar cabida a un mayor número de consultas simultáneas. 

## <a name="what-if-scaling-out-does-not-improve-performance"></a>¿Qué ocurre si escalado horizontal no mejora el rendimiento?

La adición de unidades de almacenamiento de datos aumenta el paralelismo. Si el trabajo se divide uniformemente entre los nodos de proceso, el paralelismo adicional mejora el rendimiento de las consultas. Si el escalado horizontal no cambia el rendimiento, hay algunos motivos por los que puede ocurrir. Puede que los datos se sesguen entre las distribuciones o que las consultas introduzcan un movimiento de gran cantidad de datos. Para investigar los problemas de rendimiento de consultas, consulte las [soluciones a los problemas de rendimiento](sql-data-warehouse-troubleshoot.md#performance). 

## <a name="pausing-and-resuming-compute"></a>Pausa y reanudación del proceso
Al pausar el proceso, la capa de almacenamiento se desasocia de los nodos de proceso. Se liberan los recursos de proceso desde su cuenta. No se le cobra por el proceso mientras está en pausa. Al reanudarse el proceso, se vuelve a asociar el almacenamiento con los nodos de proceso y se reanudan los cargos correspondientes. Al pausar un almacenamiento de datos:

* Los recursos de memoria y proceso se devuelven al grupo de recursos disponibles en el centro de datos
* Los costos de unidad de almacenamiento de datos son cero lo que dura la pausa.
* El almacenamiento de datos no se ve afectado y sus datos permanecen intactos. 
* SQL Data Warehouse cancela todas las operaciones de ejecución o en cola.

Al reanudarse un almacenamiento de datos:

* SQL Data Warehouse adquiere recursos de memoria y proceso equivalentes a su valor de unidades de almacenamiento de datos.
* Los cargos por la reanudación de las unidades de almacenamiento de datos se reanudan también.
* Los datos se vuelven disponibles.
* Una vez que el almacenamiento de datos está en línea, debe reiniciar las consultas de carga de trabajo.

Si quiere que el almacenamiento de datos esté siempre accesible, puede escalarlo verticalmente al tamaño más pequeño en lugar de pausarlo. 

Para conocer los pasos de pausa y reanudación, consulte las guías de inicio rápido de [Azure Portal](pause-and-resume-compute-portal.md), o [PowerShell](pause-and-resume-compute-powershell.md). También puede usar la [REST API de pausa](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) o la [REST API de reanudación](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Vaciado de las transacciones antes de aplicar la pausa o el escalado
Se recomienda permitir que finalicen las transacciones existentes antes de iniciar una operación de pausa o escalado.

Al pausar o escalar SQL Data Warehouse, las consultas se cancelan en segundo plano al iniciar la solicitud de pausa o escalado.  Una simple consulta de selección se cancela rápidamente y no afecta casi al tiempo que se tarda en aplicar la pausa o la escala a la instancia.  Sin embargo, las consultas sobre transacciones, que modifican los datos o la estructura de estos, no se puede detener rápidamente.  **Por definición, las consultas sobre transacciones deben completarse íntegramente o deshacerse los cambios.**  Deshacer el trabajo realizado con una consulta sobre una transacción puede tardar tanto (o más) que el tiempo necesario para aplicar el cambio original de esta.  Por ejemplo, si se cancela una consulta que se eliminaba filas y ya lleva en ejecución una hora, el sistema puede tardar una hora para reinsertar las filas eliminadas.  Si se ejecuta la pausa o la escala con transacciones en curso, puede parecer que tardan mucho en aplicarse, ya que deben esperar a que se deshagan todos los cambios para continuar.

Consulte también [Transacciones](sql-data-warehouse-develop-transactions.md) y [Optimización de transacciones](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Automatización de la administración de proceso
Para automatizar las operaciones de administración de proceso, consulte [Administración de proceso con Azure Functions](manage-compute-with-azure-functions.md).

Cada una de las operaciones de escalado horizontal, pausar y reanudación puede tardar varios minutos en completarse. Si va a escalar, pausar o reanudar automáticamente, se recomienda implementar una lógica para garantizar que determinadas operaciones se hayan completado antes de continuar con otra acción. La comprobación del estado del almacenamiento de datos a través de varios puntos de conexión le permite implementar correctamente la automatización de tales operaciones. 

Para comprobar el estado del almacenamiento de datos, consulte la guía de inicio rápido de [PowerShell](quickstart-scale-compute-powershell.md#check-database-state) o [T-SQL](quickstart-scale-compute-tsql.md#check-database-state). También puede comprobar el estado del almacenamiento de datos con una [REST API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).


## <a name="permissions"></a>Permisos

Para escalar el almacenamiento de datos, se requieren los permisos descritos en [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse.md).  Para pausar y reanudar, se requiere el permiso [Colaborador de base de datos SQL](../active-directory/role-based-access-built-in-roles.md#sql-db-contributor), en concreto Microsoft.Sql/servers/databases/action.


## <a name="next-steps"></a>pasos siguientes
Otro aspecto de la administración de recursos de proceso es la asignación de diferentes recursos de proceso para consultas individuales. Para más información, consulte [Clases de recursos para la administración de cargas de trabajo](resource-classes-for-workload-management.md).
