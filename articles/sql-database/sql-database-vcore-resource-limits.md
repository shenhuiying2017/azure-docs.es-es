---
title: Límites de recursos basados en núcleos virtuales de Azure SQL Database | Microsoft Docs
description: En esta página se describen algunos límites de recursos comunes basados en núcleos virtuales para Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/04/2018
ms.author: carlrab
ms.openlocfilehash: 204702eee1cf502ac873e0c1f5e3fd257ecce33c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-preview"></a>Límites del modelo de compra basado en núcleos virtuales de Azure SQL Database (versión preliminar)

> [!IMPORTANT]
> Para información sobre los límites del modelo de compra basado en DTU, consulte [SQL Database DTU-based resource limits](sql-database-dtu-resource-limits.md) (Límites de recursos basados en DTU de SQL Database).

## <a name="single-database-storage-sizes-and-performance-levels"></a>Base de datos única: tamaños de almacenamiento y niveles de rendimiento

Para las bases de datos únicas, las siguientes tablas muestran los recursos disponibles para una base de datos única en cada nivel de servicio y nivel de rendimiento. Puede establecer el nivel de servicio, el nivel de rendimiento y la cantidad de almacenamiento para una base de datos única mediante [Azure Portal](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](sql-database-single-database-resources.md#manage-single-database-resources-using-transact-sql), [PowerShell](sql-database-single-database-resources.md#manage-single-database-resources-using-powershell), la [CLI de Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-cli) o la [API de REST](sql-database-single-database-resources.md#manage-single-database-resources-using-the-rest-api).

### <a name="general-purpose-service-tier"></a>Nivel de servicio de uso general
|Nivel de rendimiento|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|
|:--- | --: |--: |--: |--: |--: |
|Generación de H/W|4|4|4|4|4|
|Núcleos virtuales|1|2|4|8|16|
|Memoria (GB)|7|14|28|56|112|
|Compatible con almacén de columnas|Sí|Sí|Sí|Sí|Sí|
|Almacenamiento OLTP en memoria (GB)|N/D|N/D|N/D|N/D|N/D|
|Tipo de almacenamiento|Premium Storage (remoto)|Premium Storage (remoto)|Premium Storage (remoto)|Premium Storage (remoto)|Premium Storage (remoto)|
|Latencia de E/S (aproximada)|5-7 ms (escritura)<br>5-10 ms (lectura)|5-7 ms (escritura)<br>5-10 ms (lectura)|5-7 ms (escritura)<br>5-10 ms (lectura)|5-7 ms (escritura)<br>5-10 ms (lectura)|5-7 ms (escritura)<br>5-10 ms (lectura)|
|Tamaño máximo de datos (GB)|1024|1024|1536|3072|4096|
|Tamaño máximo de registro|307|307|461|922|1229|
|Tamaño de TempDB (DB)|32|64|128|256|384|
|IOPS de destino|320|640|1280|2560|5120|
|Latencia de E/S (aproximada)|5-7 ms (escritura)
|Cantidad máxima de trabajos (solicitudes) simultáneos|200|400|800|1600|3200|
|Sesiones máximas permitidas|30000|30000|30000|30000|30000|
|Número de réplicas|1|1|1|1|1|
|AZ múltiple|N/D|N/D|N/D|N/D|N/D|
|Escalado horizontal de lectura|N/D|N/D|N/D|N/D|N/D|
|Almacenamiento de copia de seguridad incluido|1X el tamaño de base de datos|1X el tamaño de base de datos|1X el tamaño de base de datos|1X el tamaño de base de datos|1X el tamaño de base de datos|
|||

### <a name="business-critical-service-tier"></a>Nivel de servicio Crítico para la empresa
|Nivel de rendimiento|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|
|:--- | --: |--: |--: |--: |--: |
|Generación de H/W|4|4|4|4|4|
|Núcleos virtuales|1|2|4|8|16|
|Memoria (GB)|7|14|28|56|112|
|Compatible con almacén de columnas|Sí|Sí|Sí|Sí|Sí|
|Almacenamiento OLTP en memoria (GB)|1|2|4|8|20|
|Tipo de almacenamiento|SSD asociada|SSD asociada|SSD asociada|SSD asociada|SSD asociada|
|Tamaño máximo de datos (GB)|1024|1024|1024|1024|1024|
|Tamaño máximo de registro|307|307|307|307|307|
|Tamaño de TempDB (DB)|32|64|128|256|384|
|IOPS de destino|5000|10000|20000|40000|80000|
|Latencia de E/S (aproximada)|1-2 ms (escritura)<br>1-2 ms (lectura)|1-2 ms (escritura)<br>1-2 ms (lectura)|1-2 ms (escritura)<br>1-2 ms (lectura)|1-2 ms (escritura)<br>1-2 ms (lectura)|1-2 ms (escritura)<br>1-2 ms (lectura)|
|Cantidad máxima de trabajos (solicitudes) simultáneos|200|400|800|1600|3200|
|Sesiones máximas permitidas|30000|30000|30000|30000|30000|
|Número de réplicas|3|3|3|3|3|
|AZ múltiple|Sí|Sí|Sí|Sí|Sí|
|Escalado horizontal de lectura|Sí|Sí|Sí|Sí|Sí|
|Almacenamiento de copia de seguridad incluido|1X el tamaño de base de datos|1X el tamaño de base de datos|1X el tamaño de base de datos|1X el tamaño de base de datos|1X el tamaño de base de datos|
|||

## <a name="single-database-change-storage-size"></a>Base de datos única: cambio del tamaño de almacenamiento

- Se puede aprovisionar el almacenamiento hasta el límite máximo de tamaño con incrementos de 1 GB. El almacenamiento de datos mínimo configurable es 5 GB 
- Se puede aprovisionar almacenamiento para una base de datos única mediante el aumento o disminución de su tamaño máximo con [Azure Portal](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), la [CLI de Azure](/cli/azure/sql/db#az_sql_db_update) o la [API REST](/rest/api/sql/databases/update).
- SQL Database asigna automáticamente 30 % de almacenamiento adicional para los archivos de registro y 32 GB por núcleo virtual para TempDB, sin exceder los 384 GB. TempDB se encuentra en una SSD asociada en todos los niveles de servicio.
- El precio de almacenamiento para una única base de datos es la suma de los importes de almacenamiento de datos y almacenamiento de registros multiplicado por el precio de la unidad de almacenamiento del nivel de servicio. El costo de TempDB se incluye en el precio del núcleo virtual. Para más información sobre el precio del almacenamiento adicional, consulte los [precios de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-vcores"></a>Base de datos única: cambio de los núcleos virtuales

Después de elegir inicialmente el número de núcleos virtuales, puede escalar o reducir una base de datos verticalmente de manera dinámica en función de la experiencia real mediante [Azure Portal](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), la [CLI de Azure](/cli/azure/sql/db#az_sql_db_update) o la [API REST](/rest/api/sql/databases/update). 

Al cambiar el nivel de servicio o de rendimiento de una base de datos, se crea una réplica de la base de datos original en el nuevo nivel de rendimiento y, a continuación, se cambian las conexiones a la réplica. Durante este proceso, no se pierde ningún dato; sin embargo, durante el breve momento en que se cambie a la réplica, las conexiones a la base de datos estarán deshabilitadas, por tanto, es posible que se reviertan algunas transacciones en curso. La longitud de tiempo del cambio varía, pero suele estar por debajo de 4 segundos y es inferior a 30 segundos en el 99 % de las veces. Si el número de transacciones en curso es elevado en el momento en que las conexiones están deshabilitadas, esta longitud de tiempo puede ser mayor. 

La duración de todo el proceso de escalado vertical depende del nivel de servicio y del tamaño de la base de datos antes y después del cambio. Por ejemplo, el cambio de una base de datos de 250 GB dentro de un nivel de servicio De uso general, o bien desde o hacia este, se completará en seis horas. En una base de datos del mismo tamaño que cambie los niveles de rendimiento en el nivel de servicio Crítico para la empresa, el escalado vertical se completará en unas tres horas.

> [!TIP]
> Para supervisar las operaciones en curso, consulte los temas sobre [administración de operaciones mediante la API de REST de SQL](/rest/api/sql/Operations/List), [administración de operaciones mediante la CLI](/cli/azure/sql/db/op) y [supervisión de operaciones mediante T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database), y los dos siguientes comandos de PowerShell: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) y [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Si va a actualizar a un nivel de servicio o rendimiento más elevado, el tamaño máximo de la base de datos no aumenta a no ser que especifique un tamaño mayor (maxsize).
* Para cambiar una base de datos a una versión anterior, su espacio usado no debe alcanzar el tamaño máximo permitido del nivel de servicio de destino y del nivel de rendimiento. 
* Al actualizar una base de datos con la [replicación geográfica](sql-database-geo-replication-portal.md) habilitada, actualice sus bases de datos secundarias al nivel de rendimiento deseado antes de actualizar la base de datos principal (instrucciones generales para mejorar el rendimiento). Al actualizar a una diferente, antes hay que actualizar la base de datos secundaria.
* Al degradar una base de datos con la [replicación geográfica](sql-database-geo-replication-portal.md) habilitada, degrade sus bases de datos principales al nivel de rendimiento deseado antes de degradar la secundaria (regla general para un mejor rendimiento). Al cambiar a una versión anterior a una edición diferente, antes hay que cambiar la principal a una versión anterior.
* Las nuevas propiedades de la base de datos no se aplican hasta que se completan los cambios.

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>Grupo de bases de datos elásticas: tamaños de almacenamiento y niveles de rendimiento

Para los grupos elásticos de SQL Database, las siguientes tablas muestran los recursos disponibles en cada nivel de servicio y nivel de rendimiento. Puede establecer el nivel de servicio, el nivel de rendimiento y la cantidad de almacenamiento mediante [Azure Portal](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-powershell), la [CLI de Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-cli) o la [API de REST](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-rest-api).

> [!NOTE]
> Los límites de recursos de las bases de datos individuales de los grupos elásticos suelen ser los mismos que los de las bases de datos únicas fuera de los grupos que tienen el mismo nivel de rendimiento. Por ejemplo, el número máximo de trabajos simultáneos en una base de datos GP_Gen4_1 es 200 trabajos. Por lo tanto, el número máximo de trabajos simultáneos en una base de datos de un grupo GP_Gen4_1 también es 200 trabajos. Tenga en cuenta que el número total de trabajos simultáneos en el grupo GP_Gen4_1 es 210.

### <a name="general-purpose-service-tier"></a>Nivel de servicio de uso general
|Nivel de rendimiento|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|
|:--- | --: |--: |--: |--: |--: |
|Generación de H/W|4|4|4|4|4|
|Núcleos virtuales|1|2|4|8|16|
|Memoria (GB)|7|14|28|56|112|
|Compatible con almacén de columnas|Sí|Sí|Sí|Sí|Sí|
|Almacenamiento OLTP en memoria (GB)|N/D|N/D|N/D|N/D|N/D|
|Tipo de almacenamiento|Premium Storage (remoto)|Premium Storage (remoto)|Premium Storage (remoto)|Premium Storage (remoto)|Premium Storage (remoto)|
|Tamaño máximo de datos (GB)|512|756|1536|2048|3584|
|Tamaño máximo de registro|154|227|461|614|1075|
|Tamaño de TempDB (DB)|32|64|128|256|384|
|IOPS de destino|320|640|1280|2560|5120|
|Latencia de E/S (aproximada)|5-7 ms (escritura)<br>5-10 ms (lectura)|5-7 ms (escritura)<br>5-10 ms (lectura)|5-7 ms (escritura)<br>5-10 ms (lectura)|5-7 ms (escritura)<br>5-10 ms (lectura)|5-7 ms (escritura)<br>5-10 ms (lectura)|
|Cantidad máxima de trabajos (solicitudes) simultáneos|210|420|840|1680|3360|
|Sesiones máximas permitidas|30000|30000|30000|30000|30000|
|Densidad máxima del grupo|100|200|500|500|500|
|Número mín./máx. de clics para detener del grupo elástico|0, 0.25, 0.5, 1|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1, 2, 4|0, 0.25, 0.5, 1, 2, 4, 8|0, 0.25, 0.5, 1, 2, 4, 8, 16|
|Número de réplicas|1|1|1|1|1|
|AZ múltiple|N/D|N/D|N/D|N/D|N/D|
|Escalado horizontal de lectura|N/D|N/D|N/D|N/D|N/D|
|Almacenamiento de copia de seguridad incluido|1X el tamaño de base de datos|1X el tamaño de base de datos|1X el tamaño de base de datos|1X el tamaño de base de datos|1X el tamaño de base de datos|
|||

### <a name="business-critical-service-tier"></a>Nivel de servicio Crítico para la empresa
|Nivel de rendimiento|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|
|:--- | --: |--: |--: |--: |--: |
|Generación de H/W|4|4|4|4|4|
|Núcleos virtuales|1|2|4|8|16|
|Memoria (GB)|7|14|28|56|112|
|Compatible con almacén de columnas|Sí|Sí|Sí|Sí|Sí|
|Almacenamiento OLTP en memoria (GB)|1|2|4|8|20|
|Tipo de almacenamiento|SSD asociada|SSD asociada|SSD asociada|SSD asociada|SSD asociada|
|Tamaño máximo de datos (GB)|1024|1024|1024|1024|1024|
|Tamaño máximo de registro|307|307|307|461|614|
|Tamaño de TempDB (DB)|32|64|128|256|384|
|IOPS de destino|320|640|1280|2560|5120|
|Latencia de E/S (aproximada)|1-2 ms (escritura)<br>1-2 ms (lectura)|1-2 ms (escritura)<br>1-2 ms (lectura)|1-2 ms (escritura)<br>1-2 ms (lectura)|1-2 ms (escritura)<br>1-2 ms (lectura)|1-2 ms (escritura)<br>1-2 ms (lectura)|
|Cantidad máxima de trabajos (solicitudes) simultáneos|210|420|840|1680|3360|
|Sesiones máximas permitidas|30000|30000|30000|30000|30000|
|Densidad máxima del grupo|N/D|50|100|100|100|
|Número mín./máx. de clics para detener del grupo elástico|0, 0.25, 0.5, 1|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1, 2, 4|0, 0.25, 0.5, 1, 2, 4, 8|0, 0.25, 0.5, 1, 2, 4, 8, 16|
|AZ múltiple|Sí|Sí|Sí|Sí|Sí|
|Escalado horizontal de lectura|Sí|Sí|Sí|Sí|Sí|
|Almacenamiento de copia de seguridad incluido|1X el tamaño de base de datos|1X el tamaño de base de datos|1X el tamaño de base de datos|1X el tamaño de base de datos|1X el tamaño de base de datos|
|||
Si todos los núcleos virtuales de un grupo elástico están ocupados, cada una de las bases de datos del grupo recibe la misma cantidad de recursos de proceso para procesar las consultas. El servicio SQL Database proporciona ecuanimidad de uso compartido de recursos entre bases de datos garantizando los mismos segmentos de tiempo de proceso. La ecuanimidad de uso compartido de recursos del grupo elástico es adicional a cualquier cantidad de recursos garantizados de otro modo a cada base de datos cuando el número mínimo de núcleos virtuales por base de datos se establece en un valor distinto de cero.

### <a name="database-properties-for-pooled-databases"></a>Propiedades de base de datos para bases de datos agrupadas

En la tabla siguiente se describen las propiedades de las bases de datos agrupadas.

| Propiedad | DESCRIPCIÓN |
|:--- |:--- |
| Núcleos virtuales máximos por base de datos |El número máximo de núcleos virtuales que puede usar cualquier base de datos del grupo, si está disponible según el uso que hacen otras bases de datos del grupo. El número de núcleos virtuales por base de datos no garantiza la disponibilidad de recursos para una base de datos. Se trata de una configuración global que se aplica a todas las bases de datos del grupo. Establezca un número de núcleos virtuales por base de datos lo suficientemente alto como para gestionar los picos de uso de la base de datos. Se admite cierto grado de exceso de asignación de recursos, ya que el grupo suele basarse en patrones de uso en frío y caliente de las bases de datos, cuando en realidad los picos de uso no tienen lugar en todas las bases de datos a la vez.|
| Número mínimo de núcleos virtuales por base de datos |El número mínimo de núcleos virtuales que se garantiza en cualquier base de datos del grupo. Se trata de una configuración global que se aplica a todas las bases de datos del grupo. El número mínimo de núcleos virtuales por base de datos se puede establecer en 0, y también se trata del valor predeterminado. Esta propiedad se establece en cualquier valor entre 0 y el uso medio de núcleos virtuales por base de datos. El resultado de multiplicar el número de bases de datos del grupo y el número mínimo de núcleos virtuales por base de datos no puede superar el número de núcleos virtuales por grupo.|
| Almacenamiento máximo por base de datos |El tamaño máximo de base de datos establecido por el usuario para una base de datos de un grupo. Las bases de datos agrupadas comparten el almacenamiento del grupo asignado, de modo que el tamaño que puede alcanzar una base de datos se limita a la menor cantidad de almacenamiento restante del grupo y el tamaño de la base de datos. El tamaño máximo de la base de datos hace referencia al tamaño máximo de los archivos de datos, y no incluye el espacio utilizado por los archivos de registro. |
|||
 
## <a name="elastic-pool-change-storage-size"></a>Grupo elástico: cambio del tamaño de almacenamiento

- Se puede aprovisionar el almacenamiento hasta el límite de tamaño máximo: 
 - Para Standard Storage, aumente o disminuya el tamaño en incrementos de 10 GB.
 - Para Premium Storage, aumente o disminuya el tamaño en incrementos de 250 GB.
- Se puede aprovisionar almacenamiento para un grupo elástico si se aumenta o disminuye su tamaño máximo mediante [Azure Portal](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), la [CLI de Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) o la [API REST](/rest/api/sql/elasticpools/update).
- El precio del almacenamiento para un grupo elástico es la cantidad de almacenamiento multiplicada por el precio de la unidad de almacenamiento del nivel de servicio. Para más información sobre el precio del almacenamiento adicional, consulte los [precios de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="elastic-pool-change-vcores"></a>Grupo elástico: cambio de los núcleos virtuales

Puede aumentar o disminuir el nivel de rendimiento para un grupo elástico en función de los recursos que necesita mediante [Azure Portal](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), la [CLI de Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) o la [ API REST](/rest/api/sql/elasticpools/update).

- Al cambiar la escala de los núcleos virtuales del grupo, se interrumpen brevemente las conexiones de base de datos. Este es el mismo comportamiento que se produce cuando se cambia la escala de las DTU para una base de datos única (no en un grupo). Para más información sobre la duración y el impacto de las conexiones interrumpidas para una base de datos durante las operaciones de cambio de escala, consulte [Cambio de escala de DTU para una base de datos única](#single-database-change-storage-size). 
- La duración para cambiar la escala de los núcleos virtuales del grupo puede depender de la cantidad total de espacio de almacenamiento que usan todas las bases de datos del grupo. En general, la latencia del cambio de escalado calcula el promedio de 90 minutos o menos por cada 100 GB. Por ejemplo, si el espacio total que usan todas las bases de datos del grupo es de 200 GB, la latencia esperada para cambiar la escala del grupo es de 3 horas o menos. En algunos casos en el nivel Estándar o Básico, la latencia del cambio de escala puede ser menos de cinco minutos sin tener en cuenta la cantidad de espacio utilizado.
- En general, lo que se tarda en cambiar los núcleos virtuales mínimos por base de datos o los núcleos virtuales máximos por base de datos suele ser cinco minutos o menos.
- Cuando se reduce el tamaño de los núcleos virtuales del grupo, el espacio usado por el grupo debe ser menor que el tamaño máximo permitido del nivel de servicio de destino y de los núcleos virtuales del grupo.

## <a name="what-is-the-maximum-number-of-servers-and-databases"></a>¿Cuál es el número máximo de servidores y bases de datos?

| Máxima | Valor |
| :--- | :--- |
| Bases de datos por servidor | 5000 |
| Número de servidores por suscripción por región | 20 |
|||

> [!IMPORTANT]
> A medida que el número de bases de datos se aproxima al límite por servidor, puede ocurrir lo siguiente:
> <br> •    Aumento de la latencia de las consultas en ejecución en la base de datos maestra.  Incluye vistas de las estadísticas del uso de los recursos como sys.resource_stats.
> <br> •    Aumento de la latencia en las operaciones de administración y presentación de las perspectivas del portal que implican enumerar las bases de datos del servidor.

## <a name="what-happens-when-database-and-elastic-pool-resource-limits-are-reached"></a>¿Qué ocurre cuando se alcanzan los límites de recursos de bases de datos y grupos elásticos?

### <a name="compute-vcores"></a>Proceso (núcleos virtuales)

Cuando la utilización de recursos de proceso de base de datos (que se mide por la utilización de núcleos virtuales) es elevada, la latencia de las consultas aumenta y puede agotar el tiempo de espera. En estas condiciones, el servicio puede poner en cola a las consultas y se proporcionan recursos para la ejecución a medida que el recurso esté disponible.
Al encontrar un uso de proceso elevado, las opciones de mitigación incluyen:

- Aumentar el nivel de rendimiento de la base de datos o del grupo elástico para proporcionar a la base de datos más núcleos virtuales. Consulte [Base de datos única: cambio de los núcleo virtuales](#single-database-change-vcores) y [Grupo elástico: cambio de los núcleos virtuales](#elastic-pool-change-vcores).
- Optimizar las consultas para reducir el uso de recursos de cada consulta. Para más información, consulte [Optimización y sugerencias de consultas](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Storage

Cuando el espacio de la base de datos alcanza el límite de tamaño máximo, las inserciones y actualizaciones de la base de datos que aumentan el tamaño de los datos producen un error y los clientes reciben un [mensaje de error](sql-database-develop-error-messages.md). Las selecciones y eliminaciones de la base de datos continúan.

Al encontrar un uso elevado de espacio, las opciones de mitigación incluyen:

- Aumentar el tamaño máximo de la base de datos o del grupo elástico o cambiar el nivel de rendimiento para aumentar el almacenamiento máximo. Consulte [SQL Database vCore-based resource limits](sql-database-vcore-resource-limits.md) (Límites de recursos basados en núcleos virtuales de SQL Database).
- Si la base de datos está en un grupo elástico, se puede mover fuera del grupo para que su espacio de almacenamiento no se comparta con otras bases de datos.

### <a name="sessions-and-workers-requests"></a>Sesiones y trabajos (solicitudes) 

El número máximo de sesiones y de trabajos se determina por el nivel de servicio y el nivel de rendimiento. Las nuevas solicitudes se rechazan cuando se alcanzan los límites de sesión o de trabajo y los clientes reciben un mensaje de error. Aunque el número de conexiones disponibles se pueden controlar por la aplicación, el número de trabajos simultáneos suele ser más difícil de calcular y controlar. Esto es especialmente cierto durante los períodos de carga máximos cuando se alcanzan los límites de recursos de base de datos y los trabajos se apilan debido a consultas de ejecución más prolongada. 

Al encontrar un uso elevado de sesión o de trabajo, las opciones de mitigación incluyen:
- Aumentar el nivel de servicio o el nivel de rendimiento de la base de datos o del grupo elástico. Consulte [Base de datos única: cambio del tamaño de almacenamiento](#single-database-change-storage-size), [Base de datos única: cambio de los núcleos virtuales](#single-database-change-vcores), [Grupo elástico: cambio del tamaño de almacenamiento](#elastic-pool-change-storage-size) y [Grupo elástico: cambio de los núcleos virtuales](#elastic-pool-change-vcores).
- Optimizar las consultas para reducir el uso de recursos de cada consulta si la causa del mayor uso de trabajo es debida a la contención de los recursos de proceso. Para más información, consulte [Optimización y sugerencias de consultas](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>Pasos siguientes

- Consulte [Preguntas más frecuentes sobre SQL Database](sql-database-faq.md) para conocer las respuestas a las preguntas más frecuentes.
- Para más información sobre los límites generales de Azure, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md).
