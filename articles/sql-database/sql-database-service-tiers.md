---
title: Servicio Azure SQL Database | Microsoft Docs
description: Obtenga información acerca de los niveles de servicio para las bases de datos de grupo y únicas a fin de proporcionar niveles de rendimiento y tamaños de almacenamiento.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/04/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: a4474aec212084006becd02f317dabae6e731d98
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
---
# <a name="what-are-azure-sql-database-service-tiers"></a>¿Qué son los niveles de servicio de Azure SQL Database?

[Azure SQL Database](sql-database-technical-overview.md) ofrece dos modelos de compra para recursos de proceso, almacenamiento y E/S: uno basado en DTU y el otro en núcleos virtuales (que se encuentra en versión preliminar). En la tabla y el gráfico siguientes se comparan y contrastan estos dos modelos de compra.

|**Modelo de compra**|**Descripción**|**Más adecuado para**|
|---|---|---|
|Modelo basado en DTU|Este modelo se basa en una medida agrupada de recursos de proceso, almacenamiento y E/S. Los niveles de rendimiento se expresan como unidades de transmisión de datos (DTU) para las bases de datos únicas y como unidades de transmisión de datos elásticas (eDTU) para los grupos de bases de datos elásticas. Para más información sobre las DTU y las eDTU, consulte [¿Qué son las DTU y las eDTU?](sql-database-what-is-a-dtu.md)|Recomendado para los clientes que desean opciones de recursos simples y configuradas previamente.| 
|Modelo basado en núcleos virtuales|Este modelo le permite escalar los recursos de proceso y almacenamiento de manera independiente. También permite usar Ventaja híbrida de Azure para SQL Server para ahorrar en los costos.|Recomendado para los clientes que valoran la flexibilidad, el control y la transparencia.|
||||  

![Modelo de precios](./media/sql-database-service-tiers/pricing-model.png)

## <a name="dtu-based-purchasing-model"></a>Modelo de compra basado en DTU

La unidad de rendimiento de base de datos (DTU) representa una medida combinada de CPU, memoria, lecturas y escrituras. El modelo de compra basado en DTU ofrece un conjunto de agrupaciones preconfiguradas de recursos de proceso y almacenamiento incluido para impulsar diferentes niveles de rendimiento de la aplicación. Los clientes que prefieren la simplicidad de una agrupación preconfigurada y pagos fijos cada mes pueden encontrar el modelo basado en DTU más adecuado para sus necesidades. En el modelo de compra basado en DTU, los clientes pueden elegir entre los niveles de servicio **Básico**, **Estándar** y **Premium** tanto para [bases de datos únicas](sql-database-single-database-resources.md) como para [grupos elásticos](sql-database-elastic-pool.md). Los niveles de servicio se diferencian por una variedad de niveles de rendimiento con una cantidad fija de almacenamiento incluido, un período de retención fijo para copias de seguridad y un precio fijo. Todos los niveles de servicio proporcionan la flexibilidad de cambiar los niveles de rendimiento sin tiempo de inactividad. Las bases de datos únicas y los grupos elásticos se facturan por horas en función del nivel de servicio y el nivel de rendimiento.

> [!IMPORTANT]
> Instancia administrada de Azure SQL Database, actualmente en versión preliminar pública, no admite un modelo de compra basado en DTU. Para más información, consulte [Instancia administrada de Azure SQL Database](sql-database-managed-instance.md). 

### <a name="choosing-a-service-tier-in-the-dtu-based-purchasing-model"></a>Selección de un nivel de servicio en el modelo de compra basado en DTU

La selección de un nivel de servicio depende sobre todo de los requisitos de continuidad del negocio, de almacenamiento y de rendimiento.
||Básica|Estándar|Premium|
| :-- | --: |--:| --:| --:| 
|Carga de trabajo de destino|Desarrollo y producción|Desarrollo y producción|Desarrollo y producción||
|Acuerdo de Nivel de Servicio de tiempo de actividad|99,99%|99,99%|99,99%|N/D en versión preliminar|
|Retención de copias de seguridad|7 días|35 días|35 días|
|CPU|Bajo|Bajo, medio, alto|Medio, alto|
|Rendimiento de E/S (aproximado) |2,5 IOPS por DTU| 2,5 IOPS por DTU | 48 IOPS por DTU|
|Latencia de E/S (aproximada)|5 ms (lectura), 10 ms (escritura)|5 ms (lectura), 10 ms (escritura)|2 ms (lectura/escritura)|
|Índice de almacén de columnas |N/D|S3 y versiones posteriores|Compatible|
|OLTP en memoria (optimización en memoria|N/D|N/D|Compatible|
|||||

### <a name="performance-level-and-storage-size-limits-in-the-dtu-based-purchasing-model"></a>Nivel de rendimiento y límites de tamaño de almacenamiento en el modelo de compra basado en DTU

Los niveles de rendimiento se expresan como unidades de transmisión de datos (DTU) para las bases de datos únicas y como unidades de transmisión de datos elásticas (eDTU) para los grupos de bases de datos elásticas. Para más información sobre las DTU y las eDTU, consulte [¿Qué son las DTU y las eDTU?](sql-database-what-is-a-dtu.md)

#### <a name="single-databases"></a>Bases de datos únicas

||Básica|Estándar|Premium|
| :-- | --: | --: | --: | --: |
| Tamaño máximo de almacenamiento* | 2 GB | 1 TB | 4 TB  | 
| Cantidad máxima de DTU | 5 | 3000 | 4000 | |
||||||

Para más información sobre niveles de rendimiento y opciones de tamaño de almacenamiento específicos disponibles para bases de datos únicas, consulte [SQL Database DTU-based resource limits for single databases](sql-database-dtu-resource-limits.md#single-database-storage-sizes-and-performance-levels) (Límites de recursos basados en DTU de SQL Database para bases de datos únicas).

#### <a name="elastic-pools"></a>Grupos elásticos

| | **Básico** | **Estándar** | **Premium** | 
| :-- | --: | --: | --: | --: |
| Tamaño máximo de almacenamiento por base de datos*  | 2 GB | 1 TB | 1 TB | 
| Tamaño máximo de almacenamiento por grupo* | 156 GB | 4 TB | 4 TB | 
| Cantidad máxima de eDTU por base de datos | 5 | 3000 | 4000 | 
| Cantidad máxima de eDTU por grupo | 1600 | 3000 | 4000 | 
| Cantidad máxima de bases de datos por grupo | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> \* Los tamaños de almacenamiento mayores que la cantidad de almacenamiento incluida están en su versión preliminar y pueden generar costos adicionales. Para obtener información detallada, vea [Precios de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). 
>
> \* En el nivel Premium, más de 1 TB de almacenamiento se encuentra actualmente disponible en las siguientes regiones: Este de Australia, Sudeste de Australia, Sur de Brasil, Centro de Canadá, Este de Canadá, Centro de EE. UU., Centro de Francia, Centro de Alemania, Este de Japón, Oeste de Japón, Centro de Corea, Centro y Norte de EE. UU., Europa del Norte, Centro y Sur de EE. UU., Sudeste Asiático, Sur de Reino Unido, Oeste de Reino Unido, Este de EE. UU. 2, Oeste de EE. UU., Virginia Gob. EE. UU. y Europa Occidental. Consulte [Limitaciones actuales P11-P15](sql-database-dtu-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 

Para más información sobre niveles de rendimiento y opciones de tamaño de almacenamiento específicos disponibles para grupos elásticos, consulte [SQL Database DTU-based resource limits](sql-database-dtu-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels) (Límites de recursos basados en DTU de SQL Database).

## <a name="vcore-based-purchasing-model-preview"></a>Modelo de compra basado en núcleos virtuales (versión preliminar)

Un núcleo virtual representa la CPU lógica que se ofrece con la posibilidad de elegir entre varias generaciones de hardware. El modelo de compra basado en núcleos virtuales (versión preliminar) le ofrece flexibilidad, control, transparencia de consumo de recursos individuales y una manera sencilla de trasladar los requisitos de carga de trabajo locales a la nube. Este modelo le permite escalar los recursos de proceso, memoria y almacenamiento en función de sus necesidades de carga de trabajo. En el modelo de compra basado en núcleos virtuales, los clientes pueden elegir entre los niveles de servicio De uso general y Crítico para la empresa (versión preliminar) tanto para [bases de datos únicas](sql-database-single-database-resources.md) como para [grupos elásticos](sql-database-elastic-pool.md). 

Los niveles de servicio se diferencian por una variedad de niveles de rendimiento, el diseño de alta disponibilidad, el aislamiento de errores, los tipos de almacenamiento y el intervalo de E/S. El cliente debe configurar por separado el periodo necesario de retención y almacenamiento de las copias de seguridad. Al usar el modelo de núcleos virtuales, es posible obtener un ahorro de hasta un 30 % en las bases de datos únicas y los grupos elásticos con la [Ventaja para uso híbrido de Azure para SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

En un modelo de compra basado en núcleos virtuales, los clientes pagan por:
- Proceso (nivel de servicio + número de núcleos virtuales + generación de hardware)*
- Tipo y cantidad de almacenamiento de datos y registros 
- Número de E/S**
- Almacenamiento de copia de seguridad (RA-GRS)** 

\* En la versión preliminar pública inicial, las CPU lógicas de Gen 4 se basan en procesadores Intel E5-2673 v3 (Haswell) a 2,4 GHz.

\*\* Durante la versión preliminar, las copias de seguridad y las E/S son gratuitas durante 7 días.

> [!IMPORTANT]
> Los recursos de proceso, E/S y almacenamiento de datos y registros se cobran por base de datos o grupo elástico. El almacenamiento de copia de seguridad se cobra por cada base de datos. Para más información sobre los precios de Instancia administrada, consulte [Instancia administrada de Azure SQL Database](sql-database-managed-instance.md).

### <a name="choosing-service-tier-compute-memory-storage-and-io-resources"></a>Selección del nivel de servicio y los recursos de proceso, memoria, almacenamiento y E/S

La conversión al modelo de compra de núcleos virtuales permite escalar los recursos de proceso y almacenamiento de manera independiente, igualar el rendimiento local y optimizar el precio. Si la base de datos o el grupo elástico consumen más de 300 DTU, la conversión a núcleos virtuales puede reducir los costos. Puede realizar la conversión mediante la API de su elección o usar Azure Portal, sin experimentar tiempo de inactividad. Sin embargo, la conversión no es necesaria. Si el modelo de compra de DTU satisface sus requisitos de rendimiento y empresariales, debe seguir usándolo. Si decide pasar del modelo de DTU al modelo de núcleos virtuales, debe seleccionar el nivel de rendimiento mediante la siguiente regla general: cada 100 DTU del nivel Estándar requieren al menos 1 núcleo y cada 125 DTU del nivel Premium requieren como mínimo 1 núcleo.

La tabla siguiente le ayudará a comprender las diferencias entre estos dos niveles:

||**Uso general**|**Crítico para la empresa**|
|---|---|---|
|Más adecuado para|La mayoría de las cargas de trabajo empresariales. Ofrece opciones de proceso y almacenamiento equilibradas y escalables orientadas al presupuesto.|Aplicaciones empresariales con elevados requisitos de E/S. Ofrece la máxima resistencia a errores mediante varias réplicas aisladas.|
|Proceso|De 1 a 16 núcleos virtuales|De 1 a 16 núcleos virtuales|
|Memoria|7 GB por núcleo |7 GB por núcleo |
|Storage|Almacenamiento remoto Premium, de 5 GB a 4 TB|Almacenamiento local de SSD, de 5 GB a 1 TB|
|Rendimiento de E/S (aproximado)|500 IOPS por núcleo virtual con 7500 IOPS como máximo|5000 IOPS por núcleo|
|Disponibilidad|1 réplica, sin escalado de lectura|3 réplicas, 1 [escalado de lectura](sql-database-read-scale-out.md), HA con redundancia de zona|
|Copias de seguridad|RA-GRS, de 7 a 35 días (7 días de forma predeterminada)|RA-GRS, de 7 a 35 días (7 días de forma predeterminada)*|
|En memoria|N/D|Compatible|
|||

\* Durante la versión preliminar, el período de retención de copias de seguridad no es configurable y se fija en 7 días.

> [!IMPORTANT]
> Si tiene menos de un núcleo virtual de capacidad de proceso, use el modelo de compra basado en DTU.

Para más información sobre los niveles de rendimiento y las opciones de tamaño de almacenamiento específicos que están disponibles para bases de datos únicas, consulte [SQL Database vCore-based resource limits for single databases](sql-database-vcore-resource-limits.md#single-database-storage-sizes-and-performance-levels) (Límites de recursos basados en núcleos virtuales de SQL Database para bases de datos únicas) y para grupos elásticos, consulte [SQL Database vCore-based resource limits for elastic pools](sql-database-vcore-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels) (Límites de recursos basados en núcleos virtuales de SQL Database para grupos elásticos).

Consulte [Preguntas más frecuentes sobre SQL Database](sql-database-faq.md) para conocer las respuestas a las preguntas más frecuentes. 

### <a name="storage-considerations"></a>Consideraciones sobre el almacenamiento

Tenga en cuenta lo siguiente.
- El almacenamiento asignado lo usan los archivos de datos (MDF) y los archivos de registros (LDF).
- Cada nivel de rendimiento admite un tamaño máximo de base de datos, con un tamaño máximo predeterminado de 32 GB.
- Cuando se configura el tamaño de base de datos requerido (tamaño de MDF), el 30 % de almacenamiento adicional se agrega automáticamente para admitir LDF.
- Puede seleccionar cualquier tamaño de base de datos entre 10 GB y el máximo admitido.
 - Para Standard Storage, aumente o disminuya el tamaño en incrementos de 10 GB.
 - Para Premium Storage, aumente o disminuya el tamaño en incrementos de 250 GB.
- En el nivel de servicio De uso general, `tempdb` usa una SSD asociada y este costo de almacenamiento se incluye en el precio del núcleo virtual.
- En el nivel de servicio Crítico para la empresa, `tempdb` comparte la SSD asociada con los archivos MDF y LDF y el costo de almacenamiento de tempDB se incluye en el precio del núcleo virtual.

> [!IMPORTANT]
> Se le cobra por el almacenamiento total asignado de MDF y LDF.

Para supervisar el tamaño total actual de MDF y LDF, use [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Para supervisar el tamaño actual de los archivos MDF y LDF individuales, use [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

### <a name="backups-and-storage"></a>Copias de seguridad y almacenamiento

Para admitir las funcionalidades Restauración a un momento dado (PITR) y Retención a largo plazo (LTR) de SQL Database, se asigna almacenamiento a las copias de seguridad de base de datos. Este almacenamiento se asigna por separado para cada base de datos y se factura como dos cargos independientes por base de datos. 

- **PITR**: las copias de seguridad de base de datos individuales se copian en el almacenamiento de RA-GRS automáticamente. El tamaño de almacenamiento aumenta dinámicamente a medida que se crean las nuevas copias de seguridad.  El almacenamiento se usa para realizar cada cinco minutos copias de seguridad completas semanales, copias de seguridad diferenciales diarias y copias de seguridad de registros de transacciones. El consumo de almacenamiento depende de la tasa de cambio de la base de datos y del período de retención. Puede configurar un período de retención diferente para cada base de datos de entre 7 y 35 días. Se ofrece una cantidad de almacenamiento mínimo igual al tamaño de los datos sin costo adicional. En la mayoría de las bases de datos, esta cantidad es suficiente para almacenar copias de seguridad durante 7 días.
- **LTR**: SQL Database ofrece la opción de configurar la retención a largo plazo de copias de seguridad completas durante un período máximo de 10 años. Si la directiva de LTR está habilitada, estas copias de seguridad se almacenan en almacenamiento RA-GRS automáticamente, pero puede controlar la frecuencia con que se realizan las copias de seguridad. Para satisfacer los distintos requisitos de cumplimiento, puede seleccionar distintos períodos de retención para copias de seguridad semanales, mensuales o anuales. Esta configuración definirá la cantidad de almacenamiento que se usará para las copias de seguridad de LTR. Para estimar el costo del almacenamiento de LTR, se puede usar la calculadora de precios de LTR. Para más información, consulte [retención a largo plazo](sql-database-long-term-retention.md).

### <a name="azure-hybrid-use-benefit"></a>Ventaja de uso híbrido de Azure

En el modelo de compra basado en núcleos virtuales, puede intercambiar sus licencias existentes por tarifas de descuento en SQL Database mediante la [Ventaja híbrida de Azure para SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md). Esta ventaja de Azure le permite usar las licencias de SQL Server locales para ahorrar hasta un 30 % en Azure SQL Database al utilizarlas con Software Assurance.

![Precios](./media/sql-database-service-tiers/pricing.png)

#### <a name="migration-of-single-databases-with-geo-replication-links"></a>Migración de bases de datos únicas con vínculos de replicación geográfica

La migración del modelo basado en DTU al modelo basado en núcleos virtuales es similar a actualizar o degradar las relaciones de replicación geográfica entre las bases de datos Estándar y Premium. No es necesario terminar la replicación geográfica, pero el usuario debe respetar las reglas de secuenciación. Al actualizar, debe actualizar primero la base de datos secundaria y luego la principal. Al degradar, invierta el orden; es decir, debe degradar primero la base de datos principal y luego la secundaria. 

Cuando se usa la replicación geográfica entre dos grupos elásticos, se recomienda encarecidamente designar un grupo como principal y otro como secundario. En ese caso, la migración de grupos elásticos debe seguir las mismas instrucciones.  Sin embargo, es técnicamente posible que un grupo elástico contenga bases de datos principales y secundarias. En este caso, para migrarlas correctamente se debe tratar el grupo con la utilización más alta como principal y seguir las reglas de secuenciación como corresponda.  

En la tabla siguiente se proporcionan instrucciones para escenarios de migración específicos: 

|Nivel de servicio actual|Nivel de servicio de destino|Tipo de migración|Acciones del usuario|
|---|---|---|---|
|Estándar|Uso general|Lateral|Puede migrar en cualquier orden, pero debe asegurarse de tener un tamaño adecuado de núcleo virtual*|
|Premium|Crítico para la empresa|Lateral|Puede migrar en cualquier orden, pero debe asegurarse de tener un tamaño adecuado de núcleo virtual*|
|Estándar|Crítico para la empresa|Actualizar|Debe migrar primero la secundaria|
|Crítico para la empresa|Estándar|Degradar|Debe migrar primero la principal|
|Premium|Uso general|Degradar|Debe migrar primero la principal|
|Uso general|Premium|Actualizar|Debe migrar primero la secundaria|
|Crítico para la empresa|Uso general|Degradar|Debe migrar primero la principal|
|Uso general|Crítico para la empresa|Actualizar|Debe migrar primero la secundaria|
||||

\* Cada 100 DTU del nivel Estándar requieren como mínimo 1 núcleo virtual y cada 125 DTU de nivel Premium requieren como mínimo un núcleo.

#### <a name="migration-of-failover-groups"></a>Migración de grupos de conmutación por error 

La migración de grupos de conmutación por error con varias bases de datos requiere la migración individual de las bases de datos principales y secundarias. Durante ese proceso, se aplican las mismas consideraciones y reglas de secuenciación. Después de que las bases de datos se convierten al modelo basado en núcleos virtuales, el grupo de conmutación por error permanecerá en vigor con la misma configuración de directiva. 

#### <a name="creation-of-a-geo-replication-secondary"></a>Creación de una base de datos secundaria de replicación geográfica

Solo puede crear una base de datos secundaria de replicación geográfica con el mismo nivel de servicio que la principal. Para base de datos con una tasa de generación de registros elevada, es muy recomendable que la base de datos secundaria se cree con el mismo nivel de rendimiento que la principal. Si va a crear una base de datos secundaria con replicación geográfica en el grupo elástico para una única base de datos principal, se recomienda encarecidamente que el valor `maxVCore` del grupo coincida con el nivel de rendimiento de la base de datos principal. Si va a crear una base de datos secundaria con replicación geográfica en el grupo elástico para una principal de otro grupo elástico, se recomienda encarecidamente que los grupos tengan la misma configuración de `maxVCore`.

#### <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Uso de una copia de base de datos para convertir una base de datos basada en DTU en una base de datos basada en núcleos virtuales

Puede copiar cualquier base de datos con un nivel de rendimiento basado en DTU en una base de datos con un nivel de rendimiento basado en núcleos virtuales sin restricciones o secuencia especial, siempre y cuando el nivel de rendimiento de destino admita el tamaño máximo de base de datos de la base de datos de origen. Esto se debe a que la copia de base de datos crea una instantánea de los datos a partir de la hora de inicio de la operación de copia y no realiza la sincronización de datos entre el origen y el destino. 

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre niveles de rendimiento y opciones de tamaño de almacenamiento específicos disponibles, consulte [SQL Database DTU-based resource limits](sql-database-dtu-resource-limits.md) (Límites de recursos basados en DTU de SQL Database para bases de datos únicas) y [SQL Database vCore-based resource limits](sql-database-vcore-resource-limits.md) (Límites de recursos basados en núcleos virtuales de SQL Database).
- Consulte [Preguntas más frecuentes sobre SQL Database](sql-database-faq.md) para conocer las respuestas a las preguntas más frecuentes.
- Información sobre [límites, cuotas y restricciones de suscripción y servicios de Azure](../azure-subscription-service-limits.md).
