---
title: "Cómo funcionan los datos distribuidos en Azure SQL Data Warehouse | Microsoft Docs"
description: "Obtenga información sobre cómo se distribuyen los datos para el procesamiento paralelo masivo (MPP) y las opciones de distribución de tablas de SQL Data Warehouse y Almacenamiento de datos paralelos de Azure."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: bae494a6-7ac5-4c38-8ca3-ab2696c63a9f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 07/12/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 3c166acb17193caae32d7bad133ec510ff679353
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="distributed-data-and-distributed-tables-for-massively-parallel-processing-mpp"></a>Datos distribuidos y tablas distribuidas para el procesamiento paralelo masivo (MPP)
Obtenga información sobre cómo se distribuyen los datos de usuario en SQL Data Warehouse y Almacenamiento de datos paralelos de Azure, que son sistemas de procesamiento paralelo masivo (MPP) de Microsoft. Diseñar el almacenamiento de datos de tal forma que se utilicen los datos distribuidos de forma eficaz ayuda a lograr las ventajas de la arquitectura MPP de procesamiento de consultas. Algunas opciones de diseño de bases de datos pueden mejorar de manera significativa el rendimiento de las consultas.  

> [!NOTE]
> SQL Data Warehouse y Almacenamiento de datos paralelos de Azure utilizan el mismo diseño de procesamiento paralelo masivo (MPP), aunque con algunas diferencias debidas a la plataforma subyacente. SQL Data Warehouse es una plataforma como servicio (PaaS) que se ejecuta en Azure. Almacenamiento de datos paralelos se ejecuta en Analytics Platform System (APS), que es una aplicación local que se ejecuta en Windows Server.
> 
> 

## <a name="what-is-distributed-data"></a>¿Qué son los datos distribuidos?
En SQL Data Warehouse y Almacenamiento de datos paralelos, el término datos distribuidos se refiere a los datos de usuario que están almacenados en diversas ubicaciones del sistema MPP. Cada una de estas ubicaciones actúa como una unidad de almacenamiento y procesamiento independiente que ejecuta consultas en la parte de los datos que le corresponde. Los datos distribuidos son fundamentales para ejecutar consultas en paralelo con un rendimiento elevado.

Para distribuir los datos, el almacenamiento de datos asigna cada fila de una tabla de usuario a una ubicación distribuida.  También pueden distribuirse las tablas con un método de distribución basado en una función hash o con un método round robin. El método de distribución se especifica en la instrucción CREATE TABLE. 

## <a name="hash-distributed-tables"></a>Tablas distribuidas mediante una función hash
El siguiente diagrama muestra cómo se almacena una tabla completa (no distribuida) como una tabla distribuida mediante una función hash. Una función determinista asigna cada fila de manera que pertenezca a una distribución. En la definición de tabla, una de las columnas se designa como columna de distribución. La función hash utiliza el valor de la columna de distribución para asignar cada fila a una distribución.

Es preciso tener en cuenta consideraciones de rendimiento al seleccionar una columna de distribución, tales como la diferenciación, la asimetría de datos o los tipos de consultas se ejecutan en el sistema.

![Tabla distribuida](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "Tabla distribuida")  

* Cada fila pertenece a una distribución.  
* Un algoritmo hash determinista asigna cada fila a una distribución.  
* El número de filas de la tabla por cada distribución varía, lo que se hace patente en los diferentes tamaños de tablas.

## <a name="round-robin-distributed-tables"></a>Tablas distribuidas con el método round robin
Una tabla distribuida con el método round robin distribuye las filas asignando cada una de ellas a una distribución por orden secuencial. En una tabla de round robin, los datos se cargan rápidamente, pero el rendimiento de las consultas puede ser más lento.  Para combinar una tabla de round robin suele ser preciso reconstruir las filas para que la consulta genere un resultado exacto, lo que requiere tiempo.

## <a name="distributed-storage-locations-are-called-distributions"></a>Las ubicaciones de almacenamiento distribuidas se denominan distribuciones
Cada ubicación distribuida se denomina distribución. Cuando se ejecuta una consulta en paralelo, cada distribución lleva a cabo una consulta SQL en su parte de datos correspondiente. SQL Data Warehouse utiliza SQL Database para ejecutar la consulta. Almacenamiento de datos paralelos utiliza SQL Database para ejecutar la consulta. Este diseño de arquitectura en que no se comparte nada es fundamental para lograr la informática en paralelo con escalado horizontal.

### <a name="can-i-view-the-distributions"></a>¿Se pueden ver las distribuciones?
Cada distribución tiene un Id. de distribución y es visible en las vistas del sistema que pertenecen a SQL Data Warehouse y Almacenamiento de datos paralelos. Puede utilizar el Id. de distribución para solucionar problemas de rendimiento de las consultas y otros problemas. Para obtener una lista de las vistas del sistema, consulte la [vista del sistema de MPP](sql-data-warehouse-reference-tsql-statements.md).

## <a name="difference-between-a-distribution-and-a-compute-node"></a>Diferencia entre una distribución y un nodo de ejecución
Una distribución es la unidad básica para almacenar datos distribuidos y procesar consultas en paralelo. Las distribuciones se agrupan en nodos de ejecución. Cada nodo de ejecución realiza el seguimiento de una o varias distribuciones.  

* Analytics Platform System usa nodos de ejecución como componente central de la arquitectura de hardware y de las prestaciones de escalado horizontal. Siempre utiliza ocho distribuciones por nodo de ejecución, como se muestra en el diagrama para las tablas distribuidas mediante una función hash. El número de nodos de ejecución y, por lo tanto, el número de distribuciones, viene determinado por el número de nodos de ejecución adquiridos para la aplicación. Por ejemplo, si adquiere ocho nodos de ejecución, obtendrá 64 distribuciones (8 nodos de ejecución x 8 distribuciones/nodo). 
* SQL Data Warehouse tiene una cantidad de distribuciones (60) y un número flexible de nodos de ejecución. Los nodos de ejecución se implementan con recursos informáticos y de almacenamiento de Azure. La cantidad de nodos de ejecución puede variar según la carga de trabajo del servicio de back-end y la capacidad informática (en DWU) que se especifique para el almacenamiento de datos. Cuando cambia el número de nodos de ejecución, también varía el número de distribuciones por nodo de ejecución. 

### <a name="can-i-view-the-compute-nodes"></a>¿Se pueden ver los nodos de ejecución?
Cada nodo de ejecución tiene un Id. de nodo y es visible en las vistas del sistema que pertenecen a SQL Data Warehouse y Almacenamiento de datos paralelos.  Para ver el nodo de ejecución, busque la columna node_id en las vistas del sistema cuyos nombres comiencen por sys.pdw_nodes. Para obtener una lista de las vistas del sistema, consulte la [vista del sistema de MPP](sql-data-warehouse-reference-tsql-statements.md).

## <a name="Replicated"></a>Tablas replicadas
Una tabla que se replica tiene una copia completa de la tabla almacenada en cada nodo de proceso. Al replicar una tabla se elimina la necesidad de transferir sus datos de un nodo de proceso a otro antes de una combinación o agregación. Las tablas replicadas solo son viables si son pequeñas, debido a que requieren más espacio para almacenarlas enteras en cada nodo de ejecución.  

En el diagrama siguiente se muestra una tabla replicada que se ha almacenado en cada nodo de ejecución. Para SQL Data Warehouse, la tabla replicada se copia completa en una base de datos de distribución en cada nodo de ejecución. En el caso de Almacenamiento de datos paralelo, la tabla replicada se almacena en todos los discos asignados al nodo de proceso.  Esta estrategia de disco se implementa mediante el uso de grupos de archivos de SQL Server.  

![Tabla replicada](media/sql-data-warehouse-distributed-data/replicated-table.png "Tabla replicada") 

## <a name="next-steps"></a>Pasos siguientes
Para usar tablas distribuidas de forma eficaz, consulte [Distributing tables in SQL Data Warehouse](sql-data-warehouse-tables-distribute.md) (Distribución de tablas en SQL Data Warehouse).  

