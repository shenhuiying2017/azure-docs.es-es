<properties 
	pageTitle="Domine el crecimiento explosivo con bases de datos elásticas" 
	description="Un grupo de bases de datos elásticas de Base de datos SQL de Azure es una colección de recursos disponibles que comparte un grupo de bases de datos elásticas." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="05/29/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# Domine el crecimiento explosivo con bases de datos elásticas

Si es un desarrollador de SaaS con decenas, cientos o incluso miles de bases de datos, los grupos de bases de datos elásticos simplifican el proceso de creación, mantenimiento y administración del rendimiento de las bases de datos según el presupuesto que usted mismo controlará.

Se trata de un patrón común de aplicación SaaS para que cada base de datos tenga un cliente distinto, cada uno con consumos de recursos distintos e imprevisibles (CPU, E/S y memoria resumidos con eDTU). Estos altos y bajos de demanda de cada base de dato puede hacer que resulte difícil predecir y, por lo tanto, aprovisionar los recursos. Con esta situación, existen dos opciones: puede aprovisionar en exceso los recursos para las bases de datos según los picos de demanda, con el consiguiente exceso de pago; o bien puede aprovisionar en defecto para ahorrar costes en detrimento del rendimiento y la satisfacción del cliente durante los picos.

Microsoft creó los grupos de bases de datos elásticos precisamente para resolver este problema.

> [AZURE.VIDEO elastic-databases-helps-saas-developers-tame-explosive-growth]

Un grupo de bases de datos elásticas es una colección de recursos disponibles que comparten las bases de datos elásticas del grupo. Puede agregar bases de datos al grupo o quitarlas en cualquier momento. Las bases de datos del grupo comparten los recursos (expresados como unidades de rendimiento de la base elástica o eDTU) y la capacidad de almacenamiento del grupo, aunque cada base de datos usa solo los recursos que necesita cuando los necesita, dejando recursos libres para otras bases de datos cuando los necesiten. En lugar de aprovisionar en exceso las bases de datos individuales y pagar por recursos que permanecen inactivos, podrá asignar y pagar un precio predecible por los recursos del grupo en conjunto. Esto afecta al coste, por lo que le permitirá lograr un modelo de negocio competitivo. Además, cada base de datos ganará en adaptabilidad de rendimiento.

Las bases de datos ideales para los grupos de bases de datos elásticas son aquellas que permanecen activas menos del 50 % del tiempo. Un patrón típico de actividad de las bases de datos es el de permanecer inactivas durante algún tiempo, permanecer activas con una baja demanda de recursos y activas con elevada demanda de recursos.

No todas las bases de datos se ajustan a este patrón. Existen bases de datos que tienen una demanda de recursos más constante. Estas bases de datos son las más idóneas para los niveles de servicio Basic, Standard y Premium, cuyos recursos se asignan por separado.

Los grupos de bases de datos elásticos se pueden crear mediante el Portal de Microsoft Azure o con PowerShell. Para obtener más información, vea [Creación y administración de grupos elásticos](sql-database-elastic-pool-portal.md). Para obtener información detallada acerca de los grupos de bases de datos elásticas, incluidos los detalles de errores y de API, vea la [Referencia acerca de las bases de datos elásticas](sql-database-elastic-pool-reference.md).


> [AZURE.NOTE]Los grupos elásticos están actualmente en vista previa y solo estarán disponibles en servidores con bases de datos SQL V12.

## Administración sencilla de grandes números de bases de datos con herramientas de bases de datos elásticas

Además de proporcionar un uso de recursos más eficiente y un rendimiento predecible, los grupos de bases de datos elásticos facilitan el desarrollo de aplicaciones de SaaS gracias a herramientas que simplifican la compilación y administración del nivel de datos. La realización de tareas de mantenimiento e implementación de cambios en amplios conjuntos de bases de datos, proceso largo y complejo, se ha reducido a la simple ejecución de secuencias de comandos en trabajos elásticos. La capacidad para crear y ejecutar un trabajo de base de datos elástico elimina la mayoría del trabajo pesado asociado con la administración de cientos o incluso miles de bases de datos. Para obtener información general acerca del servicio de trabajos de bases de datos elásticos que permite la ejecución de scripts de T-SQL en todas las bases de datos elásticas de un grupo, vea [Información general de los trabajos de bases de datos elásticas](sql-database-elastic-jobs-overview.md).

También está disponible un conjunto enriquecido y eficaz de herramientas de desarrollo que le ayudará a implementar patrones de aplicaciones de bases de datos elásticas. Para las bases de datos particionadas, las herramientas como, por ejemplo, las herramientas de división y fusión, permiten dividir los datos de una partición y fusionarlos en otra. Esto reduce en gran medida el trabajo de administración de bases de datos particionadas a gran escala. Para obtener más información, vea el [Mapa de temas de herramientas de bases de datos elásticas](sql-database-elastic-scale-documentation-map.md).

## Características de continuidad del negocio para bases de datos de un grupo

Las bases de datos de un grupo de bases de datos elásticas (en el nivel estándar elástico), actualmente en vista previa, admiten la mayoría de las características que están disponibles para las bases de datos de nivel Estándar.

### Copia de seguridad y restauración de bases de datos (restauración a un momento dado)

El sistema realiza automáticamente copias de seguridad de las bases de datos del grupo de bases de datos elásticas y la directiva de retención de copias de seguridad es la misma que la de las bases de datos del nivel Estándar. Durante la vista previa, se restaurarán las bases de datos de un grupo en una nueva base de datos del mismo grupo. Las bases de datos quitadas se restaurarán como base de datos individual fuera del grupo como base de datos Standard S0. Las operaciones de restauración de bases de datos pueden realizarse a través del Portal de Azure o mediante programación con la API de REST. Los cmdlet de PowerShell podrán utilizarse próximamente.

### Restauración geográfica

La restauración geográfica permite recuperar una base de datos de un grupo en un servidor en una región distinta. Durante la vista previa, para restaurar una base de datos de un grupo en un servidor diferente, el servidor de destino deberá tener un grupo con el mismo nombre que el grupo de origen. Si es necesario, cree un nuevo grupo en el servidor de destino y asígnele el mismo nombre antes de restaurar la base de datos. Si no existe un grupo con el mismo nombre en el servidor de destino, se producirá un error en la operación de restauración geográfica. Puede realizar operaciones de restauración geográfica mediante el Portal de Azure o la API de REST. Los cmdlet de PowerShell podrán utilizarse próximamente.


### Replicación geográfica

Las bases de datos que tienen habilitada la replicación geográfica se pueden mover dentro y fuera del grupo de bases de datos elásticas sin que ello repercuta en el funcionamiento de la replicación. Actualmente en la vista previa, no se puede habilitar la replicación geográfica en una base de datos que ya se encuentre en un grupo.



 

<!---HONumber=58_postMigration-->