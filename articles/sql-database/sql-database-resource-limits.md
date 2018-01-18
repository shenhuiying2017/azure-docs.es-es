---
title: "Límites de recursos de Azure SQL Database | Microsoft Docs"
description: "En esta página se describen algunos límites de recursos comunes para Azure SQL Database."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 884e519f-23bb-4b73-a718-00658629646a
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 01/12/2018
ms.author: carlrab
ms.openlocfilehash: 13641b190c3c157f5b302314f88a42a160a1f2e0
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/13/2018
---
# <a name="azure-sql-database-resource-limits"></a>Límites de recursos de SQL Database

## <a name="single-database-storage-sizes-and-performance-levels"></a>Base de datos única: tamaños de almacenamiento y niveles de rendimiento

Para las bases de datos únicas, las siguientes tablas muestran los recursos disponibles para una base de datos única en cada nivel de servicio y nivel de rendimiento. Puede establecer el nivel de servicio, el nivel de rendimiento y la cantidad de almacenamiento para una base de datos única mediante [Azure Portal](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](sql-database-single-database-resources.md#manage-single-database-resources-using-transact-sql), [PowerShell](sql-database-single-database-resources.md#manage-single-database-resources-using-powershell), la [CLI de Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-cli) o la [API de REST](sql-database-single-database-resources.md#manage-single-database-resources-using-the-rest-api).

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

## <a name="single-database-change-storage-size"></a>Base de datos única: cambio de tamaño de almacenamiento

- El precio de la DTU para una base de datos única incluye una cierta cantidad de almacenamiento sin costo adicional. El almacenamiento adicional que supere la cantidad incluida se puede aprovisionar por un costo extra hasta el límite de tamaño máximo en incrementos de 250 GB hasta 1 TB, y luego en incrementos de 256 GB superando 1 TB. Para cantidades de almacenamiento incluido y límites de tamaño máximo, consulte [Base de datos única: tamaños de almacenamiento y niveles de rendimiento](#single-database-storage-sizes-and-performance-levels).
- Se puede aprovisionar almacenamiento adicional para una base de datos única mediante el aumento de su tamaño máximo con [Azure Portal](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), la [CLI de Azure](/cli/azure/sql/db#az_sql_db_update) o la [API de REST](/rest/api/sql/databases/update).
- El precio del almacenamiento adicional para una base de datos única es la cantidad de almacenamiento adicional multiplicado por el precio de la unidad de almacenamiento adicional del nivel de servicio. Para más información sobre el precio del almacenamiento adicional, consulte los [precios de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-dtus"></a>Base de datos única: cambio de DTU

Después de elegir inicialmente un nivel de servicio, un nivel de rendimiento y una cantidad de almacenamiento, puede escalar o reducir una base de datos verticalmente de manera dinámica en función de la experiencia real mediante [Azure Portal](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), la [CLI de Azure](/cli/azure/sql/db#az_sql_db_update) o la [API de REST](/rest/api/sql/databases/update). 

El vídeo siguiente muestra cómo cambia de manera dinámica el nivel de rendimiento para aumentar las DTU disponibles para una base de datos única.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

Al cambiar el nivel de servicio o de rendimiento de una base de datos, se crea una réplica de la base de datos original en el nuevo nivel de rendimiento y, a continuación, se cambian las conexiones a la réplica. Durante este proceso, no se pierde ningún dato; sin embargo, durante el breve momento en que se cambie a la réplica, las conexiones a la base de datos estarán deshabilitadas, por tanto, es posible que se reviertan algunas transacciones en curso. La longitud de tiempo del cambio varía, pero suele estar por debajo de 4 segundos y es inferior a 30 segundos en el 99 % de las veces. Si el número de transacciones en curso es elevado en el momento en que las conexiones están deshabilitadas, esta longitud de tiempo puede ser mayor. 

La duración de todo el proceso de escalado vertical depende del nivel de servicio y del tamaño de la base de datos antes y después del cambio. Por ejemplo, el cambio de una base de datos de 250 GB dentro de un nivel de servicio Estándar, o bien desde o hacia este, se completará en seis horas. Para una base de datos del mismo tamaño que cambie los niveles de rendimiento del nivel de servicio Premium, el escalado vertical se completará en unas tres horas.

> [!TIP]
> Para supervisar las operaciones en curso, consulte los temas sobre [administración de operaciones mediante la API de REST de SQL](/rest/api/sql/Operations/List), [administración de operaciones mediante la CLI](/cli/azure/sql/db/op) y [supervisión de operaciones mediante T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database), y los dos siguientes comandos de PowerShell: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) y [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Si va a actualizar a un nivel de servicio o rendimiento más elevado, el tamaño máximo de la base de datos no aumenta a no ser que especifique un tamaño mayor (maxsize).
* Para cambiar una base de datos a una versión anterior, su espacio usado no debe alcanzar el tamaño máximo permitido del nivel de servicio de destino y del nivel de rendimiento. 
* Al pasar de la versión **Premium** o **Premium RS** al nivel **Estándar**, se aplica un costo de almacenamiento adicional si (1) el tamaño máximo de la base de datos es compatible con el nivel de rendimiento de destino y (2) el tamaño máximo supera la cantidad de almacenamiento incluido del nivel de rendimiento de destino. Por ejemplo, si una base de datos P1 con un tamaño máximo de 500 GB se reduce a S3, se aplica un costo de almacenamiento adicional porque S3 admite un tamaño máximo de 500 GB y su cantidad de almacenamiento incluido es solo de 250 GB. Por lo tanto, la cantidad de almacenamiento adicional es 500 GB – 250 GB = 250 GB. Para conocer el precio del almacenamiento adicional, consulte los [precios de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Si la cantidad de espacio real utilizada es menor que la cantidad de almacenamiento incluido, este costo adicional puede evitarse si se reduce el tamaño máximo de la base de datos a la cantidad incluida. 
* Al actualizar una base de datos con la [replicación geográfica](sql-database-geo-replication-portal.md) habilitada, actualice sus bases de datos secundarias al nivel de rendimiento deseado antes de actualizar la principal (regla general). Al actualizar a una diferente, antes hay que actualizar la base de datos secundaria.
* Al cambiar a una versión anterior de una base de datos con la [replicación geográfica](sql-database-geo-replication-portal.md) habilitada, cambie a una versión anterior sus bases de datos principales al nivel de rendimiento deseado antes de cambiar a una versión anterior la secundaria (regla general). Al cambiar a una versión anterior a una edición diferente, antes hay que cambiar la principal a una versión anterior.
* Las ofertas del servicio de restauración son diferentes para los distintos niveles de servicio. Si va a cambiar al nivel **Básico**, hay un período de retención de copia de seguridad más bajo; consulte [Copias de seguridad de Azure SQL Database](sql-database-automated-backups.md).
* Las nuevas propiedades de la base de datos no se aplican hasta que se completan los cambios.

## <a name="single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Base de datos única: limitaciones de P11 y P15 cuando el máximo tamaño es mayor de 1 TB

Se admite un tamaño máximo mayor de 1 TB para la base de datos P11 y P15 en las siguientes regiones: este de EE. UU. 2, oeste de EE. UU., Virginia Gob. EE. UU., Europa Occidental, centro de Alemania, Asia Suroriental, Japón Oriental, este de Australia, centro de Canadá y este de Canadá. Las siguientes consideraciones y limitaciones se aplican a las bases de datos P11 y P15 con un tamaño máximo mayor de 1 TB:

- Si elige un tamaño máximo mayor de 1 TB al crear una base de datos (con un valor de 4 TB o 4096 GB), el comando de creación produce un error si la base de datos se aprovisiona en una región no admitida.
- Para las bases de datos P11 y P15 existentes ubicadas en alguna de las regiones admitidas, puede aumentar el almacenamiento máximo hasta superar 1 TB en incrementos de 256 MB hasta 4 TB. Para ver si se admite un tamaño mayor en su región, utilice la función [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) o inspeccione el tamaño de la base de datos en Azure Portal. La actualización de una base de datos P11 o P15 existente solo puede realizarse mediante un inicio de sesión de entidad de seguridad de nivel de servidor o por los miembros del rol de base de datos dbmanager. 
- Si se ha ejecutado una operación de actualización en una región admitida, la configuración se actualiza inmediatamente. La base de datos permanecerá en línea durante el proceso de actualización. Sin embargo, no puede utilizar la cantidad total de almacenamiento a partir de 1 TB de almacenamiento hasta que se hayan actualizado los archivos de base de datos reales con el nuevo tamaño máximo. El período de tiempo necesario depende del tamaño de la base de datos que se va a actualizar. 
- Al crear o actualizar una base de datos P11 o P15, solo puede optar entre un tamaño máximo de 1 TB y 4 TB en incrementos de 256 GB. Cuando crea una base de datos P11 o P15, se selecciona previamente la opción de almacenamiento predeterminada de 1 TB. En el caso de bases de datos ubicadas en alguna de las regiones admitidas, puede aumentar el almacenamiento máximo hasta un máximo de 4 TB en bases de datos únicas nuevas o existentes. Para todas las demás regiones, el tamaño máximo no se puede aumentar por encima de 1 TB. El precio no cambia cuando se selecciona la opción de 4 TB de almacenamiento incluido.
- Si el tamaño máximo de una base de datos se establece en más de 1 TB, no podrá modificarse a 1 TB incluso si el almacenamiento real utilizado es inferior a 1 TB. Por lo tanto, no puede cambiar a una versión anterior una P11 o P15 con un tamaño máximo mayor que 1 TB a un P11 de 1 TB o a un P15 de 1 TB o un nivel de rendimiento menor, por ejemplo, P1-P6). Esta restricción también se aplica a los escenarios de copia y restauración, lo que incluye la restauración a un momento dado, la restauración geográfica, la retención de copias de seguridad a largo plazo y la copia de base de datos. Cuando se configura una base de datos con un tamaño máximo de 1 TB, todas las operaciones de restauración de esta base de datos deben ejecutarse en una P11/P15 con un tamaño máximo mayor de 1 TB.
- En escenarios de replicación geográfica activa:
   - Configuración de una relación de replicación geográfica: si la base de datos principal es P11 o P15, la secundaria (una o varias) también debe P11 o P15; aquellas con un nivel de rendimiento inferior se rechazarán como secundarias, puesto que no tienen capacidad para admitir más de 1 TB.
   - Actualización de la base de datos principal en una relación de replicación geográfica: al cambiar el tamaño máximo a 1 TB en una base de datos principal, se desencadenará el mismo cambio en la base de datos secundaria. Ambas actualizaciones deben realizarse correctamente para que el cambio en la principal surta efecto. Se aplican limitaciones por región para la opción de más de 1 TB. Si la base de datos secundaria está en una región que no admite más de 1 TB, no se actualizará la principal.
- No se admite el uso del servicio Import/Export para cargar bases de datos P11/P15 con más de 1 TB. Use SqlPackage.exe para [importar](sql-database-import.md) y [exportar](sql-database-export.md) datos.

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>Grupo de bases de datos elásticas: tamaños de almacenamiento y niveles de rendimiento

Para los grupos elásticos de SQL Database, las siguientes tablas muestran los recursos disponibles en cada nivel de servicio y nivel de rendimiento. Puede establecer el nivel de servicio, el nivel de rendimiento y la cantidad de almacenamiento mediante [Azure Portal](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-powershell), la [CLI de Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-cli) o la [API de REST](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-rest-api).

> [!NOTE]
> Los límites de recursos de las bases de datos individuales de los grupos elásticos suelen ser los mismos que para las bases de datos únicas fuera de los grupos basados en DTU y el nivel de servicio. Por ejemplo, el número máximo de trabajadores simultáneos de una base de datos S2 es de 120 trabajadores. Por lo tanto, el número máximo de trabajadores simultáneos de una base de datos de un grupo estándar también es de 120 trabajadores si el número máximo de DTU por base de datos del grupo es de 50 DTU (que es equivalente a S2).
>

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Si se usan todas las unidades DTU de un grupo elástico, cada una de las bases de datos del grupo recibe una misma cantidad de recursos para procesar consultas. El servicio SQL Database proporciona ecuanimidad de uso compartido de recursos entre bases de datos garantizando los mismos segmentos de tiempo de proceso. La ecuanimidad de uso compartido de recursos del grupo elástico es adicional a cualquier cantidad de recursos garantizados de otro modo a cada base de datos cuando el número mínimo de DTU por base de datos se establece en un valor distinto de cero.

### <a name="database-properties-for-pooled-databases"></a>Propiedades de base de datos para bases de datos agrupadas

En la tabla siguiente se describen las propiedades de las bases de datos agrupadas.

| Propiedad | DESCRIPCIÓN |
|:--- |:--- |
| Cantidad máxima de eDTU por base de datos |Cantidad máxima de eDTU que puede utilizar cualquier base de datos del grupo, si está disponible según el uso que hacen otras bases de datos del grupo. La cantidad máxima de eDTU por base de datos no garantiza la disponibilidad de recursos. Se trata de una configuración global que se aplica a todas las bases de datos del grupo. Establezca una cantidad máxima de eDTU por base de datos lo suficientemente alta como para gestionar los picos de uso de la base de datos. Se admite cierto grado de exceso de asignación de recursos, ya que el grupo suele basarse en patrones de uso en frío y caliente de las bases de datos, cuando en realidad los picos de uso no tienen lugar en todas las bases de datos a la vez. Por ejemplo, supongamos que el pico de uso de cada base de datos es 20 eDTU y solo tiene lugar en el 20 % de las 100 bases de datos del grupo a la vez. Si la cantidad máxima de eDTU por base de datos se establece en 20, puede asignar al grupo una cantidad 5 veces mayor y establecer las eDTU por grupo en 400. |
| Cantidad mínima de eDTU por base de datos |Cantidad mínima de eDTU que se garantiza en cualquier base de datos del grupo. Se trata de una configuración global que se aplica a todas las bases de datos del grupo. La cantidad mínima de eDTU por base de datos se puede establecer en 0, y también se trata del valor predeterminado. Esta propiedad se establece en cualquier valor entre 0 y el uso medio de eDTU por base de datos. El resultado de multiplicar la cantidad de bases de datos del grupo y la cantidad mínima de eDTU por base de datos no puede superar el número de eDTU por grupo. Por ejemplo, si un grupo tiene 20 bases de datos y la cantidad mínima de eDTU por base de datos establecida es 10, el número de eDTU por grupo debe ser de, al menos, 200. |
| Almacenamiento máximo por base de datos |Almacenamiento máximo de una base de datos de un grupo. Las bases de datos agrupadas comparten almacenamiento de grupo, por lo que el almacenamiento de base de datos se limita a la menor cantidad de almacenamiento de grupos restante y al almacenamiento máximo por base de datos. El almacenamiento máximo por base de datos hace referencia al tamaño máximo de los archivos de datos y no incluye el espacio utilizado por los archivos de registro. |
|||
 
## <a name="elastic-pool-change-storage-size"></a>Grupo de bases de datos elásticas: cambiar el tamaño de almacenamiento

- El precio de la eDTU de un grupo elástico incluye una cierta cantidad de almacenamiento sin ningún costo adicional. El almacenamiento adicional que supere la cantidad incluida se puede aprovisionar por un costo extra hasta el límite de tamaño máximo en incrementos de 250 GB hasta 1 TB, y luego en incrementos de 256 GB superando 1 TB. Para cantidades de almacenamiento incluido y límites de tamaño máximo, consulte [Grupos elásticos: tamaños de almacenamiento y niveles de rendimiento](#elastic-pool-storage-sizes-and-performance-levels).
- Se puede aprovisionar el almacenamiento adicional para un grupo elástico si se aumenta su tamaño máximo mediante [Azure Portal](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), la [CLI de Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) o la [API de REST](/rest/api/sql/elasticpools/update).
- El precio del almacenamiento adicional para un grupo de bases de datos elásticas es la cantidad de almacenamiento adicional multiplicada por el precio de la unidad de almacenamiento adicional del nivel de servicio. Para más información sobre el precio del almacenamiento adicional, consulte los [precios de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="elastic-pool-change-edtus"></a>Grupo elástico: cambio de eDTU

Puede aumentar o disminuir los recursos disponibles para un grupo elástico en función de los recursos que necesita mediante [Azure Portal](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), la [CLI de Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) o la [ API de REST](/rest/api/sql/elasticpools/update).

- Al cambiar la escala de las eDTU de grupo, se interrumpe brevemente las conexiones de base de datos. Este es el mismo comportamiento que se produce cuando se cambia la escala de las DTU para una base de datos única (no en un grupo). Para más información sobre la duración y el impacto de las conexiones interrumpidas para una base de datos durante las operaciones de cambio de escala, consulte [Cambio de escala de DTU para una base de datos única](#single-database-change-storage-size). 
- La duración para cambiar la escala de las eDTU de grupo puede depender de la cantidad total de espacio de almacenamiento utilizado por todas las bases de datos del grupo. En general, la latencia del cambio de escalado calcula el promedio de 90 minutos o menos por cada 100 GB. Por ejemplo, si el espacio total que usan todas las bases de datos del grupo es de 200 GB, la latencia esperada para cambiar la escala del grupo es de 3 horas o menos. En algunos casos en el nivel Estándar o Básico, la latencia del cambio de escala puede ser menos de cinco minutos sin tener en cuenta la cantidad de espacio utilizado.
- En general, la duración del cambio de las eDTU mínimas por base de datos o de las eDTU máximas por base de datos suele ser de cinco minutos o menos.
- Cuando se reducen las eDTU del grupo, el grupo usado debe ser menor que el tamaño máximo permitido del nivel de servicio de destino y de las eDTU de grupo.
- Cuando se cambia la escala de las eDTU de grupo, se aplica un costo de almacenamiento adicional si (1) el tamaño máximo de almacenamiento del grupo de es compatible con el grupo de destino, y (2) el tamaño máximo de almacenamiento supera la cantidad de almacenamiento incluido del grupo de destino. Por ejemplo, si un grupo Estándar de 100 eDTU con un tamaño máximo de 100 GB se reduce a un grupo Estándar de 50 eDTU, se aplica un costo de almacenamiento adicional porque el grupo de destino admite un tamaño máximo de 100 GB y su cantidad de almacenamiento incluido es solo de 50 GB. Por lo tanto, la cantidad de almacenamiento adicional es 100 GB – 50 GB = 50 GB. Para conocer el precio del almacenamiento adicional, consulte los [precios de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Si la cantidad de espacio real utilizada es menor que la cantidad de almacenamiento incluido, este costo adicional puede evitarse si se reduce el tamaño máximo de la base de datos a la cantidad incluida. 

## <a name="what-happens-when-database-and-elastic-pool-resource-limits-are-reached"></a>¿Qué ocurre cuando se alcanzan los límites de recursos de bases de datos y grupos elásticos?

### <a name="compute-dtus-and-edtus"></a>Proceso (DTU y eDTU)

Cuando el uso del proceso de base de datos (medido por DTU y eDTU) es elevado, la latencia de las consultas aumenta e incluso puede agotar el tiempo de espera. En estas condiciones, el servicio puede poner en cola a las consultas y se proporcionan recursos para la ejecución a medida que el recurso esté disponible.
Al encontrar un uso de proceso elevado, las opciones de mitigación incluyen:

- Aumentar el nivel de rendimiento de la base de datos o del grupo elástico para proporcionar a la base de datos más DTU o eDTU. Consulte [Base de datos única: cambio de DTU](#single-database-change-dtus) y [Grupo elástico: cambio de eDTU](#elastic-pool-change-edtus).
- Optimizar las consultas para reducir el uso de recursos de cada consulta. Para más información, consulte [Optimización y sugerencias de consultas](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Storage

Cuando el espacio de la base de datos alcanza el límite de tamaño máximo, las inserciones y actualizaciones de la base de datos que aumentan el tamaño de los datos producen un error y los clientes reciben un [mensaje de error](sql-database-develop-error-messages.md). Las selecciones y eliminaciones de la base de datos continúan.

Al encontrar un uso elevado de espacio, las opciones de mitigación incluyen:

- Aumente el tamaño máximo de la base de datos o del grupo elástico o cambie el nivel de rendimiento para obtener más almacenamiento incluido. Consulte [Límites de recursos de SQL Database](sql-database-resource-limits.md).
- Si la base de datos está en un grupo elástico, se puede mover fuera del grupo para que su espacio de almacenamiento no se comparta con otras bases de datos.

### <a name="sessions-and-workers-requests"></a>Sesiones y trabajos (solicitudes) 

El número máximo de sesiones y de trabajos simultáneos se determina por el nivel de rendimiento y el nivel de servicio (DTU y eDTU).  Las nuevas solicitudes se rechazan cuando se alcanzan los límites de sesión o de trabajo y los clientes reciben un mensaje de error. Aunque el número de conexiones disponibles se pueden controlar por la aplicación, el número de trabajos simultáneos suele ser más difícil de calcular y controlar. Esto es especialmente cierto durante los períodos de carga máximos cuando se alcanzan los límites de recursos de base de datos y los trabajos se apilan debido a consultas de ejecución más prolongada. 

Al encontrar un uso elevado de sesión o de trabajo, las opciones de mitigación incluyen:
- Aumentar el nivel de servicio o el nivel de rendimiento de la base de datos o del grupo elástico. Consulte [Base de datos única: cambio de tamaño de almacenamiento](#single-database-change-storage-size), [Base de datos única: cambio de DTU](#single-database-change-dtus), [Grupo elástico: cambio de tamaño de almacenamiento](#elastic-pool-change-storage-size) y [Grupo elástico: cambio de eDTU](#elastic-pool-change-edtus).
- Optimizar las consultas para reducir el uso de recursos de cada consulta si la causa del mayor uso de trabajo es debida a la contención de los recursos de proceso. Para más información, consulte [Optimización y sugerencias de consultas](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>pasos siguientes

- Para más información sobre los niveles de servicio, consulte [Niveles de servicio](sql-database-service-tiers.md).
- Para más información sobre las bases de datos únicas, consulte [Recursos de bases de datos únicas](sql-database-resource-limits.md).
- Para más información sobre los grupos elásticos, consulte [Grupos elásticos](sql-database-elastic-pool.md).
- Para más información sobre los límites generales de Azure, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md).
- Para más información sobre las DTU y eDTU, consulte [DTU y eDTU](sql-database-what-is-a-dtu.md).
- Para obtener información acerca de los límites de tamaño de tempdb, consulte https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database.
