<properties
   pageTitle="Administración de índices | Microsoft Azure"
   description="Orientación para ayudar a los usuarios a administrar sus índices"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/18/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Administración de índices
En este artículo se muestran algunas técnicas clave para la administración de índices.

## Optimización de regeneraciones de índices
Puede optimizar la regeneración de índices de una de las dos formas siguientes:

1. Realice una partición en la tabla y ejecute la regeneración de los índices en el nivel de partición.
2. Utilice [CTAS][] para volver a crear la partición de los datos en una tabla nueva y realice el cambio de partición en la nueva tabla.

## Regeneración de índices con particiones

A continuación se muestra un ejemplo sobre cómo generar una partición única:

```
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

ALTER INDEX... REBUILD se utiliza para volúmenes de datos más pequeños, especialmente para los índices de almacén de columnas. En la regeneración se incluyen los grupos de filas abiertos, cerrados y comprimidos. Sin embargo, si la partición es bastante grande, verá que `CTAS` es la operación más eficaz. A continuación, se muestra un ejemplo de regeneración de índices completa.

```
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

Consulte el artículo [ALTER INDEX][] para obtener más detalles sobre esta sintaxis.

## Uso de CTAS para regenerar una partición

A continuación se muestra un ejemplo sobre cómo regenerar una partición mediante CTAS:

```
-- Step 01. Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 02. Create a SWITCH out table
 
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 03. Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 04. Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;

```

## Pasos siguientes
Consulte el artículo sobre las [particiones de tablas][] para obtener instrucciones sobre cómo crear y cambiar el tamaño de las particiones. El artículo también contiene un ejemplo para ayudar a identificar los límites de las particiones.

Para obtener más sugerencias de administración, consulte la información general de [administración][].

<!--Image references-->

<!--Article references-->
[CTAS]: sql-data-warehouse-develop-ctas.md
[particiones de tablas]: sql-data-warehouse-develop-table-partitions.md
[administración]: sql-data-warehouse-manage-monitor.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/es-ES/library/ms188388.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0323_2016-->