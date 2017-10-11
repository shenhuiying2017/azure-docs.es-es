---
title: "Migración de un almacenamiento de datos de Azure existente a Premium Storage | Microsoft Docs"
description: Instrucciones para migrar un almacenamiento de datos existente a Premium Storage
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: barbkess
editor: 
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 11/29/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 860e50b532b4b0a21d3be54f087730070b0e56bb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="migrate-your-data-warehouse-to-premium-storage"></a>Migración del almacenamiento de datos a Premium Storage
Azure SQL Data Warehouse ha introducido recientemente [Premium Storage para poder predecir el rendimiento de manera más eficaz][premium storage for greater performance predictability]. El almacenamiento de datos existente en el almacenamiento estándar se puede migrar a Premium Storage. Puede aprovechar las ventajas de la migración automática, o si desea controlar cuándo realizar la migración (que implica cierto tiempo de inactividad), puede realizar la migración manualmente.

Si tiene más de un almacenamiento de datos, use el [programa de migración automática][automatic migration schedule] siguiente para determinar cuándo se migrará.

## <a name="determine-storage-type"></a>Determinación del tipo de almacenamiento
Si creó un almacenamiento de datos antes de las fechas siguientes, significa que actualmente utiliza el almacenamiento estándar.

| **Región** | **Almacenamiento de datos creado antes de esta fecha** |
|:--- |:--- |
| Australia Oriental |Premium Storage no está disponible todavía |
| Este de China |1 de noviembre de 2016 |
| Norte de China |1 de noviembre de 2016 |
| Centro de Alemania |1 de noviembre de 2016 |
| Noreste de Alemania |1 de noviembre de 2016 |
| India occidental |Premium Storage no está disponible todavía |
| Oeste de Japón |Premium Storage no está disponible todavía |
| Centro-Norte de EE. UU |10 de noviembre de 2016 |

## <a name="automatic-migration-details"></a>Información de la migración automática
De forma predeterminada, la base de datos se migrará automáticamente durante las 18:00 y las 6:00 en la hora local de su región durante la [programación de migración automática][automatic migration schedule]. No podrá usar el almacenamiento de datos existente durante la migración. El proceso de migración durará aproximadamente una hora por cada terabyte de almacenamiento de cada almacenamiento de datos. No se le cobrará nada en ningún momento de la migración automática.

> [!NOTE]
> Cuando finalice la migración, el almacenamiento de datos volverá a estar en línea y utilizable.
>
>

Microsoft está llevando a cabo los pasos siguientes para completar la migración (no se requiere ninguna intervención por parte del usuario). Para este ejemplo, imagine que su almacenamiento de datos del almacenamiento estándar actualmente se llama "MiAD".

1. Microsoft cambia el nombre de "MiAD" por "MiAD_DO_NOT_USE_[Marca de tiempo]".
2. Microsoft pausa "MiAD_DO_NOT_USE_[Marca de tiempo]". Durante este tiempo, se crea una copia de seguridad. Es posible que observe que se llevan a cabo varias tareas de pausa y reanudación si se producen problemas durante este proceso.
3. Microsoft crea un nuevo almacenamiento de datos llamado "MiAD" en Premium Storage a partir de la copia de seguridad creada en el paso 2. miAD no aparecerá hasta que acabe el proceso de restauración.
4. Una vez que finalice, "MiAD" vuelve a las mismas unidades de almacenamiento de datos y al estado (en pausa o activo) que había antes de la migración.
5. Una vez que finaliza la migración, Microsoft elimina "MiAD_DO_NOT_USE_[Marca de tiempo]".

> [!NOTE]
> La configuración siguiente no se transmite como parte de la migración:
>
> * Se debe volver a habilitar la auditoría en el nivel de base de datos.
> * Se deben volver a agregar las reglas de firewall del nivel de base de datos. Las reglas de firewall del nivel de servidor no se verán afectadas.
>
>

### <a name="automatic-migration-schedule"></a>programa de migración automática
Las migraciones automáticas se llevan a cabo desde las 18:00 a las 6:00 (hora local por región) durante el programa de interrupción siguiente.

| **Región** | **Fecha de inicio estimada** | **Fecha de finalización estimada** |
|:--- |:--- |:--- |
| Australia Oriental |Sin determinar |Sin determinar |
| Este de China |9 de enero de 2017 |13 de enero de 2017 |
| Norte de China |9 de enero de 2017 |13 de enero de 2017 |
| Centro de Alemania |9 de enero de 2017 |13 de enero de 2017 |
| Noreste de Alemania |9 de enero de 2017 |13 de enero de 2017 |
| India occidental |Sin determinar |Sin determinar |
| Oeste de Japón |Sin determinar |Sin determinar |
| Centro-Norte de EE. UU |9 de enero de 2017 |13 de enero de 2017 |

## <a name="self-migration-to-premium-storage"></a>Migración manual a Premium Storage
Si desea controlar cuándo se producen los tiempos de inactividad, puede realizar los pasos siguientes para migrar un almacenamiento de datos existente del almacenamiento estándar a Premium Storage. Si elige esta opción, debe completar la migración manual antes de que comience la migración automática en dicha región. Esto garantiza que se evite que cualquier riesgo de la migración automática cause algún conflicto (vea la [programación de la migración automática][automatic migration schedule]).

### <a name="self-migration-instructions"></a>Instrucciones de migración manual
Para migrar el almacenamiento de datos manualmente, utilice las características de copia de seguridad y restauración. Se espera que el componente de restauración de la migración dure una hora aproximadamente por cada terabyte de almacenamiento en cada almacenamiento de datos. Si desea conservar el mismo nombre cuando se complete la migración, siga estos [pasos para cambiar el nombre durante la migración][steps to rename during migration].

1. [Detenga][Pause] el almacenamiento de datos. Esto realiza una copia de seguridad automática.
2. Lleve a cabo la [restauración][Restore] a partir de la instantánea más reciente.
3. Elimine el almacenamiento de datos existente del almacenamiento estándar. **Si no puede realizar este paso, se le cobrará por los dos almacenamientos de datos.**

> [!NOTE]
> La configuración siguiente no se transmite como parte de la migración:
>
> * Se debe volver a habilitar la auditoría en el nivel de base de datos.
> * Se deben volver a agregar las reglas de firewall del nivel de base de datos. Las reglas de firewall del nivel de servidor no se verán afectadas.
>
>

#### <a name="rename-data-warehouse-during-migration-optional"></a>Cambio del nombre del almacenamiento de datos durante la migración (opcional)
Dos bases de datos que se encuentren en el mismo servidor lógico no pueden tener el mismo nombre. SQL Data Warehouse ahora permite cambiar el nombre de un almacenamiento de datos.

Para este ejemplo, imagine que su almacenamiento de datos del almacenamiento estándar actualmente se llama "MiAD".

1. Cambie el nombre "MiAD" con el comando ALTER DATABASE. (En este ejemplo, se cambia por "MyDW_BeforeMigration").  Este comando detiene todas las transacciones existentes y debe realizarse dentro de la base de datos maestra para que se lleve a cabo correctamente.
   ```
   ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
   ```
2. [Detenga][Pause] "MyDW_BeforeMigration". Esto realiza una copia de seguridad automática.
3. Realice una [restauración][Restore] a partir de la instantánea más reciente de una nueva base de datos con el nombre que solía tener (por ejemplo: "MiAD").
4. Elimine "MyDW_BeforeMigration". **Si no puede realizar este paso, se le cobrará por los dos almacenamientos de datos.**


## <a name="next-steps"></a>Pasos siguientes
Con la migración a Premium Storage, también se aumenta la cantidad de archivos de blob de base de datos en la arquitectura subyacente del almacenamiento de datos. Para maximizar las ventajas en el rendimiento que implica este cambio, recompile los índices de almacén de columnas en clúster con el script siguiente. El script funciona forzando algunos de los datos existentes a los blobs adicionales. Si no realiza ninguna acción, los datos se redistribuirán naturalmente con el tiempo a medida que carga más datos en las tablas.

**Requisitos previos:**

- El almacenamiento de datos se debe ejecutar con 1000 unidades de almacenamiento de datos o más (vea el [escalado de la potencia de proceso][scale compute power]).
- El usuario que ejecuta el script debe tener el [rol mediumrc][mediumrc role] o superior. Para agregar un usuario a este rol, ejecute lo siguiente: ````EXEC sp_addrolemember 'xlargerc', 'MyUser'````

````sql
-------------------------------------------------------------------------------
-- Step 1: Create table to control index rebuild
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------
create table sql_statements
WITH (distribution = round_robin)
as select
    'alter index all on ' + s.name + '.' + t.NAME + ' rebuild;' as statement,
    row_number() over (order by s.name, t.name) as sequence
from
    sys.schemas s
    inner join sys.tables t
        on s.schema_id = t.schema_id
where
    is_external = 0
;
go

--------------------------------------------------------------------------------
-- Step 2: Execute index rebuilds. If script fails, the below can be re-run to restart where last left off.
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------

declare @nbr_statements int = (select count(*) from sql_statements)
declare @i int = 1
while(@i <= @nbr_statements)
begin
      declare @statement nvarchar(1000)= (select statement from sql_statements where sequence = @i)
      print cast(getdate() as nvarchar(1000)) + ' Executing... ' + @statement
      exec (@statement)
      delete from sql_statements where sequence = @i
      set @i += 1
end;
go
-------------------------------------------------------------------------------
-- Step 3: Clean up table created in Step 1
--------------------------------------------------------------------------------
drop table sql_statements;
go
````

Si tiene problemas con el almacenamiento de datos, [cree una incidencia de soporte técnico][create a support ticket] e indique que la posible causa es la "migración a Premium Storage".

<!--Image references-->

<!--Article references-->
[automatic migration schedule]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[create a support ticket]: sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: best-practices-availability-paired-regions.md
[main documentation site]: services/sql-data-warehouse.md
[Pause]: sql-data-warehouse-manage-compute-portal.md#pause-compute
[Restore]: sql-data-warehouse-restore-database-portal.md
[steps to rename during migration]: #optional-steps-to-rename-during-migration
[scale compute power]: sql-data-warehouse-manage-compute-portal.md#scale-compute-power
[mediumrc role]: sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->


<!--Other Web references-->
[Premium Storage for greater performance predictability]: https://azure.microsoft.com/en-us/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Azure Portal]: https://portal.azure.com
