<properties
	pageTitle="Grupo de bases de datos elásticas para bases de datos SQL | Microsoft Azure"
	description="Descubra cómo puede dominar el crecimiento explosivo de sus bases de datos SQL mediante los grupos de bases de datos elásticas; una manera de compartir los recursos disponibles en gran cantidad de bases de datos."
	keywords="base de datos elástica, bases de datos SQL"	
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor="cgronlun"/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="11/10/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Domine el crecimiento explosivo de las Bases de datos SQL, gracias a bases de datos elásticas que le permitirán compartir recursos

Si es desarrollador de SaaS con decenas, cientos o incluso miles de bases de datos SQL, los grupos de bases de datos elásticas le permitirán simplificar el proceso de creación, mantenimiento y administración del rendimiento de las bases de datos, según un presupuesto que usted mismo controlará. Los [grupos de bases de datos elásticas](sql-database-elastic-pool-portal.md) para sus bases de datos SQL se pueden crear en cuestión de minutos mediante el Portal de Microsoft Azure, PowerShell o C#.

Un patrón común de aplicación SaaS es que cada base de datos tenga un cliente distinto, cada uno con un consumo de recursos distinto e imprevisible (CPU, E/S y memoria resumidos con DTU). Estos altos y bajos de demanda de cada base de dato puede hacer que resulte difícil predecir y, por lo tanto, aprovisionar los recursos. Con esta situación, existen dos opciones: puede aprovisionar en exceso los recursos para las bases de datos según los picos de demanda, con el consiguiente exceso de pago; o bien puede aprovisionar en defecto para ahorrar costes en detrimento del rendimiento y la satisfacción del cliente durante los picos.

Microsoft creó los grupos de bases de datos elásticos precisamente para resolver este problema.

> [AZURE.VIDEO elastic-databases-helps-saas-developers-tame-explosive-growth]


Los grupos de bases de datos elásticas ofrecen una solución para los clientes que necesiten garantizar que sus bases de datos obtienen los recursos de rendimiento que necesitan, cuando los necesitan, a la vez que ofrecen además un mecanismo de asignación de recursos simple y un presupuesto predecible. El escalado de rendimiento a petición de bases de datos individuales dentro de un grupo de servidores de base de datos elásticas es posible porque cada base de datos dentro de un grupo usa eDTU desde un conjunto compartido asociado al grupo. Esto permite a las bases de datos con una carga grande consumir más para satisfacer la demanda, mientras que las bases de datos con poca carga consumen menos y las bases de datos sin carga no consumen ningún eDTU. Al aprovisionar recursos para el grupo en lugar de para las bases de datos únicas no solo simplifica la administración de varias bases de datos, sino que también tiene un presupuesto predecible para cargas de trabajo de otro modo impredecibles.

Si se necesitan más eDTU para satisfacer las necesidades de un grupo (se agregan bases de datos adicionales o las bases de datos existentes empiezan a usar más eDTU), se pueden agregar eDTU adicionales a un grupo existente sin tiempo de inactividad de base de datos o un impacto negativo en las bases de datos. De manera similar, si ya no se necesitan eDTU adicionales, se pueden quitar de un grupo existente en cualquier momento dado.

![Bases de datos SQL que comparten elementos eDTU en un grupo de bases de datos elásticas.][1]

Las bases de datos que son buenos candidatos para grupos de bases de datos elásticas tienen normalmente períodos de actividad y otros períodos de inactividad. Piense e el ejemplo anterior, donde puede ver la actividad de una base de datos única, 4 bases de datos y, por último, un grupo de bases de datos elásticas con 20 bases de datos. Estas bases de datos con actividad variable con el tiempo son excelentes candidatos para grupos de bases de datos elásticas porque no están activas al mismo tiempo y pueden compartir eDTU. No todas las bases de datos se ajustan a este patrón. Existen bases de datos que tienen una demanda de recursos más constante. Estas bases de datos son las más idóneas para los niveles de servicio Basic, Standard y Premium, cuyos recursos se asignan por separado. Para obtener ayuda y así poder determinar si las bases de datos se beneficiarían de un grupo de bases de datos elásticas, consulte [Consideraciones de precio y rendimiento de un grupo de bases de datos elásticas](sql-database-elastic-pool-guidance.md).

Para obtener más información sobre los grupos de bases de datos elásticas, incluidos los detalles de errores y de API, consulte [Referencia de grupos de bases de datos elásticas](sql-database-elastic-pool-reference.md).


> [AZURE.NOTE]Los grupos de bases de datos elásticas están actualmente en vista previa y solo estarán disponibles en servidores con bases de datos SQL V12.

## Administre de forma sencilla grandes cantidades de bases de datos SQL con las herramientas de bases de datos elásticas

Además de proporcionar un uso de recursos más eficiente y un rendimiento predecible, los grupos de bases de datos elásticos facilitan el desarrollo de aplicaciones de SaaS gracias a herramientas que simplifican la compilación y administración del nivel de datos. La realización de tareas de mantenimiento e implementación de cambios en amplios conjuntos de bases de datos, proceso largo y complejo, se ha reducido a la simple ejecución de secuencias de comandos en trabajos elásticos. La capacidad para crear y ejecutar un trabajo de base de datos elástico elimina la mayoría del trabajo pesado asociado con la administración de cientos o incluso miles de bases de datos. Para obtener información acerca del servicio de trabajos de bases de datos elásticas que le permitirá la ejecución de scripts Transact-SQL en todas las bases de datos elásticas de un grupo, consulte [Información general acerca de trabajos de bases de datos elásticas](sql-database-elastic-jobs-overview.md).

También está disponible un conjunto enriquecido y eficaz de herramientas de desarrollo que le ayudará a implementar patrones de aplicaciones de bases de datos elásticas. Para las bases de datos particionadas, las herramientas como, por ejemplo, las herramientas de división y fusión, permiten dividir los datos de una partición y fusionarlos en otra. Esto reduce en gran medida el trabajo de administración de bases de datos particionadas a gran escala. Para obtener más información, vea el [Mapa de temas de herramientas de bases de datos elásticas](sql-database-elastic-scale-documentation-map.md).

## Características de continuidad del negocio para bases de datos de un grupo

Actualmente en la vista previa, las bases de datos elásticas admiten la mayoría de las [características de continuidad empresarial](sql-database-business-continuity.md) que están disponibles para las bases de datos únicas en los servidores V12.

### Copia de seguridad y restauración de bases de datos (restauración a un momento dado)

El sistema realiza automáticamente copias de seguridad de las bases de datos del grupo de bases de datos elásticas y la directiva de retención de copias de seguridad es la misma que el nivel de servicio correspondiente de la de las bases de datos únicas. De manera más específica, una base de datos elástica de un grupo básico se puede restaurar en cualquier punto de restauración en los últimos 7 días, una base de datos elástica de un grupo estándar se puede restaurar en cualquier punto de restauración durante los últimos 14 días y una base de datos elástica de un grupo Premium se puede restaurar en cualquier punto de restauración en los últimos 35 días. Durante la vista previa, se restaurarán las bases de datos de un grupo a una nueva base de datos del mismo grupo. Las bases de datos quitadas siempre se restaurarán como bases de datos independientes fuera del grupo en el nivel de rendimiento más bajo de ese nivel de servicio. Por ejemplo, una base de datos elástica de un grupo estándar que se quita se restaurará como base de datos de S0. Las operaciones de restauración de bases de datos pueden realizarse a través del Portal de Azure o mediante programación con la API de REST. Los cmdlet de PowerShell podrán utilizarse próximamente.

### Restauración geográfica

La restauración geográfica permite recuperar una base de datos de un grupo en un servidor en una región distinta. Durante la vista previa, para restaurar una base de datos de un grupo en un servidor diferente, el servidor de destino deberá tener un grupo con el mismo nombre que el grupo de origen. Si es necesario, cree un nuevo grupo en el servidor de destino y asígnele el mismo nombre antes de restaurar la base de datos. Si no existe un grupo con el mismo nombre en el servidor de destino, se producirá un error en la operación de restauración geográfica. Puede realizar operaciones de restauración geográfica mediante el Portal de Azure o la API de REST. Los cmdlet de PowerShell podrán utilizarse próximamente.


### Replicación geográfica

La replicación geográfica está disponible para cualquier base de datos en un grupo de bases de datos elásticas estándar o premium. Una o todas las bases de datos de una asociación de replicación geográfica pueden estar en un grupo de bases de datos elásticas siempre que los niveles de servicio sean los mismos. Puede configurar la replicación geográfica para los grupos de bases de datos elásticas mediante el [Portal de Azure](sql-database-geo-replication-portal.md), [PowerShell](sql-database-geo-replication-powershell.md) o [Transact-SQL](sql-database-geo-replication-transact-sql.md).

### importación y exportación

No se admite la exportación de una base de datos desde un grupo. Actualmente, no se admite la importación de una base de datos directamente en un grupo, pero puede importar en una base de datos única y luego mover la base de datos a un grupo.


<!--Image references-->
[1]: ./media/sql-database-elastic-pool/databases.png

<!---HONumber=Nov15_HO4-->