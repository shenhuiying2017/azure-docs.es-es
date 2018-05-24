---
title: 'Administración de varias instancias de SQL Database con grupos elásticos: Azure | Microsoft Docs'
description: Administración y escalado de muchas instancias de SQL Database, cientos y miles, mediante los grupos elásticos. Un precio para los recursos que se puede distribuir cuando sea necesario.
keywords: varias bases de datos, recursos de base de datos, rendimiento de la base de datos
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.date: 04/10/2018
ms.author: ninarn
ms.topic: article
ms.openlocfilehash: ecf9450271e82132b0f31fd0c65ce95d95c2cb3d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32195471"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Los grupos elásticos pueden ayudarle a administrar y escalar varias instancias de Azure SQL Database

Los grupos elásticos de SQL Database son una solución simple y rentable para la administración y escalado de varias bases de datos que tienen distintas e imprevisibles demandas de uso. Las bases de datos de un grupo elástico se encuentran en un único servidor de Azure SQL Database y comparten un número establecido de recursos a un precio establecido. Los grupos elásticos en Azure SQL Database permiten a los desarrolladores de SaaS optimizar el rendimiento del precio para un grupo de bases de datos dentro de un presupuesto prescrito a la vez que se ofrece elasticidad de rendimiento para cada base de datos.

## <a name="what-are-sql-elastic-pools"></a>¿Qué son los grupos elásticos de SQL?

Los desarrolladores de SaaS crean aplicaciones en los niveles superiores de datos de la escala que constan de varias bases de datos. Un patrón de aplicación común es aprovisionar una base de datos única para cada cliente. Sin embargo, cada cliente suele tener patrones de uso variables e impredecibles y resulta difícil predecir los requisitos de recursos de cada usuario de bases de datos individuales. Tradicionalmente, había dos opciones:

- Aprovisionar recursos en exceso basándose en el uso máximo y pagando de más, o
- Aprovisionar por debajo de lo necesario para ahorrar costos a expensas del rendimiento y satisfacción del cliente durante las horas de máxima actividad.

Los grupos elásticos solucionan este problema y se aseguran de que las bases de datos obtengan los recursos de rendimiento que necesitan y en el momento en que los necesitan. Proporcionan un mecanismo de asignación de recursos simples dentro de un presupuesto predecible. Para más información sobre los patrones de diseño de las aplicaciones SaaS que usan grupos elásticos, consulte [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)(Diseño de patrones para aplicaciones SaaS multiempresa con Base de datos SQL de Azure).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

Los grupos elásticos permiten al desarrollador adquirir recursos para un grupo compartido entre varias bases de datos con el fin de hacer frente a periodos impredecibles de uso por bases de datos individuales. Puede configurar los recursos para el grupo según el [modelo de compra basado en DTU](sql-database-service-tiers-dtu.md) o el [modelo de compra basado en núcleos virtuales (versión preliminar)](sql-database-service-tiers-vcore.md). El requisito de recursos para un grupo viene determinado por el uso agregado de sus bases de datos. La cantidad de recursos disponibles para el grupo se determina mediante el presupuesto del desarrollador. El desarrollador simplemente agrega bases de datos al grupo, establece los recursos mínimos y máximos para las bases de datos (DTU mínimas o máximas o núcleos virtuales mínimos y máximos, según la elección del modelo de recursos) y, a continuación, establece los recursos del grupo en función de su presupuesto. Un desarrollador puede usar grupos para aumentar de forma eficiente su servicio a partir de un método Lean Startup hasta un negocio con madurez a una escala cada vez mayor.

Dentro del grupo, a las bases de datos individuales se les proporciona la flexibilidad de escalarse automáticamente dentro de unos parámetros establecidos. Con cargas elevadas, una base de datos puede consumir más recursos para satisfacer la demanda. Las bases de datos con cargas ligeras consumen menos y las bases de datos sin carga no consumen ningún recurso. El aprovisionamiento de recursos para el grupo entero en lugar de para bases de datos únicas simplifica las tareas de administración. Además, cuenta con un presupuesto predecible para el grupo. Se pueden agregar recursos adicionales a un grupo existente sin que haya un tiempo de inactividad de la base de datos, excepto en los casos en los que las bases de datos se deben mover para proporcionar los recursos de proceso adicionales para la reserva de nuevas eDTU. De manera similar, si ya no se necesitan recursos adicionales, se pueden quitar de un grupo existente en cualquier momento dado. Y puede agregar bases de datos al grupo o quitar bases de datos del grupo. Si una base de datos infrautiliza recursos de forma predecible, sáquela del grupo.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>¿Cuándo se debe usar un grupo elástico de SQL Database?

Los grupos son apropiados para un amplio número de bases de datos con patrones de utilización específicos. Para una base de datos determinada, este patrón está caracterizado por una utilización media baja con picos de utilización relativamente poco frecuentes.

Cuantas más bases de datos pueda agregar a un grupo, mayores ahorros habrá. Según su patrón de uso de la aplicación, es posible ver los ahorros con tan solo dos bases de datos S3.

En las secciones siguientes, obtendrá ayuda para evaluar si resulta beneficioso que la colección específica de bases de datos se incluya en un grupo. Los ejemplos usan grupos Estándar, pero también se aplican los mismos principios a grupos Básico y Premium.

### <a name="assessing-database-utilization-patterns"></a>Evaluación de los patrones de utilización de base de datos

La siguiente ilustración muestra un ejemplo de una base de datos que está mucho tiempo inactiva, pero que también tiene picos periódicos de actividad. Se trata de un patrón de uso que es apropiado para un grupo:

   ![una base de datos única adecuada para un grupo](./media/sql-database-elastic-pool/one-database.png)

En el período de cinco minutos de la ilustración, DB1 llega a las 90 DTU, pero su uso medio es inferior a cinco DTU. Se requiere un nivel de rendimiento S3 para ejecutar esta carga de trabajo en una base de datos única, pero esto deja a la mayoría de los recursos sin usar durante los períodos de baja actividad.

Con un grupo, estas DTU sin usar pueden compartirse entre varias bases de datos, lo que permite reducir el número total de DTU necesarias y el costo general.

Según el ejemplo anterior, suponga que existen bases de datos adicionales con patrones de utilización similares como DB1. En las siguientes dos ilustraciones, la utilización de 4 bases de datos y 20 bases de datos se estratifican en el mismo gráfico para mostrar la naturaleza de no solapamiento de su utilización con el paso del tiempo mediante el modelo de compra basado en DTU.

   ![cuatro bases de datos con un patrón de uso adecuado para un grupo](./media/sql-database-elastic-pool/four-databases.png)

   ![veinte bases de datos con un patrón de uso adecuado para un grupo](./media/sql-database-elastic-pool/twenty-databases.png)

En la ilustración anterior, el uso total de DTU en las 20 bases de datos está representado por la línea negra. Esto muestra que la utilización de DTU agregada nunca supera las 100 DTU e indica que las 20 bases de datos pueden compartir 100 eDTU en este período. El resultado es una reducción multiplicada por 20 en las DTU y una reducción del precio 13 veces menor en comparación con la colocación de cada base de datos en los niveles de rendimiento S3 para bases de datos únicas.

Este ejemplo es ideal por las siguientes razones:

* Existen grandes diferencias entre la utilización de picos y la utilización media por base de datos.
* La utilización de picos para cada base de dato se produce en puntos de tiempo distintos.
* Las eDTU se comparten entre varias bases de datos.

El precio de un grupo es una función de las eDTU del grupo. Aunque el precio unitario de una eDTU para un grupo es 1,5 veces mayor que el de una DTU para una base de datos única, **las eDTU de grupo pueden compartirse entre muchas bases de datos, por lo que el número total de eDTU que se necesitan es menor**. Estas distinciones de precio y uso compartido de la eDTU son la base de la posibilidad de ahorro en el precio que pueden proporcionar los grupos.

Las siguientes reglas generales relacionadas con el recuento de base de datos y la utilización de base de datos ayudan a garantizar que un grupo proporciona costes reducidos en comparación con el uso de niveles de rendimiento para bases de datos únicas.

### <a name="minimum-number-of-databases"></a>Número mínimo de bases de datos

Si la cantidad total de recursos para una única base de datos es superior a 1,5 veces los recursos necesarios para el grupo, un grupo elástico resultaría más rentable.

***Ejemplo de modelo de compra basado en DTU***<br>
: al menos dos bases de datos S3 o 15 bases de datos S0 son necesarias para que un grupo de 100 eDTU sea más rentable que usar niveles de rendimiento para bases de datos únicas.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Número máximo de bases de datos de picos simultáneamente

Al compartir recursos, no todas las bases de datos de un grupo pueden usar a la vez los recursos hasta el límite disponible para bases de datos únicas. Cuantas menos bases de datos con un pico simultáneo haya, más bajo puede establecerse el número de recursos de grupo y más rentable resultará el grupo. En general, no más de 2/3 (o el 67 %) de las bases de datos del grupo deben alcanzar el límite de recursos establecido como pico de forma simultánea.

***Ejemplo de modelo de compra basado en DTU***<br>
: para reducir los costos de tres bases de datos S3 de un grupo de 200 eDTU, como mucho dos de estas bases de datos pueden alcanzar simultáneamente el pico de uso máximo. De lo contrario, si más de dos de estas cuatro bases de datos S3 establecen simultáneamente el pico, tendría que establecerse un tamaño del grupo en más de 200 eDTU. Si el tamaño del grupo se cambia a más de 200 eDTU, será necesario agregar más bases de datos S3 al grupo para que los costos sigan siendo inferiores a los niveles de rendimiento de las bases de datos únicas.

Tenga en cuenta que este ejemplo no tiene en cuenta la utilización de otras bases de datos en el grupo. Si en un momento determinado se están usando todas las bases de datos, menos de los 2/3 (o el 67%) de las bases de datos podrán alcanzar simultáneamente el pico de uso.

### <a name="resource-utilization-per-database"></a>Utilización de recursos por base de datos
Una gran diferencia entre el pico y la utilización media de una base de datos indica largos períodos de poca utilización y breves períodos de uso intenso. Este patrón de uso es ideal para compartir recursos entre bases de datos. Debe considerarse utilizar una base de datos para un grupo cuando su uso máximo es aproximadamente 1,5 veces mayor que su uso medio.

***Ejemplo de modelo de compra basado en DTU***<br>
: una base de datos S3 que establece un pico en 100 DTU y de media usa 67 DTU o menos es una buena candidata para compartir DTU en un grupo. O bien, una base de datos S1 con un pico de hasta 20 DTU y que de media usa 13 DTU o menos es una buena candidata para un grupo.

## <a name="how-do-i-choose-the-correct-pool-size"></a>¿Cómo se elige el tamaño de grupo correcto?

El mejor tamaño para un grupo depende de los recursos agregados necesarios para todas las bases de datos del grupo. Esto implica determinar lo siguiente:

* Los recursos máximos que usan todas las bases de datos del grupo (DTU máximas o núcleos virtuales máximos, según la elección del modelo de recursos).
* Número máximo de bytes de almacenamiento utilizado por todas las bases de datos en el grupo.

Para información sobre los niveles de servicio disponibles para cada modelo de recursos, consulte el [modelo de compra basado en DTU](sql-database-service-tiers-dtu.md) o el [modelo de compra basado en núcleos virtuales (versión preliminar)](sql-database-service-tiers-vcore.md).

SQL Database evalúa automáticamente el historial de uso de recursos de bases de datos en un servidor de SQL Database existente y recomienda la configuración de grupo apropiada en Azure Portal. Además de las recomendaciones, una experiencia integrada calcula el uso de eDTU para un grupo personalizado de bases de datos en el servidor. Esto le permite realizar un análisis de hipótesis agregando bases de datos interactivamente al grupo y quitándolas para obtener análisis de uso de recursos y consejos de cambio de tamaño antes de confirmar los cambios. Para ver un procedimiento, consulte [Supervisión y administración de un grupo de bases de datos elásticas con el Portal de Azure](sql-database-elastic-pool-manage-portal.md).

En casos donde no se pueden usar herramientas, las siguientes instrucciones paso a paso pueden ayudarle a estimar si un grupo es más rentable que las bases de datos únicas:

1. Calcule las eDTU o los núcleos virtuales necesarios para el grupo de la siguiente forma:

   Para el modelo de compra basado en DTU: MAX(<*número total de bases de datos* X *promedio de uso de DTU por base de datos*>.<br>
   <*Número de bases de datos con picos simultáneos* X *Uso de picos de DTU por base de datos*)

   En el caso del modelo de compra basado en núcleos virtuales (versión preliminar): MAX (<*número total de bases de datos* X *promedio de utilización de núcleos virtuales por base de datos*>,<br>
   <*número de bases de datos con picos simultáneos* X *uso máximo de núcleos virtuales por base de datos*)

2. Calcule el espacio de almacenamiento necesario para el grupo agregando el número de bytes necesarios para todas las bases de datos del grupo. A continuación, determine el tamaño del grupo de eDTU que proporciona esta cantidad de almacenamiento.
3. El modelo de compra basado en DTU toma las estimaciones de eDTU más grandes del paso 1 y el paso 2. El modelo de compra basado en núcleos virtuales (versión preliminar) toma la estimación de núcleos virtuales del paso 1.
4. Consulte la [página de precios de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) y busque el tamaño de grupo más pequeño que sea mayor que la estimación del paso 3.
5. Compare el precio del grupo del paso 5 con el precio de uso de los niveles de rendimiento adecuados para bases de datos únicas.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Empleo de otras características de SQL Database con grupos elásticos

### <a name="elastic-jobs-and-elastic-pools"></a>Trabajos elásticos y grupos elásticos

Con un grupo, las tareas de administración se simplifican al ejecutarse los scripts en **[trabajos elásticos](sql-database-elastic-jobs-overview.md)**. Un trabajo elástico elimina la mayoría de las tediosas tareas asociadas con un gran número de bases de datos. Para comenzar, consulte [Introducción a Trabajos de base de datos elástica](sql-database-elastic-jobs-getting-started.md).

Para más información sobre otras herramientas de bases de datos para trabajar con varias bases de datos, consulte [Escalado horizontal con Azure SQL Database](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Opciones de continuidad de negocio para bases de datos de un grupo elástico
Las bases de datos agrupadas suelen ser compatibles con las mismas [características de continuidad empresarial](sql-database-business-continuity.md) que encontrará en las bases de datos únicas.

- **Restauración a un momento dado**: la restauración a un momento dado usa copias de seguridad automáticas de bases de datos para recuperar una base de datos de un grupo en un momento específico. Consulte [Restauración a un momento dado](sql-database-recovery-using-backups.md#point-in-time-restore)

- **Georestauración**: la georestauración proporciona la opción de recuperación predeterminada cuando una base de datos no está disponible debido a una incidencia en la región en la que se hospeda. Consulte [Restauración de una base de datos Azure SQL Database o una conmutación por error en una secundaria](sql-database-disaster-recovery.md)

- **Replicación geográfica activa**: en el caso de aplicaciones con requisitos de recuperación más exigentes que los que puede ofrecer la georestauración, configure la [replicación geográfica activa](sql-database-geo-replication-overview.md).

## <a name="manage-elastic-pools-and-databases-using-the-azure-portal"></a>Administración de grupos elásticos y bases de datos mediante Azure Portal

### <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Creación de un nuevo grupo elástico de SQL Database mediante Azure Portal

Hay dos maneras de crear un grupo elástico en Azure Portal.
1. También puede crear un grupo elástico si busca **grupo elástico de SQL** en **Marketplace** o hace clic en **+Agregar** en la hoja para examinar grupos elásticos de SQL. Puede especificar un servidor nuevo o existente por medio de este flujo de trabajo de aprovisionamiento de grupo.
2. O bien, puede crear un grupo elástico; para ello, vaya a un servidor SQL existente y haga clic en **Crear grupo** para crear un grupo directamente en ese servidor. La única diferencia aquí es que omite el paso donde se especifica el servidor durante el flujo de trabajo de aprovisionamiento del grupo.

> [!NOTE]
> Puede crear varios grupos a un servidor, pero no puede agregar bases de datos de servidores diferentes al mismo grupo.

El nivel de servicio del grupo determina las características disponibles para las bases de datos elásticas del grupo y la cantidad máxima de recursos disponibles para cada base de datos. Para más información, consulte los límites de recursos para los grupos elásticos en el [modelo de DTU](sql-database-dtu-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels) y [modelo de núcleos virtuales](sql-database-vcore-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

Para configurar los recursos y fijar el precio del grupo, haga clic en **Configurar grupo**. A continuación, seleccione un nivel de servicio, agregue las bases de datos al grupo y configure los límites de recursos para el grupo y sus bases de datos.

Después de terminar de configurar el grupo, puede hacer clic en "Aplicar", asignar un nombre al grupo y hacer clic en "Aceptar" para crear el grupo.

### <a name="monitor-an-elastic-pool-and-its-databases"></a>Supervisión de un grupo elástico y sus bases de datos

En Azure Portal puede supervisar el uso de un grupo elástico y las bases de datos de ese grupo. También puede realizar un conjunto de cambios en el grupo elástico y enviar todos los cambios a la vez. Estos cambios incluyen agregar o quitar bases de datos, cambiar la configuración del grupo elástico o cambiar la configuración de la base de datos.

Para comenzar a supervisar el grupo elástico, busque y abra un grupo elástico en el portal. En primer lugar, verá una pantalla que le proporciona información general del estado de su grupo elástico. Esto incluye:

* Supervisión de gráficos que muestran el uso de recursos del grupo elástico
* Alertas y recomendaciones recientes, si están disponibles, para el grupo elástico

El siguiente gráfico muestra un grupo elástico de ejemplo:

![Vista Grupo](./media/sql-database-elastic-pool-manage-portal/basic.png)

Si quiere más información sobre el grupo, puede hacer clic en cualquiera de la información disponible en esta información general. Al hacer clic en el gráfico **Utilización de recursos**, irá a la vista de supervisión de Azure donde puede personalizar la ventana de métricas y tiempo mostrada en el gráfico. Al hacer clic en las notificaciones disponibles, irá a una hoja que muestra los detalles completos de esa recomendación o alerta.

Si desea supervisar las bases de datos dentro de su grupo, puede hacer clic en **Uso de recursos de base de datos** en la sección **Supervisión** del menú de recursos de la izquierda.

![Página de utilización de recursos de base de datos](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

#### <a name="to-customize-the-chart-display"></a>Personalización de la visualización del gráfico

Puede editar el gráfico y la página de métricas para mostrar otras métricas, como el porcentaje de CPU, el porcentaje de E/S de datos y el porcentaje de E/S de registro usados.

En el formulario **Editar gráfico**, puede seleccionar un intervalo de tiempo fijo o hacer clic en **personalizado** para seleccionar cualquier ventana de 24 horas en las dos últimas semanas y, a continuación, seleccionar los recursos para supervisar.

#### <a name="to-select-databases-to-monitor"></a>Selección de las bases de datos que se supervisarán

De forma predeterminada, el gráfico de la hoja **Uso de recursos de base de datos** mostrará las 5 bases de datos principales por DTU o CPU (en función de su nivel de servicio). Puede seleccionar o deseleccionar las bases de datos de la lista que hay debajo del gráfico para intercambiarlas mediante las casillas de la izquierda.

También puede seleccionar más métricas para ver en paralelo en esta tabla de base de datos para obtener una vista más completa del rendimiento de su base de datos.

Para más información, consulte cómo [crear alertas de SQL Database en Azure Portal](sql-database-insights-alerts-portal.md).

### <a name="manage-an-elastic-pool-and-its-databases"></a>Administración de un grupo elástico y sus bases de datos

Todas las configuraciones de grupo pueden encontrarse en un único lugar: la hoja **Configurar grupo**. Para llegar aquí, busque un grupo elástico en el portal y haga clic en **Configurar grupo** desde la parte superior de la hoja o desde el menú de recursos de la izquierda.

Desde aquí puede realizar cualquier combinación de los siguientes cambios y guardarlos todos en un lote:
1. Cambiar el nivel de servicio del grupo
2. Escalar o reducir verticalmente el rendimiento (DTU o núcleos virtuales) y el almacenamiento
3. Agregar o quitar bases de datos en el grupo
4. Establecer un límite de rendimiento mínimo (garantizado) y máximo para las bases de datos de los grupos
5. Revisar el resumen de costos para ver los cambios en su factura como resultado de las nuevas selecciones

![Hoja de configuración del grupo elástico](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="manage-elastic-pools-and-databases-using-powershell"></a>Administración de grupos elásticos y bases de datos mediante PowerShell

Para crear y administrar grupos elásticos de SQL Database con Azure PowerShell, use los siguientes cmdlets de PowerShell. Si necesita instalar o actualizar PowerShell, consulte [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell). Para crear y administrar bases de datos, servidores y reglas de firewall, vea [Administración de servidores, bases de datos y firewalls de Azure SQL con PowerShell](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-powershell).

> [!TIP]
> Para obtener scripts de ejemplo de PowerShell, vea [Creación de grupos elásticos y traslado de bases de datos de un grupo a otro y fuera de un grupo mediante PowerShell](scripts/sql-database-move-database-between-pools-powershell.md) y [Uso de PowerShell para supervisar y escalar un grupo elástico de SQL en Azure SQL Database](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Cmdlet | DESCRIPCIÓN |
| --- | --- |
|[New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool)|Crea un grupo de bases de datos elásticas en un servidor SQL Server lógico.|
|[Get-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/get-azurermsqlelasticpool)|Obtiene los grupos elásticos y los valores de sus propiedades de un servidor SQL Server lógico.|
|[Set-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)|Modifica las propiedades de un grupo de bases de datos elásticas en un servidor SQL Server lógico. Por ejemplo, use la propiedad **StorageMB** para modificar el almacenamiento máximo de un grupo elástico.|
|[Remove-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/remove-azurermsqlelasticpool)|Elimina un grupo de bases de datos elásticas en un servidor SQL Server lógico.|
|[Get-AzureRmSqlElasticPoolActivity](/powershell/module/azurerm.sql/get-azurermsqlelasticpoolactivity)|Obtiene el estado de las operaciones en un grupo elástico en un servidor SQL Server lógico.|
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Crea una nueva base de datos en un grupo existente o como una sola base de datos. |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Obtiene una o más bases de datos.|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Establece las propiedades de una base de datos o mueve una base de datos existente a un grupo elástico, fuera de él o entre grupos elásticos.|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Quita una base de datos.|


> [!TIP]
> La creación de varias bases de datos en un grupo elástico puede tardar tiempo cuando se realiza mediante el portal o los cmdlets de PowerShell que crean una base de datos única cada vez. Para automatizar la creación en un grupo elástico, vea [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).
>

## <a name="manage-elastic-pools-and-databases-using-the-azure-cli"></a>Administración de grupos elásticos y bases de datos mediante la CLI de Azure

Para crear y administrar grupos elásticos de SQL Database con la [CLI de Azure](/cli/azure), use los siguientes comandos de la [CLI de Azure para SQL Database](/cli/azure/sql/db). Use [Cloud Shell](/azure/cloud-shell/overview) para ejecutar la CLI en el explorador o [instálela](/cli/azure/install-azure-cli) en Windows, Linux o macOS.

> [!TIP]
> Para obtener scripts de ejemplo de la CLI de Azure, vea [Uso de la CLI para mover una instancia de Azure SQL Database a un grupo elástico de SQL](scripts/sql-database-move-database-between-pools-cli.md) y [Uso de la CLI de Azure para escalar un grupo elástico de SQL en Azure SQL Database](scripts/sql-database-scale-pool-cli.md).
>

| Cmdlet | DESCRIPCIÓN |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create)|Crea un grupo elástico.|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list)|Devuelve una lista de grupos elásticos de un servidor.|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_dbs)|Devuelve una lista de bases de datos de un grupo elástico.|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_editions)|Además incluye los parámetros disponibles de DTU de grupo, los límites de almacenamiento y la configuración por base de datos. Para reducir el nivel de detalle, los límites de almacenamiento y la configuración por base de datos adicionales están ocultos de forma predeterminada.|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)|Actualiza un grupo elástico.|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_delete)|Elimina el grupo elástico.|

## <a name="manage-databases-within-elastic-pools-using-transact-sql"></a>Administración de bases de datos dentro de grupos elásticos mediante Transact-SQL

Para crear y mover bases de datos en grupos elásticos existentes o para devolver información sobre un grupo elástico de SQL Database con Transact-SQL, use los siguientes comandos de T-SQL. Puede emitir estos comandos mediante Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs) o cualquier otro programa que pueda conectarse a un servidor de Azure SQL Database y pasar comandos de Transact-SQL. Para crear y administrar bases de datos, servidores y reglas de firewall, vea [Administración de servidores, bases de datos y firewalls de Azure SQL con Transact-SQL](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-transact-sql).

> [!IMPORTANT]
> No es posible crear, actualizar ni eliminar un grupo elástico de Azure SQL Database mediante Transact-SQL. Puede agregar o quitar bases de datos de un grupo elástico y puede usar DMV para devolver información sobre grupos elásticos existentes.
>

| Get-Help | DESCRIPCIÓN |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Crea una nueva base de datos en un grupo existente o como una sola base de datos. Debe estar conectado a la base de datos maestra para crear una base de datos.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Mueve una base de datos a un grupo elástico, fuera de él o entre grupos elásticos.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Permite eliminar una base de datos.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Devuelve estadísticas de uso de recursos de todos los grupos de bases de datos elásticas de un servidor lógico. Para cada grupo de bases de datos elásticas hay una fila por cada ventana de informe de 15 segundos (cuatro filas por minuto). Esto incluye uso de CPU, E/S, registro, almacenamiento y empleo simultáneo de solicitudes o sesiones por parte de todas las bases de datos del grupo.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Devuelve la edición (nivel de servicio), el objetivo de servicio (plan de tarifa) y el nombre del grupo elástico, si existe, para una base de datos SQL de Azure o una instancia de Azure SQL Data Warehouse. Si inició sesión en la base de datos maestra en un servidor de Azure SQL Database, devuelve información sobre todas las bases de datos. Para Azure SQL Data Warehouse, debe estar conectado a la base de datos maestra.|

## <a name="manage-elastic-pools-and-databases-using-the-rest-api"></a>Administración de grupos elásticos y bases de datos mediante la API de REST

Para crear y administrar grupos elásticos de SQL Database, use estas solicitudes de la API de REST.

| Get-Help | DESCRIPCIÓN |
| --- | --- |
|[Elastic Pools - Create Or Update](/rest/api/sql/elasticpools/createorupdate)|Crea un nuevo grupo elástico o actualiza uno ya existente.|
|[Elastic Pools - Delete](/rest/api/sql/elasticpools/delete)|Elimina el grupo elástico.|
|[Elastic Pools - Get](/rest/api/sql/elasticpools/get)|Obtiene un grupo elástico.|
|[Elastic Pools - List By Server](/rest/api/sql/elasticpools/listbyserver)|Devuelve una lista de grupos elásticos de un servidor.|
|[Elastic Pools - Update](/rest/api/sql/elasticpools/update)|Actualiza un grupo elástico ya existente.|
|[Recommended Elastic Pools - Get](/rest/api/sql/recommendedelasticpools/get)|Obtiene un grupo elástico recomendado.|
|[Recommended Elastic Pools - List By Server](/rest/api/sql/recommendedelasticpools/listbyserver)|Devuelve los grupos elásticos recomendados.|
|[Recommended Elastic Pools - List Metrics](/rest/api/sql/recommendedelasticpools/listmetrics)|Devuelve las métricas de los grupos elásticos recomendados.|
|[Elastic Pool Activities](/rest/api/sql/elasticpoolactivities)|Devuelve las actividades de los grupos elásticos.|
|[Elastic Pool Database Activities](/rest/api/sql/elasticpooldatabaseactivities)|Devuelve la actividad de las bases de datos de un grupo elástico.|
|[Databases - Create Or Update](/rest/api/sql/databases/createorupdate)|Crea una nueva base de datos o actualiza una ya existente.|
|[Databases - Get](/rest/api/sql/databases/get)|Obtiene una base de datos.|
|[Databases - Get By Elastic Pool](/rest/api/sql/databases/getbyelasticpool)|Obtiene una base de datos dentro de un grupo elástico.|
|[Databases - Get By Recommended Elastic Pool](/rest/api/sql/databases/getbyrecommendedelasticpool)|Obtiene una base de datos dentro de un grupo elástico recomendado.|
|[Databases - List By Elastic Pool](/rest/api/sql/databases/listbyelasticpool)|Devuelve una lista de bases de datos de un grupo elástico.|
|[Databases - List By Recommended Elastic Pool](/rest/api/sql/databases/listbyrecommendedelasticpool)|Devuelve una lista de bases de datos dentro de un grupo elástico recomendado.|
|[Databases - List By Server](/rest/api/sql/databases/listbyserver)|Devuelve una lista de bases de datos de un servidor.|
|[Databases - Update](/rest/api/sql/databases/update)|Actualiza una base de datos existente.|

## <a name="next-steps"></a>Pasos siguientes

* Para ver un vídeo, vea el [Curso de vídeo de la Academia virtual de Microsoft sobre las funcionalidades de las bases de datos elásticas en Azure SQL Database](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554).
* Para más información sobre los patrones de diseño de las aplicaciones SaaS que usan grupos elásticos, consulte [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)(Diseño de patrones para aplicaciones SaaS multiempresa con Base de datos SQL de Azure).
* Para obtener un tutorial sobre SaaS con grupos elásticos, vea [Introducción a la aplicación de SaaS Wingtip](sql-database-wtp-overview.md).
