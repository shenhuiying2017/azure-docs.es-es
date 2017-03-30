---
title: "Mejora del rendimiento del índice de almacén de columnas en Azure SQL | Microsoft Docs"
description: "Reduzca los requisitos de memoria o aumente la memoria disponible para maximizar el número de filas que un índice de almacén de columnas comprime en cada grupo de filas."
services: sql-data-warehouse
documentationcenter: NA
author: shivaniguptamsft
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 11/18/2016
ms.author: shigu;barbkess
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 8d189256ed4c876859203406cda95ce0be36c96c
ms.lasthandoff: 03/29/2017


---

# <a name="memory-optimizations-for-columnstore-compression"></a>Optimizaciones de memoria para la compresión de almacén de columnas

Reduzca los requisitos de memoria o aumente la memoria disponible para maximizar el número de filas que un índice de almacén de columnas comprime en cada grupo de filas.  Emplee estos métodos para mejorar las tasas de compresión y el rendimiento de las consultas de los índices de almacén de columnas.

## <a name="why-the-rowgroup-size-matters"></a>Por qué importa el tamaño del grupo de filas
Como los índices de almacén de columnas examinan una tabla mediante el examen de segmentos de columna de grupos de filas individuales, al maximizar el número de filas de cada grupo de estas, se mejora el rendimiento de las consultas. Cuando los grupos de filas presentan un gran número de filas, la compresión de datos mejora; es decir, hay menos datos que se deben leer en el disco.

Para obtener más información sobre los grupos de filas, consulte [Guía de índices de almacén de columnas](https://msdn.microsoft.com/library/gg492088.aspx).

## <a name="target-size-for-rowgroups"></a>Tamaño objetivo de los grupos de filas
Para obtener el mejor rendimiento de consultas, el objetivo consiste en maximizar el número de filas por grupo de un índice de almacén de columnas. Un grupo de filas puede tener un máximo de 1.048.576 filas. No pasa nada si no se tiene el máximo número de filas por grupo de filas. Los índices de almacén de columnas logran un buen rendimiento cuando los grupos de filas cuentan con al menos 100.000 filas.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Los grupos de filas pueden recortarse durante la compresión

Durante una carga masiva o regeneración de índice de almacén columnas, a veces no queda suficiente memoria disponible para comprimir todas las filas designadas para cada grupo de filas. Cuando existe una presión de memoria, los índices de almacén de columnas recortan el tamaño de los grupos de filas para que se pueda realizar la compresión en el almacén de columnas.

Cuando no hay memoria suficiente para comprimir al menos 10.000 filas en cada grupo de filas, SQL Data Warehouse genera un error.

Para obtener más información sobre la carga masiva, consulte [Carga de datos en un índice de almacén de columnas agrupado](https://msdn.microsoft.com/en-us/library/dn935008.aspx#Bulk load into a clustered columnstore index).

## <a name="how-to-estimate-memory-requirements"></a>Cómo calcular los requisitos de memoria

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

La memoria máxima requerida para comprimir un grupo de filas es aproximadamente:

- 72 MB +
- \#filas \* \#columnas \* 8 bytes +
- \#filas \* \#columnas de cadena corta \* 32 bytes +
- \#columnas de cadena larga \* 16 MB para el diccionario de compresión

donde las columnas de cadena corta emplean tipos de datos de cadena de ≤32 bytes y las de cadena larga usan tipos de datos de cadena de >32 bytes.

Las cadenas largas que se comprimen con un método de compresión diseñado para comprimir texto. Este método de compresión utiliza un *diccionario* para almacenar patrones de texto. El tamaño máximo de un diccionario es de 16 MB. Hay solo un diccionario para cada columna de cadena larga del grupo de filas.

Para ver una discusión en profundidad sobre los requisitos de memoria del almacén de columnas, eche un vistazo al vídeo [Azure SQL Data Warehouse scaling: configuration and guidance](https://myignite.microsoft.com/videos/14822) (Escalado de Azure SQL Data Warehouse: configuración y directrices).

## <a name="ways-to-reduce-memory-requirements"></a>Formas de reducir los requisitos de memoria

Ponga en práctica las siguientes técnicas a fin de reducir los requisitos de memoria para comprimir los grupos de filas en índices de almacén de columnas.

### <a name="use-fewer-columns"></a>Utilizar menos columnas
Si es posible, diseñe la tabla con menos columnas. Cuando se comprime un grupo de filas en el almacén de columnas, el índice de este comprime cada segmento de columna por separado. Por lo tanto, los requisitos de memoria para comprimir un grupo de filas aumentan a la par que el número de columnas.


### <a name="use-fewer-string-columns"></a>Utilizar menos columnas de cadena
Las columnas de tipos de datos de cadena requieren más memoria que los tipos de datos numéricos y de fecha. Para reducir los requisitos de memoria, plantéese la posibilidad de quitar las columnas de cadena de las tablas de hechos y colocarlos en las tablas de dimensiones más pequeñas.

Requisitos de memoria adicionales para la compresión de cadenas:

- Los tipos de datos de cadena de hasta 32 caracteres pueden requerir 32 bytes adicionales por valor.
- Los tipos de datos de cadena con más de 32 caracteres se comprimen con métodos de diccionario.  Cada columna del grupo de filas puede requerir hasta 16 MB adicionales para crear el diccionario.

### <a name="avoid-over-partitioning"></a>Evitar un exceso de particiones

Los índices de almacén de columnas crean uno o varios grupos de filas por partición. En SQL Data Warehouse, el número de particiones aumenta rápidamente porque los datos se distribuyen y cada distribución se particiona. Si la tabla presenta demasiadas particiones, es posible que no haya un número de filas suficiente para llenar los grupos de filas. La falta de filas no genera presión de memoria durante la compresión, pero da lugar a grupos de filas que no logran el mejor rendimiento de consultas de almacén de columnas.

Otro motivo para evitar el exceso de particiones es que cargar filas en un índice de columnas en una tabla particionada conlleva una sobrecarga para la memoria. Durante la carga, muchas particiones podrían recibir las filas entrantes, que se guardan en la memoria hasta que cada partición alcance el número de filas suficiente para llevar a cabo la compresión. Si se cuenta con demasiadas particiones, se genera una presión de memoria adicional.

### <a name="simplify-the-load-query"></a>Simplificar la consulta de carga

La base de datos comparte la concesión de memoria para una consulta con todos los operadores de esta. Cuando una consulta de carga presenta ordenaciones y uniones complejas, se reduce la memoria disponible para la compresión.

Diseñe la consulta de carga para que se centre únicamente en cargar la consulta. Si tiene que ejecutar transformaciones en los datos, ejecútelas de forma independiente de la consulta de carga. Por ejemplo, almacene provisionalmente los datos en una tabla de montón, ejecute las transformaciones y, después, cargue la tabla de almacenamiento provisional en el índice de almacén de columnas. Puede cargar los datos primero y, después, usar el sistema MPP para transformar los datos.

### <a name="adjust-maxdop"></a>Ajustar MAXDOP

Cada distribución comprime los grupos de filas en el almacén de columnas en paralelo cuando hay más de un núcleo de CPU disponible por distribución. El paralelismo requiere recursos de memoria adicionales, lo que puede generar presión de memoria y recorte de los grupos de filas.

Para reducir la presión de memoria, puede usar la sugerencia de consulta MAXDOP a fin de forzar la operación de carga con el objetivo de que se ejecute en modo serie dentro de cada distribución.

```
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQUota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Formas de asignar más memoria

Juntos, el tamaño de DWU y la clase de recursos de usuario, determinan cuánta memoria hay disponible para la consulta de un usuario. A fin de incrementar la concesión de memoria para una consulta de carga, puede aumentar el número de DWU o la clase de recursos.

- Para aumentar las DWU, consulte [¿Cómo se realiza el escalado del rendimiento?](sql-data-warehouse-manage-compute-overview.md#scale-compute)
- Para cambiar la clase de recursos de una consulta, consulte [Cambio de ejemplo de clase de recursos de usuario](sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example).

Por ejemplo, con 100 DWU, un usuario de la clase de recursos smallrc puede usar 100 MB de memoria para cada distribución. Para obtener información más detallada, consulte el artículo sobre [simultaneidad en SQL Data Warehouse](sql-data-warehouse-develop-concurrency.md).

Supongamos que decide que necesita 700 MB de memoria para obtener tamaños de grupo de filas de alta calidad. Estos ejemplos muestran cómo se puede ejecutar la consulta de carga con suficiente memoria.

- Con 1000 DWU y mediumrc, la concesión de memoria es de 800 MB.
- Con 600 DWU y largerc, la concesión de memoria es de 800 MB.


## <a name="next-steps"></a>Pasos siguientes

Para descubrir más formas de mejorar el rendimiento en SQL Data Warehouse, consulte el tema de [información general sobre rendimiento](sql-data-warehouse-overview-manage-user-queries.md).

<!--Image references-->

<!--Article references-->


<!--MSDN references-->

<!--Other Web references-->

