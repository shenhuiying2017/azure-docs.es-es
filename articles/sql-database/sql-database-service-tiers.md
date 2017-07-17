---
title: Niveles de servicio y rendimiento de Azure SQL Database | Microsoft Docs
description: "Comparación de los niveles de servicio y rendimiento de SQL Database en bases de datos únicas e introducción a los grupos elásticos de SQL"
keywords: opciones de base de datos, rendimiento de la base de datos
services: sql-database
documentationcenter: 
author: janeng
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 06/30/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 68d55d2dd088ce6350bd65b79206f161f9d3d788
ms.contentlocale: es-es
ms.lasthandoff: 07/06/2017


---
<a id="what-performance-options-are-available-for-an-azure-sql-database" class="xliff"></a>

# ¿Qué opciones de rendimiento están disponibles para una instancia de Azure SQL Database?

[Azure SQL Database](sql-database-technical-overview.md) ofrece cuatro niveles de servicio para bases de datos únicas y [agrupadas](sql-database-elastic-pool.md). Y son: **Básico**, **Estándar**, **Premium** y **Premium RS**. Dentro de cada nivel de servicio, hay varios niveles de rendimiento ([DTU](sql-database-what-is-a-dtu.md)) y opciones de almacenamiento para controlar diferentes cargas de trabajo y tamaños de datos. Los niveles de rendimiento más elevados proporcionan recursos adicionales de proceso y almacenamiento diseñados para proporcionar una capacidad y un rendimiento cada vez mayores. Puede cambiar los niveles de servicio, los niveles de rendimiento y el almacenamiento de manera dinámica sin tiempo de inactividad. 
- Los niveles de servicio **Básico**, **Estándar** y **Premium** tienen todos ellos un Acuerdo de Nivel de Servicio de tiempo de actividad del 99,99 %, opciones de continuidad empresarial flexibles, características de seguridad y facturación por hora. 
- El nivel **Premium RS** proporciona los mismos niveles de rendimiento que el nivel Premium con un Acuerdo de Nivel de Servicio reducido porque se ejecuta con un número menor de copias redundantes que una base de datos de los demás niveles de servicio. Por ello, si se produce un error del servicio, deberá recuperar la base de datos a partir de una copia de seguridad con un retardo de hasta 5 minutos.

> [!IMPORTANT]
> Una base de datos SQL de Azure obtiene un conjunto de recursos garantizado, y las características de rendimiento previstas de la base de datos no resultan afectadas por ninguna otra base de datos de Azure. 

<a id="choosing-a-service-tier" class="xliff"></a>

## Selección de un nivel de servicio
En la tabla siguiente se proporcionan ejemplos de los niveles más adecuados para las diferentes cargas de trabajo de las aplicaciones.

| Nivel de servicio | Carga de trabajo objetivo |
| :--- | --- |
| **Básico** | Ideal para bases de datos pequeñas, que suelen admitir una sola operación activa en un momento dado. Algunos ejemplos son las bases de datos utilizadas para desarrollo o prueba, o las aplicaciones a pequeña escala que se emplean con poca frecuencia. |
| **Standard** |La mejor opción para las aplicaciones en la nube con pocos o medios requisitos de rendimiento de E/S; admite varias consultas simultáneas. Algunos ejemplos son las aplicaciones web o los grupos de trabajo. |
| **Premium** | Diseñado para un alto volumen transaccional con altos requisitos de rendimiento de E/S; admite muchos usuarios simultáneos. Algunos ejemplos son las bases de datos que admiten aplicaciones críticas. |
| **Premium RS** | Diseñado para cargas de trabajo intensivas de E/S pero que no requieren las mayores garantías de disponibilidad. Los ejemplos incluyen las pruebas en cargas de trabajo de alto rendimiento, o en una carga de trabajo analítico en el que la base de datos no es el sistema de registro. |
|||

Puede crear bases de datos únicas con recursos dedicados dentro de un nivel de servicio con un [nivel de rendimiento](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels) específico o también puede crear bases de datos dentro de un [grupo elástico de SQL](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus). En un grupo elástico de SQL, los recursos de proceso y almacenamiento se comparten entre varias bases de datos dentro de un único servidor lógico. 

Los recursos disponibles para las bases de datos únicas se expresan como unidades de transacción de base de datos (DTU) y, para los grupos elásticos de SQL, como unidades de transacción de bases de datos elásticas (eDTU). Para más información sobre las DTU y las eDTU, consulte [¿Qué son las DTU y las eDTU?](sql-database-what-is-a-dtu.md)

Para decidir el nivel de servicio, determine las características mínimas de la base de datos que necesita:

| **Características del nivel de servicio** | **Básico** | **Standard** | **Premium** | **Premium RS**|
| :-- | --: | --: | --: | --: |
| Tamaño máximo de la base de datos única | 2 GB | 250 GB | 4 TB*  | 500 GB  |
| Tamaño máximo del grupo elástico | 156 GB | 2,9 TB | 4 TB* | 750 GB |
| Tamaño máximo de la base de datos de un grupo elástico | 2 GB | 250 GB | 500 GB | 500 GB |
| Cantidad máxima de bases de datos por grupo | 500  | 500 | 100 | 100 |
| DTU máximas de la base de datos única | 5 | 100 | 4000 | 1000 |
| DTU máximas por base de datos de un grupo elástico | 5 | 3000 | 4000 | 1000 |
| Período de retención de copias de seguridad de base de datos | 7 días | 35 días | 35 días | 35 días |
||||||

> [!IMPORTANT]
> Esta opción de 4 TB se encuentra actualmente disponibles en las siguientes regiones: Este de EE. UU. 2, Oeste de EE. UU., Virginia Gob. EE. UU., Europa Occidental, Alemania Central, Asia Suroriental, Japón Oriental, Este de Australia, Canadá Central y Canadá Oriental. Consulte las [limitaciones actuales de 4 TB](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize)
>

Cuando haya determinado el nivel de servicio adecuado, estará listo para determinar el nivel de rendimiento (el número de DTU) y la cantidad de almacenamiento de la base de datos. 

- Con frecuencia, los niveles de rendimiento S2 y S3 **Estándar** son un buen punto de partida. 
- Para las bases de datos con requisitos de CPU o E/S altos, los niveles de rendimiento **Premium** son el punto de partida adecuado. 
- El nivel **Premium** ofrece más CPU y comienza en 10 veces más E/S que el nivel de rendimiento **Estándar** más alto.
- El nivel **PremiumRS** ofrece el rendimiento del nivel **Premium** a un coste más bajo, pero con un Acuerdo de Nivel de Servicio reducido.

> [!IMPORTANT]
> Revise el tema sobre los [grupos elásticos de SQL](sql-database-elastic-pool.md) para más información sobre la agrupación de bases de datos en grupos elásticos de SQL para compartir recursos de proceso y almacenamiento. El resto de este tema se centra en los niveles de servicio y rendimiento de las bases de datos únicas.
>

<a id="single-database-service-tiers-and-performance-levels" class="xliff"></a>

## Niveles de servicio de la Base de datos única y niveles de rendimiento
Para las bases de datos únicas, existen varios niveles de rendimiento y cantidades de almacenamiento dentro de cada nivel de servicio. 

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

<a id="scaling-up-or-scaling-down-a-single-database" class="xliff"></a>

## Escalado y reducción vertical de una base de datos única

Después de elegir inicialmente un nivel de rendimiento, puede escalar o reducir verticalmente una base de datos única de forma dinámica, en función de la experiencia real.  

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

Al cambiar el nivel de servicio o de rendimiento de una base de datos, se crea una réplica de la base de datos original en el nuevo nivel de rendimiento y, a continuación, se cambian las conexiones a la réplica. Durante este proceso, no se pierde ningún dato; sin embargo, durante el breve momento en que se cambie a la réplica, las conexiones a la base de datos estarán deshabilitadas, por tanto, es posible que se reviertan algunas transacciones en curso. La longitud de tiempo del cambio varía, pero suele estar por debajo de 4 segundos y es inferior a 30 segundos en el 99 % de las veces. Si el número de transacciones en curso es elevado en el momento en que las conexiones están deshabilitadas, esta longitud de tiempo puede ser mayor.  

La duración de todo el proceso de escalado vertical depende del nivel de servicio y del tamaño de la base de datos antes y después del cambio. Por ejemplo, el cambio de una base de datos de 250 GB dentro de un nivel de servicio Estándar, o bien desde o hacia este, se completará en 6 horas. Para una base de datos del mismo tamaño que cambie los niveles de rendimiento del nivel de servicio Premium, se completará en unas 3 horas.

* Si va a actualizar a un nivel de servicio o rendimiento más elevado, el tamaño máximo de la base de datos no aumenta a no ser que especifique un tamaño máximo mayor.
* Para degradar una base de datos, esta no debe alcanzar el tamaño máximo permitido del nivel de servicio de destino. 
* Al actualizar una base de datos con la [replicación geográfica](sql-database-geo-replication-portal.md) habilitada, actualice sus bases de datos secundarias al nivel de rendimiento deseado antes de actualizar la principal (regla general).
* Al degradar de un nivel de servicio **Premium** a un nivel de servicio inferior, primero debe terminar todas las relaciones de replicación geográfica. Puede seguir los pasos que se describen en el tema [Recuperación de una interrupción](sql-database-disaster-recovery.md) para detener el proceso de replicación entre la base de datos principal y las bases de datos secundarias.
* Las ofertas del servicio de restauración son diferentes para los distintos niveles de servicio. Si va a degradar al nivel **Básico**, tendrá un período de retención más bajo; consulte [Copias de seguridad de Azure SQL Database](sql-database-automated-backups.md).
* Las nuevas propiedades de la base de datos no se aplican hasta que se completan los cambios.


<a id="current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize" class="xliff"></a>

## Limitaciones actuales de las bases de datos P11 y P15 con un tamaño máximo de 4 TB

Como se ha indicado anteriormente, se admite un tamaño máximo de 4 TB para bases de datos P11 y P15 en algunas regiones. Las siguientes consideraciones y limitaciones se aplican a las bases de datos P11 y P15 con un tamaño máximo de 4 TB:

- Si elige la opción de tamaño máximo de 4 TB al crear una base de datos (con un valor de 4 TB o 4096 GB), el comando de creación produce un error si la base de datos se aprovisiona en una región no admitida.
- Para las bases de datos con niveles de rendimiento P11 y P15 ubicadas en alguna de las regiones admitidas, puede aumentar la capacidad de almacenamiento máxima hasta 4 TB. Esto se puede comprobar mediante [SELECT DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx) o inspeccionando el tamaño de la base de datos en el portal de Azure. La actualización de una base de datos P11 o P15 existente solo puede realizarse mediante un inicio de sesión de entidad de seguridad de nivel de servidor o por los miembros del rol de base de datos dbmanager. 
- Si se ha ejecutado una operación de actualización en una región admitida, la configuración se actualiza inmediatamente. La base de datos permanecerá en línea durante el proceso de actualización. Sin embargo, no podrá utilizar los 4 TB completos de almacenamiento hasta que se hayan actualizado los archivos de base de datos reales con el nuevo tamaño máximo. El período de tiempo necesario depende del tamaño de la base de datos que se va a actualizar.  
- Al crear o actualizar una base de datos P11 o P15, solo puede optar entre un tamaño máximo de 1 TB y 4 TB. Actualmente no se admiten tamaños de almacenamiento intermedios. Cuando crea una base de datos P11 o P15, se selecciona previamente la opción de almacenamiento predeterminada de 1 TB. En el caso de bases de datos ubicadas en alguna de las regiones admitidas, puede aumentar el almacenamiento máximo a 4 TB en bases de datos únicas nuevas o existentes. Para todas las demás regiones, el tamaño máximo no se puede aumentar por encima de 1 TB. El precio no cambia cuando se selecciona la opción de 4 TB de almacenamiento incluido.
- El tamaño máximo de base de datos de 4 TB no se puede cambiar a 1 TB aunque el almacenamiento real usado esté por debajo de 1 TB. Por tanto, no puede degradar una base de datos P11-4TB/P15-4TB a una base de datos P11-1TB/P15-1TB o de un nivel de rendimiento inferior (por ejemplo, a P1-P6) hasta que proporcionemos opciones de almacenamiento adicionales para el resto de los niveles de rendimiento. Esta restricción también se aplica a los escenarios de copia y restauración, lo que incluye la restauración a un momento dado, la restauración geográfica, la retención de copias de seguridad a largo plazo y la copia de base de datos. Una vez que una base de datos está configurada con la opción de 4 TB, todas las operaciones de restauración de esta base de datos deben ejecutarse en una P11/P15 con un tamaño máximo de 4 TB.
- Al crear o actualizar una base de datos P11/P15 en una región no compatible, la operación de creación o actualización dará error con el siguiente mensaje de error: **P11 and P15 database with up to 4TB of storage are available in US East 2, West US, South East Asia, West Europe, Canada East, Canada Central, Japan East, and Australia East** (Las bases de datos P11 y P15 con hasta 4 TB de almacenamiento están disponibles en las regiones de Este de EE. UU. 2, oeste de EE. UU., Virginia Gob. EE. UU., Europa Occidental, Alemania Central, Asia Suroriental, Japón Oriental, Este de Australia, Canadá Central y Canadá Oriental).
- En escenarios de replicación geográfica activa:
   - Configuración de una relación de replicación geográfica: si la base de datos principal es P11 o P15, la secundaria (una o varias) también debe P11 o P15; aquellas con un nivel de rendimiento inferior se rechazarán como secundarias, puesto que no tienen capacidad para admitir 4 TB.
   - Actualización de la base de datos principal en una relación de replicación geográfica: al cambiar el tamaño máximo a 4 TB en una base de datos principal, se desencadenará el mismo cambio en la base de datos secundaria. Ambas actualizaciones deben realizarse correctamente para que el cambio en la principal surta efecto. Se aplican limitaciones por región para la opción de 4TB (consulte anteriormente). Si la base de datos secundaria está en una región que no admite 4 TB, no se actualizará la principal.
- No se admite el uso del servicio Import/Export para cargar bases de datos P11-4TB/P15-4TB. Use SqlPackage.exe para [importar](sql-database-import.md) y [exportar](sql-database-export.md) datos.

<a id="manage-single-database-service-tiers-and-performance-levels-using-the-azure-portal" class="xliff"></a>

## Administración de niveles de servicio y rendimiento de bases de datos mediante Azure Portal

Para establecer o cambiar el nivel de servicio, el nivel de rendimiento o la cantidad de almacenamiento de una base de datos SQL de Azure nueva o existente mediante Azure Portal, abra la ventana **Configurar rendimiento** ventana de la base de datos; para ello, haga clic en **Plan de tarifa (escalar DTU)**, como se muestra en la siguiente captura de pantalla. 

- Establezca o cambie el nivel de servicio y seleccione el nivel de servicio de su carga de trabajo. 
- Establezca o cambie el nivel de rendimiento (**DTU**) dentro de un nivel de servicio mediante el control deslizante de **DTU**.
- Establezca o cambie la cantidad de almacenamiento para el nivel de rendimiento mediante el control deslizante **Almacenamiento**. 

  ![Configuración del nivel de servicio y rendimiento](./media/sql-database-service-tiers/service-tier-performance-level.png)

> [!IMPORTANT]
> Al seleccionar un nivel de servicio P11 o P15, revise las [limitaciones actuales de las bases de datos P11 y P15 con un tamaño máximo de 4 TB](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize).
>

<a id="manage-single-database-service-tiers-and-performance-levels-using-powershell" class="xliff"></a>

## Administración de los niveles de servicio y rendimiento de bases de datos únicas mediante PowerShell

Para establecer o cambiar los niveles de servicio, los niveles de rendimiento y la cantidad de almacenamiento de las bases de datos SQL de Azure mediante PowerShell, use los siguientes cmdlets de PowerShell. Si necesita instalar o actualizar PowerShell, consulte [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell). 

| Cmdlet | Descripción |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Crea una base de datos. |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Obtiene una o más bases de datos.|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Establece las propiedades de una base de datos, o mueve una base de datos existente en un grupo elástico.|


> [!TIP]
> Para ver un script de ejemplo de PowerShell que supervisa las métricas de rendimiento de una base de datos, la escala a un nivel de rendimiento superior y crea una regla de alerta sobre una de estas métricas, consulte [Supervisión y escalado de una instancia única de SQL Database mediante PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).

<a id="manage-single-database-service-tiers-and-performance-levels-using-the-azure-cli" class="xliff"></a>

## Administración de niveles de servicio y rendimiento de bases de datos únicas mediante la CLI de Azure

Para establecer o cambiar los niveles de servicio, los niveles de rendimiento y la cantidad de almacenamiento de las bases de datos SQL de Azure mediante la CLI de Azure, use los siguientes comandos de [Base de datos SQL de la CLI de Azure](/cli/azure/sql/db). Use [Cloud Shell](/azure/cloud-shell/overview) para ejecutar la CLI en el explorador o [instálela](/cli/azure/install-azure-cli) en Windows, Linux o macOS. Para crear y administrar grupos elásticos de SQL, consulte [Grupos elásticos](sql-database-elastic-pool.md).

| Cmdlet | Descripción |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#create) |Crea una base de datos.|
|[az sql db list](/cli/azure/sql/db#list)|Enumera todas las bases de datos y almacenes de datos de un servidor, o todas las bases de datos de un grupo elástico.|
|[az sql db list-editions](/cli/azure/sql/db#list-editions)|Enumera los objetivos de servicio y los límites de almacenamiento disponibles.|
|[az sql db list-usages](/cli/azure/sql/db#list-usages)|Devuelve los usos de la base de datos.|
|[az sql db show](/cli/azure/sql/db#show)|Obtiene una base de datos o un almacén de datos.|
|[az sql db update](/cli/azure/sql/db#update)|Actualiza una base de datos.|

> [!TIP]
> Para ver un script de ejemplo de la CLI de Azure que escala una única base de datos SQL de Azure a un nivel de rendimiento diferente después de consultar la información de tamaño de la base de datos, consulte [Uso de la CLI para supervisar y escalar una única base de datos de SQL](scripts/sql-database-monitor-and-scale-database-cli.md).
>

<a id="manage-single-database-service-tiers-and-performance-levels-using-transact-sql" class="xliff"></a>

## Administración de los niveles de servicio y rendimiento de bases de datos únicas mediante Transact-SQL

Para establecer o cambiar los niveles de servicio, los niveles de rendimiento y la cantidad de almacenamiento de las bases de datos SQL de Azure con Transact-SQL, use los siguientes comandos de T-SQL. Puede emitir estos comandos mediante Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs) o cualquier otro programa que pueda conectarse a un servidor de Azure SQL Database y pasar comandos de Transact-SQL. 

| Comando | Descripción |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Crear una nueva base de datos. Debe estar conectado a la base de datos maestra para crear una nueva base de datos.|
| [ALTER DATABASE (Base de datos SQL de Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Modifica una base de datos SQL de Azure. |
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Devuelve la edición (nivel de servicio), el objetivo de servicio (plan de tarifa) y el nombre del grupo elástico, si existe, para una base de datos SQL de Azure o una instancia de Azure SQL Data Warehouse. Si inició sesión en la base de datos maestra en un servidor de Azure SQL Database, devuelve información sobre todas las bases de datos. Para Azure SQL Data Warehouse, debe estar conectado a la base de datos maestra.|
|[sys.database_usage (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-usage-azure-sql-database)|Muestra el número, el tipo y la duración de las bases de datos en un servidor de Azure SQL Database.|

En el ejemplo siguiente se muestra cómo se cambia el tamaño máximo mediante el comando ALTER DATABASE:

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

<a id="manage-single-databases-using-the-rest-api" class="xliff"></a>

## Administración de bases de datos únicas mediante la API de REST

Para establecer o cambiar los niveles de servicio, los niveles de rendimiento y la cantidad de almacenamiento de las bases de datos SQL de Azure mediante la API de REST, consulte [API de REST de Azure SQL Database](/rest/api/sql/).

<a id="next-steps" class="xliff"></a>

## Pasos siguientes

* Más información acerca de las [DTU](sql-database-what-is-a-dtu.md).
* Para aprender sobre la supervisión del uso de DTU, consulte [Supervisión y optimización del rendimiento](sql-database-troubleshoot-performance.md).


